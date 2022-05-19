---
title: Microsoft Azure Well-Architected Framework review - ExpressRoute
description: Provides architectural best practices for using ExpressRoute.
author: duongau
ms.author: duau
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---

# Azure Well-Architected Framework review - ExpressRoute

This article provides architectural best practice for Azure ExpressRoute. The guidance is based on the five pillars of the architecture excellence:

* Reliability
* Security
* Cost optimization
* Operational excellence
* Performance efficiency

We assume that you have working knowledge of Azure ExpressRoute and are well verse with all of its features. For more information, see [Azure ExpressRoute](/azure/expressroute/expressroute-introduction).

## Prerequisites

For context, consider reviewing a reference architecture that reflects these considerations in its design. We recommend that you start with Cloud Adoption Framework Ready methodology's guidance [Connect to Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/connectivity-to-azure) and [Architect for hybrid connectivity](/azure/architecture/reference-architectures/hybrid-networking/expressroute) with Azure ExpressRoute. For low-code application architectures, we recommend reviewing [Enabling ExpressRoute for Power Platform](/power-platform/guidance/expressroute/overview) when planning and configuring ExpressRoute for use with Microsoft Power Platform.

## Reliability

<!-- 5A. ----------------------------------------------------

    Required

    Follow the Reliability H2 heading with a sentence about how the section contributes to the framework. 

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
> - \<One or more design considerations for "Design for business requirements">
> - \<Design or more design considerations for "Design for failure">
> - \<Design or more design considerations for "Observe application health">
> - \<Design or more design considerations for "Drive automation">

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

For more suggestions, see [Principles of the reliability pillar](/azure/architecture/framework/resiliency/principles).

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

## Azure Advisor recommendations

<!-- 6. Azure Advisor recommendations -----------------------------------------

    Required    

    Follow the Azure Advisor recommendations H2 heading with a sentence about how recommendations might help improve the configuration of the resource. 

-->

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