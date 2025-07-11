# Homelab DevSecOps Infrastructure

A comprehensive Kubernetes homelab deployment focused on DevSecOps practices, security-first approach, and modern cloud-native technologies.

## 🏗️ Infrastructure Overview

This repository contains production-ready Kubernetes manifests and Kustomize configurations for deploying a self-managed homelab cluster with emphasis on security, observability, and best practices.

### 🔧 Core Technologies
- **Kubernetes**: Container orchestration platform
- **Kustomize**: Kubernetes-native configuration management
- **Cilium**: Advanced CNI with security policies
- **Cert-Manager**: Automated SSL certificate management

## 📁 Repository Structure

```
homelab/
├── applications/                    # Application deployments
│   ├── base/                       # Base application configurations
│   │   ├── clickhouse/             # Analytics database
│   │   ├── monitoring/             # Prometheus + Grafana stack
│   │   ├── pgadmin/                # Database administration
│   │   └── postgresql-cluster/     # PostgreSQL HA cluster
│   └── overlays/                   # Environment-specific configurations
│       ├── dev/                    # Development environment
│       ├── prod/                   # Production environment
│       └── staging/                # Staging environment
├── infrastructure/                  # Infrastructure components
│   ├── base/                       # Base infrastructure
│   │   ├── cert-manager/           # SSL certificate management
│   │   ├── cilium/                 # Container networking
│   │   ├── cloudnative-pg/         # PostgreSQL operator
│   │   └── metrics-server/         # Resource metrics
│   └── overlays/                   # Environment-specific infrastructure
│       ├── dev/
│       ├── prod/
│       └── staging/
├── security/                       # Security policies and configurations
│   ├── base/                       # Base security policies
│   └── overlays/                   # Environment-specific security
└── docs/                          # Documentation
```

## 🚀 Deployed Applications

### Analytics & Data
- **ClickHouse**: High-performance analytics database with authentication
  - HTTP Interface: `localhost:8123`
  - Native TCP: `localhost:9000`
  - Credentials: `clickhouse:changeme123!` (full access), `readonly:readonly` (read-only)

### Monitoring & Observability
- **Prometheus**: Metrics collection and alerting
- **Grafana**: Visualization and dashboards
- **Metrics Server**: Resource metrics collection

### Database
- **PostgreSQL Cluster**: 3-instance HA cluster using CloudNative-PG operator
- **PgAdmin**: Web-based database administration interface

### Infrastructure
- **ArgoCD**: GitOps continuous delivery tool
- **Cert-Manager**: Automatic SSL certificate provisioning
- **Cilium**: Advanced networking with security policies
- **CloudNative-PG**: PostgreSQL operator for cloud-native deployments

## 🔐 Security Features

### Defense in Depth
- **Security Contexts**: Non-root containers, read-only filesystems, dropped capabilities
- **RBAC**: Role-based access control with least privilege
- **Resource Limits**: CPU/memory constraints and quotas
- **Network Policies**: Micro-segmentation and traffic control
- **Secret Management**: Secure credential storage and rotation

### Security Scanning
- Container image vulnerability scanning
- Kubernetes manifest security validation
- Regular security audits and compliance checks

## ⚙️ System Requirements

### Minimum Hardware Requirements

#### Development Environment
- **CPU**: 3 cores minimum
- **Memory**: 8Gi RAM minimum
- **Storage**: 30Gi minimum
- **Nodes**: 1-2 nodes

#### Staging Environment
- **CPU**: 4 cores minimum
- **Memory**: 10Gi RAM minimum
- **Storage**: 50Gi minimum
- **Nodes**: 2-3 nodes

#### Production Environment
- **CPU**: 8 cores minimum
- **Memory**: 16Gi RAM minimum
- **Storage**: 150Gi minimum
- **Nodes**: 3+ nodes (for high availability)

### Resource Allocation by Environment

| Component | Dev CPU/Memory | Staging CPU/Memory | Prod CPU/Memory |
|-----------|----------------|-------------------|-----------------|
| **Applications** | 1.8 cores / 2.9Gi | 2.1 cores / 3.6Gi | 3.4 cores / 6.2Gi |
| **Infrastructure** | 1.3 cores / 2.5Gi | 1.5 cores / 3.0Gi | 2.2 cores / 4.9Gi |
| **System Overhead** | 0.5 cores / 1.6Gi | 0.9 cores / 2.4Gi | 1.4 cores / 3.4Gi |
| **Total Required** | 3.6 cores / 7.0Gi | 4.5 cores / 9.0Gi | 7.0 cores / 14.5Gi |

