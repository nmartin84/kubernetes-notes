---
tags:
  - k8s/service
  - k8s/networking
---
Kubernetes Services provide a stable network endpoint for a logical set of Pods, enabling reliable communication within the cluster and external access to applications. Pods are ephemeral and their IP addresses change frequently, making direct communication unreliable. Services abstract away these changes, offering a consistent way to interact with your applications.


> [!NOTE] Available Services
> - [NodePort](NodePort.md)
> - [ClusterIP](ClusterIP.md)
> - [LoadBalancer](LoadBalancer.md)
> - [Ingress](Ingress.md)


## Managing Deployed Services

```sh
kubectl get service <service-name>
kubectl get service <service-name> -o yaml
```
