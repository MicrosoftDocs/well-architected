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

The application hosting platform that your AI workload is deployed on must be carefully considered to ensure that your application runs efficiently, securely, and reliably. For the purposes of this workload guidance, the application platform is defined as the compute platform that:

1. Is used for model training.
2. Is used for inferencing.
3. Is used for exploratory data analysis (EDA)

This article provides guidance on selecting the best platform for each of these functions to meet your business needs, but there are general recommendations that can be applied to all of them.

## Platform recommendations

No matter which of the functions described above you are designing for, start with the following general recommendations:

1. Start by evaluating the tools you are already using to understand whether they can be reused for your AI workload. If they support the functionality required and can also meet your requirements for reliability, security, cost, and performance, bringing in a new tool may not be worth the cost and effort.
2. Consider compliance requirements for your data as they relate to regions that you plan to deploy in. You may need to limit the regions you deploy in or isolate parts of your workload from each other to meet these requirements, so going into your designing phase with this information at the ready will protect you from redesigning later.
3. Prefer platform as a service (PaaS) or software as a service (SaaS) solutions to minimize the extra operational burden that building your own solution introduces, like patching and other maintenance. Minimizing the day-2 burden required for the new technology will ease your adoption. Many AI functions are especially complex, so building your own platform is not recommended.
4. When designing for the use of PaaS or SaaS solutions, understand any quotas or limits that apply. Your ability to scale out to meet high traffic demands may be impacted by quotas or limits, so you may need to adjust your design to minimize that risk.
5. Try to deploy all related resources in the same region to reduce latency and to simplify the design.
6. In general, you should treat the APIs for your AI workload the same as any other API in your environments. All of the APIs should be secured behind a gateway and all of the code should be handled with the same [safe deployment practices](../operational-excellence/safe-deployments) as every other code asset.
7. Establish performance benchmarks through experimentation. Every AI workload is different and the amount of compute that you'll need depends on your particular use case. Determining the amount and types of compute that is optimal for your workload should be based on thorough benchmark testing. This guide will help you choose a platform, but the SKUs appropriate for your workload will only be known to you after the benchmarking exercises.

## The EDA platform

EDA is a common preliminary function performed by data scientists before modeling or statistical analysis. As such, it can be considered a development phase, which means that targets for reliability and performance may be significantly lower than production resources and maintaining productivity is the more important factor.

### Factors to consider

When evaluating an EDA platform, consider the following:

- **Cost control:** The platform should enable the data scientists to perform their work according to their schedule requirments, but should be right-sized to ensure that cost expectations are met.

- **Transient usage:** The platform should support transient workspaces and compute, which means that the necessary resources should be able to be stopped when they aren't being used to help cost control. EDA jobs are typically interactive, so users need to be able to start VMs and stop them as they run jobs over time.

- **Compute optionality:** The platform should enable on-demand access to GPUs if needed, and provide a variety of compute options to help right-size the platform.

- **Production-grade security and observability:** The data used in your EDA phase will likely be production data, which requires you to follow production practices to secure that data and monitor the platform. To that end, your platform should support all necessary security controls, like access and authorization, encryption at rest and in transit, and regional requirements. Likewise, it should support robust monitoring and alerting functionality, including logging and auditability.

- **MLFlow support:** Your EDA platform should make it possible to choose a platform like Azure Data Science virtual machine (DSVM) that enables integration with MLFlow for tracking your experiments. Determining which combination of data, code, and parameters produces a particular result can become a daunting task. By integrating MLflow with Azure DSVM, you can ensure that your EDA and machine learning workflows are efficient, reproducible, and scalable.

- **Secure networking:** The platform should support private networking to access centralized repositories for container images, data, and code assets.
  
- **Use [Azure Machine Learning (AML)](/azure/machine-learning/overview-what-is-azure-machine-learning):** Use [AML compute instance](/azure/machine-learning/concept-compute-instance?view=azureml-api-2) with team-level file shares as your EDA platform. Unless your team or organization are already using a suitable hosting platform, like GPU-enabled compute clusters in Databricks for example, in which case it may be more appropriate to remain on that platform.  

