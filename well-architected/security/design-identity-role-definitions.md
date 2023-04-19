---
title: Roles, responsibilities, and permissions
description: Learn to define clear lines of responsibility and establish separation of duties as part of Azure identity and access management.
author: PageWriter-MSFT
ms.author: martinek
ms.date: 12/20/2021
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - azure-security-center
categories:
  - management-and-governance
  - security
ms.custom:
  - article
---

# Roles, responsibilities, and permissions

In an organization, several teams work together to make sure that the workload and the supporting infrastructure are secure. To avoid confusion that can create security risks, define clear lines of responsibility and separation of duties.

Based on Microsoft's experience with many cloud adoption projects, establishing clearly defined roles and responsibilities for specific functions in Azure avoids confusion that can lead to human and automation errors creating security risk. 

## Clear lines of responsibility

**Do the teams have a clear view on responsibilities and individual/group access levels?**
***

Designate the parties responsible for specific functions in Azure.

Clearly documenting and sharing the contacts responsible for each of these functions creates consistency and facilitates communication. Based on Microsoft's experience with many cloud adoption projects, consistency and communication prevent confusion that can lead to human and automation errors that create security risks.

Designate groups (or individual roles) that are responsible for key functions.

> [!NOTE]
> A centralized team might be responsible for establishing these roles across your organization to provide consistent support across all workload teams. If your team is not solely responsible for each of the following roles, consult the guidance on [Aligning responsibilities across teams](/azure/cloud-adoption-framework/organize/raci-alignment) with a focus on understanding how your workload team will interface with a [Cloud platform team](/azure/cloud-adoption-framework/organize/cloud-platform), [Central IT](/azure/cloud-adoption-framework/organize/central-it), or a [Cloud center of excellence](/azure/cloud-adoption-framework/organize/cloud-center-of-excellence).

|Group or individual role| Responsibility|
|---|---|
| **Network Security**                 | *Typically an existing network security team.* Configuration and maintenance of Azure Firewall, Network Virtual Appliances (and associated routing), Web Application Firewall (WAF), network security groups, application security groups (ASG), and other cross-network traffic. |
| **Network Management**               | *Typically an existing network operations team.* Enterprise-wide virtual network and subnet allocation. |
| **Server Endpoint Security**         | *Typically IT operations, security, or jointly.* Monitor and remediate server security (patching, configuration, endpoint security). |
| **Incident Monitoring and Response** | *Typically a security operations team.* Incident monitoring and response to investigate and remediate security incidents in security information and event management (SIEM) or source console such as Microsoft Defender for Cloud Azure AD Identity Protection.|
| **Policy Management**                | *Typically a GRC team and an architecture team.* Apply governance based on risk analysis and compliance requirements. Set direction for use of Azure role-based access control (Azure RBAC), Microsoft Defender for Cloud, administrator protection strategy, and Azure Policy to govern Azure resources. |
| **Identity Security and Standards**  | *Typically a security team and an identity team jointly.* Set direction for Azure AD directories, PIM/PAM usage, multifactor authentication, password and synchronization configuration, and application identity standards. |

> [!NOTE]
> Application roles and responsibilities should cover different access level of each operational function. For example, publish production release, access customer data, manipulate database records, and so on. Application teams should include central functions listed in the preceding table.

## Assign permissions

Grant roles the appropriate permissions that start with least privilege and add more based on your operational needs. Provide clear guidance to your technical teams that implement permissions. This clarity makes it easier to detect and correct that reduces human errors such as overpermissioning.

> [!NOTE]
> Many organizations manage identity, access, and permissions from a centralized cloud platform team using [Cloud Adoption Framework Azure landing zones](/azure/cloud-adoption-framework/ready/landing-zone/) as a guide for configuration and permissions across multiple workloads. If identity and access are managed outside of the workload team, see the [Identity and access management design area](/azure/cloud-adoption-framework/ready/landing-zone/design-area/identity-access) to understand how to apply the proper level of permissions in your application's landing zone.

- Assign permissions at the management group level for the segment instead of individual subscriptions. Assigning permissions drives consistency and ensures application to future subscriptions. In general, avoid granular and custom permissions.

- Consider the built-in roles in Azure before creating custom roles to grant the appropriate permissions to VMs and other objects.

- **Security managers** group membership might be appropriate for smaller teams or organizations where security teams have extensive operational responsibilities.

When assigning permissions for a segment, consider consistency while allowing flexibility to accommodate several organizational models. These models can range from a single centralized IT group to mostly independent IT and DevOps teams.

### Reference model example

