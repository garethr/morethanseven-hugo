---
comments: true
created_at: 2011/11/21
date: 2011-11-16T00:00:00Z
excerpt: null
title: Jenkins Parameterized Builds
url: /2011/11/16/Jenkins-parameterized-builds/
---

I'm a huge Jenkins fan now, but that wasn't always the case. I started (and still have a soft spot for) Cruise Control, mainly building .NET and PHP applications. I then jumped to much simpler projects like [Integrity](http://integrityapp.com/) mainly for Python and Ruby projects. I reasoned I didn't need the complexity of Cruise or Hudson, I just wanted to be able to run my tests on a remote machine and have something go green or red. I then worked out that wasn't quite the case, and ended up committing webhook like functionality to Integrity so I could chain builds together. And then I eventually tried Jenkins and found it's power mixed with flexibility won me over. That's really all just context, but hopefully explains a little about why I like a few Jenkins features in particular, one of which is [Parameterized builds](https://wiki.jenkins-ci.org/display/JENKINS/Parameterized+Build).

The Jenkins wiki describes this by saying:

> Sometimes, it is useful/necessary to have your builds take several "parameters."

But then goes onto a usecase that probably won't mean much to dynamic language folk. This is one failing of much of the documentation around Jenkins, it often feels geared towards developers of certain languages when in reality the tool is useful everywhere. The important take away here is that builds can take arguments, which can have default values. Here's an example:

Imagine we have a build which runs a set of simple tests against a live system . And further imagine that said system is composed of a number of different web services. Oh, and we're running a few different parrallel versions of the entire system for testing and staging purposes. We could have one Jenkins job per application/environment combination. Or we could have one parameterized build.

Lets first specify our parameters from the Configure build screen of our Job.

<img src="http://image-host.appspot.com/i/img?id=agppbWFnZS1ob3N0cg0LEgVJbWFnZRjZsAIM"/>

Here we're specifying a TARGET\_APPLICATION and TARGET\_PLATFORM parameter. These are going to turn into environment variables we can use in our build steps. We can specify default values for these if we like too. I'm just using strings here, but I could also use a select box or file dialog, or other options provided by various plugins.

Now when we hit the build button, instead of the build just starting, we're propted for these values.

<img src="http://image-host.appspot.com/i/img?id=agppbWFnZS1ob3N0cg0LEgVJbWFnZRjxqAIM"/>

So with our new build if we want it to run against the staging environment and just for the foobar application we enter those values and hit build. That on it's own can be used to drastically cut down on the number of individual builds you have to manage in Jenkins. And we're not just restricted to text inputs, we can use boolean values or even prompt for file uploads at build time. But throw in a few plugins and things get even more interesting.

Jenkins has an overwhelming number of plugin available. If you haven't spent the good half hour it takes to go down the list I'd highly recommend it. One of Jenkins best features is the ability to trigger a build after the successful run of another job. It allows you to chain things like test runs to integration deployments to smoke tests to production deploys. Basically your modern continuous deployment/delivery pipeline. The [Build Pipeline](https://wiki.jenkins-ci.org/display/JENKINS/Build+Pipeline+Plugin) plugin is excellent for visuallising this and introducing human gates if needed. Another useful plugin in this context is the [Parameterized Trigger](https://wiki.jenkins-ci.org/display/JENKINS/Parameterized+Trigger+Plugin) plugin. A limitation of Jenkins is that downstream builds can't pass parameters, but this plugin works around that. Instead of ticking the *Build other projects* option you go for the *Trigger parameterized build on other projects* box. This allows you to select the project and to specify parameters to pass. This could be hard coded values, paramaters already passed into the pipeline, or things from other plugins like the git sha1 hash or subversion version number.

<img src="http://image-host.appspot.com/i/img?id=agppbWFnZS1ob3N0cg0LEgVJbWFnZRjyqAIM"/>

Combine all this together and it's simple to have a per project continuous integration build running a test suite, kicking off a standard set of downsteam jobs for deploying to a test environment (by passing th relevant parameters), running some basic smoke tests and allowing someone to click a button to deploy to production. Or going the whole continuous deployment, I trust my test suite route, and deploying automatically. All within Jenkins. Getting this working requires a bit of planning. You want all of your projects to be deployed the same way but you probably want this to be the case anyway.

Providing flexible push button builds/deploys and reducing the number of nearly identical jobs in Jenkins are just two advantages to using parameterized builds. Most of the tricks come from thinking about Jenkins as much more than a continuous integration tool and more of an automation framework - I know at least one large organisation who have pretty much replaced cron for many tasks with Jenkins for instance. Running tests automatically, and in a central environment as close to production as possible, is important. But it's just a sanity check if you're doing everything right already. Centralising activity on a build pipeline requires you to be doing all that anyway, but in my opinion gives way more useful and rapid feedback about the state of the code your team is writing.
