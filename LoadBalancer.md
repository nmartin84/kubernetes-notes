---
tags:
  - k8s/networking
---
Exposes Kubernetes app to the internet. Clients will connect to the LoadBalancer service, which handles directing traffic to appropriate pods.

Cloud Providers (Azure, AWS, GCP, etc) will auto-create the load balancer for you.

## Manifest File

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: app1
  ports:
    - port: 80
      targetPort: 80
  type: LoadBalancer
```

## Cloud Provider LoadBalancer Specifics

Something to make note of, or to keep in mind, is that some providers may provide different options for their LoadBalancer configurations.