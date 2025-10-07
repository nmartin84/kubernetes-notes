---
tags:
  - k8s/clusters
---
A Kubernetes cluster is a collection of interconnected machines, known as nodes, that work together to run and manage containerized applications. It provides a platform for automating the deployment, scaling, and management of these applications. 

| Minimum Requirements | Description   |
| -------------------- | ------------- |
| Memory               | 2GB for Nodes |

## Config Files

[Clusters](clusters.md) config files can be located in the `/etc/kubernetes` directory on the worker plane node. This will include the default config for `kubectl` but also include other things like the [Scheduler](scheduler.md) config file.