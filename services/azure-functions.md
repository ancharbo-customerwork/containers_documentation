# Azure Functions (Containers)

Azure Functions is a serverless compute service designed for running small, event-driven pieces of code — called **functions**. While Functions is best known for code-based deployments (upload your code and Azure runs it), it also supports **custom container** deployments. You package your function app into a Docker image and deploy it to a hosting plan that supports containers.

This page focuses on the containerized deployment model for Azure Functions.

## Key Concepts

### What Azure Functions Does
Azure Functions runs your code in response to **triggers** — events like an HTTP request, a queue message, a timer, a blob upload, or a database change. Each function handles one trigger and executes a small, focused piece of logic.

The key difference from other container services: Functions is **function-level**, not **application-level**. You write individual functions, not a full web server. The Functions runtime handles routing, scaling, and trigger binding for you.

### Hosting Plans for Containers

Not all Azure Functions hosting plans support custom containers. Here's the breakdown:

| Plan | Container Support | Scale-to-Zero | Best For |
|------|------------------|---------------|----------|
| **Consumption** | No | Yes | Code-only deployments (cheapest) |
| **Flex Consumption** | Yes | Yes | Event-driven containers with scale-to-zero |
| **Premium (Elastic Premium)** | Yes | No (pre-warmed instances) | Containers needing fast cold starts, VNet |
| **Dedicated (App Service Plan)** | Yes | No | Predictable pricing, existing App Service Plans |
| **Container Apps Environment** | Yes | Yes | Functions running inside ACA (hybrid approach) |

> **Important:** If you want to run Functions in a custom container, you need the **Flex Consumption**, **Premium**, **Dedicated**, or **Container Apps Environment** plan — not the standard Consumption plan.

### Functions on Container Apps
Azure Functions can be deployed **directly into an Azure Container Apps environment**. This gives you the Functions programming model (triggers, bindings, function-level code) with ACA's infrastructure (KEDA scaling, scale-to-zero, VNet, Dapr). This is a hybrid approach — covered more in the [Azure Container Apps deep-dive](azure-container-apps.md).

### Triggers and Bindings
The core advantage of Functions is its **trigger and binding system**:

- **Triggers** start execution: HTTP requests, queue messages, timer (cron), blob events, Cosmos DB changes, Event Grid, Event Hub, and more
- **Input bindings** pull data in automatically (e.g., read from a database)
- **Output bindings** push data out automatically (e.g., write to a queue)

This declarative approach means less boilerplate code compared to writing a full container application that manually reads from queues or watches for events.

## Scaling

Scaling behavior depends on the hosting plan:

| Plan | Scaling Model |
|------|--------------|
| **Flex Consumption** | Event-driven autoscaling; scales to zero when idle |
| **Premium** | Pre-warmed instances + event-driven burst; minimum 1 instance always running |
| **Dedicated** | Manual or rule-based autoscale (same as App Service) |
| **Container Apps Environment** | KEDA-based (same as ACA); scales to zero |

**When Functions scales vs. ACA:** Both ACA and Functions (on Flex Consumption or Container Apps Environment) use event-driven scaling. The difference is that Functions gives you the trigger/binding programming model on top, while ACA gives you a general-purpose container platform.

## Pricing

| Plan | Billing Model | Pay When Idle? |
|------|--------------|----------------|
| **Flex Consumption** | Per execution + per GiB-sec | No — scale-to-zero |
| **Premium** | Per vCPU-hour + memory (pre-warmed instances) | Yes — minimum 1 instance |
| **Dedicated** | Fixed App Service Plan cost | Yes — plan runs 24/7 |
| **Container Apps Environment** | Same as ACA Consumption (per vCPU-sec + GiB-sec) | No — scale-to-zero |

For containerized Functions with scale-to-zero, Flex Consumption and the Container Apps Environment plan are the most cost-effective.

## OS Support

Azure Functions containers support **Linux only** for custom container deployments. Windows containers are not supported for Functions in container mode.

If you need Windows, you can still use Azure Functions with a **code-based deployment** (no Docker) on a Windows hosting plan, but that is outside the scope of this container-focused guide.

## Networking

