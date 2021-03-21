---
title: node-spotify, it lives!
date: 2016-06-11
aliases: [
  "/blog/node-spotify-lives"
]
---

## It lives
Finally, over 6 month after [the issue](https://github.com/FrontierPsychiatrist/node-spotify/issues/97)
has been reported on github, node-spotify compiles again on "recent" versions of NodeJS.
The last working version was 0.12, but since two weeks ago it supports 0.10, 0.12, 4.x, 5.x and 6.x.
This is thanks to an update of the [Nan](https://github.com/nodejs/nan) library. Sadly a major
upgrade was needed that breaks a lot of usages itself, just to fix V8 compatibly.

## But wait there's more!
Today I additionally finally got around to use [node-pre-gyp](https://github.com/mapbox/node-pre-gyp)
to distribute binary releases of node-spotify. Before I uploaded the `.node` files manually
to the website [node-spotify.com](http://node-spotify.com), but that was a tedious process both
for users and me.

Now the binary distribution is transparent for the user. A normal `npm install` will try to download the
binaries for the architecture, platform and NodeJS ABI from S3 and if they are not present compile
from source. I just have to add a step to publish to S3 after publishing to NPM. Hooray!

## One more bonus
Oh, I'm not stopping. Additionally to again-compiling code and easier binary distribution, node-spotify
is now built [on Travis](https://travis-ci.org/FrontierPsychiatrist/node-spotify).

This means I can see compile errors for different version of NodeJS (I enabled 0.10, 0.12, 4, 5, 6)
automatically and fast!

Because tests for node-spotify are a bit tricky (an appkey file and a premium login to Spotify are needed,
apart from the integration tests there is not much to test) - there are none that run automatically.
While as a professional software developer I would like to never do this, right now I don't see a good setup.

Anyway, this is totally awesome!

## Thoughts
I finally got around to fix and improve node-spotify (despite the un-motivating deprecation of libspotify).
I'm super happy and excited about this, it just feels good. Maybe I can also start working on new features
(there are two open PRs with minor addtions).

Apart from that, even as a non-soccer fan it is time to enjoy the games in the next few weeks. Doom and Dark
Souls 3 also still await completion.
