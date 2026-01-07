---
title: Architecture pattern for AI workloads on Azure
ms.reviewer: simipaul
description: Learn how to design, deploy, and govern AI workloads on Azure with architectural patterns, baseline references, and best practices for secure, scalable systems.
author: simipaul
ms.author: simipaul
ms.date: 12/18/2025
ms.topic: concept-article
---

# Architecture pattern for AI workloads on Azure
This guide helps you design, deploy, and govern AI workloads on Azure. Explore key architectural components, baseline reference architectures, and best practices for building secure, scalable AI systems.

The following diagram shows the key components for the architecture pattern that you should include in your design.

:::image type="content" source="./images/ai-workload-architecture-pattern.jpg" alt-text="Screenshot of typical AI workload architecture pattern showing data processing, model training, intelligent AI applications, AI practices, and platform services." lightbox="./images/ai-workload-architecture-pattern.jpg":::

|Component|Description|
|---|---|
|Data processing and analytics|Data processing and analytics are essential for AI workloads. They ensure machine learning models get clean, well-structured, high-quality datasets for better results. Ingest raw data from diverse sources into a central repository. Then clean, transform, and organize it into reusable datasets for model training, model fine-tuning, and grounding data for intelligent AI applications.|
|Model training and fine-tuning|Model training and fine-tuning are core components of AI architecture. Model development follows a reproducible, iterative process, where models learn from raw data to gain intelligence and deliver meaningful predictions or generative outputs. A well-architected approach tracks model versions, data lineage, and evaluation metrics. Update models continuously as new data becomes available using MLOps practices to maintain model performance and alignment with business needs.|
|Intelligent AI applications|This component brings AI-powered intelligence directly to end users. It combines pretrained models with application logic that manages AI behaviors. The application retrieves knowledge to ground responses in relevant context. It works with other tools and applications by crafting prompts, designing intuitive interfaces, and collecting feedback to improve the experience.|
|AI practices and process|These practices help you build AI solutions that are consistent, repeatable, trustworthy, and aligned with organizational and regulatory expectations. Incorporate DevOps principles such as version control and automated pipelines into your MLOps workflows. For generative AI, extend these practices to include prompt management and content monitoring. Use an iterative deployment strategy, ensuring updates to your AI models are delivered safely, with robust fail-safe mechanisms in place. Continually validate accuracy, performance, and bias throughout the AI lifecycle to keep models high-quality.|
|Platform services and tools|Here are the core cloud capabilities and tools that support AI workloads from development to deployment. Manage AI workloads securely and efficiently by protecting resources, optimizing costs, and monitoring system health through platform services. Use CI/CD pipelines to automatically deploy applications for greater reliability. Scan and filter AI outputs by using specialized tools to identify policy violations. Implement endpoint security measures to safeguard sensitive data returned by the system.|

## Intelligent application architecture
The following diagram shows the components for the intelligent application that you should include in your design.

:::image type="content" source="./images/gen-ai-workload.jpg" alt-text="Screenshot of intelligent application components." lightbox="./images/gen-ai-workload.jpg":::

|Component|Description|
|---|---|



## Training and fine-tuning architecture
The following diagram shows the components required for training and fine-tuning data for the models that you include in your design.

:::image type="content" source="./images/training-fine-tuning-workload.jpg" alt-text="Screenshot of training and fine-tuning data for the models." lightbox="./images/training-fine-tuning-workload.jpg":::

|Component|Description|
|---|---|

## Baseline architectures for AI workloads
These baseline examples serve as the recommended architecture for AI workloads.

<ul class="columns is-multiline has-margin-left-none has-margin-bottom-none has-padding-top-medium">
      <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <article class="card has-outline-hover is-relative is-fullheight">
            <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
               <a class="is-undecorated is-full-height is-block"
                 href="/azure/architecture/ai-ml/architecture/baseline-microsoft-foundry-chat">
                 <img role="presentation" alt="Microsoft Foundry chat reference architecture" src="./images/baseline-microsoft-foundry.svg">
                </a>
            </figure>
            <div class="card-content has-text-overflow-ellipsis">
                <div class="is-size-7 has-margin-top-none has-margin-bottom-none has-text-primary">
                  Baseline Microsoft Foundry chat reference architecture
                  <hr>                      
                </div>
                <div class="is-size-7 has-margin-top-small has-line-height-reset">
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
                 <img role="presentation" alt="Microsoft Foundry chat reference in Azure landing zone" src="./images/baseline-microsoft-foundry-landing-zone.svg">
                </a>
            </figure>
            <div class="card-content has-text-overflow-ellipsis">
                <div class="is-size-7 has-margin-top-none has-margin-bottom-none has-text-primary">
                  Baseline Microsoft Foundry chat reference architecture in an Azure landing zone
                  <hr>                      
                </div>
                <div class="is-size-7 has-margin-top-small has-line-height-reset">
                    <p>This architecture builds on the Microsoft Foundry Chat design and places it in a secure Azure Landing Zone. It brings together key components—Foundry Agent Service, Azure OpenAI, and App Service—inside a private, network-isolated environment. All services connect through private endpoints and are protected by Azure Firewall, with zone redundancy for high availability.</p>
                </div>
            </div>
        </article>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <article class="card has-outline-hover is-relative is-fullheight">
            <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
               <a class="is-undecorated is-full-height is-block"
                 href="/azure/architecture/example-scenario/dataplate2e/data-platform-end-to-end">
                 <img role="presentation" alt="App Services baseline architecture diagram." src="./images/azure-analytics-end-to-end.svg">
                </a>
            </figure>
            <div class="card-content has-text-overflow-ellipsis">
                <div class="is-size-7 has-margin-top-none has-margin-bottom-none has-text-primary">
                  Analytics end-to-end with Azure Synapse
                  <hr>                      
                </div>
                <div class="is-size-7 has-margin-top-small has-line-height-reset">
                    <p>Use this architecture as reference when designing a unified data platform that streamlines full analytics lifecycle.</p>
                </div>
            </div>
        </article>
    </li>
</ul>

