---
comments: true
created_at: 2010/06/29
date: 2010-06-29T00:00:00Z
excerpt: null
title: 'Python: What To Use?'
url: /2010/06/29/Python-what-to-use/
---

My friend [Jamie Rumbelow](http://jamieonsoftware.com) has started a new project and decided to use Python. He asked a great question over on [Stack Overflow](http://stackoverflow.com/questions/3143115/architecting-from-scratch-in-python-what-to-use) which basically came down to *what should I use for my first proper Python web application project*. After a quick prompting on twitter I decided to have a go. I've cross posted my anwser below more because it took as long as a typical blog post to write.

Frameworks
----------

OK, so I'm a little biased here as I currently make extensive use of Django and organise the Django User Group in London so bear that in mind when reading the following.

Start with Django because it's a great gateway drug. Lots of documentation and literature, a very active community of people to talk to and lots of example code around the web.

That's a completely non-technical reason. Pylons is probably purer in terms of Python philosophy (being much more a collection of discrete bits and pieces) but lots of the technical stuff is personal preference, at least until you get into Python more. Compare the very active Django tag on Stack Overflow with that of pylons or turbogears though and I'd argue getting started is simply easier with Django irrespective of anything to do with code.

Personally I default to Django, but find that an increasing amount of time I actually opt for writing using simpler micro frameworks (think Sinatra rather than Rails). Lots of things to choose from ([good list here](http://fewagainstmany.com/blog/python-micro-frameworks-are-all-the-rage)). I tend to use MNML (because I wrote parts of it and it's tiny) but others are actively developed. I tend to do this for small, stupid web services which are then strung together with a Django project in the middle serving people.

Worth noting here is appengine. You have to work within it's limitations and it's not designed for everything but it's a great way to just play with Python and get something up and working quickly. It makes a great testbed for learning and experimentation.

Mongo/ORM
---------

On the MongoDB front you'll likely want to look at the [basic python mongo library](http://api.mongodb.org/python/) first to see if it has everything you need. If you really do want something a little more ORM like then [mongoengine](http://hmarr.com/mongoengine/) might be what you're looking for. A bunch of folks are also working on making Django specifically integrate more seamlessly with nosql backends. Some of that is for future Django releases, but [django-norel](http://www.allbuttonspressed.com/projects/django-nonrel) has code now.

For relational data [SQLAlchemy](http://www.sqlalchemy.org/) is good if you want something standalone. Django's ORM is also excellent if you're using Django.

API
---

The most official Oauth library is [python-oauth2](http://github.com/simplegeo/python-oauth2), which handily has a Django example as part of it's docs.

[Piston](http://bitbucket.org/jespern/django-piston/wiki/Home) is a Django app which provides lots of tools for building APIs. It has the advantage of being pretty active and well maintained and in production all over the place. Other projects exist too, including [Dagny](http://zacharyvoase.github.com/dagny/) which is an early attempt to create something akin to RESTful resources in Rails.

In reality any Python framework (or even just raw WSGI code) should be reasonably good for this sort of task.

Testing
-------

Python has unittest as part of it's standard library, and unittest2 is in python 2.7 (but [backported to previous versions too](http://pypi.python.org/pypi/unittest2/0.1.4)). Some people also like [Nose](http://code.google.com/p/python-nose/), which is an alternative test runner with some additional features. [Twill](http://twill.idyll.org/) is also nice, it's a "a simple scripting language for Web browsing", so handy for some functional testing. [Freshen](http://github.com/rlisagor/freshen) is a port of cucumber to Python. I haven't yet gotten round to using this in anger, but a quick look now suggests it's much better than when I last looked.

I actually also use Ruby for high level testing of Python apps and apis because I love the combination of celerity and cucumber. But I'm weird and get funny looks from other Python people for this.

Message Queues
--------------

For a message queue, whatever language I'm using, I now always use RabbitMQ. I've had some success with stompserver in the past but Rabbit is awesome. Don't worry that it's not itself written in Python, neither is PostgresSQL, Nginx or MongoDB - all for good reason. What you care about are the libraries available. What you're looking for here is [py-amqplib](http://barryp.org/software/py-amqplib/) which is a low level library for talking amqp (the protocol for talking to rabbit as well as other message queues). I've also used [Carrot](http://github.com/ask/carrot/), which is easier to get started with and provides a nicer API. Think bunny in Ruby if you're familiar with that.

Environment
-----------

Whatever bits and pieces you decide to use from the Python ecosystem I'd recommend getting to who [pip and virtualenv](http://clemesha.org/blog/2009/jul/05/modern-python-hacker-tools-virtualenv-fabric-pip/) - note that fabric is also cool, but not essential and these docs are out of date on that tool). Think about using Ruby without gem, bundler or rvm and you'll be in the right direction.
