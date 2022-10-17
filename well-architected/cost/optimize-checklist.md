---
title: Checklist - Optimize cost
description: Use these checklist considerations to help monitor and optimize workloads by using the right resources and sizes.
author: PageWriter-MSFT
ms.author: robbymillsap
ms.date: 12/20/2021
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

- **Review the underutilized resources**. Evaluate CPU utilization and network throughput over time to check if the resources are used adequately. Azure Advisor identifies underutilized virtual machines. You can choose to decommission, resize, or shut down the machine to meet the cost requirements.

  - [Resize virtual machines](./optimize-vm.md#resize-virtual-machines)
  - [Shutdown the under utilized instances](./optimize-vm.md#shut-down-the-under-utilized-instances)

- **Continuously take action on the cost reviews**. Treat cost optimization as a process, rather than a point-in-time activity.  Use tooling in Azure that provides recommendations on usage or cost optimization. Review the cost management recommendations and take action. Make sure that all stakeholders are in agreement about the implementation and timing of the change.

  - **Recommended** tab in the [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/docs)
  - Recommendations in the [Cost Management Power BI app](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)
  - Recommendations in [Azure Advisor](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview)
  - Recommendations using [Reservation REST APIs](/rest/api/consumption/reservationrecommendations/list)

- **Use savings plans**. By using savings plans, you get the most flexible savings for dynamic workloads, while accommodating for planned or unplanned changes. Consider that with with savings plan you commit to a fixed hourly dollar amount collectively on compute services, globally.
  - [Savings plans](/azure/cost-management-billing/savings-plan)

- **Use Azure reservations**. You get the greatest cost savings for stable, predictable workloads with no planned changes by using reserved instances or capacity. Consider that you are often committing to a specific SKUs, throughput, or utilization targets, often in a particular Azure region.
  - [Azure Reservations](/azure/cost-management-billing/reservations/save-compute-costs-reservations)
  
- **Use discount prices**. These methods of buying Azure resources can lower costs.

  - [Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-benefit)
  - [Azure Reservations](https://azure.microsoft.com/reservations)

  There are also payment plans offered at a lower cost:

  - [Microsoft Azure Enterprise Agreement](/azure/cost-management-billing/manage/ea-portal-get-started)
  - [Enterprise Dev Test Subscription](https://azure.microsoft.com/offers/ms-azr-0148p/)
  - [Cloud Service Provider (Partner Program)](https://partner.microsoft.com/membership/cloud-solution-provider)

- **Have a scale-in and scale-out policy**. In a cost-optimized architecture, costs scale linearly with demand. Increasing customer base shouldn't require more investment in infrastructure. Conversely, if demand drops, scale-down of unused resources. Autoscale Azure resources when possible.
  - [Autoscale instances](./optimize-autoscale.md)

- **Reevaluate design choices**. Analyze the cost reports and forecast the capacity needs. You might need to change some design choices.

  - **Choose the right storage tier**. Consider using hot, cold, archive tier for storage account data. Storage accounts can provide automated tiering and lifecycle management. For more information, see [Review your storage options](/azure/cloud-adoption-framework/ready/considerations/storage-options)

  - **Choose the right data store**. Instead of using one data store service, use a mix of data store depending on the type of data you need to store for each workload. For more information, reference [Choose the right data store](/azure/architecture//guide/technology-choices/data-store-overview).

  - **Choose Spot VMs for low priority workloads**. Spot VMs are ideal for workloads that can be interrupted, such as highly parallel batch processing jobs.

    - [Spot VMs](./optimize-vm.md#spot-vms)

  - **Optimize data transfer**. Only deploy to multiple regions if your service levels require it for either availability or geo-distribution. Data going out of Azure datacenters can add cost because pricing is based on Billing Zones.

    - [Traffic across billing zones and regions](./design-regions.md#traffic-across-billing-zones-and-regions)

  - **Reduce load on servers**. Use Azure Content Delivery Network (CDN) and caching service to reduce load on front-end servers. Caching is suitable for servers that are continually rendering dynamic content that doesn't change frequently.

  - **Use managed services**. Measure the cost of maintaining infrastructure and replace it with Azure PaaS or SaaS services.

    - [Managed services](./design-paas.md)
