---
title: Cost optimization design principles
description: Understand cost optimization principles. These principles are a series of important considerations that can help achieve business objectives and cost justification.
author: david-stanford
ms.author: robbymillsap
ms.date: 02/10/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom:
  - overview
products:
  - azure
  - azure-cost-management
categories:
  - management-and-governance
---

# Cost optimization design principles

A cost-effective workload is driven by business goals and the return on investment (ROI) while staying within a given budget. The principles of cost optimization are a series of important considerations that can help achieve both business objectives and cost justification.

To assess your workload using the tenets found in the Azure Well-Architected Framework, reference the [Microsoft Azure Well-Architected Review](/assessments/?id=azure-architecture-review&mode=pre-assessment).

The following design principles provide:

- Context for questions
- Why a certain aspect is important
- How an aspect is applicable to Cost optimization

These critical design principles are used as lenses to assess the Cost optimization of an application deployed on Azure. These lenses provide a framework for the application assessment questions.

## Choose the correct resources

Choose the right resources that are aligned with business goals and can handle the performance of the workload.

When onboarding new workloads, explore the possibility of modernization and cloud native offerings where possible. It's typically more cost effective to use  Platform as a Service (PaaS) or Software as a Service (SaaS), as opposed to Infrastructure as a Service (IaaS).

An inappropriate or misconfigured service can affect cost. For example, building a multi-region service when the service levels don't require high-availability or geo-redundancy will increase cost without any reasonable business justification.

## Set up budgets and maintain cost constraints

Every design choice has cost implications. Consider the budget constraints set by the company, before choosing:

- An architectural pattern
- Azure service
- A price model for the service

As part of design, identify acceptable boundaries on:

- Scale
- Redundancy
- Performance against cost

After estimating the initial cost, set budgets and alerts at different scopes to measure the cost. One cost driver can be unrestricted resources. These resources typically need to scale and consume more cost to meet demand.

## Dynamically allocate and de-allocate resources

To match performance needs, dynamically allocate and de-allocate resources.

Identify idle or underutilized resources through Azure Advisor or other tools, and:

- Reconfigure
- Consolidate
- (or) Shut down

## Optimize workloads, aim for scalable costs

A key benefit of the cloud is the ability to scale dynamically. The workload cost should scale linearly with demand. You can save cost through automatic scaling.

Recommendations:

- Consider the usage metrics and performance to determine the number of instances.
- Choose smaller instances for a highly variable workload.
- Scale out, rather than up, to get the required level of performance. This choice will enable you to make your cost calculations and estimates granular.

The cost management process should be:

- Rigorous
- Iterative
- A key principle of responsible cloud optimization

## Continuously monitor and optimize cost management

To provision resources dynamically and to scale with demand:

- Conduct regular cost reviews
- Measure capacity needs
- Forecast capacity needs

If you're just starting this process, review [enable success during a cloud adoption journey](/azure/cloud-adoption-framework/get-started/enable).

### Next step

> [!div class="nextstepaction"]
> [Design checklist](./design-checklist.md)
