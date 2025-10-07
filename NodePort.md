---
tags:
  - k8s/networking
---
Will use the Node’s IP address to make it externally accessible to outside traffic. Port value ranges will be ==30000 -> 32767==, which is either defined manually or automatically by the node.

## Manifest File

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: hello-world
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```

## Retrieving the assigned NodePort IP Address

You can use `kubectl describe pod podname | more` and look for the line `Node: ` to find the IP address. The other possible option is you can use `kubectl describe service servicename | more`

```sh
kubectl describe pod <podname> | more
# Node:    aks-agentpool-18462097-vmss000003/10.224.0.199 <- look for this line

kubectl describe service <servicename> | more
```

For testing you can reference [Using wget from command line](using-wget-from-command-line.md)