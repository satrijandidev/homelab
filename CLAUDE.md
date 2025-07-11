# Homelab DevSecOps Repository

## Overview
This repository contains production-ready Kubernetes manifests and Kustomize configurations for deploying a comprehensive home Kubernetes cluster (homelab) with focus on DevSecOps practices, security-first approach, and modern cloud-native technologies.

## Repository Structure
```
homelab/
├── applications/                    # Application deployments
│   ├── base/                       # Base application configurations
│   │   ├── clickhouse/             # Analytics database (ClickHouse)
│   │   ├── monitoring/             # Prometheus + Grafana monitoring stack
│   │   ├── pgadmin/                # Database administration interface
│   │   └── postgresql-cluster/     # PostgreSQL HA cluster (3 instances)
│   └── overlays/                   # Environment-specific configurations
│       ├── dev/                    # Development environment
│       ├── prod/                   # Production environment
│       └── staging/                # Staging environment
├── infrastructure/                  # Infrastructure components
│   ├── base/                       # Base infrastructure
│   │   ├── argocd/                 # GitOps continuous delivery
│   │   ├── cert-manager/           # SSL certificate management
│   │   ├── cilium/                 # Container networking with security policies
│   │   ├── cloudnative-pg/         # PostgreSQL operator
│   │   └── metrics-server/         # Resource metrics collection
│   └── overlays/                   # Environment-specific infrastructure
│       ├── dev/
│       ├── prod/
│       └── staging/
├── security/                       # Security policies and configurations
│   ├── base/                       # Base security policies
│   └── overlays/                   # Environment-specific security
└── docs/                          # Documentation
```

## Deployed Applications

### Analytics & Data
- **ClickHouse**: High-performance analytics database
  - HTTP Interface: `localhost:8123` (port-forward required)
  - Native TCP: `localhost:9000` (port-forward required)
  - MySQL Protocol: `localhost:9004` (port-forward required)
  - Authentication: `clickhouse:changeme123!` (full access), `readonly:readonly` (read-only)

### Monitoring & Observability
- **Prometheus**: Metrics collection and alerting system
- **Grafana**: Visualization dashboards and monitoring
- **Metrics Server**: Kubernetes resource metrics collection

### Database Infrastructure
- **PostgreSQL Cluster**: 3-instance HA cluster using CloudNative-PG operator
- **PgAdmin**: Web-based database administration interface

### Core Infrastructure
- **ArgoCD**: GitOps continuous delivery tool for Kubernetes applications
- **Cert-Manager**: Automatic SSL certificate provisioning and management
- **Cilium**: Advanced CNI with security policies and observability
- **CloudNative-PG**: PostgreSQL operator for cloud-native deployments

## Key Technologies
- **Kubernetes**: Container orchestration platform (1.25+)
- **Kustomize**: Kubernetes-native configuration management (v4.5+)
- **ArgoCD**: GitOps continuous delivery for Kubernetes
- **Cilium**: Advanced CNI with security policies and networking
- **CloudNative-PG**: PostgreSQL operator for high availability
- **Prometheus**: Metrics collection and monitoring
- **Grafana**: Visualization and dashboards
- **ClickHouse**: High-performance analytics database
- **Cert-Manager**: SSL certificate automation

## Security Principles & Implementation
- **Defense in Depth**: Multi-layered security controls
- **Least Privilege Access**: RBAC with minimal required permissions
- **Security Contexts**: Non-root containers, read-only filesystems, dropped capabilities
- **Resource Limits**: CPU/memory constraints and quotas
- **Network Policies**: Micro-segmentation and traffic control
- **Secret Management**: Secure credential storage and rotation
- **Container Security**: Security scanning and vulnerability management
- **Infrastructure as Code Security**: Secure defaults and policy enforcement

## Common Commands
- `kubectl apply -k <directory>` - Apply Kustomize manifests
- `kustomize build <directory>` - Build and preview Kustomize output
- `kubectl get pods -A` - Check all pods across namespaces
- `kubectl describe <resource>` - Get detailed resource information
- `kubectl get nodes` - Check cluster nodes
- `kubectl cluster-info` - Show cluster information

## Application Access & Port Forwarding
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

## ClickHouse Operations
```bash
# Authentication required - use credentials from secret
# Full access user: clickhouse:changeme123!
# Read-only user: readonly:readonly

# Basic queries
curl -u clickhouse:changeme123! "http://localhost:8123/" -d "SELECT version()"
curl -u clickhouse:changeme123! "http://localhost:8123/" -d "SHOW DATABASES FORMAT Pretty"
curl -u clickhouse:changeme123! "http://localhost:8123/" -d "SELECT * FROM system.clusters FORMAT Pretty"

# Using clickhouse-client (if available)
clickhouse-client --host localhost --port 9000 --user clickhouse --password changeme123!
```

## ArgoCD Operations
```bash
# Get initial admin password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

# Login via CLI (if argocd CLI is installed)
argocd login localhost:8080

# Access web UI
# https://localhost:8080 (accept self-signed certificate)
# Username: admin
# Password: from secret above

# Change admin password
argocd account update-password
```

## Development Guidelines
- **Security First**: All configurations must follow security best practices
- **Kustomize Native**: Use Kustomize for all environment-specific configurations
- **RBAC Required**: Implement proper role-based access control
- **Resource Limits**: Always define resource requests and limits
- **Network Policies**: Implement micro-segmentation where applicable
- **Documentation**: Document all security configurations and decisions
- **Testing**: Validate manifests and test in isolated environments first
- **Monitoring**: Include observability and monitoring for all components

## Deployment Workflow
1. **Infrastructure First**: Deploy infrastructure components before applications
2. **Environment Separation**: Use overlays for dev/staging/prod environments
3. **Security Validation**: Validate security contexts and RBAC before deployment
4. **Health Checks**: Verify deployment health before proceeding
5. **Monitoring Setup**: Ensure monitoring is configured for new components

## Testing & Validation
- **Manifest Validation**: Use `kubectl apply --dry-run=client` and `kustomize build`
- **Security Scanning**: Scan container images for vulnerabilities
- **Network Policy Testing**: Validate network segmentation
- **RBAC Validation**: Test role permissions and access controls
- **Resource Testing**: Verify resource limits and quotas
- **Environment Testing**: Test in isolated environments before production

## Troubleshooting
```bash
# Check pod status and logs
kubectl get pods -n <namespace>
kubectl logs -n <namespace> <pod-name>
kubectl describe pod -n <namespace> <pod-name>

# Check events
kubectl get events -n <namespace> --sort-by=.metadata.creationTimestamp

# Check resources
kubectl top pods -A
kubectl top nodes

# Network troubleshooting
kubectl get networkpolicies -A
kubectl get svc -A
```

## Security Considerations
- **Credential Management**: ClickHouse uses default passwords - change for production
- **Network Security**: Implement network policies for all namespaces
- **RBAC**: Follow principle of least privilege for all service accounts
- **Pod Security**: Use security contexts with non-root users
- **Image Security**: Scan all container images for vulnerabilities
- **Secret Rotation**: Implement regular secret rotation policies