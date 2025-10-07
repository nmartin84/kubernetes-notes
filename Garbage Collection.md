---
tags:
  - k8s/garbage
  - k8s/clusters
  - k8s/objects
---
Garbage collection is a collective term for the various mechanisms Kubernetes uses to clean up cluster resources. 

**This allows the clean up of resources like the following:**

- Terminated Pods
- Completed Jobs
- Objects without owner references
- Unused Containers and Container Images
- Dynamically Provisioned Persistent Volumes with a Reclaim Policy of Delete
- Stale or Expired Certificate Signing Requests (CSR)
- Nodes deleted in the following conditions:
	- On a cloud when the cluster uses a cloud controller manager
	- On-premises when the cluster uses an addon similar to a cloud controller manager
- Node Lease Objects

## kubectl commands

```sh
kubectl delete deployment nginx-deployment --cascade=foreground
```