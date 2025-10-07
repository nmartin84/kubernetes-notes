---
tags:
  - k8s/replicas
---
In Kubernetes, a pod replica refers to a copy of a [Pod](Pod.md) that runs simultaneously to ensure high availability, scalability, and redundancy of applications. Replicas are managed by controllers such as Deployments, ReplicaSets, or StatefulSets.

- **High Availability:** Replicas ensure that if one or more pods fail, the application remains accessible. This is crucial for maintaining service continuity in production environments.
- **Load Distribution:** Multiple replicas of a pod can share the load of incoming network traffic, improving the application's responsiveness and handling capacity.

## Management of Replicas

**ReplicaSets:** 
The most common way to manage replicas. A ReplicaSet ensures that a specified number of POD Replicas are always running. It is configured with a desired count of replicas and a pod template which it uses to create and manage pods.

**Deployments:** 
Use ReplicaSets under the hood but add features like declarative updates, rollback, and versioning, which make them suitable for managing the lifecycle of pods and their replicas.

**StatefulSets:** 
Similar to Deployments but for applications that require persistent storage and a stable identity for each replica.

## Scaling

- Pods can be manually scaled by changing the number of replicas in the deployment configuration.
- Automatic scaling can be achieved using the [Horizontal Pod Autoscaler](Horizontal Pod Autoscaler.md), which adjusts the number of replicas based on real-time metrics like CPU usage or custom metrics.