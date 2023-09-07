---
title: Recommendations for building a segmentation strategy
description: 
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for building a segmentation strategy

**Applies to: SE 04**

This guide describes recommendations for building a unified segmentation strategy with the goal of using isolation boundaries in workload design, its presence in the organization, and workload team structure.

**Definitions** 

|Term   |Definition   |
|---------|---------|
|Segment      |   A logical unit that's isolated from other entities and protected by a set of security measures.      |
|Perimeter     |  Trust boundary around a segment.       |
|Containment     |  A technique to contain the blast radius if an attacker gains access to a segment.       |
|Role     |  A set of permissions needed to complete a job function.       |
|Least-privilege access     | Zero-Trust principle that aims at minimizing a set of permissions to complete a job function.        |
|Resource organization     | A strategy to group related resources by flows within a segment.        |

## Key design strategies

A segment is a logical section of your solution that needs to be secured as one unit. A segmentation strategy defines how this unit should be intentionally separated from other units, having its own set of security requirements with measures to follow.

Commonly, the concept of segmentation is used for networks. However, the same underlying principle should be used throughout a solution, including segmenting resources for management purposes and for access control.

Segmentation helps you to design a security approach based on the principles of the Zero-Trust model, including applying defense in depth. By segmenting your workloads, you ensure that an attacker who breaches one network segment won\'t gain access to another. Similarly, if an attacker breaches a segment that\'s protected by one set of identity controls, they won\'t be able to gain access to another segment because it\'s got a separate set of identity controls. In a secure system, identity and network attributes must block unauthorized access and hide the assets from being exposed.

Here are some example representations of segments:

-   Subscriptions that isolate workloads of an organization.

-   Resource groups that isolate workload assets.

-   Deployment environments that isolate deployment by stages of development.

-   Teams and roles that isolate job functions related to workload development and management.

-   Application tiers that isolate by workload utility. Further, microservices isolate one service from another.

These are the key elements of segmentation that should be considered to make sure you are building a comprehensive defense-in-depth strategy:

-   **Boundary or perimeter**. The entry edge of a segment where you apply security controls. The controls should block access to the segment unless explicitly allowed. The goal is to prevent an adversary from breaking through the perimeter and gaining control of the system. For example, an application tier might accept an end user's access token when it processes a request. The data tier might require a different access token that's got a specific permission, which only the application tier can request.

-   **Containment**. The exit edge of a segment that prevents lateral movement of adversaries in the system. The goal is to minimize the blast radius. For example, when you configure an Azure virtual network you might configure routing and network security groups to only allow traffic patterns that you expect, and avoid traffic going to arbitrary network segments.

-   **Isolation**. Entities that need to/can be secured by similar assurances are grouped together and protected by a boundary. The goal is ease of management. Also, the containment of attack vectors within an environment is critical. For example, you might group all of the resources that relate to a specific workload into one Azure subscription, and then apply access control so that only the workload teams can access the subscription.

It is important to note the distinction between perimeters and isolation. Perimeter refers to the points of location that should be checked. Isolation is about grouping. These concepts must be used together so that you can contain the impact aggressively.

Isolation, however, does not mean creating silos in the organization. Instead, a unified segmentation strategy aims to bring alignment between the technical teams and set clear lines of responsibilities. This clarity reduces the risk of human errors and automation failures that can lead to security vulnerabilities, operational downtime, or both. Suppose a security breach is detected in a component of a complex enterprise system. It\'s important that everybody understands who is responsible for that resource, and that the correct person is included in the triage team. By creating and documenting a good segmentation strategy, the organization can quickly identify how to respond to different kinds of incidents and the stakeholders to be included.

|![Tradeoff icon](../_images/trade-off.svg) Segmentation introduces complexity because there is overhead in management. There is also a tradeoff in cost. For example, segmenting deployment environments that run side by side will cause more resources to be provisioned.|
|--| 

|[Risk icon](../_images/risk.svg) Micro-segmentation beyond a reasonable limit loses the benefit of isolation. When you create too many segments, it becomes difficult to identify points of communication or to allow all of the valid communication paths within the segment.|
|--| 

### Identity as the perimeter 

Workload segments will be accessed by various identities whether that's people, software components, or devices. Identity is a non-negotiable perimeter that should be considered as the primary line of defense that authenticates and authorizes access across isolation boundaries, regardless of where the access request originates.

-   **Assign access by role**

    Identities must only have access to segments needed to do the job. Understanding roles and responsibilities of requesting identity is key. This minimizes anonymous access because you know the entity that is requesting access to a segment and for what purpose.
    
    An identity might have different access scopes in different segments. Consider a typical environment setup, where there are separate segments for each stage. Identities associated with the developer role have read-write access to the development environment. As the deployment moves to staging, those permissions are curbed. By the time the workload is promoted to production, scope for developers is reduced to read-only access.
    
