---
title: Storage considerations for Azure Virtual Desktop workloads
description: Understand Azure Virtual Desktop storage concepts. See how to optimize performance and cost considerations when you select storage solutions.
author: gaquamme
ms.author: gaquamme
ms.date: 09/15/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avd
---

# Storage considerations for Azure Virtual Desktop workloads

This article discusses the storage design area of an [Azure Virtual Desktop](/azure/virtual-desktop/overview) workload. Virtual Desktop is a cloud-based, end-user compute solution that gives users access to their desktops and applications from anywhere, on any device. This discussion covers storage considerations for virtual machines (VMs), user profiles, and applications.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected Framework Azure Virtual Desktop workload](./index.yml) series. If you aren't familiar with this series, we recommend that you start with [What is an Azure Virtual Desktop workload?](./overview.md#what-is-a-virtual-desktop-workload).

## Region selection

*Impact: Performance Efficiency, Cost Optimization*

When you deploy Virtual Desktop, region selection is an important consideration. Before you select the Azure region that you want to deploy to, you should check product availability . SKUs for services such as Azure Virtual Machines can vary by region, because not every region contains identical hardware. For the availability of VM SKUs and other services, see [Products available by region](https://azure.microsoft.com/explore/global-infrastructure/products-by-region/?products=virtual-machines).

Before you select a specific region, you should also be aware of its regional compliance and data residency requirements.

Another important consideration in region selection is latency. It's generally best to centralize all necessary resources in the same Azure region as your host pool. If you deploy Virtual Desktop in a region that's far from your users, it can increase latency and degrade performance. It can also increase the cost of data transfer between regions.

The Virtual Desktop metadata store contains workspaces, host pools, app groups, and scaling plans. This metadata store isn't always supported in the region that you select for your session hosts. This lack of support can cause the metadata store and session hosts to be separated. For detailed information, see [Data locations for Azure Virtual Desktop](/azure/virtual-desktop/data-locations).

##### Assessment question

What factors did you consider when you selected your deployment regions for Virtual Desktop?

##### Recommendations

- Check that the VM SKUs and Azure services that you need are available in your selected region.
- Before you select a region, familiarize yourself with its compliance and data residency requirements.
- To minimize latency and reduce data transfer costs, place your resources in the same Azure region as your host pool.
- Familiarize yourself with the regions that Virtual Desktop management plane resources are available in.

## VM and disk sizing

*Impact: Performance Efficiency, Cost Optimization*

VM sizing is an important consideration when you deploy Virtual Desktop. Sizing directly affects your cost and end-user experience. Sizing also determines which workloads run smoothly. The overall goal is to right-size your VMs by looking at factors like the resource requirements of your workload and whether you use personal or pooled host pools. It's also preferable to use a larger number of smaller VMs than to use a few large VMs, because it's easier to manage and maintain smaller VMs. For detailed information about VM sizing, see [Session host virtual machine sizing guidelines](/windows-server/remote/remote-desktop-services/virtual-machine-recs).

Scaling plans provide an efficient way of using small, low-user-density VMs. A scaling plan automatically adjusts the number of VMs based on user demand and schedule. This approach reduces costs by turning off unused VMs. It optimizes resources by matching host pool capacity with user sessions. For more information, see [Autoscale scaling plans and example scenarios in Azure Virtual Desktop](/azure/virtual-desktop/autoscale-scenarios).

When you select an operating system disk for Virtual Desktop, you can save costs by selecting a standard hard-disk drive (HDD) over a solid-state drive (SSD), but you sacrifice performance. If your production workload requires a service-level agreement (SLA), you should use premium SSD storage.

Another consideration is that Azure VMs have I/O operations per second (IOPS) and throughput performance limits that are based on the VM type and size. The selected SKU of operating system disks and data disks ultimately determines the IOPS that are available to the user. If you use FSLogix profiles, you have extra IOPS requirements, because the profiles are pulled from network shares and written to disk. For detailed information about IOPS and disk performance, see [Scalability and performance targets for VM disks](/azure/virtual-machines/disks-scalability-targets).

##### Assessment question

What factors did you consider when you selected a VM SKU and a disk type?

##### Recommendations

- Before you select a VM SKU, examine the CPU, GPU, memory, and storage usage of the workloads that your users run.
- For single-session hosts, use VMs with at least two physical CPU cores.
- For multiple-session hosts, use VMs with at least four cores.
- Use no more than 32 cores per VM.
- Use a larger number of smaller VMs instead of a few large VMs.
- Use scaling plans to adjust your number of VMs based on user demand and schedule.
- When you select a disk type, take into account the IOPS and throughput performance limits of your VMs.
- To reduce costs, use HDDs.
- For workloads that require large amounts of storage but don't require high performance, use HDDs.
- For high-performance workloads, use SSDs.
- For production workloads that require high performance, low latency, and an SLA, use premium SSDs.

## User profiles

*Impact: Performance Efficiency, Cost Optimization*

Virtual Desktop can efficiently handle tasks that are associated with user profiles, such as managing large offline storage table files and persisting modern settings. Overall, FSLogix is the recommended user profile solution for Virtual Desktop. It's designed to roam profiles in remote computing environments. When a user signs in, the FSLogix profile container is dynamically attached to the computing environment via a virtual hard disk (VHD) and a Hyper-V virtual hard disk (VHDX). The user profile is immediately available and appears in the system like a native user profile.

Azure provides several storage solutions for housing FSLogix profile containers for use with Virtual Desktop. In most scenarios, Azure Files is the recommended storage solution for a virtual desktop environment. As long as the capacity, IOPS, and latency meet your needs, Azure Files provides excellent value. Its fully managed file shares minimize maintenance on your part. For detailed information about available storage tiers, see [Azure Files tiers](/azure/virtual-desktop/store-fslogix-profile#azure-files-tiers).

Another option for an FSLogix storage solution is Azure NetApp Files, which is an Azure-native, enterprise-class, high-performance file storage service. The product tier and capacity that you select for Azure NetApp Files affect its performance. Provisioning more capacity leads to a higher performance budget, which can result in a lower tier requirement. That situation can lead to a more optimal total cost of ownership (TCO). When you select a tier, you need to balance capacity, performance, and cost considerations. For more information about available tiers, see [Azure NetApp Files tiers](/azure/virtual-desktop/store-fslogix-profile#azure-netapp-files-tiers).

The following table provides a quick comparison of storage options for FSLogix:

| Features | Azure Files | Azure NetApp Files |
| --- | --- | --- |
| Use case | General purpose | General purpose to enterprise scale |
| Platform service | Yes, Azure-native solution | Yes, Azure-native solution |
| Regional availability | All regions | [Selected regions](https://azure.microsoft.com/explore/global-infrastructure/products-by-region/?products=netapp&regions=all&rar=true) |
| Redundancy | Locally redundant, zone-redundant, geo-redundant, or geo-zone-redundant | Locally redundant, zone-redundant with [cross-zone replication](/azure/azure-netapp-files/cross-zone-replication-introduction), or geo-redundant with [cross-region replication](/azure/azure-netapp-files/cross-region-replication-introduction) |
| Tiers | Standard (transaction optimized), premium | Standard, premium, ultra |
| Performance | Up to a maximum of 100,000 IOPS per share with 10 GBps per share at about 3-ms latency | Up to a maximum of 460,000 IOPS per volume with 4.5 GBps per volume at about 1-ms latency. For IOPS and performance details, see [Performance considerations for Azure NetApp Files](/azure/azure-netapp-files/azure-netapp-files-performance-considerations) and [Performance FAQs for Azure NetApp Files](/azure/azure-netapp-files/faq-performance#how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops). |
| Capacity | 100 TiB per share, up to 5 PiB per general purpose account | 100 TiB per volume, up to 12.5 PiB per NetApp account |
| Required infrastructure | A minimum share size of 1 GiB | A minimum capacity pool of 2 TiB, a minimum volume size of 100 GiB |
| Protocols | Network File System (NFS) 4.1 (preview), Server Message Block (SMB) 3.0, SMB 2.1, REST | [NFS 4.1](/azure/azure-netapp-files/azure-netapp-files-create-volumes), [NFS 3](/azure/azure-netapp-files/azure-netapp-files-create-volumes), [SMB 3.x, SMB 2.x](/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb), [dual-protocol](/azure/azure-netapp-files/create-volumes-dual-protocol) |

For detailed information about FSLogix storage solutions, see [Storage options for FSLogix profile containers in Azure Virtual Desktop](/azure/virtual-desktop/store-fslogix-profile).

##### Assessment question

If you use FSLogix as a user profile solution, did you consider your workload's performance, capacity, and infrastructure requirements before you selected an FSLogix storage solution?

##### Recommendations

- Use FSLogix as your user profile solution for Virtual Desktop.
- Deploy your storage solution for FSLogix profile containers in the same region as your session hosts.
- Before you select a storage solution, check that it supports the protocols that you use.
- In most scenarios, use Azure Files as your storage solution. Consider the following guidelines when you select a tier:
  - For I/O-intensive workloads that require high performance and low latency, use premium file shares.
  - For I/O workloads that are less sensitive to performance variability, use standard file shares.
  - For a pay-as-you-go billing model, use standard file shares.
- For large-scale Virtual Desktop deployments, consider using Azure NetApp Files as a storage solution. Keep the following points in mind:
  - Check that Azure NetApp Files is available in your region.
  - Consider costs and performance requirements when you select a tier and provision capacity.
  - Be aware that the capacity that you provision can affect your choice of tier.

## Applications (MSIX app attach)

*Impact: Operational Excellence*

MSIX app attach is the recommended solution for delivering applications to Virtual Desktop VMs. It offers many benefits for packaging and app management. Examples include increased security, flexibility, and control, and reduced deployment time. For example, you can use MSIX app attach to update apps without affecting user data or settings. It supports desktop and Universal Windows Platform (UWP) apps. MSIX also provides a way for you to use the Windows app installer to install and uninstall apps easily.

MSIX app attach is different from MSIX. MSIX app attach is designed specifically for supported products like Virtual Desktop. In a Virtual Desktop deployment, MSIX app attach delivers apps to session hosts within MSIX containers. These containers separate user data, the operating system, and apps. This separation improves security and increases troubleshooting efficiency. A main benefit of MSIX app attach is that it offers flexibility and control over app delivery. For instance, you can dynamically attach apps from an MSIX package to a user session.

MSIX app attach doesn't have specific dependencies on the type of storage fabric that the file share uses. As a result, the considerations for the MSIX app attach share are the same as the considerations for an FSLogix share. For those considerations, see [User profiles](#user-profiles), earlier in this article. To learn more about storage requirements, see [Storage options for FSLogix profile containers in Azure Virtual Desktop](/azure/virtual-desktop/store-fslogix-profile).

##### Assessment question

Do you use MSIX app attach to deliver applications to Virtual Desktop VMs?

##### Recommendations

- Separate the MSIX app attach storage fabric from FSLogix profile containers.
- To avoid performance bottlenecks, exclude the following files from antivirus scans:
  - *\<MSIX-app-attach-file-share\>\*.VHD*
  - *\<MSIX-app-attach-file-share\>\*.VHDX*
  - *\<MSIX-app-attach-file-share\>.CIM*
- If you use Azure Files, exclude the following locations from antivirus scans:  
  - *\\storageaccount.file.core.windows.net\share\*.VHD*
  - *\\storageaccount.file.core.windows.net\share\*.VHDX*
  - *\\storageaccount.file.core.windows.net\share\*.CIM*
- If you run a multiple-session host, run a test to see whether two or more users on the same session host can successfully run the app at the same time.
- In your disaster recovery plans for Virtual Desktop, include steps for replicating your MSIX app attach file share in your secondary failover location. Also ensure that the file share path is accessible in the secondary location. To meet this requirement, consider using a [distributed file system (DFS) namespace](/windows-server/storage/dfs-namespaces/dfs-overview).
- When you use Azure Files, check that the MSIX app attach file share contains the following permissions:

  | Azure object | Required role | Role function |
  | --- | --- | --- |
  | Session hosts (VM computer objects) | [Storage File Data SMB Share Reader](/azure/role-based-access-control/built-in-roles#storage-file-data-smb-share-reader) | Provides read access to Azure file shares over SMB |
  | Admins on file shares | [Storage File Data SMB Share Elevated Contributor](/azure/role-based-access-control/built-in-roles#storage-file-data-smb-share-elevated-contributor) | Provides read, write, and delete access and permissions to modify access control lists on files and directories in Azure file shares |

## Next steps

Now that you've looked at storage considerations, see how to integrate a Virtual Desktop workload with Azure landing zones.

> [!div class="nextstepaction"]
> [Landing zone integration](./landing-zone-integration.md)

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./assessment.md)
