---
title: Business continuity considerations for Azure Virtual Desktop workloads
description: Understand Azure Virtual Desktop business continuity concepts. See how to design host pools, virtual networks, and other components for reliability.
author: moisesjgomez
ms.author: Mauise.Ricard
ms.date: 09/15/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avd
---

# Business continuity considerations for Azure Virtual Desktop workloads

This article discusses the business continuity design area of an Azure Virtual Desktop workload. To strengthen your organization's Virtual Desktop deployment and to help keep data safe, you should implement a business continuity and disaster recovery strategy. A good strategy helps keep your apps and workloads up and running during planned and unplanned service or Azure outages.

To help make sure that your users can connect during an Azure region outage, you might need to replicate personal virtual machines (VMs) to a different Azure region—your secondary location. During outages, the primary region fails over to the replicated VMs in the secondary location. Users can continue to access apps from the secondary location without interruption. Besides replicating VMs, you also need to ensure that user identities are accessible at the secondary location. You can use profile containers to make identities accessible. As an alternative to VM replication, you can use multiple pooled host pools with automated provisioning across regions.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected Framework Azure Virtual Desktop workload](./index.yml) series. If you aren't familiar with this series, we recommend that you start with [What is an Azure Virtual Desktop workload?](./overview.md#what-is-a-virtual-desktop-workload).

## Virtual Desktop service

*Impact: Reliability*

In the Virtual Desktop service, Microsoft manages some areas of responsibility, and the customer manages others.

- Microsoft controls metadata like the host pools, application groups, and workspaces. The metadata is always available. There's no need for the customer to do extra setup to replicate host pool data or configurations. The gateway infrastructure that connects people to their session hosts is designed to be a global, highly resilient service that Microsoft manages.
- Customer-managed areas involve the VMs that Virtual Desktop uses and the settings and configurations that are unique to the customer's deployment.

The following table provides detailed information about the areas that each party manages:

| Managed by Microsoft | Managed by the customer |
| --- | --- |
| Load balancer | Network |
| Session broker | Session hosts |
| Gateway | Storage |
| Diagnostics | User profile data |
| Cloud identity platform  | Identity |

##### Assessment question

In Virtual Desktop, do you know which areas Microsoft manages and which areas you're responsible for managing?

##### Recommendations

- Be aware of your responsibilities under the shared-responsibility model.
- Ensure that your organization actively manages areas that fall under the customer's responsibility. Examples include the VMs that Virtual Desktop uses, the user profile data, and settings that are unique to your environment.

## Host pools

*Impact: Reliability, Cost Optimization*

If distinct sets of users have different business continuity and disaster recovery requirements, we recommend that you use multiple host pools with different configurations. For example, users with a business-critical application might use a fully redundant host pool with geo–disaster recovery capabilities. But development and test users can use a separate host pool with no disaster recovery at all.

For each Virtual Desktop host pool, you can base your business continuity and disaster recovery strategy on an active-active or active-passive model. In this context, it's assumed that a specific host pool serves a set of users in a geographic location.

### Active-active scenarios

There are two possible configurations for an active-active scenario. One configuration involves two host pools. The other uses one pool.

#### Two host pools

In the two-pool configuration, for each host pool in your primary region, you deploy a secondary host pool in your secondary region. Your administrator doesn't need to intervene or fail over. Like during normal operations, the secondary host pool provides users with Virtual Desktop resources.

Each host pool has its own storage account for persistent user profiles. If your disaster recovery plans require that profiles persist, you need to use the cloud cache feature of FSLogix to sync profiles across regions. To avoid profile conflicts, don't allow users to access both host pools at the same time.

A disadvantage of the two-pool configuration is that definitions and assignments for application groups are duplicated. Users see two entries for host pools, which can cause confusion. To get around this problem, you can educate users about how to use these resources.

#### Single host pool

The second possible active-active configuration uses a single host pool that's deployed with a mix of session hosts that are located in different regions. An advantage of using a single-host pool is that there's no duplication of definitions and assignments for application groups. There are several disadvantages:

