---
tags:
  - k8s/updates
---
A **rolling update** in Kubernetes is the **default update strategy** for Deployments and StatefulSets. It lets you update your application **gradually**, replacing old Pods with new ones **without downtime**.

### How it works

- You update the Deployment (e.g., change the container image version).
- Kubernetes gradually terminates old Pods and starts new ones, respecting:
    - **maxUnavailable** → how many Pods can be down at once.
    - **maxSurge** → how many extra Pods (above desired replicas) can be started temporarily.

## Example Manifest File

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 4
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: nginx
          image: nginx:1.27   # ← updating this triggers a rolling update
```
- At most **1 Pod** will be unavailable during the update.
- At most **5 Pods total** (4 desired + 1 surge) may run temporarily.
- Clients keep getting service as Pods are rotated.