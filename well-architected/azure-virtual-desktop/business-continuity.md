---
title: Business continuity considerations for Azure Virtual Desktop workloads
description: Understand Azure Virtual Desktop business continuity concepts. See how to design host pools, virtual networks, and other components for reliability.
author: moisesjgomez
ms.author: mgomezcortez
ms.date: 09/15/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avd
---

# Business continuity considerations for Azure Virtual Desktop workloads

This article discusses the business continuity design area of an Azure Virtual Desktop workload. To strengthen your organization's Azure Virtual Desktop deployment and to help keep data safe, you should implement a business continuity and disaster recovery strategy. A good strategy helps keep your apps and workloads up and running during planned and unplanned service or Azure outages.

To help make sure that your users can connect during an Azure region outage, you might need to replicate personal virtual machines (VMs) to a different Azure region—your secondary location. During outages, the primary region fails over to the replicated VMs in the secondary location. Users can continue to access apps from the secondary location without interruption. Besides replicating VMs, you also need to ensure that user identities are accessible at the secondary location. You can use profile containers to make identities accessible. As an alternative to VM replication, you can use multiple pooled host pools with automated provisioning across regions.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected Framework Azure Virtual Desktop workload](./index.yml) series. If you aren't familiar with this series, we recommend that you start with [What is an Azure Virtual Desktop workload?](./overview.md#what-is-an-azure-virtual-desktop-workload).

## Azure Virtual Desktop service

*Impact: Reliability*

In the Azure Virtual Desktop service, Microsoft manages some areas of responsibility, and the customer manages others.

- Microsoft controls metadata like the host pools, application groups, and workspaces. The metadata is always available. There's no need for the customer to do extra setup to replicate host pool data or configurations. The gateway infrastructure that connects people to their session hosts is designed to be a global, highly resilient service that Microsoft manages.
- Customer-managed areas involve the VMs that Azure Virtual Desktop uses and the settings and configurations that are unique to the customer's deployment.

The following table provides detailed information about the areas that each party manages:

| Managed by Microsoft | Managed by the customer |
| --- | --- |
| Load balancer | Network |
| Session broker | Session hosts |
| Gateway | Storage |
| Diagnostics | User profile data |
| Cloud identity platform  | Identity |

##### Recommendations

- Be aware of your responsibilities under the shared-responsibility model.
- Ensure that your organization actively manages areas that fall under the customer's responsibility. Examples include the VMs that Azure Virtual Desktop uses, the user profile data, and settings that are unique to your environment.

## Host pools

*Impact: Reliability, Cost Optimization*

If distinct sets of users have different business continuity and disaster recovery requirements, we recommend that you use multiple host pools with different configurations. For example, users with a business-critical application might use a fully redundant host pool with geo–disaster recovery capabilities. But development and test users can use a separate host pool with no disaster recovery at all.

For each Azure Virtual Desktop host pool, you can base your business continuity and disaster recovery strategy on an active-active or active-passive model.

### Active-active scenarios

The recommended configuration for an active-active scenario uses two host pools, one host pool per region. No administrator intervention is needed for failover. Like during normal operations, the secondary host pool provides users with Azure Virtual Desktop resources. The users must have a good understanding of the capabilities that Azure Virtual Desktop offers and how to use them.

Each host pool has its own storage account for persistent user profiles. If your disaster recovery plans require that profiles persist, you need to use the cloud cache feature of FSLogix to sync profiles across regions. To avoid profile conflicts, don't allow users to access both host pools at the same time.

For more information, see [Multiregion business continuity and disaster recovery (BCDR) for Azure Virtual Desktop](/azure/architecture/example-scenario/wvd/azure-virtual-desktop-multi-region-bcdr).

### Active-passive scenarios

In an active-passive scenario, the host deployment is similar to the setup in an active-active scenario. For each host pool in the primary region, another host pool is deployed in the secondary region. But in an active-passive scenario, fewer compute resources are active in the secondary region than in the primary region. It's possible that no compute resources are active in the secondary region. The number of resources that are active usually depends on your available budget. Be aware that Azure capacity isn't guaranteed during regional outages when inactive resources must be turned on. Capacity is only guaranteed if the VMs are active. Consider using on-demand capacity reservations to reserve Azure capacity for disaster recovery scenarios. For more information, see [On-demand capacity reservation](/azure/virtual-machines/capacity-reservation-overview).

Also note the following points about active-passive models:

- Each host pool has its own storage accounts for persistent user profiles.
- If profiles must persist across regions, you can use the cloud cache feature to sync profiles.

If you plan to use a personal host pool, you can use Azure Site Recovery to replicate session hosts from the primary region to the secondary region. To take this approach, you need to have the proper infrastructure in place.

##### Recommendations

- Use multiple host pools if you have distinct sets of users who have different business continuity disaster recovery requirements.
- Evaluate the cloud cache feature of FSLogix to sync profiles across regions if profiles must be protected.
- Educate users about the proper use of resources if you use an active-active configuration.
- Use Site Recovery to replicate session hosts from the primary region to the secondary region when you use a personal host pool.

