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
- Inferencing compute
- Monitoring (required)

## Elena's (SME & area lead) seed material

### Landing Zone 

- GenAI Scaling AI Services with Landing Zone
    - Chat Baseline Architecture: https://learn.microsoft.com/en-us/azure/architecture/ai-ml/architecture/baseline-openai-e2e-chat  
    - OpenAI Landing Zone https://techcommunity.microsoft.com/t5/azure-architecture-blog/azure-openai-landing-zone-reference-architecture/ba-p/3882102 
    - OpenAI LZ Accelerator https://github.com/Azure/azure-openai-landing-zone
- Traditional ML
    - Scale Secure ML https://learn.microsoft.com/en-us/azure/architecture/example-scenario/ai/scale-ai-and-machine-learning-in-regulated-industries
    - Organize ML workspaces: https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-resource-organization
    - Secure AML end to end https://learn.microsoft.com/en-us/azure/machine-learning/how-to-secure-workspace-vnet?view=azureml-api-2&tabs=required%2Cpe%2Ccli and https://learn.microsoft.com/en-us/samples/azure/azure-quickstart-templates/machine-learning-end-to-end-secure/

### Networking
- Private Network egress/ingress , Private endpoints
- DNS
- DDOS and Filtering

### RBAC Security

### Reliability / HA
 - Regional resources avilability

### Team vs Platform Team resources
- Resources to be provisioned and managed by App Platform Team
      
### GenAI Model Deployments

- Deployments schoice: PayGo, PTU, Global  https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/deployment-types
- MAAS vs MAAP - hosting catalog models as Service, as Platform - considerations when what

- GenAI appp hosting  including plugins, code interpterter, promptflow 

### Model Catalog and Registries
 - Scanning
 - Policies for management

#### OpenAI Scalability with APIM

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
- Fine Tuning
- Hosting
  - On the Edge
  - Containerization
  - WebNN
  - Acceleration : https://techcommunity.microsoft.com/t5/azure-high-performance-computing/introducing-the-new-azure-ai-infrastructure-vm-series-nd-mi300x/ba-p/4145152
       
### Traditional ML Deployments

-  AI PAAS Services
-  Trained models on Managed Endpoints, AKS, Custom container
  
### Monitoring

- Tracing with OpenTelemetry - new SDK and PromptFlow Tracing

## Jose's (Azure Patterns & Practices engineering) seed material

App Platform

Training vs. Inference endpoints. 
  - These have different purposes and also platform requirements (scalability, security, performance). Inference will be driven by the number of concurrent users/requests. Training by amount of data, type of workload, frequency
  - Recommendations for keeping these separate and/or reusing some solution components.
  - How is this impacted by model tuning and updates over time?
  - Publishing updates

Batch inference endpoints.
  - Benefits of batch inference: long running, async, parallelize 
  
Recommendations to choose between training a model, using a pretrained model, or fine tuning a pretrained model.
  - For pretrained models: how do I evaluate pick the right model. E.g. choose between OpenAI models 
  - Examples: ML is the most flexible to train your own models. OpenAI gives you a selection of pretrained models. Cognitive services allow limited training.
  - What is the role of grounding and the RAG pattern here?
  - Considerations/tradeoffs of fine-tuning vs grounding/prompt engineering
  - Cost of training considerations.

Foundational models
  - Azure ML Model Catalog. How to choose a model. Model evaluation and fine tuning.
  - Hosting foundational models: Managed compute, Serverless API
  
Considerations to share a deployment among multiple workloads (apps) and departments, or sharing for multiple uses (completion vs sentiment analysis)
  - Chargeback issues
  - Performance tuning (e.g. less efficient use of caching by the service)
  - Open AI: when is it better to share a deployment of OpenAI across workloads and charge back, vs locally managed
  
What functions can be offloaded to a reverse proxy/gateway model like APIM when using Azure PaaS AI Services: Retries (timeouts, throttling)
  - Chargeback
  - Load balancing
  - Security (network isolation, identity authentication with Entra ID)
  - Monitoring 
  - Other:  https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/azure-openai-gateway-guide, https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/azure-openai-gateway-multi-backend
  
