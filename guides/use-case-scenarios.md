# Use-Case Scenarios

Real-world scenarios that match a **team profile** and a **workload** to the recommended Azure container service. Each scenario explains *why* the recommendation fits and what the team would need to learn if they chose a different option.

---

## Scenario 1: Small Startup — REST API

### Profile
- **Team:** 2–3 developers, no dedicated DevOps or infrastructure person
- **Skills:** Comfortable with web development (Node.js/Python/.NET), basic Docker experience, no Kubernetes knowledge
- **Priority:** Ship fast, minimize ops work, predictable costs

### Workload
A REST API serving a mobile app. Traffic is moderate and fairly steady throughout the day. The app needs a custom domain with HTTPS and basic autoscaling.

### Recommendation: **Azure App Service**

**Why it fits:**
- Lowest operational overhead — deploy a container image and App Service handles TLS, scaling, and health monitoring
- Deployment slots let the small team do zero-downtime releases without complex CI/CD pipelines
- The team's PaaS familiarity (if they've used Heroku, Railway, or similar) transfers directly
- Predictable monthly cost that's easy to budget for a startup

**If they chose ACA instead:** The team would need to learn about Container Apps environments, ingress configuration, and revision management — doable but more setup for a single API with no event-driven needs.

**If they chose AKS instead:** The team would need to learn Kubernetes fundamentals, `kubectl`, Helm, ingress controllers, and cluster maintenance. This would significantly slow their delivery pace.

---

## Scenario 2: Data Team — Nightly Batch Processing

### Profile
- **Team:** 3 data engineers, comfortable with Python scripting and Docker
- **Skills:** Can build Docker images, familiar with Azure CLI, no experience with container platforms
- **Priority:** Run jobs cheaply, only pay for compute when the job is running

### Workload
A Python container that processes sales data every night at 2 AM. The job takes about 90 minutes, uses 2 vCPUs and 4 GiB memory, and writes results to Azure Blob Storage.

### Recommendation: **Azure Container Instances (ACI)**

**Why it fits:**
- Simplest possible setup: `az container create` with a cron trigger (via Azure Logic Apps or Azure Automation)
- Pay-per-second billing means the team only pays for ~45 hours/month of compute
- No platform to manage — the container starts, runs, and stops
- The team's existing Docker + CLI skills are all that's needed

**If they chose ACA instead:** ACA Jobs would work well and adds features like event triggers and built-in scheduling. Worth considering if the team expects to add more jobs over time.

**If they chose App Service instead:** The team would pay 24/7 for a plan that's only used 90 minutes per day — significantly more expensive.

---

## Scenario 3: Mid-Size Team — Event-Driven Microservices

### Profile
- **Team:** 8 developers, 1 part-time DevOps engineer
- **Skills:** Strong Docker skills, experience with message queues, some familiarity with distributed systems. No Kubernetes experience.
- **Priority:** Handle variable traffic efficiently, keep services loosely coupled, avoid Kubernetes complexity

### Workload
Five microservices that communicate via Azure Service Bus. Traffic is heavy during business hours (8 AM–6 PM) and near-zero overnight and on weekends. Two services process queue messages; three serve HTTP traffic.

### Recommendation: **Azure Container Apps (ACA)**

**Why it fits:**
- KEDA-based scaling automatically adjusts replicas based on Service Bus queue depth — no custom scaling logic needed
- Scale-to-zero saves significant cost during off-hours (~60% of the week)
- Built-in service discovery lets services call each other by name
- Optional Dapr integration provides pub/sub and state management building blocks
- The DevOps engineer can manage the environment without Kubernetes expertise

**If they chose App Service instead:** Five separate App Service Plans running 24/7 would cost 3–5× more, and App Service lacks native queue-based scaling.

**If they chose AKS instead:** The single DevOps engineer would need to manage cluster upgrades, node pools, network policies, and Helm charts — a heavy burden for a part-time role.

---

## Scenario 4: Platform Team — Large-Scale Production System

### Profile
- **Team:** 20+ developers, 4 platform/SRE engineers with Kubernetes certification
- **Skills:** Deep Kubernetes expertise, Helm and Terraform experience, mature CI/CD pipelines with GitOps (Flux/ArgoCD)
- **Priority:** Maximum control over infrastructure, advanced networking, compliance requirements

### Workload
20 microservices with strict network segmentation, custom ingress rules, service mesh, and GPU-accelerated ML inference containers. Workloads span multiple namespaces with RBAC policies.

### Recommendation: **Azure Kubernetes Service (AKS)**

**Why it fits:**
- Full Kubernetes API access enables custom CRDs, DaemonSets, network policies, and service mesh
- The platform team has the skills to manage clusters, upgrades, and troubleshooting
- GPU node pools for ML workloads with autoscaling
- Existing GitOps workflows (Flux/ArgoCD) integrate natively

**If they chose ACA instead:** ACA cannot provide custom CRDs, DaemonSets, or fine-grained network policies. The team would lose capabilities they depend on.

**If they chose App Service instead:** Not viable — App Service cannot handle the networking complexity, GPU needs, or multi-service architecture at this scale.

---

## Scenario 5: Solo Developer — Side Project

### Profile
- **Team:** 1 developer working evenings and weekends
- **Skills:** Knows Python and basic Docker, has an Azure account with limited budget
- **Priority:** Spend as little as possible, minimal setup time, learn as they go

