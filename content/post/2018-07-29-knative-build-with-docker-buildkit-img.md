+++
date = "2018-07-29T10:13:00Z"
title = "Knative Build with Docker, BuildKit and Img"
url = "/2018/07/29/knative-build-with-docker-buildkit-img/"
+++


[Knative Build](https://github.com/knative/build) is one of the components of [Knative](https://cloud.google.com/knative/) that shipped last week.
Knative is described as a set of _"essential base primitives"_, one of those is an approach to describing build pipelines to run on Kubernetes.

[Installing](https://github.com/knative/docs/blob/master/build/installing-build-component.md) `knative/build` is relatively simple as long as you have
a Kubernetes cluster running at least 1.10. Handily Docker Desktop for [Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows) or
[Mac](https://store.docker.com/editions/community/docker-ce-desktop-mac) makes it easy to get a local cluster up and running.

```
kubectl apply -f https://storage.googleapis.com/knative-releases/build/latest/release.yaml
```

With Knative Build installed we can describe our build pipelines using the new `Build` and `BuildTemplate` objects.
The [documentation](https://github.com/knative/docs/blob/master/build/builds.md#examples) covers a few basic examples and
the [tests folder](https://github.com/knative/build/tree/master/test) has more. The project is early, so browsing through these
has been the best way I've found of discovering how to do various things. In this post I thought I'd try a few examples, making use
of the latest build functionality in Docker 18.06 and using BuildKit via [Img](https://github.com/genuinetools/img).


## Knative Build and Docker Build

First lets describe a `BuildTemplate` for BuildKit. Templates can be reused for different builds by defining parameters that need to be set by the `Build`.
Templates aren't strictly required (see the basic examples mentioned above) but feel like a useful tool in most cases. The following template uses 
the latest version of the Docker client image. We enable the experimental BuildKit builder, which isn't stricktly required but has some nifty new features.
We also bind this to the local Docker socket, so you need to be running Docker 18.06 on the server too. Again, if you're using Docker Desktop locally then you're all set.
Save the following as `buildkit.yaml`.

```yaml
apiVersion: build.knative.dev/v1alpha1
kind: BuildTemplate
metadata:
  name: buildkit
spec:
  parameters:
  - name: IMAGE
    description: Where to publish the resulting image.
  steps:
  - name: build
    image: docker:18.06
    env:
    - name: DOCKER_BUILDKIT
      value: "1"
    args: ["build", "-t", "${IMAGE}", "."]
    volumeMounts:
    - name: docker-socket
      mountPath: /var/run/docker.sock
  volumes:
  - name: docker-socket
    hostPath:
      path: /var/run/docker.sock
      type: Socket
```

Let's add that template to the cluster:

```
kubectl apply -f buildkit.yaml
```

Then we describe our build. For this I'm using [kubeval](https://github.com/garethr/kubeval) but you can use source repository which provides
a Dockerfile to build from. Save the following as `kubeval-buildkit.yaml`:


```yaml
apiVersion: build.knative.dev/v1alpha1
kind: Build
metadata:
  name: kubeval-buildkit
spec:
  source:
    git:
      url: https://github.com/garethr/kubeval.git
      revision: master
  template:
    name: buildkit
    arguments:
    - name: IMAGE
      value: garethr/kubeval
```

Finally trigger the build by submitting the `Build` object to the Kubernetes API.

```
kubectl apply -f kubeval-buildkit.yaml
```

We can watch the build by watching the object:

```
kubectl get build kubeval-buildkit -o yaml -w
```

For the actual build logs we need to go a little deeper. From the above output you'll see the name of the pod. Use that in the following command
(just swap out the `gztzq` part) to get the build logs from Docker:

```
kubectl -n default logs -f kubeval-buildkit-gztzq -c build-step-build
```


## Knative Build and Img

The above example uses the hosts Docker daemon, but if you want to run the build without you're in luck. [Img](https://github.com/genuinetools/img)
is a "standalone, daemon-less, unprivileged Dockerfile and OCI compatible container image builder." It's using the same build engine, BuildKit, as
we used above with Docker 18.06. In this example we'll need to push the resulting image to a remote repository, as it won't show up in the local engine
when you run `docker images`. For that Knative Build provides some handy [auth options](https://github.com/knative/docs/blob/master/build/auth.md).
Let's create a `Secret` and an asscoiated `ServiceAccount`. The username and password want to be changed to your authentication credentials for Docker Hub.
Save this as `serviceaccount.yaml`:

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: docker-hub
  annotations:
    build.knative.dev/docker-0: https://index.docker.io/v1/
type: kubernetes.io/basic-auth
stringData:
  username: <your username>
  password: <your password>
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: build-bot
secrets:
- name: docker-hub
```

We then define a new `BuildTemplate` for Img. Note this one has two steps rather than just one. Save the following as `img.yaml`:

```yaml
apiVersion: build.knative.dev/v1alpha1
kind: BuildTemplate
metadata:
  name: img
spec:
  parameters:
  - name: IMAGE
    description: Where to publish the resulting image.
  steps:
  - name: build
    image: garethr/img
    args: ["build", "-t", "${IMAGE}", "."]
    securityContext:
      privileged: true
  - name: push
    image: garethr/img
    args: ["push", "${IMAGE}", "-d"]
    securityContext:
      privileged: true
```

With those defined we can submit them to Kubernetes:

```
kubectl apply -f serviceaccount.yaml -f img.yaml
```

Finally we describe out build. The only changes to the above are the addition of the `serviceAccountName` property (so the build will have access to the Docker Hub credentials)
and swapping the `buildkit` template out for the `img` one we just defined. If you want this to work for you then you'll need to swap out the image name to a repository
you have permissions to push to, rather than `garethr/kubeval`.

```yaml
apiVersion: build.knative.dev/v1alpha1
 kind: Build
 metadata:
   name: kubeval-img
 spec:
   serviceAccountName: build-bot
   source:
     git:
       url: https://github.com/garethr/kubeval.git
       revision: master
   template:
     name: img
     arguments:
     - name: IMAGE
       value: garethr/kubeval
```

Save the above as `kubeval-img.yaml` and finally submit the build:

```
kubectl apply -f kubeval-img.yaml
```

As before we can watch the build by watching the relevant object:

```
kubectl get build kubeval-img -o yaml -w
```

And again to get at the logs we'll rhe name of the pod. Use that in the following command
(just swap out the `gztzq` part) to get the build logs from Img:

```
kubectl -n default logs -f kubeval-img-gztzq -c build-step-build
```

There are a few things to note with the above:

* The `img` images run as priviledged. I tried and failed to set the relevant capabilities instead but I'm sure that's fixable
  with a little more debugging
* I rebuilt the `img` Docker image and pushed this to my repo. This image runs as root, due to a conflict with permissions and
  the user in the original image. Again, I'm sure that's also resolvable with a bit more spelunking.


## Conclusions

The above demonstrates how to use Knative Build from what I've been able to pick up from the documentation and examples. This definitely 
feels like a component for folks to build higher-level interfaces on. It solves some useful problems but requires a fair understanding
of Kubernetes objects to utilise and interact with today. I could certainly see the potential however for some more user-fiendly tools on top of
this in the future.

