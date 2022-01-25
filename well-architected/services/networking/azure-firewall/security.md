---
title: Security and Azure Firewall
description: Focuses on the Azure Firewall service used in the Networking solution to provide best-practice and configuration recommendations related to Security.
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

# Security and Azure Firewall

An intelligent firewall security service, [Azure Firewall](/azure/firewall/) provides threat protection for your cloud workloads running in Azure.

Features include:

- Stateful Firewall as a service
- Built-in high availability
- Unrestricted cloud scalability
- East-west and north-south traffic inspection

For more information, reference [What is Azure Firewall?](/azure/firewall/overview)

To learn how Azure Firewall supports a secure workload, reference the following topics:

- [Security baseline for Azure Firewall](/security/benchmark/azure/baselines/firewall-security-baseline?toc=/azure/firewall/toc.json)
- [Firewall threat intelligence-based filtering](/azure/firewall/threat-intel)
- [Deploy a security partner provider](/azure/firewall-manager/deploy-trusted-security-partner)

## Checklist

**Have you configured Azure Firewall with security in mind?**

> [!div class="checklist"]
> - Create a global Azure Firewall policy to govern the security posture across the global network environment. Assign the policy to all Azure Firewalls.
> - Enable threat intelligence on Azure Firewall.
> - Enable Domain Name System (DNS) Proxy and point the infrastructure DNS to Azure Firewall.
> - Configure the user-defined routes (UDR) to force traffic to Azure Firewall.
> - Validate any overlapping or missing peering in spoke networks.
> - Leverage Security Partner Providers for third-party Security as a Service (SECaaS) Offerings.
> - Use Azure Firewall Policies through Firewall Manager.
> - Use just-in-time systems (JIT) to control access to virtual machines (VMs) from the internet.

## Configuration recommendations

Consider the following recommendations to optimize security when configuring Azure Firewall:

|Recommendation|Description|
|--------------|-----------|
|Create a global Azure Firewall policy to govern the security posture across global network environments. Assign the policy to all Azure Firewalls.|Allow for granular policies to meet requirements of specific regions. Delegate incremental Firewall Policies to local security teams through role-based access control (RBAC).|
|Enable threat intelligence on Azure Firewall.|You can enable threat intelligence-based filtering for your firewall to alert and deny traffic from or to unknown IP addresses and domains. The IP addresses and domains are sourced from the Microsoft Threat Intelligence Feed. Intelligent Security Graph powers Microsoft threat intelligence and is used by multiple services including Azure Security Center.
|Enable Domain Name System (DNS) Proxy and point the infrastructure DNS to Azure Firewall.|By default, Azure Firewall uses Azure DNS. Custom DNS allows you to configure Azure Firewall to use Corporate DNS to resolve external and internal names.|
|Configure the user-defined routes (UDR) to force traffic to Azure Firewall.|Configure UDRs to force traffic to Azure Firewall for `SpoketoSpoke`, `SpoketoInternet`, and `SpoketoHybrid` connectivity.|
|Validate any overlapping or missing peering in spoke networks.|Check if there's any overlapping or missing peering in spoke networks.|
|Leverage Security Partner Providers for third-party Security as a Service (SECaaS) Offerings.|Security partner providers help to filter internet traffic through Virtual Private Network (VNET) to internet or Branch to internet.|
|Use Azure Firewall Policies through Firewall Manager.|Firewall Policies offer Parent and Child Policies. Parent Policy can be the baseline policy on all firewall in your Tenant and Child Policy. You can configure RBAC on Firewall Policies (Parent and Child Policy).|
|Use just-in-time systems (JIT) to control access to virtual machines (VMs) from the internet.|You can use Azure Security Center JIT to control access for clients connecting from the internet using Azure Firewall.|

## Next step

> [!div class="nextstepaction"]
> [Operational excellence and Azure Firewall](operational-excellence.md)