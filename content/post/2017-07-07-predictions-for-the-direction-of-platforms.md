+++
date = "2017-07-07T13:56:00Z"
title = "Predictions for the direction of serverless platforms"
url = "/2017/06/26/predictions-for-the-direction-of-serverless-platforms/"
+++


While at [JeffConf](http://www.jeffconf.com) I had a particularly
interesting conversation with [Anne](https://twitter.com/anne_e_currie),
[James](https://twitter.com/monkchips) and
[Guy](https://twitter.com/guypod). Inspired by that, and
the fact James is currently writing a blog post a day, I thought I'd
have a go at writing up some of my thoughts.

I want to focus on a couple of things relevant to the evolution of
Serverless as a platform and the resulting commercial ecosystem,
namely the importance of [Open Service Broker](https://openservicebrokerapi.org/)
and a bet on [OpenWhisk](https://openwhisk.incubator.apache.org/).

![](/public/predictions.png)

The above is a pretty high-level picture of how I think Serverless
platforms are playing out. I've skipped a few things from the diagram
that I'll come back to, and I'm not trying to detail all options, just
the majority by usage, but the main points are probably:

## OpenWhisk

OpenWhisk is one of a number of run-your-own-serverless platforms [vying
for
attention](http://redmonk.com/fryan/2017/03/02/serverless-redefining-devops/).
My guess is it's the one that will see actual market adoption over time.

One of the reasons for that is that [Red Hat recently announced they are supporting
OpenWhisk](https://developers.redhat.com/blog/2017/06/07/red-hat-and-apache-openwhisk/).
Specifically they are working to run OpenWhisk on top of Kubernetes
which I see as being something that will tap into the growing number of
people and companies adopting Kubernetes in one form or another.
Kubernetes provides a powerful platform to build higher-level user
interfaces on, and I see it ending up as the default here.

There are a few things that could make this prediction much messier. One
of those is that OpenWhisk is currently in the incubator for the Apache
Foundation. CNCF has been building up a product portfolio across tools
in this space, and has a [Serverless working
group](https://github.com/cncf/wg-serverless). It would be a shame if
this somehow ends up with a perceived need to _bless_ something else.
The other might be say an aquisition of Serverless Framework by Docker
Inc, or a new entrant completely.

## Open Service Broker

Without supporting services to bind to, Serverless computing doesn't look as
interesting. Although you'll see some standalone Lambda usage it's much
more common to see it combined with API Gateway, DynamoDB, S3, Kinesis,
etc. The catch is that these are AWS services. Azure (with Azure
Functions) and Google Cloud (with Cloud Functions) are busy building
similar ecosystems. This makes the barrier to entry for a pure
technology play (like OpenWhisk or something else) incredibly high.

AWS Services work together because they are built by the same
organisation, and integrated together via the shared AWS fabric. How can
you build that sort of level of agreement between a large number of
totally unconnected third parties? What about exposing internal services
(for instance your large Oracle on-premise cluster) to your serverless
functions? Enter Open Service Broker.

Open Service Broker has [been around since the end of last
year](https://www.cloudfoundry.org/open-service-broker-api-launches-as-industry-standard/).
It defines an API for provisioning, deprovisioning and binding services
for use by other platforms, for instance by Cloud Foundry or Kubernetes or
similar. Here's a quick [example
broker](https://github.com/cloudfoundry-samples/github-service-broker-ruby).
I think we'll see Open Service Broker powering some powerful
higher-level user interfaces in other platforms eventually which aim to
compete directly with, for instance, the ease of binding an API Gateway
to a Lambda function in AWS. Especially if you're a service provider I'd
get a march on the competition and start looking at this now. This is
also a potential avenue for the other cloud providers to expand on there
claims of openness. I'd wager on us seeing Azure services available over
Open Service Broker for instance.

## A few extra observations

* Anyone but Amazon will be real - on both the buyer side (a little) and
  the vendor side (a lot) you'll see a number of unlikely
  collaborations between competitors at the technology level.
* Open Source is a fundamental part of any AWS competition. AWS hired
  some great open source community focused people recently so expect to
  see them try to head that threat off if it gets serious
* The fact Amazon provides the entire stack AND runs a chunk of the
  infrastructure for the other options help explains why people (again
  especially other vendors) fear AWS.
* Both Pivotal and Red Hat will be in the mix when it comes to hosted
  serverless platforms, with CloudFoundry and OpenShift (powered by
  OpenWhisk) respectively.
* The stack in the middle of some OS, some provisioning, Kubernetes and
  OpenWhisk will have lots of variants, including from a range of
  vendors. Eventually some might focus on serverless, but for others
  it will be simply another user interface atop a lower-level
  infrastructure play.
* If you're thinking "But Cloud Foundry isn't serverless?" then you're
  not paying attention to things like the recent release of [Spring Functions](https://spring.io/blog/2017/07/05/introducing-spring-cloud-function).
* In theory Azure and Google Cloud could end up playing in the same place
  as AWS. But with the lead AWS Lambda has in the community that is going
  to take something more than just building a parallel ecosystem. I could
  totally see Azure embracing OpenWhisk as an option in Azure Functions,
  in the way Azure Container Service is happy to provide multiple
  options.

Overall I'll be interested to see how this plays out, and whether my
guesses above turn out to be anywhere near right. There will be a lot of
intermediary state, with companies large and small, existing and new,
shipping software. And like anything else it will take time to stablise,
whether to something like the above or something else entirely.
I feel like the alternative to the above is simply near total domination
by 1 or maybe 2 of the main cloud providers, which would be less
interesting to guess about and made for a shorter blog post.
