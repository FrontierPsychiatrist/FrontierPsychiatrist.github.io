---
layout: post
title: "syncthing, berlinpixels"
date: 2014-07-21
---
This post will be a little less technical. Unfortunately, still no news on node-spotify.

# syncthing
Last year I switched back to Firefox from Chrome, a humble attempt for more privace (but I still use Google a lot, so...). One time I stumbled upon [syncthing](http://syncthing.net/) and thought it would be a cool idea to have my own file syncing that does not reside on some US server.

But I was too lazy to try it out. Last week that changed, I finally set it up on my three computers. If you know just a little about networks and are not afraid of a terminal it's really easy. Also the documentation is really good - that's a big thumbs up!

And it does what it promises. Well, I can only be sure that the file syncing works, if it is actually secure I can't really test.

I'm not sure if I'm really going to use it that much, I hadn't much use for Dropbox until now. But it was fun to install it and see it work.

# berlinpixels
Photografy isn't really my thing. For a really long time now I went through life without owning a camera. Apart from the one on my smartphone which is nowadays of course enough for a nice snapshot but fails horrendous at night.

Since in October I will go to Japan for two weeks and really want to have some nice pictures of that I bought a camera. No fully fledged DSLR - too large, to expensive. A Canon Powershot S120 which I surely will carry around all the time.

To get a feeling what I can do with the camera I went out and took a few photos, using the manual mode. Of course most of them are shit, not only technically but also the motive. But suddenly it struck me - as a fan of retro/pixel art, why not try to make at least that out of the photos? So many people go the HDR or b/w route, why not do something different?

You can see the result at [berlinpixels.com](http://berlinpixels.com).

It was actually not that hard. I'm using [gimp](http://www.gimp.org) to edit the pictures. The process is as follows:

* crop the picture to some interesting part
* resize the remaining picture, usely I choose a height around 600px
* pixelate the picture, trying out different pixel sizes. If there is a potion that needs more definition choose a lower pixel size only for that area
* now one of the most important parts: convert the image to an indexed image, choosing somewhere between 64-320 colors. You really have to try out a few settings, some pictures work with much less colors than others
* edit the palette if necessary. E.g. in the \"wasserturm\" picture I made the sky more blue. This is of course much easier on the now indexed image.

I quite like the outcome - the tempelhof pictures really nail an atmosphere I personally like. The ivy on the wasserturm picture looks just like in an old adventure game.

So, maybe I will actually continue to shoot some photos and get a little into this hobby. We'll see.

Oh, the [berlinpixels](http://berlinpixels) homepage is generated with [jade](http://jade-lang.com) out of a really small template and a JSON array containing the needed picture data

{% highlight javascript %}
    var pictures = [{
        name: 'wasserturm',
        date: '20.07.2014',
        file: 'wasserturm-pixel.png'
    }];
{% endhighlight %}
    
It doesn't have any dynamic element so every browser should be able to display it.
