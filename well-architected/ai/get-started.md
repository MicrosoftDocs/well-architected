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

Generally, AI workloads break down into two categories: generative AI and traditional AI.

**Generative AI** refers to workloads that are based on machine learning practices. These practices are designed to autonomously generate novel outputs rather than return results that the workload finds within the data model based on pattern analysis. Generative AI includes language models that you can pretrain on users' datasets or use like software as a service (SaaS), such as Azure OpenAI Service. 

GPT is a further subset of generative AI. GPT is a family of language models that specialize in mimicking human conversational language and are well-suited for chatbots, like Microsoft 365 Copilot or OpenAI's ChatGPT. 

You can use generative AI to:

  - Create content like articles, fiction, and artistic works.
  - Augment data by using automatically generated synthetic data that can augment existing, unbalanced datasets.
  - Make chatbots and virtual assistants more human-like.
    
**Traditional AI** relies on explicit programming to perform specific tasks. In this approach, developers create rules and algorithms that guide the AI system's behavior. These predefined rules determine how the AI responds to input data. You can categorize traditional AI by whether it's model based.

Model-based workloads are predictive machine learning systems that look for patterns in a model to return a specific type of prediction based on defined rules. These systems can't generate new content or adapt autonomously, but they're excellent at predicting outcomes.

Traditional AI systems that aren't model based include autonomous agents that you use to interact with an application or system, collect data, and use that data to perform predefined tasks. For example, characters in video games are agents that can move around in the game environment and react to game changes based on predefined rules. Developers program agents by using *optimization algorithms*, which find the best solution to a specific problem, or *search algorithms*, which find a specific target or fulfill a specific condition by searching within a dataset.

You can use traditional AI to:

  - Perform predictive analytics, which you can use in weather forecasting or stock market predictions.
  - Build recommendation systems, which e-commerce platforms use to recommend products to consumers based on their behavior and preferences.
  - Implement fraud detection, which can help you detect fraudulent activities based on behavior anomalies in vast amounts of data.
 
The guidance in this series of articles considers each type of AI workload generally and focuses on specific types, like language models, when special considerations for that type warrant dedicated guidance.

Some examples of workloads that aren't included in the scope of this series of articles are:

