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
An AI architecture pattern is a reusable framework that shows you the core components and interactions you'll find in AI workloads. 

It helps you design, deploy, and govern AI solutions on Azure. You get baseline reference architectures and best practices to build secure, scalable, and well-governed AI systems. 

Here's an example: an organization builds an enterprise AI assistant that lets employees ask natural language questions about internal documents and operational data. At a high level, you design the solution using the AI workload architecture pattern, which shows how data, applications, models, practices, and platform services work together to deliver a secure, scalable, and well-governed AI system.  

The data processing layer cleans, enriches, and indexes internal content and operational data so the assistant can retrieve trusted, up-to-date context. When a user asks a question, the intelligent application orchestrates the workflow. It figures out what data's needed, retrieves context from the processing layer, and calls the right model to generate a response. Foundation models are evaluated, tuned, or adapted for enterprise needs outside of runtime. At runtime, the intelligent application invokes trained models to produce responses grounded in your enterprise data, while the training process stays separate from live operations. 

Practices that span the lifecycle such as responsible AI, testing and evaluation, MLOps/GenAIOps, and safe deployment ensure the assistant behaves responsibly, can be validated systematically, and runs reliably in production. Platform services provide secure access to data and models, observability, cost control, and consistent governance and safety enforcement across the solution.

While the AI assistant represents a specific business scenario, the architecture diagram reflects a generic AI workload design you can adapt to many AI use cases with similar characteristics. 

## [**High-level AI workload**](#tab/aiworkload)

This diagram shows the key components you'll need in your AI workload design.

:::image type="content" source="./images/ai-workload-architecture-pattern.png" alt-text="Diagram of AI workload design with labeled components for AI practices and process, data processing and analytics, model training and fine-tuning, intelligent AI applications, and platform services and tools." lightbox="./images/ai-workload-architecture-pattern.png":::

|Component|Description|
|---|---|
|Data processing and analytics|Data processing and analytics is where you gather raw data from different sources and bring it to a central repository. From there, you clean the data, transform it, and organize it into datasets that are ready for model training, fine-tuning, and grounding your AI applications. It doesn’t interact with users directly and enables accurate, efficient AI interactions downstream.|
|Model training and fine-tuning|Model training is where your models learn and improve. You follow a repeatable process, train models on data, track different versions and monitor what's working. You keep improving as new data comes in with MLOps practices to maintain performance and alignment with business needs.|
|Intelligent AI applications|Intelligent AI applications is where users actually interact with your AI. It brings together pretrained models and the logic that controls how AI responds. Your application finds the right information, crafts prompts, builds intuitive interfaces, and learns from feedback. It's the control plane for AI interactions, combining traditional application logic with AI-driven reasoning.|
|AI practices and process|These are the practices that keep your AI solution reliable and trustworthy. You incorporate DevOps principles like version control and automated pipelines into your MLOps workflows. For generative AI, you also manage prompts and monitor content. Deploy iteratively with safeguards in place, and continuously check for accuracy, performance, and bias.|
|Platform services and tools|Core cloud services and tools support your AI workloads from development to deployment. They help you secure your resources, control costs, and monitor system health. Use CI/CD pipelines to deploy automatically and reliably. Use specialized tools to scan AI outputs for compliance, and protect sensitive data with endpoint security.|

The following sections break down design considerations for the intelligent application and training/fine-tuning components.

### Design consideration

TODO: Define Characteristic and why is it important, risk and tradeoff for each component, high level implication of each component.

When designing your AI workload architecture, consider the following design characteristics to make informed decisions about component design and interactions. Each characteristic has implications for how you structure your solution, the tradeoffs you may encounter, and the risks to manage.

#### Resource lifetime and state

Lifetime: What's the expected lifetime of the resource, relative to other resources in the solution? Should the resource outlive or share the lifetime with the entire system or region, or should it be temporary? State: What impact will the persisted state at this layer have on reliability or manageability?

#### Resource reach and dependencies 

Reach: Is the resource required to be globally distributed? Can the resource communicate with other resources, located globally or within that region? Dependencies: What are the dependencies on other resources?

