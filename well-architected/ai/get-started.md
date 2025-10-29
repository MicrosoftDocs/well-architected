---
title: AI workloads on Azure
description: Learn about architectural considerations, including common challenges and key design areas, for building and operating AI workloads on Azure.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2024
ms.topic: concept-article
ms.update-cycle: 180-days  
---

# AI workloads on Azure

This article addresses architectural challenges of designing AI workloads. It focuses on nondeterministic functionality, data and application design, and operations. The recommendations are based on Azure Well-Architected Framework principles and include insights from successful Azure implementations.

These articles are meant for *workload owners* and *technical stakeholders* like architects, development leads, and IT leaders. Specialized AI and data roles, such as data scientists, should also be aware of this guidance because collaboration across various roles and teams is a key aspect.

> [!NOTE]
>
> Azure offers various AI services that you can integrate into your workload or build around it. Depending on your business needs, you can choose between fully managed software as a service (SaaS) solutions, platform as a service (PaaS) solutions, or build your own AI solution. *Specific Azure services and their capabilities are not covered here*. We recommend that you refer to the respective product documentation for that information.
>
> Also, certain AI workloads aren't in scope, such as:
>
> - Workloads that are realized through low-code and no-code offerings, such as Copilot Studio.
> - Workloads that require high-performance computing.
> - Workloads that don't implement generative or discriminative AI use cases.

## What is an AI workload?

In the context of Well-Architected Framework, an AI workload meets the needs of predictive, discriminatory, or generative tasks. It focuses on ethical functionality, adapting to fast-evolving AI technologies, and staying relevant and explainable. Apply the [Well-Architected Framework pillars](../pillars.md) at every decision point to ensure that the system is reliable, secure, efficient, and cost-effective.

AI workloads are different from traditional ones because they replace deterministic functionality in parts of the workload with nondeterministic behavior that solves for situations where fixed outcomes are impractical. Instead, they combine code and data into an entity, or a _model_, to enable unique experiences that traditional systems can't provide.

Before you begin your design strategies, consider these key points first.

#### Familiarize yourself with the broad categories of models

- **Generative AI**: Uses machine learning to autonomously create new content. It includes language models that can be customized with user data or used as services like Azure OpenAI Service. For example, GPT, a type of language model, specializes in mimicking human conversational language and is ideal for chat and natural language experiences.

  Use cases: Generative AI can produce articles, stories, and art. It can also generate synthetic data to balance datasets and make chatbots more humanlike.
  
- **Discriminative AI**: Uses explicit programming to perform specific tasks based on rules and algorithms. It's divided into:

  - **Model-based**: Predictive systems find patterns based on training performed from prior observations to make predictions but can't create new content or adapt on their own.
  - **Non-model-based**: Autonomous agents follow predefined rules to interact with systems, like video game characters.

  Use case: Discriminative AI is used for predictive analytics, recommendation systems, and fraud detection.

This series of articles covers different AI workloads and focuses on specific types like language models when needed.

> [!IMPORTANT]
>
> When you choose between generative and discriminative models, think about the task you need to accomplish. Generative models create new data. Discriminative models classify existing data based on features. For classification or regression tasks, pick models that fit the job. For example, a language model that can classify might be more versatile than one that only classifies.

#### Evaluate your build versus buy options

If generic responses are acceptable, a prebuilt model or an AI service-based solution that uses opaque processing should be sufficient for your workload. But if you need data specific to your business or have compliance requirements, create a custom model.

When you choose between a custom model, a prebuilt model, or a service, consider these factors:

- **Data control**: Custom models give you more control over sensitive data. Prebuilt models are easier for general tasks.
- **Customization**: Custom models are better for unique needs. Prebuilt models might lack flexibility.
- **Cost and upkeep**: Custom models need ongoing maintenance and resources. Prebuilt models usually have lower initial costs and less infrastructure burden.
- **Performance**: Prebuilt services offer optimized infrastructure and scalability. They're ideal for low-latency or high-scalability needs.
- **Expertise**: Custom models require a skilled team. Prebuilt models are often quicker to deploy and easier to use if expertise is limited.

> [!IMPORTANT]
>
> Creating and maintaining your own model requires a lot of resources, time, and expertise. It's important to research thoroughly before you decide. Usually, choosing a prebuilt model or a managed service is a better option.