### Workload
A small web app (personal dashboard) that gets a handful of requests per hour. No need for autoscaling, custom domains, or advanced features.

### Recommendation: **Azure Container Apps (Consumption)**

**Why it fits:**
- Free tier grants cover light usage — the app may cost $0/month
- Scale-to-zero means no cost during the many hours with zero traffic
- Simple deployment via Azure CLI or Portal
- Room to grow — if the project gains users, ACA scales up without re-architecting

**Alternatively:** App Service Free tier works for code-based deployments (not custom containers). ACI works if the developer just wants to quickly test a container image.

**If they chose AKS instead:** Minimum ~$35/month for the smallest node, plus the time investment of learning Kubernetes — not worth it for a side project.

---

## Scenario 6: Integration Team — Event-Driven Glue Code

### Profile
- **Team:** 4 developers on an integrations team, familiar with C#/.NET or Python
- **Skills:** Strong experience with Azure services (Service Bus, Blob Storage, Cosmos DB). Know the Azure Functions programming model. Can write Dockerfiles but are not container experts.
- **Priority:** React to events from multiple Azure services, process data, and forward results — with custom dependencies that don't fit the built-in Functions runtime

### Workload
Three functions: (1) trigger on blob upload → resize image → write back to storage, (2) trigger on Service Bus message → enrich data from an API → write to Cosmos DB, (3) timer trigger → generate nightly summary report. The team needs a custom Linux image because the image processing library isn't available in the standard Functions runtime.

### Recommendation: **Azure Functions (Custom Container)**

**Why it fits:**
- The triggers/bindings model is the natural fit — "on this event, run this code" is exactly how this team thinks
- Custom container support means the team can install any native dependency (image processing, ML models, etc.) in their Docker image while keeping the Functions programming model
- Scale-to-zero on Flex Consumption means the nightly report function costs nothing during the day
- Built-in integration with Blob Storage, Service Bus, and Cosmos DB via bindings — no SDK boilerplate needed

**If they chose ACA instead:** The team would need to write their own queue consumers, blob event handlers, and timer loops. ACA can do it, but the team would be rebuilding what Functions provides out of the box.

**If they chose ACI instead:** ACI has no event-trigger mechanism — the team would need external orchestration (Logic Apps, Automation) and would lose the functions programming model.

**If they chose App Service instead:** No scale-to-zero, no native trigger/binding support, and the team would write more infrastructure code.

---

## Scenario 7: Team Migrating from Docker Compose

### Profile
- **Team:** 5 developers using Docker Compose locally for development
- **Skills:** Comfortable with Docker, `docker-compose.yml` files, and multi-container setups. No Kubernetes experience. Want to move to the cloud.
- **Priority:** Keep the development experience familiar, don't want to learn an entirely new paradigm

### Workload
A web app (frontend + backend API + Redis cache) currently defined in a Docker Compose file. The team wants to deploy this to Azure for staging and production.

### Recommendation: **Azure Container Apps (ACA)**

**Why it fits:**
- ACA supports deploying from Docker Compose-like configurations
- Each container (frontend, API, Redis) can be a separate container app in the same environment with built-in service discovery
- The team's Docker skills transfer directly — no need to learn Kubernetes
- The mental model (multiple containers communicating over a shared network) maps well to ACA environments

**If they chose App Service instead:** App Service supports multi-container via Docker Compose on Linux, but the feature is limited — no sidecar health probes, limited networking between containers, and Redis would likely need to be Azure Cache for Redis instead.

**If they chose AKS instead:** The team would need to convert their Compose file to Kubernetes manifests (or use Kompose), then learn `kubectl`, services, deployments, and ingress — a significant pivot from their current workflow.

---

## Summary Table

| Scenario | Team Size | Key Skill | Recommended Service |
|----------|-----------|-----------|-------------------|
| Startup REST API | 2–3 devs, no ops | Docker + PaaS basics | App Service |
| Nightly batch job | 3 data engineers | Docker + scripting | ACI |
| Event-driven microservices | 8 devs + 1 DevOps | Docker + queues | ACA |
| Large-scale production | 20+ devs + 4 SREs | Kubernetes + GitOps | AKS |
| Solo side project | 1 developer | Basic Docker | ACA (Consumption) |
| Event-driven glue code | 4 integration devs | Functions + Docker | Azure Functions (Container) |
| Docker Compose migration | 5 developers | Docker Compose | ACA |

## Learn More

- [Decision Guide](decision-guide.md)
- [Feature Comparison](../comparisons/feature-comparison.md)
- [Pricing Comparison](../comparisons/pricing-comparison.md)

### Official Azure Docs (GitHub)
- [Compare container hosting options in Azure](https://github.com/microsoftdocs/azure-docs/blob/main/articles/container-apps/compare-options.md)
- [Container Apps quickstart](https://github.com/microsoftdocs/azure-docs/blob/main/articles/container-apps/get-started.md)
- [Container Instances quickstart](https://github.com/microsoftdocs/azure-docs/blob/main/articles/container-instances/container-instances-quickstart.md)
- [App Service custom container quickstart](https://github.com/microsoftdocs/azure-docs/blob/main/articles/app-service/quickstart-custom-container.md)
- [Azure Functions on containers quickstart](https://github.com/microsoftdocs/azure-docs/blob/main/articles/azure-functions/functions-deploy-container.md)
- [Back to README](../README.md)
