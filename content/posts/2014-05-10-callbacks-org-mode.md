---
title: Ghost, callbacks, org-mode
date: 2014-05-10
aliases: [
  "/blog/callbacks-org-mode"
]
---

## Ghost
You probably noticed this blog is powered by [Ghost](https://github.com/TryGhost/Ghost). I tried it when it was all over HackerNews half a year (?) ago but it didn't feel very polished (and was not announced as!). This week I felt like trying it again. Setup was really easy and it works just fine.

So I guess I will try to write something here once a week. Mostly about programming stuff.

## Callbacks in node-spotify
This week I hopefully gave playlist callbacks in [node-spotify](http://www.node-spotify.com/) a final thought.
The current situation is: the user can access playlists and attach a callback like so

```javascript
var playlists = spotify.playlistContainer.getPlaylists();
var playlist = playlists[0];
playlist.on('playlist_renamed', fn);
```

Problem: If you call `getPlaylists()` twice you will get two different playlist arrays that contain new playlists.

```javascript
playlists[0].id = 1;
playlists = spotify.playlistContainer.getPlaylists();
playlists[0].id; //undefined
```

I decided to save a relationsship of libspotify's `sp_playlist` and the V8 objects.

```c++
std::map<sp_playlist*, V8Wrapped*> objectMapping;
```

Otherwise I wouldn't know which V8 object to use in a callback. Take `tracks_added` for an example:

```c
void(*tracks_added)(sp_playlist *pl, sp_track *const *tracks, int num_tracks, int position, void *userdata)
```

I want to provide

```javascript
function tracksAdded(playlist, tracks[])
```

in Javascript, so I need to know how to get the V8 wrapped `sp_playlist`. With the `objectMapping` map i can access it.
 
Bu this comes with the cost of added complexity in the C++ code. Also if the user gets the playlists twice with this approach only the _last_ accessed playlist will be used in the callbacks!

I have come to the conclusion that this is actually more counterintuitive than just attaching the callbacks to the JavaScript object and providing it as the userdata in the callback. So I will change the node-spotify implementation to go that way.

This will put a little more work to users of node-spotify: You have to track your playlists more carefully. If a playlists objects goes out of scope it is advisefully to unregister all callbacks, otherwise you have to wait for the garbage collector to kick in and the callbacks to be unregistered in the destructor.
    Another nice thing that I can do better with this approach is to register the callbacks in libspotify only when the user registers them in node-spotify. Currently all playlist callbacks will be registered for every playlist and it will be checked in the callback function itself if the user had interest in this specific callback.

## Org Mode
When coming to vim and emacs I always used vim. I started node-spotify completely in vim but nowadays it's only my \"console text-editor\", not everyday work editor.

I always wanted to learn a bit of emacs to see how it works. I read about [Org Mode](http://orgmode.org/) sometime last year and thought it sounded really cool. This week I finally came around to give it a try.
First I have to say that it's really a pain to get emacs working on OSX, just because of the meta key. Alt does not work. If you switch alt to meta in Terminal.app you will lose the ability to input ~ which is not viable.

So I started with Emacs.app which runs in Cocoa and uses the alt key as meta while still maintaining the ability to use the special characters that are accessed via alt. But - copy-paste via cmd+c, cmd+v does not work. It also stops responding if I close a file with changes and the \"do you want to save\"-dialog comes up.

Nonetheless - Org Mode turned out to be really nice. I probably only scratched its surface with some todos and formatting. The concept of a plain/human readable text file that can be parsed as an agenda file by a text editor is really to my liking.
