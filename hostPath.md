---
tags:
  - k8s/storage
---
A **hostPath** in Kubernetes is a type of **Volume** that mounts a **file or directory from the node’s filesystem** directly into a Pod. It’s one of the simplest volume types, but also one of the riskiest if misused.

> [!DANGER] Risks
> - **Not portable** → ties Pod to specific node path.
> - **Security risks** → container gets direct access to the host filesystem (can be dangerous).
> - **No persistence guarantee** → if Pod is rescheduled to another node, data won’t follow.
> - Generally **not recommended for production**, except for node-specific workloads (like storage drivers, monitoring agents).

## How It Works

- When you define a Pod with a `hostPath` volume:
    - You specify a path on the **node’s filesystem** (e.g., `/var/log/app`).
    - Kubernetes mounts that path into the container at a location you choose.

## Manifest File

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-using-hostpath
spec:
  containers:
    - name: app
      image: busybox
      command: ["sh", "-c", "echo 'hello from pod' >> /data/hostpath.txt && sleep 3600"]
      volumeMounts:
        - name: host-storage
          mountPath: /data
  volumes:
    - name: host-storage
      hostPath:
        path: /mnt/data        # Path on the node
        type: DirectoryOrCreate
```
- `/mnt/data` on the node is mounted into the container at `/data`.
- Whatever is written in `/data` inside the Pod shows up on the node.

## Supported `type` values

You can control how Kubernetes expects the path to behave:

- `""` (empty) → no checks, just mount.
- `DirectoryOrCreate` → create dir if missing.
- `Directory` → must already exist and be a directory.
- `FileOrCreate` → create file if missing.
- `File` → must already exist and be a file.
- `Socket`, `CharDevice`, `BlockDevice` → enforce special file types.