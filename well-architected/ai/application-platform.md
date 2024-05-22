---
title: Application platform for AI workloads on Azure
description: Azure resources needed for running AI workloads.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 04/15/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-ai
---

# Application platform for AI workloads on Azure

- Dealing with latency of AI and ML models
    - API load balancing and charge back
- Dealing with large amounts of requests
    - Front Door and Compute options for AI applications that run at scale
- Choosing between packaged AI services, Azure Open AI or Custom Model
- Redundancy and Scalability of the different options
- Managing user identity and privacy at the application level
- Considerations for front end and headless apps
- Deployment and operational considerations

## Landing Zone and Networking

- GenAI Scaling AI Services with Landing Zone
    - OpenAI Landing Zone https://techcommunity.microsoft.com/t5/azure-architecture-blog/azure-openai-landing-zone-reference-architecture/ba-p/3882102
    - OpenAI LZ Accelerator https://github.com/Azure/azure-openai-landing-zone
- Traditional ML
    - Scale Secure ML https://learn.microsoft.com/en-us/azure/architecture/example-scenario/ai/scale-ai-and-machine-learning-in-regulated-industries
    - Secure AML end to end
## Model Deployments
-  GenAI
    - PayGo, PTU, Global  https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/deployment-types 
-  Traditional ML
-  AI PAAS Services

## Monitoring
