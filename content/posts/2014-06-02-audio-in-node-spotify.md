---
title: Audio in node-spotify
date: 2014-06-02
aliases: [
  "/blog/audio-in-node-spotify"
]
---

## More audio options
Last week I continued to work on the audio features of node-spotify. To provide audio data to Javascript with node.js buffers was easy. Not so easy was providing all of the data in the correct order. That was mainly my fault - once again reading documentation early would have been useful.

All code examples are not real code.

### First try
My first approach was to just send the audio data with a `uv_async_t` handle to the node.js main thread, wrap it in a buffer and call the user provided `musicDelivery` callback.

```c
void setup() {
  uv_async_init(uv_devault_loop(), musicDeliveryHandle, handleMusic);
}

//Will be called in an internal spotify thread, no HandleScopes possible
int music_delivery(sp_session* session, sp_audioformat* format, void* frames, int num_frames) {
  size_t size = format->channels * num_frames * sizeof(int16_t);
  audio_data* data = allocate_and_copy_data(size, num_frames, frames);
  musicDeliveryHandle->data = (void*)data;
  uv_async_send(musicDeliveryHandle);
}

void handleMusic(uv_async_t* handle, int status) {
  node::Buffer buffer = node::Buffer::New(...);
  callMusicDeliveryCallback(buffer->handle_);
  free(handle->data);
}
```

When I used this to write the PCM data to a [node-speaker](https://github.com/TooTallNate/node-speaker) instance the music was recognizable but very choppy. I tried a few things to circumvent that. I implemented a node.js readable [stream](http://nodejs.org/api/stream.html) containing the data, didn't help. I buffered in Javascript - didn't help.

### Second try
Then I took a look at [node-libspotify](https://github.com/Floby/node-libspotify) that already has this feature. It buffers the audio data in C++ in a queue (same implementation like the libspotify examples) and calls the musicDelivery callback periodically (every 100ms), emptying the queue into it. I adapted it and it worked. Only at the beginning of a track there where a few chops, then it would catch up.

```c++
void setup() {
  uv_timer_init(uv_default_loop(), musicTimer);
  uv_timer_start(musicTimer, &handleMusic, 100, 100);
}

//Will be called in an internal spotify thread, no HandleScopes possible
int music_delivery(sp_session* session, sp_audioformat* format, void* frames, int num_frames) {
  size_t size = format->channels * num_frames * sizeof(int16_t);
  audio_data* data = allocate_and_copy_data(size, num_frames, frames);
  push_data_to_queue(data);
}

void handleMusic(uv_async_t* handle, int status) {
  ...
  while(data = audio_get()) {
    node::Buffer buffer = node::Buffer::New(...);
    callMusicDeliveryCallback(buffer->handle_);
    queue_remove(data);
    free(handle->data);
  }
}
```

But I did not really like that approach. The timer to call the callback just feels... hacky. For a long time I didn't realize why this worked and my implementation not. My mistake was to assume that somehow my chunks were being modified which was not the case. It's very simple actually: `uv_async_send` is *not* a queue. The handles you send can be executed in any order and I believe even be dropped.

When I wrote the audio data to a file I found a chunk that was in \"line\" 40 in the working file in \"line\" 200040 in the corrupt file written with my code. I then inserted a static counter for chunks in C++ and immediately saw that I was missing out a lot of chunks.

### Third try
So I created a mixture of the two approaches: I buffer the data in C++, too, but will call the callback always when a certain amount of frames is available in the queue. And it works very good.

```c
void setup() {
  uv_async_init(uv_devault_loop(), musicDeliveryHandle, handleMusic);
}

//Will be called in an internal spotify thread, no HandleScopes possible
int music_delivery(sp_session* session, sp_audioformat* format, void* frames, int num_frames) {
  size_t size = format->channels * num_frames * sizeof(int16_t);
  audio_data* data = allocate_and_copy_data(size, num_frames, frames);
  musicDeliveryHandle->data = (void*)data;
  push_data_to_queue(data);
  if(queue_has_enough_data()) {
    uv_async_send(musicDeliveryHandle);
  }
}

void handleMusic(uv_async_t* handle, int status) {
  ...
  while(data = audio_get()) {
    node::Buffer buffer = node::Buffer::New(...);
    callMusicDeliveryCallback(buffer->handle_);
    queue_remove(data);
    free(handle->data);
  }
}
```

The implementation of `queue_has_enough_data` will maybe have a user settable value.

I will just have to take care of a few more things like pausing, seeking and correct handling of the end of the track/stopping. But a within the next weeks it should be possible to get a stream of audio data from node-spotify.

### Leaving pthread for libuv
While browsing to the audio code I also did quite a bit of refactoring. I replaced pthreads with libuv calls. Of course this will use pthreads in the end but it is more consistent. I also hopes this helps with a future Windows build.

A lot of renaming was done since the libspotify guys thought it was a good idea to use very short variable names. I mean, `q` for `queue` is understandable when you are involved but I believe the four more characters bring more clarity then noise.

### Installation flags for native node modules
I have searched for a specific feature of npm/gyp for a long time - how can I enable the user to define installation flags that resolve in preprocessor `#define`s? The answer turned out to be very simple, but apparently my google-fu wasn't good enough. I found it when browsing the code of node-speaker. So I finally added a `--native_audio=true/false` flag to node-spotify that allows you to switch the ALSA/OpenAL backend totally of.

```bash
npm install node-spotify --native_audio=false
node-gyp build --release --native_audio=false
```

How is it done? Have a look:

```javascript
//binding.gyp
{
  "targets": [{
    "name": "node-spotify",
    "variables": {
      "native_audio%": "false"
    },
    "conditions": [
      ["OS=='mac' and native_audio='true'", {
        "sources": ["src/audio/openal-audio.c"],
        "link_settings" : { "libraries" : ["-framework", "OpenAL"] },
        "defines": ["NODE_SPOTIFY_NATIVE_SOUND"]
      }]
    ]
  }]
}
```

```c
//node-spotify.cc
...
#ifdef NODE_SPOTIFY_NATIVE_SOUND
    audio_init_native(application->fifo);
#endif
```

The % seems to be important, I don't know why. But it doesn't work without it. So now I can put code belonging to native audio in `#ifdef`s in C/C++ and don't even compile them when the user doesn't need them. I guess this also means double the release builds for me...

