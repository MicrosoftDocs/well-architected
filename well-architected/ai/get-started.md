---
title: AI Workloads on Azure
description: Learn about architectural considerations, including common challenges and key design areas, for building and operating AI workloads on Azure.
author: danielstocker
ms.author: prwilk
ms.date: 11/01/2024
ms.topic: conceptual
ms.custom: template-overview
ms.service: azure-waf
ms.subservice: waf-workload-ai
---

# AI workloads on Azure

This guidance is intended for **workload owners** and **technical stakeholders**, including architects, development leads, and other IT leadership roles. Specialized roles like data scientists or developers aren't the target audience of this guidance, but workload owners and technical stakeholders should use what they learn to help guide those roles through development and deployment. 

This set of articles provides actionable and authoritative guidance for building and operating a highly reliable and scalable solution on Azure. The guidance has a technical foundation in Azure Well-Architected Framework best practices. It's also based on reviews of numerous customer implementations. 

## What is an AI workload in the context of the Well-Architected Framework?

An AI workload is a workload that uses AI technology for predictive, discriminatory, or generative use cases. Sometimes, this functionality replaces a traditional code logic and data access solution that is otherwise deterministic. This design produces an experience that determinism and a classical code and data model can't accomplish. AI combines code logic and data into a single package.

Generally, AI workloads break down into two categories: generative AI and discriminative AI.

**Generative AI** refers to workloads that are based on machine learning practices. These practices are designed to autonomously generate novel outputs rather than return results that the workload finds within the data model based on pattern analysis. Generative AI includes language models that you can pretrain on users' datasets or use like software as a service (SaaS), such as Azure OpenAI Service. 

GPT is a further subset of generative AI. GPT is a family of language models that specialize in mimicking human conversational language and are well-suited for chatbots, like Microsoft 365 Copilot or OpenAI's ChatGPT. 

You can use generative AI to:

  - Create content like articles, fiction, and artistic works.
  - Augment data by using automatically generated synthetic data that can augment existing, unbalanced datasets.
  - Make chatbots and virtual assistants more human-like.
    
**Discriminative AI** relies on explicit programming to perform specific tasks. In this approach, developers create rules and algorithms that guide the AI system's behavior. These predefined rules determine how the AI responds to input data. You can categorize discriminative AI by whether it's model based.

Model-based workloads are predictive machine learning systems that look for patterns in a model to return a specific type of prediction based on defined rules. These systems can't generate new content or adapt autonomously, but they're excellent at predicting outcomes.

Discriminative AI systems that aren't model based include autonomous agents that you use to interact with an application or system, collect data, and use that data to perform predefined tasks. For example, characters in video games are agents that can move around in the game environment and react to game changes based on predefined rules. Developers program agents by using *optimization algorithms*, which find the best solution to a specific problem, or *search algorithms*, which find a specific target or fulfill a specific condition by searching within a dataset.

You can use discriminative AI to:

  - Perform predictive analytics, which you can use in weather forecasting or stock market predictions.
  - Build recommendation systems, which e-commerce platforms use to recommend products to consumers based on their behavior and preferences.
  - Implement fraud detection, which can help you detect fraudulent activities based on behavior anomalies in vast amounts of data.
 
The guidance in this series of articles considers each type of AI workload generally and focuses on specific types, like language models, when special considerations for that type warrant dedicated guidance.

Some examples of workloads that aren't included in the scope of this series of articles are:

