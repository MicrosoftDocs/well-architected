---
title: Capacity best practices
description: Learn about best practices to provide sufficient capacity to meet your application's performance efficiency needs.
author: martinekuan
ms.author: martinek
ms.date: 05/03/2023
ms.topic: conceptual
ms.custom:
  - How are you ensuring you have sufficient capacity?
  - article
---

# Capacity best practices

The following practices help you maintain sufficient capacity to meet your application's performance efficiency needs.

## Use Content Delivery Networks

Content Delivery Networks (CDNs) cache and deliver static objects from Azure Blob Storage, web applications, or any publicly accessible web server by using the closest point of presence (POP) server. CDNs can also accelerate dynamic content, which can't be cached, by using various network and routing optimizations. For more information, see [What is a content delivery network on Azure](/azure/cdn/cdn-overview).

## Prepare for large-scale events

Work with your business and marketing teams to prepare for large-scale events like sports matches, sales, or marketing campaigns. You can anticipate sudden spikes in traffic caused by these events to prepare infrastructure ahead of time.

## Choose the right resources

Right sizing your infrastructure to meet the needs of your applications can save costs compared to the one-size-fits-all solution often employed with on-premises hardware. Identify the needs of your application and choose the resources that best fit those needs. For more information, see [Sizes for virtual machines in Azure](/azure/virtual-machines/windows/sizes).

## Use metrics to adjust scaling policies

Autoscaling rules can use a detection mechanism based on a measured trigger attribute, such as CPU usage or queue length. These rules use an aggregated value over time, rather than instantaneous values, to trigger an autoscaling action. By default, the aggregate is an average of the values, which prevents the system from reacting too quickly or causing rapid oscillation. For more information, see [Use Azure Monitor autoscale](/azure/architecture/best-practices/auto-scaling#use-azure-monitor-autoscale).

## Preemptively scale based on trends

Preemptive scaling based on historical data can ensure your application has consistent performance, even though your metrics haven't yet indicated the need to scale. If you can predict the load on the application, consider using scheduled autoscaling, which adds and removes instances to meet anticipated peaks in demand. For more information, see [Autoscaling](/azure/architecture/best-practices/auto-scaling).

## Automate scale operations

Fluctuation in application traffic is expected. To ensure optimal operation conditions are maintained, such variations should be met by automated scalability operations. Autoscaling enables a platform as a service (PaaS) or infrastructure as a service (IaaS) to scale within a preconfigured range of resources. However, provisioning or deprovisioning capacity, for example adding scale units like clusters, compute instances, or deployments, is more advanced and complex. The process should be codified and automated, and the effects of adding or removing capacity should be well understood. For more information, see [Repeatable infrastructure](../devops/automation-infrastructure.md).

## Monitor application health

Any change in the health state of application components can influence the capacity demands on other components. These impacts need to be fully understood, and autoscaling measures must be in place to handle the impacts. For example, if an outage in an external API is mitigated by writing messages into a retry queue, this queue needs to handle the sudden spikes in load.