#### Scalability and availability 

Scale: What is the expected throughput for that resource? How much scale is provided by the resource to fit that demand? Availability: What is the impact on availability from a disaster at this layer? Would it cause a systemic outage or only a localized capacity or availability issue?

#### Security and responsible AI 

Security: What measures are in place to protect data and ensure compliance with regulations? Responsible AI: How does the system ensure ethical AI practices, including fairness, transparency, and accountability?



## [**Intelligent application workload**](#tab/intelligentaiworkload)

This diagram shows the key components of the intelligent application workload to include in your design.

:::image type="content" source="./images/gen-ai-workload.png" alt-text="Diagram of intelligent application workload showing clients, intelligence layer, inferencing, knowledge, and tools components." lightbox="./images/gen-ai-workload.png":::

|Component|Description|
|---|---|
|Client layer|The client layer lets users and external systems connect with AI. This layer takes your requests and returns AI-generated responses, while making sure the experience is straightforward and easy to use.|
|Intelligence layer - API|The Intelligence layer – API bridges clients and the intelligence features of the system through well-defined APIs. It's responsible for directing requests to the right agent or orchestration process, making sure interactions between users and services are smooth and consistent. This layer also handles how data is accessed, puts security measures in place, and sets limits to prevent the system from getting overloaded. If an app just needs a simple prediction, this layer can skip the complex orchestration steps and send the request directly to the inference engine for a fast response.|
|Intelligence layer - orchestration and agent compute|Orchestration and agent compute layer is responsible for coordinating how different AI components work together to get each task done. Depending on what's required, it can run tasks one after the other or let several agents work at the same time and then merge their results. It figures out user intent, checks responses to make sure they're safe, integrates with the knowledge layer for information, and uses tools to combine everything and give you the best answer.|
|Intelligence layer - conversation management|Conversation management layer is the system's memory and conversation manager. It lets the AI chat naturally by recalling previous messages, keeping track of ongoing topics, and storing important parts from the discussion, so conversations can flow smoothly even during long sessions. It also looks after how the conversation data is kept or deleted, ensuring your information is handled responsibly.|
|Inferencing layer - foundation or predictive models|Inferencing layer is where a trained model makes predictions, generates content, or provides decisions based on the information it receives. The process starts by loading your AI model, prepping the data, running the predictions, and then formatting the results so they're available immediately (real-time) or later on(batch processing).|
|Knowledge layer|Knowledge layer is where the system gets the information and context it needs to answer questions accurately. It makes sure data is accessed securely, using permissions and authorisation. The knowledge layer helps AI follow the RAG approach by searching through indexes or vector databases to find just the right content. It lets AI access various internal and external data sources in a consistent way, whether that's through MCP or REST protocols.|
|Tools layer|Tools layer is where business actions and external capabilities are made accessible. The intelligence layer can trigger these actions or connect with other systems by calling tools or agents in a standardised way, whether that's through MCP, A2A, or OpenAPI/REST. These capabilities are presented as actionable options, ready for the intelligence layer to use, and they might be handled directly by the workload or by external services.|

### Design considerations

TODO: What is this component doing for this characteristic and why is it important, risk and tradeoff for each component.

#### Resource lifetime and state

#### Resource reach and dependencies 

#### Scalability and availability 

#### Security and responsible AI 

## [**Training and fine-tuning workload**](#tab/trainingmodelworkload)

This diagram shows the key components required for training and fine-tuning data for the models that you include in your design.

:::image type="content" source="./images/model-training-workload.png" alt-text="Diagram of a machine learning workflow showing data sources, processing, model training, and inferencing steps." lightbox="./images/model-training-workload.png":::

