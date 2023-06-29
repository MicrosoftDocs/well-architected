---
title: Overview of the operational excellence pillar
description: Learn how the operational excellence pillar covers the operations processes that keep an application running in production.
ms.author: martinek
author: martinekuan
ms.date: 04/26/2023
ms.topic: conceptual
ms.custom:
  - fasttrack-edit
  - overview
categories:
  - management-and-governance
---

# Overview of the operational excellence pillar

The operational excellence pillar covers the processes that keep an application running in production. Deployments must be reliable and predictable. Automated deployments reduce the chance of human error. Fast and routine deployment processes don't slow down the release of new features or bug fixes. It's equally important to be able to quickly roll backward or forward if an update has problems.

To assess your workload by using the tenets found in the [Microsoft Azure Well-Architected Framework](/azure/well-architected/), see the [Azure Well-Architected Review](/assessments/?id=azure-architecture-review&mode=pre-assessment).

We recommend the following video to help you achieve operational excellence with the Azure Well-Architected Framework:

<!-- markdownlint-disable MD034 -->

<br>

> [!VIDEO https://learn.microsoft.com/shows/azure-enablement/achieve-operational-excellence-with-azure-well-architected-framework/player]

<!-- markdownlint-enable MD034 -->

## Articles

The Microsoft Azure Well-Architected Framework includes the following articles in the operational excellence pillar:

| Operational excellence articles | Description |
|-------------------|-------------|
| [Release engineering: application development][app-design] | Provides guidance on how to design, build, and orchestrate workloads with operational excellence principles in mind. |
| [Monitoring operations of cloud applications][monitoring] | Shows how monitoring and diagnostics are essential to any workload and are crucial for cloud applications that run in a remote datacenter. |
| [Performance considerations for your deployment infrastructure][performance] | Describes the monitoring and management of performance and availability of software applications through operational excellence. |
| [Release engineering: deployment][deployment] | Shows how deploying your application code is one of the key factors that determines your application stability. |
| [Repeatable Infrastructure][iac] | Refers to best practices for deploying the platform where your application runs. Infrastructure provisioning is frequently known as *Deployment Automation* or *Infrastructure as code*. |
| [Testing your application and Azure environment][testing] | Shows how testing is fundamental for preparing for the unexpected and catching mistakes before they affect users. |

Enforcing resource-level rules through [Azure Policy](/azure/governance/policy/overview) helps ensure adoption of operational excellence best practices for all the assets, which supports your workload. For example, Azure Policy can help ensure all the virtual machines (VMs) supporting your workload adhere to a preapproved list of VM SKUs.

Azure Advisor provides a set of [Azure Policy recommendations](/azure/advisor/advisor-operational-excellence-recommendations#use-azure-policy-recommendations) to help you quickly identify opportunities to implement Azure Policy best practices for your workload.

Use the [DevOps checklist][devops-checklist] as a starting point to assess your DevOps culture and process.

## Next steps

Reference the operational excellence principles to guide you in your overall strategy.

> [!div class="nextstepaction"]
> [Principles](principles.md)

<!-- devops disciplines -->
[monitoring]: ./checklist.md
[performance]: ./release-engineering-performance.md
[deployment]: ./release-engineering-cd.md
[iac]: ./automation-infrastructure.md
[testing]: ./release-engineering-testing.md
[app-design]: /azure/well-architected/devops/release-engineering-app-dev

<!-- checklist -->
[devops-checklist]: /azure/architecture/checklist/dev-ops
