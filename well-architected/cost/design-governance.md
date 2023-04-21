---
title: Cost governance for an Azure workload
description: Understand how centralized governance functions in Azure can support your team with cost management. Follow organizational policies that define cost boundaries.
author: PageWriter-MSFT
ms.author: martinek
ms.date: 04/20/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-pillar-optimization
ms.custom:
  - article
  - internal-intro
products:
  - azure
  - azure-cost-management
---

# Governance

Governance helps with cost management. This work benefits your ongoing cost review process and offers a level of protection for new resources.

## Understand how centralized governance functions can support your team

Centralized governance can relieve some of the burden related to on-going cost monitoring and optimization. But it's an augmentation to the workload team's responsibilities, not a replacement. To understand how centralized cloud governance teams operate, see [Govern methodology for the cloud](/azure/cloud-adoption-framework/govern/methodology).

- For more detailed information on cost optimization, see [Cost Management discipline](/azure/cloud-adoption-framework/govern/cost-management/).
- For an example of the types of guardrails provided by a governance team, see [Cost management discipline improvement](/azure/cloud-adoption-framework/govern/cost-management/discipline-improvement). This article includes examples of suggested tags and policies for improving cost governance.
- If centralized governance teams don't support your team, see [Cloud governance function](/azure/cloud-adoption-framework/organize/cloud-governance). This article helps you understand the activities your team might consider including in each sprint.

## Follow organizational policies that define cost boundaries

Use policies to ensure compliance with identified cost boundaries. Policies eliminate the need for manual resource approval and they speed up provisioning.

Azure Policy can set rules on management groups, subscriptions, and resources groups. The policies control clouds service resource SKU size, replication features, and supported locations. Use policies to prevent provisioning expensive resources. Identify the built-in Azure policies that can help lower costs. For even more control, create custom policies.

For more information, see [Create management groups for resource organization and management](/azure/governance/management-groups/create).

For more information on how to control the group who manages resources in the subscription, see [Azure built-in roles](/azure/role-based-access-control/built-in-roles).

Set limits or quotas to prevent unexpected costs. For more information, see [Azure subscription and service limits, quotas, and constraints](/azure/azure-resource-manager/management/azure-subscription-service-limits).

## Enforce resource tagging

Use tags on resources and resource groups to track incurred costs. Identify the service meters that you can't tag or view in the cost analysis tool in the Azure portal.

The advantages of tagging include:

- The cost can be reported to an owner, an application, a business department, or a project initiative. This feature is useful because the overall cost can span multiple resources, locations, and subscriptions.
- Filter information. Use filtering in the Azure portal cost analysis tool to get granular reports.

There are some limitations:

- You can't tag all Azure resources and not all taggable resources in Azure are accounted for in the Azure cost analysis tool.
