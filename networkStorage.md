---
tags:
  - k8s/storage
  - k8s/networking
---
Volume type that stores data on shared network storage (not just on a single node’s disk).

## Why Network Storage Matters

- A **hostPath** or local disk volume ties your data to **one node**.
- If that node dies or a Pod is rescheduled, the data may be lost.
- **Network storage** solves this by providing data over the network (NFS, iSCSI, cloud block/file storage, CSI drivers), so multiple nodes can attach to it and Pods can be rescheduled safely.

## Common Types of Network Storage in Kubernetes

- **iSCSI**
- **GlusterFS**
- **CephFS / RBD**
- **CSI drivers** (Container Storage Interface) — the modern, pluggable way to integrate storage systems.
- **Cloud storage** (which is network-attached under the hood):
    - AWS EBS
    - GCP Persistent Disk
    - Azure Disk/File
    - etc.

## NFS Storage

```yaml
volumes:
  - name: nfs-volume
    nfs:
      server: 10.0.0.50
      path: /exported/path
```

## Example Manifest File

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 20Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: 10.0.0.50
    path: /exported/path
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 10Gi
```

Pods then mount the PVC instead of directly declaring NFS:
```yaml
volumes:
  - name: data
    persistentVolumeClaim:
      claimName: pvc-nfs
```