|Component|Description|
|---|---|
|Data Sources|Data sources contain wide range of data help train and fine-tune the models. Typically, these include:
-	Structured data from relational databases like SQL Server, which have clear schemas and relationships.
-	Semi-structured data such as application logs and telemetry, often in JSON or XML formats.
-	Unstructured data such as image files, videos, audio, and text documents like PDFs.
-	Real-time streams from sensors, devices, or event sources.
These data are gathered from various sources such as proprietary sources owned by the organisation, user-generated content from interactions, expert feedback and collaboration, and public sources like websites, research papers, and shared databases.|
|Data Aggregation Store|Think of a data aggregation store as the central hub for all the information you collect from various sources. It’s a place where your raw data is kept in its original form before any processing begins. Tools like Azure Data Lake Storage or Azure Synapse Analytics are used for this kind of storage. As the data moves through different stages of processing, its structure gets refined, fields and columns are named consistently, values are checked for accuracy, and everything is organized to make reporting and analytics easier. You can always trace where the data came from, see what changes were made, and know which process transformed it. Versioning also ensures you have historical snapshots as your data evolves.|
|Data Processing Platform|At this stage, the raw data gets turned into useful dataset for machine learning and analytics. The process starts by collecting data from multiple sources, cleans and enriches it, so you get high-quality datasets and features that are ready for model training and analysis. This layer supports ETL pipelines, follows a medallion architecture, and enables feature and data enrichment based on existing patterns. It typically uses tools such as  Azure Data Factory, Synapse Analytics, and Spark.|
|Feature Store|A feature store is a central place for storing pre-computed features, so teams can easily reuse them across different machine learning models. It keeps track of feature definitions, transformations, and metadata like ownership, update frequency, data sources, and versioning. This helps teams build models faster and ensures consistency, making model behavior more predictable. Azure Machine Learning's feature store also offers versioning and lineage, and organizations can choose to set it up centrally, in a distributed way, or as a hybrid.|
|Training Platform|A compute environment that is used to train and fine-tune machine learning models at scale. It lets you select algorithms to see which performs best, automatically tests different parameter values, manages retries and dependencies, and supports repeated training cycles for ongoing model improvement. Every training run’s metrics, parameters, and artifacts are tracked, and the environment can be hosted on Azure Machine Learning, Databricks, or Kubernetes.|
|Model Registry|A version-controlled repository that lets you store, manage, and track machine learning models as they progress from development to production. Tools like Azure Machine Learning Model Registry make this easy by keeping model binaries, metadata, training configurations, and lineage organized. You can compare different model versions and roll back to a previous one if required.|
|Inferencing Layer - Predictive Models|Trained models are used to generate predictions or make decisions based on data. You can deploy them as real-time REST APIs for quick predictions or as batch endpoints for processing large datasets asynchronously. Besides client applications, models are also called during data processing such as to extract entities or sentiment for data enrichment, and to handle data normalization and transformation.|

### Design consideration

TODO: What is this component doing for this characteristic and why is it important, risk and tradeoff for each component.

#### Resource lifetime and state

#### Resource scope and dependencies 

#### Scalability and availability 

#### Security and responsible AI 

---

## Baseline architectures for AI workloads
These baseline examples serve as the recommended architecture for AI workloads.

<ul class="columns is-multiline has-margin-left-none has-margin-bottom-none has-padding-top-medium">
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <article class="card has-outline-hover is-relative is-fullheight">
            <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
               <a class="is-undecorated is-full-height is-block"
                 href="/azure/architecture/ai-ml/architecture/baseline-microsoft-foundry-chat">
                 :::image type="content" source="./images/baseline-microsoft-foundry.svg" alt-text="Microsoft Foundry chat reference architecture.":::
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
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <article class="card has-outline-hover is-relative is-fullheight">
            <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
               <a class="is-undecorated is-full-height is-block"
                 href="/azure/architecture/ai-ml/architecture/baseline-microsoft-foundry-landing-zone">
                 :::image type="content" source="./images/baseline-microsoft-foundry-landing-zone.svg" alt-text="Microsoft Foundry chat reference in Azure landing zone.":::
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
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <article class="card has-outline-hover is-relative is-fullheight">
            <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
               <a class="is-undecorated is-full-height is-block"
                 href="/azure/architecture/example-scenario/dataplate2e/data-platform-end-to-end">
                 :::image type="content" source="./images/azure-analytics.svg" alt-text="App Services baseline architecture diagram.":::
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