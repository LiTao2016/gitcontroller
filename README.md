# gitcontroller

gitcontroller watches [Kubernetes Deployments](http://kubernetes.io/docs/user-guide/deployments/) which use one or more [`gitRepo` volumes](http://kubernetes.io/docs/user-guide/volumes/#gitrepo) and then watches for changes in the associated git repository and branch.

When there are changes in git the `gitcontroller` will perform a rolling upgrade of the [Kubernetes Deployments](http://kubernetes.io/docs/user-guide/deployments/) to use the new configuration git repository revision; or rollback. The rolling upgrade policy (e.g. speed and number of concurrent pods which update and so forth) is all specified by your [rolling update configuration in the Deployment specification](http://kubernetes.io/docs/user-guide/deployments/#rolling-update-deployment).

Here is an [example of how to add a `gitRepo` volume to your application](https://github.com/jstrachan/springboot-config-demo/blob/master/src/main/fabric8/deployment.yml#L5-L14); in this case a spring boot application to load the [`application.properties`](https://github.com/jstrachan/sample-springboot-config/blob/master/application.properties) file from a git repository.

You can either run `gitcontroller` as a microservice in your namespace or you can use the `gitcontroller` binary at any time or as part of your [CI / CD Pipeline](http://fabric8.io/guide/cdelivery.html) process.

**Note** we recommend using separate git based configuration repository only for things which truly are environment specific. Its simpler to include all other configuration data with your microservice source code and then use a more regular [CI / CD Pipeline](http://fabric8.io/guide/cdelivery.html) from a single git repository to build your code, create the configuration files and package it all into an immutable docker image.

An alternative approach to using a git repository for environmental configuration and `gitcontroller` is to use ConfigMap such as the [ConfigMap PropertySource for spring](https://github.com/fabric8io/spring-cloud-kubernetes#configmap-propertysource). On the plus side `ConfigMap` is supported natively inside Kubernetes; though the downside is ConfigMap has no versioning or history; so once a ConfigMap is changed you've lost track of who changed what and when which makes it harder to track changes.

So if history or tracking changes in your configuration is important we recommend `git` over `ConfigMap`.

## Using gitcontroller as a command

To use `gitcontroller` as a command, such as in a CI / CD pipeline [download a binary of gitcontroller](https://github.com/fabric8io/gitcontroller/releases) then use the following command:

```sh
gitcontroller check

```

This will check all [Deployments](http://kubernetes.io/docs/user-guide/deployments/) in the current namespace for  [`gitRepo` volumes](http://kubernetes.io/docs/user-guide/volumes/#gitrepo) and perform rolling upgrades if they have changed.

You can specify a label selector expression via the `--selector` command line option which will filter the deployments that are checked.


## Using gitcontroller as a microservice

The `gitcontroller` microservice is now included in the [fabric8 release](http://fabric8.io/) so you can install and run gitcontroller via the [fabric8 developer console](http://fabric8.io/guide/console.html) via the `Run...` button on the `Runtime` view or the [gofabric8 installer](https://github.com/fabric8io/gofabric8) via:

```
gofabric8 deploy -y --app=gitcontroller --domain=vagrant.f8
```

You can also run the `gitcontroller` microservice locally on your machine via the following (adding a `--selector` selector to filter the deployments watched if you prefer)

```
gitcontroller run
```

## Development

### Prerequisites

Install [go version 1.5.1](https://golang.org/doc/install)

### Building

```sh
git clone git@github.com:fabric8io/gitcontroller.git $GOPATH/src/github.com/fabric8io/gitcontroller
./make
```

Make changes to *.go files, rerun `make` and run the generated binary..

e.g.

```sh
./build/gitcontroller help

```
