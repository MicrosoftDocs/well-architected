---
title: Governance, risk, and compliance
description: Security priorities around governance, risk, and compliance.
author: PageWriter-MSFT
ms.author: martinek
ms.date: 12/20/2021
ms.topic: conceptual
products:
  - azure-security-center
categories:
  - management-and-governance
  - security
ms.custom:
  - article
---

<!-- cSpell:ignore NIST -->

# Governance, risk, and compliance

As part of overall design, prioritize where to invest the available resources: finances, people, and time. Constraints on resources also affect the security implementation across the organization. To achieve an appropriate return on investment (ROI) on security, the organization needs to first understand and define its security priorities.

- **Governance:** How does the organization monitor, audit, and report on its security? The design and implementation of security controls within an organization are only the beginning of the story. How does the organization know that things are actually working? Are they improving? Are there new requirements? Is there mandatory reporting? Similar to compliance, there might be external industry, government, or regulatory standards that need to be considered.

- **Risk:** What risk types does the organization face while trying to protect identifiable information, intellectual property (IP), and financial information? Who might be interested or could use this information if it's stolen, such as external and internal threats and unintentional or malicious threats? A commonly forgotten but important consideration within risk is addressing disaster recovery and business continuity.

- **Compliance:** Is there a specific industry, government, or regulatory requirements that dictate or provide recommendations on criteria that your organization's security controls must meet? Examples of such standards, organizations, controls, and legislation include [ISO27001]( https://www.iso.org/isoiec-27001-information-security.html), [NIST]( https://www.nist.gov), and [PCI-DSS]( https://www.pcicomplianceguide.org/faq).

The collective role of organization(s) is to manage the security standards of the organization through their lifecycle:

- **Define:** Set organizational policies for operations, technologies, and configurations based on internal factors (business requirements, risks, and asset evaluation) and external factors (benchmarks, regulatory standards, and threat environment).

- **Improve:** Continually push security standards incrementally towards the ideal state to ensure continual risk reduction.

- **Sustain:** Ensure the security posture doesn't degrade naturally over time by instituting auditing and monitoring compliance with organizational standards.

**Take action**

[Establish security best practices across all workloads](/azure/cloud-adoption-framework/secure)

[Establish governance best practices to detect, enforce, and automate governance decisions](/azure/cloud-adoption-framework/govern)


## Prioritize security best practices investments

Security best practices are ideally applied proactively and completely to all systems as you build your cloud program, but this ideal isn't the reality for most enterprise organizations. Business goals, project constraints, and other factors often cause organizations to balance security risk against other risks and apply a subset of best practices at any given point.

We recommend applying as many of the best practices as early as possible, and then working to retrofit any gaps over time as you mature your security program. Your security program should include review, prioritization, and proactive application of best practices to cloud resources. We recommend evaluating the following considerations when  prioritizing which to follow first:

- **High business impact and highly exposed systems:** These include systems with direct intrinsic value and the systems that provide attackers a path to them. For more information, see [Identify and classify business critical applications](./design-apps-services.md#identify-and-classify-business-critical-applications).

- **Easiest to implement mitigations:** Identify quick wins by prioritizing the best practices, which your organization can execute quickly because you already have the required skills, tools, and knowledge to do it. For example, implementing a web app firewall (WAF) to protect a legacy application. Be careful not to exclusively use (or overuse) this short-term prioritization method. Doing so can increase your risk by preventing your program from growing and leaving critical risks exposed for extended periods.

Use the provided lists of prioritized security initiatives to help organizations start with these decisions. The provided lists are based on Microsoft's experience with threats and mitigation initiatives in internal environments and across Microsoft's customers. See [Module 4a](/office365/securitycompliance/ciso-workshop-module-4a) of the [Microsoft CISO Workshop](/security/ciso-workshop/ciso-workshop).

**Take action**

[Security in the Microsoft Cloud Adoption Framework for Azure](/azure/cloud-adoption-framework/secure)

[What is an Azure landing zone?](/azure/cloud-adoption-framework/ready/landing-zone/)

## Checklist
**What considerations for compliance and governance did you make?**

> [!div class="checklist"]
> - Create a landing zone for the workload. The infrastructure must have appropriate controls and be repeatable with every deployment.
> - Enforce creation and deletion of services and their configuration through Azure Policy.
> - Ensure consistency across the enterprise by applying policies, permissions, and tags across all subscriptions through careful implementation of root management group.
> - Understand regulatory requirements and operational data that might be used for audits.
> - Continuously monitor and assess the compliance of your workload. Perform regular attestations to avoid fines.
> - Review and apply recommendations from Azure.
> - Remediate basic vulnerabilities to keep the attacker costs high.

## In this section

Follow these questions to assess the workload at a deeper level.

|Assessment|Description|
|---|---|
|[**Are there any regulatory requirements for this workload?**](design-regulatory-compliance.md)|Understand all regulatory requirements. Check the Microsoft Trust Center for the latest information, news, and best practices in security, privacy, and compliance.|
|[**Is the organization using a landing zone for this workload?**](design-governance-landing-zone.md)|Consider the security controls placed on the infrastructure into which the workload is deployed.|
|[**Do you have a segmentation strategy?**](design-segmentation.md)|Reference model and strategies of how the functions and teams can be segmented.|
|[**Are you using management groups as part of your segmentation strategy?**](design-management-groups.md)|Strategies using management groups to manage resources across multiple subscriptions consistently and efficiently.|
|[**What security controls do you have in place for access to Azure infrastructure?**](design-admins.md)|Guidance on reducing risk exposure in scope and time when configuring critical impact accounts such as Administrators. |

## Azure security benchmark

The Azure Security Benchmark includes a collection of high-impact security recommendations you can use to help secure the services you use in Azure:

> ![Security Benchmark](../_images/benchmark-security.svg) The questions in this section are aligned to these controls:
>
> - [Governance and strategy](/azure/security/benchmarks/security-controls-v2-governance-strategy)
> - [Posture and vulnerability management](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

## Reference architecture

Here are some reference architectures related to governance:

[Cloud Adoption Framework enterprise-scale landing zone architecture](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

## Next steps

Provide security assurance through identity management to authenticate and grant permission to users, partners, customers, applications, services, and other entities.

> [!div class="nextstepaction"]
> [Identity and access management](./design-identity.md)

## Related links

> Go back to the main article: [Security](overview.md)
