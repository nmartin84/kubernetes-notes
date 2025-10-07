---
tags:
  - k8s/networking
---
A Kubernetes ClusterIP Service is the default type of Service in Kubernetes, designed for internal communication within a cluster. It provides a stable, virtual IP address that is only reachable from within the cluster’s virtual network.

## Manifest File

```yaml
apiVersion: v1
kind: Service
metadata:
  name: Backend
spec:
  type: ClusterIP # or NodePort or LoadBalancer
  ports:
  - targetPort: 80 # port exposed
    port: 80 # port in the pod
selector:
  app: app1
  type: front-end-app
```