- For pooled host pools, you can't force a user to use a session host in the same region.
- Users might experience higher latency and suboptimal performance when they connect to session hosts in remote regions.  
- If storage is required for user profiles, you need to use a complex configuration to manage assignments for session hosts in the primary and secondary regions.
- You can use drain mode to temporarily disable access to session hosts that are located in the secondary region. But this practice increases management overhead and the complexity of your configuration. It also makes inefficient use of resources.
- You can maintain session hosts in an offline state in the secondary regions. But this practice increases management overhead and the complexity of your configuration.

### Active-passive scenarios

In an active-passive scenario, the host deployment is similar to the setup in an active-active scenario. Specifically, for each host pool in the primary region, you deploy a secondary host pool in the secondary region. But in an active-passive scenario, fewer compute resources are active in the secondary region than in the primary region. The number of resources that are active depends on your available budget. You can use automatic scaling to provide more compute capacity. But it requires time to become available, and Azure capacity isn't guaranteed.

Also note the following points about active-passive models:

- Administrator intervention is needed to run a failover procedure during an Azure outage.
- The secondary host pool doesn't normally give your users access to Virtual Desktop resources.
- Each host pool has its own storage accounts for persistent user profiles. If you need to persist profiles across regions, you can use the cloud cache feature to sync profiles.
- Users are assigned to one set of application groups. During normal operations, the primary host pool serves users. During an outage, users are assigned to secondary host pool application groups.

If you use a personal host pool, you can use Azure Site Recovery to replicate session hosts from the primary region to the secondary region. But you need to have the proper infrastructure in place.

##### Assessment question

How do you implement business continuity and disaster recovery for your host pools?

##### Recommendations

- Use multiple host pools if you have distinct sets of users who have different business continuity disaster recovery requirements.
- If you don't have an administrator who can intervene during an outage, use an active-active configuration.
- Use the cloud cache feature to sync profiles across regions.
- In an active-active configuration, don't allow users to access both host pools at the same time.
- If you use an active-active configuration, educate users about the duplication of resources.
- To use fewer compute resources, use an active-passive configuration.
- Use automatic scaling to increase compute capacity if your workload isn't sensitive to performance variability.
- With a personal host pool, use Site Recovery to replicate session hosts from the primary region to the secondary region.

## Capacity planning

*Impact: Reliability, Cost Optimization*

Virtual Desktop operates within the constraints of Azure capacity limits.

For instance, Azure imposes subscription-level limits on the number of VMs that you can deploy in Virtual Desktop. For example, a subscription might have a limit of 25,000 VMs. To help ensure smooth operations, you need to be aware of this limit and plan deployments accordingly. Scaling beyond this limit might require extra subscriptions or alternative strategies, which can have cost implications.

Another example is session hosts in a host pool. Within Virtual Desktop, each host pool has its own limits on the number of session hosts that can accommodate user sessions. The VM series and the size that you select for the host pool determine the maximum number of session hosts that you can use. To optimize performance and user experience, you need to consider these limits when you design your deployment.

