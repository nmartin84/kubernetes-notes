---
tags:
  - k8s/availability
  - k8s/clusters
  - k8s/scaling
---
Single control plane clusters are easy to install but have a critical weakness: if the control plane node fails, worker nodes cannot communicate with the scheduler, ReplicaSets can't create new Pods, and the cluster becomes inaccessible externally (e.g., via `kubectl`).

High-availability (HA) clusters solve this by having multiple control plane nodes, providing redundancy and scalability. In an HA setup, if one control plane node fails, others can take over, ensuring continued operation.

## kubectl commands

```sh
kubectl get hpa

# manually scaling
kubectl scale deployment deploymentName --replicas=4

# set auto-scale by cpu usage
kubectl autoscale deploymentName nginx --cpu-percent=50 --min=1 --max=5
```