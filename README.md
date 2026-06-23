# GitOps Platform Using ArgoCD on Amazon EKS

## Overview

This project demonstrates the implementation of a GitOps deployment platform using ArgoCD and Amazon EKS. The application deployment process is fully automated through Git commits, enabling continuous synchronization between the Git repository and the Kubernetes cluster.

## Architecture

```text
Git Commit
    ↓
GitHub Repository
    ↓
ArgoCD
    ↓
Amazon EKS
    ↓
Application Deployment
```

## Repository Structure

```text
gitops/
├── dev
│   ├── deployment.yaml
│   └── service.yaml
├── qa
│   ├── deployment.yaml
│   └── service.yaml
└── prod
    ├── deployment.yaml
    └── service.yaml
```

## Technologies Used

* Amazon EKS
* Kubernetes
* ArgoCD
* GitHub
* NGINX
* AWS EC2
* eksctl
* kubectl

## Features Implemented

### Auto Sync

ArgoCD continuously monitors the Git repository and automatically deploys any new changes to the EKS cluster.

### Self Heal

If a Kubernetes resource is modified or deleted manually, ArgoCD automatically restores it to the desired state defined in Git.

### Pruning

Resources removed from the Git repository are automatically removed from the Kubernetes cluster.

## Setup Steps

### 1. Create Amazon EKS Cluster

```bash
eksctl create cluster \
--name gitops-cluster \
--region us-east-1 \
--nodegroup-name workers \
--node-type t3.small \
--nodes 2
```

### 2. Install ArgoCD

```bash
kubectl create namespace argocd

kubectl apply -n argocd \
-f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 3. Expose ArgoCD

```bash
kubectl patch svc argocd-server \
-n argocd \
-p '{"spec":{"type":"LoadBalancer"}}'
```

### 4. Create ArgoCD Application

```bash
kubectl apply -f argocd-app.yaml
```

## ArgoCD Application Configuration

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: dev-app
  namespace: argocd
spec:
  project: default

  source:
    repoURL: https://github.com/<your-username>/gitops-platform.git
    targetRevision: HEAD
    path: gitops/dev

  destination:
    server: https://kubernetes.default.svc
    namespace: default

  syncPolicy:
    automated:
      prune: true
      selfHeal: true

    syncOptions:
      - CreateNamespace=true
```

## Demonstration

### Auto Sync Demonstration

1. Update the deployment manifest.
2. Change replicas from 1 to 2.
3. Commit and push the changes to GitHub.
4. ArgoCD automatically detects the change.
5. The EKS cluster is updated without manual intervention.

### Self Heal Demonstration

1. Identify a running NGINX pod.
2. Delete the pod manually.

```bash
kubectl delete pod <pod-name>
```

3. ArgoCD detects drift from the desired state.
4. Kubernetes automatically recreates the pod.

### Pruning Demonstration

1. Remove `service.yaml` from the repository.
2. Commit and push the changes.
3. ArgoCD synchronizes the cluster.
4. The service resource is automatically removed from EKS.

## Verification Commands

```bash
kubectl get applications -n argocd
kubectl get pods
kubectl get svc
```

## Demo Video

Watch the complete project demonstration:

https://drive.google.com/file/d/1LiX4GY1wpUylbDQQWbDXcTSTmdH3ZSMt/view?usp=sharing

## Learning Outcomes

* Understanding GitOps principles
* Deploying applications using ArgoCD
* Managing Kubernetes applications through Git
* Implementing Auto Sync, Self Heal, and Pruning
* Deploying workloads on Amazon EKS

## Author

Justus Faby Jeyakumar

GitHub: https://github.com/JustusFaby
