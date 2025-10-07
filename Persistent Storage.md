---
tags:
  - k8s/storage
---
Containers provide only temporary filesystems, so any data written inside a container is lost when it restarts. For applications that need to retain data—such as those interacting with a database—persistent storage is required. Kubernetes allows you to mount external storage to your application workloads, ensuring data persists beyond the lifecycle of individual containers. 