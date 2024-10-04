---
title: Application platform for AI workloads on Azure
description: Learn about the Azure resources that you need for running AI workloads.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/01/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-ai
---

# Application platform for AI workloads on Azure

You must carefully consider the application hosting platform that your AI workload is deployed on to ensure that you can maximize efficiency, the security of operations, and reliability. 

This design area covers several different types of applications that might be relevant to your AI workload:

- Exploratory data analysis (EDA)
- Model training
- Inferencing

This article provides guidance for selecting the best platform for each of these functions to meet your business needs. There are also general recommendations that can be applied to all of them.

## Platform recommendations

No matter which of the previously described functions you're designing for, start with the following general recommendations:

1. Start by evaluating the tools you already use to understand whether they can be reused for your AI workload. If they support the functionality required and can also meet your requirements for reliability, security, cost, and performance, bringing in a new tool might not be worth the cost and effort.
2. Consider compliance requirements for your data as they relate to the regions that you plan to deploy in. You might need to limit the regions you deploy in or isolate parts of your workload from each other to meet these requirements, so going into your designing phase with this information ready will help protect you from needing to redesign later.
3. Prefer platform as a service (PaaS) or software as a service (SaaS) solutions to minimize the extra operational burden that building your own solution introduces, like patching and other maintenance. Minimizing the Day-2 burden required for the new technology will ease your adoption. Many AI functions are complex, so we don't recommend building your own platform.
4. When you design for the use of PaaS or SaaS solutions, understand any quotas or limits that apply. Your ability to scale out to meet high traffic demands might be affected by quotas or limits, so you might need to adjust your design to minimize that risk.
5. Try to deploy all related resources in the same region to reduce latency and to simplify the design.
6. In general, you should treat the APIs for your AI workload the same as any other API in your environments. All APIs should be placed behind a gateway and all code should be handled with the same [safe deployment practices](../operational-excellence/safe-deployments) as every other code asset.
7. Establish performance benchmarks through experimentation. Every AI workload is different, and the amount of compute that you need depends on your use case. Determining the amount and types of compute that's optimal for your workload should be based on thorough benchmark testing. This guide helps you choose a platform, but you'll only know which SKUs are appropriate for your workload after benchmarking exercises.

## Considerations for the EDA platform

EDA is a common preliminary function that's performed by data scientists before modeling or statistical analysis. It can therefore be considered a development phase, which means that targets for reliability and performance might be significantly lower than those for production resources and maintaining productivity is the more important factor. 

This section provides guidance on capabilities to consider when you select an EDA platform solution.

### Functional requirements

When you evaluate an EDA platform, consider the following questions:

- **Does the platform support transient usage?**

   The platform should support transient workspaces and compute, which means that you should be able to stop the necessary resources when they aren't being used. This capability helps control costs. EDA jobs are typically interactive, so users need to be able to start VMs and stop them as they run jobs.

- **Does the platform support compute optionality?**

   The platform should enable on-demand access to GPUs as needed, and provide a variety of compute options to help right-size the platform.

- **Does the platform support MLflow?**

   Your EDA platform should make it possible to choose a technology that enables integration with MLflow for tracking your experiments. MLflow is recommended as a model development, deployment, and management protocol because it provides the following benefits:

     - *Experiment tracking.* MLflow allows you to track experiments by recording parameters, metrics, and artifacts. This capability is essential during EDA so that you can keep track of different data preprocessing steps and feature engineering techniques and their impacts on model performance.

     - *Reproducibility.* By logging all the details of your experiments, MLflow ensures that you can reproduce your results, which is critical for validating findings.

     - *Data and model versioning.* MLflow helps with versioning datasets and models, which makes it easier to manage different versions of data transformations and tested models.

     - *Collaborative work.* MLflow provides a centralized platform where data scientists can share their experiments and results, which facilitates collaboration and knowledge sharing.

### Nonfunctional requirements

