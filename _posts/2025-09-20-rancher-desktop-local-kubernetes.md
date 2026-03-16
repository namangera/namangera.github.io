---
layout: post
title: "Rancher Desktop for local Kubernetes"
date: 2025-09-20
category: Programming
---

Rancher Desktop is a free alternative to Docker Desktop that bundles container management and a local Kubernetes cluster. I use it when I need to test something that runs in Kubernetes without spinning up a cloud cluster.

You get to choose your container runtime: containerd or dockerd. If you pick dockerd, the `docker` CLI just works. If you pick containerd, you use `nerdctl`, which has the same interface.

## Why bother with local Kubernetes

Most of what I build doesn't need Kubernetes. But occasionally I want to test a deployment config, or run a multi-service setup with proper service discovery, and doing that locally is faster than pushing to a staging environment and waiting.

Rancher Desktop gives you a single-node k3s cluster. k3s is a lightweight Kubernetes distribution, good enough to test real configs against.

```bash
# check the cluster is running
kubectl get nodes

# deploy something
kubectl apply -f deployment.yaml

# check it
kubectl get pods
```

## For GIS work specifically

I've used it to run a local instance of GeoServer in Kubernetes, testing a Helm chart before deploying it elsewhere. Spinning up GeoServer normally involves a fair amount of setup. In a container it's one command. In Kubernetes with Rancher Desktop it's reproducible and easy to tear down.

## The downside

It uses more memory than I'd like. On a machine with 16GB of RAM it's fine. On 8GB it competes noticeably with everything else. You can adjust the VM memory allocation in the settings if needed.