## Capacity planning

*Impact: Reliability, Cost Optimization*

Azure Virtual Desktop operates within the constraints of Azure capacity limits.

For instance, Azure imposes subscription-level limits on the number of VMs that you can deploy in Azure Virtual Desktop. For example, a subscription might have a limit of 25,000 VMs. To help ensure smooth operations, you need to be aware of this limit and plan deployments accordingly. Scaling beyond this limit might require extra subscriptions or alternative strategies, which can have cost implications.

Another example is session hosts in a host pool. Within Azure Virtual Desktop, each host pool has its own limits on the number of session hosts that can accommodate user sessions. The VM series and the size that you select for the host pool determine the maximum number of session hosts that you can use. To optimize performance and user experience, you need to consider these limits when you design your deployment.

For more information about platform constraints, see [Azure Virtual Desktop limitations](/azure/architecture/example-scenario/wvd/windows-virtual-desktop#azure-virtual-desktop-limitations).

##### Recommendations

- Monitor and plan for subscription limits. Closely monitor your Azure Virtual Desktop deployments, and keep track of resource usage within your subscription. By proactively monitoring capacity, you can identify potential challenges early on, and you can take suitable actions to avoid reaching limits.
- Consider scaling across multiple subscriptions if further scaling is needed, or work with Azure support to adjust limits based on your business requirements.
- Scale horizontally for high demand. To handle a large number of users, consider scaling horizontally by creating multiple host pools.

## FSLogix profiles and App Attach

*Impact: Reliability, Cost Optimization*

To minimize the amount of data in user profiles, redirect user data to a storage solution. When you minimize the data in user profiles, you also minimize the protection that you need to provide for the profiles. A no-profile recovery strategy decreases overhead but can affect the user experience in disaster recovery scenarios.

In some cases, the profile container content needs a separate business continuity and disaster recovery solution, because the profile container requirements differ from the office container requirements. In this situation, you can separate the two and manage each one independently.

For FSLogix profiles and App Attach, it's best to consider three scenarios:

- The local corruption of data, metadata, or resources. In this case, you can use Azure Backup:
  - If you use Azure Files storage, you can configure Backup for Azure Files.
  - If you use Azure NetApp Files for storage, you can configure Backup for Azure NetApp Files snapshots.

  Alternatively, you can use the backup service to protect files and folders on server VMs.

- The failure of a single datacenter of an availability zone within an Azure region. In this scenario, you can use Azure Files with premium zone-redundant storage to take advantage of support for availability zones. FSLogix profiles and App Attach virtual hard disks (VHDs) remain available during datacenter outages.

- An Azure region outage. If you have a multiregion deployment, you can use the FSLogix cloud cache feature to help protect user profiles by replicating across regions.

##### Recommendations

- Consider using a no-profile recovery strategy to minimize the need for protecting profiles.
- Use Backup to back up FSLogix profiles that you store in Azure Files or Azure NetAppFiles.
- Use zone-redundant storage to replicate data synchronously across Azure availability zones.
- Use the FSLogix cloud cache feature to replicate profiles across regions.

## Virtual networks

*Impact: Reliability, Cost Optimization*

Virtual networks are managed services that are unaffected by:

- The local corruption of data, metadata, or resources.
- The failure of a single datacenter of an availability zone within an Azure region.

Azure Virtual Network provides a private IP address block that you can use to deploy your resources for private connectivity. You can then secure those resources within a boundary. As a result, a virtual network doesn't go down or experience an outage if there's a local failure of resources in a single datacenter.

When an entire Azure region experiences an outage, virtual networks are affected. Regional failures also affect the services that are deployed within virtual networks. To help protect your resources, you need to plan for a virtual network in your secondary region to allow for the replication of personal host pool VMs or space for deployment of session hosts. You can also use Site Recovery to set up the virtual network in the failover region and preserve your primary network's settings. In an Azure Virtual Desktop environment that's connected with your on-premises network, you should configure the virtual network in the secondary region with connectivity to the on-premises network.

##### Recommendations

- Set up a virtual network in your secondary region for failover.
- Use Site Recovery to set up a virtual network in your failover region.

## Golden images

*Impact: Reliability, Cost Optimization*

For golden images, it's best to consider three scenarios:

- The local corruption of data, metadata, or resources. In this case, you can use Azure Compute Gallery to store and share images that you use in Azure Virtual Desktop. By default, Compute Gallery uses locally redundant storage.
- The failure of a single datacenter of an availability zone within an Azure region. In this scenario, you can use zone-redundant storage to spread copies of images across availability zones. When zone-redundant storage is available, use it for high availability.
- An Azure region outage. Compute Gallery is a regional resource. To help protect against regional failures, you need to create a secondary compute gallery within a secondary region. You also need to have multiple replicas of the same image in the primary compute gallery.

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
