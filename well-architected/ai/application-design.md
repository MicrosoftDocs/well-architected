---
title: Application Design for AI Workloads on Azure
description: Learn about design considerations for creating AI workloads on Azure, including design patterns and architecture considerations.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/18/2024
ms.topic: conceptual
---

# Application design for AI workloads on Azure

There are many choices to consider when you create an application that has AI functions. Your unique functional and nonfunctional requirements, like whether the use case is traditional machine learning, generative, deterministic, or a combination of AI types, help you narrow down high-level decisions about your design. You'll consider these choices as you move from high-level design areas to lower-level design areas.

As discussed in the [Get started](./get-started.md) article, whether to build your own model or use a prebuilt model is one of the first important decisions to make. When you use a prebuilt model, consider these points:

- **Catalog sources**. Explore repositories like the Hugging Face Model Hub, TensorFlow Hub, and the [Azure AI Foundry portal](https://ai.azure.com/) model catalog to find pre-trained models. These platforms provide an extensive catalog of models for various tasks.

- **Licensing**. Make sure the model's licensing terms fit your security, compliance, and application goals, especially if you plan to distribute the application or integrate it with other services.

- **Key components**. Look at the model's architecture, training data, performance, and licensing to determine whether it's fine-tuned for your task or domain.

For guidance on choosing a hosting platform, see [Considerations for the model hosting and inferencing platform](./application-platform.md#considerations-for-the-model-hosting-and-inferencing-platform).

This article describes common design areas and factors to consider when you make decisions about technology and approach.

## Recommendations

The following table summarizes the recommendations provided in this article.

|Recommendation|Description|
|---|---|
| **Prioritize off-the-shelf solutions**. | Whenever practical, use platform as a service (PaaS) solutions to handle workload functions. Use prebuilt and pre-trained models where possible to minimize the operational and development burden for your workload and operations teams. |
| **Abstract functions and capabilities away from the client**. | Keep the client as thin as possible by designing back-end services to handle cross-cutting concerns like rate limiting and failover operations. |
| **Block access to the data stores**. | Code in the AI system shouldn't directly access your data stores. Route all data requests through an API layer. The APIs should be built specifically for the required task. |
| **Isolate your models**. | As with the data stores, use an API layer to act as a gateway for requests to the model. Some PaaS solutions like Azure OpenAI Service and Azure Machine Learning use SDKs for this purpose. Many tools, like prompt flow, contain native support to propagate APIs to the service. |
| **Design components to be independently deployable**. | AI models, data pipelines, front-end components, and microservices like data preprocessing, feature extraction, and inferencing should be independently deployable to optimize the flexibility, scalability, and operability of your workload. |

## Containerize components

To ensure that your independently deployable components are fully self-contained and to streamline your deployments, consider containerization as part of your design strategy. The following components should be containerized:

- **Microservices**. Individual microservices that handle specific functions of the application, like data processing, model inference, and user authentication, should be containerized. This approach enables independent deployment and scaling and facilitates more efficient updates and maintenance.

- **AI models**. Containerize AI models to ensure that all dependencies, libraries, and configurations are bundled together. This approach isolates the model environment from the host system to prevent version conflicts and help ensure consistent behavior across different deployment environments.

- **Data processing pipelines**. Any data processing tasks that precede or follow model inference, like data cleaning, transformation, and feature extraction, should be containerized. This approach enhances reproducibility and simplifies the management of dependencies.

- **Infrastructure services**. Services that provide infrastructure support, like databases and caching layers, can also benefit from containerization. Containerizing these services helps maintain version consistency and facilitates easier scaling and management of these components.

## Colocate AI components with other workload components

There are several good reasons to colocate your AI components with other workload components, but there are tradeoffs associated with doing so. You might choose to colocate components for these reasons:

- **Latency sensitivity**. Colocate AI components with other services, like API hosting, when low latency is important. For example, if you need real-time inference to enhance the user experience, placing AI models close to the API can minimize the time it takes to retrieve results.

- **Data proximity**. When AI models require frequent access to specific datasets, such as a search index, colocating these components can improve performance and reduce the overhead of data transfer to speed up processing and inference.

- **Resource utilization**. If specific components have complementary resource needs, like CPU and memory, colocating them can optimize resource usage. For example, a model that requires significant computation can share resources with a service that has lower demands at the same time.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff.** There are tradeoffs to consider when you decide whether to colocate components. You might lose the ability to independently deploy or scale components. You might also increase the risk of malfunction by increasing the potential blast radius of incidents.

## Evaluate the use of orchestrators in generative AI solutions

An orchestrator manages a workflow by coordinating the communication among the different components of the AI solution that would otherwise be difficult to manage in complex workloads. We recommend that you build an orchestrator into your design if your workload has any of the following characteristics:

- **Complex workflows**. The workflow involves multiple steps, like preprocessing, model chaining, or postprocessing.

- **Conditional logic**. Decisions, like routing results to different models, need to be made dynamically based on model outputs.

- **Scaling and resource management**. You need to manage resource allocation for high-volume applications by using model scaling that's based on demand.

- **State management**. You need to manage the state and the memory of user interactions.

- **Data retrieval**. You need to be able to retrieve augmentation data from the index.

### Considerations for using multiple models

When your workload uses multiple models, an orchestrator is essential. The orchestrator routes data and requests to the appropriate model based on the use case. Plan for data flow between models, ensuring that outputs from one model can serve as inputs for another. Planning might involve data transformation or enrichment processes.

### Orchestration and agents

For generative AI workloads, consider taking an agent-based, sometimes referred to as *agentic*, approach to your design to add extensibility to your orchestration. Agents provide context-bound functionality. They share many characteristics with microservices and perform tasks in conjunction with an orchestrator. The orchestrator can advertise tasks to a pool of agents, or agents can register capabilities with the orchestrator. Both approaches allow the orchestrator to dynamically determine how to break up and route the query among the agents.

Agentic approaches are ideal when you have a common UI surface that has multiple, evolving features that can be plugged into the experience to add more skills and grounding data to the flow over time.

For complex workloads that have many agents, it's more efficient to allow agents to dynamically collaborate rather than using an orchestrator to break up tasks and assign them.

Communication between the orchestrator and agents should follow a topic-queue pattern, where agents are subscribers to a topic and the orchestrator sends out tasks via a queue.

Using an agentic approach works best with an orchestration pattern rather than a [choreography pattern](/azure/architecture/patterns/choreography).

For more information, see [Considerations for the orchestration platform](./application-platform.md#considerations-for-the-orchestration-platform).

## Evaluate the use of API gateways

API gateways like [Azure API Management](/azure/api-management/api-management-key-concepts) abstract functions away from APIs, which decouples dependencies between the requesting service and the API. API gateways provide the following benefits to AI workloads:

- **Multiple microservices**. Gateways help you manage multiple AI model endpoints when you need to enforce consistent policies, like rate limiting and authentication.

- **Traffic management**. Gateways help you manage high-traffic apps efficiently by managing requests, caching responses, and distributing loads.

- **Security**. Gateways provide centralized access control, logging, and threat protection for the APIs behind the gateway.

## Use AI application design patterns

Several common design patterns have been established in the industry for AI applications. You can use them to simplify your design and implementation. These design patterns include:

- **Model ensembling**. This design pattern involves combining predictions from multiple models to improve accuracy and robustness by mitigating the weaknesses of individual models.

- **Microservices architecture**. Separating components into independently deployable services enhances scalability and maintainability. It enables teams to work on different parts of the application simultaneously.

- **Event-driven architecture**. Using events to trigger actions enables decoupled components and real-time processing to make the system more responsive and adaptable to changing data.

### RAG pattern and chunking strategies

The Retrieval-Augmented Generation (RAG) pattern combines generative models with retrieval systems, which enables the model to access external knowledge sources for improved context and accuracy. See the [Design and develop a RAG solution](/azure/architecture/ai-ml/guide/rag/rag-solution-design-and-evaluation-guide) series of articles for in-depth guidance on this pattern. There are two RAG approaches:

- **Just-in-time**. This approach retrieves relevant information dynamically at the time of a request to ensure that the latest data is always used. It's beneficial in scenarios that require real-time context, but it might introduce latency.

- **Pre-calculated (cached)**. This method involves caching retrieval results to reduce response times by serving pre-computed data. It's suitable for high-demand scenarios where consistent data can be stored. The data might not reflect the most current information, which could lead to relevance issues.

When you use a RAG pattern, a well-defined chunking strategy is critical to optimizing your workload's performance efficiency. Start with the [guidance](/azure/architecture/ai-ml/guide/rag/rag-chunking-phase) provided in the [Design and develop a RAG solution](/azure/architecture/ai-ml/guide/rag/rag-solution-design-and-evaluation-guide) series. Here are some additional recommendations to consider:

- Implement a dynamic chunking strategy that adjusts chunk sizes based on data type, query complexity, and user requirements. Doing so can enhance retrieval efficiency and context preservation.

- Incorporate feedback loops to refine chunking strategies based on performance data.

- Preserve data lineage for chunks by maintaining metadata and unique identifiers that link back to the grounding source. Clear lineage documentation helps to ensure that users understand the data origin, its transformations, and how it contributes to the output.

### When to use design patterns

Consider using these design patterns when your use case meets the condition that's described:

- **Complex workflows**. When you have complex workflows or interactions between multiple AI models, patterns like RAG or microservices can help manage complexity and ensure clear communication between components.

- **Scalability requirements**. If the demand on your application fluctuates, a pattern like microservices enables individual components to scale independently to accommodate varying loads without affecting the overall system performance.

- **Data-driven applications**. If your application requires extensive data handling, an event-driven architecture can provide real-time responsiveness and efficient data processing.

> [!NOTE]
> Smaller applications or POCs typically don't benefit from these design patterns. These applications should be designed for simplicity. Likewise, if you have resource (budget, time, or headcount) constraints, using a simple design that can be refactored later is a better approach than adopting a complex design pattern.

## Choose the right frameworks and libraries

The choice of frameworks and libraries is closely intertwined with application design. They affect performance, scalability, and maintainability. However, design requirements can limit your framework choices. For example, use of the Semantic Kernel SDK often encourages a microservices-based design where each agent or function is encapsulated within its own service. Consider these factors when you choose frameworks and libraries:

- **Application requirements**. The requirements of the application, like real-time processing or batch processing, might limit the choice of framework. For instance, if the application requires low latency, you might need to use a framework that has asynchronous capabilities.

- **Integration needs**. The design might require specific integrations with other systems or services. If a framework doesn't support the necessary protocols or data formats, you might need to reconsider the design or choose a different framework.

- **Team expertise**. The skill set of the development team can limit framework choices. A design that relies on a less familiar framework might lead to increased development time and complexity, so you might want to use a more familiar tool.

## Design a strategy for identities, authorization, and access

Generally speaking, you should approach identity, authorization, and access in the same way that you would when you normally design applications. You should use an identity provider, like Microsoft Entra ID, to manage these areas as much as possible. However, many AI applications have unique challenges that necessitate special consideration. It's sometimes challenging or even impossible to persist access control lists (ACLs) through the system without new development.

See [Guide to design a secure multitenant RAG inferencing solution](/azure/architecture/ai-ml/guide/secure-multitenant-rag) to learn how to add security-trimming metadata to documents and chunks. This trimming can be based on security groups or similar organizational constructs.

## Consider nonfunctional requirements

Your workload might have nonfunctional requirements that pose challenges because of factors that are inherent to AI technologies. Following are some common nonfunctional requirements and their challenges:

- **Latency of model inferencing / timeouts**. AI applications often require real-time or near-real-time responses. Designing for low latency is crucial. It involves optimizing model architecture, data processing pipelines, and hardware resources. Implementing caching strategies and ensuring efficient model loading are also essential to avoid timeouts and provide timely responses.

- **Token or request throughput limitations**. Many AI services impose limits on the number of tokens or the throughput of requests, particularly with cloud-based models. Designing for these limitations requires careful management of input sizes, batching of requests when necessary, and potentially implementing rate-limiting or queuing mechanisms to manage user expectations and prevent service disruptions.

- **Cost and chargeback scenarios**. Designing for cost transparency involves implementing usage tracking and reporting features that facilitate chargeback models. These features enable organizations to allocate costs accurately across departments. Chargeback management is typically handled by an API gateway, like [Azure API Management](https://techcommunity.microsoft.com/blog/appsonazureblog/calculating-chargebacks-for-business-unitsprojects-utilizing-a-shared-azure-open/3909202).

## Next step

> [!div class="nextstepaction"]
> [Design area: Application platform](application-platform.md)
