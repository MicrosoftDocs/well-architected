---
title: Architecture pattern for AI workloads on Azure
ms.reviewer: simipaul
description: Learn how to design, deploy, and govern AI workloads on Azure with architectural patterns, baseline references, and best practices for secure, scalable systems.
author: simipaul
ms.author: simipaul
ms.date: 12/18/2025
ms.topic: concept-article
---

# Azure architecture pattern for AI workloads
An AI architecture pattern is a reusable, high-level framework that describes the core components and interactions commonly found in AI workloads. 

It's intended to help you design, deploy, and govern AI solutions on Azure. It provides baseline reference architectures and best practices to help build secure, scalable, and well governed AI systems. 

For example, an organization builds an enterprise AI assistant that enables employees to ask natural language questions over internal documents and operational data.
At a high-level, the solution is designed using AI workload architecture pattern, which shows how data, application, models, practices, and platform services work together to deliver a secure, scalable, and well governed AI system.  

The data processing layer cleans, enriches, and indexes internal content and operational data so the assistant can retrieve trusted, up-to-date context. When a user asks a question, the intelligent application orchestrates the workflow. It determines what data is needed, retrieves grounded context from the processing layer, and calls the appropriate model to generate a response. Foundation models are evaluated, tuned, or adapted for enterprise needs outside of runtime. At runtime, the intelligent application invokes trained models to produce grounded responses using retrieved enterprise data, while the training process remains decoupled from live operations. 

Practices that span the lifecycle such as responsible AI, testing and evaluation, MLOps/GenAIOps, and safe deployment ensure the assistant behaves responsibly, can be validated systematically, and runs reliably in production. Platform services provide secure access to data and models, observability, cost control, and consistent governance and safety enforcement across the solution.

While the AI assistant represents a specific business scenario, the top level architecture diagram reflects a generic AI workload design that can be adapted to many AI use cases with similar architectural characteristics. 

## [**High-level AI workload**](#tab/aiworkload)

:::image type="content" source="./images/ai-workload-architecture-pattern.png" alt-text="Screenshot of typical AI workload architecture pattern showing data processing, model training, intelligent AI applications, AI practices, and platform services." lightbox="./images/ai-workload-architecture-pattern.png":::

|Component|Description|
|---|---|
|Data processing and analytics|Data processing and analytics are essential for AI workloads. This layer ingests raw data from diverse sources into a central repository. Then clean, transform, and organize it into reusable datasets for model training, model fine-tuning, and grounding data for intelligent AI applications. It doesn’t interact with users directly and enables accurate, efficient AI interactions downstream.|
|Model training and fine-tuning|Training and fine-tuning are core components of AI architecture. Model development follows a reproducible, iterative process, where models learn from raw data to gain intelligence and deliver meaningful predictions or generative outputs. A well-architected approach tracks model versions, data lineage, and evaluation metrics. Update models continuously with MLOps practices as new data becomes available to maintain performance and alignment with business needs|
|Intelligent AI applications|This component brings AI-powered intelligence directly to end users. It combines pretrained models with application logic that manages AI behaviors. Applications retrieve knowledge to ground responses in context, craft prompts, design intuitive interfaces, and collect feedback to improve user experience. This layer serves as the control plane for AI interactions, combining traditional application logic with AI-driven reasoning.|
|AI practices and process|These practices ensure AI solutions are consistent, repeatable, and trustworthy while meeting organizational and regulatory standards. Incorporate DevOps principles such as version control and automated pipelines into your MLOps workflows. For generative AI, extend these practices to include prompt management and content monitoring. Use iterative deployment strategies with fail-safes, and continually validate accuracy, performance, and bias throughout the AI lifecycle.|
|Platform services and tools|Core cloud services and tools support AI workloads from development to deployment. Keep your AI workloads secure and cost-effective by protecting resources, optimizing spending, and monitoring system health through platform services. Use CI/CD pipelines to automatically deploy applications for greater reliability. Use specialized tools to scan and filter AI outputs for policy compliance, while endpoint security safeguards sensitive data.|

The table below describes the design considerations that is applied to the intelligent application workload and training/fine-tuning workload components of the architecture pattern.

| Design Characteristic | Description |
|---|---|
| Resource lifetime and state | Lifetime: What's the expected lifetime of the resource, relative to other resources in the solution? Should the resource outlive or share the lifetime with the entire system or region, or should it be temporary? State:What impact will the persisted state at this layer have on reliability or manageability? |
| Resource scope and dependencies | Scope: Is the resource required to be globally distributed? Can the resource communicate with other resources, located globally or within that region? Dependencies: What are the dependencies on other resources? |
| Scalability and availability | Scale: What is the expected throughput for that resource? How much scale is provided by the resource to fit that demand? Availability: What is the impact on availability from a disaster at this layer? Would it cause a systemic outage or only a localized capacity or availability issue?|
| Security and responsible AI | Considerations for security and responsible AI practices. |


