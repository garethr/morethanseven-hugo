---
comments: true
created_at: 2011/01/01
date: 2011-01-01T00:00:00Z
excerpt: null
title: Solr Libraries and Good API Design
url: /2011/01/01/Solr-libraries-and-good-api-design/
---

I'm a huge [Solr](http://wiki.apache.org/solr/) fan. Once you understand what it does (it's a search engine, which means more than you think) and how it works you spot lots of thorny problems that map to it's features really well. In my experience it's also very fast and very stable once installed and setup. Oh, and the community support is great as well.

When I talk to some folks about Solr all they can think about is full text search. The main reason for this I think is a number of poor libraries. I've come across lots of Python or Ruby libraries that simply say you don't have to know anything about Solr, just install this code and you get full text search! This works in the same way as using the default Mysql or Apache configs works, nowhere near as well as if you get your hands dirty even a little. Some of the ruby gems even ship the Solr jar file in the gem. Now you don't even need to know Solr exists. You take a generic configuration and run it using a rake task behind which is some unknown Java application server. Good luck debugging that when it goes wrong, that's one hell of a leeky abstraction.

In better news I've now found two excellent Solr libraries, one's that start with the assumption that you know what you're doing or happy to learn about the tools you're using. All you really want from a library is a good API that maps to how you write in that language.

Delsolr (Ruby)
--------------

The [delsolr](http://delsolr.rubyforge.org/) API is beautiful. It seemlessly merges the worlds of Ruby and Solr in a way that's easy to write and easy to guess. It's also clever, the design accepts that new features might be added to Solr before the library is updated or that the library might not support every usecase or option. In these cases you can still pass information through to Solr directly.

Solr's interface is based around URLs, so any library is really just giving you an interface to creating those URLs.Writing the following in Ruby:

    rsp = solr.query('standard',
                   :query => '*:*',
                   :filters => {:status => 'Active'},
                   :facets => [{:field => 'project'}]
        ])

Results in the following URL:

    /select?q=*:*&wt=ruby&facet=true&facet.field=status&facet.field=name&fq=status:Active

If you already know Solr and how to construct URLs for searches by hand you'll immediately get the Ruby code. You can probably even guess how to pass other params like sort or order.

Another nice touch is that you can use either hashes or Lucene search syntax for each attribute. So:

    :filters => {:status => 'Active'}

Is the same as:

    :filters => 'status:Active'

Sunburnt (Python)
-----------------

[Sunburnt](http://blog.timetric.com/2010/02/08/sunburnt-a-python-solr-interface) is a python Solr interface from the nice folks at [Timetric](http://timetric.com). I've not had chance to use this library in anger as it was released after I'd dont quite a bit of python-solr work in an old job but I'd definately use it now. The API looks like:

    rsp = solr.query('*:*').filter(status='Active').facet_by('project').execute()

It's based around chaining so again you can probably guess how to make further queries from even this simple example.

Both Sunburnt and Delsolr also support adding documents to the index.

Uses
----

Once you understand facets and the usefulness of filter queries you see lots of places where Solr is useful apart from text search. Lots of ecommerce operations use facetted search interfaces, I'm sure everyone has spent time clicking through nested heirachies and watching the numbers (showing the number of products) next to the links decrease? You can built these interfaces using SQL but it's incredibly expensive and gets out of hand quickly. Caching only helps a bit due to the number of permutations in all but the smallest stores or simplest products. It's a similar problem with tagging, it's pretty easy to kill your database

But it's not just things that have the word search in that you can map Solr to. Two good examples are Timetric (from whom the Sunburnt library comes from) and the [Guardian Content API](http://www.guardian.co.uk/open-platform/blog/what-is-powering-the-content-api). Both of these present lots of read data straight from Solr with great success and less database killing performance issues. Solr can really be seen as a simple place to denormalise your data, one advantage being that it keeps your database schema clean.

Learning More
-------------

Solr could do with better documentation for beginners. The wiki is an excellent reference once you know how to write schema and configuration files but I think the getting started section sacrifices introducing configuration in favour of getting people searching quicker. The example schema and solrconfig files that ship with Solr are also amazingly useful references (officially the best commented XML I've ever seen) but also intimidating to beginners. The Drupal community appear to be writing some good docs that fill this gap though, here's a few links that I'd recommend:

-   [Step by step guide including Tomcat](http://www.drupalconnect.com/blog/steve/configuring-apache-solr-multi-core-drupal-and-tomcat-ubuntu-910)
-   [Fantastic multi-part tutorial](http://synapticloop.com/tomes/solr/solr-tutorial/)
