---
comments: true
created_at: 2010/04/29
date: 2010-04-29T00:00:00Z
excerpt: null
title: You're Going To Need A Bigger Toolbox
url: /2010/04/29/Youre-going-to-need-a-bigger-toolbox/
---

I'm just getting back from Newcastle after getting to present at the first Design It Build It conference. It was great to be back up in Newcastle and to see lots of familiar faces. As with most conferences it was also good to meet new people (especially those for whom it was their first conference) and to listen to people talking about interesting stuff. Personal highlights for me were David Singleton from Last.fm and Michael Brunton Spall from The Guardian going through really interesting case studies from their respective organisations. It's the sort of gritty content it's often hard to come by. Speaking did mean I missed out on most of the design track unfrotunately, but videos should be available soon and by all accounts it sounds like the larger designer crowd went home happy.

<object id="__sse3884141" width="425" height="355">
<param name="movie" value="http://static.slidesharecdn.com/swf/ssplayer2.swf?doc=biggertoolbox-100428092640-phpapp02&stripped_title=youre-going-to-need-a-bigger-toolbox" /><param name="allowFullScreen" value="true"/><param name="allowScriptAccess" value="always"/><embed name="__sse3884141" src="http://static.slidesharecdn.com/swf/ssplayer2.swf?doc=biggertoolbox-100428092640-phpapp02&stripped_title=youre-going-to-need-a-bigger-toolbox" type="application/x-shockwave-flash" allowscriptaccess="always" allowfullscreen="true" width="425" height="355"></embed></object>

I think my presentation at [DIBI](http://dibiconference.co.uk) went OK. I'd got a little bit carried away with cramming content in, which meant it felt rushed at times and I still went over by 5 minutes into my Q&A time. But hey, a few people said nice things afterwards.

I wanted to tell the world (of web developers) about as many different tools as I could. I think most people who read this blog have probably come across most of this software, heck you might be commiting code to it or already using it in production. But lots of people haven't ever heard of Memcached, never mind Cassandra or RabbitMQ. And more inportant than the specific software is the differnt types of tools available. Small web servers, message queues, HTTP caches, etc. Conferences are a good place to find and educate people. Hopefully I managed to do just that.

One thing I hope I got across, I'm not for a moment saying you shouldn't be using tools you know and love. Nor am I saying you should jump in and start using lots of crazy software. But keeping an eye on new developments can serve you well when it comes to deciding whether the best approach is really to build something from scratch. I've spoken to several people over the last few weeks who where starting to write simple queing systems using cron and mysql, or using a hand rolled file system based caching setup. And in both cases I think they would be better served by existing tools.

My slides had *a lot* of links in them and I mentioned during my talk I'm put a list of them somewhere:

Small Web Servers
-----------------

-   Nginx - <http://wiki.nginx.org>
-   Thin - <http://code.macournoyer.com/thin/>
-   Mongrel - <http://github.com/fauna/mongrel/>
-   Spawning - <http://pypi.python.org/pypi/Spawning/>
-   Unicorn - <http://github.com/defunkt/unicorn/>

Caching
-------

-   Memcached - <http://memcached.org>
-   Squid - <http://www.squid-cache.org>
-   Varnish - <http://varnish-cache.org>

Search Engines
--------------

-   Solr - <http://lucene.apache.org/solr/>
-   Xapian - <http://xapian.org>
-   Sphinx - <http://sphinxsearch.com>
-   Nutch - <http://lucene.apache.org/nutch/>
-   Whoosh - <http://whoosh.ca>

Message Queues
--------------

-   RabbitMQ - <http://rabbitmq.com>
-   Apache ActiveMQ - <http://activemq.apache.org>
-   Beanstalk - <http://kr.github.com/beanstalkd/>
-   Stomp Server - <http://stomp.codehaus.org>
-   MemcacheQ - <http://memcachedb.org/memcacheq/>

Non-relational Data Stores
--------------------------

-   CouchDB - <http://couchdb.apache.org>
-   MongoDB - <http://www.mongodb.org>
-   Tokyo Tyrant - <http://1978th.net/tokyotyrant/>
-   Cassandra - <http://cassandra.apache.org>
-   Redis - <http://code.google.com/p/redis/>

Data Mining
-----------

-   Hadoop - <http://hadoop.apache.org>
-   Hive - <http://wiki.apache.org/hadoop/Hive/>
-   Pig - <http://hadoop.apache.org/pig/>
-   Dumbo - <http://lastfm.com/dumbo/>
-   Disco - <http://discoproject.org>

Functional Testing
------------------

-   Cucumber - <http://cukes.info>
-   Cucumber-Nagios - <http://auxesis.github.com/cucumber-nagios/>

Server Provisioning
-------------------

-   Puppet - <http://puppetlabs.com>
-   Chef - <http://wiki.opscode.com/display/chef/Home/>
