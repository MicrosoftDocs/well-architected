---
title: Microsoft Azure Well-Architected Framework
titleSuffix: Azure Well-Architected Framework
description: Learn about the five pillars of the Azure Well-Architected Framework and how they can produce a high quality, stable, and efficient cloud architecture.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 03/27/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: 
products:
  - azure
---

# Microsoft Well-Architected Framework Pillars

The Well-Architected Framework pillars drive architectural excellence at the fundamental level of a workload. 

Use this matrix to familiarize yourself with the key concepts:

| Pillar | Workload concern | Apply the principles | Strike a balance
|--------|-------------|-------------------|-----------
| [**Reliability**][resiliency-pillar] | Resiliency, availability, recovery| [Principles for building redundancy and self healing](/azure/architecture/framework/resiliency/principles) | [Coming soon!](/azure/architecture/framework/resiliency/business-metrics)
| [**Security**][security-pillar] | Data protection, threat detection, mitigation |[Principles for attaining Zero-Trust](/azure/architecture/framework/security/security-principles)|[Tradeoffs](/azure/architecture/framework/security/security-tradeoffs)
| [**Cost optimization**][cost-pillar] | Cost modeling, budgets, reduce waste|[Principles for right-sizing and evaluating under utilization](/azure/architecture/framework/cost/overview)|[Tradeoffs](/azure/architecture/framework/cost/tradeoffs)|
| [**Operational excellence**][devops-pillar] | Holistic observability, DevOps practices |[Principles for automated operations, monitoring, and continuous improvements](/azure/architecture/framework/devops/principles)|[Coming soon!]()|
| [**Performance efficiency**][scalability-pillar] | Scalability, load testing|[Principles for scale-out](/azure/architecture/framework/scalability/principles)|[Tradeoffs](/azure/architecture/framework/scalability/tradeoffs)|

> [!IMPORTANT]
>
> The reference architectures available in the [**Azure Architecture Center**](/azure/architecture/browse/) have been designed with the principles in mind. The architecture articles describe a prescriptive path by applying the design principles that provide a holistic view on the [**Ten design principles for Azure applications**](/azure/architecture/guide/design-principles/). 

## Assessment review tool

Assess your workload using the core pillars to **identify and prioritize opportunities** to improve the posture of your workloads.

:::image type="content" source="./_images/well-architected-review.png" alt-text="Screenshot of the Microsoft Azure Well-Architected Review." lightbox="./_images/well-architected-review.png":::

> [!TIP]
> 
> Start your assessment with [**Azure Well-Architected Review**](/assessments/?id=azure-architecture-review&mode=pre-assessment).

## Integration with Azure recommendation services

- [**Azure Advisor**](/azure/advisor/) uses the **core pillars** as a basis for analyzing your resource configuration and usage telemetry and provides appropriate recommendations. 

- [**Advisor Score**](/azure/advisor/azure-advisor-score) is a core feature of Azure Advisor that aggregates Advisor recommendations into a simple, actionable score. The overall score can be categorized into the core pillars of the Well-Architected Framework. Use the score to  prioritize the actions that yield the biggest improvement.

## Get started with this video series

To learn about how to architect successful workloads on Azure by using the Well-Architected Framework, watch this video:

<!-- markdownlint-disable MD034 -->
> [Design well-architected workloads](/shows/azure-enablement/architect-successful-workloads-on-azure--introduction-ep-1-well-architected-series/player)

For more information about resiliency, watch the following video that shows you how to start improving the reliability of your Azure workloads:

<!-- markdownlint-disable MD034 -->

> [Improve the reliability of your workloads](/shows/azure-enablement/start-improving-the-reliability-of-your-azure-workloads--reliability-ep-1--well-architected-series/player)

Learn to ask the right questions about secure application development on Azure by watching the following video:

<!-- markdownlint-disable MD034 -->
> [Secure your workloads](/shows/azure-enablement/ask-the-right-questions-about-secure-application-development-on-azure/player)


Adopt an optimization mindset and start evaluating your Azure costs:

<!-- markdownlint-disable MD034 -->

> [Optimize costs](/shows/azure-enablement/start-optimizing-your-azure-costs--cost-optimization-ep-1--well-architected-series/player)



<!-- links -->

[resiliency]: ./resiliency/principles.md

<!-- practices -->
[autoscale]: /azure/architecture/best-practices/auto-scaling
[background-jobs]: /azure/architecture/best-practices/background-jobs
[caching]: /azure/architecture/best-practices/caching
[cdn]: /azure/architecture/best-practices/cdn
[data-partitioning]: /azure/architecture/best-practices/data-partitioning
[monitoring]: /azure/architecture/best-practices/monitoring
[retry-service-specific]: /azure/architecture/best-practices/retry-service-specific
[transient-fault-handling]: /azure/architecture/best-practices/transient-faults

<!-- pillars -->
[cost-pillar]: ./cost/index.yml
[security-pillar]: ./security/index.yml
[resiliency-pillar]: ./resiliency/index.yml
[scalability-pillar]: ./scalability/index.yml
[devops-pillar]: ./devops/index.yml
