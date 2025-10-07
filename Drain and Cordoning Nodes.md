---
tags:
  - k8s/updates
  - k8s/maintenance
---
Draining will remove all pods out of a node to prep a node for some task, say maintenance or some other task. Draining can be leveraged to release those pods from the node and cordoning can be leveraged to ensure pods are not reassigned to this node.

**Draining Node:** This will also cordone the node (ignore daemonsets will allow daemonsets to continue running)
```sh
kubectl drain nodename --ignore-daemonsets --force
```

**Cordoning Node:**
```sh
kubectl cordone nodename # cordones off a node
kubectl uncordone nodename # uncordones a node
```