Also consider these questions: 

- **How can the platform help control costs?**

   The platform should enable the data scientists to perform their work according to their schedule requirements, but it should be right-sized to ensure that cost expectations are met. 

- **What security requirements must be followed for the platform?**

   The data used during your EDA phase will probably be production data, which requires you to follow production practices to secure that data and monitor the platform. To that end, your platform should support all necessary security controls, like:

   - *Access and authorization*
   - *Encryption at rest and in transit* 
   - *Regional data protection requirements*
   - *Robust monitoring and alerting functionality, including logging and auditability*
   - *Private networking to access centralized repositories for container images, data, and code assets*

### Tools

Use [Azure Machine Learning](/azure/machine-learning/overview-what-is-azure-machine-learning): Use an [Azure Machine Learning compute instance](/azure/machine-learning/concept-compute-instance?view=azureml-api-2) with team-level file shares as your EDA platform. One exception is if your team or organization are already using a suitable hosting platform, like GPU-enabled compute clusters in Databricks, for example. In that case, it might be more appropriate to stay on that platform.

> [!NOTE]
> Don't build a full EDA platform unless you need to. GPU-optimized compute is expensive and isn't appropriate if your use case doesn't require it.

## Considerations for the model training and fine-tuning platform

When you move to model training and fine-tuning, you'll probably need high-performance GPU-optimized compute for the compute-intensive work required by those activities. Reliability typically isn't as important as performance because most of this work occurs behind the scenes. If high reliability is a requirement, evaluate whether spreading the workload across availability zones or regions is necessary. High reliability becomes more important in cases where model freshness is updated frequently, which requires training to be completed on a tighter schedule. Your [RTO](../reliability/metrics#recovery-metrics) should determine the reliability design you choose.

The guidance in this section applies to both model training and fine-tuning functions. Unless you're forced to use separate platforms for these functions, you should use the same platform for both of these functions.

### Functional requirements

- **Does the platform support transient usage?**

Like EDA activities, model training and fine-tuning are typically not run full-time so prefer a platform that can be stopped when not in use to help control costs. Unlinke EDA however, model training is typically a batch process, so the compute is only needed when the batch runs and then can be shut down until the next run.

- **Does the platform provide orchestration functionality?**

Due to the complexity required in managing the compute for model training and fine-tuning, an orchestrator is recommended.

-  **Can existing technologies in your environment be part of the solution?**

If your existing data platform has ML capabilities, like [Databricks](/azure/databricks/machine-learning/), you can use that for certain steps like data transformation and feature engineering, while performing the training, fine-tuning and further steps in AML. This combining of technologies helps you minimize the cost and complexity involved in using a data platform for those functions it might not be ideally suited for.

### Nonfunctional requirements

- **What is the tolerable tradeoff between costs and performance?**

Due to the high-performance, GPU-optimized compute requirements, test and benchmark your training and fine-tuning extensively to land on the ideal SKU that balances performance against costs.

### Tools

Azure Machine Learning is the recommended solution for the model training and fine-tuning platform as it offers orchestration functionality with support for batch compute utilization. There are two compute options to evaluate:

  -  [Serverless compute](/azure/machine-learning/how-to-use-serverless-compute) is ideal for short, infrequent runs that can tolerate noisy neighbor effects. You can choose between standard and spot pricing. Spot pricing is only recommended for highly interruptible training. **Do not** use serverless for full-time operations as the costs can balloon quickly.
    
  -  [Compute Clusters](/azure/machine-learning/how-to-create-attach-compute-cluster?view=azureml-api-2&tabs=python#what-is-a-compute-cluster) gives you significant control over available hardware and is tuned for parallel or distributed training.

> [!NOTE]
> For foundation models, your choice of model hosting platform may limit your fine-tuning options. For example, using Azure OpenAI for model hosting limits your fine-tuning options to the built-in Azure OpenAI fine-tuning functionality.

## Considerations for the model hosting and inferencing platform

Model hosting and inferencing functions make up the serve layer of the AI workload and those functions are performed with endpoints specific to the software that you choose, like NVIDIA Triton, TorchServe, TensorFlow Serving and many others. These model serving software solutions, in essence, are Python SDKs that are specialized in fronting a model with an API and adding some additional functionality specific to that solution. As such, you can either choose your hosting platform based upon your choice of software, or choose your software based upon your choice of hosting platform.

When using SaaS or PaaS solutions with pre-packaged models - such as the large language models available in Azure OpenAI - there will be fewer or no opportunities to select a serving software. Instead, the service that you are consuming will provide an API. This reduces the amount of choice in the process of creating a model deployment which comes with advantages (for example: a streamlined development process of your workload) and disadvantages (for example: reduced choice when it comes to how your application can call and interact with the model).

Fundamentally, the APIs for the serve layer are microservices, so you should follow the same practices for these APIs as other microservices in your environments. Where you host them, they should be containerized, [bulkheaded](/azure/architecture/patterns/bulkhead) from other services, and should have their own lifecycles independent of other services and APIs. That being said, serve layer APIs generally require significantly more GPU-based compute power and much larger container images than traditional APIs. 

This section provides guidance on capabilities to consider when selecting a model hosting and inferencing platform.

### Functional requirements

- **Does your workload require batch or online inferencing?**

The inferencing endpoints will either be used for batch or online inferencing processes, and the inferencing method will help determine the right hosting platform. Batch inferencing is best hosted on a platform that supports transient usage and supports the compute to be shutdown when not in use. Online inferencing is best hosted on a platform that supports elastic compute utilization, which scales automatically based on the load at any given time. 

- **Does the platform support traceability?**

Traceability is a critical for maintaining the integrity of the models used in your workload. It is important to know information about the model like the current version, who deployed it, when it was deployed, and the model's data lineage.

Adopt a strategy of applying meaningful tags to images in your container registry to ensure that your model hosting service is pulling a specific version that the team can easily identify. This approach helps with data governance, reducing the risk of outdated or incorrect models being used in production.

- **Will your hosting platform be a centralized resource?**

Many organizations use a centralized model hosting platform that is used by different workload teams for their own workloads. If your hosting platform will be centralized, support for chargeback should be considered. This functionality allows you to properly account for utilization of the platform between teams and workloads.

### Nonfunctional requirements

- **What are the reliability requirements for the platform?**

Serve layer APIs are production resources, so you should apply the same reliability requirements to these as other workload flows that match their [criticality](/azure/well-architected/reliability/identify-flows) rating. If their criticality requires high availability, your hosting platform should support Availability Zones or a multi-region design.

- **What networking controls are required for the platform's?**

Determine whether you require private networking and egress firewalling to protect the platform.

- **What are the identity and access security requirements for the platform?**

Determine what identity and access controls are required for your endpoints. For example, do you require native role-based access control (RBAC) or built-in support for your identity and access platform, like Microsoft Entra ID.

 - **What monitoring capabilities are supported by the platform?**

Determine the required monitoring capabilities for your endpoints. Depending on the platform you may have limited access to logs and metrics, which may limit your ability to audit activities or detect malfunctions.

 - **What are the performance requirements for the platform?**

Inference latency is a common concern and different platforms come with different performance profiles. Serverless and PaaS services using a utility model can have "noisy neighbor" tendencies and often have no throughput guarantees. On the other hand, those same platforms may offer self-hosted that offer guaranteed throughput with a pre-purchasing model, or you could consider self-hosting on a Kubernetes to get predictable latency behavior.

Be aware of service limits and quotas that may effect your performance, like those for [Azure Open AI](/azure/ai-services/openai/quotas-limits). Often these quotas and limits are aggressively set to meet capacity demands, so if your choice of platform doesn't deliver the performance that you target, you may need to adopt strategies to spread the compute demand across instances to stay within those limits. 

Advanced architectures can combine multiple deployments to achieve both fixed throughput for a bulk of the workload and bursting capabilities into more flexible compute. 

### Tools 

#### Batch inferencing

- If you are performing inferencing on data that resides in a platform that supports model hosting, like Databricks, consider using that platform for inferencing. Be sure to isolate the inferencing compute from other functions performed by the data platform.

- Prefer [Azure OpenAI Batch API](/azure/ai-services/openai/how-to/batch) for foundation models.

For non-foundation models, consider the following recommendations:

- Prefer [Azure ML batch endpoints](/azure/machine-learning/how-to-mlflow-batch) for the following scenarios:

  - You need to perform inferencing on a large dataset that's distributed in multiple files and you don’t require low latency.

  - You need to perform long-running batch operations over large datasets and can take advantage of parallelization.

  - You need to deploy pipeline components for batch processing.

- If you need to run Spark jobs for distributed data processing, consider using [Azure Synapse Analytics](/azure/synapse-analytics/), [Azure Databricks](/azure/databricks/), or [Azure ML serverless Spark compute](/azure/machine-learning/apache-spark-azure-ml-concepts?view=azureml-api-2).

- If none of these scenarios apply, prefer Azure ML batch endpoints.

#### Online inferencing

- Evaluate platform as a service (PaaS) and serverless solutions as a first step. These services are typically the easiest to adopt and manage, simplifying your design and minimizing operational burden. For example, Azure Open AI is a good choice for foundational models.

   - Consider using Azure ML's Serverless API to aggregate endpoint access even if you use Azure OpenAI or another foundational model hosting solution. 

- Prefer Azure ML for with managed Compute Clusters for scenarios when PaaS or serverless solutions are not the best fit. Azure ML-managed compute supports traffic splitting and mirroring for A/B testing, debugging, and robust auditing. As the compute is managed by the service, day-2 operations are much easier than self-hosting. It also offers a wide selection of compute configurations and scaling capabilities.

- If you choose to self-host your model on a Azure Kubernetes Service cluster [attached to Azure ML](/azure/machine-learning/how-to-attach-kubernetes-anywhere),  or another container-based platform, ensure that the node pool is isolated from other APIs or any other workloads on the cluster to achieve predictable performance and to optimize security. Avoid using GPU-based or GPU-optimized compute for anything other than your AI workload functions in an effort to reduce costs. Instead, establish your performance baseline through testing and right-size your compute to meet your performance requirements while avoiding over-provisioning.

- You can also fully self-host your model using infrastructure as a service (IaaS) solutions, like [Azure Data Science Virtual Machine](/azure/machine-learning/data-science-virtual-machine/overview).

## Considerations for the orchestration platform

Orchestration in the context of AI workload application platforms refers to tools like prompt flow in [Azure ML](/azure/machine-learning/prompt-flow/overview-what-is-prompt-flow) and [Azure AI Studio](/azure/ai-studio/how-to/prompt-flow), that are designed to streamline the entire development cycle of AI applications by automating many common workflow functions.

### Nonfunctional requirements

Like all other production workloads in your cloud estate, the orchestration tool require considerations for:

- **Reliability, security, and monitoring:** These tools should adhere to standards for production workloads for reliability, security, and monitoring.

- **Peformance:** These tools do not require GPU-optimized or GPU-based compute, so prefer general purpose SKUs.

- **Cost optimization:** The tools are "always-on" so prefer elastic compute options to minimize utilization costs.

### Tools

- Prefer an off-the-shelf solution like Prompt Flow, verifying that its capabilities match your orchestration needs before looking into custom hosting with tools like Langchain or Semantic Kernel.

- Endpoints for solutions like Propmpt Flow should be hosted on Azure ML with Compute Instances or with self-hosting on AKS.