### Key Resource Consumers

- **ArgoCD**: Most resource-intensive infrastructure component
- **ClickHouse**: Scales significantly in production (4x CPU, 4x memory)
- **PostgreSQL**: Consistent 3-instance HA cluster across all environments
- **Monitoring Stack**: Prometheus + Grafana for observability

## 🛠️ Quick Start

### Prerequisites
- Kubernetes cluster (1.25+)
- kubectl configured with cluster access
- Kustomize (v4.5+)
- Sufficient resources per environment requirements above

### Deploy Infrastructure
```bash
# Deploy core infrastructure
kubectl apply -k infrastructure/overlays/prod

# Verify infrastructure is ready
kubectl get pods -A
```

### Deploy Applications
```bash
# Deploy all applications
kubectl apply -k applications/overlays/prod

# Check application status
kubectl get pods -A
```

### Access Applications
```bash
# Port forward ClickHouse
kubectl port-forward -n clickhouse svc/clickhouse 8123:8123 9000:9000

# Port forward Grafana
kubectl port-forward -n monitoring svc/grafana 3000:3000

# Port forward PgAdmin
kubectl port-forward -n postgresql svc/pgadmin 5050:80

# ArgoCD
kubectl port-forward -n argocd svc/argocd-server 8080:443
```

## 📊 Monitoring & Observability

### Prometheus Metrics
Access Prometheus at `localhost:9090` (when port-forwarded)

### Grafana Dashboards
Access Grafana at `localhost:3000` (when port-forwarded)

### ArgoCD GitOps
Access ArgoCD at `https://localhost:8080` (when port-forwarded)
- Username: `admin`
- Password: `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`
### Health Checks
```bash
# Check cluster health
kubectl get nodes
kubectl get pods -A

# Check application health
kubectl get pods -n clickhouse
kubectl get pods -n monitoring
kubectl get pods -n postgresql
kubectl get pods -n argocd
```

## 🔧 Common Operations

### Port Forwarding
```bash
# ClickHouse
kubectl port-forward -n clickhouse svc/clickhouse 8123:8123 9000:9000

# Grafana
kubectl port-forward -n monitoring svc/grafana 3000:3000

# Prometheus
kubectl port-forward -n monitoring svc/prometheus 9090:9090

# PgAdmin
kubectl port-forward -n postgresql svc/pgadmin 5050:80

# ArgoCD
kubectl port-forward -n argocd svc/argocd-server 8080:443
```

### Database Operations
```bash
# ClickHouse queries
curl -u clickhouse:changeme123! "http://localhost:8123/" -d "SELECT version()"
curl -u clickhouse:changeme123! "http://localhost:8123/" -d "SHOW DATABASES FORMAT Pretty"

# PostgreSQL connection
kubectl exec -it -n postgresql postgresql-cluster-1 -- psql -U postgres
```

### Troubleshooting
```bash
# Check pod logs
kubectl logs -n <namespace> <pod-name>

# Describe resources
kubectl describe pod -n <namespace> <pod-name>

# Check events
kubectl get events -n <namespace> --sort-by=.metadata.creationTimestamp
```

## 🔄 Environment Management

### Development Environment
```bash
kubectl apply -k applications/overlays/dev
kubectl apply -k infrastructure/overlays/dev
```

### Production Environment
```bash
kubectl apply -k applications/overlays/prod
kubectl apply -k infrastructure/overlays/prod
```

### Staging Environment
```bash
kubectl apply -k applications/overlays/staging
kubectl apply -k infrastructure/overlays/staging
```

## 📚 Documentation

- [CLAUDE.md](./CLAUDE.md) - Developer instructions and project context
- [Security Best Practices](./docs/security.md) - Security guidelines and policies
- [Deployment Guide](./docs/deployment.md) - Detailed deployment instructions
- [Troubleshooting](./docs/troubleshooting.md) - Common issues and solutions

## 🤝 Contributing

1. Follow security-first principles
2. Use Kustomize for configuration management
3. Implement proper RBAC and network policies
4. Add comprehensive testing and validation
5. Document all changes and configurations

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
