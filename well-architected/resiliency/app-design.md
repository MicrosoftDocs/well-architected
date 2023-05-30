---
title: Design reliable Azure applications
description: Review design considerations for making sure that Azure applications are reliable, scalable, and resilient to failure.
author: martinekuan
ms.author: martinek
ms.date: 04/21/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-pillar-reliability
ms.custom:
  - How have you ensured that your application is resilient to failures?
  - article
---

# Design reliable Azure applications

Building a reliable application in the cloud differs from traditional application development. In the past, you might have purchased redundant higher-end hardware to minimize the chance of platform failure. However, for applications that run in the cloud, it's important to acknowledge up front that failures happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component. The kinds of failures that you can expect in the cloud are inherent to all highly distributed systems.

## Key points

- Use availability zones, where applicable, to improve reliability and optimize costs.
- Design applications to operate when impacted by failures.
- Use the native resiliency capabilities of platform as a service (PaaS) to support overall application reliability.
- Design to scale out and also scale in.
- Validate that required capacity is within Azure service scale limits and quotas.

## Use availability zones within a region

Design your application architecture to use [availability zones](/azure/reliability/availability-zones-overview) within a region. Availability zones can be used to optimize application availability within a region by providing datacenter-level fault tolerance. However, the application architecture must not share dependencies between zones to use them effectively.

> [!NOTE]
> Availability zones might introduce performance and cost considerations for applications that are extremely "chatty" across zones given the implied physical separation between each zone and inter-zone bandwidth charges. For this reason, availability zones can be considered to achieve a higher SLA for lower cost.

Consider if component proximity is required for application performance reasons. If all or part of the application is highly sensitive to latency, components might need to be co-located which can limit the applicability of multi-region and multi-zone strategies.

If your requirements demand even greater failure isolation than availability zones alone can offer, consider deploying to multiple regions. Multiple regions should be used for failover purposes in a disaster state. Other cost needs should be considered. Examples of cost needs are data and networking, and services such as [Azure Site Recovery](/azure/site-recovery/site-recovery-overview).

## Respond to failure

The possibility of failure is unavoidable in public cloud environments. As a result, applications require resilience to respond to outages and deliver reliability. The application should therefore be designed to operate even when impacted by regional, zonal, service, or component failures across critical application scenarios and functionality. Application operations might experience reduced functionality or degraded performance during an outage.

Define an availability strategy to capture how the application remains available during a failure state. The strategy should apply across all application components and the application deployment stamp as a whole, such as via multi-geo scale-unit deployment approach. There are cost implications as well. More resources need to be provisioned in advance to provide high availability. *Active-active* setup, while more expensive than single deployment, can balance cost by lowering load on one stamp and reducing the total amount of resources needed.

In addition to an availability strategy, define a Business Continuity Disaster Recovery (BCDR) strategy for the application and/or its key scenarios. A BCDR strategy should capture how the application responds to a disaster situation, such as a regional outage or the loss of a critical platform service, using either a redeployment, warm-spare *active-passive*, or hot-spare *active-active* approach.

To reduce costs, consider splitting application components and data into groups. For example:

- Must protect
- Nice to protect
- Ephemeral, can be rebuilt or lost instead of protected by the same policy

## Considerations to improve reliability

### Is the application designed to use managed services?

Azure-managed services provide native resiliency capabilities to support overall application reliability. PaaS offerings should be used to leverage these capabilities. PaaS options are easier to configure and administer. You don't need to provision VMs, set up VNets, manage patches and updates, or do all of the other overhead associated with running software on a VM. To learn more, see [Use managed services](/azure/architecture/guide/design-principles/managed-services).

### Has the application been designed to scale out?

Azure provides elastic scalability and you should design to scale out. However, applications must leverage a scale-unit approach to navigate service and subscription limits to ensure that individual components and the application as a whole can scale horizontally. Don't forget about scale in, which is important to reduce cost. For example, scale in and out for App Service is done via rules. Often customers write *scale out* rules and never write *scale in* rules, which leaves the App Service more expensive.

### Is the application deployed across multiple Azure subscriptions?

Understand the subscription landscape of the application and how components are organized within or across subscriptions when you analyze if relevant subscription limits or quotas can be navigated. Review Azure subscription and service limits to validate that required capacity is within Azure service scale limits and quotas. To learn more, see [Azure subscription and service limits](/azure/azure-resource-manager/management/azure-subscription-service-limits).

## Related links

- To learn how to minimize dependencies, see [Minimize coordination](/azure/architecture/guide/design-principles/minimize-coordination).
- For more information on fault-points and fault-modes, see [Failure mode analysis for Azure applications](/azure/architecture/resiliency/failure-mode-analysis).
- For information on managed services, see [Use platform as a service (PaaS) options](/azure/architecture/guide/design-principles/managed-services).

Go back to the main article: [Design for reliability](design-checklist.md)

## Next steps

> [!div class="nextstepaction"]
> [Resiliency and dependencies](./design-resiliency.md)
