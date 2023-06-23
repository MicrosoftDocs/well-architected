---
title: Microsoft Azure Well-Architected Framework
titleSuffix: Azure Well-Architected Framework
description: Learn about the five pillars of the Azure Well-Architected Framework and how they can produce a high quality, stable, and efficient cloud architecture.
author: martinekuan
ms.author: martinek
ms.date: 03/27/2023
ms.topic: conceptual
categories:
  - management-and-governance
ms.custom:
  - seojan19
  - guide
  - seo-aac-fy21q3
keywords:
  - "Well-architected framework"
  - "Azure Well Architected Framework"
  - "Azure architecture"
  - "architecture framework"
---

# Microsoft Azure Well-Architected Framework

The Azure Well-Architected Framework is a set of guiding tenets that you can use to improve the quality of a workload. The framework consists of five pillars of architectural excellence:

- [Reliability](#reliability)
- [Security](#security)
- [Cost optimization](#cost-optimization)
- [Operational excellence](#operational-excellence)
- [Performance efficiency](#performance-efficiency)

Incorporating these pillars helps produce a high-quality, stable, and efficient cloud architecture:

| Pillar | Description |
|--------|-------------|
| [Reliability][resiliency-pillar] | The ability of a system to recover from failures and continue to function. |
| [Security][security-pillar] | Protecting applications and data from threats. |
| [Cost optimization][cost-pillar] | Managing costs to maximize the value delivered. |
| [Operational excellence][devops-pillar] | Operations processes that keep a system running in production. |
| [Performance efficiency][scalability-pillar] | The ability of a system to adapt to changes in load. |

To learn about how to architect successful workloads on Azure by using the Well-Architected Framework, watch this video:

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://learn.microsoft.com/shows/azure-enablement/architect-successful-workloads-on-azure--introduction-ep-1-well-architected-series/player]

<!-- markdownlint-enable MD034 -->

## Overview

The following diagram is a high-level overview of the Azure Well-Architected Framework:

:::image type="content" source="./_images/waf-diagram-revised.png" alt-text="Diagram of the Well-Architected Framework and supporting elements.":::

In the center is the *Well-Architected Framework*, which includes the five pillars of architectural excellence. Surrounding the Well-Architected Framework are six supporting elements:

- [Azure Well-Architected Review](/assessments/?id=azure-architecture-review&mode=pre-assessment)
- [Azure Advisor](/azure/advisor/)
- [Documentation](/azure/architecture/framework/)
- [Partners](https://azure.microsoft.com/partners/), [Support](https://azure.microsoft.com/support/options/#support-plans), and Services Offers
- [Reference architectures](/azure/architecture/guide/)
- [Design principles](/azure/architecture/guide/design-principles/)

### Assess your workload

To assess your workload using the tenets found in the Microsoft Azure Well-Architected Framework, see the [Microsoft Azure Well-Architected Review](/assessments/?id=azure-architecture-review&mode=pre-assessment).

:::image type="content" source="./_images/well-architected-review.png" alt-text="Screenshot of the Microsoft Azure Well-Architected Review." lightbox="./_images/well-architected-review.png":::

We also recommend using *Azure Advisor* and *Advisor Score* to identify and prioritize opportunities to improve your workload. Both services are free to all Azure users and align with the five pillars of the Well-Architected Framework:

- *Azure Advisor* is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. It analyzes your resource configuration and usage telemetry. It recommends solutions that can help you improve the reliability, security, cost-effectiveness, performance, and operational excellence of your Azure resources. Learn more about [Azure Advisor](/azure/advisor/).

- *Advisor Score* is a core feature of Azure Advisor that aggregates Advisor recommendations into a simple, actionable score. This score lets you tell at a glance if you're taking the necessary steps to build reliable, secure, and cost-efficient solutions. It helps to prioritize the actions that yield the most significant improvement to the posture of your workload. The Advisor Score consists of an overall score, which can be broken down into five categories corresponding to each Well-Architected pillar. You can learn more about [Advisor Score](/azure/advisor/azure-advisor-score).

## Reliability

A reliable workload is both *resilient* and *available*. [Resiliency](./resiliency/index.yml) is the ability of the system to recover from failures and continue to function. The goal of resiliency is to return the application to a fully functioning state after failure. Availability is whether your users can access your workload when they need to.

For more information about resiliency, watch the following video that shows you how to start improving the reliability of your Azure workloads:

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://learn.microsoft.com/shows/azure-enablement/start-improving-the-reliability-of-your-azure-workloads--reliability-ep-1--well-architected-series/player]

<!-- markdownlint-enable MD034 -->

### Reliability guidance

The following resources offer guidance on designing and improving reliable Azure applications:

- [Reliability design principles][resiliency]
- [Design patterns for resiliency](./resiliency/reliability-patterns.md)
- Best practices:
  - [Transient fault handling][transient-fault-handling]
  - [Retry guidance for specific services][retry-service-specific]

For an overview of reliability principles, see [Reliability design principles](./resiliency/principles.md).

## Security

Think about [security](./security/index.yml) throughout the entire lifecycle of an application, from design and implementation to deployment and operations. The Azure platform provides protection against various threats, such as network intrusion and DDoS attacks. You still need to build security into your application and into your DevOps processes.

Learn to ask the right questions about secure application development on Azure by watching the following video:

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://learn.microsoft.com/shows/azure-enablement/ask-the-right-questions-about-secure-application-development-on-azure/player]

<!-- markdownlint-enable MD034 -->

### Security guidance

Consider the following broad security areas:

- [Identity management](./security/overview.md#identity-management)
- [Protect your infrastructure](./security/overview.md#protect-your-infrastructure)
- [Application security](./security/overview.md#application-security)
- [Data sovereignty and encryption](./security/overview.md#data-sovereignty-and-encryption)
- [Security resources](./security/overview.md#security-resources)

For more information, see [Overview of the security pillar](./security/overview.md).

## Cost optimization

When you design a cloud solution, focus on generating incremental value early. Apply the principles of *Build-Measure-Learn* to accelerate your time to market while avoiding capital-intensive solutions. See [What is the build-measure-learn feedback loop](/azure/cloud-adoption-framework/innovate/considerations/adoption#what-is-the-build-measure-learn-feedback-loop).

For more information, see [Cost optimization](./cost/index.yml) and watch the following video on how to start optimizing your Azure costs:

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://learn.microsoft.com/shows/azure-enablement/start-optimizing-your-azure-costs--cost-optimization-ep-1--well-architected-series/player]

<!-- markdownlint-enable MD034 -->

### Cost guidance

The following resources offer cost optimization guidance as you develop the Well-Architected Framework for your workload:

- Review [cost principles](./cost/overview.md)
- [Develop a cost model](./cost/design-model.md)
- Create [budgets and alerts](./cost/monitor-alert.md)
- Review the [cost optimization checklist](./cost/optimize-checklist.md)

For a high-level overview, see [Overview of the cost optimization pillar](./cost/overview.md).

## Operational excellence

[Operational excellence](./devops/index.yml) covers the operations and processes that keep an application running in production. Deployments must be reliable and predictable. Automate deployments to reduce the chance of human error. Fast and routine deployment processes don't slow down the release of new features or bug fixes. Equally important, you must be able to quickly roll back or roll forward if an update has problems.

For more information, watch the following video about bringing security into your DevOps practice on Azure:

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://learn.microsoft.com/shows/azure-enablement/devsecops-bringing-security-into-your-devops-practice-on-azure/player]

<!-- markdownlint-enable MD034 -->

### Operational excellence guidance

The following resources guide on designing and implementing DevOps practices for your Azure workload:

- [Operational excellence patterns](./devops/devops-patterns.md)
- Best practices: [Monitoring and diagnostics guidance][monitoring]

For a high-level summary, you can view [Overview of the operational excellence pillar](./devops/overview.md).

## Performance efficiency

[Performance efficiency](./scalability/index.yml) is the ability of your workload to scale to meet the demands placed on it by users efficiently. The main ways to achieve performance efficiency include scaling appropriately and implementing PaaS offerings with built-in scaling.

For more information, watch [Performance Efficiency: Fast & Furious: Optimizing for Quick and Reliable VM Deployments](/events/all-around-azure-well-architected-the-backstage-tour/performance-efficiency).

### Performance efficiency guidance

The following resources offer guidance on how to design and improve the performance efficiency posture of your Azure workload:

- [Performance efficiency patterns](./scalability/performance-efficiency-patterns.md)
- Best practices:
  - [Autoscaling][autoscale]
  - [Background jobs][background-jobs]
  - [Caching][caching]
  - [CDN][cdn]
  - [Data partitioning][data-partitioning]

For a high-level overview, see [Overview of the performance efficiency pillar](./scalability/overview.md).

## Next steps

Learn more about:

- [Azure Well-Architected Review](/assessments/?id=azure-architecture-review&mode=pre-assessment)
- [Well-Architected Series](https://channel9.msdn.com/Tags/well-architected-series)
- [Introduction to the Microsoft Azure Well-Architected Framework](/training/modules/azure-well-architected-introduction/)
- [Microsoft Defender for Cloud](/azure/security-center/)
- [Cloud Adoption Framework](/azure/cloud-adoption-framework/)
- [Azure Deployment Environments](/azure/deployment-environments/)
- [Microsoft Dev Box](/azure/dev-box/)

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
