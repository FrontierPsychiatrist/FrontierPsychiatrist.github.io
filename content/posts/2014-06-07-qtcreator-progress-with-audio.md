---
title: QTCreator, progress with audio
date: 2014-06-07
aliases: [
  "/blog/qtcreator-progress-with-audio"
]
---

## QtCreator
node-spotify development started with [vim](http://www.vim.org/). At the time I didn't really know vim and wanted to learn it, actually doing a project with it seemed as a good way. I even tried out a auto completion plugin (I don't remember the name). At some point node-spotify became too large for me to handle it with vim. Maybe there are a lot of plugins out there which would have helped but I didn't want to put too much time into it.

That was when I switched to [Sublime Text](http://www.sublimetext.com/). It was hip at the time, fast and didn't get in may way. For more than a year now I was very happy with it - only actual code completion was missing (again, there probably is a plugin somewhere that tries to do it). Coming from a Java background with excellent tooling like IntelliJ I certainly missed that convenience.

This week a colleague introduced me to [QtCreator](http://qt-project.org/wiki/Category:Tools::QtCreator). I had searched for a C++ IDE and tried out Eclipse and Code::Blocks, but somehow QtCreator got past me. I heard of Qt before but somehow I remembered it being a paid solution which I didn't like to do for a hobby project. Turns out I was wrong, it's free. And it is really great. It doesn't have nearly as many features as IntelliJ, but certainly enough to satisfy me. So now node-spotify is developed in QtCreator - but of course that should not impact the project.

## Progress in the audio code
After a long week of thinking I made some progress in the audio code. Context: I want to provide a way to register the `music_delivery` callback in Javascript. This means the user must have a way of switching between native audio and Javascript. I started out without thinking too much about this switch and just implemented the Javascript audio part. But I noticed I would have to add many `if`s here and there and it was getting really messy.

Some people nowadays think object oriented design is outdated and rather use functional or procedural styles. Again, coming from Java I think it still has its place. There isn't much object oriented design in node-spotify, just objects. When I noticed I couldn't switch session callbacks in libspotify while a session is active it hit me - why not actually use objects for this.

```c++
class Audiohandler {
private:
  audio_fifo_t* audioFifo;
  static int musicDelivery(...);
protected:
  virtual void afterMusicDelivery() = 0;
};

class NativeAudioHandler : public Audiohandler {};
class NodeAudioHandler : public AudioHandler {};

//Spotify.cc
class Spotify {
private:
  std::unique_ptr<AudioHandler> audioHandler;
}
```

Given correct implementations of `AudioHandler` I can now switch how audio is handled easily at runtime and follow RAII for everything that needs to be done (initializing the fifo, creating a thread for native audio and so on). Again I am very happy with these changes. They provide a much clearer separation of concerns.
