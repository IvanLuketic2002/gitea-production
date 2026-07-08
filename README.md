# Gitea Production Deployment

Production-grade self-hosted Git server deployed on Kubernetes (k3s) on AWS EC2. Simulates real DevOps workflow — GitHub Actions automatically deploys the entire stack on every push.



## Architecture

```
GitHub Push
    │
    ▼
GitHub Actions CI/CD
    │
    ▼
AWS EC2 t3.small
    └── k3s Kubernetes Cluster
            └── Namespace: gitea
                    ├── Gitea :30000 (NodePort)
                    ├── PostgreSQL :5432
                    └── Prometheus :9090
```

## Features
- Self-hosted Git server (Gitea) — full GitHub-like functionality
- PostgreSQL database backend
- Prometheus monitoring
- GitHub Actions CI/CD — auto deploy on every push
- k3s lightweight Kubernetes on AWS EC2
- NodePort service for public access

## Tech Stack
- **Gitea** - Self-hosted Git service
- **Kubernetes (k3s)** - Container orchestration on EC2
- **PostgreSQL** - Database
- **Prometheus** - Monitoring
- **GitHub Actions** - CI/CD pipeline
- **AWS EC2 t3.small** - Cloud hosting
- **Terraform** - Infrastructure as Code (optional)

## Kubernetes Manifests

| File | Description |
|------|-------------|
| k8s/namespace.yml | gitea namespace |
| k8s/postgres.yml | PostgreSQL deployment + service |
| k8s/gitea.yml | Gitea deployment + NodePort service |
| monitoring/prometheus-config.yml | Prometheus ConfigMap |
| monitoring/prometheus.yml | Prometheus deployment + service |

## CI/CD Pipeline

Every push to `main` branch:
1. Connects to k3s cluster via kubeconfig secret
2. Applies all Kubernetes manifests
3. Waits for rollout to complete
4. Reports pod status

## Quick Start

```bash
# Install k3s on EC2
curl -sfL https://get.k3s.io | sh -s - --tls-san YOUR_EC2_IP

# Clone repo
git clone https://github.com/IvanLuketic2002/gitea-production.git
cd gitea-production

# Deploy manually
sudo kubectl apply -f k8s/
sudo kubectl apply -f monitoring/

# Check status
sudo kubectl get pods -n gitea
```

## GitHub Secrets Required

| Secret | Description |
|--------|-------------|
| KUBECONFIG | k3s kubeconfig with public EC2 IP |

## Cost
~$0.02/hour for EC2 t3.small. Run `terraform destroy` or stop instance after demo.
