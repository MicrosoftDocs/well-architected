---
title: Application Design for AI Workloads on Azure
description: Learn about design considerations for creating AI workloads on Azure, including design patterns and architecture considerations.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/18/2024
ms.topic: conceptual
ms.update-cycle: 180-days  
---

# Application design for AI workloads on Azure

There are many choices to consider when you design intelligent capabilities into your workload. Your unique functional and nonfunctional requirements help you determine whether you need basic inferencing capabilities or more dynamic problem-solving capabilities. These requirements guide your high-level design decisions. You'll consider these choices as you move from high-level design areas to lower-level design areas.

As discussed in the [Get started](./get-started.md) article, whether to build your own model or use a prebuilt model is one of the first important decisions to make. When you use a prebuilt model, consider these points:

- **Catalog sources**. Explore repositories like the Hugging Face Model Hub, TensorFlow Hub, and the [Azure AI Foundry portal](https://ai.azure.com?cid=learnDocs) model catalog to find pre-trained models. These platforms provide an extensive catalog of models for various tasks.

- **Licensing**. Make sure the model's licensing terms fit your security, compliance, and application goals, especially if you plan to distribute the application or integrate it with other services.

- **Key components**. Look at the model's architecture, training data, and performance to determine whether it's fine-tuned for your task or domain.

For guidance on choosing a hosting platform, see [Considerations for the model hosting and inferencing platform](./application-platform.md#considerations-for-the-model-hosting-and-inferencing-platform).

This article describes common design areas and factors to consider when you make decisions about technology and approach. The guidance applies to both traditional machine learning (ML) and generative AI workloads, covering frameworks like TensorFlow, Keras, and PyTorch, as well as various serving and hosting options including batch, stream, and online processing.

## Application layer architecture

When designing intelligent capabilities, establish clear boundaries in your design across these five key layers:

:::image type="content" source="../_images/ai-application-layers.svg" alt-text="Diagram showing the four application layers: Client, Intelligence, Knowledge, and Tools." lightbox="../_images/ai-application-layers.svg":::

- **Client layer**. The user interface and client applications where users or processes experience your workload's intelligent capabilities. Keep this layer thin and delegate most capabilities to other layers.

- **Intelligence layer**. Routing, orchestration, and agent capabilities that coordinate AI operations. This layer includes model routing, conversation management, and intelligent decision-making.

- **Inferencing layer**. Executing trained models to generate predictions or decisions based on data. This layer handles model loading and runtime execution, input preprocessing and output postprocessing, along with serving predictions via APIs or embedded systems.

- **Knowledge layer**. Grounding data, knowledge graphs, and retrieval services that provides relevant context and information to the intelligence layer. This layer enforces data access policies and authorization.

- **Tools layer**. Business APIs, external services, and action capabilities that the intelligence layer can invoke. This layer should use standardized interfaces and enforce its own security policies.

Each layer enforces its own policies, identities, and caching strategies to achieve their own localized reliability, security, and performance requirements. The layers also enable focused development, testing, and troubleshooting.

## Recommendations

The following table summarizes the recommendations provided in this article.

|Recommendation|Description|
|---|---|
| **Prioritize security and Responsible AI controls**. | Implement traditional application security plus AI-specific safety measures as a primary design driver. Enforce provider safety systems, input/output filtering, identity-bound rate limiting and quotas, and token/prompt caps. Security and safety controls must be verified and cannot be assumed from managed services. |
| **Keep intelligence away from the client**. | Design back-end services to handle cross-cutting concerns like rate limiting, failover operations, and AI processing logic. Abstract behavior and intelligence away from the client to future-proof your design and improve maintainability. |
| **Block direct access to data stores**. | Code in AI systems shouldn't directly access your data stores. Route all data requests through an API or similar data access abstraction that enforces authorization and propagates user/tenant context into retrieval and filtering. Pass forward user identity so data level security can be applied. |
| **Abstract your models and tools**. | Use abstraction layers to decouple your application from specific models, tools, and technologies. Implement standardized interfaces and protocols to provide flexibility as technologies evolve, making your design more maintainable and future-proof. |
| **Isolate behaviors and actions**. | Design clear boundaries across client, intelligence (routing/orchestration/agents), knowledge (grounding data), and tools (business APIs) layers. Each layer should enforce its own policies, identities, and caching strategies to reduce blast radius and focus development efforts. |
| **Prioritize off-the-shelf solutions**. | Use software or platform as a service (SaaS or PaaS) to handle workload functions when they meet security, safety, compliance, and quota needs. Implement compensating controls through gateways that enforce authentication, quotas, safety, and logging. Use prebuilt and pre-trained models where possible to minimize the operational and development burden for your workload and operations teams. |

## Distinguish between inferencing and intelligent applications

When designing your application architecture, first determine whether you're building an inferencing-focused application or an intelligence-focused application, as this distinction drives design decisions.

### Inferencing applications

Inferencing applications perform single-step operations like classification, translation, or summarization. These applications have simpler architectures:

**Typical architecture:** Client communicates with an AI gateway which provides auth, quotas, safety, and routing. That gateway  calls into the model serving layer such as Azure AI Foundry, AKS, or managed online endpoints. Where practical, the results may be cached for future inferencing calls before being returned to the client.

Inferencing applications typically feature direct model calls, require minimal orchestration, focus on performance and throughput, and don't require complex state management.

### Intelligent applications

Intelligent applications perform planning, coordination, and multi-step reasoning and are typically handled through agents and agent orchestration. They require more concepts to be addressed in their architectures:

**Typical architecture:** Client invokes an agent or agent orchestrator. Based on the design or autonomous capabilities, this layer will invoke a tools layer such as MCP servers, custom API. The agent might need to call into grounding knowledge services such as a search index, database, or graph. Models might be invoked at multiple points during this process. Caching can occur at multiple levels as well to optimize the process.

Intelligent applications typically feature agentic patterns and model routing with complex workflow coordination, integration with multiple data sources and tools, and require conversation and context management.

## Fundamental AI application design guidelines

When designing AI applications, apply these foundational guidelines to create resilient and maintainable systems:

### Start with business outcomes

Before selecting technologies, clearly define the business problem you're solving. This definition guides technology choices and architecture design. Consider these factors:

- **Success metrics**. Define measurable outcomes that demonstrate value, such as accuracy improvements, cost reductions, or user satisfaction scores.

- **User experience requirements**. Understand how users and processes will interact with AI capabilities and what response times they expect.

- **Regulatory constraints**. Identify compliance requirements that might affect your design choices, such as data residency or explainability requirements.

### Design for observability from day one

AI applications require monitoring beyond traditional application metrics. Build observability into your design from the start:

- **Model performance tracking**. Monitor accuracy drift, inference latency, and prediction confidence scores.

- **Data quality monitoring**. Track input data distribution changes that might affect model performance.

- **User interaction analysis**. Understand how users interact with AI features to identify improvement opportunities.

### Plan for abstraction and future flexibility

Design your system with abstraction layers that allow you to adapt as AI technologies evolve:

- **Model abstraction**. Abstract models behind consistent interfaces to enable swapping or upgrading without application changes.

- **Standardized protocols**. Prefer open, documented interfaces and formats such as OpenAPI for tools, ONNX for model portability, and OpenTelemetry for telemetry.

- **Capability-based design**. Design around capabilities rather than specific technologies to maintain flexibility.

### Externalize prompts and configuration

Treat prompts as configuration that should be externalized following [The Twelve-Factor App](https://12factor.net/config) design principles:

- **Version control**. Keep prompts in version control with clear deployment tracking to correlate telemetry and safety results with specific prompt versions.

- **Role separation**. Enable business analysts and domain experts to tune prompts without code changes.

- **Safe deployment**. Implement controlled rollout processes for prompt changes, treating them as significant configuration updates.

## Plan for model deprecation

Foundation models will eventually reach end-of-life and be retired by your model hosting platform. The models are replaced by models that perform better, are more cost effective, have updated training knowledge, and support new capabilities. Design abstractions that minimize the impact of your workload's future model transitions.

- **Provider abstraction**. Use abstraction layers that allow switching between model providers without application changes.

- **Version management**. Implement versioning strategies that support gradual migration between model versions.

- **Fallback strategies**. Design fallback mechanisms for when preferred models become unavailable.

For architecture design techniques to address model lifecycle concerns, see [Design to support foundation model life cycles](/azure/architecture/ai-ml/guide/manage-foundation-models-lifecycle).

## Containerize components

To ensure that your independently deployable components are fully self-contained and to streamline your deployments, consider containerization as part of your design strategy. The following components should be containerized:

- **Microservices**. Individual microservices that handle specific functions of the application, like data processing, model inference, and user authentication, should be containerized. This approach enables independent deployment and scaling and facilitates more efficient updates and maintenance.

- **AI models**. Containerize AI models to ensure that all dependencies, libraries, and configurations are bundled together. This approach isolates the model environment from the host system to prevent version conflicts and help ensure consistent behavior across different deployment environments.

- **Data processing pipelines**. Any data processing tasks that precede or follow model inference, like data cleaning, transformation, and feature extraction, should be containerized. This approach enhances reproducibility and simplifies the management of dependencies.

- **Infrastructure services**. Services that provide infrastructure support, like databases and caching layers, can also benefit from containerization. Containerizing these services helps maintain version consistency and facilitates easier scaling and management of these components.

## Implement multi-layer caching strategies 

A multi-layer caching approach can help improve performance and reduce costs in your AI applications. Consider implementing caching at multiple levels of your application stack:

- **Result and answer caching**. This allows you to reuse responses for identical or semantically similar queries, if appropriate for the situation. In workloads where responses are cacheable, this can significantly reduce invocations to your model, improving performance and reducing model invocation costs.

- **Retrieval and grounding snippet caching**. Cache frequently retrieved knowledge fragments and grounding data to avoid repeated database and search queries or data API operations.

- **Model output caching**. Cache intermediate model outputs that can be reused across requests.

When designing your caching approach in each layer you'll need to understand what data is frequently accessed and how the application's users' roles and permissions might influence what data they can access. For caching to be effective you must expect a healthy cache hit rate. Focus on caching for high-traffic paths like product catalog searches, while monitoring cache evictions.

- **Cache key components**. Cached values need to be tied to specific runtime factors within your workload. Include values such as tenant/user identity, policy context, model version, and prompt version in cache keys to ensure a cached value is only being returned when appropriate for the request.

- **Time-to-live (TTL) policies**. Set appropriate expiration times based on data freshness requirements and content sensitivity.

- **Invalidation hooks**. Implement cache invalidation triggers for data updates, model changes, and prompt modifications.

- **User privacy protection**. Never cache user-private content unless properly scoped by key and policy. Generally speaking, caching is best implemented for data that would work across multiple users. Focusing on caching for a single user isn't practical in most situations. Avoid caching user-specific responses as a valid response for all users. For example, a cached response for "How many hours of paid time off do I have left?" would only ever be appropriate for the user that requested the data, even if other users present the same query.

> :::image type="icon" source="../_images/risk.svg"::: **Risk.** Caching improves performance and reduces costs but introduces security and data freshness risks. These risks include **data leakage**, **stale data**, and **privacy violations**.

## Evaluate the use of orchestration and agents in generative AI solutions

Choose the appropriate coordination approach based on your application's requirements for determinism and exploratory capabilities.

### When to use orchestration

An orchestrator manages a workflow by coordinating the communication among the different components of the AI solution that would otherwise be difficult to manage in complex workloads. We recommend that you build an orchestrator into your design if your workload needs any of the following characteristics:

- **Predictable workflows**. Multi-step processes with well-defined sequences and decision points.

- **Compliance requirements**. Scenarios where you need to ensure specific steps are executed in order for regulatory compliance.

- **Performance-critical paths**. Workflows where latency and resource usage must be tightly controlled.

- **Simple coordination**. Straightforward task delegation that doesn't require dynamic reasoning.

### When to use agent collaboration

An **agent** is a way to wrap, extract, and define intelligent behavior in your application. Agents provide context-bound functionality and can work with orchestration frameworks like Semantic Kernel, Autogen, or Azure Agent Service.

Use agent-routed collaboration approaches for exploratory or composed tasks:

- **Multi-step reasoning**. Tasks that require planning and decision-making across multiple steps.

- **Tool coordination**. Complex workflows that involve coordinating multiple specialized tools and services.

- **Adaptive behavior**. Scenarios where the system needs to adjust its approach based on intermediate results or changing conditions.

- **Context management**. Applications that need to maintain conversation state and user context across interactions.

#### Agent design considerations

When designing agent-based systems, consider isolation and boundaries, communication patterns, and tool abstraction strategies. Design clear boundaries between different agent capabilities to reduce blast radius and improve testability. Use established patterns like topic-queue systems for agent communication. And, abstract tool capabilities using standardized interfaces to enable agent flexibility.

> [!IMPORTANT]
> Don't automatically add agents between the task to be completed and model calls. Evaluate whether the intelligence being delivered requires the complexity of agent patterns, or if direct model calls are sufficient for your use case. Agent layers add latency and surface area and testing complexity.

### Hybrid approaches

Consider hybrid designs where orchestrators delegate to agents for specific subtasks:

- **Structured workflows with flexible steps**. Use orchestration for the overall workflow while allowing agents to handle complex individual steps.

- **Escalation patterns**. Start with deterministic orchestration and escalate to agent-based reasoning when predetermined logic is insufficient.

- **Domain-specific reasoning**. Use orchestration for cross-domain coordination while employing specialized agents for domain-specific tasks.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff.** Orchestration provides predictability and control but limits adaptability. Agent collaboration enables dynamic problem-solving but introduces variability and complexity.

## Implement AI gateways for policy enforcement

AI gateways are used in your design to provide [gateway offloading](/azure/architecture/patterns/gateway-offloading), [gateway routing](/azure/architecture/patterns/gateway-routing), and sometimes [gateway aggregation](/azure/architecture/patterns/gateway-aggregation) capabilities within your workload. Gateways are typically implemented using platforms like Azure API Management or through more custom-coded solutions, such as an Envoy or NGINX implementation. Gateways can be used to proxy requests to model providers, agent endpoints, tools, and knowledge stores.

### Core AI gateway capabilities

An AI gateway can address cross-cutting concerns in addition to being a layer of abstraction and indirection from the target system. When designing an AI gateway strategy, consider the following capabilities:

- **Protocol normalization**. Standardize interfaces across different providers (OpenAI API, Azure OpenAI, open-source models) to provide consistent client experiences.

- **Authentication and authorization**. Enforce identity-based access control and propagate user context to downstream services.

- **Rate limiting and quotas**. Implement per-user, per-application, or per-tenant rate limits and token quotas to prevent abuse and manage costs.

- **Request and response filtering**. Apply input validation, prompt filtering, and output safety checks at the gateway level.

- **Token and prompt caps**. Enforce maximum token limits and prompt size restrictions to prevent resource exhaustion by preemptively rejecting requests that represent more demand on the system than desired or possible.

- **Model routing policies**. Route requests to appropriate models based on user permissions, request characteristics, or cost optimization goals. Gateways are often used to implement spillover between multiple models, when capacity or pricing is consumed on one model but is available on another model deployment.

- **Header injection and transformation**. Add required headers, user context, and security tokens for downstream services.

### Multi-provider scenarios

AI gateways are especially valuable when using multiple model providers or shared model hosting platforms. Often, you'll see these as centralized resources within your organization that your workload will be encouraged or required to take a dependency on. A sufficiently complex workload might also benefit from building this layer of abstraction within its own design.

- **Unified interface**. Provide a single API surface that abstracts multiple backend providers.

- **Failover and redundancy**. Route requests to alternate providers when primary services are unavailable.

- **Cost optimization**. Route requests to the most cost-effective provider based on request characteristics.

> [!IMPORTANT]
> Don't rely solely on SDK retries and timeouts. Enforce limits, authentication, quotas, and timeouts at the gateway and policy layer for comprehensive control.

## Use AI application design patterns

Several common design patterns have been established in the industry for AI applications. You can use them to simplify your design and implementation. These design patterns include:

- **Model ensembling**. This design pattern involves combining predictions from multiple models to improve accuracy and robustness by mitigating the weaknesses of individual models.

- **Microservices architecture**. Separating components into independently deployable services enhances scalability and maintainability. It enables teams to work on different parts of the application simultaneously.

- **Event-driven architecture**. Using events to trigger actions enables decoupled components and real-time processing to make the system more responsive and adaptable to changing data.

### Grounding and knowledge integration patterns

Many AI applications access external knowledge sources to provide accurate, up-to-date information. Treat retrieval as an agent tool or knowledge call behind a service that enforces authorization.

- **Knowledge routing**. Direct queries to appropriate knowledge sources based on query type, domain, or user context.

- **Context layering**. Build systems that can combine multiple knowledge sources and context types to provide comprehensive responses.

- **Dynamic grounding**. Implement systems that can adapt their knowledge sources and grounding strategies based on changing requirements or data availability.

- **Authorization-aware retrieval**. Ensure that grounding services enforce user permissions and tenant context when retrieving information.

For a deeper understanding of designing a purpose-built vector index that supports your workload-specific grounding data for semantic retrieval, see [Design and develop a Retrieval-Augmented Generation (RAG) solution](/azure/architecture/ai-ml/guide/rag/rag-solution-design-and-evaluation-guide).

### Multi-model routing patterns

Using a model router can improve your workload's availability by routing requests to a healthy model when another model is in an unhealthy state, or it can help improve the quality of responses by selecting the best model for a particular task in real time. Model routers add flexibility but introduces additional variability. Use them selectively:

**When to use model routers:** Consider using a model router when your workload can tolerate added variability and latency, the user experience expects breadth across model types, or you need to balance costs and capabilities across different models.

**When to avoid model routers:** Using a model router is inappropriate in scenarios where the workload needs precise answers optimized for specific tasks, you're using fine-tuned models with narrow SLOs, or when deterministic behavior, including consistent performance, is critical for your use case.

When adding a model router to your application prefer provider-native routing when available and monitor performance impacts and user satisfaction.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff.** Model routers provide flexibility and cost optimization but add non-deterministic behavior and complexity to testing and troubleshooting.

If your workload is a multi-tenant application, see [Guide to design a secure multitenant RAG inferencing solution](/azure/architecture/ai-ml/guide/secure-multitenant-rag) for recommendations on accessing per-tenant grounding data.

### When to use design patterns

Consider using these design patterns in scenarios like these:

- **Complex workflows**. When you have complex workflows or interactions between multiple AI models, patterns like RAG or microservices can help manage complexity and ensure clear communication between components.

- **Scalability requirements**. If the demand on your application fluctuates, a pattern like microservices enables individual components to scale independently to accommodate varying loads without affecting the overall system performance.

- **Data-driven applications**. If your application requires extensive data handling, an event-driven architecture can provide real-time responsiveness and efficient data processing.

> [!NOTE]
> Smaller applications or POCs typically don't benefit from these design patterns. These applications should be designed for simplicity. Likewise, if you have resource (budget, time, or headcount) constraints, using a simple design that can be refactored later is a better approach than adopting a complex design pattern.

## Choose the right frameworks, libraries, and protocols

The choice of frameworks and libraries is closely intertwined with application design. They affect performance, scalability, and maintainability. However, design requirements can limit your framework choices. For example, use of the Semantic Kernel SDK often encourages a microservices-based design where each agent or function is encapsulated within its own service. Consider these factors when you choose frameworks and libraries:

- **Application requirements**. The requirements of the application, like real-time processing or batch processing, might limit the choice of framework. For instance, if the application requires low latency, you might need to use a framework that has asynchronous capabilities.

- **Integration needs**. The design might require specific integrations with other systems or services. If a framework doesn't support the necessary protocols or data formats, you might need to reconsider the design or choose a different framework.

- **Team expertise**. The skill set of the development team can limit framework choices. A design that relies on a less familiar framework might lead to increased development time and complexity, so you might want to use a more familiar tool.

- **Community and support**. Consider the maturity and support ecosystem of the framework. Active communities and comprehensive documentation reduce implementation risks.

- **Performance characteristics**. Evaluate framework performance for your specific use case, including memory usage, startup time, and inference speed.

Adopt standardized tool protocols to improve governance and enable flexibility:

- **MCP-style servers**. Use Model Context Protocol (MCP) or other suitable standard to wrap business capabilities as discoverable tool servers.

- **Use specifications that have broad SDK support**. For example, define tools using OpenAPI specifications for consistent interface documentation and validation.

- **Capability advertisement**. Design tools to advertise their capabilities, allowing orchestrators to discover and route requests appropriately. For example, wrap enterprise resource planning (ERP) read/write operations as a tool server that advertises capabilities. This approach allows you to tune the ERP interactions without changing the agent logic.

### Protocol preferences

When selecting protocols and standards, prefer open, documented interfaces over proprietary formats. There are a lot of emerging protocols in the industry. As you consider your agent-to-agent and control use cases, you'll want to consider technical debt that could come from protocols that are under rapid evolution or deprecation. Layers of abstraction can help keep your design interoperable across different AI frameworks and providers.

## Design a security strategy for your workload's AI components

AI components require security considerations beyond traditional application security. Implement security measures across all application layers while using standard identity providers like Microsoft Entra ID for foundational authentication and authorization.

### Core security principles

Implement these foundational security practices:

- **Standard authentication and authorization**. Use established identity providers and role-based access control (RBAC) systems.

- **Data lineage security with user context propagation**. Ensure that access controls are maintained throughout the data pipeline. Pass forward user identity using on-behalf-of flows so that users can only access results based on data they're authorized to see.

- **Audit trails**. Implement detailed logging of AI interactions for compliance and security monitoring.

### AI-specific security measures

Address security challenges that are unique to AI applications:

- **Prompt filtering and injection prevention**. Implement safeguards against prompt injection attacks that could manipulate AI behavior or extract sensitive information.

- **Safe tool usage controls**. When using agents with tool access, implement controls to prevent harmful actions and validate tool usage before execution.

- **Agent behavior monitoring**. Monitor agent actions and decisions to detect unusual or potentially harmful behavior patterns.

- **Model access control**. Implement fine-grained permissions for different models and capabilities within your AI system.

- **Response filtering and safety checks**. Filter AI outputs to prevent the generation of harmful, inappropriate, or sensitive content.

### Security-trimmed grounding

Go beyond preventing direct database access and implement security-aware knowledge retrieval:

- **Identity propagation**. Pass Microsoft Entra group claims or equivalent identity information into knowledge tool calls so grounding services never retrieve unauthorized data or media.

- **Group and ACL-based trimming**. Implement filtering based on security groups and access control lists at the knowledge layer.

- **Auditable denials**. Log and audit when grounding requests are denied due to insufficient permissions.

- **Authorization enforcement**. Require that code sits between agents and knowledge sources to enforce authorization policies.

**Example**: Pass Microsoft Entra group claims into the knowledge tool call so grounding never retrieves unauthorized documents, ensuring that cached results and AI responses respect user permissions.

## Consider nonfunctional requirements

Your workload might have nonfunctional requirements that pose challenges because of factors that are inherent to AI technologies. Following are some common nonfunctional requirements and their challenges:

- **Latency of model inferencing / timeouts**. AI applications often require real-time or near-real-time responses. Designing for low latency is crucial. It involves optimizing model architecture, data processing pipelines, and hardware resources. Implementing caching strategies and ensuring efficient model loading are also essential to avoid timeouts and provide timely responses.

- **Token or request throughput limitations**. Many AI services impose limits on the number of tokens or the throughput of requests, particularly with cloud-based models. Designing for these limitations requires careful management of input sizes, batching of requests when necessary, and potentially implementing rate-limiting or queuing mechanisms to manage user expectations and prevent service disruptions.

- **Cost and chargeback scenarios**. Designing for cost transparency involves implementing usage tracking and reporting features that facilitate chargeback models. These features enable organizations to allocate costs accurately across departments. Chargeback management is typically handled by an API gateway, like [Azure API Management](https://techcommunity.microsoft.com/blog/appsonazureblog/calculating-chargebacks-for-business-unitsprojects-utilizing-a-shared-azure-open/3909202).

- **Model accuracy and drift**. AI models can degrade over time as data patterns change. Design monitoring systems to detect accuracy drift and implement automated retraining pipelines when necessary.

- **Compliance and explainability**. Some industries require explainable AI decisions. Design your system to capture and provide reasoning for AI-generated outputs when required.

- **Data privacy and residency**. Ensure your design meets data protection requirements, including the ability to delete user data and comply with geographic data residency rules.

## Next step

> [!div class="nextstepaction"]
> [Design area: Application platform](application-platform.md)
