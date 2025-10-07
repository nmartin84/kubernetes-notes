---
tags:
  - k8s/networking
  - k8s/routing
---
Kubernetes Ingress is an API object that manages external access to services within a Kubernetes cluster, typically HTTP and HTTPS traffic. It provides a way to define routing rules for external connections, directing them to the appropriate backend services.

> [!TIP] Requires Ingress Controller Installation
> Component of the cluster that's auto started. Various ingress controllers can be installed via the kubernetes.io documentation.

> [!INFO] Features
> - Load Balancing
> - SSL Termination
> - Name Based Virtual Hosting

## Manifest File

```yaml
apiVersion: v1
kind: Deployment
metadata:
  name: aks-helloworld-two
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-two
  template:
    metadata:
      labels:
        app: aks-helloworld-two
    spec:
      containers:
      - name: aks-helloworld-two
        image: image:name
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
```

## Configuring Ingress Resource

Install an ingress resource from kubernetes.io ([Ingress Controllers | Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/)). The below example will install an nginx controller.

```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.13.1/deploy/static/provider/cloud/deploy.yaml
```

## Managing Ingress Resources

```sh
kubectl get service ingress-nginx-controller
```

## Ingress Catch All Re-Route

Use a simple catch-all to reroute all traffic to a specific service when one is not provided:
```yaml
- path: /(.*)
  pathType: Prefix
  backend:
    service:
      name: aks-helloworld-one
      port:
        number: 80
```

