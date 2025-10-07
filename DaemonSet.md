---
tags:
  - k8s/workloads
---
A Kubernetes DaemonSet ensures that a copy of a specific Pod runs on every Node in a Kubernetes cluster, or on a subset of Nodes that match a defined selector. This is distinct from Deployments, which aim to maintain a specified number of Pod replicas distributed across the cluster, but not necessarily one per Node.