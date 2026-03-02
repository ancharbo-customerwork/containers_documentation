# Pricing Comparison

Understanding cost differences between Azure container services is critical for choosing the right one. This guide compares how each service charges you and walks through example scenarios so you can estimate real-world costs.

> **Note:** Pricing varies by region and changes over time. The numbers below are illustrative examples to show *how* each model works, not exact current prices. Always check the [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) for current rates.

## How Each Service Bills You

| Service | Billing Model | Pay When Idle? | Minimum Cost |
|---------|--------------|----------------|--------------|
| **Azure Container Apps (Consumption)** | Per vCPU-second + per GiB-second | No — scale-to-zero | $0 (free tier includes monthly grants) |
| **Azure Container Apps (Dedicated)** | Reserved workload profiles | Yes — reserved capacity runs continuously | Cost of the workload profile |
| **Azure Container Instances** | Per vCPU-second + per GiB-second | No — stopped containers cost nothing | $0 when not running |
| **Azure App Service** | Monthly plan cost (tier-based) | Yes — plan runs 24/7 | ~$13/month (Basic B1 tier) |
| **Azure Kubernetes Service** | Cost of VM node pools | Yes — nodes run 24/7 | Cost of smallest VM × node count |

## Key Takeaways

- **ACA (Consumption) and ACI** use true pay-per-use billing — ideal for workloads with unpredictable or bursty usage
- **App Service** has predictable monthly costs — good for steady workloads where you want budget certainty
- **AKS** costs are driven by the VMs in your node pools — you pay for capacity whether or not it's fully utilized

## Example Scenarios

### Scenario 1: Always-On Web API (24/7)

**Workload:** A REST API that runs continuously, handling moderate and steady traffic. Requires 1 vCPU and 2 GiB memory.

| Service | Approximate Monthly Cost | Notes |
|---------|------------------------|-------|
| **App Service (Standard S1)** | ~$70/month | Predictable; includes autoscale, slots |
| **ACA (Consumption)** | ~$45–65/month | Depends on exact usage; no idle savings since it's always processing |
| **ACA (Dedicated)** | ~$70–100/month | Reserved capacity, predictable |
| **ACI** | ~$55–75/month | Billed every second it runs; no autoscale |
| **AKS (1-node B2s)** | ~$35–45/month | Cheapest VM, but you manage the cluster |

**Recommendation:** For a simple always-on API, **App Service** offers the best balance of simplicity and cost predictability. ACA is competitive if you value auto-scaling headroom.

---

### Scenario 2: Nightly Batch Job (Runs 2 Hours/Day)

**Workload:** A data processing container that runs every night for approximately 2 hours, using 2 vCPUs and 4 GiB memory.

| Service | Approximate Monthly Cost | Notes |
|---------|------------------------|-------|
| **ACI** | ~$5–10/month | Only pay for the ~60 hours/month it runs |
| **ACA (Consumption)** | ~$5–10/month | Scale-to-zero; jobs feature fits this well |
| **App Service (Basic B2)** | ~$55/month | Pays 24/7 even though job runs 2 hours |
| **AKS** | ~$35+/month | Overkill; node runs 24/7 |

**Recommendation:** **ACI or ACA** — both charge only for actual runtime and are ideal for short-lived tasks.

---

### Scenario 3: Event-Driven Microservices (Variable Traffic)

**Workload:** Three microservices processing messages from Azure Service Bus. Traffic is heavy during business hours (8 hours/day) and near-zero overnight and weekends. Each service needs 0.5 vCPU and 1 GiB memory when active.

| Service | Approximate Monthly Cost | Notes |
|---------|------------------------|-------|
| **ACA (Consumption)** | ~$15–30/month | Scales to zero overnight/weekends; KEDA scales with queue depth |
| **App Service (3× Standard S1)** | ~$210/month | Three plans running 24/7; no scale-to-zero |
| **ACI (3 groups)** | ~$25–40/month | Manual management of start/stop; no autoscale on queue depth |
| **AKS (2-node cluster)** | ~$70–90/month | Handles it well but requires Kubernetes skills |

**Recommendation:** **ACA** is the clear winner here — scale-to-zero and KEDA-based queue scaling were built for exactly this pattern.

---

### Scenario 4: Solo Developer Side Project

**Workload:** A small web app with minimal traffic (a few requests per hour). 0.25 vCPU and 0.5 GiB memory is more than enough.

| Service | Approximate Monthly Cost | Notes |
|---------|------------------------|-------|
| **ACA (Consumption)** | ~$0–5/month | Free tier grants cover light usage; scale-to-zero |
| **ACI** | ~$1–5/month | Cheap, but needs to be manually kept running |
| **App Service (Free/Shared)** | $0–10/month | Free tier available but no custom containers; Basic starts at ~$13 |
| **AKS** | ~$35+/month | Way overkill for a side project |

**Recommendation:** **ACA (Consumption)** — the free tier grants and scale-to-zero make it essentially free for light workloads.

---

### Scenario 5: Large-Scale Production Microservices

**Workload:** 15 microservices running on dedicated infrastructure with guaranteed resources, high availability, and complex networking. Team has a platform engineering group.

| Service | Approximate Monthly Cost | Notes |
|---------|------------------------|-------|
| **AKS (multi-node cluster)** | $500–2000+/month | Full control; cost depends on VM sizes and count |
| **ACA (Dedicated)** | $300–1000+/month | Simpler ops than AKS, but less flexibility |
| **App Service (Premium/Isolated)** | $1000–3000+/month | Expensive at scale for many apps |
| **ACI** | Not practical | No orchestration for 15 services |

**Recommendation:** **AKS** if the team has Kubernetes expertise; **ACA (Dedicated)** if they want to avoid cluster management.

## Cost Optimization Tips

1. **Use scale-to-zero** (ACA Consumption) for workloads with idle periods — this is the single biggest cost saver
2. **Right-size your App Service Plan** — don't pay for Premium features you don't use
3. **Use ACI for short-lived jobs** instead of keeping a VM or App Service Plan running
4. **Share App Service Plans** — multiple apps can run on one plan to spread the cost
5. **Use AKS spot node pools** for interruptible batch workloads to save up to 80%
6. **Monitor actual usage** with Azure Cost Management to find over-provisioned resources

## Learn More

- [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/)
- [Feature Comparison](feature-comparison.md)
- [Back to README](../README.md)