-   **Consider application and management identities separately**

    In most solutions, users have a different level of access than do developers or operators. In some applications, you might use different identity systems or directories for each type of identity. Consider access scopes and roles separately for each.
    
-   **Assign least-privilege access**

    If the identity is allowed access, determine the level of access. Start with the least privilege per segment and broaden that scope only if/when needed.
    
    By applying the least privilege, you limit the impact if the identity is ever compromised. If access is limited by time, the attack surface will be reduced further. This is especially applicable to critical impact accounts, such as Administrators, who should only have limited time access. Or, a software component whose identity is compromised.
    
|![Tradeoff icon](../_images/trade-off.svg) The performance of the workload can be impacted. Verifying each request explicitly requires extra compute cycles and extra network IO. <br> There is also a management overhead with role-based access. Keeping track of identities and their access scopes can become complex in role assignments. The workaround is to assign roles to security groups instead of individual identity.|
|--| 



|[Risk icon](../_images/risk.svg) Identity settings can be complex. Misconfigurations can impact reliability of the workload. Suppose there's a misconfigured role assignment that's denied access to database. The requests will start failing, eventually causing reliability issues that cannot otherwise be detected until runtime.|
|--| 

For information about identity controls, see [Identity and access management](identity-access-management.md).

In contrast to network access controls, identity validates access control at access time. Conducting regular access reviews is highly recommended. Especially require an approval workflow to obtain privileges for critical impact accounts.

