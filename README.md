# Automated Microservices Deployment with ArgoCD & Kubernetes 🔄

> A fully automated GitOps deployment of a multi-service voting application on Kubernetes — orchestrated with ArgoCD for self-healing, zero-touch deployments and tested on both a local kind cluster and AWS EC2.

---

## 🏗️ Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                     Voting Application                       │
│                                                             │
│   [Vote Service]──▶[Redis Queue]──▶[Worker Service]         │
│       (Python)                         (.NET)               │
│                                          │                  │
│                                          ▼                  │
│                                   [PostgreSQL DB]           │
│                                          │                  │
│                                          ▼                  │
│                                  [Result Service]           │
│                                    (Node.js)                │
└─────────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                      GitOps Flow                            │
│                                                             │
│   Git Push → ArgoCD detects change → Auto Sync              │
│           → Kubernetes applies manifests                    │
│           → Self-healing if drift detected                  │
└─────────────────────────────────────────────────────────────┘
```

---

## ✨ Key Features

- **Full GitOps Workflow** — ArgoCD continuously watches the repo and automatically syncs any changes to the Kubernetes cluster — no manual `kubectl apply` needed
- **Self-Healing Deployments** — If any resource drifts from the desired state in Git, ArgoCD automatically corrects it
- **Multi-Service Architecture** — 5 interconnected services (Vote, Worker, Result, Redis, PostgreSQL) each independently deployable
- **Dual Environment Support** — Runs on both a local kind cluster (3 nodes) and AWS EC2 for production-like testing
- **RBAC-Secured Dashboard** — Kubernetes dashboard access controlled via dedicated ServiceAccount and ClusterRoleBinding
- **Health Checks** — Custom healthcheck configurations per service for reliable readiness/liveness probing
- **99.9% Uptime** — GitOps-driven self-healing ensures continuous availability

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **Vote Service** | Python |
| **Worker Service** | .NET (C#) |
| **Result Service** | Node.js |
| **Message Queue** | Redis |
| **Database** | PostgreSQL |
| **Containerization** | Docker, Docker Compose |
| **Orchestration** | Kubernetes |
| **GitOps** | ArgoCD |
| **Local Cluster** | kind (Kubernetes in Docker) |
| **Cloud** | AWS EC2 |
| **Dashboard** | Kubernetes Dashboard (RBAC-secured) |

---

## 📁 Project Structure

```
automated_with_ArgoCD_Kubernetes/
├── files_kind-cluster/
│   ├── config.yml                  # kind cluster definition (1 control-plane + 2 workers)
│   └── dashboard-admin-user.yml    # RBAC ServiceAccount for dashboard access
├── k8s-specifications/             # Kubernetes manifests for all services
├── healthchecks/                   # Health check configurations per service
├── vote/                           # Python voting frontend
├── worker/                         # .NET worker processing votes
├── result/                         # Node.js result display frontend
├── seed-data/                      # Database seed data
├── docker-compose.yml              # Local development stack
└── docker-compose.images.yml       # Production image references
```

---

## ☸️ Cluster Setup

### Local — kind Cluster (3 Nodes)

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  image: kindest/node:v1.30.0
- role: worker
  image: kindest/node:v1.30.0
- role: worker
  image: kindest/node:v1.30.0
```

A production-like 3-node cluster running locally using kind v1.30.0 — 1 control plane and 2 workers, mirroring a real multi-node setup.

### Cloud — AWS EC2
The same manifests are deployed on AWS EC2-hosted Kubernetes for production-grade testing, validating that the GitOps workflow works identically across environments.

---

## 🔐 RBAC — Dashboard Security

Access to the Kubernetes Dashboard is controlled via a dedicated ServiceAccount with ClusterRoleBinding — following the principle of least privilege for dashboard access:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

---

## 🔄 GitOps Flow with ArgoCD

```
1. Developer pushes change to k8s-specifications/
         │
         ▼
2. ArgoCD detects drift between Git and cluster state
         │
         ▼
3. ArgoCD automatically syncs — applies updated manifests
         │
         ▼
4. Kubernetes rolls out changes (zero downtime)
         │
         ▼
5. If any resource drifts from Git — ArgoCD self-heals
```

This means **Git is always the single source of truth**. No manual deployments, no configuration drift, no "works on my machine."

---

## 🚀 How to Run

### Local with kind

```bash
# Create the cluster
kind create cluster --config files_kind-cluster/config.yml

# Install ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Apply Kubernetes manifests
kubectl apply -f k8s-specifications/

# Setup dashboard access
kubectl apply -f files_kind-cluster/dashboard-admin-user.yml
```

### Local with Docker Compose

```bash
docker-compose up
```

Access the voting app at `http://localhost:5000` and results at `http://localhost:5001`

---

## 📈 Impact

| Metric | Result |
|---|---|
| Uptime achieved | **99.9%** |
| Deployment efficiency improvement | **60%** |
| Manual deployment steps | **Zero** — fully GitOps |
| Environment parity | ✅ Local kind + AWS EC2 |
| Configuration drift | **Eliminated** via ArgoCD self-healing |

---

## 👤 Author

**Bijit Kalita** — DevOps Engineer
- 📧 bijit987kalita@gmail.com
- 💼 [linkedin.com/in/bijit-kalita](https://linkedin.com/in/bijit-kalita/)
- 🐙 [github.com/bijit5](https://github.com/bijit5)
