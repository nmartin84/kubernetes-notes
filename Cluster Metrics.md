---
tags:
  - k8s/observability
---
Kubernetes Cluster Metrics are the signals your control plane and workloads emit (CPU, memory, QPS, latency, etc.) that you collect and query to understand health, capacity, and performance—then act on (dashboards, HPAs, alerts).



## Key Ideas

- Kubernetes surfaces **resource metrics** (CPU/memory) via **metrics-server** for `kubectl top` and HPA’s CPU/Memory targets.
- **Custom metrics** (per-pod app metrics) and **external metrics** (cluster-external signals) require an adapter (e.g., **Prometheus Adapter**).
- Core plumbing: **kubelet/cAdvisor ➝ metrics-server (resource)** and **app /metrics ➝ Prometheus ➝ Adapter (custom/external)**.
- HPA v2 supports multiple metric sources; scale logic is the **most aggressive** of the configured metrics.
- Secure and label your metrics: scrape only what you need, restrict retention, and standardize labels (app, instance, version, zone).

> [!NOTE] Signal Types
> 
> - **Resource**: CPU/Memory of Pods/Nodes (from metrics-server).
>     
> - **Custom**: App-exposed metrics (Prometheus-style) aggregated per Pod/Namespace.
>     
> - **External**: Metrics not tied to Kubernetes objects (e.g., SQS depth, Kafka lag).
>     

> [!TIP] Minimal Pipeline  
> Start with **metrics-server** for `kubectl top` and basic HPA, then add **Prometheus + Prometheus Adapter** to unlock custom/external metrics and alerting.

> [!WARNING] Common Pitfalls
> - HPA CPU targets depend on **requests**. If requests are missing or unrealistic, scaling misbehaves.
> - Time skew or TLS/mTLS issues can break scraping; watch `APIService` status for metrics-server.
> - Unbounded Prometheus retention or high scrape cardinality = and slow queries.

## Installation

Option A ->
```sh
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```
This applies the official Deployment, RBAC, and Service in `kube-system`. There’s also an HA manifest if you want replicas >1.

