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
- Choosing between packaged AI services, Azure Open AI or Custom Model - https://learn.microsoft.com/en-us/azure/architecture/data-guide/technology-choices/cognitive-services
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
    - Organize ML workspaces: https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-resource-organization
    - Secure AML end to end https://learn.microsoft.com/en-us/azure/machine-learning/how-to-secure-workspace-vnet?view=azureml-api-2&tabs=required%2Cpe%2Ccli and https://learn.microsoft.com/en-us/samples/azure/azure-quickstart-templates/machine-learning-end-to-end-secure/
      
## GenAI Model Deployments
- Deployments schoice: PayGo, PTU, Global  https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/deployment-types

### OpenAI Scalability with APIM

- Gateway in front of multiple OAI https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/azure-openai-gateway-multi-backend   
- GenAI API Gateway Capabilities https://techcommunity.microsoft.com/t5/azure-integration-services-blog/introducing-genai-gateway-capabilities-in-azure-api-management/ba-p/4146525
- GenAI API Gateway Labs: https://github.com/Azure-Samples/AI-Gateway
- https://github.com/microsoft/AzureOpenAI-with-APIM
 - Highlight LB, Logging, Sematic Caching to speedup OAI
       
### Chargebacks
 - New Policy Azure OpenAI Emit Token Metric Policy
 - Granular Chargebacks
 - Chargebacks https://techcommunity.microsoft.com/t5/apps-on-azure-blog/calculating-chargebacks-for-business-units-projects-utilizing-a/ba-p/3909202 and source  https://github.com/Azure-Samples/private-openai-with-apim-for-chargeback/tree/main and https://github.com/shksin/azure-openai-with-apim-for-chargeback

### Small Language Models
 - On the Edge
 - Containerization
 - WebNN
 - Acceleration : https://techcommunity.microsoft.com/t5/azure-high-performance-computing/introducing-the-new-azure-ai-infrastructure-vm-series-nd-mi300x/ba-p/4145152
     
## Traditional ML Deployments
-  AI PAAS Services

## Monitoring
 - Tracing with OpenTelemetry
