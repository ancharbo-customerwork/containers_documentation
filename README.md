# Azure Container Services — A Beginner's Guide

Choosing the right container service in Azure can feel overwhelming. This guide breaks down the main options, explains when to use each one, and helps you pick the best fit based on your workload **and** your team's skill set.

## Services Covered

| Service | What It Is | Best For | Team Skill Level |
|---------|-----------|----------|-----------------|
| [Azure Container Apps (ACA)](services/azure-container-apps.md) | Serverless container platform with built-in scaling | Microservices, APIs, event-driven apps | Medium — Docker + basic scaling concepts |
| [Azure Container Instances (ACI)](services/azure-container-instances.md) | Run containers on-demand without managing infrastructure | Batch jobs, short-lived tasks, simple workloads | Low — Docker basics + Azure CLI |
| [Azure App Service](services/azure-app-service.md) | Managed PaaS for web apps and APIs with container support | Web apps, REST APIs, teams wanting minimal ops | Low — Docker basics + familiarity with PaaS |
| Azure Kubernetes Service (AKS) | Fully managed Kubernetes for complex container orchestration | Large-scale microservices, teams with Kubernetes expertise | High — Kubernetes, Helm, cluster ops |

> **Note:** AKS is included in comparisons for completeness, but this guide focuses primarily on ACA, ACI, and App Service — the three services most accessible to teams without deep Kubernetes experience.

## Quick Decision Flowchart

1. **Do you need full Kubernetes control and does your team have Kubernetes expertise?**
   - Yes → **AKS**
   - No → Continue

2. **Is your workload a short-lived task, batch job, or one-off process?**
   - Yes → **Azure Container Instances (ACI)**
   - No → Continue

3. **Is your workload event-driven or do you need scale-to-zero?**
   - Yes → **Azure Container Apps (ACA)**
   - No → Continue

4. **Are you deploying a web app or API and want the simplest managed experience?**
   - Yes → **Azure App Service**
   - Otherwise → **Azure Container Apps (ACA)** (the most versatile default)

For a more detailed decision process that accounts for team skills, scaling needs, networking, and cost: see the [Decision Guide](guides/decision-guide.md).

## Deeper Reading

### Service Deep-Dives
- [Azure Container Apps](services/azure-container-apps.md)
- [Azure Container Instances](services/azure-container-instances.md)
- [Azure App Service (Containers)](services/azure-app-service.md)

### Comparisons
- [Feature Comparison](comparisons/feature-comparison.md) — side-by-side table of capabilities across all four services
- [Pricing Comparison](comparisons/pricing-comparison.md) — cost models with real-world example scenarios

### Guides
- [Decision Guide](guides/decision-guide.md) — step-by-step walkthrough combining technical and skill-set factors
- [Use-Case Scenarios](guides/use-case-scenarios.md) — team profiles + workloads matched to recommended services

## Reference

For the most up-to-date information, see the official [Azure documentation](https://github.com/microsoftdocs/azure-docs).
# containers_documentation
