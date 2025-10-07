---
tags:
  - k8s/networking
  - k8s/nodes
  - k8s/containers
feature: 339c2671-9456-4392-8692-1b4c77813422--CNI-1.png
thumbnail: thumbnails/resized/ec06975b5d941a5d521ba185340baf34_b89e22fb.jpg
---
A specification and set of libraries for building plugins that configure network interfaces in Linux [Containers](Containers.md). In Kubernetes, CNI is responsible for establishing network connectivity between Pods in the cluster.

To enable networking between Pods, you must install a CNI plugin on your [Control Plane Node](Control Plane Node.md). Various plugins are available, each supporting different networking features and policies. 

  - The CNI is a specification and set of plugins that provide network connectivity to containers.
  - It is responsible for assigning IP addresses to pods, configuring network interfaces, and implementing network policies.
  - Examples of popular CNI plugins include Calico, Flannel, Cilium, and Amazon VPC CNI.

![[339c2671-9456-4392-8692-1b4c77813422--CNI-1.png]]