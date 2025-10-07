---
tags:
  - k8s/workloads
  - k8s/pods
---
A Kubernetes Job is a resource that creates one or more Pods and ensures that a specified number of them successfully terminate. Unlike Deployments or ReplicaSets, which manage long-running, continuously available applications, Jobs are designed for tasks that run to completion and then stop, such as batch processing, calculations, or data backups.