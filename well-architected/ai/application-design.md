---
title: Application design for AI workloads on Azure
description: Code deployed on the resources.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 04/15/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-ai
---

# Application design for AI workloads on Azure

Scope:

- Code deployed on the client
- Code deployed in any middleware (such as orchestrators) that calls to models (e.g. classification, language, vision, etc).
- Model hosting code (APIs)

Out of scope:

- Code for model training
- Code for RAG database population

Topics:

- Model endpoint code
  - How do you pick between exposing real-time, streaming, or batch inferencing APIs?
  - API SDKs
  - Designing for observability
  - Supporting stateful experiences
  - Building scalable model hosting code
  - How does Azure help?
  - Containerization?
  - What is the API surface?

- When do you design using an orchestrator/middleware vs direct client access to models?
  
- Recommendations specific to orchestrators
  - Error handling (setting client expectations)
  - How do you select orchestrator/middleware language/SDK?
  - How does Azure help?
  - Building scalable code
  - Supporting stateful experiences
  - Containerization?
  - Grounding/augmentative data access (dependencies)
  - What is the API surface?

- Recommendations specific to clients:

  - going directly to models
    - E.g. failover, loadblancing, self-preservation
    - Language and SDK considerations
    - How does Azure help?
  - going to orchestrators
    - E.g. failover, loadblancing, self-preservation
    - Language and SDK considerations
    - How does Azure help?

- Working with long running AI jobs gracefully in an application
- Dealing with latency on a user experience level
- Programming Patterns (not sure whether to include)
  - Implementing the RAG pattern 
  - Implementing Machine Learning algorithms
  - Optimization of classical AI code

- Efficiency considerations for classical AI problems
- Efficiency considerations for working with models
- Planning Prompts 
- Prompt Engineering
- Prompt Evaluation
- Avoiding Jailbreak
- Orchestration (not sure if this should include direct SDK references – examples below)
  - Semantic Kernel & Langchain

## Monitoring


## Leftover

 Data / LLM Education
	
Do user get an introduction on the the expected quality of the chat system, the likelyhood that AI creates wrong content, and limitation of what kind of questions can be answered?
	
Standart RAG architecture cannot answer questions like "how many documents have this feature?" Consider a GraphRAG architecture instead
	

## Measurement

Groundedness: Refers to how well a generative model’s answers align with the source data. It measures whether the model generates content that is consistent with reality.
Relevancy: Indicates how pertinent the response is to the given question. A highly grounded answer may still lack relevancy if it doesn’t address the question directly.
Coherence: Evaluates whether the model’s speech flows naturally and coherently. It assesses whether the conversation feels like a genuine exchange.
Fluency: Relates to the vocabulary usage. If the model adheres to a style guide and presents content in the appropriate format, it can be fluent even if it lacks grounding or relevancy.
Retrieval Score: Measures the effectiveness of the orchestrator’s retrieval process. It considers whether the query to the index retrieved the most relevant data.



Data design and application design cannot be decoupled. Application design involves understanding use cases, query patterns, and freshness requirements. From a data design perspective, consider whether data needs constant refreshing or occasional updates. This choice impacts the ability to do online or offline inferencing.

In offline inferencing, predictions are based on precomputed data that's stored in look-up tables. Because results are determined ahead of time, look up has minimal latency. Another advantage is that you can validate (or even tweak) predictions before  production.

For example, consider a computer vision model trained to identify cats in pictures. These models remain effective as long as the underlying data is relatively static and doesn't require doesn't require frequent updates or real-time adjustments. Offline inferencing often relies on static training.

In contrast, for online inferencing, there aren't any precomputed predictions. Predictions are made in real time by invoking models on-demand, responding to specific queries or events. Data freshness becomes critical in this case because models need to adapt quickly to changing patterns. Keeping such models up-to-date is required to maintain accuracy. TODO: Add something around low latency requirements. 

For example, models trained on user behavior, such as website interactions, need dynamic data. The model should be able to adapt the website behavior based on recent user actions.  