## [**Intelligent application workload**](#tab/intelligentaiworkload)

The following diagram shows the components for the intelligent application to include in your design.

:::image type="content" source="./images/gen-ai-workload.png" alt-text="Screenshot of intelligent application components." lightbox="./images/gen-ai-workload.png":::

|Component|Description|
|---|---|
|Client layer|The client layer lets users and external systems connect with AI. It handles requests and returns AI-generated responses, ensuring an intuitive and accessible experience.|
|Intelligence layer - API|This layer bridges clients and intelligence capabilities through well-defined APIs. It routes requests to the right agent or orchestrator and ensures smooth, consistent interactions between clients and services. It manages data access, applies security protections, and enforces rate limits and quotas to prevent overload. For applications that only need a simple prediction, the layer can skip orchestration and send the request straight to the inference engine.|
|Intelligence layer - orchestration and agent compute|This layer is responsible for orchestrating and managing the interactions between AI components for each task. It uses various orchestration modes to execute the tasks. Task execution can follow a sequential, step-by-step processing where agents work one after the other, and a concurrent processing which allows multiple agents to work in parallel and combine their outputs. This layer identifies user intent and applies necessary safety checks to responses. It integrates with knowledge layer to get information and can use tools, to combine their outputs to give the best response.|
|Intelligence layer - conversation management|This layer allows the system to hold a natural, flowing conversation by remembering previous messages, keeping track of ongoing topics and saving important parts of the conversation to maintain continuity for longer sessions. It also manages the data retention policy for the conversation.|
|Inferencing layer - foundation or predictive models||
|Knowledge||
|Tools layer||

### Design consideration

#### Resource lifetime and state

#### Resource scope and dependencies 

#### Scalability and availability 

#### Security and responsible AI 

## [**Training and fine-tuning workload**](#tab/trainingmodelworkload)

The following diagram shows the components required for training and fine-tuning data for the models that you include in your design.

:::image type="content" source="./images/training-fine-tuning-workload.png" alt-text="Screenshot of training and fine-tuning data for the models." lightbox="./images/training-fine-tuning-workload.png":::

|Component|Description|
|---|---|
|Data Sources||
|Data Aggregation Store||
|Data Processing Platform||
|Feature Store||
|Training Platform||
|Model Registry||
|Inferencing Layer - Predictive Models||

### Design consideration

#### Resource lifetime and state

#### Resource scope and dependencies 

#### Scalability and availability 

#### Security and responsible AI 

---

## Baseline architectures for AI workloads
These baseline examples serve as the recommended architecture for AI workloads.

<ul>
      <li>
        <article class="card has-outline-hover is-relative is-fullheight">
            <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
               <a class="is-undecorated is-full-height is-block"
                 href="/azure/architecture/ai-ml/architecture/baseline-microsoft-foundry-chat">
                 <img alt="Microsoft Foundry chat reference architecture" src="./images/baseline-microsoft-foundry.svg">
                </a>
            </figure>
            <div>
                <div>
                  Baseline Microsoft Foundry chat reference architecture
                  <hr>                      
                </div>
                <div>
                    <p>Use this architecture as a reference to build and deploy secure, scalable conversational AI solutions on Azure using Foundry, Azure OpenAI, and other related services. It emphasizes private networking, zone redundancy, and strict security controls to ensure compliance and enterprise readiness.</p>
                </div>
            </div>
        </article>
    </li>
    <li>
        <article class="card has-outline-hover is-relative is-fullheight">
            <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
               <a class="is-undecorated is-full-height is-block"
                 href="/azure/architecture/ai-ml/architecture/baseline-microsoft-foundry-landing-zone">
                 <img alt="Microsoft Foundry chat reference in Azure landing zone" src="./images/baseline-microsoft-foundry-landing-zone.svg">
                </a>
            </figure>
            <div>
                <div>
                  Baseline Microsoft Foundry chat reference architecture in an Azure landing zone
                  <hr>                      
                </div>
                <div>
                    <p>This architecture builds on the Microsoft Foundry Chat design and places it in a secure Azure landing zone. It brings together key components—Foundry Agent Service, Azure OpenAI, and App Service—inside a private, network-isolated environment. All services connect through private endpoints and are protected by Azure Firewall, with zone redundancy for high availability.</p>
                </div>
            </div>
        </article>
    </li>
    <li>
        <article class="card has-outline-hover is-relative is-fullheight">
            <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
               <a class="is-undecorated is-full-height is-block"
                 href="/azure/architecture/example-scenario/dataplate2e/data-platform-end-to-end">
                 <img alt="App Services baseline architecture diagram." src="./images/azure-analytics-end-to-end.svg">
                </a>
            </figure>
            <div>
                <div>
                  Analytics end-to-end with Azure Synapse
                  <hr>                      
                </div>
                <div>
                    <p>Use this architecture as reference when designing a unified data platform that streamlines full analytics lifecycle.</p>
                </div>
            </div>
        </article>
    </li>
</ul>
