---
title: Overview of the operational excellence pillar
description: Understand the operational excellence pillar, which covers the operations processes that keep an application running in production.
ms.author: robbymillsap
author: david-stanford
ms.date: 03/03/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom:
  - fasttrack-edit
  - overview
products:
  - azure
categories:
  - management-and-governance
---

# Overview of the operational excellence pillar

The operational excellence pillar covers the operations processes that keep a workload running in production. Deployments must be reliable and predictable. Automated deployments reduce the chance of human error. Fast and routine deployment processes won't slow down the release of new features or bug fixes. Equally important, you must be able to quickly roll back or roll forward if an update has problems.

To assess your workload using the tenets found in the [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/), reference the [Microsoft Azure Well-Architected Review](/assessments/?id=azure-architecture-review&mode=pre-assessment).

We recommend the following video to help you achieve operational excellence with the Azure Well-Architected Framework:

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://docs.microsoft.com/shows/azure-enablement/achieve-operational-excellence-with-azure-well-architected-framework/player]

<!-- markdownlint-enable MD034 -->

## Topics

The Microsoft Azure Well-Architected Framework includes the following topics in the operational excellence pillar:

| Operational excellence topics | Description |
|-------------------|-------------|
| [Application design][app-design] | Provides guidance on how to design, build, and orchestrate workloads with DevOps principles in mind.  |
| [Monitoring][monitoring] | Monitoring helps ensure that a workload performs as expected. Best practices around monitoring help ensure observability as cloud deployments grow. |
| [Application performance management][performance] | This discipline applies DevOps practices to manage application performance and availablity. |
| [Code deployment][deployment] | How you deploy your application code is one of the key factors that determines your application stability.  |
| [Infrastructure provisioning][iac] | Frequently referred to as *Infrastructure as code*, this discipline refers to best practices for deploying the platform where your application will run. |
| [Testing][testing] | Testing is fundamental to prepare for the unexpected and to catch mistakes before they impact users. |
| [Azure Policy](/azure/governance/policy/overview) | Enforcing resource-level rules through Azure Policy helps ensure adoption of operational excellence best practices for all the assets, which support your workload. Azure Advisor provides [a set of Azure Policy recommendations](/azure/advisor/advisor-operational-excellence-recommendations#use-azure-policy-recommendations) to help you quickly identify opportunities to implement Azure Policy best practices for your workload.
| [DevOps checklist][devops-checklist] | Use the DevOps checklist to review your design from a management and DevOps standpoint.

## Next steps

Reference the operational excellence principles to guide you in your overall strategy.

> [!div class="nextstepaction"]
> [Principles](principles.md)

<!-- devops disciplines -->
[monitoring]: ./monitoring.md
[performance]: ./release-engineering-performance.md
[deployment]: ./release-engineering-cd.md
[iac]: ./automation-infrastructure.md
[testing]: ./release-engineering-testing.md
[app-design]: /azure/architecture/framework/devops/release-engineering-app-dev

<!-- checklist -->
[devops-checklist]: /azure/architecture/checklist/dev-ops
