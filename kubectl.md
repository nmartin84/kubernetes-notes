---
tags:
  - k8s/cli
  - k8s/clusters
---
`kubectl` is the primary command-line tool for managing Kubernetes clusters. Mastering its usage is essential, especially for certification exams focused solely on `kubectl` operations.

**Basic Usage Pattern:** The general syntax for a `kubectl` command is:
```sh
kubectl [command] [TYPE] [NAME] [flags]
```

| Type    | Description                                                                                                               |
| ------- | ------------------------------------------------------------------------------------------------------------------------- |
| command | The operation to perform (e.g., `create`, `get`, `describe`, `delete`).                                                   |
| type    | The resource type (e.g., `service` or the short form `svc`).                                                              |
| name    | The user-defined name of the resource (from `metadata.name` in YAML). **Note**: this is not the same as the internal UID. |
| flags   | Optional command-line flags for extra configuration (e.g., `--port` to expose a container port).                          |
- Resource names must be unique within their type and namespace. Practice with various commands is crucial for proficiency.


## Examples

```sh
# export json path
kubectl get csr user1 -o jsonpath='{.status.certificate}' | base64 -d > user1.crt

# edit deployed object
kubectl edit pod nginx-pod1

# deploy object or manifest file
kubectl apply -f my_file.yaml
```