Training platform
  - Minimize data access latency
  - How to pick library/framework to use for training (PyTorch, Tensorflow, etc.). Considerations other than functionality?
  - Recommendations for training compute selection and configuration. Azure Machine Learning Environments (https://learn.microsoft.com/en-us/azure/machine-learning/concept-environments?view=azureml-api-2&viewFallbackFrom=azureml-api-1)
  - Considerations for Initial training vs re-training: 

Where to host inference endpoint
  - Options: VMs, ML, K8, App Service, Function Apps, Container Apps
  - How to choose a hosting platform. Technical requirements that drive decision: reliability, security, scalability, performance
  - Integrated ML Solutions. When to choose these: Azure Synapse Analytics, Databricks, and Apache Spark.  
    ○ Use for inference vs. training

Orchestration at the inference endpoint
  - Custom code: Python, C#, Java
  - Libraries/runtimes: Semantic Kernel LangChain. 
-	Link: https://learn.microsoft.com/en-us/semantic-kernel/overview/?tabs=Csharp#why-use-an-sdk-like-semantic-kernel. Also: https://learn.microsoft.com/en-us/semantic-kernel/agents/plugins
  - Example: calling multiple models/inference endpoints to fulfill an app requirement: 
-	OCR + Summarization
-	Speech to text + summarization + …
-	Example: RAG pattern. 
-	Multiple/orchestrated calls to various endpoints. Need to process user input to understand intent, enrich and add context to search for grounding data, interact with model, generate responses.
-	Example: chat/RAG pattern with Actions
  - Prompt engineering

Considerations for performance
  - Choosing the right hardware, OS, model, GPUs, etc.
  - Open AI: choosing the right quota TPM/RPM and TPUs
  - How to make more efficient use of the inferencing capacity: caching, batching, dedicated endpoints based on usage purpose (e.g. completion vs. sentiment analysis)

Considerations for scaling
  - Dealing with limitation in the supported RPS by having multiple instances. For example, OpenAI and Azure Cognitive Services deployments may have lower supported RPS that needed by the workload.
  - Scaling up vs scaling out. Type of compute, GPUs, FPGAs

Considerations for Monitoring
  - Monitoring technical requirements (perf, reliability) vs. functional (accuracy, groundlessness)

Considerations for cost optimization
  - Choosing the most cost-effective model when leveraging a pre-trained/foundational model
  - Using multiple models for a task to save on cost. Try less expensive first, escalate to next model on failure.

## Chad's (Azure Patterns & Practices engineering) seed material

### Training platform

- Training ML models is typically a batch processing job requiring access to batch and distributed processing compute, typically with ephemeral infrastructure. The load for that process is significant, but has a end of life after the model is trained. Your compute platform to perform model training should be built with an ephemeral nature in mind.

- GPU vs CPU based training platform

- What are the considerations for training platfrom to training data access (letency, bandwidth).  Co-location vs seperation.

- Your platform may have specific SDKs to use

#### Training tooling

- scikit-learn
- Spark MLlib
- hyperpot
- xgaboost
- TensorFlow
  - Keras, and PyTorch
  - Horovod
  - Petastorm
- AutoML Python SDK
- Where do notebooks fit into this?
- SynapseML

#### Model training Azure facilitation

- Azure Databricks
- Azure Synapse with Spark Pools
  - Apache Spark for Azure Synapse
  - Serverless

#### Foundation model training

- Finetuning compute
- `databricks_genai`
- Databricks [Foundation Model Training API](https://learn.microsoft.com/en-us/azure/databricks/large-language-models/foundation-model-training/create-fine-tune-run)

### Model serving

You need a platform to serve trained models through the Rest APIs that you developed as part of [application design](./application-design.md). Custom models, foundation models, etc.

When do you need GPU for model serving?

Your model serving platform must provide the following capabilities:

- Support an elastic backend with scaling that matches your inferencing needs.
- Minimize latency in processing inferencing requests
- Be reliable (spread across availability zones, etc)
- Keep private data/services private through limiting network access
- Need a way to deploy updated models
- Need to monitor usage
- Need to authorize usage

- Must align to your workload or organizational requirements around standard procedures
  - such as patching, network isolation, network egress control, etc.
  - vnet isolation
  - keep container images UTD

Potentially important features
- Ability to break down costs or usage by endpoint deployment
- Host versioned APIS (and support A/B deployments)
- Traffic mirroring (to test a green model before it goes live.)
- Debugging capabilities
- Local develop experience
- Secret management
- Abuse prevention/detection

- Build vs buy

Another option is in-situ such as TSQL PREDICT right along side of your data.

#### Hosting modalities

- out-of-the-box hosting with things like MLflow models (ONNX, etc). -- A "no-code" approach.
- low-code approach is using pre-built container images, but you provide the scoring code
- full-code, your images (hosted in your own container registry), you bring all your own dependencies (in the container image), and full scoring code
- full PaaS (Azure OpenAI, I think, is the only one of these?), but requires all logic external to the host
- full self hosting (Flask + TensorFlow (or simliar) on VMs)

#### Realtime vs batch

Most inferecing is syncrhonous. However, asynchronous needs are are possible, and that is where batch API and batch processing comes in. Designed for parallelization.

#### Model serving Azure facilitation

- Azure Databricks Model Serving
- Azure Synapse SQL Pools (`TSQL PREDICT`)
- Azure Machine Learning
  - Triton Inference Server (managed online endpoints and kubernetes online endpoints)
  - Managed online endpoints
  - Kubernetes online endpoints

### Client hosting

- Keep UI and API code unrelated to direct inferencing isolated and off of the compute for inferencing.  This could be client code (meaning, don't load and execute models in your application's API, build specific hosting for the models and expose their own API)
- Orchestration
  - This requires compute selection to use an SDK like Semantic Kernel or LangChan.

PromptFlow hosting:

- In AML studio
- On your own compute
