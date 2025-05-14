# fullstack-pw: Home Lab Infrastructure & DevOps Platform

This organization houses the infrastructure, services, and applications that make up the **fullstack.pw** homelab environment - a comprehensive, production-grade platform built on Kubernetes for learning, experimentation, and portfolio demonstration.

## 🌐 Overview

The **fullstack.pw** platform demonstrates modern DevOps practices and cloud-native architectures in a homelab setting, including:

- **Multi-environment Kubernetes clusters** (dev, staging, production, home, sandbox, tools)
- **Infrastructure as Code** (Terraform, Ansible)
- **GitOps workflows** for continuous deployment
- **Observability stack** (Prometheus, Grafana, Jaeger, OpenTelemetry)
- **Self-hosted CI/CD** (GitHub Actions and GitLab CI/CD runners)
- **Security practices** (Vault, cert-manager, network policies, SOPS)
- **Custom CI/CD pipelines** (shared workflows for application deployments)
- **Home automation and media services** (Immich photo management)

## 🏗️ Architecture

![fullstack.pw Architecture](/fullstack.drawio.svg)

### Physical Infrastructure

The homelab runs on modest but capable hardware:

- **NODE01** (Acer Nitro, i7-4710HQ, 16GB RAM) - old notebook
- **NODE02** (HP ED800 G3 Mini, i7-7700T, 32GB RAM) - mini pc with USB storage
- **NODE03** (X99 2x Xeon E5-2699-V3 2.3Ghz 18-Core, 128GB RAM) - home server
- **Lenovo Legion** (Personal laptop, running ephemeral CI/CD runners)

All nodes run Proxmox VE for virtualization, hosting various VMs and Kubernetes clusters.

### Network Design

- **Cloudflare** provides DNS, CDN, and WAF services for public-facing components
- **HAProxy** load balancer (`k8s.fullstack.pw`) distributes traffic to Kubernetes clusters
- **Internal DNS** server (PiHole) provides name resolution within the homelab environment
- **MetalLB** provides internal load balancing services for Kubernetes

### Kubernetes Clusters

| Cluster | Type     | Purpose                                  | Node(s)               | Key Workloads                    |
|---------|----------|------------------------------------------|------------------------|-----------------------------------|
| Sandbox | K8s      | Central observability and core services  | k01 k02 k03           | Observability stack, Vault, Harbor |
| Sandboxy| K3s      | Experimental virtualization              | k8s-sandbox           | KubeVirt                         |
| Dev     | K3s      | Development environment                  | k8s-dev               | Development workloads            |
| Stg     | K3s      | Staging environment                      | k8s-stg               | Staging workloads                |
| Prod    | K3s      | Production environment                   | k8s-prod              | Production services              |
| Runners | K3s      | CI/CD pipeline execution                 | Local (Rancher Desktop)| GitHub/GitLab runners           |
| Tools   | K3s      | Supporting tools and services            | k8s-tools             | PostgreSQL, Redis, NATS, CI/CD   |
| Home    | K3s      | Home automation and media                | k8s-home              | Immich photo management          |

## 📁 Key Repositories

### Infrastructure & Platform

