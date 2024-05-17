---
title: AI workloads on Azure
description: Architectural considerations for building and operating AI systems on Azure.
author: danielstocker
ms.author: prwilk
ms.date: 04/15/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-ai
---

# AI workloads on Azure

This guidance is intended for **workload owners** and **technical stakeholders**, including architects, development leads and other IT leadership roles. Specialized roles like data scientists or developers are not the target audience of this guidance, but workload owners and technical stakeholders should be able to help guide those roles through development and deployment through their learnings. 

This documentation provides actionable and authoritative guidance for building and operating a highly reliable, scalable solution on Azure. The guidance has a technical foundation in Azure Well-Architected Framework best practices. It's also based on reviews of numerous customer implementations. 

## What is an AI workload in the context of WAF?

An AI workload is one that designs a particular portion of its functionality around artificial intelligence technology for predictive, discrimatory or generative use cases. Sometimes this replacing a traditional code logic and data access solution that would be otherwise deterministic. Sometimes that is producing an experience that cannot be accomplished with determinism and classical code & data model. Artificial intelligence combines code logic and data into a single package.

Generally AI workloads break down into two categories:

**Generative AI** refers to workloads that are based on machine learning (ML) practices, that are designed to autonomously generate novel outputs rather than return results found within the data model based on pattern analysis. Generative AI includes small language models (SLMs) and large language models (LLMs), that can be pre-trained on users' own datasets or can be used like a SaaS service like Azure OpenAI. A further subset of generative AI is generative pre-trained transformer (GPT), which is a family of LLMs that specialize in mimicing human conversational language and are especially well-suited for chatbots, like Microsoft Copilot or OpenAI's ChatGPT. 

Examples of generative AI use cases are:

  - Content creation like articles, fiction, and artistic works
  - Data augmentation like automatically generated synthetic data that can augemnt existing data sets, when dealing with unbalanced data sets
  - Chatbots and virtual assistants can be made more human-like
    
**Traditional AI** relies on explicit programming to execute specific tasks. In this approach, developers create rules and algorithms that guide the AI system’s behavior. These predefined rules determine how the AI responds to input data. Tradtional AI can be further categorized by whether they are model based or not.

Model based workloads are predictive ML systems that look for patterns in a model to return a specific type of prediction, based on the rules defined for it. These systems cannot generate new content or adapt autonomously, but are excellent at predicting outcomes. 

On the other hand, traditional AI systems that are not model based include autonomous agents that are used to interact with an application or system, collect data, and use the data to perform pre-defined tasks. For example, characters in video games are agents that can move around in the game environment and react to game changes based on pre-defined rules. Agents can be programmed with *optimization algorithms*, which are used to find the best solution to a specific problem, or *search algorithms*, which are used to find a specific target or fulfill a specific condition by searching within a data set.

Examples of traditional AI use cases are:

  - Predictive analytics, which is used in weather forecasting or stock market predictions
  - Recommendation systems, which are commonly used by e-commerce platforms to recommend products to consumers based on user behavior and preferences
  - Fraud detection, which can help detect fraudulent activities based on finding anomolies in behavior amongst vast amounts of data
 
The guidance in this series considers AI workloads of each type in general and will focus on specific types, like LLMs, when special considerations for that type warrant dedicated sections.

Some examples of workloads that aren't included in the scope of this workload are:

- Workload that leverage [Azure AI Studio](https://azure.microsoft.com/products/ai-studio/) for their AI components, which is a SaaS service that doesn't require the types of guidance provided in this series
- AI workloads that are realized through Low Code and No Code offerings such as [Microsoft Copilot Studio](https://www.microsoft.com/microsoft-copilot/microsoft-copilot-studio)
- Workloads that require a [high performance computing](https://azure.microsoft.com/solutions/high-performance-computing/) component
- Workloads that do not implement generative or traditional AI use cases

## What are the common challenges?

- **Size your workload for training**: Training your workload on a model takes a significant amount of compute resources initially, but that need decreases after the training has been completed. After you've trained your system on a model, running the system requires less compute resources as it is only responding to prompts at that point.
- **Size your workload for inference**: In cases that you won't be running a model based workload, you will need a more consistent amount of compute power.
- **Build vs buy**: Determine whether an off-the-shelf solution, like Azure OpenAI, will suit your requirements or you will need to build your own solution.
- **Deal with data**: You'll need strategies to handle multiple possible issues like:
  - The volume of data needs to be determined to plan for the appropriate amount of resources
  - The types of data you'll be working with need to be determined to ensure that you have the right kinds of resources to handle those types and you plan for any new processes that need to be developed. For example, you may have a fraud detection system that has been trained on training data, but processes real-time transaction data in production. Both types of data need to be handled in particular ways as the real-time data may have sensitive information that has special requirements.
  - The origins of the data will need to be identified to prepare pipelines for ingestion
  - The security and compliance requirements for your data will need to be identified to implement proper enforcement measures
  - The requirements for data preparation, cleansing and freshness need to be determined to ensure that proper automated and manual processes are designed.  
- **Test your workload**
  - Testing your model while you build it, helps you ensure that your solution will produce predictable, valid, and useful results.
  - Testing nondeterministic AI systems can be especially challenging due to their inherent randomness. They can pass sometimes and fail sometimes with no changes to obvious factors like the code or environment. Using a large suite of test cases can help reduce the randomness.
- **New skills and roles**: If your organization is new to AI workloads, you'll need to invest in training and possibly new roles to support your workload. You may require data scientists, specialized developers, and other specialized roles in addition to new operations processes that may require extensive training.
- **Adopt the right technology for the best user experience**: New technologies and new functions are released with incredible speed so it can be tempting to use the latest and most impressive technology to stay at the bleeding edge. However, by going that route, you may adopt a technology that provides a worse user experience overall for your customers. For instance, LLMs are increasingly more human-like in their conversational abilities, but a conversational interface might not be the best way for your customers to interact with your system, so using the best LLM for conversational interactions might not be the best investment.
- **Ethics and responsibility**: You need to clearly determine whether your use case is an ethical target for AI. The implications of putting an AI workload into production are not always immediately obvious, so careful consideration needs to be done throughout the planning and implementation phases to ensure that you are building a [responsible system](https://www.microsoft.com/en-us/ai/responsible-ai).
[User experience considerations](https://online.stanford.edu/how-to-use-AI-to-enhance-user-experience#Avoid%20Overpromising%20and%20Under%20Delivering)
- different [user design patterns](https://uxdesign.cc/emerging-interaction-patterns-in-generative-ai-experiences-8c351bb3392a) emerge that account for the sometimes slow or unpredictable response time of models

## Design area considerations

TBD

|Design area|Summary|
|---|---|
|[Application design](./application-design.md)| . |
|[Application platform ](./application-platform.md)| . |
|[Data design](./data-design.md)| . |
|[Data platform ](./data-platform.md)| . |
|[MLOps and LLMOps](./mlops-llmops.md)| .|
|[Workload operations](./operations.md)| .|
|[Testing and validation](./testing.md)| . |
|[Personas](./personas.md)| . |
|[User input and ethics](./operations.md)| .|


