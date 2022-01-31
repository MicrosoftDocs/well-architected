---
title: Cost optimization and IP addresses
description: Focuses on the IP addresses service used in the Networking solution to provide best-practice and configuration recommendations related to Cost optimization.
author: v-stacywray
ms.author: robbymillsap
ms.date: 01/31/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - ip-addresses
categories:
  - networking
  - management-and-governance
---

# Cost optimization and IP addresses

[IP services](/azure/virtual-network/ip-services/) are a collection of IP address-related services that enable communication in an Azure Virtual Network. Public and private IP addresses are used in Azure for communication between resources. The communication with resources can occur in a private Azure Virtual Network and the public internet.

Key features include:

- [Public IP addresses](/azure/virtual-network/ip-services/ip-services-overview#public-ip-addresses)
- [Public IP address prefixes](/azure/virtual-network/ip-services/ip-services-overview#public-ip-address-prefixes)
- [Private IP addresses](/azure/virtual-network/ip-services/ip-services-overview#private-ip-addresses)
- [Routing preference](/azure/virtual-network/ip-services/ip-services-overview#routing-preference)
- [Routing preference unmetered](/azure/virtual-network/ip-services/ip-services-overview#routing-preference-unmetered)

For more information, reference [What is Azure Virtual Network IP Services?](/azure/virtual-network/ip-services/ip-services-overview)

To understand how IP services support a cost-optimized workload, reference the following articles:

- [IP addresses pricing](https://azure.microsoft.com/pricing/details/ip-addresses/)
- [Create, change, or delete an Azure public IP address](/azure/virtual-network/ip-services/virtual-network-public-ip-address)
- [Routing over public Internet (ISP network)](/azure/virtual-network/ip-services/routing-preference-overview#routing-over-public-internet-isp-network)

## Checklist

**Have you configured IP addresses with cost optimization in mind?**

> [!div class="checklist"]
> - PIPs (Public IPs) are free until used. Static PIPs are paid even when not assigned to resources.

## Configuration recommendations

Consider the following recommendation for cost optimization when configuring IP addresses:

|Recommendation|Description|
|--------------|-----------|
|PIPs (Public IPs) are free until used. Static PIPs are paid even when not assigned to resources.|There's a difference in billing for regular and static public IP addresses. Develop a process to look for orphan network interface cards (NICs) and PIPs that aren't being used in production and non-production.|

## Next step

> [!div class="nextstepaction"]
> [Cost optimization and Log Analytics](../../monitoring/log-analytics/cost-optimization.md)