## What are the common challenges?

- **Compute costs**: AI functions can be expensive because of high compute needs, and compute needs can vary according to your workload design. Understand your requirements and choose the right service to manage costs.
- **Security and compliance requirements**: Off-the-shelf solutions might not meet your security and compliance needs. Research options to avoid unnecessary burdens.
- **Volume of data**: Handling large data volumes in various formats comes with challenges of protecting sensitive information and efficient processing. Optimizing storage, processing, and transfer costs should be an ongoing activity.
- **Model decay**: Models can degrade over time, which leads to inaccurate results. Testing AI systems is challenging because of their randomness.
- **Skill challenges**: New AI workloads might need specialized roles and new operations processes that require extensive training.
- **Pace of AI innovation**: Adopting the latest technologies can be tempting to stay at the cutting edge. Evaluate new technologies carefully to ensure that they improve user experience and don't just add complexity for the sake of being up to date.
- **Ethical requirements**: Clearly determine whether your use case is an ethical target for AI. Maintaining ethical standards is necessary throughout the planning and implementation phases to ensure that you're building a responsible system.

## How to use this guidance

**&#10004;** Start with [Design methodology](./design-methodology.md), which outlines the rationale and recurring themes across technical and operational areas. This systematic approach helps to define requirements and design strategies. Revisit this methodology when you face uncertain choices to stay aligned with the workload's overall goals. It also provides a framework for collaborating with stakeholders to justify technical decisions and incorporate customer feedback for continuous improvement.

**&#10004;** Proceed to [Design principles](./design-methodology.md) to see how the design methodology aligns with the core Well-Architected Framework pillars. Consider growth evolution. Evaluate the underlying principles for all pillars collectively, including the tradeoffs.

**&#10004;** Focus on the *design areas* that have the biggest effect on your solution. Each area includes considerations and recommendations to guide you through the design decisions.

**&#10004;** Use the [Assessment Review Tool](./assessment.md) to evaluate the readiness of your optimized AI workload in production.

## Typical architecture pattern and design areas

The following diagram illustrates how data flows through the system from initial collection to final user interaction.

:::image type="content" source="./images/ai-architecture-pattern.png" alt-text="Diagram that shows the typical architecture pattern of an AI workload." lightbox="./images/ai-architecture-pattern.png":::

The architecture highlights the integration of different components to enable efficient data processing, model optimization, and real-time application deployment in AI-driven solutions. It includes modules such as data sources, data processing, model training, model deployment, and user interfaces. 

The following table describes some key design areas related to that pattern.

|Design areas|
|---|
|[Application design](./application-design.md): Learn about considerations unique to AI workloads that might have a significant effect on your existing application design standards. |
|[Application platform](./application-platform.md): Determine the best platforms to use to support AI workload functions, like model hosting, model training, and inferencing. |
|[Training data design](./training-data-design.md): Design strategies for data ingestion, preprocessing, retention, and governance topics to handle your model training data. |
|[Grounding data design](./grounding-data-design.md): Design strategies to optimize searchability and retrieval, while meeting security and compliance requirements for your grounding data. |
|[Data platform](./data-platform.md): Determine the best hosting platform to handle the large amounts and potentially many formats of data that your workload uses. |
|[Machine learning operations and Generative AI operations](./mlops-genaiops.md): Establish modern DevOps practices to support your machine learning or generative AI functions and systems. |
|[Workload operations](./operations.md): Modernize your operational practices with new approaches and add specialized roles and training. |
|[Testing and evaluation](./test.md): Develop testing and evaluation strategies to measure characteristics like accuracy, precision, sensitivity, and specificity through metrics that are targeted for AI workloads. |
|[Workload personas](./personas.md): Understand how personas are involved in the full lifecycle of your AI workload to help ensure that your team is fully capable of building and supporting it. |
|[Responsible AI](./responsible-ai.md): Pay special attention to the user experience and ethical implications of releasing your AI solution to the public. AI brings incredible opportunities for new products and services, but it also carries a considerable degree of risk. |

> [!TIP]
> Every architectural decision involves a range of considerations and a set of acknowledged compromises that balance different aspects of the framework. These tradeoffs are indicated by this icon :::image type="icon" source="../_images/trade-off.svg":::.

## Next step

> [!div class="nextstepaction"]
> [Design methodology](./design-methodology.md)
