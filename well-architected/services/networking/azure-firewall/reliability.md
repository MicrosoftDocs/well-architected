---
title: Reliability and Azure Firewall
description: Focuses on the Azure Firewall service used in the Networking solution to provide design considerations, best-practice, and configuration recommendations related to Reliability.
author: v-stacywray
ms.author: robbymillsap
ms.date: 01/24/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - azure-firewall
categories:
  - networking
  - management-and-governance
---

# Reliability and Azure Firewall

An intelligent network firewall security service, [Azure Firewall](/azure/firewall/) provides threat protection for your cloud workloads running in Azure.

Features include:

- Stateful Firewall as a service
- Built-in high availability
- Unrestricted cloud scalability
- East-west and north-south traffic inspection

For more information, reference [What is Azure Firewall?](/azure/firewall/overview)

To learn how Azure Firewall supports a reliable workload, reference the following topics:

- [Introduction to Azure Firewall](/learn/modules/introduction-azure-firewall/)
- [Quickstart: Deploy Azure Firewall with Availability Zones](/azure/firewall/deploy-template)

## Design considerations

For more information about the Azure Firewall Service Level Agreement (SLA), reference [SLA for Azure Firewall](/support/legal/sla/azure-firewall/v1_1/).

Use Azure Firewall to govern:

- Outbound Azure traffic to the internet
- Non-HTTP/S inbound connections
- East-west traffic filtering (if required by customer)

Use Firewall Manager with Azure Virtual Wide Area Networks (WAN) to deploy and manage Azure Firewalls across Azure Virtual WAN Hubs, or in Hub VNets.

## Checklist

**Have you configured Azure Firewall with reliability in mind?**

> [!div class="checklist"]
> - Create a global Azure Firewall policy to govern the security posture across the global network environment. Assign the policy to all Azure Firewalls.
> - Configure supported third-party SaaS security providers within the Firewall Manager, if the customer wants to use these solutions to protect outbound connections.

## Configuration recommendations

Consider the following recommendation to optimize reliability when configuring Azure Firewall:

|Recommendation|Description|
|--------------|-----------|
|Create a global Azure Firewall policy to govern the security posture across global network environments. Assign the policy to all Azure Firewalls.|Allow for granular policies to meet requirements of specific regions by delegating incremental Firewall Policies to local security teams through role-based access control (RBAC).|

## Next step

> [!div class="nextstepaction"]
> [Security and Azure Firewall](security.md)