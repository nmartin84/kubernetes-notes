---
tags:
  - k8s/monitoring
  - k8s/availability
---
Self-healing is a key feature of Kubernetes ( [High Availability Cluster](High Availability Cluster.md) ) that ensures deployed applications are always running and healthy.

Kubernetes continuously monitors the state of Pods and other deployment objects. If a Pod fails due to hardware failure or any other reason, Kubernetes automatically restarts the failed Pods, replaces them, reschedules them, and even kills Pods that do not respond to user-defined health checks.

## Configuring Self-Healing Application

1. **Controllers maintain desired replicas**
    - Use a **Deployment/StatefulSet/DaemonSet** so the control plane reschedules pods if a node/pod dies.
2. **Health probes drive restarts & traffic gating**
    - **livenessProbe** → restarts a stuck container.
    - **readinessProbe** → removes an unready pod from Service endpoints.
    - **startupProbe** → gives slow apps time to boot before liveness kicks in.
3. **Resource limits prevent OOM thrash**
    - Set realistic **requests/limits** so the scheduler places pods correctly and you avoid OOM kills.
4. **Availability during failures**
    - **PodDisruptionBudget (PDB)** to keep a minimum of pods up.
    - Spread replicas across nodes/zones to avoid single-point failures.

## Manifest File Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
  namespace: prod
spec:
  replicas: 3
  revisionHistoryLimit: 3
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
      terminationGracePeriodSeconds: 30
      containers:
        - name: app
          image: ghcr.io/example/web:1.2.3
          ports:
            - containerPort: 8080
          resources:
            requests:
              cpu: "200m"
              memory: "256Mi"
            limits:
              cpu: "500m"
              memory: "512Mi"
          startupProbe:
            httpGet: { path: /healthz/startup, port: 8080 }
            failureThreshold: 30
            periodSeconds: 2
          readinessProbe:
            httpGet: { path: /healthz/ready, port: 8080 }
            initialDelaySeconds: 5
            periodSeconds: 5
            timeoutSeconds: 1
            failureThreshold: 3
          livenessProbe:
            httpGet: { path: /healthz/live, port: 8080 }
            initialDelaySeconds: 15
            periodSeconds: 10
            timeoutSeconds: 2
            failureThreshold: 3
          lifecycle:
            preStop:
              httpGet: { path: /shutdown, port: 8080 } # drain gracefully
```
- If the process hangs → **liveness** restarts it.
- If it’s booting or not ready → **readiness** keeps traffic away.
- If a node dies → **Deployment** recreates the pod elsewhere.
- Rolling releases keep at least **N-1** pods available.

## Keep Minimum Pods Running

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: web-pdb
  namespace: prod
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: web
```
- Prevents voluntary disruptions (node drains, upgrades) from taking you below 2 running pods.

## Spread replicas to avoid correlated failures

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
  namespace: prod
spec:
  replicas: 3
  selector:
    matchLabels: { app: web }
  template:
    metadata:
      labels: { app: web }
    spec:
      topologySpreadConstraints:
        - maxSkew: 1
          topologyKey: kubernetes.io/hostname   # spread across nodes
          whenUnsatisfiable: ScheduleAnyway
          labelSelector:
            matchLabels: { app: web }
      # Anti-affinity alternative:
      # affinity:
      #   podAntiAffinity:
      #     preferredDuringSchedulingIgnoredDuringExecution:
      #       - weight: 100
      #         podAffinityTerm:
      #           topologyKey: kubernetes.io/hostname
      #           labelSelector:
      #             matchLabels: { app: web }
      containers: [ ... as above ... ]
```
