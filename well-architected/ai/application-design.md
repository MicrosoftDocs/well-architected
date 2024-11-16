---
title: Application Design for AI Workloads on Azure
description: Learn about the design considerations for building AI workloads on Azure, including recommendations, design patterns, architecture considerations, and specialized application layers.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 04/15/2024
ms.topic: conceptual
ms.service: azure-waf
ms.subservice: waf-workload-ai
---

# Application design for AI workloads on Azure

There are many choices available for you to consider when planning to build an application with AI functions. Your unique functional and nonfunctional requirements will help you narrow down high-level decisions about your design, like whether the use case is traditional machine learning, generative, or deterministic, or a combination of AI types. As you move from the high-level design areas to lower level design areas, you'll have several choices to consider along the way. 

As discussed in the [Get started](./get-started.md) article, choosing whether to build your own model or use a prebuit model is one of the first important decisions to make. When choosing a prebuilt model, consider these points.

- **Catalog sources**. Explore repositories like Hugging Face Model Hub, TensorFlow Hub, or Azure AI Studio model catalog to find pretrained models. These platforms provide an extensive catalog of models across various tasks.
 
- **Licensing**. Make sure the model's licensing terms fit your security, compliance, and application goals, especially if you plan to distribute the application or integrate it with other services.
 
- **Key components**. Look at the model's architecture, training data, performance, and licensing. See if it's fine-tuned for your task or domain.
 
