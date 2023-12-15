---
title: Operational procedure considerations for Azure Virtual Desktop workloads
description: Examine Azure Virtual Desktop operational procedures. Understand shared responsibilities and see practices for efficiently running workloads.
author: jszubryt
ms.author: szubrytjim
ms.date: 10/12/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avd
---

# Operational procedure considerations for Azure Virtual Desktop workloads

This article discusses the operational procedure design area of an Azure Virtual Desktop workload. When you run your organization's Azure Virtual Desktop environment, you need to establish proper operations management outcomes. You need to have a plan for how to manage technology solutions, support the platform, ensure smooth upgrades, and provide platform resiliency.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected Framework Azure Virtual Desktop workload](./index.yml) series. If you aren't familiar with this series, we recommend that you start with [What is an Azure Virtual Desktop workload?](./overview.md#what-is-an-azure-virtual-desktop-workload).

## Shared responsibilities

*Impact: Operational Excellence, Performance Efficiency, Security*

As with other services that Microsoft offers, there are Azure Virtual Desktop components that Microsoft manages and components that the customer manages. The following diagram illustrates how various areas of responsibility are shared between the customer and Microsoft.

:::image type="content" source="./images/shared-responsibility-areas.png" alt-text="Diagram that lists components of Azure Virtual Desktop. Colored boxes indicate which areas Microsoft manages and which areas customers manage." border="false" lightbox="./images/shared-responsibility-areas.png":::

### Components that Microsoft manages

Microsoft manages the following Azure Virtual Desktop services as part of Azure:

- Web access. When you use the web access service within Azure Virtual Desktop, you can access virtual desktops and remote apps through an HTML5-compatible web browser just as you would with a local PC. This access is available from anywhere and on any device. You can help secure web access by using multifactor authentication in Microsoft Entra ID.
- Gateway. The remote connection gateway service connects remote users to Azure Virtual Desktop apps and desktops from any internet-connected device that can run an Azure Virtual Desktop client. The client connects to a gateway, which then orchestrates a connection from a virtual machine (VM) back to the same gateway.
- Connection broker. The connection broker service manages user connections to virtual desktops and remote apps. The connection broker provides load balancing and reconnection to existing sessions.
- Diagnostics. Remote desktop diagnostics is an event-based aggregator that marks each user or administrator action on the Azure Virtual Desktop deployment as a success or failure. Administrators can query the event aggregation to identify failing components.
- Extensibility components. Azure Virtual Desktop includes several extensibility components. You can manage Azure Virtual Desktop by using Windows PowerShell or the provided REST APIs, which also enable support from third-party tools.

### Components that the customer manages

You manage the following components of Azure Virtual Desktop solutions:

- Azure Virtual Network. In Virtual Network, Azure resources like VMs can communicate privately with each other and with the internet. When you connect Azure Virtual Desktop host pools to an Active Directory domain, you can define a network topology to access virtual desktops and virtual apps from the intranet or internet, based on organizational policy. You can connect Azure Virtual Desktop to an on-premises network by using a virtual private network (VPN). Or you can use Azure ExpressRoute to extend the on-premises network into the Azure cloud over a private connection.
- Microsoft Entra ID. Azure Virtual Desktop uses Microsoft Entra ID for identity and access management. Microsoft Entra ID integration applies Microsoft Entra ID security features, such as Conditional Access, multifactor authentication, and intelligent security graph. It also helps maintain app compatibility in domain-joined VMs.
- Optionally, Active Directory Domain Services (AD DS). Azure Virtual Desktop VMs must be domain-joined to AD DS or Microsoft Entra Domain Services. AD DS must be in sync with Microsoft Entra ID to associate users between the two services. You can use Microsoft Entra Connect to associate AD DS with Microsoft Entra ID.
- Azure Virtual Desktop session hosts. Session hosts are VMs that users connect to for their desktops and applications. Several versions of Windows are supported, and you can create images with your applications and customizations. You can choose VM sizes, including GPU-enabled VMs. Each session host has an Azure Virtual Desktop host agent, which registers the VM as part of the Azure Virtual Desktop workspace or tenant. Each host pool can have one or more app groups, which are collections of remote applications or desktop sessions that you can access. To see which versions of Windows are supported, see [Operating systems and licenses](/azure/virtual-desktop/prerequisites?tabs=portal#operating-systems-and-licenses).
- Azure Virtual Desktop workspaces. The Azure Virtual Desktop workspace, or tenant, is a management construct for managing and publishing host pool resources.

##### Recommendations

- Be aware of your responsibilities under the shared-responsibility model.
- Ensure that your organization actively manages components that fall under the customer's responsibility. Examples include your network topology, your session hosts, and your workspace.

## Manage the environment

*Impact: Operational Excellence, Reliability*

To manage your Azure Virtual Desktop environment, focus on the following areas:

- Business alignment. To help meet service-level agreements (SLAs), deploy your session hosts in an availability zone to help protect them from failures in specific fault domains.
- Operations baseline. Establish an operations baseline. For more information, see [Overview of Azure server management services](/azure/cloud-adoption-framework/manage/azure-server-management/).
- Platform operations. Use monitoring techniques like tools, dashboards, and alerts to help the operations team effectively monitor and react to incidents to maintain a reliable platform. For more information, see [Monitoring considerations for an Azure Virtual Desktop workload](monitoring.md).

##### Recommendations

- Deploy your session hosts in an availability zone.
- Establish an operations baseline.
- Use monitoring tools, dashboards, and alerts.

## Awareness of new developments

*Impact: Operational Excellence*

It's important to stay up to date with the latest updates, features, feature improvements, and bug fixes. For monthly updates, see [What's new in Azure Virtual Desktop?](/azure/virtual-desktop/whats-new).

##### Recommendations

- Be aware of the latest Azure Virtual Desktop updates, features, feature improvements, and bug fixes.
- Check resources like [What's new in Azure Virtual Desktop?](/azure/virtual-desktop/whats-new) on a monthly basis.

## Monitor limit thresholds

*Impact: Operational Excellence*

As your Azure Virtual Desktop platform grows, you need to be mindful of which limits you're close to reaching. Successfully managing the platform and proactively preventing service disruptions to your customers requires that you carefully monitor the limits of the components that you use.

### FSLogix

The limitations of FSLogix depend on the storage fabric that you use to store user profile virtual hard disk (VHD) and VHD extended (VHDX) files.

The following table provides examples of how many input/output operations per second (IOPS) an FSLogix profile requires to support each Azure Virtual Desktop user in various scenarios. User data, applications, and the amount of activity on each profile influence the amount that's required.

| Resource | IOPS requirement per user | Number of users | Number of IOPS needed |
| --- | --- | --- | --- |
| Steady-state IOPS | 10 | 100 | 1,000 |
| Sign-in and sign-out IOPS | 50 | 100 | 5,000 |

### Host pools

The following factors can affect host pool scaling:

- The Azure template limits the number of objects that you can create, and each VM creates a certain number of objects. As a result, there's a limit to the number of VMs that you can create each time that you run the template. For more information, see [Is there a scale limit for host pools created in the Azure portal?](/azure/virtual-desktop/faq#is-there-a-scale-limit-for-host-pools-created-in-the-azure-portal-).
- For vCPUs, there's a limit on the number that you can create per region, per subscription, and type of subscription. Enterprise Agreement subscriptions have a limit of 350 vCPUs by default. To determine the number of VMs that you can create per template run, divide your vCPU limit by the number of vCPUs that you have per VM.

### Service limits

All resources that are used in Azure Virtual Desktop, such as VMs, storage space, and networking, are subject to restrictions and limitations. For example, there's a service limit of 10,000 on session host objects. Crossing these limits can impact your service availability.

The Azure Virtual Desktop infrastructure uses the following components. For the corresponding service limits, see [Azure Virtual Desktop service limits](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-virtual-desktop-service-limits).

- Application group
- Host pool
- RemoteApp
- Role assignment
- Session host
- Workspace

### VM token expirations

In Azure Virtual Desktop, VMs are registered to a host pool and are assigned a token. The Azure Virtual Desktop agent regularly refreshes a VM's token when the VM is active. Registration tokens are valid for 90 days.

To prevent tokens from expiring, the Azure Virtual Desktop team should use automation to regularly turn on each VM. For instance, an automated solution might turn on each VM for 20 minutes every 90 days. Then each VM's token is refreshed before it expires or becomes invalid. The agent and side-by-side stack components are also updated.

VMs that have been turned off for more than 90 days experience registration problems. To use a VM again, follow the steps in [Troubleshoot common Azure Virtual Desktop agent issues](/azure/virtual-desktop/troubleshoot-agent#your-issue-isnt-listed-here-or-wasnt-resolved). These instructions explain how to remove the VM from your host pool, reinstall the agent, and register the VM again with your host pool.

##### Recommendations

- Monitor the resource usage of your components.
- Be aware of system limits on:
  - Services.
  - Azure objects.
  - The number of vCPUs that you can create.
- Understand how many IOPS an FSLogix profile requires to support each user.
- Use automation to prevent VM tokens from expiring.

## Host pool golden image updates

*Impact: Operational Excellence, Reliability*

You can update your host pool VMs by taking either of the following approaches:

- Deploy a second host pool. When it's ready, assign users to that pool. This approach gives you the option of having the initial host pool available for a rollback. You can remove the original host pool after you receive confirmation that the new host pool is performing as expected.
- Set your original VMs to drain mode in the host pool. Then deploy new VMs from a new golden image into that same host pool. This approach is riskier. You can hit resource constraints or API throttling limits when you double the number of VMs in a single host pool.

##### Recommendations

- Deploy a second host pool when you update your VMs if you prefer to have your initial host pool available for a rollback.
- Update your VMs by deploying new VMs from a new golden image into your host pools if you can double your number of VMs per host pool.

## Image management

*Impact: Operational Excellence, Security*

You can use Azure VM Image Builder to automate the build, update, system preparation, and distribution processes for golden images. You can use Azure Marketplace with supported base images to help ensure that you have the latest updates.

As part of your golden image build process for updating VMs, you can use a script to install the applications that you use. PowerShell is the recommended scripting approach. If you need to be able to roll back applications or data, use a version control system and a repository to manage scripts and installers. Azure Key Vault is a recommended approach for storing any secrets that you need as part of an automated deployment process.

##### Recommendations

- Use VM Image Builder to automate the process of updating golden images.
- Retrieve the latest versions of images from Azure Marketplace.
- Use PowerShell scripts to install applications.
- Use a version control system to manage deployment scripts.
- Use Key Vault to store secrets that your automated deployment processes use.

## Stay up to date with supported versions

*Impact: Operational Excellence*

Version compliance is important when you run any type of platform. The following resources provide up-to-date information about Azure Virtual Desktop components:

- [What's new in the Azure Virtual Desktop agent?](/azure/virtual-desktop/whats-new-agent)
- [What's new in Azure Virtual Desktop insights?](/azure/virtual-desktop/whats-new-insights)
- [FSLogix release notes](/fslogix/overview-release-notes?context=%2Fazure%2Fvirtual-desktop%2Fcontext%2Fcontext)
- [What's new in the Remote Desktop WebRTC Redirector service](/azure/virtual-desktop/whats-new-webrtc)
- [What's new in multimedia redirection?](/azure/virtual-desktop/whats-new-multimedia-redirection)
- [What's new in the MSIXMGR tool](/azure/virtual-desktop/whats-new-msixmgr)

##### Recommendations

- Regularly review release notes and other articles about developments in Azure Virtual Desktop components.
- Install updates when they become available.

## Next steps

Now that you've looked at operational procedures, see how to design Azure Virtual Desktop components for reliability.

> [!div class="nextstepaction"]
> [Business continuity](./business-continuity.md)

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./assessment.md)