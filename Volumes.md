---
tags:
  - k8s/storage
---
In Kubernetes, a **Volume** is a way to give containers in a Pod **persistent or shared storage**.

By default, anything a container writes inside its filesystem disappears when:
- the container restarts,
- or the Pod is deleted.

Volumes solve this by providing **storage that outlives containers** and can even be shared between them.

> [!INFO] Volume Types
> - [EmptyDir](emptydir.md)
> - [hostPath](hostpath.md)
> - [ConfigMaps](configmaps.md) / [Secrets](secrets.md)
> - [Persistent Volume Claim](persistent-volume-claim.md)
> - [networkStorage](networkstorage.md)

## Key Points About Volumes

- **Defined at the Pod level** → available to all containers in that Pod.
- **Mounted inside containers** → each container chooses where to mount it (`mountPath`).
- **Many volume types** exist, from simple temporary storage to persistent cloud disks.

## Example Manifest File

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-volume
spec:
  containers:
    - name: app
      image: busybox
      command: ["sh", "-c", "echo Hello > /data/hello.txt && sleep 3600"]
      volumeMounts:
        - name: data
          mountPath: /data
  volumes:
    - name: data
      emptyDir: {}
```
