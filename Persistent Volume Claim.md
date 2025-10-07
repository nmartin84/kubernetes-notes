---
tags:
  - k8s/storage
---
A Persistent Volume Claim (PVC) is a request for storage by a user. It is similar to a Pod in that Pods consume node resources, while PVCs consume Persistent Volume Resources.
- PVCs specify size, and access modes (such as read-write or read-only).
- A PVC, once created, is automatically bound to an available PV in the cluster that meets its requirements, if available.
- If no suitable PV exists, the PVC remains unbound indefinitely until a suitable PV is created.

## Dynamic Provisioning

When a PVC is created, a corresponding PV can be dynamically provisioned through a [Storage Class](storage-class.md), depending on the configuration. This decouples the task of configuring underlying storage from the usage of storage in Pods.

## Usage

PVCs allow a user to abstract the details of the storage being requested. Developers only need to be aware of the claim to storage, without needing to be concerned about the actual physical storage specifics.

## Limiting PVC Storage Usage

Can be defined by specifying the `LimitRange` for the PVC.

## Setting default PVC Setting

A default PVC setting cannot be define but you can set a default [Storage Class](storage-class.md) so PVC's without a storage class get auto-assigned one.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: standard
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
    # (optional backwards-compat)
    storageclass.beta.kubernetes.io/is-default-class: "true"
provisioner: kubernetes.io/no-provisioner   # <- example; use your CSI provisioner
volumeBindingMode: WaitForFirstConsumer
reclaimPolicy: Delete
allowVolumeExpansion: true
parameters:
  # provisioner-specific params here
---
apiVersion: v1
kind: PersistentVolumeClaim # no storage class is defined for this PVC
metadata:
  name: my-data
spec:
  accessModes: [ReadWriteOnce]
  resources:
    requests:
      storage: 10Gi
```
