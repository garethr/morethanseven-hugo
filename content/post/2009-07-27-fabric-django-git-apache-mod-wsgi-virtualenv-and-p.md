---
comments: true
created_at: 2009/07/27}
date: 2009-07-27T00:00:00Z
title: Fabric, Django, Git, Apache, mod_wsgi, virtualenv and pip deployment
url: /2009/07/27/fabric-django-git-apache-mod-wsgi-virtualenv-and-p/
---

I've been playing with automating Django deployments again, this time using [Fabric](http://fabfile.org/). I found a number of examples on the web but non of them quite fit the bill for me. I don't like serving directly from a repository, I like to have either a package or tar I can use to say "that is what went to the server". I also like having a quick rollback command as well as being able to deploy a particular version of the code when the need arises. I also wanted to go from a clean ubuntu install (plus SSH) to a running Django application in one command from the local development machine. The Apache side of things is nicely documented in [this Gist](http://gist.github.com/106077) which made a good starting point.

I'm still missing a few things in this setup mind and at the moment you still have to setup your local machine yourself. I'm probably going to create a paster template and another fabfile to do that I think. The instructions are a little rough as well at the moment and I've left the database out of it as everyone has there own preference.

This particular fabric file makes setting up and deploying a django application much easier, but it does make a few assumptions. Namely that you're using Git, Apache and mod\_wsgi and your using Debian or Ubuntu. Also you should have Django installed on your local machine and SSH installed on both the local machine and any servers you want to deploy to.

*note that I've used the name project\_name throughout this example. Replace this with whatever your project is called.*

First step is to create your project locally:

pre. mkdir project\_name
cd project\_name
django-admin.py startproject project\_name

Now add a requirements file so pip knows to install Django. You'll probably add other required modules in here later. Creat a file called requirements.txt and save it at the top level with the following contents:

pre. Django

Then save this fabfile.py file in the top level directory which should give you:

pre. project\_name
fabfile.py
requirements.txt
project\_name
*init*.py
manage.py
settings.py
urls.py

You'll need a WSGI file called project\_name.wsgi, where project\_name is the name you gave to your django project. It will probably look like the following, depending on your specific paths and the location of your settings module

pre. import os
import sys

1.  put the Django project on sys.path
    sys.path.insert(0, os.path.abspath(os.path.join(os.path.dirname(*file*), "../")))
    os.environ\["DJANGO\_SETTINGS\_MODULE"\] = "project\_name.settings"
    from django.core.handlers.wsgi import WSGIHandler
    application = WSGIHandler()

Last but not least you'll want a virtualhost file for apache which looks something like the following. Save this as project\_name in the inner directory. You'll want to change /path/to/project\_name/ to the location on the remote server you intent to deploy to.

pre. <VirtualHost *:80>
WSGIDaemonProcess project\_name-production user=project\_name group=project\_name threads=10 python-path=/path/to/project\_name/lib/python2.6/site-packages
WSGIProcessGroup project\_name-production
WSGIScriptAlias / /path/to/project\_name/releases/current/project\_name/project\_name.wsgi
<Directory /path/to/project_name/releases/current/project_name>
Order deny,allow
Allow from all
</Directory>
ErrorLog /var/log/apache2/error.log
LogLevel warn
CustomLog /var/log/apache2/access.log combined
</VirtualHost>

Now create a file called .gitignore, containing the following. This prevents the compiled python code being included in the repository and the archive we use for deployment.

pre. \*.pyc

You should now be ready to initialise a git repository in the top level project\_name directory.

pre. git init
git add .gitignore project\_name
git commit -m "Initial commit"

All of that should leave you with

pre. project\_name
.git
.gitignore
requirements.txt
fabfile.py
project\_name
*init*.py
project\_name
project\_name.wsgi
manage.py
settings.py
urls.py

In reality you might prefer to keep your wsgi files and virtual host files elsewhere. The fabfile has a variable (config.virtualhost\_path) for this case. You'll also want to set the hosts that you intend to deploy to (config.hosts) as well as the user (config.user).

The first task we're interested in is called setup. It installs all the required software on the remote machine, then deploys your code and restarts the webserver.

pre. fab local setup

After you've made a few changes and commit them to the master Git branch you can run to deply the changes.

pre. fab local deploy

If something is wrong then you can rollback to the previous version.

pre. fab local rollback

Note that this only allows you to rollback to the release immediately before the latest one. If you want to pick a arbitrary release then you can use the following, where 20090727170527 is a timestamp for an existing release.

pre. fab local deploy\_version:20090727170527

If you want to ensure your tests run before you make a deployment then you can do the following.

pre. fab local test deploy

The actual fabfile looks like this. I've uploaded a [Gist of it](http://gist.github.com/156623), along with the docs, so if you want to improve it please clone it.

pre. \# globals
config.project\_name = 'project\_name'

1.  environments
    def local():
    "Use the local virtual server"
    config.hosts = \['172.16.142.130'\]
    config.path = '/path/to/project\_name'
    config.user = 'garethr'
    config.virtualhost\_path = "/"
2.  tasks
    def test():
    "Run the test suite and bail out if it fails"
    local("cd $(project\_name); python manage.py test", fail="abort")
    def setup():
    """
    Setup a fresh virtualenv as well as a few useful directories, then run
    a full deployment
    """
    require('hosts', provided\_by=\[local\])
    require('path')
    sudo('aptitude install -y python-setuptools')
    sudo('easy\_install pip')
    sudo('pip install virtualenv')
    sudo('aptitude install -y apache2')
    sudo('aptitude install -y libapache2-mod-wsgi')
    \# we want rid of the defult apache config
    sudo('cd /etc/apache2/sites-available/; a2dissite default;')
    run('mkdir -p $(path); cd $(path); virtualenv .;')
    run('cd $(path); mkdir releases; mkdir shared; mkdir packages;', fail='ignore')
    deploy()
    def deploy():
    """
    Deploy the latest version of the site to the servers, install any
    required third party modules, install the virtual host and
    then restart the webserver
    """
    require('hosts', provided\_by=\[local\])
    require('path')
    import time
    config.release = time.strftime('%Y%m%d%H%M%S')
    upload\_tar\_from\_git()
    install\_requirements()
    install\_site()
    symlink\_current\_release()
    migrate()
    restart\_webserver()
    def deploy\_version(version):
    "Specify a specific version to be made live"
    require('hosts', provided\_by=\[local\])
    require('path')
    config.version = version
    run('cd $(path); rm releases/previous; mv releases/current releases/previous;')
    run('cd $(path); ln -s $(version) releases/current')
    restart\_webserver()
    def rollback():
    """
    Limited rollback capability. Simple loads the previously current
    version of the code. Rolling back again will swap between the two.
    """
    require('hosts', provided\_by=\[local\])
    require('path')
    run('cd $(path); mv releases/current releases/\_previous;')
    run('cd $(path); mv releases/previous releases/current;')
    run('cd $(path); mv releases/\_previous releases/previous;')
    restart\_webserver()
3.  Helpers. These are called by other functions rather than directly
    def upload\_tar\_from\_git():
    require('release', provided\_by=\[deploy, setup\])
    "Create an archive from the current Git master branch and upload it"
    local('git archive --format=tar master | gzip &gt; $(release).tar.gz')
    run('mkdir $(path)/releases/$(release)')
    put('$(release).tar.gz', '$(path)/packages/')
    run('cd $(path)/releases/$(release) && tar zxf ../../packages/$(release).tar.gz')
    local('rm $(release).tar.gz')
    def install\_site():
    "Add the virtualhost file to apache"
    require('release', provided\_by=\[deploy, setup\])
    sudo('cd $(path)/releases/$(release); cp $(project\_name)$(virtualhost\_path)$(project\_name) /etc/apache2/sites-available/')
    sudo('cd /etc/apache2/sites-available/; a2ensite $(project\_name)')
    def install\_requirements():
    "Install the required packages from the requirements file using pip"
    require('release', provided\_by=\[deploy, setup\])
    run('cd $(path); pip install -E . -r ./releases/$(release)/requirements.txt')
    def symlink\_current\_release():
    "Symlink our current release"
    require('release', provided\_by=\[deploy, setup\])
    run('cd $(path); rm releases/previous; mv releases/current releases/previous;', fail='ignore')
    run('cd $(path); ln -s $(release) releases/current')
    def migrate():
    "Update the database"
    require('project\_name')
    run('cd $(path)/releases/current/$(project\_name); ../../../bin/python manage.py syncdb --noinput')
    def restart\_webserver():
    "Restart the web server"
    sudo('/etc/init.d/apache2 restart')