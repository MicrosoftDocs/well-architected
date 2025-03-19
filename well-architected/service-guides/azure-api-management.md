---
title: Architecture Best Practices for Azure API Management
description: Article description.
author: your GitHub alias
ms.author: your Microsoft alias or a team alias
ms.topic: conceptual
ms.date: mm/dd/yyyy
ms.subservice: waf-service-guide
products:
 - item
 - item
azure.category:
 - item
 - item

---

<!--

This template provides the basic structure of a Well-Architected Framework (WAF)
service guide for an Azure offering article.

For more information, see the
[pattern instructions](waf-review-for-product-template-instructions.md).

Remove all comments in this template before you sign off
or merge to the main branch.

For metadata values to set, including products and Azure categories,
see [Taxonomies for Learn]
(https://review.learn.microsoft.com/help/platform/metadata-taxonomies?branch=main).

-->

# Architecture best practices for Azure API Management

<!-- Required: Introductory paragraph

Create a paragraph that contains:

- Links to a decision tree for the area of cloud infrastructure that
your Azure offering operates in.
- Links to an overview of the Well-Architected Framework pillars.

Example:

--> 

This article assumes that as an architect, you've reviewed the
[[cloud-infrastructure-area] decision tree]([decision-tree-URL]) and
chose [Azure offering] as the [cloud-infrastructure-area] for
your workload. The guidance in this article provides architectural
recommendations that are mapped to the principles of the
[Well-Architected Framework pillars](/azure/well-architected/pillars).

<!-- Required: Alert and bold text sections

Use the following required sections for each WAF pillar::

-  A standardized alert that contains a link to a reference architecture
that focuses on your Azure offering and illustrates the article's
considerations.

Example:

-->

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural
areas of concern along with design strategies localized to the technology
scope.
>
> Also included are recommendations for the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations that are available for [Azure offering] and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or to optimize your existing environments.
>
> Foundational architecture that demonstrates the key recommendations:
> [[Azure offering] baseline architecture]([baseline-architecture-URL]).

<!-- Required: Bold section 

- Add a section to list the technology scope. List products, services, and technology
areas that are related to your Azure offering. Use bold text as a heading,
not an H2, or H3.
- Include an image from an architecture that showcases the placement of the service in the context of a workload. 

Example:

-->

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:

- [interrelated-resource]
- [interrelated-resource]

<!-- :::image source="" alt-text="Diagram of an [insert service] architecture."::: -->

<!-- Required: Reliability H2

Include a standardized description of the pillar.
For each framework pillar, present a vision and recommendations for your offering's
architecture. Use the pillar H2 headings in the order that this template lists them in.

-->

## Reliability

The purpose of the Reliability pillar is to provide continued
functionality by **building enough resilience and the ability
to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles)
provide a high-level design strategy applied for individual components,
system flows, and the system as a whole.

<!-- Required: Design checklist H3

In the first H3 of the pillar section, lead readers through design principles by:

- Using standardized text that contains a link to the design review checklist for
the pillar.
- Presenting a checklist of the pillar's design review recommendations that are
relevant for your Azure offering.

For each applicable principle:

- Discuss considerations that relate to that checklist item.
- Provide links to conceptual articles in product documentation if needed.
- Focus on areas of architectural concern for the architect, not on
specific configuration settings.

Example:

'Start your design strategy based on the
[design review checklist for Reliability](../reliability/checklist.md). Determine
its relevance to your business requirements while keeping in mind the
[offering-specific-aspects]. Extend the strategy to include more approaches
as needed.'

-->

### Design checklist

[Add your content.]

> [!div class="checklist"]
>
> - [design-consideration]
> - [design-consideration]
>   ...

<!-- Required: Recommendations H3

In the second H3 of the pillar section, present a table of recommendations
for optimizing the configuration of your Azure offering. The recommendations
should relate to the pillar and show how to materialize the vision of the
preceding design checklist items.

-->

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| ... | ... |

<!-- Required: Security H2

Include a standardized description of the pillar.

-->

## Security

The purpose of the Security pillar is to provide **confidentiality,
integrity, and availability** guarantees to the workload.

The [Security design principles]
(/azure/well-architected/security/security-principles)
provide a high-level design strategy for achieving those goals by
applying approaches to the technical design of [Azure offering].

<!-- Required: Design checklist H3

In the first H3 of the pillar section, lead readers through
design principles by:

- Using standardized text that contains a link to the design
review checklist for the pillar.
- Presenting a checklist of the pillar's design review recommendations
that are relevant for your Azure offering.

For each applicable checklist item:

- Discuss considerations that relate to that checklist item.
- Provide links to conceptual articles in product documentation if needed.
- Focus on areas of architectural concern for the architect, not on
specific configuration settings.

Example:

