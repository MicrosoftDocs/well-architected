---
title: Microsoft Azure Well-Architected Framework review - Azure Firewall
description: Provides architectural best practices for the Azure Firewall.
author: vhorne
ms.author: victorh
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.date: 04/14/2022
---

# Azure Well-Architected Framework review - Azure Firewall

This article provides architectural best practices for Azure Firewall. The guidance is based on the five pillars of architecture excellence:

- [Reliability](#reliability)
- [Security](#security)
- [Cost optimization](#cost-optimization)
- [Operational excellence](#operational-excellence)
- [Performance efficiency](#performance-efficiency)

We assume that you have working knowledge of Azure Firewall and are well versed with its features. For more information, see [Azure Firewall Standard features](/azure/firewall/features).

## Prerequisites

- Understanding the Well-Architected Framework pillars can help produce a high-quality, stable, and efficient cloud architecture. We recommend that you review your workload by using the [Azure Well-Architected Framework Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.
- Use a reference architecture to review the considerations based on the guidance provided in this article. We recommend that you start with [Ref 1](/azure/architecture/reference-architectures) and [Ref 2](/azure/architecture).

## Reliability

Azure Firewall ia an intelligent firewall security service that provides threat protection for your cloud workloads running in Azure.

Features include:

- Stateful Firewall as a service
- Built-in high availability
- Unrestricted cloud scalability
- East-west and north-south traffic inspection

To learn how Azure Firewall supports a reliable workload, see the following articles:

- [Introduction to Azure Firewall](/learn/modules/introduction-azure-firewall/)
- [Quickstart: Deploy Azure Firewall with Availability Zones](/azure/firewall/deploy-template)

### Design checklist

As you make design choices for Azure Firewall, review the [Reliability design principles](../../resiliency/principles.md).

<!--
The following are the Reliability design principles:
 
Design for business requirements
Design for failure
Observe application health
Drive automation
Design for self-healing
Design for scale-out
-->

> [!div class="checklist"]
> - Deploy using a secured virtual hub
> - Use a global Azure Firewall policy
> - Determine if you want to use use third-party security as a service (SECaaS) providers.

### Recommendations

<!--
In table format, present the top recommendations that demonstrate how to achieve the points described in the design checklist.
-->

Explore the following table of recommendations to optimize your Azure Firewall configuration for Reliability.

| Recommendation | Benefit |
|--------|----|
|Use Firewall Manager with Azure Virtual Wide Area Networks (WAN) to deploy and manage Azure Firewalls across Azure Virtual WAN Hubs, or in Hub VNets.|Easily create hub-and-spoke and transitive architectures with native security services for traffic governance and protection.|
|Create a global Azure Firewall policy to govern the security posture across global network environments. Assign the policy to all Azure Firewalls.|Allow for granular policies to meet requirements of specific regions. Delegate incremental Firewall Policies to local security Teams through role-based access control (RBAC).|
|Configure supported third-party SaaS security providers within the Firewall Manager, if you want to use these solutions to protect outbound connections.|You can use your familiar, best-in-breed, third-party security as a service (SECaaS) offerings to protect Internet access for your users.|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Security

Security is one of the most important aspects of any architecture. An intelligent firewall security service, [Azure Firewall](/azure/firewall/) provides threat protection for your cloud workloads running in Azure.

### Design checklist

As you make design choices for Azure Firewall, review the [Security design principles](../../security/security-principles.md).

<!--
The following are the Security design principles:
 
- Plan resources and how to harden them
- Automate and use least privilege
- Classify and encrypt data
- Monitor system security, plan incident response
- Identify and protect endpoints
- Protect against code-level vulnerabilities
- Model and test against potential threats
-->

> [!div class="checklist"]
> - Use a global Azure Firewall policy
> - Use threat intelligence
> - Use DNS proxy
> - Direct network traffic through Azure Firewall
> - Validate spoke networks
> - Determine if you want to use use third-party security as a service (SECaaS) providers.
> - Use just-in-time systems (JIT)

### Recommendations

<!--
In table format, present the top recommendations that demonstrate how to achieve the points described in the design checklist.
-->

Explore the following table of recommendations to optimize your Azure Firewall configuration for Security.

| Recommendation | Benefit |
|--------|----|
|Create a global Azure Firewall policy to govern the security posture across global network environments. Assign the policy to all Azure Firewalls.|Allow for granular policies to meet requirements of specific regions. Delegate incremental Firewall Policies to local security teams through role-based access control (RBAC).|
|Enable threat intelligence on Azure Firewall.|You can enable threat intelligence-based filtering for your firewall to alert and deny traffic from or to unknown IP addresses and domains. The IP addresses and domains are sourced from the Microsoft Threat Intelligence Feed. Intelligent Security Graph powers Microsoft threat intelligence and is used by multiple services including Azure Security Center.
|Enable Domain Name System (DNS) Proxy and point the infrastructure DNS to Azure Firewall.|By default, Azure Firewall uses Azure DNS. Custom DNS allows you to configure Azure Firewall to use Corporate DNS to resolve external and internal names.|
|Configure the user-defined routes (UDR) to force traffic to Azure Firewall.|Configure UDRs to force traffic to Azure Firewall for `SpoketoSpoke`, `SpoketoInternet`, and `SpoketoHybrid` connectivity.|
|Validate any overlapping or missing peering in spoke networks.|Check if there's any overlapping or missing peering in spoke networks.|
|Leverage Security Partner Providers for third-party Security as a Service (SECaaS) Offerings.|Security partner providers help to filter internet traffic through Virtual Private Network (VNET) to internet or Branch to internet.|
|Use just-in-time systems (JIT) to control access to virtual machines (VMs) from the internet.|You can use Azure Security Center JIT to control access for clients connecting from the internet using Azure Firewall.|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

### Policy definitions

- XXX

All built-in policy definitions related to Azure Networking are listed in [Built-in policies - Network](/azure/governance/policy/samples/built-in-policies#network).

## Cost optimization

Cost optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies. We recommend you review the [Cost optimization design principles](../../cost/principles.md).

### Design checklist

> [!div class="checklist"]
> - XXX

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for Cost optimization.

| Recommendation | Benefit |
|--------|----|
| XXX | XXX|

For more suggestions, see [Principles of the cost optimization pillar](/azure/architecture/framework/cost/overview).

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Operational excellence

Monitoring and diagnostics are crucial. Not only can you measure performance statistics but also use metrics troubleshoot and remediate issues quickly.

### Design checklist

As you make design choices for Azure Firewall, review the [Operational excellence design principles](../../devops/principles.md).

<!--
The following are the Operational excellence design principles:

- Optimize build and release processes
- Understand operational health
- Rehearse recovery and practice failure
- Embrace continuous operational improvement
- Use loosely coupled architecture

-->

> [!div class="checklist"]
> - Use logs for monitoring
> - Use tags when possible allow traffic through the firewall
> - Use Workbooks
> - Use Azure Firewall connector in Azure Sentinel

### Recommendations

<!--
In table format, present the top recommendations that demonstrate how to achieve the points described in the design checklist.
-->

Explore the following table of recommendations to optimize your Azure Firewall configuration for Operational excellence.

| Recommendation | Benefit |
|--------|----|
|Turn on logs for Azure Firewall.|You can monitor Azure Firewall using firewall logs or workbooks. You can also use activity logs to audit operations on Azure Firewall resources.|
|Use Fully Qualified Domain Name (FQDN) tags on Azure Firewall.|FQDN tags make it easy to allow known Azure service network traffic through your firewall. For example, say you want to allow Windows Update network traffic through your firewall. You create an application rule and use the Windows Update tag. Now network traffic from Windows Update can flow through your firewall.|
|Use Workbooks in Azure Log Analytics|Helps you visualize firewall logs.|
|Enable Azure Firewall connector in Azure Sentinel.|You can use Azure Sentinel to create detections and logic apps for Azure Firewall.|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Performance efficiency

Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. We recommend you review the [Performance efficiency principles](../../scalability/principles.md).

### Design checklist

> [!div class="checklist"]
> XXX

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for Performance efficiency.

| Recommendation | Benefit |
|--------|----|
| XXX | XXX|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your Azure Firewall.

### Reliability

- XXX

## Additional resources

### Azure Architecture Center guidance

- XXX

## Next steps

- Deploy an Azure Firewall to see how it works: [Tutorial: Deploy and configure Azure Firewall and policy using the Azure portal](/azure/firewall/tutorial-firewall-deploy-portal-policy)
