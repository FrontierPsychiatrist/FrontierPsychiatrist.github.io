---
layout: post
title: "AWS summit, node-spotify"
date: 2014-05-17
---

## AWS summit
On thursday, I went to the [AWS](https://aws.amazon.com/) (Amazon Webservice) summit in Berlin. I don't have much experience with AWS but thought it would be interesting to here some people talking about it. I will give you a short overview over the talks I attended.

A quick note on the event as itself: It was hosted in the Postbahnhof near Ostbahnhof which really wasn't fitted for so many people. One \"room\" was a tent next to the S-Bahn tracks, the speakers and microphones were setup in a way that had constant feedback. I think this could be improved.

### Keynote
The keynote was mainly held by Werner Vogels, the CTO of Amazon.com. It felt very much like a PR event - for me as a programmer most of the stuff he talked about was irrelevant.
![Werner Vogels](/img/aws_vogels.jpg)
A few guest speakers were invited, two of them spoke for companies that one would not immediately associate with AWS. But the guy from Kärcher didn't really talk about AWS and how they use it but mostly about their product.

### Kinesis
The next talk was about Kinesis. It was presented as a managed service in AWS to analyze a large stream of realtime data. To be honest I couldn't really tell what the difference between Kinesis and a message queue is from this talk.
You get an HTTP API to put your data into it and other services like EC2 or Beanstalk get access to that data.

### Live programming a web API
The next talk was about putting a web API (used for a single page application) together with mostly only managed services from AWS.
I really liked this talk, as a novice in AWS services I got a lot of information on what there is and how I can connect it. The solution was probably over engineered, but as the point was to show a few services that is quite ok. He used S3 as storage for the static files, DynamoDB for some dynamic data, SQS for a queue, Elastic Beanstalk for a small NodeJS application, Route 53 for some routing and Cloudfront for caching (I think).

### Amazon Linux
The guy talked about Amazon Linux, a derivative of Redhat Linux that is heavily used by Amazon and free to use on EC2 instances.
While before the talk I didn't even know it existed I didn't get too much useful information out of it. It's RPM based and they really try to fit it to the different EC2 types of instances. He claimed this will get you the best perfomance.

 Overall it was a nice day - but I don't think I will attend again, at least not for the talks. Of course meeting a lot of people that are in the same industry as you can always be benefitial.

## node-spotify
As mentioned in the last post I revisited callbacks, especially for playlists. The plan I had in my head last week didn't work out. I ended up changing the API more than I wanted. But then, I'm still at version 0.6 so node-spotify is not stable.

Instead of

{% highlight javascript %}
playlist.on('playlist_renamed', function(err, pl) {...});
playlist.on('tracks_added', function(err, pl, tracks, position) {...});
{% endhighlight %}

it is now

{% highlight javascript %}
playlist.on({
  playlistRenamed: function(err, pl) {},
  tracksAdded: function(err, pl, tr, pos) {}
});
{% endhighlight %}

This allows me to register all callbacks at once in C++/C and to remove old ones. Maybe it's actually easier for the user, too. You can save the callbacks in one object and attach them to a playlist in one call.
        
I have already ported the callbacks for playlists and playlist containers, after the player follows this feature is ready. This also means I can rid of my most complex (in terms of C++) class, [`NodeWrappedWithCallbacks`](https://github.com/FrontierPsychiatrist/node-spotify/blob/740e41132896bd51c1d8b4476c5f11f78fcbc717/src/objects/node/NodeWrappedWithCallbacks.h).
          
### The nice and the raw API
For a long time I have been thinking of providing some more methods and callbacks to the user of node-spotify and writing the \"nice-and-easy\" facade completely in JavaScript. Today I got a request to access the `music_delivery` callback in JavaScript. This is of course a nice feature but a little out of scope for my initial approach of providing a very easy wrapper around libspotify.

My plan is to leave the `spotify` object you get from node-spotify as it is, but provide an `spotify.internal` field with some advanced stuff.
