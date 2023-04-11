---
title: Checklist - Optimize cost
description: Learn how to follow checklist considerations to monitor and optimize workloads by using the right resources and sizes.
author: PageWriter-MSFT
ms.author: martinek
ms.date: 04/11/2023
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - azure-advisor
  - azure
  - azure-cost-management
ms.custom:
  - article
  - internal-intro
---

# Checklist - Optimize cost

Continue to monitor and optimize the workload by using the right resources and sizes. Use this checklist to optimize a workload.

- **Review the underutilized resources**. Evaluate CPU utilization and network throughput over time to check if the resources are used adequately. Azure Advisor identifies underutilized virtual machines (VMs). You can choose to decommission, resize, or shut down the machine to meet the cost requirements.

  - [Resize virtual machines](./optimize-vm.md#resize-virtual-machines)
  - [Shutdown the underutilized instances](./optimize-vm.md#shut-down-underutilized-instances)

**Auto start and stop VMs tool to non-production VMs**. The start and stop VMs v2 feature starts or stops Azure Virtual Machines instances across multiple subscriptions. It starts or stops virtual machines on user-defined schedules. For more informations, see [Start and stop VMs](/azure/azure-functions/start-stop-vms/overview).

- **Continuously take action on the cost reviews**. Treat cost optimization as a process, rather than a point-in-time activity. Use tooling in Azure that provides recommendations on usage or cost optimization. Review the cost management recommendations and take action. Make sure that all stakeholders are in agreement about the implementation and timing of the change.

  - **Recommendations for cost management** in the [Azure portal](https://ms.portal.azure.com/#view/Microsoft_Azure_CostManagement/Menu/~/overview/openedBy/AzurePortal)
  - Recommendations in the [Cost Management Power BI app](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)
  - Recommendations in [Azure Advisor](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview)
  - Recommendations using [Reservation REST APIs](/rest/api/consumption/reservationrecommendations/list)

- **Use savings plans**. By using savings plans, you get the most flexible savings for dynamic workloads, while accommodating planned or unplanned changes. With savings plans, you commit to a fixed, hourly, dollar amount, collectively, on compute services, globally.
  - [Savings plans](/azure/cost-management-billing/savings-plan)

- **Use Azure reservations**. You get the greatest cost savings for stable, predictable workloads with no planned changes by using reserved instances or capacity. Consider that you're often committing to specific SKUs, throughput, or utilization targets, often in a particular Azure region.
  - [Azure reservations](/azure/cost-management-billing/reservations/save-compute-costs-reservations)
  
- **Use discount prices**. The following methods of buying Azure resources can lower costs.

  - [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit)
  - [Azure reservations](https://azure.microsoft.com/reservations)

  There are also payment plans offered at a lower cost:

  - [Microsoft Azure Enterprise portal](/azure/cost-management-billing/manage/ea-portal-get-started)
  - [Enterprise Dev Test Subscription](https://azure.microsoft.com/offers/ms-azr-0148p/)
  - [Microsoft Cloud Solution Provider program](https://partner.microsoft.com/membership/cloud-solution-provider)

- **Have a scale-in and scale-out policy**. In a cost-optimized architecture, costs scale linearly with demand. Increasing customer base shouldn't require more investment in infrastructure. Conversely, if demand drops, scale-down unused resources. And autoscale Azure resources when possible.
  - [Autoscale instances](./optimize-autoscale.md)
  - [Azure SQL Database elastic pools](/azure/azure-sql/database/elastic-pool-overview) are a simple, cost-effective solution for managing and scaling multiple databases that have varying and unpredictable usage demands
  - [SQL Serverless](/azure/azure-sql/database/serverless-tier-overview) is a compute tier for single databases in Azure SQL Database that automatically scales compute based on workload demand and bills for the amount of compute that you use per second.

- **Reevaluate design choices**. Analyze the cost reports and forecast the capacity needs. You might need to change some design choices.

  - **Choose the right storage tier**. Consider using hot, cool, and archive tiers for storage account data. Storage accounts can provide automated tiering and lifecycle management. For more information, see [Review your storage options](/azure/cloud-adoption-framework/ready/considerations/storage-options).

  - **Choose the right data store**. Instead of using one data store service, use a mix of data store depending on the type of data you need to store for each workload. For more information, see [Choose the right data store](/azure/architecture//guide/technology-choices/data-store-overview).

  - **Choose Spot VMs for low priority workloads**. Spot VMs are ideal for workloads that can be interrupted, such as highly parallel batch processing jobs.

    - [Spot VMs](./optimize-vm.md#spot-vms)

  - **Optimize data transfer**. Only deploy to multiple regions if your service levels require it for either availability or geo-distribution. Data going out of Azure data centers can add cost because pricing is based on billing zones.

    - [Traffic across billing zones and regions](./design-regions.md#traffic-across-billing-zones-and-regions)

  - **Reduce load on servers**. Use Azure Content Delivery Network (CDN) and caching service to reduce load on front-end servers. Caching is suitable for servers that are continually rendering dynamic content that doesn't change frequently.

  - **Use managed services**. Measure the cost of maintaining infrastructure and replace it with Azure platform as a service (PaaS) or software as a service (SaaS) services.

    - [Managed services](./design-paas.md)
