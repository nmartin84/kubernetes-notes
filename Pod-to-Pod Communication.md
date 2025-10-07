---
tags:
  - k8s/networking
  - k8s/pods
---
[Containers](Containers.md) in a [Pod](Pod.md) share an IP Address and port space, and can find each other via `localhost`. They can also communicate using standard inter-process communications like SystemV semaphores or POSIX shared memory.

## Container IP's are Ephemeral by Nature

Meaning that all pods will get assigned a temporary, or random IP address when the pod spins up, or is destroyed and re-created, so relying on a pod IP address will never work due to the nature of how pods work.

## Node auto manages IP assignments to Pods

`kubectl describe node` will give you the CIDR range configured on the node used to assign to pods on that node. This will require the [Container Network Interface (CNI)](Container Network Interface (CNI).md) plugin.