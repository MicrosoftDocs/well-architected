---
title: Application design for AI workloads on Azure
description: Code deployed on the resources.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 04/15/2024
ms.topic: conceptual
ms.service: azure-waf
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


-------------------------------------------

## DUMP ZONE

@Clayton, @Chad, @Jose. These concepts were discussed during the RAI content development and weren't suitable for that design area. In internal discussions, these were more suitable for app design. See reuse as you see fit.

### User experience performance

- **Monitor UI experience**. Engagement metrics are a good way to efficacy of the user experience. Increased time on a page can indicate either positive engagement or user frustration. There might be expected or unexpected latency issues. For example, with multimodal capabilities, such as those providing responses through voice or video, latency can be significant, leading to longer response times. If there's increased time, consider providing immediate feedback to the user, which can improve user satisfaction.   


### Encrypt data


- **Data at rest**

    At a minimum, data should be encrypted using platform-level features like Microsoft-managed keys. For added security, Azure offers customer-managed keys, so that only you as the owner can decrypt the data. Additionally, data can be double encrypted, meaning Azure retains a key for extra protection.
    
    Platform-level encryption ensures data is protected while at rest, but once accessed via an API, the data is decrypted and available in plain text.
    
    Key rest points for data include aggregated data stores, such as data lakes, and the model itself, which combines data and compute.

    @Chad/@Jose Homework 1: Customer managed key in Azure OpenAI, does it affect the model. 

- **Data in transit**

    For data in transit, all platform services support HTTPS traffic. If you host your own models and endpoints, avoid using HTTP. Instead, use HTTPS, which is practical and provides essential encryption.

    As data moves through various hops, decide whether to encrypt/decrypt at each point. Network intermediaries, like API gateways, need to decrypt the payload in order to process it.  API gateways typically terminate TLS connection, decrypt the payload, and then re-encrypt it for backend connections. 

- **Data in use**

    The preceding options are generally sufficient for most workloads where data is encrypted in transit and at rest, but it's decrypted for processing, such as for training models, fine tuning, or reading from an index. 
    
    Homomorphic encryption enables inferencing on fully encrypted data, ensuring the highest level of security. Data remains encrypted as it flows through various components. This specialized mechanism goes beyond traditional TLS, preventing intermediary points from inspecting the encrypted data. This design is appropriate for highly trusted scenarios that requires a closed system. 
    
    However, there's a tradeoff on the cost and accuracy. You can't analyze, inspect, or log the encrypted data. Also, content safety checks can't be performed on the encrypted data. In less sensitive cases, accuracy might be prioritized over this level of encryption. In highly secured environments with extra layers of encryption, achieving explainability can be challenging due to the lack of transparency.
	
	
### Access management:

Several types of identities can potentially access user data or user identities that influence inferencing need to be preserved. This requirement might need complex workload design. Or, the case where system reliability engineers need appropriate level of access when needed, for operational purposes. Additionally, internal components and external systems may communicate with the model. Therefore, robust access control must address: 

-  **Data access**. Implement Role-Based Access Control (RBAC) for both the control plane and data plane, covering users and system-to-system communication.

-  **User segmentation**. Maintain proper user segmentation to protect privacy. For example, Copilot can search and provide answers based on a user's specific documents and emails, ensuring only content relevant to that user is accessed.

@chad/jose: homework 2: What does this design actually look like. Perhaps a diagram in the app design and then show access controls in it. This is from Jose:

When indexing documents for multiple users using productivity tools like SharePoint, access control can be done through RBAC or Attribute-Based Access Control (ABAC), depending on complexity. While RBAC may suffice in some cases, ABAC might be necessary for more complex requirements. The specific approach depends on the actual requirements.

When a user initiates a call to an index, implementing RBAC is straightforward. However, if an orchestrator handles the request and makes subsequent calls to various indexes and models, the user's identity must be preserved throughout these interactions. This ensures that access permissions are correctly enforced, allowing the user to access only the documents they are authorized to view.

### Enforce security on user access to APIs and data**

A fundamental strategy of the Well-Architected Framework Security pillar is minimizing the attack surface and harden resources. That strategy should be applied to standard endpoint security practices by tightly controlling API endpoints, exposing only essential data, and avoiding extraneous information in responses. The design choice should be balanced between flexibility and control. Here are some check points:

- **All inferencing endpoints must require user authentication**, even for system-to-system calls. Do not have anonymous endpoints. 

- **Restrict the API surface**. For example, if the client sends prompts and hyperparameters, the larger surface requires analyzing prompts to prevent jailbreaking. Instead, reduce flexibility by having the client send minimal data, with the server constructing prompts using predefined templates. This approach minimizes user-injected security risks.

- **Design a constrained API at the cost of client-side flexibility**. If the SDK is client-side and calls your API, it offers flexibility but lacks control. Using a custom API with only necessary fields, where your API handles the SDK functions, increases security by limiting client-side flexibility. 

    In general, avoid giving clients more control than necessary. In most scenarios, clients don't need to adjust hyperparameters except in experimental environments. For typical use cases, such as interacting with a virtual agent, clients should only control essential aspects to ensure security by limiting unnecessary control.

