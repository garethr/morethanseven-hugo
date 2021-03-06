---
created_at: 2010/12/24
date: 2010-12-24T00:00:00Z
excerpt: null
publish: false
title: A Vagrant Ecosystem
url: /2010/12/24/A-vagrant-ecosystem/
---

As mentioned loudly and repeatedly on here and on Twitter I love [vagrant](http://vagrantup.com). While writing a chef cookbook to bootstrap my virtual machines I started thinking about how things around vagrant could help it be more useful. These might be things I'm going to do, or ideally get involved with others to do. If anyone has any other ideas, or suggestions please leave comments, I definately think this is the time for discussion.

Baseboxes
---------

I don't really want to have to maintain baseboxes but I want access to lots of them. I'm sure some people will want a *Ruby on Rails in a box* but all I really care about is having access to recent 32 and 63 bit vanilla linux distributions. I want a good source for trusted baseboxes. At the moment the approach is to look on the wiki, then look on the mailing list and then search the web, then create your own (even using [VeeWee](https://github.com/jedi4ever/veewee) it's still a little fiddly). I've managed to find good lucid, maverick and debian boxes, but have had problems with centos and a few others. Part of this is the rate of change recenty of both vagrant and now VirtualBox (both good things), part of it is the lack of reviews and shared experiences around baseboxes.

What I'd love to see is a single place where anyone can post a link to a basebox and vagrant users can come along and assign metadata about whether it worked and on what hardware, vagrant version, virtual box version, etc. It could even act as a tracker, counting downloads of boxes to gauge popularity.

Templated Vagrantfiles
----------------------

As mentioned previously I have a chef cookbook I use to bootstrap all my new virtual machines. My process is therefore: *vagrant init*, make some manual changes to the Vagrantfile (or copy it from elsewhere), *vagrant up*. I'm lazy and want a nicer way to reuse Vagrantfiles or to script their creation.

I started out thinking that the ability to point the init command at a template and to provide context on the command line might be a good idea. Now I'm wondering whether we just need a command line application which allows for writing or modifying the Vagrantfile? Something like:

    vagrant config vm.provisioner=:chef_solo
    vagrant config chef.recipe_url=http://cloud.github.com/downloads/garethr/chef-repo/cookbooks.tar.gz

Hosted cookbooks
----------------

I dissed the idea of a *Ruby on Rails in a box* basebox above but I still want to be able to let people more easily share custom configuration for specialist applications. But what I'd prefer would be people sharing packaged cookbooks, a bit like I've done for my default virtual machine setup. Again the beauty of this is it's pretty much just sharing a URL to a tar.gz file. This makes more sense to me at least than random people connecting to my chef server (I shouldn't know about their machines) and lowers the barrier to entry for those not interested in hosting their own chef server or using the opscode platform for local virtual machines.

I'm also not talking here about just sharing individual cookbooks like [cookbooks.opscode.com](http://cookbooks.opscode.com/), but rather a packaged collection of individual recipes designed for a specific purpose. A fully working solr instance, a django application server using apache/mod\_wsgi, etc.

Many of the points about baseboxes above would work here too I think. Having a good community resource which points to lots of cookbook tar files. Allowing people to feed back about what works for them. I've mainly taked about Chef here as that's what vagrant initially shipped with, with the puppet provisioner now ready to go with would stand for puppet manifests too.
