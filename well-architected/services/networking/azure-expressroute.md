---
title: Microsoft Azure Well-Architected Framework review - Azure ExpressRoute
description: Provides architectural best practices for using Azure ExpressRoute.
author: duongau
ms.author: duau
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---

# Azure Well-Architected Framework review - Azure ExpressRoute

This article provides architectural best practice for Azure ExpressRoute. The guidance is based on the five pillars of the architecture excellence:

* [Reliability](#reliability)
* [Security](#security)
* [Cost optimization](#cost-optimization)
* [Operational excellence](#operational-excellence)
* [Performance efficiency](#performance-efficiency)

We assume that you have working knowledge of Azure ExpressRoute and are well versed with all of its features. For more information, see [Azure ExpressRoute](/azure/expressroute/expressroute-introduction).

## Prerequisites

For context, consider reviewing a reference architecture that reflects these considerations in its design. We recommend that you start with Cloud Adoption Framework Ready methodology's guidance [Connect to Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/connectivity-to-azure) and [Architect for hybrid connectivity](/azure/architecture/reference-architectures/hybrid-networking/expressroute) with Azure ExpressRoute. For low-code application architectures, we recommend reviewing [Enabling ExpressRoute for Power Platform](/power-platform/guidance/expressroute/overview) when planning and configuring ExpressRoute for use with Microsoft Power Platform.

## Reliability

In the cloud, we acknowledge that failures happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component. Use the following information to minimize down time to and from Azure when establishing connectivity using Azure ExpressRoute.

When discussing about reliability with Azure ExpressRoute it is important to taking into consideration bandwidth usage, physical layout of the network, and disaster recovery in case of failures. Azure ExpressRoute is capable of achieving these design consideration and have recommendations for each item in the checklist.

In the **design checklist** and **list of recommendations** below, information is presented in order for you to design a highly available network between your Azure environment and on-premises network.

### Design checklist

As you make design choices for Azure ExpressRoute, review the [design principles](/azure/architecture/framework/resiliency/principles) for adding reliability to the architecture.

> [!div class="checklist"]
> - Select either ExpressRoute circuit or ExpressRoute Direct for business requirements.
> -	Identify the bandwidth requirement, identify the circuit SKU type.
> -	Physical layer diversity to the service provider.
> -	Using geo-redundant ExpressRoute circuits.
> -	Using diverse service provider networks for different ExpressRoute circuit.
> - Active-Active ExpressRoute connections.
> - Using availability zone aware ExpressRoute Virtual Network Gateways.
> -	Terminating the different ExpressRoute circuit in different location on the customer network.
> -	Use ExpressRoute virtual network gateway in different regions.
> - Using site-to-site VPN as a backup for private peering.
> -	Monitoring ExpressRoute circuit health.
> - Monitoring ExpressRoute Virtual Network Gateway health.
> - Configure service health to receive maintenance notification.

### Recommendations

Explore the following table of recommendations to optimize your ExpressRoute configuration for Reliability.

| Recommendation | Benefit |
|--------|----|
| Plan for ExpressRoute circuit or ExpressRoute Direct | ExpressRoute circuit allows private dedicated connectivity into Azure with the help of a connectivity provider. ExpressRoute Direct allows customers to extend on-premises connectivity directly into the Microsoft network at a peering location. |
| Physical layer diversity | For better resiliency plan for diverse path between customer edge and the provider/Microsoft edge location.   |
| Plan for geo-redundant circuits| For planning for disaster recovery, set up ExpressRoute circuits in more than one peering locations. You can choose to create circuits in  peering locations in the same metro or different metro and choose to work with different service provider for diverse paths through each circuit. |
| Plan for Active-Active connection | Set up ExpressRoute circuits in an Active-Active mode for higher availability and configure BFD for faster failover in case of link failures on a connection. |
| Planning for Virtual Network Gateways | Create availability zone aware Virtual Network Gateway for higher resiliency and plan for Virtual Network Gateways in different region for disaster recovery and high availability. |
| Monitor circuits and gateway health | Set up monitoring to alert on circuits and gateway health based on various metrics available. |
| Enable service health | ExpressRoute uses service health to notify about planned and unplanned maintenances. Configuring service health will notify for any changes on the ExpressRoute circuit.  |

For more suggestions, see [Principles of the reliability pillar](/azure/architecture/framework/resiliency/principles).

## Security

<!-- 5B. ----------------------------------------------------

    Follow the Security H2 heading with a sentence about how the section contributes to the framework. 
-->

[H2 section introduction here.]

### Design checklist

<!--

    Required    

    A design checklist is always the first H3 in each pillar section.

    Make sure the considerations you document:
    - Are presented in a checklist format.
    - Map to the design principles. Identify the appropriate principle, and then think about your recommendation in relation to it.
    - Focus on the salient features for that pillar as they relate to the product.
    - Provide full coverage of the design principles. (If not all design principles exist, cover no fewer than three design principles.)
-->

As you make design choices for \<product>, review the \[design principles](\<design principles link>) for \<pillar>.

> [!div class="checklist"]
> - \<One or more design considerations for "Plan resources and how to harden them">
> - \<One or more design considerations for "Automate and use least privilege">
> - \<One or more design considerations for "Classify and encrypt data">
> - \<One or more design considerations for "Monitor system security, plan incident response">
> - \<One or more design considerations for "Identify and protect endpoints">
> - \<One or more design considerations for "Protect against code-level vulnerabilities">
> - \<One or more design considerations for "Model and test against potential threats">

### Recommendations

<!--

    Required    

    The Recommendations H3 is always the second section in each pillar. The content in this section should be formatted as a table.

-->

Explore the following table of recommendations to optimize your \<product> configuration for \<pillar>.

| Recommendation | Benefit |
|--------|----|
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |

For more suggestions, see [Principles of the security pillar](/azure/architecture/framework/security/security-principles).

Azure Advisor helps you ensure and improve \<pillar-specific text>. Review the [recommendations](../../contribute-how-to-write-waf-for-azure-offerings.md).

### Policy definitions

<!-- 

    Required

    A list of policy definitions is always the third section in each pillar.

    Provide a list of policies the customer can use for resource governance. After the list of policies, provide a list of all policy definitions available in Azure.
-->

- \<Policy>
- \<Policy>
- \<Policy>

All built-in policy definitions related to Azure Virtual Machines are listed in \[Built-in policies - \<category>]\(/azure/governance/policy/samples/built-in-policies#\<anchorlink>\).

## Cost optimization

<!-- 5C. ----------------------------------------------------

    Follow the Cost optimization H2 heading with a sentence about how the section contributes to the framework. 
-->

[H2 section introduction here.]

### Design checklist

<!--

    Required    

    A design checklist is always the first H3 in each pillar section.

    Make sure the considerations you document:
    - Are presented in a checklist format.
    - Map to the design principles. Identify the appropriate principle, and then think about your recommendation in relation to it.
    - Focus on the salient features for that pillar as they relate to the product.
    - Provide full coverage of the design principles. (If not all design principles exist, cover no fewer than three design principles.)
-->

As you make design choices for \<product>, review the \[design principles](\<design principles link>) for \<pillar>.

> [!div class="checklist"]
> - \<One or more design considerations for "Choose the correct resources">
> - \<One or more design considerations for "Set up budgets and maintain cost constraints">
> - \<One or more design considerations for "Dynamically allocate and de-allocate resources">
> - \<One or more design considerations for "Optimize workloads, aim for scalable costs">
> - \<One or more design considerations for "Continuously monitor and optimize cost management">

### Recommendations

<!--

    Required    

    The Recommendations H3 is always the second section in each pillar. The content in this section should be formatted as a table.

-->

Explore the following table of recommendations to optimize your \<product> configuration for \<pillar>.

| Recommendation | Benefit |
|--------|----|
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |

For more suggestions, see [Principles of the cost optimization pillar](/azure/architecture/framework/cost/overview).

Azure Advisor helps you ensure and improve \<pillar-specific text>. Review the [recommendations](../../contribute-how-to-write-waf-for-azure-offerings.md).

### Policy definitions

<!-- 

    Required

    A list of policy definitions is always the third section in each pillar.

    Provide a list of policies the customer can use for resource governance. After the list of policies, provide a list of all policy definitions available in Azure.
-->

- \<Policy>
- \<Policy>
- \<Policy>

All built-in policy definitions related to Azure Virtual Machines are listed in \[Built-in policies - \<category>]\(/azure/governance/policy/samples/built-in-policies#\<anchorlink>\).

## Operational excellence

<!-- 5D. ----------------------------------------------------

    Follow the Operational excellence H2 heading with a sentence about how the section contributes to the framework. 
-->

[H2 section introduction here.]

### Design checklist

<!--

    Required    

    A design checklist is always the first H3 in each pillar section.

    Make sure the considerations you document:
    - Are presented in a checklist format.
    - Map to the design principles. Identify the appropriate principle, and then think about your recommendation in relation to it.
    - Focus on the salient features for that pillar as they relate to the product.
    - Provide full coverage of the design principles. (If not all design principles exist, cover no fewer than three design principles.)
-->

As you make design choices for \<product>, review the \[design principles](\<design principles link>) for \<pillar>.

> [!div class="checklist"]
> - \<One or more design considerations for "Optimize build and release processes">
> - \<One or more design considerations for "Understand operational health">
> - \<One or more design considerations for "Rehearse recovery and practice failure">
> - \<One or more design considerations for "Embrace continuous operational improvement">
> - \<One or more design considerations for "Use loosely coupled architecture">

### Recommendations

<!--

    Required    

    The Recommendations H3 is always the second section in each pillar. The content in this section should be formatted as a table.

-->

Explore the following table of recommendations to optimize your \<product> configuration for \<pillar>.

| Recommendation | Benefit |
|--------|----|
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |

For more suggestions, see [Principles of the operational excellence pillar](/azure/architecture/framework/devops/principles).

Azure Advisor helps you ensure and improve \<pillar-specific text>. Review the [recommendations](../../contribute-how-to-write-waf-for-azure-offerings.md).

### Policy definitions

<!-- 

    Required

    A list of policy definitions is always the third section in each pillar.

    Provide a list of policies the customer can use for resource governance. After the list of policies, provide a list of all policy definitions available in Azure.
-->

- \<Policy>
- \<Policy>
- \<Policy>

All built-in policy definitions related to Azure Virtual Machines are listed in \[Built-in policies - \<category>]\(/azure/governance/policy/samples/built-in-policies#\<anchorlink>\).

## Performance efficiency

<!-- 5E. ----------------------------------------------------

    Follow the Performance efficiency H2 heading with a sentence about how the section contributes to the framework. 
-->

### Design checklist

<!--

    Required    

    A design checklist is always the first H3 in each pillar section.

    Make sure the considerations you document:
    - Are presented in a checklist format.
    - Map to the design principles. Identify the appropriate principle, and then think about your recommendation in relation to it.
    - Focus on the salient features for that pillar as they relate to the product.
    - Provide full coverage of the design principles. (If not all design principles exist, cover no fewer than three design principles.)
-->

As you make design choices for \<product>, review the \[design principles](\<design principles link>) for \<pillar>.

> [!div class="checklist"]
> - \<One or more design considerations for "Understand the challenges of distributed architectures">
> - \<One or more design considerations for "Run performance testing in the scope of development">
> - \<One or more design considerations for "Continuously monitor the application and the supporting infrastructure">
> - \<One or more design considerations for "Identify improvement opportunities with resolution planning">
> - \<One or more design considerations for "Invest in capacity planning">

### Recommendations

<!--

    Required    

    The Recommendations H3 is always the second section in each pillar. The content in this section should be formatted as a table.

-->

Explore the following table of recommendations to optimize your \<product> configuration for \<pillar>.

| Recommendation | Benefit |
|--------|----|
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |

For more suggestions, see [Principles of the performance efficiency pillar](/azure/architecture/framework/scalability/principles).

Azure Advisor helps you ensure and improve \<pillar-specific text>. Review the [recommendations](../../contribute-how-to-write-waf-for-azure-offerings.md).

## Azure Policy

Azure Policy doesn't provide any built-in policies for ExpressRoute, but custom policies can be created to help govern how ExpressRoute circuits should match your desired end state, such as SKU choice, peering type, peering configurations and so on.

## Azure Advisor recommendation

### Reliability

Azure Advisor provides many recommendations for ExpressRoute circuits as they relate to reliability. For example, Azure Advisor can detect:

* ExpressRoute gateways in which only a single ExpressRoute circuit is deployed, instead of multiple. Multiple ExpressRoute circuits are recommended for add resiliency for the peering location.
* ExpressRoute circuits that are not being observed by Connection Monitor, as end-to-end monitoring of your ExpressRoute circuit is critical for reliability insights.
* Network topologies involving multiple peering locations that would benefit from ExpressRoute Global Reach to improve disaster recovery designs for on-premises connectivity to account for unplanned connectivity loss.

### Security

There are no Azure Advisor related recommendations for this category.

### Cost Optimization

Azure Advisor can detect ExpressRoute circuits that have been deployed for a significant time but have a provider status of *Not Provisioned*. Circuits in this state are not operational; and removing the unused resource will reduce unnecessary costs.

### Operational Excellence

There are no Azure Advisor related recommendations for this category.

### Performance Efficiency

Azure Advisor will offer a recommendation to upgrade your ExpressRoute circuit bandwidth to accommodate usage when your circuit has recently been consuming over 90% of your procured bandwidth. If your traffic exceeds your allocated bandwidth, you’ll experience dropped packets which can lead to significant performance or reliability impact.

## Additional resources

### Cloud Adoption Framework guidance

* [Traditional Azure network topology](/azure/cloud-adoption-framework/ready/azure-best-practices/traditional-azure-networking-topology)
* [Virtual WAN network topology (Microsoft-managed)](/azure/cloud-adoption-framework/ready/azure-best-practices/traditional-azure-networking-topology)

## Next steps

<!-- 8. Next steps ------------------------------------------------------------

    Required

    Add a context sentence for the following links: 

    Best practices:
        - Provide at least one next step and no more than three.
        - Include some context, so the customer can determine why they would select the link.
        - Do not use a "More information" or "See also" section.
        - Do provide a link to relevant quickstarts in the product documentation.
        - Do provide a link to a Learn module that covers resource provisioning.

-->

<!-- Remove all the comments in this template before you sign off or merge to the main branch. -->