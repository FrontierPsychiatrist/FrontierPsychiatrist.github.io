---
title: Migrating to Jekyll and github.io
date: 2016-05-02
aliases: [
  "/blog/migrating-to-jekyll"
]
---

## node-spotify
So, node-spotify hasn't been updated in a long time. It's grown a lot over 100 stars on Github,
which I'm very proud of.

Since NodeJS version 4, I think that's after the io.js merge it doesn't compile anymore. I
started migrating to a newer NaN version, but that's a _lot_ of compile errors to fix - and
then I will have to see if it even runs.

Recently some friendly fellows offered help over at [github](https://github.com/FrontierPsychiatrist/node-spotify/issues/97). Since this week I finally am working in Berlin again, maybe I'll start to try over with the
NaN migration.

I have to say, since `libspotify` was declared deprecated by Spotify my motivation is not super
high. It's a sad thing, but "newer" features will never make it into node-spotify like this. The
new library announced last year by Spotify is still to be seen, the website now lists the "hope to be able
to provide a release in 2016".

## Jekyll, github.io
For a long time, [frontierpsychiatrist.de](http://frontierpsychiatrist.de) and
[berlinpixels.com](http://berlinpixels.com) have been served from a Raspberry Pi at my home.
The blog was hosted with Ghost, but at some point I took that down and it was not available anymore.

Since my Raspberry Pi is now used as an [openelec](http://openelec.tv/) machine I decided to move
to a different hosting. I looked at Github pages and their recommendation for [Jekyll](https://jekyllrb.com).

I have to say, wow. I basically poorly emulated Jekyll with some custom Jade templating, but it has
everything I need and more. And hosting with github is easier than ever. I don't even have to care about
some sort of build, just push my posts and Github does the rest. So props to them!

Only bad thing is, https is no more for frontierpsychiatrist.de. I made the classic mistake of enforcing
https via a 301 redirect, but that means I had to teach my browsers to forget about that. Not so easy
for other visitors. Luckily I'm pretty sure no one is visiting this site anyway :D

# 2016
So, a quarter of 2016 is already over. I _really_ hope I can finally get back into working on
node-spotify. There's also another project I would be so glad to finally "kickstart" and get a working
version out - it's something about my day-to-day IDE, IDEA by Jetbrains. We'll see. Maybe I'll get around to
writing more updates here more often. At least my new Jekyll setup makes that easier.
