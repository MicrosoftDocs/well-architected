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

Topics:

- Model endpoint code
  - How do you pick between exposing real-time, streaming, or batch inferencing APIs?
  - API SDKs
  - Designing for observability
  - Supporting stateful experiences
  - Building scalable model hosting code
  - How does Azure help?
  - Containerization?

- When do you design using an orchestrator/middleware vs direct client access to models?
  
- Recommendations specific to orchestrators
  - Error handling (setting client expectations)
  - How do you select orchestrator/middleware language/SDK?
  - How does Azure help?
  - Building scalable code
  - Supporting stateful experiences
  - Containerization?
  - Grounding/augmentative data access (dependencies)

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
