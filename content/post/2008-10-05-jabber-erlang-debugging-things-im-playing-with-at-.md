---
comments: true
created_at: 2008/10/05}
date: 2008-10-05T00:00:00Z
title: Jabber, Erlang, Debugging. Things I'm playing with at the moment
url: /2008/10/05/jabber-erlang-debugging-things-im-playing-with-at-/
---

I'm busy experimenting with various blogging approaches at the moment, hence the short links I've been posting recently. Another type of post I thought I'd give a try to was the list of interesting things. I find this sort of thing strangely cathartic - if nothing else by writing down the things I'm thinking about I won't forget to spend time playing with them.

-   I still need to play around some more with Jabber/XMPP. I tried a little time ago to install a server locally on my Mac with only some success. I've now got a load of linux virtual images handy which might make that easier. What I would really like to see is a dirt simple XMPP server that you can use for local development. Something like [Morbid](http://pypi.python.org/pypi/morbid/0.8.2). I'm just not sure I have the time to build one.
-   Which brings me on to virtualisation. I'm more convinced than ever that sand boxing different local environments is a good idea. I now have a stack of VMWare images set up for configuring.
-   It turns out [Yahoo used Erlang](http://blog.socklabs.com/cufp2008.html) under the hood for some part of the new [Delicious](http://del.icio.us) I'm still pretty interested in actually doing something with Erlang, though what I'm not yet sure. I've been kicking round an idea for ages involving logging which might fit the bill.
-   I talked at barcampbrighton recently about debugging tools for django (and more broadly any development environment) that you need once you have a reasonable sized team. I've been busy packaging some of the tools I mentioned. I'll either turn it into an article somewhere, multiple blog posts and/or hopefully get some code released on google code some time soon.
-   The [Sphinx](http://sphinx.pocoo.org/) documentation system used by Django for the [1.0 documentation](http://docs.djangoproject.com/en/dev/) is pretty nifty. I'm investigating it for use at work at the moment.
-   I've been a huge fan of Textile since I used Textpattern years ago for an earlier incarnation of this site. But recently I've had a few niggling issues around extensibility and slight differences in implementation. So I'm pondering using [ReStructuredText](http://docutils.sourceforge.net/rst.html) for my writing duties. It appears to be more powerful, more flexible and inherently extensible.