- **VNet integration** — available on Premium, Dedicated, and Container Apps Environment plans
- **Private endpoints** — supported on Premium and Dedicated plans
- **Public endpoint** — all plans expose an HTTP endpoint by default
- **Service discovery** — available when running on a Container Apps Environment (inherits ACA's service discovery)

> **Note:** The Flex Consumption plan has VNet support but with some restrictions. Check the current documentation for specifics.

## When to Use Azure Functions (Containers)

- Your app is fundamentally **event-driven** — responding to queue messages, blob uploads, timer schedules, HTTP webhooks
- You want the **trigger/binding programming model** to reduce boilerplate code for reading from and writing to Azure services
- Your function code has **custom dependencies** that require a specific OS-level package, library, or runtime version not available in the built-in Functions runtime
- You want to use a **language or runtime not natively supported** by Azure Functions (e.g., Rust, a specific Python version, or a custom ML framework)
- You already use Azure Functions (code-based) and want to **migrate to containers** for more control over the runtime environment

## When NOT to Use Azure Functions (Containers)

- **Your code has no custom dependencies** — if the built-in Functions runtime supports your language and packages, a code-based deployment is simpler and cheaper (Consumption plan, no Docker needed)
- **You are building a full web application or API** — Functions is designed for function-level logic, not for hosting a complete web server. Use App Service or ACA instead.
- **You need long-running processes** — Functions has execution time limits (varies by plan: 5–60 minutes). For long-running tasks, use ACI or ACA.
- **You want a general-purpose container platform** — ACA is more flexible for arbitrary containerized workloads

## Limitations

- **Execution time limits** — Functions have maximum execution durations (configurable per plan, but not unlimited)
- **Cold starts** — container-based Functions can have longer cold starts than code-based deployments due to image pull time
- **Linux containers only** — no Windows container support for custom container deployments
- **Not all triggers available everywhere** — some triggers may have restrictions depending on the hosting plan
- **Complexity of plan choice** — five hosting plans with different capabilities can be confusing; choosing the wrong plan is a common mistake

## Skill Set Needed

| Skill | Required? |
|-------|-----------|
| Docker / container basics | Yes |
| Azure Functions programming model | Yes (triggers, bindings, function.json / decorators) |
| Understanding of event-driven patterns | Yes |
| Azure CLI or Portal | Yes |
| Kubernetes | No (unless using AKS-hosted Functions) |
| Networking | Basic (only for VNet/private endpoint scenarios) |

### How Functions Skills Compare to Other Services

The unique skill here is the **Azure Functions programming model**. If your team already writes Azure Functions (code-based), the container version is a natural extension — you're just changing how the app is packaged. If your team has never used Functions, there's a learning curve for triggers, bindings, and the Functions project structure.

For teams that know Functions but not Docker: the container model adds a Dockerfile step but doesn't change how you write function code.

For teams that know Docker but not Functions: you'll need to learn the Functions SDK, trigger configuration, and binding syntax — but the Docker/deployment side will feel familiar.

## Azure Functions vs. ACA: Which Event-Driven Option?

Since both ACA and Functions (in containers) handle event-driven workloads with scale-to-zero, here's how to decide:

| Factor | Azure Functions (Containers) | Azure Container Apps |
|--------|-----------------------------|--------------------|
| **Programming model** | Function-level (triggers + bindings) | Application-level (full container) |
| **Best for** | Discrete event handlers, glue code, integrations | Microservices, APIs, general-purpose containers |
| **Boilerplate** | Less — triggers/bindings handle plumbing | More — you write the event-processing logic yourself |
| **Flexibility** | Constrained to Functions model | Full control over the application |
| **Execution limits** | Yes (time limits per invocation) | No inherent time limits |
| **Team already uses Functions?** | Easy to adopt — same code, just containerized | Requires rearchitecting as a standalone service |

**Rule of thumb:** If your workload is "react to event → do something small → done," Functions is a great fit. If your workload is a full service that happens to receive events, ACA is more appropriate.

## Learn More

- [Azure Functions documentation](https://learn.microsoft.com/azure/azure-functions/)
- [Azure Functions custom container deployment](https://learn.microsoft.com/azure/azure-functions/functions-create-container-registry)
- [Azure Functions hosting plans comparison](https://learn.microsoft.com/azure/azure-functions/functions-scale)
- [Functions source docs on GitHub](https://github.com/microsoftdocs/azure-docs/tree/main/articles/azure-functions) — community-maintained, always up to date
- [Functions container hosting](https://github.com/microsoftdocs/azure-docs/blob/main/articles/azure-functions/functions-deploy-container.md)
- [Functions on Container Apps](https://github.com/microsoftdocs/azure-docs/blob/main/articles/azure-functions/functions-container-apps-hosting.md)
- [Back to README](../README.md)
