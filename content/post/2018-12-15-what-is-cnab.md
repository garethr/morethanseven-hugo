+++
date = "2018-12-15T09:25:00Z"
title = "What is CNAB?"
url = "/2018/12/15/what-is-cnab/"
+++

We announced [Cloud Native Application Bundles (CNAB)](https://cnab.io) at DockerCon EU and Microsoft Connect just last week, and I spent a bunch of time talking to folks and anwsering questions about it at KubeCon too. As I’m heading home I thought it would be good to write down a few thoughts about what CNAB _is_. As I see it CNAB is two things:

* Formalisation of a pattern we’ve seen for multi-toolchain installers
* Commoditization of the packaging part of the infrastructure-as-code stack

Let’s take each of these in turn.

## Formalisation of a pattern we’ve seen for multi-toolchain installers

The explosion of APIs for infrastructure services has led to a corresponding explosion of tools to help you manage and orchestrate them. From API clients for individual projects (countless Kubernetes configuration management tools) to cloud-provider specific tools (CloudFormation templates, ARM templates) to high-level DSLs (Ansible, Terraform). Throw in the [configuration complexity clock](http://mikehadlow.blogspot.com/2012/05/configuration-complexity-clock.html) (so we have Pulumi in Typescript, Terraform with it’s own HCL DSL, too much YAML everywhere) and we have a lot of tool options. I posit this choice is a good thing, and that we’re never going to see everyone agree on one approach or another. One pattern that has emerged is packaging API clients as Docker images. [lachlanevenson/k8s-kubectl](https://hub.docker.com/r/lachlanevenson/k8s-kubectl) has seen 5 million pulls, there are CloudFormation images on Hub that have millions of pulls, the official Terraform image has more than 10 million pulls. There are also lots of projects like [terraform-ansible-docker](https://github.com/jtbonhomme/terraform-ansible-docker) which use multiple tools at the same time. CNAB is a formalisation of these patterns.

## Commoditization of the packaging part of the infrastructure-as-code stack

There is a now familiar pattern when it comes to infrastructure-as-code tools, namely the introduction of a package format and some form of registry. Puppet and the Forge. Chef and the Supermarket. Ansible and Ansible Galaxy. Terraform and the Module Registry. Everyone basically creates a tar file with some metadata. Not all projects have done this, but most would probably benefit from doing so. Compose files, ARM templates, CloudFormation templates; all are shared predominantly as source artefacts which are copy-and-pasted around. 

CNAB aims to commoditize this part of the stack and provide tools for anyone implementing something similar. It’s predominantly undifferentiated heavy lifting. No-one is competing based on the package metadata format so maybe lets build some agreement, some shared tooling and move on.


## What has this got to do with Kubernetes?

One point that came up in a few conversations around CNAB was what this has to do with Kubernetes? Typically this came from people so deep into Kubernetes that all they can see is the Kubernetes API. If that’s you then you probably don’t need CNAB, especially at this early stage. You probably think in one of two ways when it comes to application of configuration:

* You like the package metaphor and you’re a big fan of Helm
* You don’t like the package metaphor and you use some other tool to access the Kubernetes API more directly

Back to our two original points. When it comes to a multi-toolchain installer, in the case of Kubernetes-only, you might not have multiple toolchains so this point is moot. Everything in your world is just the Kubernetes API.

When it comes to the second point around a standard for packaging, Helm already serves this need within the tight scope of Kubernetes. I fully expect to see (or hey, to build) a Helm plugin which will allow for wrapping a Helm chart in CNAB. There are a few advantages to CNAB support for Helm; a single artefact could include the desired Helm chart and it’s dependent charts and it could include all of the images in use within those charts. That single artefact can be signed and stored in a registry of your choise. But that’s mainly a case of incremental benefit to most Helm users. It’s also theorectical, CNAB is just a specifcation, so concrete things like a Helm plugin still need to be actually built.

What I would say to those that view everything through a Kubernetes lens is that not everyone does. CNAB is mainly for a messier world of multiple toolchains and multiple APIs. 

## Why you might care?

Depending on your role in the pantheon of software development you may care about one or both of the original points. You may care about them directly (especially if you’re a software vendor) or indirectly (because of what they allow us to collectively build for end-users.)

The story is pretty straightforward if you’re a software vendor. It’s very likely you’re shipping software to customers using multiple toolchains. Either you’re dealing with the complexity of asking customers to install particular tools and providing content for each (say asking users to first run some Terraform code and then run something else) or you’re struggling to get a large number of different teams to use the same tool and cram everything into it. Lots of vendors also have something that looks like a storefront for mixed content, or multiple stores for individual tools. Everyone is getting bored of supporting all the different formats, so a standard here appeals.

From the end user point of view the story is more about future potential. This is analogous to other standards. You might not have read or implemented anything against the OCI image or distribution spec, but you benefit from the compatability between all the different registries and runtimes. If you’ve built a multi-toolchain installer then you at least appreciate one of the problems CNAB aims to solve. But then you already have a point solution and the CNAB tooling is early here. You only benefit from commoditization of the format indirectly, and only if it’s actually adopted by multiple tools. But assuming it is widely adopted, in the future when a nifty new infrastructure as code tool comes along (say like Pulumi recently, or Terraform previously) then you’ll aleady know how to install applications defined using it if it implements the CNAB specifcation. You’ll already have CI/CD toolchains that accomodate CNAB. You’ll already have CNAB-compatible tools for storing and distributing bundles. The typical barriers to entry for yet another tool are reduced.


## The importance of agreement

There is a risk with any effort like this, one that aims to solve a general problem at a low level, that the actual user need gets lost. This is one of those solutions which impacts tool builders directly, and end users somewhat indirectly. That means you have to be a certain type of developer to benefit from looking at CNAB today. But if you look at [Docker App](https://blog.docker.com/2018/12/docker-app-and-cnab/) you’ll find a much more opinionated tool which just happens to implement CNAB under the hood, without exposing it to end users. The end user benefits there (sharing Compose-based applications on Docker Hub, a single artefact with the application definition and associated images) are much more concrete.

CNAB isn’t about one tool to rule them alI. It’s about building agreement between people and projects solving the same set of problems. That agreement means more time to focus on end users problems rather than undifferentiated heavy lifting and more compatibility between tools. I expect the future to look like a range of completely separate tools for specific formats and toolchains which just happen to be compatible because they implement CNAB. We can then build a whole new set of tools that work across all of these packages, from graphical installers to documentation generation to whatever else we can think of. If that sounds interesting join the conversation around this [new specifaction](https://github.com/deislabs/cnab-spec) and lets see if it just might work.

