---
comments: true
created_at: 2011/01/16
date: 2011-01-16T00:00:00Z
excerpt: null
title: Why Developers Should Care About System Packages
url: /2011/01/16/Why-developers-should-care-about-system-packages/
---

First a bit of background. I'm a software developer (lately in Ruby and a tiny bit of Java, previously in Python, C\# and PHP; yes I got around a bit), but have spent enough time looking after production hardware (mainly debian, solaris and recently a bit of RHEL) to have a feel for sysadmin work. I even have friends who are systems administrators. I mainly use a shiny apple laptop for my development work, but I actually execute all the code on Linux virtual machines. The aim of this post is to bridge a divide, not start a flame war about specific tools.

I'm writing this partly to address [a tweet I made](http://twitter.com/CoffeeScript/status/18867889742282752) that in hindsight needed more than 140 characters. Actually a number of my recent tweets have been on the same theme so I should be more helpful. What I'm seeing recently is an increase in the ways I'm being asked to install software and for me at least that's annoying.

1.  Several projects will ask you to do something like *curl http://bit.ly/installsh | sh* which downloads a shell script and executes it.
2.  Some will insist I have git installed
3.  A new framework might come with it's own package manager

I'm a polyglot programmer (so I shouldn't care about \#3) that uses git for everything (scratch \#2) and who writes little bash scripts to make my life easier (exactly like \#1). So I understand exactly how and why these solutions appear fine. And for certain circumstances they are, in particular for local development on a machine owned and maintained by one person. But on a production machine and even on my clean and tidy virtual machines none of these cut it for me in most cases.

Most developers I know have only a passing awareness of packaging so I'm going to have an aside to introduce some cool tricks. I think this is one place where sysadmins go wrong, they assume developers understand their job and that they know the various tools intimately.

System Package Tips
-------------------

I'm going to show examples using the debian tools so these apply to debian and ubuntu distros. RPM and the Yum tool have similar commands too, I just happen to know debs better.

### List all installed packages

This one is a bit obvious, it's probably going to be available in anyones home grown package management system. But if you're installing software via hand using git or a shell script then you can't even ask the machine what is installed.

    dpkg -l

### List files from package

I love this one. Have you ever installed a package and wondered where the config files are? You can soft of guess based on your understanding of the OS file system layout but this command is handy.

    dpkg -L lynx
    /.
    /usr
    /usr/share
    /usr/share/doc
    /usr/share/doc/lynx
    /usr/share/doc/lynx/copyright
    /usr/share/doc/lynx/changelog.gz
    /usr/share/doc/lynx/changelog.Debian.gz

### Where did that file come from?

Have a file on disk that you're not sure where it came from? Ask the system package manager. The more everything is installed from packages the more useful this becomes.

    dpkg -S /bin/netstat

### Unmet dependencies

At the heart of a good package system is the ability to map dependencies and to have unmet dependencies installed as needed. Having tools to query that tree is useful in various places.

    apt-cache unmet

Will give you output a little like the followning:

    Package libdataobjects-sqlite3-ruby1.9.1 version 0.10.1.1-1 has an unmet dep:
     Depends: libdataobjects-ruby1.9

### What needs upgrading?

The apticron tool can alert you to packages that are now out of date. It's easy to set it up to email you each day for each host and tell you about packages that need upgrading. Remember that the reason one of these might have an update could be a documented security bug and it becomes even more important to know about it quickly.

    apticron report [Fri, 19 Jan 2007 18:42:01 -0800]
    ========================================================================

    apticron has detected that some packages need upgrading on: 

        faustus.example.com
        [ 1.2.3.4 ]

    The following packages are currently pending an upgrade:

        xfree86-common 4.3.0.dfsg.1-14sarge3
        libice6 4.3.0.dfsg.1-14sarge3
        libsm6 4.3.0.dfsg.1-14sarge3
        xlibs-data 4.3.0.dfsg.1-14sarge3
        libx11-6 4.3.0.dfsg.1-14sarge3
        libxext6 4.3.0.dfsg.1-14sarge3
        libxpm4 4.3.0.dfsg.1-14sarge3

I'm really not an expert on using debs but even I find these tools useful, and you don't get the same capabilities when you use anything else.

Good and bad examples
---------------------

Still here? Good. I'm going to pick on a few pieces of software to give examples of what I mean. All of this software I actively use and think is brilliant earth shattering stuff, I'm not dissing the software so if any fanboys reading can kindly not attack me please, I'm one of you.

### RabbitMQ (Erlang)

