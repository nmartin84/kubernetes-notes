---
tags:
  - k8s/clusters
---
A distributed key-value store that persists all cluster state data stored on the [Control Plane Node](control-plane-node.md), enabling recovery after node or cluster restarts. 

## Stacked ETCD Topology

In the stacked etcd topology, each [Control Plane Node](control-plane-node.md) runs its own etcd instance colocated with the [API Server](api-server.md), [Scheduler](scheduler.md), and [Controller Manager](controller-manager.md). This approach is common for [High Availability Cluster](high-availability-cluster.md).

- Multiple control plane nodes (typically three or more) provide redundancy and fault tolerance.
- Worker nodes communicate with the API servers through a load balancer.
- The close coupling of etcd and control plane components means that losing a node could affect both etcd and the control plane—so three or more nodes are recommended.
- By default, kubeadm creates and manages an etcd instance when adding a control plane node.