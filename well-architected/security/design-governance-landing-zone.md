---
title: Azure landing zone integration
description: Security controls on a prepared infrastructure into which a workload can get deployed.
author: PageWriter-MSFT
ms.author: martinek
ms.date: 02/28/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - azure-blueprints
  - azure-policy
categories:
  - management-and-governance
ms.custom:
  - article
---

# Azure landing zone integration

From a workload perspective, a *landing zone* refers to a prepared platform into which the application gets deployed. A landing zone implementation can have compute, data sources, access controls, and networking components already provisioned. With the required plumbing ready in place; the workload needs to plug into it. When considering the overall security, a landing zone offers centralized security capabilities that adds a threat mitigation layer for the workload. Implementations can vary but here are some common strategies that enhance the security posture.

- Isolation through segmentation. You can isolate assets at several layers from Azure enrollment down to a subscription that has the resources for the workload. This strategy of having resources within a boundary that is separate from other parts of the organization is an effective way of detecting and containing adversary movements.

- Consistent adoption of organizational policies. Policies govern which resources can be used and their usage limits. Policies also provide identity controls. Only authenticated and authorized entities are allowed access. This approach decouples the governance requirements from the workload requirements. It's crucial that a landing zone is handed over to the workload owner with the security guardrails deployed.

- Configurations that align with [principles of Zero Trust ](/security/zero-trust/#guiding-principles-of-zero-trust). For instance an implementation might have network connectivity to on-premises data centers. When designing networking controls, the landing zone may apply the least-privilege principle by opening communication paths only when necessary and only to trusted entities.

The preceding examples are conceptually simple but the implementation can get complicated for an enterprise-scale deployment. Azure landing zone as part of the Cloud Adoption Framework (CAF) provides architecture guidance about identity and access management, networking, and other design areas necessary to achieve an optimal implementation.  

**Learn more**

[What is an Azure landing zone?](/azure/cloud-adoption-framework/ready/landing-zone/)

[Landing zone implementation options](/azure/cloud-adoption-framework/ready/landing-zone/implementation-options)

## Increase automation with Azure Blueprints

Use Azure's native automation capabilities to increase consistency, compliance, and deployment speed for workloads. A recommended way to implement a landing zone is with Azure Blueprints and Azure Policies.

Automation of deployment and maintenance tasks reduces security and compliance risk by limiting opportunity to introduce human errors during manual tasks. This will also allow both IT Operations teams and security teams to shift their focus from repeated manual tasks to higher value tasks like enabling developers and business initiatives, protecting information, and so on.

Utilize the Azure Blueprint service to rapidly and consistently deploy application environments that are compliant with your organization's policies and external regulations. [Azure Blueprint Service](/azure/governance/blueprints/) automates deployment of environments including Azure roles, policies, resources, such as virtual machines, networking, storage, and more. Azure Blueprints builds on Microsoft's significant investment into the Azure Resource Manager to standardize resource deployment in Azure and enable resource deployment and governance based on a desired-state approach. You can use built in configurations in Azure Blueprint, make your own, or just use Resource Manager scripts for smaller scope.

Several Security and Compliance Blueprints [samples](/azure/governance/blueprints/samples/) are available to use as a starting template.

## Enforce policy compliance

Organizations of all sizes will have security compliance requirements. Industry, government, and internal corporate security policies all need to be audited and enforced. Policy monitoring is critical to check that initial configurations are correct and that it continues to be compliant over time.

In Azure, you can take advantage of Azure Policy to create and manage policies that enforce compliance. Like Azure Blueprints, Azure Policies are built on the underlying Azure Resource Manager capabilities in the Azure platform (and Azure Policy can also be assigned via Azure Blueprints).

For more information on how to do this in Azure, please review [Tutorial: Create and manage policies to enforce compliance](/azure/governance/policy/tutorials/create-and-manage).

**How do you consistently deploy landing zones that follow organizational policies?**
***

Key Azure services that can help in creating a landing zone:

- [Azure Blueprints](/azure/governance/blueprints/overview) sketches a solution's design parameters based on an organization's standards, patterns, and requirements.
- [Azure Resource Manager template specs](/azure/azure-resource-manager/templates/template-specs)  stores an Azure Resource Manager template (ARM template) in Azure for later deployment.
- [Azure Policy](/azure/governance/policy/overview) enforces organizational standards and to assess compliance at-scale.
- [Azure AD](https://azure.microsoft.com/services/active-directory/) and [Azure role-based access control (Azure RBAC)](/azure/role-based-access-control/overview) work in conjunction to provide identity and access controls.
- [Microsoft Defender for Cloud](/azure/security-center/security-center-intro)
- [Microsoft Defender for Cloud](/azure/security-center/azure-defender)

## Architecture

For information about an enterprise-scale reference architecture, see [Cloud Adoption Framework enterprise-scale landing zone architecture](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture). The architecture provides considerations in these critical design areas:

- [Enterprise Agreement (EA) enrollment and Azure Active Directory tenants](/azure/cloud-adoption-framework/ready/enterprise-scale/enterprise-enrollment-and-azure-ad-tenants)
- [Identity and access management](/azure/cloud-adoption-framework/ready/enterprise-scale/identity-and-access-management)
- [Management group and subscription organization](/azure/cloud-adoption-framework/ready/enterprise-scale/management-group-and-subscription-organization)
- [Network topology and connectivity](/azure/cloud-adoption-framework/ready/enterprise-scale/network-topology-and-connectivity)
- [Management and monitoring](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)
- [Business continuity and disaster recovery](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)
- [Security, governance, and compliance](/azure/cloud-adoption-framework/ready/enterprise-scale/security-governance-and-compliance)
- [Platform automation and DevOps](/azure/cloud-adoption-framework/ready/enterprise-scale/platform-automation-and-devops)


## Next

Use management groups to manage resources across multiple subscriptions consistently and efficiently.

> [!div class="nextstepaction"]
> [Management groups](design-management-groups.md)

> Back to the main article: [Governance](design-governance.md)
