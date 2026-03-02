# Azure Container Instances (ACI)

Azure Container Instances is the fastest and simplest way to run a container in Azure. There are no virtual machines to manage, no orchestrators to configure — you give Azure a container image, and it runs it. ACI is designed for scenarios where you need to run containers without the overhead of a full platform.

## Key Concepts

### Container Groups
A **container group** is the basic unit in ACI. It is a collection of containers that are scheduled on the same host machine and share a lifecycle, local network, and storage volumes. A container group is similar to a Kubernetes pod.

You can run a single container per group (the most common case) or co-locate multiple containers that need to work together (e.g., an app container plus a logging sidecar).

### Restart Policies
Each container group has a **restart policy** that controls what happens when a container exits:

| Policy | Behavior | Use Case |
|--------|----------|----------|
| **Always** | Restart the container whenever it stops | Long-running services |
| **OnFailure** | Restart only on non-zero exit code | Tasks that should retry on failure |
| **Never** | Do not restart | One-off jobs, batch tasks |

### OS Support
ACI supports both **Linux** and **Windows** containers.

| Aspect | Linux | Windows |
|--------|-------|---------|
| **Region availability** | All ACI regions | Fewer regions — check availability |
| **Container group features** | Full support (multi-container, init containers, VNet) | Some limitations (e.g., multi-container groups may not be available) |
| **Image size** | Typically smaller, faster pulls | Larger base images, slower cold starts |
| **Cost** | Standard ACI pricing | Same billing model but Windows images use more resources |
| **GPU support** | Yes (select regions) | Limited |

**When to use Windows containers on ACI:** Your application depends on .NET Framework 4.x, Windows-specific libraries, or legacy Windows services that cannot be recompiled for Linux.

**Default recommendation:** Use Linux unless your app specifically requires Windows.

## Scaling

ACI **does not autoscale**. Each container group runs exactly the number of containers you define. If you need more capacity, you create additional container groups. This makes ACI unsuitable for workloads with highly variable traffic — but perfect for predictable, discrete tasks.

For burst scaling from AKS, you can use the **virtual nodes** feature, which spins up containers on ACI when your AKS cluster needs extra capacity.

## Pricing

ACI uses **pure pay-per-use pricing**:

- Billed per **vCPU-second** and **GiB-second** of memory
- Billing starts when the container group is created and stops when it is deallocated
- No upfront costs, no reserved capacity

This makes ACI extremely cost-effective for short-lived workloads. A container that runs for 5 minutes costs a fraction of what a dedicated VM would cost.

### Resource Limits (vary by region)

| Resource | Typical Limit |
|----------|--------------|
| vCPUs per container group | Up to 4 |
| Memory per container group | Up to 16 GiB |
| GPU | Available in select regions (NVIDIA) |

## Networking

- **Public IP** — assign a public IP address and DNS name label directly to a container group
- **VNet integration** — deploy container groups into an Azure Virtual Network for private communication with other Azure resources
- **No built-in load balancing** — if you need to distribute traffic across multiple container groups, you must use an external load balancer (e.g., Azure Application Gateway)
- **Port exposure** — you define which ports are exposed when you create the container group

## When to Use ACI

- **Batch processing** — run a data processing job, wait for it to finish, then tear it down
- **CI/CD build agents** — spin up a container for each build, tear it down when complete
- **Scheduled tasks** — run nightly reports, database maintenance, or data exports
- **Quick prototyping** — test a container image without setting up infrastructure
- **AKS burst scaling** — overflow workloads from AKS via virtual nodes
- **Simple, single-purpose services** — a container that does one thing and does not need orchestration

## Limitations

- **No autoscaling** — you must manually manage the number of container groups
- **No orchestration** — no built-in service discovery, load balancing, or inter-service communication
- **Resource caps** — limited vCPU and memory per container group
- **No persistent storage by default** — you can mount Azure Files shares, but there is no built-in persistent volume system
- **Cold start** — starting a new container group takes a few seconds to a minute depending on image size

## Skill Set Needed

| Skill | Required? |
|-------|-----------|
| Docker / container basics | Yes |
| Azure CLI or Portal | Yes |
| Scripting (for automation) | Helpful for scheduling and managing groups |
| Kubernetes | No |
| Networking | Basic (only if using VNet integration) |

ACI has the **lowest barrier to entry** of all four container services. If you can build a Docker image, you can run it on ACI.

## Learn More

- [Azure Container Instances documentation](https://learn.microsoft.com/azure/container-instances/)
- [ACI source docs on GitHub](https://github.com/microsoftdocs/azure-docs/tree/main/articles/container-instances) — community-maintained, always up to date
- [ACI container groups](https://github.com/microsoftdocs/azure-docs/blob/main/articles/container-instances/container-instances-container-groups.md)
- [ACI virtual network deployment](https://github.com/microsoftdocs/azure-docs/blob/main/articles/container-instances/container-instances-vnet.md)
- [ACI GPU resources](https://github.com/microsoftdocs/azure-docs/blob/main/articles/container-instances/container-instances-gpu.md)
- [Back to README](../README.md)
