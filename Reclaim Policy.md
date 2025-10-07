---
tags:
  - k8s/storage
  - k8s/pods
---
Specified what should happen to the data when the pod or container is spun down. The options are **Retain, Recycle, Delete**.

- **Retain**: Retains data
- **Recycle**: Wipes out the data on the volume and makes it available
- **Delete**: Default state. Deletes data after a pod is destroyed. 