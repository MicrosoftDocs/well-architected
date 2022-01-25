---
title: Operational excellence and Azure Firewall
description: Focuses on the Azure Firewall service used in the Networking solution to provide design considerations, best-practice, and configuration recommendations related to Operational excellence.
author: v-stacywray
ms.author: robbymillsap
ms.date: 01/25/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - azure-firewall
categories:
  - networking
  - management-and-governance
---

# Operational excellence and Azure Firewall

An intelligent firewall security service, [Azure Firewall](/azure/firewall/) provides threat protection for your cloud workloads running in Azure.

Features include:

- Stateful Firewall as a service
- Built-in high availability
- Unrestricted cloud scalability
- East-west and north-south traffic inspection

For more information, reference [What is Azure Firewall?](/azure/firewall/overview)

To learn how Azure Firewall supports operational excellence, reference the following topics:

- [Azure Firewall Manager policy overview](/azure/firewall-manager/policy-overview)
- [Azure Firewall Policy rule sets](/azure/firewall/policy-rule-sets)
- [Azure Firewall central management](/azure/firewall/central-management)

## Design considerations

For more information about the Azure Firewall Service Level Agreement (SLA), reference [SLA for Azure Firewall](/support/legal/sla/azure-firewall/v1_1/).

Use Azure Firewall to govern:

- Outbound Azure traffic to the internet
- Non-HTTP/S inbound connections
- East-west traffic filtering (if required by customer)

Use Firewall Manager with Azure Virtual Wide Area Networks (WAN) to deploy and manage Azure Firewalls across Azure Virtual WAN Hubs, or in Hub VNets.

## Checklist

**Have you configured Azure Firewall with operational excellence in mind?**

> [!div class="checklist"]
> - Create a global Azure Firewall policy to govern the security posture across the global network environment. Assign the policy to all Azure Firewalls.
> - Configure supported third-party SaaS security providers within the Firewall Manager, if the customer wants to use these solutions to protect outbound connections.
> - Turn on logs for Azure Firewall.
> - Use Fully Qualified Domain Name (FQDN) tags on Azure Firewall.
> - Use Workbooks in Azure Log Analytics to visualize firewall logs.
> - Enable Azure Firewall connector in Azure Sentinel.

## Configuration recommendations

Consider the following recommendations for operational excellence when configuring Azure Firewall:

|Recommendation|Description|
|--------------|-----------|
|Create a global Azure Firewall policy to govern the security posture across global network environments. Assign the policy to all Azure Firewalls.|Allow for granular policies to meet requirements of specific regions. Delegate incremental Firewall Policies to local security teams through role-based access control (RBAC).|
|Turn on logs for Azure Firewall.|You can monitor Azure Firewall using firewall logs or workbooks. You can also use activity logs to audit operations on Azure Firewall resources.|
|Use FQDN tags on Azure Firewall.|FQDN tags make it easy to allow known Azure service network traffic through your firewall. For example, say you want to allow Windows Update network traffic through your firewall. You create an application rule and use the Windows Update tag. Now network traffic from Windows Update can flow through your firewall.|
|Enable Azure Firewall connector in Azure Sentinel.|You can use Azure Sentinel to create detections and logic apps for Azure Firewall.|

## Next step

> [!div class="nextstepaction"]
> [Reliability and Azure Front Door](reliability.md)
