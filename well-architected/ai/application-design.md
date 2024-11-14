---
title: Application design for AI workloads on Azure
description: Code deployed on the resources.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 04/15/2024
ms.topic: conceptual
ms.service: azure-waf
ms.subservice: waf-workload-ai
---

# Application design for AI workloads on Azure

There are many choices available for you to consider when planning to build an application with AI functions. Your unique functional and nonfunctional requirements will help you narrow down high-level decisions about your design, like whether the use case is traditional machine learning, generative, or deterministic, or a combination of AI types. As you move from the high-level design areas to lower level design areas, you'll have several choices to consider along the way. This article explores many common design areas and covers factors to consider when making important decisions about technology or approach choices.

## Recommendations

|Recommendation|Description|
|---|---|
| Prefer off-the-shelf solutions. | Whenever practical use platform as a service (PaaS) solutions to handle much of the workload functions. Likewise, use prebuilt and pre-trained models whenever practical to minimize the operational and development burden for your workload and operations teams. |
| Abstract functions and capabilities away from the client. | Keep the client as thin as possible by designing the backend services to handle cross-cutting concerns like rate limiting and failover operations. |
| Block access to the data stores. | No code in the AI system should directly touch your data stores. Route all data requests through an API layer. The APIs should be purpose built for the specific task required. |
| Isolate your models. | Like the data stores, use an API layer to act as a gateway for requests to the model. Some PaaS solutions like Azure Open AI and Azure ML use SDKs for this purpose and there is native support in many tools, like PromptFlow to propagate APIs through to the service. |

## Considerations for model selection

Defining your strategy for your model is one of the first critical steps in designing you AI workload. You'll decide what type of model to use and whether to build, train, and maintain your own model, host a prebuilt model, or use a fully managed solution like [Azure Document Intelligence](/azure/ai-services/document-intelligence/overview).

### Using a generative vs a discriminative model

Determine whether to use generative models (like GPT for text generation) or discriminative models (like logistic regression for classification). Generative models create new data samples, while discriminative models classify existing samples based on learned features. Consider the specific task (classification, regression, etc.) and choose models that are well-suited for that purpose. For example, a language model capable of classification might provide more versatility compared to a strictly classification model.

### Building your own model vs using a prebuilt model vs using a fully managed service

Building, training, and maintaining your own model requires a significant investment in costs, time, effort, and expertise, so thoroughly research your options before choosing to go down that path. In most cases, using a prebuilt model or using a fully managed service is the better choice. The following factors can help you decide which of these options is the right one for your use case.

- **Data ownership and sensitivity:** When sensitive data or specific IP needs protection, building and maintaining your own model can provide more control over data handling. For general tasks, hosted or service-based models can be easier to use. 

- **Customization requirements:** If the application requires unique or highly customized model behavior, a custom-built model may be the best choice. Prebuilt models or AI services may lack the flexibility needed. 

- **Cost and maintenance:** Hosting and training a custom model requires ongoing maintenance, computational power, and potential updates. For budget-conscious projects, prebuilt or third-party services often offer a lower upfront investment and reduce infrastructure burdens. 

- **Performance needs:** Third-party services can offer optimized infrastructure and scalability. If an AI application requires low-latency responses or high scalability, a prebuilt model hosted by a specialized service might be ideal. 

- **Expertise availability:** Building and training models require a skilled team for data science, ML engineering, and deployment. If this expertise is limited, using prebuilt or service-based models can expedite deployment and reduce the learning curve. 

When choosing a prebuilt model, consider the following recommendations:

- *Catalog sources:* Explore repositories like Hugging Face Model Hub or TensorFlow Hub to find pretrained models. These platforms provide extensive catalogs of models across various tasks.

- *Licensing concerns:* Ensure that the model's licensing terms align with your security and compliance standards and your application goals, especially if you plan to distribute the application or integrate it with other services. 

- *Key components to consider:* Look for the model architecture, training data, performance benchmarks, and licensing information. Assess whether the model has been fine-tuned for your specific task or domain. 

## AI application design patterns

There are several common design patterns that have been established in the industry for AI applications that you can use to simplify your design and implementation. These design patterns include:

- **Retrieval-Augmented Generation (RAG):** This pattern combines generative models with retrieval systems, enabling the model to access external knowledge sources for improved context and accuracy. See the [Designing and developing a RAG solution](azure/architecture/ai-ml/guide/rag/rag-solution-design-and-evaluation-guide) series for in-depth guidance on this pattern. There are two RAG approaches:
    
    - *Just-in-time:* This approach retrieves relevant information dynamically at the time of a request, ensuring the latest data is always used. It's beneficial in scenarios requiring real-time context, but may introduce latency. 
    
    - *Pre-calculated (cached):* This method involves caching retrieval results, reducing response times by serving pre-computed data. It's suitable for high-demand scenarios where consistent data can be stored, but may not reflect the most current information, leading to potential relevance issues. 

- **Model ensembling:** This design pattern involves combining predictions from multiple models to improve accuracy and robustness, mitigating the weaknesses of individual models.

- **Microservices architecture:** Separating components into independently deployable services enhances scalability and maintainability, allowing teams to work on different parts of the application simultaneously.

- **Event-driven architecture:** Utilizing events to trigger actions allows for decoupled components and real-time processing, making the system more responsive and adaptable to changing data.

- **Layered architecture:** Organizing components into layers (presentation, business logic, and data access, for example) promotes separation of concerns and easier maintenance.

Consider using one of these design patterns when your use case meets one of these conditions:

- *Complex workflows:* When dealing with complex workflows or interactions between multiple AI models, patterns like RAG or microservices can help manage complexity and ensure clear communication between components.

- *Scalability requirements:* If the demand on your application may fluctuate, using a patterns like microservices allows individual components to scale independently, accommodating varying loads without impacting overall system performance.

- *Data-driven applications:* If your application require extensive data handling, an event-driven architecture can provide real-time responsiveness and efficient data processing. 

Smaller applications or POCs typically will not benefit from adopting one of these design patterns and should be built with a simplistic design. Likewise, if you have resource (budget, time, or headcount) constraints, staying with a simplistic design that can be refactored later is a better approach than adopting a complex design pattern.

## Architecture design considerations

As a general rule of thumb, approach your design with the same philosophy as other modern applications. Avoid tight coupling of components to the extent practical and abstract functions away from critical components to optimize reliability and security. 
When considering the number of application layers, strive for using only as many layers as necessary for a viable product. Start with one layer, and add in layers when the complexity requires specialized tasks, like routing, transformation, and authentication.

### Independently deployable components

Designing components to be independently deployable enhances the flexibility, scalability, and maintainability of your workload. Key components that should be independently deployable include: 

- **AI models:** Each AI model (classification models, regression models, or recommendation systems) should be treated as a separate deployable unit. This allows for model updates, rollback, and versioning without impacting other components of the system.

- **Microservices:** Any microservices that handle distinct functionalities, such as data preprocessing, feature extraction, or inferencing, should be independently deployable. This modularity supports continuous integration and deployment (CI/CD) practices, allowing teams to work on different services simultaneously without causing disruption.

- **Data pipelines:** The components of data pipelines (ingestion, transformation, loading) should also be independently deployable. This separation enables you to make changes or optimize specific parts of the pipeline without requiring a full redeployment of the entire pipeline.

- **User interfaces (UIs):** If your application has a frontend component, ensure that it can be deployed independently. This enables UI updates and enhancements without requiring backend changes, leading to faster iteration cycles. 

### Containerization of components

To ensure that your independently deployable components are fully self-contained and to streamline your deployments, consider containerization as part of your design strategy. Benefits of containerization include:

- **Version pinning:** Containerization allows you to specify and pin the exact versions of libraries and dependencies, ensuring that the application behaves consistently across different environments (development, staging, production). 

- **Isolation:** Containers provide an isolated environment for each component, reducing the risk of conflicts and dependencies affecting other parts of the application. 

- **Scalability:** Containerized applications can be easily scaled up or down based on demand, enabling efficient resource utilization. 

- **Portability:** Containers encapsulate all necessary components, making it easier to deploy applications across various environments, whether on-premises or in the cloud. 

- **Simplified deployment**: Containers streamline the deployment process, enabling automated deployment pipelines and reducing manual intervention during updates or scaling operations. 

The following components should be containerized:

