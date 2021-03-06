---
comments: true
created_at: 2008/12/28}
date: 2008-12-28T00:00:00Z
title: Local Continuous Integration with Integrity
url: /2008/12/28/local-continuous-integration-integrity/
---

[Integrity](http://integrityapp.com/) is ace. I'm a huge fan of working under the ever watchful eye of a [Continuous Integration](http://en.wikipedia.org/wiki/Continuous_integration) server. I'm also becoming more and more of a fan of Git, and [GitHub](http://github.com), for my [personal projects](http://github.com/garethr). At work we run [CruiseControl](http://cruisecontrol.sourceforge.net/) and it does it's job well, but locally I only use it for larger projects. It comes with a little overhead and if I'm just hacking on the train I rarely check on it's status.

Integrity is an unashamedly lightweight and straightforward continuous integration server written in Ruby. It comes with Git integration as well as a nifty notifications framework. There are already notification plugins available for jabber, IRC and email. If I get time and inclination I'd love to hack together a webhooks plugin too. It's a simple app to get up and running with and you can always [browse the code](http://github.com/foca/integrity/tree/master) if something isn't clear. All in all it's perfect for my type of smaller project.

So, with a local *CI* server up and running you're left with one problem; having to click the \_Manual Build" button whenever you want a build. Now Integrity comes with a mechanism to allow pushes to GitHub to trigger a build. But this only works when you have an internet connection and are using GitHub and are pushing frequently. Personally I often make lots of local commits and then push at a later date. Also not all of my projects are on GitHub for various reasons.

Well it turns out that all the build button does is sent an empty HTTPpost request to a URL of the following format:

    http://{integrity-url}:8910/{project-name}/builds

That means with a little bit of Git magic we can have our integration server run a new build whenever we commit our code. All we need to do is write a very simple post-commit git hook script. I've written the script in Python but you could write it in anything. This script is from a real project so adjust the server address and path as needed.

    #! /usr/bin/env python
    import httplib
    conn = httplib.HTTPConnection("localhost:8910")
    conn.request("POST", "/appengine-books/builds")

All you need to do is drop this script in your .git/hooks folder as *post-commit*. Make sure you set the executable bit with *chmod +x* as well, otherwise you'll be wondering why it's not working and probably blaming me.