For more information about platform constraints, see [Azure Virtual Desktop limitations](/azure/architecture/example-scenario/wvd/windows-virtual-desktop#azure-virtual-desktop-limitations).

##### Assessment question

How do you address platform service limitations in Virtual Desktop?

##### Recommendations

- Monitor and plan for subscription limits. Closely monitor your Virtual Desktop deployments, and keep track of resource usage within your subscription. By proactively monitoring capacity, you can identify potential challenges early on, and you can take suitable actions to avoid reaching limits.
- Consider scaling across multiple subscriptions, or work with Azure support to adjust limits based on your business requirements.
- Scale horizontally for high demand. To handle a large number of users, consider scaling horizontally by creating multiple host pools. Distributing user sessions across several session hosts helps ensure that no single host pool reaches its capacity limit.

## FSLogix profiles and MSIX app attach

*Impact: Reliability, Cost Optimization*

For FSLogix profiles and MSIX app attach, it's best to consider three scenarios:

- The local corruption of data, metadata, or resources. In this case, you can use Azure Backup:
  - If you use Azure Files for FSLogix storage, you can configure Backup for Azure Files.
  - If you use Azure NetApp Files for FSLogix storage, you can configure Backup for Azure NetApp Files snapshots.

  Alternatively, you can use the backup service to protect files and folders on server VMs.

- The failure of a single datacenter of an availability zone within an Azure region. In this scenario, you can use Azure Files with premium zone-redundant storage to take advantage of support for availability zones. FSLogix profiles and MSIX app attach virtual hard disks (VHDs) remain available during datacenter outages.

- An Azure region outage. If you have a multiple-region deployment, you can use the FSLogix cloud cache feature to help protect your system in this case by replicating profiles across regions.

Another option for business continuity and disaster recovery is no-profile recovery. With this approach, profiles aren't recovered after a failure. Instead, users create new profiles in the new location or storage provider. This strategy helps ensure that user profiles don't contain business-critical data. This option is the most cost-effective in terms of infrastructure cost. But it can have a negative effect on user experience.

##### Assessment question

How do you protect FSLogix profiles against data corruption, datacenter failures, and regional outages?

##### Recommendations

- Use Backup to back up FSLogix profiles that you store in Azure Files or Azure NetAppFiles.
- Use zone-redundant storage for premium Azure file shares to replicate data synchronously across Azure availability zones.
- Use the FSLogix cloud cache feature to replicate profiles across regions.
- Consider using a no-profile recovery strategy.

## Virtual networks

*Impact: Reliability, Cost Optimization*

Virtual networks are managed services that are unaffected by:

- The local corruption of data, metadata, or resources.
- The failure of a single datacenter of an availability zone within an Azure region.

Azure Virtual Network provides a private IP address block that you can use to deploy your resources for private connectivity. You can then secure those resources within a boundary. As a result, a virtual network doesn't go down or experience an outage if there's a local failure of resources in a single datacenter.

When an entire Azure region experiences an outage, virtual networks are affected. Regional failures also affect the services that are deployed within virtual networks. To help protect your resources, you need to plan for a virtual network in your secondary region. You can create a virtual network manually and then set up peering with your primary network. You can also use Site Recovery to set up the virtual network in the failover region and preserve your primary network's settings. In a Virtual Desktop environment that's connected with your on-premises network, you should configure the virtual network in the secondary region with connectivity to the on-premises network.

##### Assessment question

How do you protect your virtual network against datacenter and Azure region failures?

##### Recommendations

- Deploy your resources in the private IP address block that Virtual Network provides.
- Set up a virtual network in your secondary region and peer it with your primary network.
- Use Site Recovery to set up a virtual network in your failover region.

## Golden images

*Impact: Reliability, Cost Optimization*

For golden images, it's best to consider three scenarios:

- The local corruption of data, metadata, or resources. In this case, you can use Azure Compute Gallery to store and share images that you use in Virtual Desktop. By default, Compute Gallery uses locally redundant storage.
- The failure of a single datacenter of an availability zone within an Azure region. In this scenario, you can use zone-redundant storage to spread copies of images across availability zones. When zone-redundant storage is available, use it for high availability.
- An Azure region outage. Compute Gallery is a regional resource. To help protect against regional failures, you need to create a secondary compute gallery within a secondary region. You also need to have multiple replicas of the same image in the primary compute gallery.

##### Assessment question

How do you protect your golden images against data corruption, datacenter failures, and regional outages?

##### Recommendations

- Use Compute Gallery to store images.
- Use zone-redundant storage to spread copies of images across availability zones.
- Create a secondary compute gallery within a secondary region.

## Next steps

Now that you've examined business continuity considerations, see how to optimize performance and cost considerations when you select storage solutions for your workload.

> [!div class="nextstepaction"]
> [Storage](./storage.md)

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./assessment.md)