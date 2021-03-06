---
comments: true
created_at: 2009/02/05}
date: 2009-02-05T00:00:00Z
title: Hosting Images on App Engine
url: /2009/02/05/hosting-images-app-engine/
---

I like adding images to the occasional blog post but don't do it as often as I want to. The reason being I can hardly ever be bothered to resize images. It means opening up a memory hogging application, fiddling around for a few minutes and then saving it out somewhere, then uploading said image to my server. All of those things bore me.

Appengine to the rescue. As an excuse to play around with the [image API](http://code.google.com/appengine/docs/python/images/overview.html) as well as add more pictures to this here blog I decided to build myself a small image hosting application. I really only care about the two things noted above; resizing and uploading. If I can do that in one go I'm happy.

A couple of train rides (all the best code gets written on train journeys, fact) later and I've deployed the first version of Image Host which looks a little something like this:

<img src="http://image-host.appspot.com/i/img?id=agppbWFnZS1ob3N0cgwLEgVJbWFnZRjtBww" alt="Image Hosting interface on Google App Engine"/>

(And yes that image is an image of image-host hosted on image-host. If you're thinking *what image?* then it means this experiment isn't working at the moment.)

The application is designed in such a way as you could have as many people uploading and managing their own set of images as you want, all within one instance. The only reason I don't just open it up to any google user account is that it would eat into my quotas if it got popular and I have no interest in policing whatever dubious images anyone might upload.

I've been playing with App Engine quite extensively of late. I really appreciate the SDK and the limited, but well designed and thought out APIs and testing stubs. At the moment I'm happy with where it's at. It's obviously early days but in my mind all I really want are APIs for offline processing, some sort of message queuing facility, XMPP support and a payment model for raising the limitations. And several of those are already slated for the near future.

If anyone wants to host their own version you can grab the code from [GtHub](http://github.com/garethr/appengine-image-host/tree/master). You'll need your own App Engine account and your own snappy name but that's all. If you really don't want to do that, have a good reason to needing image hosting and ask nicely I might add more people to my version as well.

Their are a few bugs I'm going to fix when I get a moment, a few documented limitations I know about and I'm still fleshing out the test suite to cover all the functionality. But all in all a worthwhile few hours spent hacking. I'll probably get round to writing up some pointers for testing App Engine code as well as their are a host of gotchas and the only real documentation at present is comments in the code.
