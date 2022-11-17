---
title: API Management and cost optimization
description: Focuses on the API Management service used in the Networking solution to provide best-practice and configuration recommendations related to Cost optimization.
author: v-stacywray
ms.author: martinek
ms.date: 01/24/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - api-management
categories:
  - networking
  - management-and-governance
---

# API Management and cost optimization

Learn how to use [API Management](/azure/api-management/) to publish APIs to external, partner, and employee developers securely and at scale. This networking service is a hybrid, multicloud management platform for APIs across all environments.

Components include:

- [API gateway](/azure/api-management/api-management-key-concepts#api-gateway)
- [Management plane](/azure/api-management/api-management-key-concepts#management-plane)
- [Developer portal](/azure/api-management/api-management-key-concepts#developer-portal)

For more information, reference [About API Management](/azure/api-management/api-management-key-concepts).

To understand how API Management supports cost optimization for your workload, reference the following topics:

- [Automatically scale an Azure API Management instance](/azure/api-management/api-management-howto-autoscale)
- [Use a virtual network with Azure API Management](/azure/api-management/virtual-network-concepts?tabs=stv2)

## Checklist

**Have you configured API Management with cost optimization in mind?**

> [!div class="checklist"]
> - Configure autoscaling where appropriate.
> - Consider which features you need all the time.

## Configuration recommendations

Consider the following recommendations to optimize reliability when configuring your API Management service:

|Recommendation|Description|
|--------------|-----------|
|Configure autoscaling where appropriate.|Consider scaling your API Management instance up or down to control costs. You can [configure](/azure/api-management/api-management-howto-autoscale) API Management with Autoscale based on a metric or a specific count. Costs depend upon the number of units, which determines throughput in requests per seconds (RPS). An autoscaled API Management instance switches between scale units appropriate for RPS numbers during a specific time window. Autoscaling helps to achieve balance between cost optimization and performance.|
|Consider which features you need all the time.|Consider switching between Basic, Standard, and Premium tiers. If a workload does not need features available in higher tiers, then consider switching to a lower tier. As an example, a workload may need just `1GB` of cache during off-peak periods compared to `5GB` of cache during peak periods. Costs associated with such a workload can be reduced by switching from a Premium to Standard tier during off-peak periods and back to a Premium tier during peak periods. This process can be automated as a job using [Set-AzApiManagement](/powershell/module/az.apimanagement/set-azapimanagement?view=azps-7.1.0&viewFallbackFrom=azps-5.4.0&preserve-view=true) cmdlet. Refer to [API Management pricing](https://azure.microsoft.com/pricing/details/api-management/) about features available in different API Management tiers.|

## Next step

> [!div class="nextstepaction"]
> [API Management and operational excellence](operational-excellence.md)