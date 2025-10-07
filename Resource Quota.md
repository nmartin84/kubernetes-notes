---
tags:
  - k8s/quotas
---
 A Resource Quota in Kubernetes is a way to limit how many resources a namespace can consume.
 
 > [!NOTE] Limited to Namespaces
> - Quotas are applied **per namespace**.
> - Whenever you create or update a resource (Pod, PVC, etc.), Kubernetes checks the namespace’s quota.
> - If the quota would be exceeded, the request is rejected.

## What Resource Quota Can Control

1. **Compute resources**
    - `requests.cpu`, `requests.memory`
    - `limits.cpu`, `limits.memory`  
        (ensures fair CPU & RAM usage)
2. **Object counts**
    - Number of Pods, Services, ConfigMaps, PersistentVolumeClaims, etc.
3. **Storage resources**
    - Total storage requested across PVCs (`requests.storage`)
    - Storage by StorageClass
4. **Custom resources**
    - You can also set quotas on custom resources if they’re extended in the cluster.

## Managing Resource Quotas

```sh
kubectl get resourcequota
```

## Manifest File

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-team-quota
  namespace: dev-team
spec:
  hard:
    requests.cpu: "4"              # max total CPU requested
    requests.memory: 8Gi           # max total memory requested
    limits.cpu: "8"                # max total CPU limits
    limits.memory: 16Gi            # max total memory limits
    pods: "20"                     # max number of Pods
    persistentvolumeclaims: "5"    # max number of PVCs
    requests.storage: 100Gi        # total PVC storage
```
- All Pods in `dev-team` namespace combined can request up to **4 CPU, 8Gi memory**, and set limits up to **8 CPU, 16Gi memory**.
- They can only run **20 Pods total**.
- They can only claim **5 PVCs** and use at most **100Gi storage**.

## Limit Storage Quota Space

This will limit the size per PVC, not the entirety of space being utilized in the namespace:

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: storageLimits-defaultnamespace
spec:
  limits:
  - type: PersistentVolumeClaim
    max:
      storage: 2Gi
    min:
      storage: 1Gi
```
