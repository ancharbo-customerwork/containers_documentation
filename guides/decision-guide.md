# Decision Guide: Choosing the Right Azure Container Service

This guide walks you through a structured set of questions — covering both **technical requirements** and **team skill sets** — to help you land on the right Azure container service. Work through the sections in order; each one narrows down your options.

---

## Step 1: Assess Your Team's Skills (Start Here)

Before looking at technical features, honestly assess what your team knows today. This is the most common reason teams pick the wrong service: they choose based on features but struggle with the operational demands.

### Question 1A: Does your team have Kubernetes experience?

Specifically: Is someone on the team comfortable with `kubectl`, Helm charts, YAML manifests, and cluster lifecycle management (upgrades, node pool sizing, network policies)?

- **Yes, we have dedicated Kubernetes expertise** → AKS is an option. Continue to Step 2.
- **Some familiarity, but no deep expertise** → AKS will be a steep learning curve. Prefer ACA. Continue to Step 2.
- **No Kubernetes experience** → **Eliminate AKS.** Choose from ACA, ACI, or App Service. Continue to Step 2.

### Question 1B: Does your team have a dedicated DevOps or platform engineering function?

- **Yes** → Your team can absorb the operational overhead of AKS or complex ACA configurations.
- **No — developers handle ops too** → Favor services with less operational burden: App Service or ACA (Consumption).

### Question 1C: What is your team's Docker experience level?

- **Comfortable building and debugging Docker images** → All four services are viable.
- **Can write a basic Dockerfile but not much more** → ACI, App Service, or ACA are all fine. Avoid AKS.
- **New to Docker** → Start with **App Service** (easiest onboarding; lots of guides). Consider ACI for simple experiments.

### Quick Skill-Set Summary

| Service | Min. Skill Level | What Your Team Needs |
|---------|-----------------|---------------------|
| ACI | Low | Docker basics, Azure CLI |
| App Service | Low | Docker basics, Azure Portal/CLI, PaaS concepts |
| ACA | Medium | Docker, understanding of scaling and event-driven patterns |
| AKS | High | Kubernetes, Helm, networking, cluster operations, CI/CD |

---

## Step 2: Identify Your Workload Type

### Question 2A: Is your workload a short-lived task or batch job?

Examples: nightly data processing, CI/CD build runner, report generation, one-off migration scripts.

- **Yes** → **ACI** is the simplest and cheapest option. ACA Jobs also works well if you need event-triggered or scheduled jobs with more features.
- **No — it runs continuously or on-demand** → Continue to Step 3.

### Question 2B: Is your workload a single web application or API?

- **Yes, a single app/API with steady traffic** → **App Service** is the simplest choice. Continue to Step 4 to confirm.
- **No — multiple services or event-driven** → Continue to Step 3.

---

## Step 3: Evaluate Scaling and Event Needs

### Question 3A: Does your workload need to scale based on events (queue messages, topics, webhooks)?

- **Yes** → **ACA** is designed for this with KEDA-based scaling. AKS also supports KEDA but requires more setup.
- **No — traffic is HTTP-based or steady** → Continue.

### Question 3B: Do you need scale-to-zero to avoid paying during idle periods?

- **Yes** → **ACA (Consumption)** is the only service with native scale-to-zero for long-running apps. ACI only costs money while running, but you manage start/stop manually.
- **No — always-on is fine** → App Service, ACA (Dedicated), and AKS all work.

### Question 3C: Do you need to run many interconnected microservices?

- **Yes, and my team has Kubernetes expertise** → **AKS** gives the most control.
- **Yes, but my team does not know Kubernetes** → **ACA** provides microservice features (service discovery, Dapr, revisions) without Kubernetes complexity.
- **No — just one or two services** → App Service or ACA.

---

## Step 4: Consider Operational Requirements

### Question 4A: Do you need deployment slots for zero-downtime deployments?

- **Yes, and simplicity matters** → **App Service** has the most mature slot/swap experience.
- **Yes, and I want revision-based deployments** → **ACA** supports multi-revision traffic splitting.
- **Not critical** → Any service works.

### Question 4B: Do you need VNet isolation or private networking?

- **Yes, with complex network policies** → **AKS** provides the most networking flexibility.
- **Yes, basic VNet integration is enough** → ACA, ACI, and App Service all support VNet integration.
- **No** → Any service works.

### Question 4C: Do you need GPU support?

- **Yes** → **AKS** has the broadest GPU support. **ACI** supports GPUs in select regions. ACA and App Service have limited or no GPU options.
- **No** → Continue.

---

## Step 5: Factor in Cost Priorities

### Question 5A: Is cost predictability more important than cost efficiency?

- **Yes — I want a fixed monthly bill** → **App Service** (fixed plan cost) or **ACA Dedicated** (reserved profiles).
- **No — I want to pay only for what I use** → **ACA Consumption** or **ACI**.

### Question 5B: Is this a low-budget or personal project?

- **Yes** → **ACA Consumption** (free tier grants for light usage) or **ACI** (pennies for short tasks).
- **No — production budget is available** → Cost model matters less; choose based on features and skills.

---

## Decision Summary Matrix

After working through the steps above, use this matrix to confirm your choice:

| If your workload is... | And your team... | Then choose... |
|------------------------|-----------------|----------------|
| A short-lived batch job or task | Has basic Docker skills | **ACI** |
| A single web app or API | Wants the simplest managed experience | **App Service** |
| Event-driven microservices | Knows Docker but not Kubernetes | **ACA** |
| A web app that needs scale-to-zero | Wants to minimize idle costs | **ACA (Consumption)** |
| Complex multi-service with strict networking | Has Kubernetes + platform engineering expertise | **AKS** |
| A quick prototype or experiment | Is new to containers | **ACI** or **App Service** |
| A side project with minimal traffic | Wants lowest possible cost | **ACA (Consumption)** |

---

## Common Mistakes to Avoid

1. **Choosing AKS because "it's the industry standard"** — Kubernetes adds significant operational overhead. If your team doesn't have Kubernetes skills, the learning curve will slow your project down. ACA provides many of the same capabilities with far less complexity.

2. **Using App Service when you need scale-to-zero** — App Service Plans always cost money, even when idle. For event-driven or variable workloads, ACA Consumption is a better fit.

3. **Using ACI for long-running services** — ACI is meant for discrete tasks. For always-on services, App Service or ACA are better managed options.

4. **Ignoring your team's skill set** — Picking a service your team cannot operate effectively leads to longer development cycles, more incidents, and higher real cost than the service that's technically "best" on paper.

5. **Over-provisioning "just in case"** — Start with the simplest service that meets your needs. You can migrate to a more powerful option later as your workload and team grow.

## Learn More

- [Feature Comparison](../comparisons/feature-comparison.md)
- [Pricing Comparison](../comparisons/pricing-comparison.md)
- [Use-Case Scenarios](use-case-scenarios.md)
- [Back to README](../README.md)