This section uses a [reference model](design-segmentation.md#reference-model) to demonstrate the considerations for assigning permissions for different segments. If your workload architecture requires segmentation and shared services spanning multiple segments for the same workload, Microsoft recommends starting from these models and adapting to your organization.

> [!WARNING]
> Shared services are seldom deployed or managed as part of a single workload or by the workload team. When centralized teams provide shared service, the best practice is to begin with [Azure landing zones](/azure/cloud-adoption-framework/ready/landing-zone/) in the Cloud Adoption Framework. Azure Landing Zones provide a conceptual architecture, reference implementations, and proven design processes to customize and implement the platform (or shared) services needed to support multiple applications. Those best practices aid in making platform wide decisions regarding [network topology and connectivity](/azure/cloud-adoption-framework/ready/landing-zone/design-area/network-topology-and-connectivity), [segmentation](/azure/cloud-adoption-framework/ready/azure-best-practices/plan-for-landing-zone-network-segmentation) and [governance](/azure/cloud-adoption-framework/ready/landing-zone/design-area/governance), which should be used when these decisions impact more than one workload.

#### Core services reference permissions

This segment hosts shared services utilized across the organization. These shared services typically include Active Directory Domain Services, DNS/DHCP, System Management Tools hosted on Azure Infrastructure as a Service (IaaS) virtual machines.

![Conceptual art showing reference permissions](images/ref-perms.png)

**Security visibility across all resources:** For security teams, grant read-only access to security attributes for all technical environments. This access level is needed to assess risk factors, identify potential mitigations, and advise organizational stakeholders who accept the risk. For more information, see
[Security team visibility](#security-team-visibility).

**Policy management across some or all resources:** To monitor and enforce compliance with external (or internal) regulations, standards, and security policy, assign appropriate permission to those roles. The roles and permissions you choose depend on the organizational culture and expectations of the policy program. See [Microsoft Cloud Adoption Framework for Azure](/azure/cloud-adoption-framework/govern/security-baseline).

Before defining the policies, consider:

- How is the organization's security audited and reported? Is there mandatory reporting?
- Are the existing security practices working?
- Are there any requirements specific to industry, government, or regulatory requirements?

Designate group(s) (or individual roles) for central functions that affect shared services and applications.

After the policies are set, continuously improve those standards incrementally. Make sure that the security posture doesn't degrade over time by having auditing and monitoring compliance. For information about managing security standards of an organization, see [governance, risk, and compliance (GRC)](/azure/cloud-adoption-framework/migrate/azure-best-practices/governance-or-compliance).

**Central IT operations across all resources:** Grant permissions to the central IT department (often the infrastructure team) to create, modify, and delete resources like virtual machines and storage. **Contributor** or **owner** roles are appropriate for this function.

**Central networking group across network resources:** To ensure consistency and avoid technical conflicts, assign network resource responsibilities to a single central networking organization. These resources should include virtual networks, subnets, Network Security Groups (NSG), and the virtual machines hosting virtual network appliances. Assign network resource responsibilities to a single central networking organization. The **network contributor** role is appropriate for this group. For more information, see [Centralize network management and security](/azure/architecture/framework/security/design-network-segmentation#centralize-network-management-and-security).

**Resource role permissions:** For most core services, administrative privileges required to manage them are granted through the application (Active Directory, DNS/DHCP, System Management Tools), so no other Azure resource permissions are required. If your organizational model requires these teams to manage their own VMs, storage, or other Azure resources, you can assign these permissions to those roles.

Workload segments with autonomous DevOps teams manage the resources associated with each application. The actual roles and permissions depend on the application size and complexity, the application team's size and complexity, and the organization and application team's culture.

**Service admin (break glass account):** Use the **service administrator** role only for emergencies and initial setup. Don't use this role for daily tasks. For more information, see [Emergency access ('break glass' accounts)](/azure/architecture/framework/security/design-admins#emergency-access-or-break-glass-accounts).

#### Segment reference permissions

This segment permission design provides consistency while allowing enough flexibility to accommodate the range of organizational models. The organizational models span from a single centralized IT group to mostly independent IT and DevOps teams.

![Diagram showing segment Permissions.](images/ref-segment.png)

**Security visibility across all resources:** For security teams, grant read-only access to security attributes for all technical environments. This access level is needed to assess risk factors, identify potential mitigations, and advise organizational stakeholders who accept the risk. See
[Security Team Visibility](#security-team-visibility).

**Policy management across some or all resources:** To monitor and enforce compliance with external (or internal) regulations, standards, and security policy assign appropriate permission to those roles. The roles and permissions you choose depend on the organizational culture and expectations of the policy program. See [Security Baseline discipline overview](/azure/cloud-adoption-framework/govern/security-baseline).

**IT Operations across all resources:** Grant permission to create, modify, and delete resources. The purpose of the segment (and resulting permissions) depends on your organization structure.

- Segments with resources managed by a centralized IT organization can grant the central IT department (often the infrastructure team) permission to modify these resources.

- Segments managed by independent business units or functions (such as a Human Resources IT Team) can grant those teams permission to all resources in the segment.

- Segments with autonomous DevOps teams don't need to grant permissions across all resources because the resource role grants permissions to application teams. For emergencies, use the service admin account (break-glass account).

**Central networking group across network resources:** To ensure consistency and avoid technical conflicts, assign network resource responsibilities to a single central networking organization. These resources should include virtual networks, subnets, Network Security Groups (NSG), and the virtual machines hosting virtual network appliances. See [Centralize Network Management And Security](/azure/architecture/framework/security/design-network-segmentation#centralize-network-management-and-security).

**Resource role permissions:** Segments with autonomous DevOps teams manage the resources associated with each application. The actual roles and permissions depend on the application size and complexity, the application team's size and complexity, and the organization and application team's culture.

**Service admin (break glass account):** Use the service admin role only for emergencies (and initial setup if necessary). Don't use this role for daily tasks. For more information, see [Emergency access or 'Break Glass' accounts)](/azure/architecture/framework/security/design-admins#emergency-access-or-break-glass-accounts).

## Security team visibility

An application team needs to be aware of security initiatives to align their security improvement plans with the outcome of those activities. Provide security teams read-only access to the security aspects of all technical resources in their purview.

Security organizations need visibility into the technical environment to perform their duties of assessing and reporting on organizational risk. Without this visibility, security has to rely on information provided from groups operating in the environment, which have potential conflict of interest (and other priorities).

Security teams might separately be granted other privileges if they have operational responsibilities or a requirement to enforce compliance on Azure resources.

For example in Azure, assign security teams to the **security readers** permission that provides access to measure security risk (without providing access to the data itself).

For enterprise security groups with broad responsibility for security of Azure, you can assign this permission using:

- *Root management group* – for teams responsible for assessing and reporting risk on all resources

- *Segment management group(s)* – for teams with limited scope of responsibility (typically required because of organizational boundaries or regulatory requirements)

> [!IMPORTANT]
> Because security will have broad access to the environment (and visibility into potentially exploitable vulnerabilities), treat security teams as critical impact accounts and apply the same protections as administrators. The [Administrative account security](/azure/architecture/framework/security/design-admins) section details these controls for Azure.

**Suggested actions**

- Define a process for aligning communication, investigation, and hunting activities with the application team.
- Following the principle of least privilege, establish access control to all cloud environment resources for security teams with sufficient access. Security teams gain the required visibility into the technical environment and to perform their duties of assessing, and reporting on organizational risk.

**Learn more**

[Engage your organization's security team](/azure/security/develop/secure-dev-overview#engage-your-organizations-security-team)

## Manage connected tenants

Does your security team have visibility into all existing subscriptions and cloud environments? How do they discover new ones?

Ensure your security organization is aware of all enrollments and associated subscriptions connected to your existing environment (via ExpressRoute or a site-to-site VPN) and monitoring as part of the overall enterprise.

These Azure resources are part of your enterprise environment and security organizations require visibility into them. Security organizations need this access to assess risk and to identify whether organizational policies and applicable regulatory requirements are being followed.

The organizations' cloud infrastructure should be well documented, with security team access to all resources required for monitoring and insight. Frequent scans of the cloud-connected assets should be performed to ensure no other subscriptions or tenants have been added outside of organizational controls. Regularly review Microsoft guidance to ensure security team access best practices are consulted and followed.

### Suggested actions

Ensure all Azure environments that connect to your production environment and network apply your organization's policy, and IT governance controls for security.

You can discover existing connected tenants using a
[tool](/azure/role-based-access-control/elevate-access-global-admin?toc=%252fazure%252factive-directory%252fprivileged-identity-management%252ftoc.json) provided by Microsoft for guidance on permissions.

## Next steps

Restrict access to Azure resources based on a need-to-know basis starting with the principle of least privilege security and add more based on your operational needs.

> [!div class="nextstepaction"]
> [Azure control plane security](design-identity-control-plane.md)

## Related links

For considerations about using management groups to reflect the organization's structure within an Azure Active Directory (Azure AD) tenant, see [Management groups](/azure/cloud-adoption-framework/ready/landing-zone/design-area/resource-org-management-groups).

> Back to the main article: [Azure identity and access management considerations](design-identity.md)
