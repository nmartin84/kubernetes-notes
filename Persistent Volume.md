---
tags:
  - k8s/storage
---
A Persistent Volume (PV) is a Persistent Volume Resources in [Clusters](Clusters.md) that has been provisioned by an administrator or dynamically provisioned using a [Storage Class](Storage Class.md). It is a piece of storage in the cluster that has been provisioned independently of the usage of individual [Pod](Pod.md).

- A **cluster-wide resource** that represents a piece of storage already provisioned and made available to the cluster.
- PVs are volume plugins like Volumes but have a lifecycle independent of any individual Pod that uses the PV.
- A PV is a cluster-wide resource, similar to a node that is a cluster resource.
- PVs can have various underlying storage backends, such as NFS, iSCSI, or cloud-specific storage APIs.
- It’s not tied to a single node unless the backing storage is _node-local_ (like `hostPath` or `local` volumes).
	- Example: An NFS or cloud volume can be mounted from any node.
	- A `hostPath` PV only works on the node where that path exists.

## Manifest File

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-hostpath-10gi
spec:
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: local-hostpath
  hostPath:
    path: /mnt/k8s/pv1   # must exist on the node
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-hostpath-5gi
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: local-hostpath
  resources:
    requests:
      storage: 5Gi        # <= PV capacity
```