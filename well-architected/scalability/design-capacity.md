---
title: Design applications for capacity
description: Plan to meet capacity design requirements for performance efficiency. Understand options to reduce cost.
author: martinekuan
ms.author: martinek
ms.date: 05/02/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-pillar-efficiency
categories:
  - management-and-governance
ms.custom:
  - How are you ensuring you have sufficient capacity?
  - article
---
# Design applications for capacity

This article describes ways you can design applications for capacity to optimize performance efficiency. Azure offers many options to meet capacity requirements as your business grows, which can also minimize costs.

## Scale out rather than scaling up

When using cloud technologies, it's often easier, cheaper, and more effective to *scale out*, or add resources, than to *scale up*, or increase the capacity of a resource. If you plan to scale up by increasing the capacity allocated to your hosts, you reach a limit where it becomes cost-prohibitive to scale any further. Scaling up also often requires downtime for servers to reboot. Instead, plan to scale your application tier by adding extra infrastructure to meet demand. Be sure to remove the resources when they're no longer needed.

## Prepare infrastructure for large-scale events

Large-scale application design takes careful planning and possibly complex implementation. Work with your business and marketing teams to prepare for large-scale events like large sporting events, Black Friday, or marketing campaigns. You can anticipate sudden spikes in traffic due to these events to prepare infrastructure ahead of time.

A fundamental Azure design principle is to scale out by adding machines or service instances based on increased demand. Scaling out can be better than purchasing more hardware that might not be in your budget. Depending on your payment plan, you don't have to pay for idle virtual machines (VMs), or need to reserve capacity in advance. A pay-as-you-go plan can be ideal for applications that need to meet planned spikes in traffic.

> [!NOTE]
> Don't plan for capacity to always meet the highest level of expected demand. An inappropriate or misconfigured service can impact cost. For example, building a multiregion service when the service levels don't require high availability or georedundancy increases cost without reasonable business justification.

## Choose the right resources

Right-sizing your infrastructure to meet the needs of your applications can save costs compared to the one-size-fits-all solution often employed with on-premises hardware. You can choose various options when you deploy Azure VMs to support workloads.

Each VM type has specific features and different combinations of CPU, memory, and disks. For example, [B-Series VMs](/azure/virtual-machines/sizes-b-series-burstable) are ideal for workloads that don't need full CPU performance continuously, like web servers, proofs of concept, small databases, and development build environments. B-Series VMs offer a cost effective way to deploy workloads that have bursts in performance and don't need full continuous CPU performance. For a list of VM sizes and recommended uses, see [Sizes for virtual machines in Azure](/azure/virtual-machines/sizes).

Continually monitor workloads to find out if your VMs aren't optimized or have frequent unused periods. It makes sense to either downscale these VMs by using virtual machine scale sets, or shut them down. You can optimize VMs with Azure Automation, virtual machine scale sets, autoshutdown, or scripted or third-party solutions. For more information, see [Automate VM optimization](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs#best-practice-automate-vm-optimization).

Along with choosing the right VMs, selecting the right storage type can save your organization significant monthly costs. For a list of storage data types, access tiers, storage account types, and storage redundancy options, see [Select the right storage](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs#best-practice-select-the-right-storage).

## Use metrics to fine-tune scaling

It can be difficult to understand the relationship between metrics and capacity requirements, especially when an application is initially deployed. Provision a little extra capacity at the beginning, and then monitor and tune the *autoscaling* rules to bring capacity closer to the actual load.

Autoscaling lets you run the right amount of resources to handle your app load by adding resources or scaling out to handle increases in load such as seasonal workloads and customer-facing applications. After you configure the autoscaling rules, monitor the performance of your application over time. Use the results of this monitoring to adjust how the system scales if necessary.

[Azure Monitor autoscale](/azure/azure-monitor/platform/autoscale-overview) provides a common set of autoscaling functionality for virtual machine scale sets and Azure App Service. Scaling can be done on a schedule, or based on a runtime metric, such as CPU or memory usage. For example, you can scale out by one instance if average CPU usage is above 70%, and scale in by one instance if CPU usage falls below 50 percent.

The default autoscaling rules execute an autoscaling action at the correct time to prevent the system from reacting too quickly. For more information, see [Autoscaling](/azure/architecture/best-practices/auto-scaling).

For a list of built-in metrics, see [Azure Monitor autoscaling common metrics](/azure/azure-monitor/platform/autoscale-common-metrics). You can also implement custom metrics by using [Application Insights](/azure/azure-monitor/app/app-insights-overview) to monitor the performance of your live applications. Some Azure services use different scaling methods.

## Scale preemptively and schedule autoscaling

Preemptive scaling based on historical data can help ensure your application performs consistently, even though your metrics haven't yet indicated the need to scale. Schedule-based rules allow you to scale when you see time patterns in your load, and want to scale before a possible load increase or decrease occurs. For example, you can set a trigger attribute to scale out to 10 instances on weekdays, and scale in to four instances on weekends.

If you can predict the load on the application, consider using scheduled autoscaling, which adds and removes instances to meet anticipated peaks in demand. For more information, see [Use Azure Monitor autoscale](/azure/architecture/best-practices/auto-scaling#use-azure-monitor-autoscale).

## Test capacity planning

The business should communicate any fluctuation in expected load for performance testing. Load can be impacted by world events, such as political, economic, or weather changes. Load can also be affected by marketing initiatives, such as sales or promotions, or by seasonal events like holidays.

Test load variations prior to events, including unexpected load variations, to ensure that your application can scale. You should also ensure that all regions can adequately scale to support total load if one region fails.

## Next steps

> [!div class="nextstepaction"]
> [Performance testing](./performance-test.md)

