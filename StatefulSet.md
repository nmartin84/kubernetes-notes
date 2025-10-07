---
tags:
  - k8s/workloads
---
A Kubernetes StatefulSet is a workload API object used to manage stateful applications. Unlike Deployments, which are designed for stateless applications and treat Pods as interchangeable, StatefulSets provide stable, unique network identifiers and persistent storage for each Pod they manage. This makes them suitable for applications requiring stable hostnames, ordered deployment and scaling, and persistent data storage, such as databases (e.g., MySQL, PostgreSQL), distributed systems, and message queues.