- **Microservices:** Individual microservices that handle specific functions of the application, such as data processing, model inference, or user authentication, should be containerized. This allows for independent deployment and scaling, facilitating more efficient updates and maintenance.

- **AI models:** AI models can be containerized to ensure that all dependencies, libraries, and configurations are bundled together. This isolates the model environment from the host system, preventing version conflicts and ensuring consistent behavior across different deployment environments.

- **Data processing pipelines:** Any data processing tasks that precede or follow model inference, such as data cleaning, transformation, and feature extraction, should be containerized. This approach enhances reproducibility and simplifies the management of dependencies.

- **Infrastructure services:** Services that provide infrastructure support, like databases or caching layers, can also benefit from containerization. This helps in maintaining version consistency and facilitates easier scaling and management of these components.

### Colocating AI components with other workload components

There are several good reasons to colocate your AI components with other workload components, but there are tradeoffs with doing so. Reasons that you might colocate are:

- **Latency sensitivity:** Co-locate AI components with other services (like API hosting) when low latency is crucial. For example, if real-time inference is required to enhance user experience, placing AI models close to the API can minimize the time it takes to retrieve results. 

- **Data proximity:** When AI models require frequent access to specific datasets (such as a search index), co-locating these components can improve performance. It reduces the overhead of data transfer, allowing for faster processing and inference. 

- **Resource utilization:** If certain components have complementary resource needs (e.g., CPU, memory), co-locating them can optimize resource usage. For example, a model that requires significant computation can share resources with a service that has lower demands at the same time. 

- **Development and maintenance simplicity:** Co-locating components can simplify deployment and management. By having related services in the same environment, you can streamline updates and ensure consistency in versioning and dependencies. 

- **Service dependencies:** When there are tight dependencies between components, such as an AI model that relies on real-time data from a search index, co-locating these can reduce the risk of issues arising from network latency or data synchronization problems. 

Tradeoffs to consider include:

- **Scalability:** If different components have significantly different scaling needs, it may be better to isolate them. This prevents one component from affecting the performance of another during load spikes. 

- **Fault isolation:** Keeping components isolated can enhance fault tolerance. If one service experiences issues, it won't impact others if they are not co-located. 

- **Deployment independence:** Different components might have different deployment cycles. Keeping them isolated allows teams to update or scale services independently without affecting others. 

## Considerations for nonfunctional requirements

You may have nonfunctional requirements for your workload that are challenging due to factors inherent to AI technologies. Common nonfunctional requirements and their challenges include:

- **Latency of model inferencing/timeouts:** AI applications often require real-time or near-real-time responses. Designing for low latency is crucial, which involves optimizing model architecture, data processing pipelines, and hardware resources. Implementing caching strategies and ensuring efficient model loading are also essential to avoid timeouts and provide timely responses. 

- **Token or request throughput limitations:** Many AI services impose limits on the number of tokens or the throughput of requests, particularly when using cloud-based models. Designing for these limitations requires careful management of input sizes, batching requests when necessary, and potentially implementing rate limiting or queuing mechanisms to manage user expectations and prevent service disruptions. 

- **Telemetry:** Effective monitoring and telemetry are vital for understanding application performance and user interactions. Implementing robust logging and monitoring solutions helps capture relevant metrics and errors, enabling real-time insights into the system's health. This data is crucial for identifying bottlenecks, debugging issues, and optimizing performance. 

- **Cost and chargeback scenarios:** AI applications can incur significant costs, particularly when leveraging cloud resources or third-party APIs. Designing for cost transparency involves implementing usage tracking and reporting features that facilitate chargeback models, allowing organizations to allocate costs accurately across departments. Careful planning of resource allocation and scaling strategies can help manage expenses effectively while still meeting application demands.

## Considerations for specialized application layers

### Using orchestrators

Orchestrators like prompt flow streamline AI workflows that would otherwise be difficult to manage in complex workloads, so building them into your design is highly recommended if your workload has these characteristics:

- *Complex workflows:* The workflow involves multiple steps, such as preprocessing, model chaining, or postprocessing. 

- *Conditional logic:* Decisions need to be made dynamically based on model outputs, like routing results to different models. 

- *Scaling and resource management:* You need to manage resource allocation for high-volume applications with model scaling based on demand.

#### Special considerations for using multiple models

