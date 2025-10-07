---
tags:
  - k8s/networking
---
A Kubernetes `Service` of type `ExternalName` allows for mapping a service name within the Kubernetes cluster to an external DNS name. This enables applications running inside the cluster to access external services (e.g., a database, an API endpoint) using a familiar Kubernetes service name, without needing to know the external service's full DNS name or IP address.