For guidance on choosing a hosting platform, see [Model hosting platform considerations](./application-platform.md#considerations-for-the-model-hosting-and-inferencing-platform).

This article explores many common design areas and covers factors to consider when making important decisions about technology or approach choices.

## Recommendations

Here's the summary of recommendations provided in this article.

|Recommendation|Description|
|---|---|
| **Prioritize off-the-shelf solutions**. | Whenever practical use platform as a service (PaaS) solutions to handle much of the workload functions. Likewise, use prebuilt and pre-trained models whenever practical to minimize the operational and development burden for your workload and operations teams. |
| **Abstract functions and capabilities away from the client**. | Keep the client as thin as possible by designing the backend services to handle cross-cutting concerns like rate limiting and failover operations. |
| **Block access to the data stores**. | No code in the AI system should directly touch your data stores. Route all data requests through an API layer. The APIs should be purpose built for the specific task required. |
| **Isolate your models**. | Like the data stores, use an API layer to act as a gateway for requests to the model. Some PaaS solutions like Azure Open AI and Azure ML use SDKs for this purpose and there is native support in many tools, like PromptFlow to propagate APIs through to the service. |
| **Design componenets to be independently deployabe**. | AI models, data pipelines, frontend components, and microservices like data preprocessing, feature extraction, and inferencing should be independently deployable to optimize the flexibility, scalability and operability of your workload. |

## Containerize components

To ensure that your independently deployable components are fully self-contained and to streamline your deployments, consider containerization as part of your design strategy. The following components should be containerized:

- **Microservices:** Individual microservices that handle specific functions of the application, such as data processing, model inference, or user authentication, should be containerized. This allows for independent deployment and scaling, facilitating more efficient updates and maintenance.

- **AI models:** AI models can be containerized to ensure that all dependencies, libraries, and configurations are bundled together. This isolates the model environment from the host system, preventing version conflicts and ensuring consistent behavior across different deployment environments.

- **Data processing pipelines:** Any data processing tasks that precede or follow model inference, such as data cleaning, transformation, and feature extraction, should be containerized. This approach enhances reproducibility and simplifies the management of dependencies.

- **Infrastructure services:** Services that provide infrastructure support, like databases or caching layers, can also benefit from containerization. This helps in maintaining version consistency and facilitates easier scaling and management of these components.

## Co-locate AI components with other workload components

There are several good reasons to colocate your AI components with other workload components, but there are tradeoffs with doing so. Reasons that you might colocate are:

- **Latency sensitivity:** Co-locate AI components with other services (like API hosting) when low latency is crucial. For example, if real-time inference is required to enhance user experience, placing AI models close to the API can minimize the time it takes to retrieve results. 

- **Data proximity:** When AI models require frequent access to specific datasets (such as a search index), co-locating these components can improve performance. It reduces the overhead of data transfer, allowing for faster processing and inference. 

- **Resource utilization:** If certain components have complementary resource needs (like CPU and memory), co-locating them can optimize resource usage. For example, a model that requires significant computation can share resources with a service that has lower demands at the same time. 

> [!NOTE]
> There are tradeoffs with colocating components that should be considered. You may lose the ability to independently deploy or scale components and you may increase your risk of malfunction by increasing the potential blast radius of incidents.

## Evaluate the use of orchestrators in generative AI solutions

An orchestrator manages the workflow coordinating the communication between the different solution components of the AI solution that would otherwise be difficult to manage in complex workloads, so building them into your design is highly recommended if your workload has these characteristics: 

- *Complex workflows:* The workflow involves multiple steps, such as preprocessing, model chaining, or postprocessing. 

- *Conditional logic:* Decisions need to be made dynamically based on model outputs, like routing results to different models. 

- *Scaling and resource management:* You need to manage resource allocation for high-volume applications with model scaling based on demand.

- *State management:* You need to manage the state and the memory of user interactions.

- *Data retrieval:* You need to be able to retrieve augementation data from the index.

### Special considerations for using multiple models

When your workload uses multiple models, using an orchestrator is essential. The orchestrator will be responsible for routing data and requests to the appropriate model based on the use case. Plan for data flow between models, ensuring that outputs from one model can serve as inputs for another. This might involve data transformation or enrichment processes.

### Orchestration and agents

For generative AI workloads, consider taking an agent-based (sometimes referred to as "agentic") approach to your design to add extensibility to your orchestration. Agents refer to context-bound functionality, sharing many microservices style characteristics that perform tasks in conjunction with an orchestrator. The orchestrator can advertise tasks out to a pool of agents or agents can register capabilities with the orchestrator. Both allow the orchestrator to dynamically decide how to break up and route up the query amongst the agents.

Agentic approaches are ideal when you have a common UI surface but with multiple, evolving features that can be "plugged into" that experience to add more skills and grounding data to the flow over time.

For complex workloads with many agents, it is more efficient to allow agents to dynamically collaborate than using the orchestrator to break up tasks and assign them.

Communication between the orchestrator and agents should follow a topic-queue pattern, where agents are subscribers to a topic and the orchestrator sends out tasks via a queue.

Using an agentic approach works best with an orchestration pattern rather than an [choreography pattern](/azure/architecture/patterns/choreography).

See the [orchestration platform considerations](./application-platform.md#considerations-for-the-orchestration-platform) for guidance on choosing an orchestration platform.

## Evaluate the use of API gateways

API gateways, like [Azure API Management](/azure/api-management/api-management-key-concepts), abstract functions away from APIs which decouples dependencies between the requesting service and the API. API gateways provide the following benefits to AI workloads:

- *Multiple microservices:* They help you manage multiple AI model endpoints and you need to enforce consistent policies, like rate limiting and authentication. 

- *Traffic management:* They help you manage high-traffic apps efficiently by managing requests, caching responses, and distributing load. 

- *Security:* They provide centralized access control, logging, and threat protection for the APIs behind the gateway. 

## Take advantage of AI application design patterns

There are several common design patterns that have been established in the industry for AI applications that you can use to simplify your design and implementation. These design patterns include:

- **Model ensembling:** This design pattern involves combining predictions from multiple models to improve accuracy and robustness, mitigating the weaknesses of individual models.

- **Microservices architecture:** Separating components into independently deployable services enhances scalability and maintainability, allowing teams to work on different parts of the application simultaneously.

- **Event-driven architecture:** Utilizing events to trigger actions allows for decoupled components and real-time processing, making the system more responsive and adaptable to changing data.

### RAG pattern and chunking strategies

The Retrieval-Augmented Generation (RAG) pattern combines generative models with retrieval systems, enabling the model to access external knowledge sources for improved context and accuracy. See the [Designing and developing a RAG solution](/azure/architecture/ai-ml/guide/rag/rag-solution-design-and-evaluation-guide) series for in-depth guidance on this pattern. There are two RAG approaches:
    
- *Just-in-time:* This approach retrieves relevant information dynamically at the time of a request, ensuring the latest data is always used. It's beneficial in scenarios requiring real-time context, but may introduce latency. 
    
- *Pre-calculated (cached):* This method involves caching retrieval results, reducing response times by serving pre-computed data. It's suitable for high-demand scenarios where consistent data can be stored, but may not reflect the most current information, leading to potential relevance issues. 

When using a RAG pattern, a well-defined chunking strategy is critical to optimizing your workload's performance efficiency. Start with the [guidance](/azure/architecture/ai-ml/guide/rag/rag-chunking-phase) provided in the [Designing and developing a RAG solution](/azure/architecture/ai-ml/guide/rag/rag-solution-design-and-evaluation-guide) series. Additional recommendations to consider are:

- Implement a dynamic chunking strategy that adjusts chunk sizes based on the data type, query complexity, and user requirements. This can enhance retrieval efficiency and context preservation.

- Incorporate feedback loops to refine chunking strategies based on performance data.

- Preserve data lineage for chunks by maintaining metadata and unique identifiers that link back to the grounding source. Clear lineage documentation ensures users understand the data origin, its transformations, and how it contributes to the output.

**Consider using one of these design patterns when your use case meets one of these conditions:**

- *Complex workflows:* When dealing with complex workflows or interactions between multiple AI models, patterns like RAG or microservices can help manage complexity and ensure clear communication between components.

- *Scalability requirements:* If the demand on your application may fluctuate, using a patterns like microservices allows individual components to scale independently, accommodating varying loads without impacting overall system performance.

- *Data-driven applications:* If your application require extensive data handling, an event-driven architecture can provide real-time responsiveness and efficient data processing. 

> [!NOTE]
> Smaller applications or POCs typically will not benefit from adopting one of these design patterns and should be built with a simplistic design. Likewise, if you have resource (budget, time, or headcount) constraints, staying with a simplistic design that can be refactored later is a better approach than adopting a complex design pattern.

## Choose the right frameworks and libraries

The choice of frameworks and libraries is closely intertwined with application design, impacting not just the architecture but also performance, scalability, and maintainability. Conversely, design requirements can limit framework choices, creating a dynamic interplay between the two. For example, using the Semantic Kernel SDK (SK) often encourages a microservices-based design where each agent or functionality is encapsulated within its own service. Factors to consider when choosing frameworks and libraries are:

- **Application requirements:** The specific requirements of the application (like real-time processing, batch processing, and others) may limit the choice of frameworks. For instance, if the application demands low latency, a framework with asynchronous capabilities may be necessary. 

- **Integration needs:** The design may require specific integrations with other systems or services. If a framework doesn’t support the necessary protocols or data formats, it could necessitate reconsidering the design or selecting a different framework. 

- **Team expertise:** The skillset of the development team can constrain framework choices. A design that relies on a less familiar framework might lead to increased development time and complexity, prompting a selection of a more familiar tool.

## Design a strategy for identities, authorization, and access

Generally speaking, you should approach identity, authorization, and access in the same way that you normally design applications. You should use an identity provider, like Microsoft Entra, to manage these areas as much as possible. There are unique challenges to many AI applications that need special consideration however. Persisting access control lists (ACLs) through the system is sometimes challenging or even impossible without introducing novel development. Review the guidance found in the [secure multi-tenant RAG solution](/azure/architecture/ai-ml/guide/secure-multitenant-rag) to learn how to add security trimming metadata to documents and chunks. This trimming can be based on security groups or similar organizational constructs.

## Consider the nonfunctional requirements

You may have nonfunctional requirements for your workload that are challenging due to factors inherent to AI technologies. Common nonfunctional requirements and their challenges include:

- **Latency of model inferencing/timeouts:** AI applications often require real-time or near-real-time responses. Designing for low latency is crucial, which involves optimizing model architecture, data processing pipelines, and hardware resources. Implementing caching strategies and ensuring efficient model loading are also essential to avoid timeouts and provide timely responses. 

- **Token or request throughput limitations:** Many AI services impose limits on the number of tokens or the throughput of requests, particularly when using cloud-based models. Designing for these limitations requires careful management of input sizes, batching requests when necessary, and potentially implementing rate limiting or queuing mechanisms to manage user expectations and prevent service disruptions. 

- **Cost and chargeback scenarios:** Designing for cost transparency involves implementing usage tracking and reporting features that facilitate chargeback models, allowing organizations to allocate costs accurately across departments. Chargeback management is normally handled by an API gateway, like [Azure API Management](https://techcommunity.microsoft.com/blog/appsonazureblog/calculating-chargebacks-for-business-unitsprojects-utilizing-a-shared-azure-open/3909202)

## Next steps

> [!div class="nextstepaction"]
> [Application platform](application-platform.md)
