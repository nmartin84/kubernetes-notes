---
tags:
  - k8s/pods
  - k8s/containers
---
Similar to [Init Containers](init-containers.md) where they'll spin up before the main app pod containers, except they'll stay up and running while the pods are running.

An example sidecar container could be one focused on **logging**. 