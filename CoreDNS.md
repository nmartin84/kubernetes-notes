---
tags:
  - k8s/networking
  - k8s/name-resolution
---
**CoreDNS** is the **default DNS server** used by Kubernetes to provide **service discovery and name resolution** inside the cluster.

Think of it as the cluster’s phone book: it lets Pods find each other by name instead of IP address.

## Why DNS Matters in Kubernetes

- Pods get **ephemeral IPs** that can change when rescheduled.
- Services provide a **stable DNS name** (`my-service.my-namespace.svc.cluster.local`).
- CoreDNS answers DNS queries so Pods can talk to each other reliably.

## What CoreDNS Does

1. **Resolves service names → cluster IPs**
    - Example: Pod A connects to `my-service.dev.svc.cluster.local`
    - CoreDNS returns the **ClusterIP** of that Service.
2. **Handles Pod DNS lookups**
    - Each Pod gets `/etc/resolv.conf` configured to query CoreDNS.
3. **Can forward external queries**
    - If a Pod looks up `google.com`, CoreDNS forwards the query to upstream DNS (like kubelet’s or your cloud provider’s).
4. **Custom DNS rules** (via Corefile plugin system)
    - Stub domains, rewrite rules, caching, etc.

## Where CoreDNS Runs

- Runs as a **Deployment** in the `kube-system` namespace.
- Usually has 2+ replicas (for HA).
- Exposed inside the cluster via a Service named `kube-dns` (for historical compatibility).
- Check by running -> `kubectl -n kube-system get pods -l k8s-app=kube-dns`

## CoreDNS Config (Corefile)

The config is stored in a ConfigMap (`coredns`) in the `kube-system` namespace.

```pgsql
.:53 {
    errors
    health
    kubernetes cluster.local in-addr.arpa ip6.arpa {
       pods insecure
       fallthrough in-addr.arpa ip6.arpa
    }
    prometheus :9153
    forward . /etc/resolv.conf
    cache 30
    loop
    reload
    loadbalance
}
```