- Workloads that use [Azure AI Studio](https://azure.microsoft.com/products/ai-studio/) for their AI components. Studio is a SaaS that doesn't require the type of guidance that these articles provide.
- AI workloads that are realized through low-code and no-code offerings such as [Microsoft Copilot Studio](https://www.microsoft.com/microsoft-copilot/microsoft-copilot-studio).
- Workloads that require a [high performance computing](https://azure.microsoft.com/solutions/high-performance-computing/) component.
- Workloads that don't implement generative or traditional AI use cases.

## What are the common challenges?

- **Size your workload for training**: Training your workload on a model requires a significant amount of compute resources initially, but that amount decreases after the training is finished. After you train your system on a model, running the system requires less compute resources because it's only responding to prompts.
- **Size your workload for inference**: When you aren't running a model-based workload, you need a more consistent amount of compute power.
- **Buy versus build**: Determine whether an off-the-shelf solution, like Azure OpenAI, suits your requirements or whether you need to build your own solution.
- **Deal with data**: You need strategies to handle the following data scenarios:
  - You need to determine the volume of data to plan for the appropriate amount of resources.
  - You need to determine the types of data that you're working with to ensure that you have the right resources to handle that data. You also need to plan for new processes that you might need to develop. For example, you might have a fraud detection system that you trained on training data, but it processes real-time transaction data in production. You need to handle each type of data in specific ways because the real-time data might have sensitive information that has special requirements.
  - You need to identify the origins of the data to prepare pipelines for ingestion.
  - You need to identify the security and compliance requirements for your data to implement the proper enforcement measures.
  - You need to determine the requirements for data preparation, cleansing, and freshness to design the appropriate automated and manual processes. 
- **Test your workload**:
  - Testing your model while you build it helps you ensure that your solution produces predictable, valid, and useful results.
  - Testing nondeterministic AI systems can be especially challenging because they're inherently random. They can pass sometimes and fail sometimes with no changes to obvious factors like the code or environment. Use a large suite of test cases to help reduce the randomness.
- **Invest in new skills and roles**: If your organization is new to AI workloads, you need to invest in training and possibly new roles to support your workload. You might need data scientists, specialized developers, and other specialized roles in addition to new operational processes that might require extensive training.
- **Adopt the right technology for the best user experience**: New technologies and new functions are released quickly, so it can be tempting to use the latest and most impressive technology to stay current. However, by following this practice, you might adopt a technology that provides a worse experience for your customers. For instance, language models are increasingly more human-like in their conversational abilities, but a conversational interface might not be the best way for your customers to interact with your system. In this case, using the most advanced language model might not be a good investment.
- **Ensure your use case is ethical and responsible**: You need to clearly determine whether your use case is an ethical target for AI. The implications of putting an AI workload into production aren't always obvious, so consider what you need to do throughout the planning and implementation phases to ensure that you build a [responsible system](https://www.microsoft.com/ai/responsible-ai).
- **Improve your [customers' experience](https://online.stanford.edu/how-to-use-AI-to-enhance-user-experience#Avoid%20Overpromising%20and%20Under%20Delivering)**: Consider different [user design patterns](https://uxdesign.cc/emerging-interaction-patterns-in-generative-ai-experiences-8c351bb3392a) to account for the sometimes slow or unpredictable response time of AI models.

## What are the key design areas?

The following table describes some key design areas for building and operating AI workloads on Azure.

|Design area|Summary|
|---|---|
|[Application design](./application-design.md)| A discussion about application design choices based on how they pertain to AI workloads. Integration of AI workloads within traditional workloads requires unique design considerations that might have a significant effect on your existing application design standards. |
|[Application platform ](./application-platform.md)| A discussion about application platform choices for AI workloads. AI workloads often have different platform requirements than traditional apps, and adopting those platforms for seamless integration into your environments requires careful planning. |
|[Data design](./data-design.md)| A discussion about data design based on how it pertains to AI workloads. AI workloads use data in new ways. Handling the data that's involved in your AI workload might require a new perspective and consideration of new challenges and requirements. |
|[Data platform ](./data-platform.md)| A discussion about data platform choices and considerations for AI workloads. The platform that you use to handle your AI workload might have unique requirements based on the functionality of your app. |
|[Machine learning operations and language model operations](./mlops-llmops.md)| A discussion about machine learning operations and language model operations, which are DevOps practices specifically for AI workloads. AI workloads have unique properties that you need to understand to build new continuous integration and continuous delivery practices for your workload. Machine learning and language model deployments can be more complex than traditional application deployments, so you might need to adopt new DevOps practices or update them significantly. |
|[Workload operations](./operations.md)| A discussion about operations considerations based on how they relate to AI workloads. Operational activities for AI workloads can be a significant change for organizations that are new to AI. Your organization might need to take new approaches and consider adding specialized roles and training. |
|[Testing and validation](./testing.md)| A discussion about testing and validation considerations for AI workloads. Testing and validating your AI workload can be notably different than testing traditional apps. You might need to adopt new testing practices and consider new choices to meet your requirements. |
|[Workload personas](./personas.md)| A discussion about personas that might interact with your AI workload. While you build an AI workload, you need to plan for personas and understand unique considerations that might change your design standards. |
|[User input and ethics](./userinput-ethics-security.md)| A discussion about user input and ethics based on how they relate to AI workloads. AI brings incredible opportunities for new products and services, but it also carries a considerable degree of risk. You must pay special attention to the user experience and ethical implications of releasing your AI solution to the public. |

## Next step

Learn about design priciples for building and operating AI workloads on Azure.

> [!div class="nextstepaction"]
> [Design principles](./design-principles.md)