'Start your design strategy based on the
[design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture.
Extend the strategy to include more approaches as needed.'

-->

### Design checklist

[Add your content.]

> [!div class="checklist"]
>
> - [design-consideration]
> - [design-consideration]
>   ...

<!-- Required: Recommendations H3

In the second H3 of the pillar section, present a table of recommendations
for optimizing the configuration of your Azure offering. The recommendations
should relate to the pillar and show how to materialize the vision of the
preceding design principles.

-->

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| ... | ... |

<!-- Required: Cost Optimization H2

Include a standardized description of the pillar.

-->

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing
investments in critical areas, and optimizing in others** to meet the
organization's budget while meeting business requirements.

The [Cost Optimization design principles
](/azure/well-architected/cost-optimization/principles)
provide a high-level design strategy for achieving those goals and making
tradeoffs as necessary in the technical design related to [Azure offering]
and its environment.

<!--  Required: Design checklist H3

In the first H3 of the pillar section, lead readers through
design principles by:

- Using standardized text that contains a link to the design
review checklist for the pillar.
- Presenting a checklist of the pillar's design review recommendations
that are relevant for your Azure offering.

For each applicable principle:

- Discuss considerations that relate to that checklist item.
- Provide links to conceptual articles in product documentation
if needed.
- Focus on areas of architectural concern for the architect, not
on specific configuration settings.

Example:

'Start your design strategy based on the
[design review checklist for Cost Optimization](../cost-optimization/checklist.md)
for investments. Fine-tune the design so that the workload is aligned with
the budget that's allocated for the workload. Your design should use the right
Azure capabilities, monitor investments, and find opportunities to optimize
over time.'

-->

### Design checklist

> [!div class="checklist"]
>
> - [design-consideration]
> - [design-consideration]
>   ...
> - [design-consideration]

<!-- Required: Recommendations H3

In the second H3 of the pillar section, present a table of recommendations
for optimizing the configuration of your Azure offering. The recommendations 
should relate to the pillar and show how to materialize the vision of the
preceding design principles.

-->

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| ... | ... |

<!-- Required: Operational Excellence H2 

Include the standard description for the pillar.

-->

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development
practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles)
provide a high-level design strategy for achieving those goals for the
operational requirements of the workload.

<!-- Required: Design checklist H3

In the first H3 of the pillar section, lead readers through
design principles by:

- Using standardized text that contains a link to the design
review checklist for the pillar.
- Presenting a checklist of the pillar's design review recommendations
that are relevant for your Azure offering.

For each applicable principle:

- Discuss considerations that relate to that checklist item.
- Provide links to conceptual articles in product documentation
if needed.
- Focus on areas of architectural concern for the architect, not
on specific configuration settings.

Example:

`Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md)
 for defining processes for observability, testing, and deployment related to [offering].`

-->

### Design checklist

> [!div class="checklist"]
>
> - [design-consideration]
> - [design-consideration]
>   ...
> - [design-consideration]

<!-- Required: Recommendations H3

In the second H3 of the pillar section, present a table of
recommendations for optimizing the configuration of your Azure
offering. The recommendations should relate to the pillar and
show how to materialize the vision of the preceding design principles.

-->

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| ... | ... |

<!-- Required: Performance Efficiency H2

Include a standardized description of the pillar.

-->

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when
there's an increase in load** by managing capacity. The strategy includes
scaling resources, identifying and optimizing potential bottlenecks, and
optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles)
provide a high-level design strategy for achieving those capacity goals
against the expected usage.

<!--  Required: Design checklist H3

In the first H3 of the pillar section, lead readers through
design principles by:

- Using standardized text that contains a link to the design
review checklist for the pillar.
- Presenting a checklist of the pillar's design review recommendations
that are relevant for your Azure offering.

Start your design strategy based on the
[design review checklist for Performance Efficiency](../performance-efficiency/checklist.md).
Define a baseline that's based on key performance indicators for [Azure offering].

For each applicable principle:

- Discuss considerations that relate to that checklist item.
- Provide links to conceptual articles in product documentation
if needed.
- Focus on areas of architectural concern for the architect, not
on specific configuration settings.

Example:

`Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md)
for defining a baseline based on key performance indicators for [offering].'

-->

### Design checklist

> [!div class="checklist"]
>
> - [design-consideration]
> - [design-consideration]
>   ...
> - [design-consideration]


<!-- Required: Recommendations H3

In the second H3 of the pillar section, present a table of
recommendations for optimizing the configuration of your Azure
offering. The recommendations should relate to the pillar and
show how to materialize the vision of the preceding design principles.

-->

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| ... | ... |

<!-- Required: Azure policies H2

In a list, summarize policies that customers can use for resource
governance. Describe policies in general terms instead of mentioning
them by name. After the summary list, include a statement that provides
a link to your offering's built-in policy definitions.

Example:

'Azure provides an extensive set of built-in policies related to
<Azure-offering> and its dependencies. Some of the preceding
recommendations can be audited through Azure Policy. For example,
you can check whether:

For comprehensive governance, review the
[Azure Policy built-in definitions for 
[Azure offering]](/azure/governance/policy/samples/built-in-policies#[Azure-offering-anchor])
and other policies that might affect the security of the [cloud-infrastructure-area].'

-->

## Azure policies

[Add your content.]

- [policy-description]
- [policy-description]
  ...
- [policy-description]

<!-- Required: Azure Advisor recommendations H2

Provide links to Azure Advisor guidance that helps improve the
configuration of your Azure offering.

Example:

'Azure Advisor is a personalized cloud consultant that helps you follow
best practices to optimize your Azure deployments. 

For more information, see [Azure Advisor](/azure/advisor).

-->

## Azure Advisor recommendations

[Add your content.]

<!-- Optional: Related resources H2

List code artifacts or any other resources that might be beneficial
to the reader, such as custom queries and tools. Separate the resources
into H3 sections with headings that indicate the purpose of the resources. 

-->

## Related resources

[Add your content.]

<!-- Required: Next step or Related content H2

Consider adding one of these H2 sections (not both):

A "Next step" section that uses 1 link in a blue box 
to point to a next, consecutive article in a sequence.

-or- 

A "Related content" section that lists links to 
1 to 3 articles the user might find helpful.

-->

## Next step -or- Related content

> [!div class="nextstepaction"]
> [Next sequential article title](link.md)

-or-

- [Related article title](link.md)
- [Related article title](link.md)
- [Related article title](link.md)

<!--

Remove all the comments in this template before you
sign off or merge to the main branch.

-->