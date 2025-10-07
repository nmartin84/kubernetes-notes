---
tags:
  - k8s/scaling
  - k8s/availability
---
The Horizontal Pod Autoscaler automatically adjusts the number of pods in a deployment, replica set, or Replication Controller based on observed CPU utilization or other selected metrics.

- HPA fetches metrics from a series of aggregated APIs like metrics.k8s.io, custom.metrics.k8s.io, and external.metrics.k8s.io.
- It adjusts the number of replicas automatically to maintain the target metric value (like CPU usage), defined in the HPA configuration.

*An HPA configuration includes the definition of the minimum and maximum number of pods, the target type (e.g., CPU utilization), and the target value for that metric.*

## Example Manifest File

Separate Object:
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: web-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: web
  minReplicas: 2
  maxReplicas: 10
  metrics:
    # Scale when average CPU utilization across pods > 60%
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 60
    # Also scale when average memory across pods > 500Mi
    - type: Resource
      resource:
        name: memory
        target:
          type: AverageValue
          averageValue: 500Mi
  behavior:
    # Faster scale-up; choose the most aggressive of these policies
    scaleUp:
      stabilizationWindowSeconds: 0
      selectPolicy: Max
      policies:
        - type: Percent
          value: 100     # at most double per minute
          periodSeconds: 60
        - type: Pods
          value: 4       # or add up to 4 pods per minute
          periodSeconds: 60
    # More conservative scale-down to avoid flapping
    scaleDown:
      stabilizationWindowSeconds: 300
      selectPolicy: Max
      policies:
        - type: Percent
          value: 100
          periodSeconds: 60
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
  labels:
    app: web
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: web
          image: nginx:1.25
          ports:
            - containerPort: 80
          resources:
            # Requests/limits help the HPA make good decisions for CPU-based scaling
            requests:
              cpu: 100m
              memory: 256Mi
            limits:
              cpu: 500m
              memory: 512Mi
```