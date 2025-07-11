# Homelab DevSecOps Repository

## Overview
This repository contains Kubernetes manifests and Kustomize configurations for deploying a home Kubernetes cluster (homelab). Focus is on DevSecOps practices with security-first approach.

## Repository Structure
- **Kustomize manifests**: Kubernetes resource definitions using Kustomize for configuration management
- **Homelab deployment**: Self-managed Kubernetes cluster deployment configurations
- **Security focus**: Emphasis on defensive security measures and best practices

## Key Technologies
- **Kubernetes**: Container orchestration platform
- **Kustomize**: Kubernetes native configuration management
- **DevSecOps**: Security-integrated development and operations practices

## Security Principles
- Defense in depth
- Least privilege access
- Security scanning and monitoring
- Secure defaults
- Infrastructure as code security

## Common Commands
- `k apply -k <directory>` - Apply Kustomize manifests (using kubectl alias)
- `kustomize build <directory>` - Build and preview Kustomize output
- `k get pods -A` - Check all pods across namespaces
- `k describe <resource>` - Get detailed resource information
- `k get nodes` - Check cluster nodes
- `k cluster-info` - Show cluster information

## Aliases
- `k` = `kubectl` (configured in ~/.bashrc for convenience)

## Development Guidelines
- All configurations should follow security best practices
- Use Kustomize for environment-specific configurations
- Implement proper RBAC and network policies
- Regular security audits and updates
- Documentation for all security configurations
- Always use declarative and implement DevSecOps best practices

## Testing
- Validate manifests before applying
- Test in isolated environments first
- Security scanning of container images
- Network policy testing
- RBAC validation