# fullstack-pw: Cloud-Native DevOps Portfolio

Production-grade homelab platform demonstrating enterprise DevOps practices, cloud-native architectures, and infrastructure automation. Built as a comprehensive portfolio showcasing modern software engineering, SRE, and platform engineering capabilities.

## Platform Overview

The fullstack.pw organization encompasses a complete infrastructure and application ecosystem built on Kubernetes, implementing:

- Multi-environment Kubernetes clusters with automated provisioning and lifecycle management
- Infrastructure as Code with Terraform modular architecture and Ansible configuration management
- GitOps-driven continuous deployment with ArgoCD and automated CI/CD pipelines
- Comprehensive observability using Prometheus, Grafana, Jaeger, Loki, and OpenTelemetry
- Self-hosted CI/CD infrastructure with GitHub Actions and GitLab runners
- Defense-in-depth security with Vault, cert-manager, Istio service mesh, and SOPS encryption
- Automated cluster bootstrapping via commit-triggered workflows
- Hub-and-spoke telemetry architecture for centralized multi-cluster monitoring

## Architecture

![fullstack.pw Architecture](/fullstack.drawio.svg)

### Physical Infrastructure

**Compute Nodes**
- NODE01: Acer Nitro (i7-4710HQ, 16GB RAM)
- NODE02: HP ED800 G3 Mini (i7-7700T, 32GB RAM) with USB-attached storage
- NODE03: X99 dual Xeon E5-2699-V3 18-Core, 128GB RAM
- Lenovo Legion: Personal laptop running ephemeral CI/CD runners

**Virtualization**: Proxmox VE managing 10+ VMs across physical hosts with YAML-driven declarative provisioning

### Network Architecture

- Cloudflare: DNS, CDN, and WAF for public-facing services with automatic DNS record management
- HAProxy: Load balancer at k8s.fullstack.pw for vanilla Kubernetes traffic distribution
- Pi-hole: Internal DNS server with ad-blocking for homelab name resolution
- MetalLB: Bare metal load balancer for Kubernetes LoadBalancer service type
- External-DNS: Automated DNS record lifecycle management with TXT ownership verification

### Kubernetes Cluster Topology

| Cluster | Distribution | Purpose | Infrastructure | Key Platform Components |
|---------|--------------|---------|----------------|-------------------------|
| dev | K3s | Development environment | k8s-dev | Istio service mesh, development workloads |
| stg | K3s | Pre-production staging | k8s-stg | Staging validation, integration testing |
| prod | K3s | Production services | k8s-prod | Production applications with HA |
| tools | K3s | Platform infrastructure | k8s-tools | PostgreSQL, Redis, NATS, Vault, CI/CD runners |
| home | K3s | Home automation | k8s-home | Immich photo management, media services |
| observability | K3s | Central telemetry hub | k8s-observability | Prometheus, Grafana, Jaeger, Loki |
| sandboxy | K3s | Experimental platform | k8s-sandbox | KubeVirt, Longhorn distributed storage |

## Repository Organization

### Infrastructure & Platform

