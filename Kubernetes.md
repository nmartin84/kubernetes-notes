An open-source container orchestration platform. It automates the deployment, management, scaling, and networking of containerized applications. Essentially, it helps manage and coordinate the infrastructure needed to run applications reliably and efficiently.

```
Kubernetes Cluster
├── Namespaces
│   ├── Pods
│   │   ├── Containers
│   │   │   ├── Images
│   │   │   └── Volumes (mounted inside containers)
│   │   └── Init Containers
│   ├── Workloads
│   │   ├── ReplicaSet
│   │   │   └── Pods
│   │   ├── Deployment
│   │   │   └── ReplicaSets
│   │   │       └── Pods
│   │   ├── StatefulSet
│   │   │   └── Pods (with persistent identity & storage)
│   │   ├── DaemonSet
│   │   │   └── Pods (one per node)
│   │   └── Job / CronJob
│   │       └── Pods (short-lived)
│   ├── Services
│   │   ├── ClusterIP
│   │   ├── NodePort
│   │   ├── LoadBalancer
│   │   └── ExternalName
│   ├── Networking
│   │   ├── Ingress
│   │   └── NetworkPolicies
│   ├── Config & Storage
│   │   ├── ConfigMaps
│   │   ├── Secrets
│   │   ├── PersistentVolumes (PV)
│   │   ├── PersistentVolumeClaims (PVC)
│   │   └── StorageClasses
│   ├── Security
│   │   ├── ServiceAccounts
│   │   ├── Roles / ClusterRoles
│   │   └── RoleBindings / ClusterRoleBindings
│   └── Other
│       ├── HorizontalPodAutoscaler (HPA)
│       ├── PodDisruptionBudget (PDB)
│       └── ResourceQuotas & LimitRanges
└── Cluster-level Components
    ├── Nodes
    │   ├── Kubelet
    │   ├── kube-proxy
    │   └── Pods scheduled here
    ├── Control Plane
    │   ├── API Server
    │   ├── etcd (data store)
    │   ├── Controller Manager
    │   └── Scheduler
    └── CRDs (Custom Resource Definitions)
        └── Custom Controllers / Operators

```