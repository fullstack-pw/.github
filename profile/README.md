# fullstack-pw: Home Lab Infrastructure & DevOps Platform

This organization houses the infrastructure, services, and applications that make up the **fullstack.pw** homelab environment - a comprehensive, production-grade platform built on Kubernetes for learning, experimentation, and portfolio demonstration.

## 🌐 Overview

The **fullstack.pw** platform demonstrates modern DevOps practices and cloud-native architectures in a homelab setting, including:

- **Multi-environment Kubernetes clusters** (dev, staging, production)
- **Infrastructure as Code** (Terraform, Ansible)
- **GitOps workflows** for continuous deployment
- **Observability stack** (Prometheus, Grafana, Jaeger, OpenTelemetry)
- **Self-hosted CI/CD** (GitHub Actions and GitLab CI/CD runners)
- **Security practices** (Vault, cert-manager, network policies)
- **Custom CI/CD pipelines** (shared workflows for application deployments)

## 🏗️ Architecture

![fullstack.pw Architecture](/fullstack.drawio.svg)

### Physical Infrastructure

The homelab runs on modest but capable hardware:

- **NODE01** (Acer Nitro, i7-4710HQ, 16GB RAM)
- **NODE02** (HP ED800 G3 Mini, i7-7700T, 32GB RAM)
- **Lenovo Legion** (Personal laptop, running ephemeral CI/CD runners)

All nodes run Proxmox VE for virtualization, hosting various VMs and Kubernetes clusters.

### Network Design

- **Cloudflare** provides DNS, CDN, and WAF services for public-facing components
- **HAProxy** load balancer (`k8s.fullstack.pw`) distributes traffic to Kubernetes clusters
- **Internal DNS** server provides name resolution within the homelab environment
- **MetalLB** provides internal load balancing services for Kubernetes

### Kubernetes Clusters

| Cluster | Type     | Purpose                                  | Node(s)               |
|---------|----------|------------------------------------------|------------------------|
| Sandbox | K8s      | Testing and experimental workloads       | k01, k02, k03         |
| Dev     | K3s      | Development environment                  | k8s-dev               |
| Stg     | K3s      | Staging environment                      | k8s-stg               |
| Prod    | K3s      | Production environment                   | k8s-prod              |
| Runners | K3s      | CI/CD pipeline execution                 | Local (Rancher Desktop)|
| Tools   | K3s      | Supporting tools and services            | k8s-tools             |

## 📁 Key Repositories

### Infrastructure & Platform

