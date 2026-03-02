# Azure Container Apps (ACA)

Azure Container Apps is a serverless container platform that lets you run containerized applications without managing the underlying infrastructure. It is built on Kubernetes under the hood but abstracts away all cluster management, so you never interact with Kubernetes directly.

## Key Concepts

### Container Apps Environment
A Container Apps **environment** is the secure boundary around a group of container apps. Apps in the same environment share the same virtual network and logging configuration. Think of it as the "workspace" your apps live in.

### Revisions
Every time you deploy a change to your container app, ACA creates a new **revision** — an immutable snapshot of your app at that point in time. You can run multiple revisions simultaneously, which enables blue/green deployments and A/B testing without extra tooling.

### Dapr Integration
ACA has built-in support for [Dapr](https://dapr.io/) (Distributed Application Runtime), which provides building blocks for microservices: service-to-service invocation, pub/sub messaging, state management, and more. Using Dapr is optional — you enable it per app if you want it.

### Jobs
In addition to long-running services, ACA supports **jobs** — container workloads that run to completion and exit. Jobs can be triggered manually, on a schedule (cron), or by events.

## Scaling

ACA uses **KEDA** (Kubernetes Event-Driven Autoscaling) to scale your apps based on:

- **HTTP traffic** — scale up as requests increase, scale down when traffic drops
- **Event sources** — Azure Service Bus queues, Kafka topics, Azure Storage queues, and more
- **Custom metrics** — any KEDA-supported scaler

**Scale-to-zero:** When there is no traffic or events, ACA can scale your app down to zero replicas — meaning you pay nothing when the app is idle. This is one of ACA's biggest advantages over App Service.

## Pricing

ACA offers two plan types:

| Plan | How It Works | Best For |
|------|-------------|----------|
| **Consumption** | Pay per vCPU-second and GiB-second of active use; scale-to-zero means no cost when idle | Event-driven workloads, variable traffic |
| **Dedicated** | Reserved compute capacity with predictable pricing | Steady workloads that need guaranteed resources |

With the Consumption plan, you only pay for what you use — making it very cost-effective for workloads with idle periods.

## Networking

- **External ingress** — expose your app to the internet with a built-in HTTPS endpoint and automatic TLS certificates
- **Internal ingress** — expose your app only within the environment (service-to-service communication)
- **VNet integration** — deploy your environment into a virtual network for network isolation
- **Service discovery** — apps in the same environment can call each other by name; Dapr adds additional discovery features

## When to Use ACA

- You are building **microservices** that need to communicate with each other
- Your workload is **event-driven** (responding to queue messages, webhooks, etc.)
- You want **scale-to-zero** to save costs during idle periods
- You want a managed platform but need **more flexibility than App Service** (custom ports, sidecars, Dapr)
- Your team knows Docker but **does not know or want to manage Kubernetes**

## OS Support

ACA supports **Linux containers only**. Windows containers are not supported.

If your application requires Windows (e.g., it depends on .NET Framework 4.x or Windows-specific APIs), you will need to use a different service — see [Azure Container Instances](azure-container-instances.md), [Azure App Service](azure-app-service.md), or AKS.

For most modern workloads (.NET 6+, Node.js, Python, Go, Java), Linux is the standard choice and works well with ACA.

## Limitations

- **No Windows container support** — Linux only
- **No direct node access** — you cannot SSH into the underlying infrastructure
- **GPU support** — limited compared to AKS; check current availability by region
- **Not all Kubernetes features** — features like custom CRDs, DaemonSets, or privileged containers are not available
- **Container image size** — very large images may have slower cold-start times

## Skill Set Needed

| Skill | Required? |
|-------|-----------|
| Docker / container basics | Yes |
| Understanding of HTTP and event-driven patterns | Yes |
| Dapr | Optional (only if using Dapr features) |
| Kubernetes | No — fully abstracted |
| Azure CLI or Portal | Yes (for deployment and configuration) |

ACA is a good fit for teams that are comfortable with containers but do not want the operational burden of managing Kubernetes clusters.

## Learn More

- [Azure Container Apps documentation](https://learn.microsoft.com/azure/container-apps/)
- [ACA source docs on GitHub](https://github.com/microsoftdocs/azure-docs/tree/main/articles/container-apps) — community-maintained, always up to date
- [ACA scaling and KEDA](https://github.com/microsoftdocs/azure-docs/blob/main/articles/container-apps/scale-app.md)
- [ACA networking](https://github.com/microsoftdocs/azure-docs/blob/main/articles/container-apps/networking.md)
- [ACA Dapr integration](https://github.com/microsoftdocs/azure-docs/blob/main/articles/container-apps/dapr-overview.md)
- [Back to README](../README.md)