- [**infra**](https://github.com/fullstack-pw/infra): Infrastructure as code (Proxmox, Kubernetes, Terraform)
- [**pipelines**](https://github.com/fullstack-pw/pipelines): Reusable CI/CD workflows and pipelines

### Applications

- [**cks**](https://github.com/fullstack-pw/cks): Certified Kubernetes Security examples and best practices
- [**demo-apps**](https://github.com/fullstack-pw/demo-apps): Collection of cloud-native demonstration applications

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
| **K3s/K8s** | Lightweight and standard Kubernetes distributions | [infra](https://github.com/fullstack-pw/infra) |
| **Cert Manager** | Certificate automation with Let's Encrypt | [infra](https://github.com/fullstack-pw/infra) |
| **External DNS** | Dynamic DNS updates for services | [infra](https://github.com/fullstack-pw/infra) |
| **External Secrets** | Secret management with Vault integration | [infra](https://github.com/fullstack-pw/infra) |
| **NGINX Ingress** | Kubernetes ingress controller | [infra](https://github.com/fullstack-pw/infra) |
| **MetalLB** | Load balancing for bare metal K8s | [infra](https://github.com/fullstack-pw/infra) |
| **KubeVirt** | Virtual machine management on K8s | [infra](https://github.com/fullstack-pw/infra) |

### Storage & Services

| Service | Purpose | URL | Repository |
|---------|---------|-----|------------|
| **MinIO** | S3-compatible object storage | [s3.fullstack.pw](https://s3.fullstack.pw) | [infra](https://github.com/fullstack-pw/infra) |
| **Registry** | Private Docker registry | [registry.fullstack.pw](https://registry.fullstack.pw) | [infra](https://github.com/fullstack-pw/infra) |
| **Harbor** | Enterprise container registry | [registry.fullstack.pw](https://registry.fullstack.pw) | [infra](https://github.com/fullstack-pw/infra) |
| **Vault** | Secret management | [vault.fullstack.pw](https://vault.fullstack.pw) | [infra](https://github.com/fullstack-pw/infra) |

### CI/CD System

| Component | Description | Repository |
|-----------|-------------|------------|
| **GitHub Actions Runners** | Self-hosted GitHub Actions executor with custom Docker image | [infra](https://github.com/fullstack-pw/infra) |
| **GitLab Runners** | Self-hosted GitLab CI executor | [infra](https://github.com/fullstack-pw/infra) |
| **Reusable Workflows** | Shared CI/CD pipeline components | [pipelines](https://github.com/fullstack-pw/pipelines) |

### Observability Stack (Hub-and-Spoke Model)

#### Central Hub (Sandbox Cluster)
| Component | Description | URL | Repository |
|-----------|-------------|-----|------------|
| **Prometheus** | Metrics collection and storage | [prometheus.fullstack.pw](https://prometheus.fullstack.pw) | [infra](https://github.com/fullstack-pw/infra) |
| **Grafana** | Metrics visualization and dashboards | [grafana.fullstack.pw](https://grafana.fullstack.pw) | [infra](https://github.com/fullstack-pw/infra) |
| **Jaeger** | Distributed tracing | [jaeger.fullstack.pw](https://jaeger.fullstack.pw) | [infra](https://github.com/fullstack-pw/infra) |
| **Loki** | Log aggregation | [loki.fullstack.pw](https://loki.fullstack.pw) | [infra](https://github.com/fullstack-pw/infra) |
| **OpenTelemetry Collector** | Central telemetry ingestion | [otel-collector.fullstack.pw](https://otel-collector.fullstack.pw) | [infra](https://github.com/fullstack-pw/infra) |

#### Edge Collectors (All Other Clusters)
- **OpenTelemetry Collector**: Local trace and metric collection
- **Fluent Bit**: Log collection and forwarding
- **Prometheus**: Local metrics collection
- All forwarding to the central Sandbox cluster

### Home Services

| Service | Purpose | URL | Repository |
|---------|---------|-----|------------|
| **Immich** | Self-hosted photo and video management | [immich.fullstack.pw](https://immich.fullstack.pw) | [infra](https://github.com/fullstack-pw/infra) |

## 🚀 CI/CD Pipelines

The platform implements a robust CI/CD system with:

1. **Build Stage**
   - Automated testing and code quality checks
   - Security scanning with Trivy
   - Container image building with custom Docker images
   - Pushing to private registry

2. **Deploy Stage**
   - Kustomize-based deployments to multiple environments
   - Progressive delivery with environment promotion
   - Configuration management via External Secrets
   - Automated Terraform operations

3. **Test Stage**
   - End-to-end testing with Cypress
   - Infrastructure as Code validation
   - Security scanning

## 🔍 Observability Architecture

The platform features comprehensive observability with a hub-and-spoke architecture:

```
[Edge Clusters] → [observability-box] → [Central Sandbox] → [observability stack]
(dev/stg/prod/tools/home)              (Jaeger/Prometheus/Loki/Grafana)
```

- **Metrics**: Prometheus for infrastructure and application metrics
- **Logging**: Loki with Fluent Bit for log aggregation and analysis
- **Tracing**: Distributed tracing with Jaeger and OpenTelemetry
- **Dashboards**: Grafana dashboards for visualization

## 🛡️ Security

Security measures implemented in the platform include:

- **Secret Management**: HashiCorp Vault with Kubernetes integration
- **Secret Encryption**: SOPS with age encryption for GitOps
- **Certificate Management**: Automated TLS with cert-manager and Let's Encrypt
- **Authentication**: Role-based access control (RBAC) for Kubernetes resources
- **Network Security**: Firewall rules and network policies
- **Vulnerability Scanning**: Trivy for container and IaC scanning

## 🧪 Example Applications

The platform hosts several example applications to demonstrate its capabilities:

- [**CKS (Certified Kubernetes Security)**](https://github.com/fullstack-pw/cks): Kubernetes security best practices and examples
  - Security implementations and CKS exam preparation materials
  - Demonstrates security policies, RBAC, and hardening techniques

- [**Demo Apps**](https://github.com/fullstack-pw/demo-apps): Collection of demonstration applications
  - Various microservices showcasing different patterns
  - Cloud-native application examples with full observability
  - Multi-environment deployment configurations

## 📦 Module Architecture

The infrastructure follows a modular design with two categories:

### Base Modules (Building Blocks)
- **namespace**: Kubernetes namespace management
- **helm**: Standardized Helm chart deployment
- **values-template**: Template rendering for Helm values
- **ingress**: Common ingress configuration
- **persistence**: Volume management
- **credentials**: Secret handling
- **monitoring**: Prometheus/ServiceMonitor integration

### Application Modules (Complete Solutions)
Built by composing base modules:
- **cert-manager**, **externaldns**, **external-secrets**: Platform essentials
- **observability** & **observability-box**: Monitoring stack
- **github-runner** & **gitlab-runner**: CI/CD runners
- **postgres**, **redis**, **nats**: Data services
- **harbor**, **registry**, **minio**: Storage solutions
- **immich**: Home media management
- **kubevirt**: Virtualization platform

## 🔄 GitOps & Secret Management

The platform uses a GitOps approach with encrypted secrets:

- **SOPS with Age**: All secrets are encrypted in Git
- **External Secrets**: Syncs secrets from Vault to Kubernetes
- **Automated Workflows**: GitHub Actions for infrastructure changes
- **Terraform State**: Stored in MinIO with S3 backend

## 🚧 Recent Enhancements

Recent improvements to the platform include:

- **Custom GitHub Runner Image**: Enhanced with kubectl, Terraform, SOPS, and more
- **USB Storage Integration**: External storage for Immich photo management
- **KubeVirt Implementation**: VM management within Kubernetes
- **Observability Hub-and-Spoke**: Centralized monitoring architecture
- **Home Cluster**: Dedicated environment for home automation and media

## 🌟 Technologies Used

The fullstack.pw platform leverages numerous technologies:

- **Infrastructure**: Proxmox, Terraform, Ansible
- **Containerization**: Docker, Kubernetes (K3s/K8s), KubeVirt
- **CI/CD**: GitHub Actions, GitLab CI, Custom Docker images
- **Observability**: Prometheus, Grafana, Jaeger, OpenTelemetry, Loki, Fluent Bit
- **Security**: Vault, cert-manager, Trivy, SOPS
- **Networking**: HAProxy, NGINX Ingress, MetalLB, ExternalDNS
- **Storage**: MinIO, Harbor, Docker Registry, USB-attached storage
- **Databases**: PostgreSQL (with pgvector), Redis, NATS
- **Development**: Go, JavaScript/Node.js, Python

## 📚 Documentation

- Infrastructure diagrams: See [infra](https://github.com/fullstack-pw/infra) repository
- Pipeline documentation: See [pipelines](https://github.com/fullstack-pw/pipelines) repository
- Application specifications: See individual application repositories
- Secret management guide: See [docs/SECRETS_EXTRACTION.md](https://github.com/fullstack-pw/infra/blob/main/docs/SECRETS_EXTRACTION.md)

## 🤝 Contributing

This is a personal homelab project, but feel free to explore the code and use it as inspiration for your own infrastructure!
