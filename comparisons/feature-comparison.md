# Feature Comparison

A side-by-side look at the four Azure container services. Use this table to quickly compare capabilities and find the right fit for your workload and team.

## Overview

| Feature | Azure Container Apps (ACA) | Azure Container Instances (ACI) | Azure App Service (Containers) | Azure Kubernetes Service (AKS) |
|---------|---------------------------|--------------------------------|-------------------------------|-------------------------------|
| **Service type** | Serverless containers | On-demand containers | Managed PaaS | Managed Kubernetes |
| **Abstraction level** | High — Kubernetes abstracted | High — no orchestration | High — PaaS managed | Low — full Kubernetes access |
| **Team skill required** | Medium | Low | Low | High |

## Scaling

| Capability | ACA | ACI | App Service | AKS |
|-----------|-----|-----|-------------|-----|
| **Autoscaling** | Yes — KEDA-based (HTTP, events, custom) | No | Yes — rule-based (CPU, memory, metrics) | Yes — HPA, VPA, KEDA, cluster autoscaler |
| **Scale-to-zero** | Yes | No (but you only pay while running) | No | Limited (with KEDA + virtual nodes) |
| **Max instances** | 300 per app (Consumption) | 1 per container group (manual) | 30 (Premium) / 100 (Isolated) | Thousands (depends on cluster size) |
| **Scale triggers** | HTTP requests, queue depth, cron, custom | N/A — manual only | CPU %, memory %, HTTP queue, schedule | Any Kubernetes-supported metric |

## Pricing Model

| Aspect | ACA | ACI | App Service | AKS |
|--------|-----|-----|-------------|-----|
| **Billing model** | Per vCPU-sec + GiB-sec (Consumption) or reserved (Dedicated) | Per vCPU-sec + GiB-sec | Fixed plan cost (tier-based) | Pay for underlying VMs; control plane is free |
| **Pay when idle** | No (Consumption plan) | No (stopped = no cost) | Yes (plan always runs) | Yes (nodes always run) |
| **Predictability** | Variable (Consumption) or fixed (Dedicated) | Variable | Fixed | Fixed (per node pool) |

## Networking

| Capability | ACA | ACI | App Service | AKS |
|-----------|-----|-----|-------------|-----|
| **Public endpoint** | Yes — built-in ingress | Yes — public IP + DNS label | Yes — custom domains + managed TLS | Yes — via ingress controller |
| **VNet integration** | Yes | Yes | Yes (Standard+) | Yes — full CNI |
| **Private endpoints** | Yes | Via VNet deployment | Yes | Yes |
| **Service discovery** | Yes — by app name + Dapr | No | No | Yes — Kubernetes DNS |
| **Network policies** | Limited | No | IP restrictions | Full Kubernetes network policies |
| **Load balancing** | Built-in | No — external LB required | Built-in | Via services + ingress controllers |

## Container Features

| Capability | ACA | ACI | App Service | AKS |
|-----------|-----|-----|-------------|-----|
| **OS support** | Linux only | Linux and Windows | Linux and Windows | Linux and Windows |
| **Sidecar containers** | Yes | Yes (container groups) | Limited (Docker Compose on Linux) | Yes (pods) |
| **Init containers** | Yes | Yes | No | Yes |
| **GPU support** | Limited (select regions) | Yes (select regions) | No | Yes |
| **Persistent storage** | Azure Files (mounted volumes) | Azure Files (mounted shares) | Built-in filesystem + mounted storage | Full Kubernetes persistent volumes |
| **Secrets management** | Built-in secrets + Key Vault refs | Environment variables + Key Vault | App settings + Key Vault refs | Kubernetes secrets + CSI driver |
| **Health probes** | Yes (liveness, readiness, startup) | No | Yes (health check endpoint) | Yes (liveness, readiness, startup) |

### Windows vs. Linux: What Beginners Should Know

Most Azure container services support **Linux containers**, and Linux is the default choice for the majority of workloads. Here's what to keep in mind:

- **Linux** — supported by all four services. Broadest feature availability, most community images, and lowest cost. If you're starting fresh, use Linux.
- **Windows** — supported by ACI, App Service, and AKS. Required if your application depends on Windows-specific frameworks (e.g., .NET Framework 4.x, legacy IIS workloads). Windows containers are larger, start slower, and cost more.
- **ACA does not support Windows containers** — if you need Windows, use ACI (for tasks), App Service (for web apps), or AKS (for orchestration).
- **Feature gaps** — some features (like multi-container groups in ACI, Docker Compose in App Service) are only available on Linux. Always check per-service documentation for OS-specific limitations.

**Rule of thumb:** Unless your app *requires* Windows, choose Linux.

## Operational Features

| Capability | ACA | ACI | App Service | AKS |
|-----------|-----|-----|-------------|-----|
| **Deployment slots** | Revisions (multi-revision mode) | No | Yes — staging/production swap | Rolling updates, blue/green via config |
| **CI/CD integration** | GitHub Actions, Azure Pipelines | CLI/API scripting | GitHub Actions, Azure Pipelines, local Git | GitHub Actions, Azure Pipelines, Flux, Argo |
| **Built-in observability** | Azure Monitor, Log Analytics | Azure Monitor (basic) | Azure Monitor, App Insights built-in | Azure Monitor, Prometheus, Grafana |
| **Managed TLS** | Yes — automatic | No — bring your own | Yes — free managed certificates | Via cert-manager or add-ons |
| **Custom domains** | Yes | No (DNS label only) | Yes | Via ingress controller config |

## Skill Set Comparison

| Skill | ACA | ACI | App Service | AKS |
|-------|-----|-----|-------------|-----|
| **Docker basics** | Required | Required | Required | Required |
| **Kubernetes** | Not needed | Not needed | Not needed | **Required** |
| **Helm / manifests** | Not needed | Not needed | Not needed | **Required** |
| **Networking** | Basic | Basic | Basic | **Intermediate to Advanced** |
| **CI/CD pipelines** | Helpful | Helpful | Helpful | **Important** |
| **Cluster operations** | Not needed | Not needed | Not needed | **Required** |
| **Dapr** | Optional | Not applicable | Not applicable | Optional |

### What This Means for Your Team

- **Small team, no Kubernetes skills?** → ACI (for simple tasks) or App Service (for web apps) or ACA (for microservices)
- **Growing team, want to avoid ops burden?** → ACA gives you more power than App Service without requiring Kubernetes knowledge
- **Dedicated platform/DevOps team?** → AKS gives maximum control but requires significant investment in Kubernetes expertise

## Learn More

- [Azure Container Apps](../services/azure-container-apps.md)
- [Azure Container Instances](../services/azure-container-instances.md)
- [Azure App Service](../services/azure-app-service.md)
- [Back to README](../README.md)