When your workload uses multiple models, using an orchestrator is essential. The orchestrator will be responsible for routing data and requests to the appropriate model based on the use case. Plan for data flow between models, ensuring that outputs from one model can serve as inputs for another. This might involve data transformation or enrichment processes.

#### Orchestration and agents

For generative AI workloads, consider taking an agent-based (sometimes referred to as "agentic") approach to your design to add extensibilty to your orchestration. Agents refer to context-bound functionality, sharing many microservices style charactertics that perform tasks in conjunction with an orchestrator. The orchestrator can advertise tasks out to a pool of agents or agents can register capabilities with the orchestrator. Both allow the orchestrator to dynamically decide how to break up and route up the query amongst the agents.

Agentic approaches are ideal when you have a common UI surface but with multiple, evolving features that can be "plugged into" that experience to add more skills and grounding data to the flow over time.

For complex workloads with many agents, it is more efficient to allow agents to dynamically collaborate than using the orchestrator to break up tasks and assign them.

Communication between the orchestrator and agents should follow a topic-queue pattern, where agents are subscribers to a topic and the orchestrator sends out tasks via a queue.

Using an agentic approach works best with an orchestration pattern rather than an [choreography pattern](/azure/architecture/patterns/choreography).

See the [orchestration platform considerations](./application-platform.md#considerations-for-the-orchestration-platform) for guidance on choosing an orchestration platform.

### Using API gateways

API gateways, like [Azure API Management](/azure/api-management/api-management-key-concepts), abstract functions away from APIs which decouples dependencies between the requesting service and the API. API gateways provide the following benefits to AI workloads:

- *Multiple microservices:* They help you manage multiple AI model endpoints and you need to enforce consistent policies, like rate limiting and authentication. 

- *Traffic management:* They help you manage high-traffic apps efficiently by managing requests, caching responses, and distributing load. 

- *Security:* They provide centralized access control, logging, and threat protection for the APIs behind the gateway. 

## Chunking strategies

A well-defined chunking strategy is critical to optimizing your workload's performance efficiency. Start with the [guidance](/azure/architecture/ai-ml/guide/rag/rag-chunking-phase) provided in the [Designing and developing a RAG solution](azure/architecture/ai-ml/guide/rag/rag-solution-design-and-evaluation-guide) series. Additional recommendations to consider are:

- Implement a dynamic chunking strategy that adjusts chunk sizes based on the data type, query complexity, and user requirements. This can enhance retrieval efficiency and context preservation.

- Incorporate feedback loops to refine chunking strategies based on performance data.

- Preserve data lineage for chunks by maintaining metadata and unique identifiers that link back to the grounding source. Clear lineage documentation ensures users understand the data origin, its transformations, and how it contributes to the output.

## Considerations for choosing frameworks and libraries

The choice of frameworks and libraries is closely intertwined with application design, impacting not just the architecture but also performance, scalability, and maintainability. Conversely, design requirements can limit framework choices, creating a dynamic interplay between the two. For example, using the Semantic Kernel SDK (SK) often encourages a microservices-based design where each agent or functionality is encapsulated within its own service. Factors to consider when choosing frameworks and libraries are:

- **Application requirements:** The specific requirements of the application (e.g., real-time processing, batch processing) may limit the choice of frameworks. For instance, if the application demands low latency, a framework with asynchronous capabilities may be necessary. 

- **Integration needs:** The design may require specific integrations with other systems or services. If a framework doesn’t support the necessary protocols or data formats, it could necessitate reconsidering the design or selecting a different framework. 

- **Team expertise:** The skillset of the development team can constrain framework choices. A design that relies on a less familiar framework might lead to increased development time and complexity, prompting a selection of a more familiar tool.

## Identity, authorization, and access considerations

Generally speaking, you should approach identity, authorization, and access in the same way that you normally design applications. You should use an identity provider, like Microsoft Entra, to manage these areas as much as possible. There are unique challenges to many AI applications that need special consideration however. Persisting access control lists (ACLs) through the system is sometimes challenging or even impossible without introducing novel development. Review the guidance found in the [secure multi-tenant RAG solution](/azure/architecture/ai-ml/guide/secure-multitenant-rag) to learn how to add security trimming metadata to documents and chunks. This trimming can be based on security groups or similar organizational constructs.
