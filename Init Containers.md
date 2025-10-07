---
tags:
  - k8s/pods
  - k8s/containers
---
Are [Containers](containers.md) that'll spin up before the app container [Pod](pod.md) to help with initializing and setting up the environment for the main pods. These can have a restart policy to allow failed init containers to be tried again, and once they are done, they'll spin down.

When a pod is spun up with an init container, the status field will show `Init: 0/1` until that init container deploys successfully. 

## Manifest File

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-init1
spec:
  initContainers:
    - name: make-dir
      image: alpine
      command: ['sh', '-c', 'sleep 3 && echo "creating directory" && mkdir -p /opt/log']
      volumeMounts:
        - name: data
          mountPath: /opt
  containers:
    - name: app1
      image: alpine
      command: ['sh', '-c', 'echo "app1 is now running" && sleep infinity']
      volumeMounts
        - name: data
          mountPath: /opt
  volumes:
    - name: data
      emptyDir: {}
```