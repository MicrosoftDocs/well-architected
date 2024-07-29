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
3. Prefer platform as a service (PaaS) or software as a service (SaaS) solutions to minimize the extra operational burden that building your own solution introduces, like patching and other maintenance. Minimizing the day-2 burden required for the new technology will ease your adoption.
4. When designing for the use of PaaS or SaaS solutions, understand any quotas or limits that apply. Your ability to scale out to meet high traffic demands may be impacted by quotas or limits, so you may need to adjust your design to minimize that risk.
5. Try to deploy all related resources in the same region to reduce latency and to simplify the design.
6. In general, you should treat the APIs for your AI workload the same as any other API in your environments. All of the APIs should be secured behind a gateway and all of the code should be handled with the same [safe deployment practices](../operational-excellence/safe-deployments) as every other code asset.

## The EDA platform

EDA is a common preliminary function performed by data scientists before modeling or statistical analysis. As such, it can be considered a development phase, which means that targets for reliability and performance may be significantly lower than production resources and maintaining productivity is the more important factor.

### Factors to consider

When evaluating an EDA platform, consider the following:

- **Cost control:** The platform should enable the data scientists to perform their work according to their schedule requirments, but should be right-sized to ensure that cost expectations are met.
- **Transient usage:** The platform should support transient workspaces and compute, which means that the necessary resources should be able to be stopped when they aren't being used to help cost control.
- **Compute optionality:** The platform should enable on-demand access to GPUs if needed, and provide a variety of compute options to help right-size the platform.
- **Production-grade security and observability:** The data used in your EDA phase will likely be production data, which requires you to follow production practices to secure that data and monitor the platform. To that end, your platform should support all necessary security controls, like access and authorization, encryption at rest and in transit, and regional requirements. Likewise, it should support robust monitoring and alerting functionality, including logging and auditability.
- **MLFlow support:** 
