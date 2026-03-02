# Azure App Service (Containers)

Azure App Service is a fully managed platform-as-a-service (PaaS) for hosting web applications and APIs. While App Service is commonly used with code-based deployments (e.g., deploying a Node.js or .NET app directly), it also supports **custom container** deployments — you provide a Docker image and App Service runs it for you.

This page focuses on the containerized deployment model for App Service.

## Key Concepts

### App Service Plans
Every App Service app runs inside an **App Service Plan**, which defines the compute resources (CPU, memory, storage) available to your app. The plan determines your pricing tier and scaling capabilities.

| Tier | Description | Container Support |
|------|------------|------------------|
| **Free / Shared** | For testing and development | No custom containers |
| **Basic** | Dedicated compute, manual scale | Yes |
| **Standard** | Autoscale, deployment slots, VNet | Yes |
| **Premium** | More memory/CPU, enhanced networking | Yes |
| **Isolated** | Runs in a dedicated App Service Environment (ASE) | Yes |

### Deployment Slots
App Service provides **deployment slots** — separate live environments (e.g., staging, production) within the same app. You can deploy a new container image to a staging slot, test it, then **swap** it into production with zero downtime. This is a feature unique to App Service among the four container services.

### Custom Containers
You can deploy containers from:
- **Azure Container Registry (ACR)**
- **Docker Hub**
- **Any private registry**

App Service pulls the image, runs it, and routes HTTP traffic to it. Your container must listen on a port (configurable; defaults to 80 or 8080).

### Multi-Container (Docker Compose)
App Service on Linux supports running **multiple containers** defined in a Docker Compose file. This is useful for simple multi-container setups (e.g., an app plus a Redis sidecar), but it is more limited than ACA's sidecar support or AKS's pod model.

### OS Support: Linux vs. Windows
App Service supports both **Linux** and **Windows** containers, but the experience differs:

| Aspect | Linux | Windows |
|--------|-------|---------|
| **Custom containers** | Full support on Basic tier+ | Full support on Basic tier+ |
| **Multi-container (Compose)** | Supported | Not supported |
| **Base image size** | Smaller, faster deployments | Larger, slower cold starts |
| **Framework support** | .NET 6+, Node.js, Python, Java, Go, PHP | .NET Framework 4.x, .NET 6+, other Windows-native frameworks |
| **Pricing** | Same tier pricing | Same tier pricing (but Windows images may use more memory) |

**When to use Windows containers on App Service:** You have a .NET Framework 4.x application or a dependency on Windows-specific APIs/libraries that cannot run on Linux.

**Default recommendation:** Use Linux. It has broader feature support (multi-container, faster starts) and most modern frameworks run on Linux natively.

## Scaling

App Service supports **rule-based autoscaling** on Standard tier and above:

- Scale based on **CPU usage, memory, HTTP queue length**, or custom metrics
- Scale up to **30 instances** on Premium plans (more in Isolated/ASE)
- Scale rules are configured in the Azure Portal, CLI, or ARM/Bicep templates

**App Service does not support scale-to-zero.** Your App Service Plan is always running at least one instance, which means you pay for the plan even when there is no traffic.

## Pricing

App Service uses a **plan-based pricing model**:

- You pay for the App Service Plan (reserved compute), not per-request or per-second
- Multiple apps can share the same plan to reduce costs
- Higher tiers cost more but include more features (autoscale, slots, VNet)

This is different from ACA and ACI, where you pay for actual resource consumption. App Service pricing is more predictable but less cost-efficient for workloads with long idle periods.

## Networking

- **Custom domains and TLS** — built-in support for custom domains with free managed TLS certificates
- **VNet integration** — route outbound traffic from your app through a virtual network (Standard tier+)
- **Private endpoints** — make your app accessible only from within a VNet
- **Hybrid connections** — connect to on-premises resources without a VPN
- **IP restrictions** — allow or deny traffic by IP address or IP range

## When to Use App Service

- You are building a **web application or REST API** and want the simplest managed hosting
- Your team is familiar with PaaS concepts and wants **minimal operational overhead**
- You want features like **deployment slots** for zero-downtime deployments
- Your app has **steady traffic** (not event-driven, not bursty with long idle periods)
- You are already using App Service for code-based apps and want to **switch to container-based deployment** without changing platforms
- Your team **does not need scale-to-zero** and prefers predictable monthly costs

## Limitations

- **No scale-to-zero** — you always pay for at least one running instance
- **Limited container configuration** — you cannot customize the underlying host, run privileged containers, or access low-level settings
- **Multi-container is limited** — Docker Compose support exists but lacks the maturity of ACA environments or Kubernetes pods
- **Port restrictions** — your container must expose HTTP/HTTPS traffic; non-HTTP workloads (e.g., gRPC without HTTP/2, raw TCP) have limited support
- **Scaling ceiling** — maximum 30 instances on Premium (more with ASE, but at higher cost)

## Skill Set Needed

| Skill | Required? |
|-------|-----------|
| Docker / container basics | Yes |
| Azure Portal or CLI | Yes |
| Understanding of PaaS concepts | Helpful (plans, slots, scaling rules) |
| Web development basics (HTTP, DNS) | Yes |
| Kubernetes | No |
| Networking | Basic (only for VNet/private endpoint scenarios) |

App Service has a **low barrier to entry** and is well-suited for teams that want to deploy containers but do not want to learn orchestration platforms. If your team has experience with any cloud PaaS (Heroku, AWS Elastic Beanstalk, Google App Engine), App Service will feel familiar.

## Learn More

- [Azure App Service documentation](https://learn.microsoft.com/azure/app-service/)
- [Deploy a custom container to App Service](https://learn.microsoft.com/azure/app-service/quickstart-custom-container)
- [Back to README](../README.md)
