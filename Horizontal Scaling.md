---
tags:
  - k8s/scaling
  - k8s/availability
---
Horizontal scaling in Kubernetes involves increasing or decreasing the number of POD replicas of an application to adjust to the load without changing any of the Pod’s resources.

## Mechanism

This is typically managed by the [Horizontal Pod Autoscaler](^cbfad32c-2334-41eb-8ae2-eaa342cc5828), which automatically scales the number of Pods in a **Replication Controller**, deployment, replica set, or stateful set based on observed CPU utilization or other select metrics provided by custom metrics APIs.