- Workloads that use [Azure AI Studio](https://azure.microsoft.com/products/ai-studio/) for their AI components. Studio is a SaaS that doesn't require the type of guidance that these articles provide.
- AI workloads that are realized through low-code and no-code offerings such as [Microsoft Copilot Studio](https://www.microsoft.com/microsoft-copilot/microsoft-copilot-studio).
- Workloads that require a [high performance computing](https://azure.microsoft.com/solutions/high-performance-computing/) component.
- Workloads that don't implement generative or discriminative AI use cases.

## What are the common challenges?

Microsoft Azure offers a broad range of AI services that you can build your workload around or integrate into an existing workload. You can choose between fully managed software as a service solutions (SaaS), platform as a service solutions (PaaS), or you can build your own AI solution using Azure compute services. While these options can make adopting AI technologies easier for your workload teams, there are challenges that will inevitably come up. Common challenges include:

- Building and running AI functions can be a costly endeavor. Some phases of your AI workload implementation will require a significant amount of compute resources, like model training, while other phases, like the day-to-day operation of the workload will require less. In other cases, like batch inferencing, your compute requirements may need to be scalable on a frequently recurring schedule. Understanding your compute requirements and finding the right compute service to meet those requirements can help you keep costs under control.

- Your organization may have security or compliance requirements that limit your options to adopt off-the-shelf services, like pre-trained models. In other cases, your use case may not be a good fit for a SaaS or PaaS service, so building your own solution may be the best option for your workload team. Its important to research all of your options to ensure that you're not creating unnecessary development and operational burden by building your own solution when an off-the-shelf solution is available to you.

- AI functions typically deal with very large amounts of data, often coming in multiple data types. Handling your data securely, efficiently, and cost effectively is critical to the overall workload. Challenges related to data include:

  - The volume of data needs to be determined to plan for the appropriate amount of resources.

  - The types of data you'll be working with need to be determined to ensure that you have the right kinds of resources to handle those types and you plan for any new processes that need to be developed. For example, you may have a fraud detection system that has been trained on training data, but processes real-time transaction data in production. Both types of data need to be handled in particular ways as the real-time data may have sensitive information that has special requirements.

  - The origins of the data will need to be identified to prepare pipelines for ingestion.

  - The security and compliance requirements for your data will need to be identified to implement proper enforcement measures.

  - The requirements for data preparation, cleansing and freshness need to be determined to ensure that proper automated and manual processes are designed.

- Testing is a critical part of any workload, but there are unique challenges inherent to AI functions. Models can become degraded over time, which leads to inaccurate outputs. Testing nondeterministic AI systems can be especially challenging due to their inherent randomness. They can pass sometimes and fail sometimes with no changes to obvious factors like the code or environment.

- If your organization is new to AI workloads, you'll need to invest in training and possibly new roles to support your workload. You may require data scientists, specialized developers, and other specialized roles in addition to new operations processes that may require extensive training.

- New technologies and new functions are released with incredible speed so it can be tempting to use the latest and most impressive technology to stay at the bleeding edge. However, by going that route, you may adopt a technology that provides a worse user experience overall for your customers, or is unnecessarily complex. 

- You need to clearly determine whether your use case is an ethical target for AI. The implications of putting an AI workload into production are not always immediately obvious, so careful consideration needs to be done throughout the planning and implementation phases to ensure that you are building a [responsible system](https://www.microsoft.com/en-us/ai/responsible-ai).

- Building a positive [user experience](https://online.stanford.edu/how-to-use-AI-to-enhance-user-experience#Avoid%20Overpromising%20and%20Under%20Delivering) is challenging due to the sometimes slow or unpredictable response time of models. Understanding different [user design patterns](https://uxdesign.cc/emerging-interaction-patterns-in-generative-ai-experiences-8c351bb3392a) is an important step in addressing user experience challenges.

## How to use this guidance?

**&#10004;** Start with [**Design Methodology**](./design-methodology.md), which outlines the rationale and recurring themes across technical and operational areas. This systematic approach helps define requirements and design strategies. Revisit this methodology when facing uncertain choices to stay aligned with the workload's overall goals. It also provides a framework for collaborating with marketing and sales teams to validate technical decisions and incorporate customer feedback for continuous improvement.

**&#10004;** Proceed to [**Design Principles**](./design-methodology.md) to see how the SaaS design methodology aligns with the core Well-Architected Framework pillars, considering growth evolution. Evaluate the underlying principles for all pillars collectively, including the tradeoffs.

**&#10004;** Focus on the **design areas** that have the biggest impact on your solution. Each area includes considerations and recommendations to guide you through the design decisions.

## Design areas

The following table describes some key design areas for building and operating AI workloads on Azure.

|Design areas|
|---|
|[Application design](./application-design.md): Learn about considerations unique to AI workloads that might have a significant effect on your existing application design standards. |
|[Application platform ](./application-platform.md): Determine the best platforms to use to support AI workload functions, like model hosting, model training, and inferencing. |
|[Training data design](./training-data-design.md): Design strategies for data ingestion, preprocessing, retention, and governance topics to handle your model training data. |
|[Grounding data design](./grounding-data-design.md): Design strategies to optimize searchability and retrieval, while meeting security and compliance requirements for your grounding data. |
|[Data platform ](./data-platform.md): Determine the best hosting platform to handle the large amounts and potentially many formats of data that your workload uses. |
|[Machine learning operations and Generative AI operations](./mlops-genaiops.md): Establish modern DevOps practices to support your machine learning or generative AI functions and systems. |
|[Workload operations](./operations.md): Modernize your operational practices with new approaches and adding specialized roles and training. |
|[Testing and evaluation](./testing.md): Develop testing and evaluation strategies to measure characteristics like accuracy, precision, sensistivity, and specificity through metrics that have been specifically targeted for AI workloads. |
|[Workload personas](./personas.md): Understand how personas are involved in the full lifecycle of your AI workload to help ensure that your team is fully capable of building and supporting it. |
|[Responsible AI](./responsible-ai.md): AI brings incredible opportunities for new products and services, but it also carries a considerable degree of risk. Pay special attention to the user experience and ethical implications of releasing your AI solution to the public. |


## Next step

Learn about design priciples for building and operating AI workloads on Azure.

> [!div class="nextstepaction"]
> [Design principles](./design-principles.md)