> [!NOTE]
> Do not build a full EDA platform unless you need to. GPU-optimized compute is expensive and is not appropriate if your use case doesn't require it.

## The model training and fine-tuning platform

As you move to model training and fine-tuning, you will likely need high-performance GPU-optimized compute for the compute-intensive work involved in these activities. Reliability is typically not as important as performance as most of this work happens behind the scenes. If high reliability is a requirement, evaluate whether spreading the workload across availability zones or regions is necessary. High reliability becomes more important in cases where model freshness is updated frequently, which requires training to be completed on a tighter schedule. Your [RTO](../reliability/metrics#recovery-metrics) should determine the reliability design you choose.

The recommendations below apply to both model training and fine-tuning functions. Unless you're forced to use separate platforms for these functions, you should use the same platform for both of these functions.

### Factors to consider

- **Cost vs performance:** Due to the high-performance, GPU-optimized compute requirements, test and benchmark your training and fine-tuning extensively to land on the ideal SKU that balances performance against costs.
- **Transient usage:** Like EDA activities, model training and fine-tuning are typically not run full-time so prefer a platform that can be stopped when not in use to help control costs. Unlinke EDA however, model training is typically a batch process, so the compute is only needed when the batch runs and then can be shut down until the next run.
- **Use machine learning platform:** Due to the complexity required in managing the compute for these activities, an orchestrator is recommended. AML is the recommended solution for these activites. There are two options to evaluate:
  -  [Serverless compute](/azure/machine-learning/how-to-use-serverless-compute) is ideal for short, infrequent runs that can tolerate noisy neighbor effects. You can choose between standard and spot pricing. Spot pricing is only recommended for highly interruptible training. **Do not** use serverless for full-time operations as the costs can balloon quickly.
  -  [Compute Clusters](/azure/machine-learning/how-to-create-attach-compute-cluster?view=azureml-api-2&tabs=python#what-is-a-compute-cluster) gives you significant control over available hardware and is tuned for parallel or distributed training.
-  **Combine technologies when appropriate:** If your existing data platform has ML capabilities, like [Databricks](/azure/databricks/machine-learning/), you can use that for certain steps like data transformation and feature engineering, while performing the training, fine-tuning and further steps in AML. This helps you minimize the cost and complexity involved in using a data platform for those functions it might not be ideally suited for.

> [!NOTE]
> For foundation models, your choice of model hosting platform may limit your fine-tuning options. For example, using Azure OpenAI for model hosting limits your fine-tuning options to the built-in Azure OpenAI fine-tuning functionality.

## The model hosting and inferencing platform

Model hosting and inferencing functions make up the serve layer of the AI workload and those functions are performed with endpoints specific to the software that you choose, like NVIDIA Triton, TorchServe, TensorFlow Serving and many others. These model serving software solutions, in essence, are Python SDKs that are specialized in fronting a model with an API and adding some additional functionality specific to that solution. As such, you can either choose your hosting platform based upon your choice of software, or choose your software based upon your choice of hosting platform.

Fundamentally, the APIs for the serve layer are microservices, so you should follow the same practices for these APIs as other microservices in your environments. They should be containerized, [bulkheaded](/azure/architecture/patterns/bulkhead) from other services, and should have their own lifecycles independent of other services and APIs. That being said, serve layer APIs generally require significantly more GPU-based compute power and much larger container images than traditional APIs. 

The inferencing endpoints will either be used for batch or online inferencing processes, and the inferencing method will help determine the right hosting platform. Batch inferencing is best hosted on a platform that supports transient usage and supports the compute to be shutdown when not in use. Online inferencing is best hosted on a platform that supports elastic compute utilization, which scales automatically based on the load at any given time. 

### Factors to consider

- **Reliability:** Server layer APIs are production resources, so you should apply the same reliability requirements to these as other workload flows that match their [criticality](/azure/well-architected/reliability/identify-flows) rating. If their criticality requires high availability, your hosting platform should support Availability Zones or a multi-region design.

- **Networking:** Determine whether you require private networking and egress firewalling.

- **Identity and access security:** Determine what identity and access controls are required for your endpoints. For example, do you require native role-based access control (RBAC) or built-in support for your identity and access platform, like Microsoft Entra ID.

Adopt a strategy of applying meaningful tags to images in your container registry to ensure that your model hosting service is pulling a specific version that the team can easily identify. This approach helps with data governance, reducing the risk of outdated or incorrect models being used in production.

 - **Monitoring capabilities:** Determine the required monitoring capabilities for your endpoints. Depending on the platform you may have limited access to logs and metrics, which may limit your ability to audit activities or detect malfunctions.

Traceability is a critical component of monitoring for model hosting. It is important to know information about the model like the current version, who deployed it, when it was deployed, and the model's data lineage. 

 - **Performance:** Inference latency is a common concern and different platforms come with different performance profiles. Serverless and PaaS services using a utility model can have "noisy neighbor" tendencies and often have no throughput guarantees. On the other hand, those same platforms may offer self-hosted that offer guaranteed throughput with a pre-purchasing model, or you could consider self-hosting on a Kubernetes to get predictable latency behavior.

Be aware of service limits and quotas that may effect your performance, like those for [Azure Open AI](/azure/ai-services/openai/quotas-limits). Often these quotas and limits are aggressively set to meet capacity demands, so if your choice of platform doesn't deliver the performance that you target, you may need to adopt strategies to spread the compute demand across instances to stay within those limits. 

Advanced architectures can combine multiple deployments to achieve both fixed throughput for a bulk of the workload and bursting capabilities into more flexible compute. 

### Recommendations 

#### Batch inferencing

- If you are performing inferencing on data that resides in a platform that supports model hosting, like Databricks, consider using that platform for inferencing. Be sure to isolate the inferencing compute from other functions performed by the data platform.

- If you do not have an appropriate platform already in use, prefer [Azure ML batch endpoints](/azure/machine-learning/how-to-mlflow-batch).

#### Online inferencing

- Evaluate platform as a service (PaaS) and serverless solutions as a first step. These services are typically the easiest to adopt and manage, simplifying your design and minimizing operational burden. For example, Azure Open AI is a good choice for foundational models.

   - Consider using Azure ML's Serverless API to aggregate endpoint access even if you use Azure OpenAI or another foundational model hosting solution. 

- Prefer Azure ML for with managed Compute Clusters for scenarios when PaaS or serverless solutions are not the best fit. Azure ML-managed compute supports traffic splitting and mirroring for A/B testing, debugging, and robust auditing. As the compute is managed by the service, day-2 operations are much easier than self-hosting. It also offers a wide selection of compute configurations and scaling capabilities.

- If you choose to self-host your model on a Kurbentes cluster within Azure ML or another platform, ensure that the node pool is isolated from other APIs or any other workloads on the cluster to achieve predictable performance and to optimize security. Avoid using GPU-based or GPU-optimized compute for anything other than your AI workload functions in an effort to reduce costs. Instead, establish your performance baseline through testing and right-size your compute to meet your performance requirements while avoiding over-provisioning.

## The orchestration platform

Orchestration in the context of AI workload application platforms refers to tools like prompt flow in [Azure ML](/azure/machine-learning/prompt-flow/overview-what-is-prompt-flow) and [Azure AI Studio](/azure/ai-studio/how-to/prompt-flow), that are designed to streamline the entire development cycle of AI applications by automating many common workflow functions.

### Factors to consider

Like all other production workloads in your cloud estate, the orchestration tool require considerations for:

- **Reliability, security, and monitoring:** These tools should adhere to standards for production workloads for reliability, security, and monitoring.

- **Peformance:** These tools do not require GPU-optimized or GPU-based compute, so prefer general purpose SKUs.

- **Cost optimization:** The tools are "always-on" so prefer elastic compute options to minimize utilization costs.

### Recommendations

- Prefer an off-the-shelf solution like Prompt Flow, verifying that its capabilities match your orchestration needs before looking into custom hosting with tools like Langchain or Semantic Kernel.

- Endpoints for solutions like Propmpt Flow should be hosted on Azure ML with Compute Instances or with self-hosting on AKS.