For example, patterns related to identity segmentation, see [Identity segmentation patterns](#identity-segmentation-patterns).

### Networking as a perimeter

Identity perimeters are network agnostic. Network perimeters augment identity but never replaces it. Network perimeters are established to control blast radius, block unexpected, prohibited, and unsafe access, and obfuscate workload resources.

While the primary focus of the identity perimeter is least privilege, assume breach should be the mindset when you're designing the network perimeter.

You can create software-defined perimeters in your networking footprint by using the various Azure services and features. When a workload (or parts of a given workload) is placed into separate segments, you can control traffic from/to those segments to secure communication paths. If a segment is compromised, you will be able to better contain the impact and prevent it from laterally spreading through the rest of your network.

Think like an attacker to achieve a foothold at various points within the workload and establish controls to mitigate further expansion. The controls should detect, contain, and stop attackers from gaining access to an entire workload.

-   Define your edge perimeter between public networks and the network in which your workload is placed. Restrict line of sight from public networks to your network as much as possible.

-   Implement demilitarized zones (DMZs) in front of the application with proper controls through firewalls.

-   Create micro-segmentation within your private network by grouping parts of the workload into separate segments. Secure communication paths between them.

-   Creating boundaries based on the intent. For example, segment workload functional network from operational networks.

For common patterns related to networking segmentation, see [this section](#networking-segmentation-patterns).

|![Tradeoff icon](../_images/trade-off.svg) Network security controls are expensive as often they are included as part of the premium SKUs. There's cost of operations. Configuring rules on firewalls often results in overwhelming complexity requiring broad exceptions.  <br>
Private connectivity will change architecture design, often adding more components. For example, adding jump boxes for private access to compute nodes. <br>
Because network perimeter is based on control points (or hops) on the network, each hop can be a potential point of failure. These points can have an impact on the reliability of the system.|
|--|

|[Risk icon](../_images/risk.svg) Network controls are rule based and there's a significant chance of misconfiguration, which is a reliability concern. |
|--|

For information about network controls, see [Networking and connectivity](networking.md).

### Roles and responsibility

Defining clear lines of responsibility within a workload team and central teams is also a form of segmentation. This delineation avoids confusion that can create security risks.

Clearly documenting and sharing the roles and functions creates consistency and facilitates communication. Designate groups (or individual roles) that are responsible for key functions. Consider the built-in roles in Azure before creating custom roles for objects.

When assigning permissions for a segment, consider consistency while allowing flexibility to accommodate several organizational models. These models can range from a single centralized IT group to mostly independent IT and DevOps teams.

|[Risk icon](../_images/risk.svg) Membership of groups can change over time as employees join or leave teams or change roles. Management of roles across segments can be a management overhead.|
|--|

For an example of how roles and responsibilities are segmented in a workload team, [Roles and responsibilities](#roles-and-responsibilities).

### Resource organization

Segmentation allows you to isolate workload resources from other parts of the organization or even within the team. Azure constructs such as management groups, subscriptions, environments, resource groups, are ways of organizing your resources that promote segmentation. Here are some examples of resource level isolation.

Using polyglot persistence supports segmentation by using a mix of data store technologies instead of a single database system. The reason might be separation by various data models, separation of functionalities such as data storage and analytics, or the need to separate by access patterns.

When organizing your compute, you can allocate one service per server. This level of isolation minimizes complexity and can help in containing an attack.

Azure also provides built-in isolation in some of the services. For example, separation of compute from storage. For other examples, see [Isolation in the Azure Public Cloud](/azure/security/fundamentals/isolation-choices).

|![Tradeoff icon](../_images/trade-off.svg) There is an increase in total cost of ownership (TCO). For data stores, there might be added complexity and coordination during disaster recovery.|
|--|

## Azure facilitation

### Identity

Azure role-based access control (RBAC) supports segmentation by isolating access by job functions. Only certain actions are allowed for certain roles and scopes.

For example, job functions that only need to observe the system can be assigned Reader permissions versus Contributor permissions that allows the identity to manage resources.

For more information, see [Best practices for Azure RBAC](/azure/role-based-access-control/best-practices).

### Networking

:::image type="content" source="images/segmentation/azure-facilitation-networking.png" alt-text="Diagram that shows networking options for segmentation." border="false" lightbox="images/segmentation/azure-facilitation-networking.png":::

[Virtual Network (VNets)](/azure/virtual-network/virtual-networks-overview): Created within a subscription in private address spaces. They provide network level containment of resources with no traffic allowed by default between any two virtual networks. Like subscriptions, any communication between virtual networks needs to be explicitly provisioned.

Network Security Groups (NSG): An access control mechanism for controlling traffic between resources within a virtual network and with external networks, such as the internet, other virtual networks. Use User Defined Routes (UDR) to control the next hop for traffic. NSGs can take your segmentation strategy to a granular level by creating perimeters for a subnet, a VM, or a group of VMs. For information about possible operations with subnets in Azure, see [Subnets (Azure Virtual Networks)](/rest/api/virtualnetwork/subnets).

[Application Security Groups (ASGs)](/azure/virtual-network/application-security-groups): Similar to NSGs but are referenced with an application context. It allows you to group a set of VMs under an application tag and define traffic rules that are then applied to each of the underlying VMs.

[Azure Firewall](/azure/firewall/): A cloud native stateful Firewall as a service, which can be deployed in your VNet or in [Azure Virtual WAN](/azure/virtual-wan/virtual-wan-about) hub deployments for filtering traffic flowing between cloud resources, the internet, and on-premise. You create rules or policies (using Azure Firewall or [Azure Firewall Manager](/azure/firewall-manager/overview)) specifying allow/deny traffic using layer 3 to layer 7 controls. You can also filter traffic going to the internet using both Azure Firewall and third parties by directing some or all traffic through third-party security providers for advanced filtering & user protection. Azure supports network virtual appliance deployment which helps segmentation with third-party firewalls.

## Example

Here are some common patterns for segmenting a workload in Azure. Choose a pattern based on your organization\'s needs.

### Identity segmentation patterns

#### Pattern 1: Job title-based grouping

[]{#_Networking_segmentation_patterns .anchor}One way to organize security groups is by job title. This approach involves creating security groups for your workload team based on their roles, not considering the work that needs to be accomplished. Grant those security groups RBAC permissions (standing or JIT as needed) according to their responsibilities in the workload. Assign human and service principles to security groups based on their as-needed access.

Examples of job titles include Software Engineer, DBA, SRE, QA, and Security Analyst.

Membership is highly visible at the role assignment level, making it easy to see to what all "role" has access to. Each human is usually only a member of one security group, making onboarding and offboarding easy.

However, unless job titles overlap perfectly with responsibilities, this won't lead to the tightest least privilege implementation. You might end up mixing in function-based grouping which then leads to a mixed implementation.

#### Pattern 2: Function-based grouping

Function-based grouping is a security group organization method that reflects discrete work that needs to be accomplished, not taking into account your team structure. This approach involves creating security groups for your workload that reflect discrete work that needs to be accomplished, again not taking into account your team structure. Grant those security groups RBAC permissions (standing or JIT as needed) according to their required function in the workload.

Assign human and service principles to security groups based on their as-needed access. Where possible, use existing homogeneous groups as members of the function-based groups, such as those groups from Pattern 1.

Examples of function-based groups include Production Database Operators, Pre-production Database Operators, Production Certificate Rotation Operators, Pre-production Certificate Rotation Operators, Production live-site/triage, and Pre-production all access.

This approach maintains the tightest least privilege access and provides self-describing security groups (scope is evident), which makes it easy to audit membership relative to job duties performed. Often a built-in Azure role exists to match this job function.

However, membership is abstracted at least one layer, forcing you to go to the IdP to understand who is in the group when looking from the resource perspective. One person needs to have multiple memberships maintained for complete coverage. The matrix of overlapping security groups can be complex.

Pattern 2 is recommended to make the access patterns the focus, not the org chart. Org charts and members in roles sometimes change often, and capturing your workload's identity and access management from a "functional" perspective allows you to abstract your team organization from the secure management of the workload.

### Networking segmentation patterns

#### Pattern 1: Segmentation within a workload (soft boundaries)

In this pattern, workload is placed in a single VNet using subnets to mark off boundaries. Segmentation is achieved by using two subnets, one for database and another for the web workloads. You must configure NSGs that allow Subnet1 to only communicate with Subnet2 and Subnet2 can only communicate with the internet. You can only have Layer 3 level control.

![Single Virtual Network](./media/image2.png){width="4.247786526684164in" height="2.7692300962379703in"}

#### Pattern 2: Segmentation within a workload

![Multiple Virtual Networks](./media/image3.png){width="4.094885170603675in" height="2.8296708223972002in"}

This is an example of platform-level segmentation. Workload components are spread across multiple VNets without peering between them. All communication is routed through an intermediary that serves as a public access point. All networks are owned by the workload team.

Pattern 2 provides containment but has added complexity of VNet management and sizing. Communication between the two VNets is over the public internet and that can be considered as a risk. There's also latency with public connections. The two networks can be peered. However, peering breaks segmentation by connecting the two networks creating a larger segment. Peering should be done when no other public endpoint are needed.

+-----------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------+
| Considerations                                                        | Pattern 1                                                                                                                     | Pattern 2                                                                                                                      |
+=======================================================================+===============================================================================================================================+================================================================================================================================+
| **Connectivity/routing: how each segment communicates to each other** | System routing provides default connectivity to workload components. No external component can communicate with the workload. | Within the VNet, same as a pattern 1.                                                                                          |
|                                                                       |                                                                                                                               |                                                                                                                                |
|                                                                       |                                                                                                                               | Between VNet, the traffic goes over the public internet. There's no direct connectivity between the networks.                  |
+-----------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------+
| **Network level traffic filtering**                                   | Traffic between the segments is allowed by default. Use NSG, ASG to filter traffic.                                           | Within the VNets, same as a pattern 1.                                                                                         |
|                                                                       |                                                                                                                               |                                                                                                                                |
|                                                                       |                                                                                                                               | Between the networks you can filter traffic through a firewall that can filter both ingress and egress traffic.                |
+-----------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------+
| **Unintended open public endpoints**                                  | NICs don't get public IPs. VNet also isn't exposed to internet API management.                                                | Same as a pattern 1. Intended open public endpoint on one virtual network. That could be misconfigured to accept more traffic. |
+-----------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------+

### Resource organization

**Organize Azure resources based on ownership responsibility**

Consider an Azure estate that contains multiple workloads, as well as shared services components like hub virtual networks, firewalls, identity services, and security services like Microsoft Sentinel:

![A screenshot of a computer Description automatically generated](./media/image4.png){width="6.5in" height="2.2430555555555554in"}

Components throughout the estate should be grouped based on their functional areas, workloads, and ownership. For example, shared networking resources should be grouped together into a single subscription and managed by a networking team. Components that are dedicated to individual workloads should be in their own segment and might be further divided based on application tiers or other organizational principles.

You can then grant access to manage resources within individual segments by creating Azure RBAC role assignments. For example, the cloud networking team might be granted administrative access to the subscription that contains their resources, but not to individual workload subscriptions.

When you create a good segmentation strategy, it should be possible to easily identify the owners of each segment. Consider using Azure resource tags to annotate resource groups or subscriptions with the owner team.

**Configure and review access control**

When your segments are clearly defined for your resources, you can grant appropriate access based on need.

Carefully consider the principle of least privilege when you define access control policies. It's also important to distinguish between *control plane operations* (management of the resource itself) from *data plane operations* (access to the data stored by the resource). For example, suppose you have a workload that contains a database with sensitive information about employees. You might grant management access to some users who need to configure settings like database backups and who monitor the performance of the database server. However, these individuals shouldn't be able to query the sensitive data stored in the database, so be sure to select permissions that grant the minimum scope they need to perform their duties.

Regularly review the role assignments for each segment and remove access that's no longer required.

> [!NOTE] 
> Some highly privileged roles, like the Owner role in Azure RBAC, give users the ability to grant other users access to a resource. Limit how many users or groups are assigned the Owner role, and regularly review audit logs to ensure they are only performing valid operations.
