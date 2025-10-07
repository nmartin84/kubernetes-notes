---
tags:
  - k8s/cli
  - k8s/clusters
---
`kubeadm` is a low-level CLI tool used for bootstrapping and managing Kubernetes clusters. It handles the creation and management of the cluster itself but does **not** provision the underlying infrastructure (like VMs or bare metal nodes). Typically, `kubeadm` is preinstalled in environments where you’ll need it, such as for certification exams.

High-level cluster setup and management tasks—like initializing the control plane, joining worker nodes, or upgrading the cluster—are commonly performed with `kubeadm`. Always refer to the official documentation for detailed, step-by-step instructions.

