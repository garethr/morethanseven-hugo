---
comments: true
created_at: 2007/09/16}
date: 2007-09-16T00:00:00Z
title: Getting going with Symfony
url: /2007/09/16/getting-going-with-symfony/
---

I've tinkered with a rather large number of these web application frameworks, in Ruby, Python, PHP and C\#, at some point over the last few years. But I've never really settled on any of them - mainly because my day job didn't need me to and also because I like playing with new toys. I even tinkered with [my own homage](http://morethanseven.net/posts/webphp/) to [Web.py](http://webpy.org/) in PHP which a few people have picked and are [running with](http://code.google.com/p/wephp/).

Right now though I've been doing some work with a [few](http://rbd.shiftnetwork.co.uk/) [people](http://www.bofh.org.uk/) on a Rails project. Their are some seriously nice things in Rails that I don't see elsewhere; really good testing support baked in for one, and some serious deplyments for another.

[Symfony](http://www.symfony-project.com/) is another PHP framework but one that only supports PHP5 (a good thing in my book). It's basically a slick integration of lots of existing mature PHP tools plus some good old MVC and tried and trusted Software Patterns. I also discovered from a few YAHOO's that they have started using it on some levels within YAHOO.

My interest piqued I found a couple of [related](http://www.slideshare.net/fullo/federico-feroldi-php-in-yahoo/) [presentations](http://www.slideshare.net/dustin.whittle/symfony-yui-professional-web-20) on [Slideshare](http://www.slideshare.net/) (my new favourite web app), and the site has a [high quality online documentation](http://www.symfony-project.com/book/1_0) which can also be [purchased in dead tree format](http://www.amazon.co.uk/Definitive-Guide-Symfony-Experts-Source/dp/1590597869).

Having a play around with Symfony it took me a little while to get everything up and running, but I'd kept some handy notes for later. Hopefully they might be useful to someone other than me.

I've dropped in a few variables denoted by braces where relevant. The paths are from my MAMP and PEAR based environment and I'm using a MySQL database, though you could just as easily use Sqlite.

First we set up a virtual host in Apache. You have to do this manually at the moment. I already have a script for setting up virtual hosts under apache on OS X which I might extend to do this for me if I end up using Symfony alot.

<code>

    <VirtualHost 127.0.0.1>
      ServerName 
      DocumentRoot "/Applications/MAMP/htdocs/{project}/web"
      DirectoryIndex index.php
      Alias /sf /Applications/MAMP/bin/php5/lib/php/data/symfony/web/sf
      <Directory "/Applications/MAMP/bin/php5/lib/php/data/symfony/web/sf">
        AllowOverride All 
        Allow from All
      </Directory>
      <Directory "/Applications/MAMP/htdocs/{project}/web">
        AllowOverride All
        Allow from All
      </Directory>
    </VirtualHost>

</code>

<code>

    cd /www
    mkdir {project}
    cd {project}

</code>

Now we dive into the actual symfony commands. We'll just setting up both a frontend and a backend for this app which I think is likely to be pretty typical. I quite like the idea of seperate entities for these.

<code>

    symfony init-project {project_name}
    symfony init-app frontend
    symfony init-module frontend {module_name}

</code>

Next we have to configure our database connection by editing /config/databases.yml

<code>

    all:
        propel:
          class: sfPropelDatabase
          param:
            dsn: mysql://{username}:{password}@localhost:3306/{database_name}

</code>

That done we can set up our data definitions in */config/schema.yml*. Read the documention for all the details but you can probably get the general idea.

<code>

    propel:
     item:
       id:
       title: varchar(200)
       description: longvarchar
       created_at:

</code>

And yet another quick change in */config/propel.ini*

<code>

    propel.database.createUrl = mysql://{username}:{password}@localhost:3306
    propel.database.url = mysql://{username}:{password}@localhost:3306/{database_name}

</code>

The next set of commands build our model files, create SQL for out chosen database and then create the tables in the database.

<code>

    symfony propel-build-model
    symfony propel-build-sql
    symfony propel-insert-sql

</code>

We'll quickly create a backend using the scaffolding. Note that *item* here is the model name which is taken from the *schema.yml* config file above.

<code>

    symfony init-app backend
    symfony propel-generate-crud backend items Item

</code>

All in all not bad going. And that's basically the same for most of the apps you're likely to develop. It could probably be a little easier in the database and apache config areas in fairness. Rails' use of an inbuild webserver really helps here. But the use of YAML to define the model is, in my mind, more straightforward that using Ruby in Rails migrations. I've an app in the works at the moment and I'm looking at using Symfony. If that pans out I'll blog more about the bits I'm interested in - the inbuilt unit testing framework high on my list.