The nice folk building the [RabbitMQ](http://www.rabbitmq.com/) message queue provide downloads of the [source code as well as various system packages](http://www.rabbitmq.com/server.html). Knowing that some people will want to use the latest and greatest version of the application they also host the latest deboan packages in their [own package repo with details on their site](http://www.rabbitmq.com/debian.html).

### Chef (Ruby)

The [Chef configuration management system](http://wiki.opscode.com/display/chef/Home) also provides multiple methods to install their software. For people already using, happy and familiar with it they provide everything as a ruby gem. If you prefer [system packages they have those too](http://wiki.opscode.com/display/chef/Installation). They also provide their [own deb repo](http://wiki.opscode.com/display/chef/Package+Installation+on+Debian+and+Ubuntu) for people to grab the latest software.

### Cloudera Hadoop (Java)

Before I found the Cloudera Hadoop packages I remember having great fun [manually applying patches](http://morethanseven.net/2009/10/18/problems-installing-hadoop-and-dumbo-ubuntu.html) to get everything working. Cloudera do exactly the same as the above two developers, namely [host their owns debs](https://docs.cloudera.com/display/DOC/Hadoop+Installation+%28CDH3%29).

### RVM

[RVM](http://rvm.beginrescueend.com/) is a fantastic way of managing multiple ruby versions and multiple isolated sets of gems. But it's also probably the first place I saw the install from remote shell script approach.

    bash < <( curl http://rvm.beginrescueend.com/releases/rvm-install-head )

I like to do the same things on my development machine as I do in production, and the main problem I have with RVM is that it's so useful I want it everywhere. I'd prefer if the system wide install had some sort of option to install the rubies from packages rather than compile everything on the machine (meaning you need a full set of compile tools installed everywhere), or that we can automate the creation of the packages using rvm.

### Solr

You'll probably find packages for the [Solr search server](http://lucene.apache.org/solr/) in recent distros. It's hugely popular predominantly because it's a fantasic piece of software. But everytime I have a look at the system packages I can't quite get them to work, or they are out of date. I now know my way around Solr setup relatively well and just end up creating my own packages and I've spoken to other folk who have done the same. The [Solr documentation](http://lucene.apache.org/solr/tutorial.html) recommends downloading a zip file to get started and I can't see any mention of the packages. My guess is the packages aren't maintained as part of the core development which is a quick way to get them out of sync with current progress.

Enough beating up on my fellow developers
-----------------------------------------

System packages aren't blameless, I think the culture often seen in debian of splitting the developer from the package maintainer is part of the problem. This manifests in various ways, all negative:

-   Out of date packages. The biggest complaint from developers about system packages is nearly always that they are out of date. Maintainers should more readily release packaging scripts (ideally back to the project) so people can easily roll their own.
-   The documentation around packaging is either fantastic or terrible, depending on what you want to do and who you are. It turns out making your own packages (using something like [checkinstall](http://checkinstall.izto.org/)) is actually quite easy.
-   The official debian docs I think focus on the role of package maintainer, rather than trying to push that downstream to the developers. That doesn't make them bad, it just means we need documentation aimed at a developer just getting started with packaging their software.
-   Developers hosting their own package repository and asking people to point at that is also quite easy. The projects I praised above all do it nicely. But simple attractive documentation is hard to come by.

What to do
----------

First up lets talk more about the distribution and installation of software. And lets do that in the spirit of making things better for everyone involved. The ongoing spat between Ruby and Debian people is just counterproductive. [This](http://ryanbigg.com/2010/12/ubuntu-ruby-rvm-rails-and-you/) would be a good article if it didn't lead with:

> This system (apt-get) is out-dated and leads to major headaches. Avoid it for Ruby-related packages. We do Ruby, we know what’s best. Trust us.

We need better documentation aimed at developers. I'm going to try and write some brief tutorials soon (otherwise I'd feel like this rant was just me complaining) but I'm not an expert. I'll hapily help [promote](http://devopsweekly.com) or collate good material as well. Maybe it already exists and I just can't find it?

I'm a git user and a big [GitHub](http://github.com) fan, but one of the features of Launchpad I really like is the [Personal Package Archive](https://help.launchpad.net/Packaging/PPA). This lets you upload source code and have it automatically built into a package. This is specific to Ubuntu but that's understandable given Launchpad is also operated by Canonical. What I'd like is the same feature in GitHub but that allowed building debs and RPMs for different architectures. Alternatively a webhook based third party that could do the same would be awesome (anyone fancy building one? I might pitch in). The only real advantage of it being GitHub would be it would make packages immediately cool, which hopefully you all now realise that they are.
