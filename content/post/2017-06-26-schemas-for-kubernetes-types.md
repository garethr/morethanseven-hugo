+++
date = "2017-06-26T13:56:00Z"
title = "Schemas for Kubernetes types"
url = "/2017/06/26/schemas-for-kubernetes-types/"
+++


I've been playing around building a few Kubernetes developer tools at
the moment and a few of those led me to the question; how do I validate
this Kubernetes resource definition? This simple question led me through
a bunch of GitHub issues without resolution, conversations with folks
who wanted something similar, the OpenAPI specification and finally to
what I hope is a nice resolution.

If you're just after the schemas and don't care for the details just
head on over to the following GitHub repositories.

* [garethr/kubernetes-json-schema](https://github.com/garethr/kubernetes-json-schema)
* [garethr/openshift-json-schema](https://github.com/garethr/openshift-json-schema)

OpenShift gets a separate repository as it has an independent version
scheme and adds a number of additional types into the mix.


## But why?

It's worth asking the question *why* before delving too far into the
how. Let's go back to the problem; I have a bunch of Kubernetes resource
definitions, lets say in YAML, and I want to know if they are valid?

Now you might be thinking I could just run them with `kubectl`? This
raises a few issues which I don't care for in a developer tool:

* It requires `kubectl` to be installed and configured
* It requires `kubectl` to be pointed at a working Kubernetes cluster

Here are a few knock-on effects of the above issues:

* Running what should be a simple validate step in a CI system now
  requires a Kubernetes cluster
* Why do I have to shell out to something and parse it's output to validate
  a document, for instance if I'm doing it in the context of a unit test?
* If I want to validate the definition against multiple versions of
  Kubernetes I need multiple Kubernetes clusters

Hopefully at this point it's clear why the above doesn't work. I don't
want to have to run a boat load of Kubernetes infrastructure to validate
the structure of a text file. Why can't I just have a schema in a
standard format with widespread library support?


## From OpenAPI to JSON Schema

Under-the-hood Kubernetes is all about types. Pods,
ReplicationControllers, Deployments, etc. It's these primatives that
give Kubernetes it's power and shape. These are described in the
Kubernetes source code and are used to generate an OpenAPI description of
the Kubernetes HTTP API. I've been spelunking here before with some work on
[generating Puppet types from this same specification](https://github.com/garethr/garethr-kubernetes).

The latest version of OpenAPI in fact already contains the type
information we seek, encoded in a superset of [JSON
Schema](http://json-schema.org) in the `definitions` key. This is
used by the various tools which generate clients from that definition.
For instance the official python client doesn't know about these types
directly, it all comes from the [OpenAPI description of the
API](https://github.com/kubernetes-incubator/client-python/blob/master/scripts/update-client.sh). But how
do we use those `definitions` separately for our own nefarious
validation purposes? Here's a quick sample of what we see in the [50,000
line-long OpenAPI definition file](https://raw.githubusercontent.com/kubernetes/kubernetes/master/api/openapi-spec/swagger.json)

```json
- definitions: {
    io.k8s.api.admissionregistration.v1alpha1.AdmissionHookClientConfig: {
      description: "AdmissionHookClientConfig contains the information to make a TLS connection with the webhook",
      required: [
        "service",
        "caBundle"
      ],
      properties: {
        caBundle: {
          description: "CABundle is a PEM encoded CA bundle which will be used to validate webhook's server certificate. Required",
          type: "string",
          format: "byte"
        },
        service: {
          description: "Service is a reference to the service for this webhook. If there is only one port open for the service, that port will be used. If there are multiple ports open, port 443 will be used if it is open, otherwise it is an error. Required",
          $ref: "#/definitions/io.k8s.api.admissionregistration.v1alpha1.ServiceReference"
        }
      }
    },
```


The discussion around wanting JSON Schemas for Kubernetes types has
cropped up in a few places before, there are some useful comments on [this
issue](https://github.com/kubernetes/kubernetes/issues/14987) for
instance. I didn't find a comprehensive solution however, so set out on
a journey to build one.

## OpenAPI2JsonSchema

The tooling I've build for this purpose is called
[openapi2jsonschema](https://github.com/garethr/openapi2jsonschema).
It's not Kubernetes specific and should work with other OpenAPI
specificied APIs too, although as yet I've done only a little testing of
that. Usage of `openapi2jsonschema` is fairly straightforward, just
point it at the URL for an OpenAPI definition and watch it generate a
whole bunch of files.

```
openapi2jsonschema https://raw.githubusercontent.com/kubernetes/kubernetes/master/api/openapi-spec/swagger.json
```

`openapi2jsonschema` can generate different flavours of output, useful
for slightly different purposes. You probably only need to care about
this if you're generating you're own schemas or you want to work
completely offline.

* default - URL referenced based on the specified GitHub repository
* standalone - de-referenced schemas, more useful as standalone documents
* local - relative references, useful to avoid the network dependency

The [build script](https://github.com/garethr/kubernetes-json-schema/blob/master/build.sh)
for the Kubernetes schemas is a simple way of seeing this in practice.


## Published Schemas

Using the above tooling I'm publishing Schemas for Kubernetes, and for
OpenShift, which can be used directly from GitHub.

* [garethr/kubernetes-json-schema](https://github.com/garethr/kubernetes-json-schema)
* [garethr/openshift-json-schema](https://github.com/garethr/openshift-json-schema)

As an example of what these look like, here are the links to the latest `deployment` schemas for
1.6.1:

* [v1.6.1/deployment.json](https://github.com/garethr/kubernetes-json-schema/tree/master/v1.6.1/deployment.json)
* [v1.6.1-standalone/deployment.json](https://github.com/garethr/kubernetes-json-schema/tree/master/v1.6.1-standalone/deployment.json)
* [v1.6.1-local/deployment.json](https://github.com/garethr/kubernetes-json-schema/tree/master/v1.6.1-local/deployment.json)


## A simple example

There are lots of use cases for these schemas, although they are primarily
useful as a low-level part of other developer workflow tools. But at a most
basic level you can validate a Kubernetes config file.

Here is a quick example using the Python [jsonschema
client](https://github.com/Julian/jsonschema) and an invalid deployment
file:

```bash
$ jsonschema -F "{error.message}" -i hello-nginx.json
1.5.1-standalone/deployment.json
u'template' is a required property
```


## What to do with all those schema?

As noted these schemas have lots of potential uses for development
tools. Here are a few ideas, some of which I've already been hacking on:

* Demonstrating using with the more common YAML serialisation
* Testing tools to show your Kubernetes configuration files are valid,
  and against which versions of Kubernetes
* Migration tools to check your config files are still valid against
  master or beta releases
* Integration with code editors, for instance via something like [Schema
  Store](http://schemastore.org/json/)
* Validation of Kubernetes configs generated by higher-level tools, like
  Helm, Ksonnet or Puppet
* Visual tools for crafting Kubernetes configurations
* Tools to show changes between Kubernetes versions

If you do use these schemas for anything please let me know, and I'll
try and keep them updated with releases of Kubernetes and OpenShift. I
plan on polishing the `openapi2jsonschema` tool when I get some time,
and I'd love to know if anyone uses that with other OpenAPI compatible
APIs. And if all you want to do is validate your Kubernetes
configuration and don't care too much about what's happening under the
hood then stick around for the next blog post.
