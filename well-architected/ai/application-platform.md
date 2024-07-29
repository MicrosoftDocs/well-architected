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

## General recommendations

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
- **Transient usage:** The platform should support transient workspaces and compute, which means that the necessary resources should be able to be stopped when they aren't being used to help cost control.
- **Compute optionality:** The platform should enable on-demand access to GPUs if needed, and provide a variety of compute options to help right-size the platform.
- **Production-grade security and observability:** The data used in your EDA phase will likely be production data, which requires you to follow production practices to secure that data and monitor the platform. To that end, your platform should support all necessary security controls, like access and authorization, encryption at rest and in transit, and regional requirements. Likewise, it should support robust monitoring and alerting functionality, including logging and auditability.
- **MLFlow support:**
- **Secure networking:** The platform should support private networking to access centralized repositories for container images, data, and code assets.
- **Use [Azure Machine Learning (AML)](/azure/machine-learning/overview-what-is-azure-machine-learning):** If you have existing access to a suitable hosting platform, like GPU-enabled compute clusters in Databricks for example, it may be appropriate to remain on that platform. If that's not the case, [AML compute instance](/azure/machine-learning/concept-compute-instance?view=azureml-api-2) with team-level file shares is the recommended hosting platform design.

> [!NOTE]
> Do not build a full EDA platform unless you need to. GPU-optimized compute is expensive and is not appropriate if your use case doesn't require it.

## The model training and fine-tuning platform

As you move to model training and fine-tuning, you will likely need high-performance GPU-optimized compute for the compute-intensive work involved in these activities. Reliability is typically not as important as performance as most of this work happens behind the scenes. If high reliability is a requirement, evaluate whether spreading the workload across availability zones or regions is necessary. High reliability becomes more important in cases where model freshness is updated frequently, which requires training to be completed on a tighter schedule. Your [RTO](../reliability/metrics#recovery-metrics) should determine the reliability design you choose.

### Factors to consider

- **Cost vs performance:** Due to the high-performance, GPU-optimized compute requirements, test and benchmark your training and fine-tuning extensively to land on the ideal SKU that balances performance against costs.
- **Transient usage:** Like EDA activities, model training and fine-tuning are typically not run full-time so prefer a platform that can be stopped when not in use to help control costs.
- **Use an orchestrator:** Due to the complexity required in managing the compute for these activities, an orchestrator is recommended. AML is the recommended solution for these activites. There are two options to evaluate:
  -  [Serverless compute](/azure/machine-learning/how-to-use-serverless-compute) is ideal for short, infrequent runs that can tolerate noisy neighbor effects. You can choose between standard and spot pricing. Spot pricing is only recommended for highly interruptible training. **Do not** use serverless for full-time operations as the costs can balloon quickly.
  -  [Compute Clusters](/azure/machine-learning/how-to-create-attach-compute-cluster?view=azureml-api-2&tabs=python#what-is-a-compute-cluster) gives you significant control over available hardware and is tuned for parallel or distributed training.
-  **Combine technologies when appropriate:** If your existing data platform has ML capabilities, like [Databricks](/azure/databricks/machine-learning/), you can use that for certain steps like data transformation and feature engineering, while performing the modeling, fine-tuning and further steps in AML. This helps you minimize the cost and complexity involved in using a data platform for those functions it might not be ideally suited for.
  -  
