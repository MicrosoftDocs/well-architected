---
title: Establish segmentation with management groups
description: Strategies using management groups to manage resources across multiple subscriptions consistently and efficiently.
author: PageWriter-MSFT
ms.author: martinek
ms.date: 12/20/2021
ms.topic: conceptual
categories:
  - management-and-governance
subject:
  - security
ms.custom:
  - article
---

# Establish segmentation with management groups

Management groups can manage resources across multiple subscriptions consistently and efficiently. However, due to its flexibility, your design can become complex and compromise security and operations.

> [!NOTE]
> Management groups could be defined as part of the workload architecture. More commonly, centralized teams use management groups to consistently apply governance and segmentation strategies across multiple workloads. If a centralized team is responsible for design and operations of your management groups, reference Cloud Adoption Framework's [Azure landing zones](/azure/cloud-adoption-framework/ready/landing-zone/). The [Resource organization](/azure/cloud-adoption-framework/ready/landing-zone/design-area/resource-org) design area outlines the best practice recommendations and considerations for segmentation through management groups, subscriptions, and application landing zones.

## Support your segmentation strategy with management groups

Structure management groups into a simple design that guides the enterprise segmentation model.

Management groups offer the ability to consistently and efficiently manage resources (including multiple subscriptions as needed). However, because of their flexibility, it's possible to create an overly complex design. Complexity creates confusion and negatively impacts both operations and security (as illustrated by overly complex Organizational Unit (OU) and Group Policy Object (GPO) designs for Active Directory).

Microsoft recommends aligning the top level of management groups (MGs) into a simple enterprise segmentation strategy and limiting the levels to no more than two.

In the example [Reference model](/azure/well-architected/security/design-segmentation#reference-model), there are enterprise-wide resources used by all segments, a set of core services that share services, and more segments for each workload.

- Root management group for enterprise-wide resources.

  Use the root management group to include identities that have the requirement to apply policies across every resource. For example, regulatory requirements, such as restrictions related to data sovereignty. This group is effective in by applying policies, permissions, tags, across all subscriptions.

  > [!CAUTION]
  > Be careful when using the root management group because the policies can affect all resources on Azure and potentially cause downtime or other negative impacts. For considerations, see [Use root management group with caution](#use-root-management-group-with-caution) later in this article.
  >
  > For complete guidance about using management groups for an enterprise, see [Management groups](/azure/cloud-adoption-framework/ready/landing-zone/design-area/resource-org-management-groups).

- Management group for each workload segment.

  Use a separate management group for teams with limited scope of responsibility. This group is typically required because of organizational boundaries or regulatory requirements.

- Root or segment management group for the core set of services.

## Use root management group with caution

Use the Root Management Group (MG) for enterprise consistency, but test changes carefully to minimize risk of operational disruption.

The root management group enables you to ensure consistency across the enterprise by applying policies, permissions, and tags across all subscriptions. Take care when you're planning and implementing assignments to the root management group. Assignments can affect every resource on Azure and potentially cause downtime or other negative impacts on productivity if there are errors or unanticipated effects.

- **Plan carefully:** Select enterprise-wide elements to the root management group that have a clear requirement to be applied across every resource or to be low impact.

  Select enterprise-wide identities that have a clear requirement to be applied across all resources. Good candidates include:

  - **Regulatory requirements** with clear business risk or impact. For example, restrictions related to data sovereignty.

  - **Near-zero potential negative impact.** For example, policy with audit effect, tag assignment, and Azure RBAC permissions assignments that have been carefully reviewed.

  Use a dedicated service principal name (SPN) to execute management group management operations, subscription management operations, and role assignment. SPN reduces the number of users who have elevated rights and follows least-privilege guidelines. Assign the **User Access Administrator** at the root management group scope (`/`) to grant the SPN access at the root level. After the SPN is granted permissions, the **User Access Administrator** role can be safely removed. In this way, only the SPN is part of the **User Access Administrator** role. Assign **Contributor** permission to the SPN, which allows tenant-level operations. This permission level ensures that you can use the SPN to deploy and manage resources to any subscription within your organization.

  Limit the number of Azure Policy assignments made at the root management group scope (`/`). This limitation minimizes debugging inherited policies in lower-level management groups.

  Don't create any subscriptions under the root management group. This hierarchy ensures that subscriptions don't only inherit the small set of Azure policies assigned at the root-level management group, as the small set of Azure policies don't represent a full set necessary for a workload.

- **Test first:** Plan, test, and validate all enterprise-wide changes on the root management group before applying (policy, tags, Azure RBAC model, and so on).

  - **Test lab:** The representative lab tenant or lab segment in the production tenant.

  - **Production pilot:** The production pilot can be a segment management group or designated subset in the subscription(s) management group.

- **Validate changes:** Validate changes to ensure they have the desired effect.

## Next steps

> [!div class="nextstepaction"]
> [Administrative account security](design-admins.md)
