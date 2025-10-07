---
tags:
  - k8s/objects
  - k8s/env
---
These are `key:value` pairs to pass non-secret data into pods.
## kubectl commands

```sh
# get config maps
kubectl get configmap
```

## Manifest Files

Config Map
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app1db1
data:
  database_host: "192.168.1.1"
  database_port: "8080"
  debug_mode: "1"
  log_level: "verbose"
```
> [!NOTE] Config Map Manifest File
> - `metadata: -> name:` is how this map will be targeted via the selector
> - `data: -> database_host:` is an example of a key that you'll need to pass to your app deployment.

Define Config Map in [Pod](pod.md) Manifest File:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox-configmap-pod
spec:
  containers:
  - name: busybox-container
    image: busybox:0.1
    command: ['sh', '-c', 'while true; do date; sleep 3; done']
    env:
    - name: dbhost
      valueFrom:
        configMapKeyRef:
          name: app1db1
          key: database_host
```
> [!NOTE] Deploy Manifest File
> - The config map is added under the `env:` section for `spec:`
> - Also, `name:` is the configmap name and `key:` is the key name defined in the mapping file.
