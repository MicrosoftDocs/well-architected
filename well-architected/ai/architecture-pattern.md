---
title: Architecture pattern for AI workloads on Azure
ms.reviewer: simipaul
description: The design areas represent the architecturally significant topics that must be discussed and designed for when defining a target architecture
author: simipaul
ms.author: simipaul
ms.date: 12/18/2025
ms.topic: concept-article
---

# Architecture pattern for AI workloads on Azure
Learn how to assess, plan, and manage AI workloads. Discover best practices and tools for governing, securing, and maintaining your data and models throughout their lifecycle.

The following diagram shows the key components for the architecture pattern that you should include in your design.

:::image type="content" source="./images/ai-workload-architecture-pattern.png" alt-text="Diagram that shows the typical architecture pattern of an AI workload." lightbox="./images/ai-workload-architecture-pattern.png":::

## Data processing and analytics
Data processing and analytics are essential for AI workloads. They ensure machine learning models get clean, well-structured, high-quality datasets for better results. Prepare your data to make better business decisions. This stage covers three key areas: data ingestion, data processing, and analytics. Ingest raw data from diverse sources into a central repository. Then clean, transform, and organize it into reusable datasets for model training. Use these refined datasets to enable advanced analytics—generating reports, dashboards, and key business metrics. 

## ML training and fine-tuning
ML training and fine-tuning is the core of the AI architecture. This phase enables data scientists and architects to experiment with models and validate ideas quickly. Starting with a pre-trained model provides a solid foundation, which you can then customize for specific tasks. Model development follows a reproducible, iterative process, where models learn from raw data to gain intelligence and deliver meaningful predictions or generative outputs. Carefully track each model’s lifecycle for governance, ensuring easy retrieval and robust management for deployment. Deploy effective models to production through a pipeline. Update models continuously as new data becomes available using MLOps practices to maintain performance.

## Generative AI applications
This component brings AI-powered intelligence directly to end users. It combines pre-trained models with application logic that manages AI behaviours. The application retrieves knowledge to ground responses in relevant context.  It works with other tools and applications by crafting prompts, designing intuitive interfaces, and collecting feedback to improve the experience.
 
## AI practices and process
Build trustworthy AI systems by incorporating ethics, transparency, and DevOps principles throughout development and operation. Design your system with ethics, transparency, and your users in mind to build trust and stay compliant. Incorporate DevOps principles such as version control and automated pipelines into your ML Ops workflows. For generative AI, extend these practices to include prompt management and content monitoring. Use an iterative deployment strategy, ensuring updates to your AI models are delivered safely, with robust fail-safe mechanisms in place. Continually validate accuracy, performance, and bias throughout the AI lifecycle to keep models high-quality.

## Platform services and tools
Secure and efficiently manage AI workload operations. Focus on protecting resources, optimising costs, and maintaining system health. Monitor AI operation health and performance through platform services. Use CI/CD pipelines to automatically deploy applications for greater reliability. Scan and filter AI outputs using specialized tools to identify policy violations. Implement endpoint security measures to safeguard sensitive data returned by the system.


## Baseline architectures for AI workloads
    • AAC Foundry Architectures - GenAI inference
        ○ Baseline Microsoft Foundry chat reference architecture: https://learn.microsoft.com/en-us/azure/architecture/ai-ml/architecture/baseline-azure-ai-foundry-chat
        ○ Baseline Microsoft Foundry chat reference architecture in an Azure landing zone: https://learn.microsoft.com/en-us/azure/architecture/ai-ml/architecture/baseline-azure-ai-foundry-landing-zone
    • Data
        ○ Analytics end-to-end with Azure Synapse: https://learn.microsoft.com/en-us/azure/architecture/example-scenario/dataplate2e/data-platform-end-to-end

