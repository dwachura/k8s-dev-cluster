# K8S development cluster

## Overview

The goal of this project is to provide Kubernetes cluster for local development and testing.

The single node cluster created consists of cluster-local docker registry and NGINX ingress pre-configured.

## Requirements

  * docker (>=20.10.8)
  * kubectl (>=1.23)
  * kind (>=0.11.1)
  * helmsman (>=3.8.0)

## Usage

There is `devclusterctl` script prepared that works as a simple CLI tool to provide easy
and intuitive (hopefully) way to manage the cluster. It's recommended to add it to operating system's PATH variable.

Before the usage, K8S_DEV_CLUSTER_NAME environment variable must be set that specifies the name of the cluster (created by _kind_) that the `devclusterctl` will manage.

### Cluster basic management

  * creation  
    `devclusterctl init`
  * update
    `devclusterctl update`
  * removal 
    `devclusterctl delete`

### Cluster local registry

There is cluster-local docker registry created into the cluster. It's available through NGINX ingress under `docker-registry.${K8S_DEV_CLUSTER_NAME}.localdev.me` host, on port `80 `.

To be able to access it from outside of the cluster (to push images for example) ingress needs to be exposed, for example using kubectl port forwarding feature:
```
kubectl port-forward -n ingress-nginx service/ingress-nginx-controller <host machine port>:80
```

**Pushing docker images**

Then, one may push images to the docker registry on the cluster like (assuming cluster name was set to "dev-cluster"):
```
docker tag <source image> docker-registry.dev-cluster.localdev.me:<host forwarded port>/<target image>
docker push docker-registry.dev-cluster.localdev.me:<host forwarded port>/<target image>
```

**Using docker images in the cluster**

After docker image was pushed into the cluster's docker-registry, it may be used into k8s components. Docker registry is configured to be available in cluster under `docker-registry.cluster.local` host, so the reference to an image from cluster's registry should look like:
```
docker-registry.cluster.local/<image name>:<image tag>
```

