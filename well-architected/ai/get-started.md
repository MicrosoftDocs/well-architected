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

- **Generative AI** refers to workloads that are based on machine learning (ML) practices, that are designed to autonomously generate novel outputs rather than return results found within the data model based on pattern analysis. Generative AI includes small language models (SLMs) and large language models (LLMs), that can be pre-trained on users' own datasets or can be used like a SaaS service like Azure OpenAI. A further subset of generative AI is generative pre-trained transformer (GPT), which is a family of LLMs that specialize in mimicing human conversational language and are especially well-suited for chatbots, like Microsoft Copilot or OpenAI's ChatGPT. Examples of generative AI use cases are:
  - Content creation like articles, fiction, and artistic works
  - Data augmentation like automatically generated synthetic data that can augemnt existing data sets, when dealing with unbalanced data sets
  - Chatbots and virtual assistants can be made more human-like
    
- **Traditional AI** relies on explicit programming to execute specific tasks. In this approach, developers create rules and algorithms that guide the AI system’s behavior. These predefined rules determine how the AI responds to input data. Tradtional AI can be further categorized by whether they are model based or not.

Model based workloads are predictive ML systems that look for patterns in a model to return a specific type of prediction, based on the rules defined for it. These systems cannot generate new content or adapt autonomously, but are excellent at predicting outcomes. 

On the other hand, traditional AI systems that are not model based include autonomous agents that are used to interact with an application or system, collect data, and use the data to perform pre-defined tasks. For example, characters in video games are agents that can move around in the game environment and react to game changes based on pre-defined rules. Agents can be programmed with optimization algorithms, which are used to find the best solution to a specific problem, or search algorithms, which are used to find a specific target or fulfill a specific condition by searching within a data set.

Examples of traditional AI use cases are:
  - Predictive analytics, which is used in weather forecasting or stock market predictions
  - Recommendation systems, which are commonly used by e-commerce platforms to recommend products to consumers based on user behavior and preferences
  - Fraud detection, which can help detect fraudulent activities based on finding anomolies in behavior amongst vast amounts of data
 
The WAF AI workload guidance considers... 

...workloads deployed in Azure as being in scope.

Some examples of workloads that aren't included in the scope of this workload are:

- Workload that leverage [Azure AI Studio](https://azure.microsoft.com/products/ai-studio/) for their AI components
- AI workloads that are realized through Low Code and No Code offerings such as [Microsoft Copilot Studio](https://www.microsoft.com/microsoft-copilot/microsoft-copilot-studio)
- Workloads that require a [high performance computing](https://azure.microsoft.com/solutions/high-performance-computing/) component
- Workloads that do not implement generative or traditional AI use cases

## What is the difference between traditional AI, machine learning, optimization algorithms, and generative AI?

Comparison between
[Traditional AI VS Generative AI](https://visionx.io/blog/traditional-ai-vs-generative-ai/)

Generative AI's large language models have their origins in more traditional machine learning practices. 
Large language models are just one type of machine learning implementation that help solve customer problems.
Optimization algorithms are a compute-heavy machine learning-centric alternative that helps solve specific problems.
[Optimization Algorithms](https://machinelearningmastery.com/tour-of-optimization-algorithms/)

Optimization alogrithms could be described as highly specialized traditional AI algorithms. 

[Other Machine Learning use cases](https://www.codecademy.com/resources/blog/machine-learning-examples/)

## What are the common challenges?

- Size your workload for training
- Size your workload for inference
- 
Skills and roles required in a team to solve an AI problem

[User experience considerations](https://online.stanford.edu/how-to-use-AI-to-enhance-user-experience#Avoid%20Overpromising%20and%20Under%20Delivering)
- orgs often default to chat interface without asking if that's the best possible user experience
- orgs often want to augment existing systems but don't know what the most efficient way to do so is
- different [user design patterns](https://uxdesign.cc/emerging-interaction-patterns-in-generative-ai-experiences-8c351bb3392a) emerge that account for the sometimes slow or unpredictable response time of models
- companies need to understand that just because a large language model is able to be conversational a conversational interface isn't always the best interface to use

[Ethical and responsible use of AI](https://news.harvard.edu/gazette/story/2020/10/ethical-concerns-mount-as-ai-takes-bigger-decision-making-role/)
- companies and organizations need to be clear as to whether a use case is an ethical target for AI
- it's often not immediately obvious what implications an AI use case might have
  - example: Should we create an AI therapist if that helps users with their mental health? Even if they know that they are talking to an AI, should they feel comfort in entrusting their deepest darkest secrets to a chat agent? What implications would this have for the profession and the licensed industry more boradly?

Efficient use of AI
- just because generative AI is an option should it be used?
- just because chat is an input method should it be chosen?
- [What are people really using AI for](https://hbr.org/2024/03/how-people-are-really-using-genai)


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


