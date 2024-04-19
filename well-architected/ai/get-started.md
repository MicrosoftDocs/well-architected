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

## What is an AI workload in the context of WAF?

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