- [**infra**](https://github.com/fullstack-pw/infra): Infrastructure as code (Proxmox, Kubernetes, Terraform)
- [**pipelines**](https://github.com/fullstack-pw/pipelines): Reusable CI/CD workflows and pipelines

### Applications

- [**api_usermgmt**](https://github.com/fullstack-pw/api_usermgmt): Demo API for user management
- [**trace-demo**](https://github.com/fullstack-pw/trace-demo): OpenTelemetry instrumentation demo

## 🔧 Core Components

### Infrastructure Layer

| Component | Description | Repository |
|-----------|-------------|------------|
| **Proxmox** | Virtualization platform for homelab nodes | [infra](https://github.com/fullstack-pw/infra) |
| **Terraform** | Infrastructure as code for VM and K8s resources | [infra](https://github.com/fullstack-pw/infra) |
| **Ansible** | Configuration management for nodes | [infra](https://github.com/fullstack-pw/infra) |
| **PXE Boot Server** | Network boot for quick node provisioning | [infra](https://github.com/fullstack-pw/infra) |

### Kubernetes Platform

| Component | Description | Repository |
|-----------|-------------|------------|
| **K3s/K8s** | Lightweight Kubernetes distribution | [infra](https://github.com/fullstack-pw/infra) |
| **Cert Manager** | Certificate automation with Let's Encrypt | [infra](https://github.com/fullstack-pw/infra) |
| **External DNS** | Dynamic DNS updates for services | [infra](https://github.com/fullstack-pw/infra) |
| **External Secrets** | Secret management with Vault integration | [infra](https://github.com/fullstack-pw/infra) |
| **NGINX Ingress** | Kubernetes ingress controller | [infra](https://github.com/fullstack-pw/infra) |
| **MetalLB** | Load balancing for bare metal K8s | [infra](https://github.com/fullstack-pw/infra) |

### Storage & Services

| Service | Purpose | URL | Repository |
|---------|---------|-----|------------|
| **MinIO** | S3-compatible object storage | [s3.fullstack.pw](https://s3.fullstack.pw) | [infra](https://github.com/fullstack-pw/infra) |
| **Registry** | Private Docker registry | [registry.fullstack.pw](https://registry.fullstack.pw) | [infra](https://github.com/fullstack-pw/infra) |
| **Vault** | Secret management | [vault.fullstack.pw](https://vault.fullstack.pw) | [infra](https://github.com/fullstack-pw/infra) |

### CI/CD System

| Component | Description | Repository |
|-----------|-------------|------------|
| **GitHub Actions Runners** | Self-hosted GitHub Actions executor | [pipelines](https://github.com/fullstack-pw/pipelines) |
| **GitLab Runners** | Self-hosted GitLab CI executor | [pipelines](https://github.com/fullstack-pw/pipelines) |
| **Reusable Workflows** | Shared CI/CD pipeline components | [pipelines](https://github.com/fullstack-pw/pipelines) |

### Observability Stack

| Component | Description | URL | Repository |
|-----------|-------------|-----|------------|
| **Prometheus** | Metrics collection and storage | [prometheus.fullstack.pw](https://prometheus.fullstack.pw) | [observability](https://github.com/fullstack-pw/observability) |
| **Grafana** | Metrics visualization and dashboards | [grafana.fullstack.pw](https://grafana.fullstack.pw) | [observability](https://github.com/fullstack-pw/observability) |
| **Jaeger** | Distributed tracing | [jaeger.fullstack.pw](https://jaeger.fullstack.pw) | [observability](https://github.com/fullstack-pw/observability) |
| **OpenTelemetry Collector** | Telemetry collection and processing | [otel-collector.fullstack.pw](https://otel-collector.fullstack.pw) | [observability](https://github.com/fullstack-pw/observability) |

## 🚀 CI/CD Pipelines

The platform implements a robust CI/CD system with:

1. **Build Stage**
   - Automated testing and code quality checks
   - Security scanning with Trivy
   - Container image building and pushing to private registry

2. **Deploy Stage**
   - Kustomize-based deployments to multiple environments
   - Progressive delivery with environment promotion
   - Configuration management via External Secrets

3. **Test Stage**
   - End-to-end testing with Cypress
   - Load testing (WIP)
   - Security scanning (WIP)

## 🔍 Observability

The platform features comprehensive observability with:

- **Metrics**: Prometheus for infrastructure and application metrics
- **Logging**: Log aggregation and analysis (Elasticsearch planned)
- **Tracing**: Distributed tracing with Jaeger and OpenTelemetry
- **Dashboards**: Grafana dashboards for visualization

## 🛡️ Security

Security measures implemented in the platform include:

- **Secret Management**: HashiCorp Vault with Kubernetes integration
- **Certificate Management**: Automated TLS with cert-manager and Let's Encrypt
- **Authentication**: Role-based access control (RBAC) for Kubernetes resources
- **Network Security**: Firewall rules and network policies
- **Vulnerability Scanning**: Trivy for container and IaC scanning

## 🧪 Example Applications

The platform hosts several example applications to demonstrate its capabilities:

- [**API User Management**](https://github.com/fullstack-pw/api_usermgmt): RESTful API with CRUD operations
  - Deployments: [dev.api-usermgmt.fullstack.pw](https://dev.api-usermgmt.fullstack.pw), [stg.api-usermgmt.fullstack.pw](https://stg.api-usermgmt.fullstack.pw), [api-usermgmt.fullstack.pw](https://api-usermgmt.fullstack.pw)

- [**Trace Demo**](https://github.com/fullstack-pw/trace-demo): OpenTelemetry instrumentation example
  - Deployments: [dev.trace-demo.fullstack.pw](https://dev.trace-demo.fullstack.pw), [stg.trace-demo.fullstack.pw](https://stg.trace-demo.fullstack.pw)

## 🚧 Future Enhancements

Planned improvements to the platform include:

- **Service Mesh**: Implementation of Istio or Linkerd
- **Automated Disaster Recovery**: Enhanced backup/restore capabilities
- **Security Hardening**: Pod Security Policies and additional scanning
- **Performance Testing**: Load testing integration in CI/CD pipelines

## 🌟 Technologies Used

The fullstack.pw platform leverages numerous technologies:

- **Infrastructure**: Proxmox, Terraform, Ansible
- **Containerization**: Docker, Kubernetes (K3s/K8s)
- **CI/CD**: GitHub Actions, GitLab CI
- **Observability**: Prometheus, Grafana, Jaeger, OpenTelemetry
- **Security**: Vault, cert-manager, Trivy
- **Networking**: HAProxy, NGINX Ingress, MetalLB, ExternalDNS
- **Storage**: MinIO, local path provisioners
- **Development**: Go, JavaScript/Node.js

## 📚 Documentation

- Infrastructure diagrams: See [infra](https://github.com/fullstack-pw/infra) repository
- Pipeline documentation: See [pipelines](https://github.com/fullstack-pw/pipelines) repository
- Application specifications: See individual application repositories
