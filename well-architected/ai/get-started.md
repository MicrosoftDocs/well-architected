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

- **Generative AI** are systems that have been pre-trained on a data model and can respond to prompts by autonomously generating novel outputs based on pattern analysis. Examples of generative AI are:
  - Workloads that leverage large language models (LLMs), like ChatGPT
  - Workloads that leverage Azure AI services, like Azure Open AI service
- **Deterministic AI (sometimes referred to as traditional AI)** relies on explicit programming to execute specific tasks. In this approach, developers create rules and algorithms that guide the AI system’s behavior. These predefined rules determine how the AI responds to input data. These systems are not trained on datasets, but can analyze datasets based on the rules and algorithms programmed for the job. These systems cannot generate new content or adapt autonomously. Examples of deterministic AI are:
  - Workloads that implement traditional AI algorithms at scale
  - Workloads that leverage machine learning models to derive intelligent insights
 
This is in comparison to a specialized role doing ad-hoc work in a Jypter notebook.

Generative AI creates new and original content (images, text, etc). Examples: Generate a picture of a cat on the moon. What are some good ways to maintain a credit history? What should I buy my grandma for her 90th birthday? Can you summarize the terms of service in olde english?
 
Predictive/discriminative AI uses patterns to forecase future outcomes or classify future events. Examples: What's the likelyhood this  is a cat? What's the likelyhood that you will default on a loan over the lone period? What's the next most likely thing someone like you would purchase? What's the likelyhood this message violates our terms of service?
The WAF AI workload guidance considers... 

- generative AI
  - workloads leveraging large language models
  - workloads leveraging Azure AI services 
- traditional AI
  - workloads implementing traditional AI algorithms at scale
  - workloads leveraging machine learning models to derive intelligent insights

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

## Common challenges

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


