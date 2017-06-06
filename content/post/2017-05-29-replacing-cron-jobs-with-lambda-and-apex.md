+++
date = "2017-05-29T13:56:00Z"
title = "Replacing cron jobs with Lambda and Apex"
url = "/2017/05/29/replacing-cron-jobs-with-lambda-and-apex/"
+++

Everyone has little scripts that want running on some schedule. I've
seen entire organisations basically running on cron jobs. But for all
the simplicity of cron it has a few issues:

* It's a per-host solution, in a world where hosts might be short-lived
  or unavailable for some other reason
* It requires a fully configured and secured machine to run on, which
  comes with direct and indirect costs

There are a variety of _distributed cron_ solutions around, but each
adds complexity for what might be a throw-away script. In my view this
is the perfect usecase for trying out AWS Lambda, or other Serverless
platforms. With a Serverless platform the above two issues go away from
the point-of-view of the user, they are below the level of abstraction
of the provided service. Lets see a quick example of doing this.


## Apex and Lambda

There are a number of frameworks and tools for helping deploy Serverless
functions for different platforms. I'm going to use [Apex](http://apex.run/)
because I've found it provides just enough of a user interface without
getting in the way of writing a function.

Apex supports a wide range of different languages, and has [lots of
examples](https://github.com/apex/apex/tree/master/_examples) which
makes getting started relatively easy. Installation is
[straightforward](http://apex.run/#installation) too.


## A sample function

The function isn't really relevant to this post, but I'll include one
for completeness. You can write functions in officially supported
languages (like Javascript or Python) or pick one of the languages
supported via a shim in Apex. I've been writing Serverless functions in
Go and Clojure recently, but I prefer Clojure so lets use that for now.

```clojure
(ns net.morethanseven.hello
    (:gen-class :implements [com.amazonaws.services.lambda.runtime.RequestStreamHandler])
    (:require [clojure.java.io :as io]
              [clojure.string :as str])
    (:import (com.amazonaws.services.lambda.runtime Context)))

(defn -handleRequest
  [this input-stream output-stream context]
  (let [handle (io/writer output-stream)]
    (.write handle (str "hello" "world"))
    (.flush handle)))
```

This would be saved at
`functions/hello/src/net/morethanseven/hello.clj`, and the Apex
`project.json` file should point at the function above:

```json
{
  "runtime": "clojure",
  "handler": "net.morethanseven.hello::handleRequest"
}
```

You would also need a small configuration file at `functions/hello/project.clj`:

```clojure
(defproject net.morethanseven "0.1.0-SNAPSHOT"
  :description "Hello World."
  :dependencies [[com.amazonaws/aws-lambda-java-core "1.1.0"]
                 [com.amazonaws/aws-lambda-java-events "1.1.0" :exclusions [
com.amazonaws/aws-java-sdk-s3
com.amazonaws/aws-java-sdk-sns
com.amazonaws/aws-java-sdk-cognitoidentity
com.amazonaws/aws-java-sdk-kinesis
com.amazonaws/aws-java-sdk-dynamodb]]
                 [org.clojure/clojure "1.8.0"]]
  :aot :all)
```

The above is really just showing an example of how little code a
function might contain, the specifics are relevant only if you're
intersted in Clojure. But imagine the same sort of thing for your
language of choice.


# Infrastructure

The interesting part (hopefully) of this blog post is the observation
that using AWS Lambda doesn't mean you don't need any infrastructure or
configuration. The good news is that, for the periodic job/cron usecase
this infrastructure is fairly standard between jobs.

Apex has [useful integration with Terraform](http://apex.run/#managing-infrastructure)
to help manage any required infrastructure too. We can run the following
two commands to provision and then manage our infrastructure.

```
apex infra init
apex infra deploy
```

Doing so requires us to write a little Terraform code. First we need
some variables, we'll invclude this in `infrastructure/variables.tf`:

```terraform
variable "aws_region" {
  description = "AWS Region Lambda function is deployed to"
}

variable "apex_environment" {
  description = "Apex configured environment. Auto provided by 'apex infra'"
}

variable "apex_function_role" {
  description = "Provisioned Lambda Role ARN via Apex. Auto provided by 'apex infra'"
}

variable "apex_function_hub" {
  description = "Provisioned function 'hub' ARN information. Auto provided by 'apex infra'"
}

variable "apex_function_hub_name" {
  description = "Provisioned function 'hub' name information. Auto provided by 'apex infra'"
}
```

And then we need to describe the resources for our cron job in
`infrastructure/main.tf`:

```terraform
resource "aws_cloudwatch_event_rule" "every_five_minutes" {
    name = "every-five-minutes"
    description = "Fires every five minutes"
    schedule_expression = "rate(5 minutes)"
}

resource "aws_cloudwatch_event_target" "check_hub_every_five_minutes" {
    rule = "${aws_cloudwatch_event_rule.every_five_minutes.name}"
    target_id = "${var.apex_function_hub_name}"
    arn = "${var.apex_function_hub}"
}

resource "aws_lambda_permission" "allow_cloudwatch_to_call_hub" {
    statement_id = "AllowExecutionFromCloudWatch"
    action = "lambda:InvokeFunction"
    function_name = "${var.apex_function_hub_name}"
    principal = "events.amazonaws.com"
    source_arn = "${aws_cloudwatch_event_rule.every_five_minutes.arn}"
}
```

Here we're running the job every 5 minutes, but it should be relatively
easy to see how you can change that frequency. See the Terraform and AWS
Lambda documentation for all the possible options.


## On complexity, user interface and abstractions

The above is undoutedly powerful, and nicely solves the described
probles with using Cron. However it's not all plain sailing I feel
with Serverless as a Cron replacement.

Let's talk about complexity. If I can make the assumptions that:

* The underlying host I was going to run my cron job on host is well managed,
  potentially by another team in my organisation
* Hosts don't suffer downtime, or if they do it's occasional and they
  are brough back up quickly

Then I can just use cron. And the interface to cron looks something more
like:

```
*/5 * * * * /home/garethr/run.sh
```

I still had to write my function (the Clojure code above) but I
collapsed the configuration of three distinct AWS resources and the use of a
new tool (Terraform) into a one-line crontab entry. You might have
provisioned that cron job using Puppet or Chef which adds a few lines
and a new tool, which sits somewhere between hand editing and the above
example.

This is really a question of user interface design and abstractions.
On one hand Serverless provides a nice high-level shared abstraction for
developers (the function). On another Serverless requires a great deal
of (virtual) infrastructure, which at the moment tends not to be abstracted
from the end-user. In the simple case above I had to care about
`aws_cloudwatch_event_targets`, `aws_cloudwatch_event_rules` and
`aws_lambda_permissions`. The use of those non-abstract resources all
couples my simple cron example to a specific platform (AWS) when the
simple function could likely run on any Serverless platform that
supports the JVM.


## Serverless, not Infrastructureless

I'm purposefully nit-picking with the above example. Serverless does
provide a more straightforward cron experience, mainly because it's
self-contained. But the user interface even for simple examples is still
very raw in many cases. Importantly, in removing the concept of ervers,
we don't appear to have removed the need to configure infrastructure,
which I think is what many people thing of when they hope to be rid of
servers in the first place.

