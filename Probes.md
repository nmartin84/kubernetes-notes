---
tags:
  - k8s/availability
---
# Probes in Kubernetes

In Kubernetes, **probes** are mechanisms used by the kubelet to determine the health and status of containers running inside pods. They help ensure that workloads are functioning correctly and enable Kubernetes to take corrective action when something goes wrong.

---

## Types of Probes

> [!info] Liveness Probe
> - Checks if a container is **still running**.
> - If the liveness probe fails, Kubernetes **restarts the container**.
> - Example use case: detect if an application is stuck in a deadlock.

> [!info] Readiness Probe
> - Checks if a container is **ready to serve requests**.
> - If it fails, Kubernetes **removes the pod from Service endpoints**, meaning it won’t receive traffic.
> - Example use case: delay traffic until app initialization is complete.

> [!info] Startup Probe
> - Checks if the application inside a container has **successfully started**.
> - Useful for **slow-starting applications** where normal liveness/readiness probes might fail prematurely.
> - Prevents unnecessary restarts during startup.

---

## Probe Mechanisms

Kubernetes supports different mechanisms to perform probe checks:

- **HTTP Probe**  
	Sends an HTTP request to a container and expects a successful status code (2xx–3xx).
- **TCP Probe**  
	Attempts to open a TCP connection on a specified port. Success = healthy.
- **Exec Probe**  
	Runs a specified command inside the container. Exit code `0` = healthy.

---

## Example: Pod with Probes

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: probe-example
spec:
  containers:
    - name: myapp
      image: myapp:latest
      livenessProbe:
        httpGet:
          path: /healthz
          port: 8080
        initialDelaySeconds: 5
        periodSeconds: 10
      readinessProbe:
        tcpSocket:
          port: 8080
        initialDelaySeconds: 5
        periodSeconds: 10
      startupProbe:
        exec:
          command: ["cat", "/tmp/healthy"]
        failureThreshold: 30
        periodSeconds: 10
```

## Why Probes Matter

> [!tip]
> 
> - **Self-healing:** Kubernetes can automatically restart unhealthy containers.
> 		
> - **Traffic safety:** Ensures only healthy pods receive requests.
> 		
> - **Flexibility:** You can tune probe timing and thresholds based on workload behavior.
> 		

---

## Key Takeaways

- **Liveness** → Is the app alive? If not, restart.
- **Readiness** → Is the app ready to serve? If not, remove from traffic.
- **Startup** → Has the app fully started? Prevents premature failures.