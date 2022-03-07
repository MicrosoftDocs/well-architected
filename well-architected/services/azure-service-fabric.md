---
title: Azure Well-Architected Framework review - Azure Service Fabric
description: Describes the Azure Service Fabric offering in the context of the Azure Well-Architected Framework pillars.
author: v-stacywray 
ms.author: robbymillsap
ms.date: 03/07/2022
ms.topic: conceptual 
ms.service: architecture-center 
ms.subservice: well-architected 
---

<!-- Remove all the comments in this template before you sign off or merge to the main branch. -->

<!-- 1. Metadata ---------------------------------------------------------

    Required

    TO DO: Complete the metadata section above for your Microsoft Azure Well-Architected Framework 
    review article. Leave the ms.topic, ms.service, and ms.subservice values
    as they are listed in this template.
-->

<!-- 2. H1 ---------------------------------------------------------------

    Required

    Goal(s): Tell the customer they are in the right place. Set expectations
    for the content that follows. Set SEO.

    Best practices:
        - Start the H1 with "Azure Well-Architected Framework review - <product name>". 

    Example: "# Azure Well-Architected Framework review - Azure Application Gateway"

    TO DO: Add your H1.
-->

# [H1 headline here]

<!-- 3. Introductory paragraph -------------------------------------------

    Required

    Goal: Help customers quickly determine whether an article is relevant to them.

    Best practices:
        - Lead with a light intro that describes, in customer-friendly language,
          what the customer will learn, or do, or accomplish.
        - Answer the fundamental question, "Why would I want to do this?"
        - Keep it short.

    Example: "This article provides architectural best practices for the Azure Application
              Gateway v2 family of SKUs. The guidance is based on the five pillars of architecture
              excellence: Reliability, security, cost optimization, operational excellence, and performance efficiency."

    TO DO: Add your introductory paragraph.
-->

[Introduction here.]

<!-- 4. Prerequisites ----------------------------------------------------

    Required

    Best practices:
        - Make Prerequisites your first H2 in a WAF review.
        - Recommend a reference architecture that the reader can use to understand the considerations in this article. Choose an architecture that focuses on the service being described in this article.

    Here's an example:
     - Understanding the Well-Architected Framework pillars can help produce a high-quality, stable, and efficient cloud architecture. We recommend that you review your workload by using the [Azure Well-Architected Framework Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.

     - Use a reference architecture to review the considerations based on the guidance provided in this article. We recommend that you start with [Run a Linux VM on Azure](/azure/architecture/reference-architectures/n-tier/linux-vm).

<!-- 5. Pillar H2s ----------------------------------------------------

    Required

    The H2 headings in this template should be followed as-is. The order in which they are presented is a best practice. Each heading sets expectations for the content that follows. 

    TO DO: Add your H2 sections.
-->

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
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>

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
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>

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
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>

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
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>

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
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>

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

<!-- 7. Additional resources ----------------------------------------------------

    Optional

    The resources should be separated in sections. The H3 heading should indicate the purpose of the resource. 

-->

- \[\<link text>]\(\<link>)
- \[\<link-text>]\(\<link>)

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

- \[\<link text>]\(\<link>)

<!-- Remove all the comments in this template before you sign off or merge to the main branch. -->