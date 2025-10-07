---
tags:
  - k8s/clusters
  - k8s/monitoring
  - k8s/scheduler
---
The scheduler watches for [Pod](Pod.md) that have no assigned [Node](Node.md) (i.e., newly created Pods). It selects a suitable node in the cluster where each Pod should run, based on resource requirements, policies, and constraints. Once the scheduler makes its decision, it updates the Pod's specification to indicate which node should run the Pod. 

## When Scheduler Implements Changes

- When a new pod is created
- When a pod is deleted
- When a node becomes available
- When resource requirements change
- When scheduling policies are updated

## How Scheduler Implements Changes

- Resource needs: CPU, memory, etc.
- Node availability: Is the node healthy, does it have enough resources, etc.
- Constraints and policies: Labels, affinities/anti-affinities, taints, tolerations, etc.