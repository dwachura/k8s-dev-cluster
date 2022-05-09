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

## Apps available

### [NGINX ingress controller](https://kubernetes.github.io/ingress-nginx/)

Ingress controller based on NGINX server.

**Exposing cluster to external world**

To be able to access applications from outside of the cluster via ingresses, one may expose ports (by default, 80 for http and 443 for https) of the ingress controller service, for example using kubectl port forwarding feature:
```
kubectl port-forward -n nginx-ingress service/nginx-ingress-controller <host machine port>:<ingress controller service port>
```
One may use `debclusterctl ports-fwd` as a shortcut to the above one (which forwards both http and https cluster ports to the random local ports).

### Docker registry

Cluster-local docker registry.

**Pushing docker images**

Registry is available through NGINX ingress under `docker-registry.${K8S_DEV_CLUSTER_NAME}.localdev.me` host, on port `80`.

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

### [Keycloak](https://www.keycloak.org/)

**Login into admin console**

Keycloak is available through NGINX ingress under `keycloak.${K8S_DEV_CLUSTER_NAME}.localdev.me` host, on port `80`.

Administrator credentials are `admin:admin`.
