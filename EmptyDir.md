---
tags:
  - k8s/storage
---
An empty volume that exists in either RAM or the `kubelet` base directory. This directory is shared by all containers running on that pod. Each containers can have a different mount path.

## Example Manifest File

```yaml
volumes:
  - name: cache
    emptyDir: {}
```