**[infra](https://github.com/fullstack-pw/infra)**: Complete infrastructure-as-code repository
- Terraform modules for Proxmox VM provisioning and Kubernetes workload deployment
- Ansible playbooks for cluster bootstrapping (K3s, vanilla K8s, Talos Linux)
- 35+ reusable Terraform modules (11 base + 24 application modules)
- GitHub Actions workflows for automated infrastructure lifecycle
- SOPS-encrypted secrets management with age encryption

**[pipelines](https://github.com/fullstack-pw/pipelines)**: Centralized CI/CD workflows
- Reusable GitHub Actions workflows for application deployment
- Multi-environment Kustomize-based deployment pipelines
- Security scanning and validation workflows
- Standardized build and release processes

### Application Repositories

**[demo-apps](https://github.com/fullstack-pw/demo-apps)**: Cloud-native microservices portfolio
- Go-based microservices with OpenTelemetry instrumentation
- Multi-environment Kustomize overlays (dev, stg, prod)
- Full observability stack integration (metrics, logs, traces)
- ArgoCD GitOps deployment manifests
- Examples: writer, enqueuer, memorizer microservices

**[cks-backend](https://github.com/fullstack-pw/cks-backend)**: CKS training platform backend
- Go-based backend implementing cluster pool management for Kubernetes security training
- KubeVirt integration for VM-based Kubernetes cluster provisioning
- Snapshot-based restoration achieving sub-second session allocation
- Unified validation engine supporting resource checks, script execution, and file content validation
- WebSocket terminal access with deterministic terminal IDs for reconnection

**[cks-frontend](https://github.com/fullstack-pw/cks-frontend)**: CKS training platform frontend
- Next.js 14 application with browser-based terminal emulation using xterm.js
- SWR-based state management with optimistic updates
- Multi-stage Docker build achieving 70% image size reduction via standalone output
- Admin dashboard for cluster pool and session management

## Platform Components

### Infrastructure Automation

| Component | Technology | Implementation |
|-----------|-----------|----------------|
| VM Provisioning | Terraform + Proxmox | YAML-driven declarative VM definitions |
| Configuration Management | Ansible | Idempotent playbooks with dynamic inventory |
| State Management | Terraform + MinIO S3 | Remote state with workspace isolation |
| Network Boot | PXE Boot Server | Rapid bare metal provisioning |

### Kubernetes Platform Services

| Service | Purpose | Implementation | Access |
|---------|---------|----------------|--------|
| cert-manager | Automated TLS certificate management | Let's Encrypt with DNS-01 challenge | Cluster-internal |
| External-DNS | Dynamic DNS record management | Cloudflare and Pi-hole integration | Cluster-internal |
| External Secrets | Vault to Kubernetes secret sync | ClusterSecretStore with namespace selector | Cluster-internal |
| Istio | Service mesh for dev cluster | mTLS, traffic management, observability | Gateway at dev cluster |
| NGINX Ingress | Ingress controller for other clusters | HTTP/HTTPS routing with TLS termination | Multiple clusters |
| MetalLB | LoadBalancer for bare metal | Layer 2 mode for service exposure | Vanilla K8s clusters |
| KubeVirt | Virtual machine orchestration | Nested VMs on Kubernetes | sandboxy cluster |

### Storage & Registry Infrastructure

| Service | Technology | Purpose | URL |
|---------|-----------|---------|-----|
| MinIO | S3-compatible object storage | Terraform state backend, backups | [s3.fullstack.pw](https://s3.fullstack.pw) |
| Harbor | Enterprise container registry | Multi-tenant registry with security scanning, image replication | [registry.fullstack.pw](https://registry.fullstack.pw) |
| Longhorn | Distributed block storage | Persistent volumes for KubeVirt VMs on sandboxy cluster | [longhorn.fullstack.pw](https://longhorn.fullstack.pw) |

### Secrets & Security

| Component | Implementation | Purpose | Location |
|-----------|---------------|---------|----------|
| HashiCorp Vault | KV v2 engine with K8s auth | Runtime secret storage and distribution | [vault.fullstack.pw](https://vault.fullstack.pw) |
| External Secrets Operator | ClusterSecretStore | Vault to Kubernetes secret synchronization | All clusters |
| SOPS + age | Encrypted YAML in Git | Secrets at rest in version control | infra repository |
| cert-manager | Let's Encrypt automation | TLS certificate lifecycle management | All clusters |
| Trivy | Security scanner | Container and IaC vulnerability scanning | CI/CD pipelines |

### CI/CD Infrastructure

| Component | Implementation | Deployment |
|-----------|---------------|------------|
| GitHub Actions Runners | Actions Runner Controller (ARC) | tools cluster |
| GitLab Runners | GitLab Runner with Docker executor | tools cluster |
| Custom Runner Image | Docker image with kubectl, Helm, Terraform, SOPS | Harbor registry |
| Reusable Workflows | Shared GitHub Actions workflows | [pipelines](https://github.com/fullstack-pw/pipelines) |

### Observability Stack

**Central Hub (Observability Cluster)**

| Component | Version | Purpose | Access |
|-----------|---------|---------|--------|
| Prometheus | kube-prometheus-stack v79.0.1 | Multi-cluster metrics aggregation | [prometheus.fullstack.pw](https://prometheus.fullstack.pw) |
| Grafana | Bundled with kube-prometheus-stack | Unified dashboards and visualization | [grafana.fullstack.pw](https://grafana.fullstack.pw) |
| Jaeger | v2.57.0 | Distributed tracing backend | [jaeger.fullstack.pw](https://jaeger.fullstack.pw) |
| Loki | v6.28.0 | Centralized log aggregation | [loki.fullstack.pw](https://loki.fullstack.pw) |
| OpenTelemetry Collector | v0.33.0 | Central telemetry ingestion and processing | Cluster-internal |

**Edge Collectors (All Other Clusters)**

Deployed via observability-box Terraform module:
- Prometheus with remote write to central hub
- Fluent Bit v0.48.9 for log forwarding to Loki
- OpenTelemetry Collector for trace and metric forwarding
- Automatic cluster labeling for multi-tenant observability

**Data Flow Architecture**
```
Application Pods → ServiceMonitor/PodMonitor → Prometheus (edge)
                → Fluent Bit → Loki (central)
                → OTel Collector (edge) → Jaeger (central)

Edge Prometheus → Remote Write → Central Prometheus → Grafana
```

### Data Services

| Service | Technology | Purpose | Deployment |
|---------|-----------|---------|------------|
| PostgreSQL | PostgreSQL 15 with pgvector | Relational database with vector search | tools cluster |
| Redis | Bitnami Redis chart | Caching and session storage | tools cluster |
| NATS | NATS with JetStream | Message broker and streaming | tools cluster |

### Home Services

| Service | Purpose | Technology | Access |
|---------|---------|-----------|--------|
| Immich | Self-hosted photo and video management | Container deployment with external storage | [immich.fullstack.pw](https://immich.fullstack.pw) |

## CI/CD Pipeline Architecture

**Build Stage**
- Conventional commit validation
- Automated unit and integration testing
- Container image building with multi-stage Dockerfiles
- Trivy security scanning (containers and IaC)
- TruffleHog secret leak detection
- Image pushing to Harbor/Docker Registry with SHA tagging

**Deploy Stage**
- Terraform plan on pull requests with parallel execution
- Terraform apply on merge with path-based change detection
- Kustomize overlay selection based on environment
- ArgoCD application sync with health checks
- Progressive environment promotion (dev → stg → prod)
- External Secrets synchronization from Vault

**Test Stage**
- Infrastructure validation tests
- End-to-end testing with Cypress
- Security posture validation
- SARIF output to GitHub Security tab

**Automation Workflows**

10 GitHub Actions workflows:
- terraform-plan.yml: Parallel plans for infrastructure changes
- terraform-apply.yml: Automated infrastructure deployment
- ansible.yml: VM provisioning via `[ansible PLAYBOOK]` commit tag
- build.yml: Container image build and push
- sec-trivy.yml: Vulnerability scanning
- sec-trufflehog.yml: Secret leak detection
- conventional-commits.yml: Commit message validation
- release.yml: Semantic versioning and changelog
- iac-tests.yml: Infrastructure validation

## DevOps Practices Demonstrated

**Infrastructure as Code**
- Two-tier Terraform module architecture for composability and reusability
- YAML-driven VM provisioning with dynamic resource creation
- Automated state backup to Oracle Cloud Object Storage
- Workspace-based environment isolation

**GitOps Methodology**
- Git as single source of truth for all infrastructure
- ArgoCD with app-of-apps pattern and sync waves
- Automated drift detection and remediation
- Pull request-based change management with automated validation

**Continuous Observability**
- Hub-and-spoke telemetry architecture
- OpenTelemetry instrumentation in applications
- Distributed tracing with correlation across services
- Custom dashboards and alerting rules (PostgreSQL, Redis, NATS)
- Structured JSON logging with trace context

**Security Best Practices**
- Multi-layered secrets management (SOPS → Vault → External Secrets)
- Automated TLS certificate lifecycle
- Service mesh with mTLS capability
- Continuous vulnerability scanning
- Least-privilege RBAC and Vault policies
- Non-root container execution

**Automation & Self-Service**
- Single-commit VM-to-Kubernetes provisioning workflow
- Automated cluster bootstrapping with kubeconfig integration
- Self-healing via Kubernetes probes and ArgoCD sync
- Automated DNS and certificate management

**Resilience & Recovery**
- Multi-cluster architecture for blast radius isolation
- Automated backup strategies (state, databases, storage)
- Complete infrastructure reproducible from Git
- High availability with replica distribution
- Disaster recovery procedures documented

## Module Architecture

**Base Modules (11 building blocks)**
- namespace: Kubernetes namespace with labels and annotations
- helm: Standardized Helm release deployment
- values-template: Dynamic Helm values rendering
- ingress: Ingress resource with TLS configuration
- persistence: PersistentVolumeClaim management
- credentials: Secret and ConfigMap handling
- monitoring: ServiceMonitor and PodMonitor creation
- istio-gateway: Istio Gateway resource
- istio-virtualservice: Istio VirtualService routing

**Application Modules (24 complete solutions)**

Platform Infrastructure:
- cert-manager, externaldns, external-secrets, argocd
- istio, ingress-nginx, metallb

Observability:
- observability (central hub), observability-box (edge collector)
- fluent, otel-collector

CI/CD:
- github-runner (ARC), gitlab-runner

Storage & Registry:
- harbor, minio, longhorn

Data Services:
- postgres, redis, nats

Applications:
- immich, kubevirt, terraform-state-backup

## Technologies & Tools

**Infrastructure & Virtualization**
- Proxmox VE, Terraform 1.10.5+, Ansible, cloud-init

**Kubernetes Ecosystem**
- K3s (lightweight), vanilla Kubernetes (HA), Talos Linux (immutable), KubeVirt (VMs)
- ArgoCD 7.7.12, Helm, Kustomize, kubectl

**Observability & Monitoring**
- Prometheus (kube-prometheus-stack v79.0.1), Grafana
- Jaeger v2.57.0, OpenTelemetry v0.33.0, Loki v6.28.0, Fluent Bit v0.48.9
- ServiceMonitor, PodMonitor, PrometheusRule CRDs

**Security & Compliance**
- HashiCorp Vault, SOPS with age encryption, cert-manager
- Istio service mesh, External Secrets Operator
- Trivy, TruffleHog, RBAC, Vault policies

**Networking & Service Mesh**
- Istio, NGINX Ingress, HAProxy, MetalLB
- External-DNS (Cloudflare + Pi-hole)
- Cloudflare (DNS, CDN, WAF)

**Storage Solutions**
- MinIO S3, Harbor, Docker Registry
- Longhorn distributed storage
- USB-attached storage for home services

**Data & Messaging**
- PostgreSQL 15 with pgvector extension
- Redis (Bitnami charts)
- NATS with JetStream streaming

**CI/CD & Automation**
- GitHub Actions with Actions Runner Controller
- GitLab CI with self-hosted runners
- Custom runner images with comprehensive tooling
- Semantic versioning and conventional commits

**Programming & Development**
- Go (microservices with OTel instrumentation)
- JavaScript/Node.js, Python
- Dockerfile multi-stage builds

## Documentation & Resources

**Infrastructure Documentation**
- [Infrastructure README](https://github.com/fullstack-pw/infra): Complete infrastructure overview

**Application Documentation**
- [Pipelines Documentation](https://github.com/fullstack-pw/pipelines): Reusable workflow specifications
- [Demo Apps](https://github.com/fullstack-pw/demo-apps): Cloud-native application examples
- [CKS Backend](https://github.com/fullstack-pw/cks-backend): CKS training platform backend implementation
- [CKS Frontend](https://github.com/fullstack-pw/cks-frontend): CKS training platform web interface

**Architecture Diagrams**
- Network topology and cluster layout
- Observability data flow architecture
- CI/CD pipeline workflows
- Secret management lifecycle