> [!TIP] High availability  
> Use the HA manifest on Kubernetes v1.21+:
> ```sh
> kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/high-availability-1.21+.yaml
> ```
> Then set `replicas` > 1 if needed. [Metric Server](https://github.com/kubernetes-sigs/metrics-server)

Option B -> Helm (official chart)
```sh
helm repo add metrics-server https://kubernetes-sigs.github.io/metrics-server/
helm upgrade --install metrics-server metrics-server/metrics-server -n kube-system
```

Helm makes it easy to set flags/values (like preferred address types) via `--set` or a values file. [GitHub+1](https://github.com/kubernetes-sigs/metrics-server/blob/master/charts/metrics-server/README.md?utm_source=chatgpt.com)

> [!INFO] EKS add-on  
> On Amazon EKS you can also install Metrics Server as a managed add-on from the console (“Community add-ons”) or via CLI. [AWS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/metrics-server.html?utm_source=chatgpt.com)

## Manifest File

```yaml
# --- 1) Sample app exposing Prometheus metrics -------------------------------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
  labels: {app: api}
spec:
  replicas: 2
  selector: {matchLabels: {app: api}}
  template:
    metadata:
      labels: {app: api}
    spec:
      containers:
        - name: api
          image: ghcr.io/example/api:1.0
          ports:
            - containerPort: 8080
          env:
            - name: PORT
              value: "8080"
          # Exposes /metrics on :8080 for Prometheus to scrape
          # (e.g., via promhttp in Go, micrometer in Java, etc.)
          resources:
            requests: {cpu: "200m", memory: "256Mi"}
            limits:   {cpu: "1",    memory: "512Mi"}

---
# --- 2) Service for scraping -------------------------------------------------
apiVersion: v1
kind: Service
metadata:
  name: api
  labels: {app: api}
spec:
  selector: {app: api}
  ports:
    - name: http
      port: 80
      targetPort: 8080

---
# --- 3) (Prometheus Operator) ServiceMonitor to scrape /metrics --------------
# If you use Prometheus Operator. Otherwise, add this job to your prometheus.yml.
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: api
  labels: {release: kube-prometheus-stack}  # match your Prometheus selector
spec:
  selector:
    matchLabels: {app: api}
  endpoints:
    - port: http
      path: /metrics
      interval: 15s
      scrapeTimeout: 10s

---
# --- 4) Prometheus Adapter partial config mapping a query to a custom metric -
# This ConfigMap snippet shows how to expose an RPS metric as a custom.metric.
# Exact schema may vary by adapter version/helm chart values.
apiVersion: v1
kind: ConfigMap
metadata:
  name: prometheus-adapter
  namespace: monitoring
data:
  config.yaml: |
    rules:
      # Per-namespace / per-deployment request rate
      - seriesQuery: 'http_requests_total{job="api"}'
        resources:
          overrides:
            namespace: {resource: "namespace"}
            pod:       {resource: "pod"}
            # OR map to "deployment" if you use a deployment label
        name:
          matches: "^(.*)_total"
          as: "${1}_per_second"
        metricsQuery: |
          sum(rate(http_requests_total{<<.LabelMatchers>>}[2m])) by (<<.GroupBy>>)

---
# --- 5) HPA using both CPU and a custom metric (RPS) -------------------------
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api
  minReplicas: 2
  maxReplicas: 20
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 60
    - type: Pods
      pods:
        # Target average 20 requests/sec per pod (from adapter's custom metric)
        metric:
          name: http_requests_per_second
        target:
          type: AverageValue
          averageValue: "20"
  behavior:
    scaleUp:
      stabilizationWindowSeconds: 0
      selectPolicy: Max
      policies:
        - type: Percent
          value: 100
          periodSeconds: 60
        - type: Pods
          value: 4
          periodSeconds: 60
    scaleDown:
      stabilizationWindowSeconds: 300
      selectPolicy: Max
      policies:
        - type: Percent
          value: 50
          periodSeconds: 60
```

> [!TIP] Quick Win  
> If you aren’t ready for custom metrics, drop the adapter sections and keep CPU-based HPA. Add custom metrics later without changing your app.
## Operational Snippets

```sh
# --- Verify resource metrics pipeline (metrics-server) -----------------------
kubectl top nodes
kubectl top pods -A
kubectl get apiservice v1beta1.metrics.k8s.io -o yaml | kubectl-neat
kubectl -n kube-system logs deploy/metrics-server --tail=200

# Raw resource metrics endpoints (debug):
kubectl get --raw "/apis/metrics.k8s.io/v1beta1/nodes" | jq .
kubectl get --raw "/apis/metrics.k8s.io/v1beta1/pods"  | jq .

# --- Verify custom/external metrics via adapter ------------------------------
kubectl api-resources | grep -E 'custom.metrics|external.metrics'
# List available custom metrics names:
kubectl get --raw "/apis/custom.metrics.k8s.io/v1beta2" | jq .
# Query a specific metric for a workload:
kubectl get --raw \
  "/apis/custom.metrics.k8s.io/v1beta2/namespaces/default/pods/*/http_requests_per_second" | jq .

# --- Prometheus health & cardinality checks ---------------------------------
kubectl -n monitoring get pods -l app.kubernetes.io/name=prometheus
kubectl -n monitoring port-forward svc/prometheus-k8s 9090 &
# In PromQL, watch heavy series:
#   topk(20, count by (__name__)({__name__=~".+"}))
#   sum by (job) (scrape_samples_scraped)

# --- HPA status & decisions --------------------------------------------------
kubectl describe hpa api-hpa
kubectl get hpa api-hpa -w
```

> [!INFO] Sizing & Retention  
> Keep scrape intervals and retention aligned to your SLOs and budgets. Many teams start at 15s/7d, then tune based on query load and storage.

> [!TIP] Label Hygiene  
> Standardize labels like `app`, `team`, `env`, `version`, `zone` so metrics roll up cleanly and alerts route to the right owners.
