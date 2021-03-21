---
title: No more node-spotify
date: 2017-04-26
aliases: [
  "/blog/no-more-node-spotify"
]
---

## Ceased development of node-spotify
After my very enthusiastic last post about a living node-spotify now the time has come to
announce that I will not develop node-spotify anymore.

There are several reasons for this. The final one was an email by the "Spotify brand protection team"
that I cannot use node-spotify.com. I understand that trademark laws are crazy and it is not really
their fault. But it really annoyed me. The website has been taken down since then.

Also, for two or more years now I was waiting for a newer version of C bindings for Spotify. `libspotify`, which node-spotify is based on is deprecated and does not support a lot of features.

Finally my interests simply have shifted. I have a lot of other ideas for projects and simply not enough time to to everything.

## Kubernetes!
Since my last customer project where we used [Kubernetes](https://kubernetes.io/). I have grown to really like it. Not just using it as a developer but actually also setting it up.

At techdev I installed a cluster out of three old computers with [CoreOS](https://coreos.com/). I am learning a lot about topics that I previously never had to touch. A little networking here, file systems there, sprinkle systemd...

## New email provider
My personal email address was hosted by my DNS registrar. For the most time this worked pretty good, but actually there was already one outage. For email that is not really acceptable. The web interface is also not really good.

A friend told me he switched to [mailbox.org](https://mailbox.org). mailbox.org is a German email provider that focusses on privacy and encryption. So I decided to move there, too. At 1 Euro per month for the basic package it's also really cheap. Payment is even possible via Bitcoin! And of course it supports custom domains so I can keep my old address!

To my surprise I actually got a lot more than just mail. Some of these features will help me move away more from Google one day.

* Calendar
* Address book
* File storage
* 2 Factor authentication

I am pretty happy so far and will definitely stay at mailbox.org

## SoundCloud Alexa skill
At techdev we started playing around with Amazon's Alexa using an echo dot. We first created a skill to manage shopping lists with Wunderlist, you can read about it [here](https://techdev.de/we-just-published-an-alexa-skill-for-wunderlist/).

Another idea came to my mind: Spotify and Amazon Music are available to play music with, but how great would it be to play music directly from SoundCloud? So I started to work on a skill to do that.

There are two videos of me showcasing the current state of the skill (the first one is a little boring, but I was super happy that it worked): [first one](https://www.youtube.com/watch?v=ddQGn98mbjU) and [second one](https://www.youtube.com/watch?v=ddQGn98mbjU). A short blog post with some technical details is available [here](https://techdev.de/soundcloud-for-alexa-getting-started-with-an-audio-playing-skill/).
