---
title: Azure Well-Architected Framework perspective on Azure Disk Storage
description: See Azure Well-Architected Framework design considerations and configuration recommendations that are relevant to Azure Disk Storage.
author: roygara
ms.author: rogarana
ms.date: 09/17/2024
ms.topic: conceptual
ms.service: azure-waf
ms.subservice: waf-service-guide
products:
  - azure-disk-storage
azure.category:
  - storage
---

# Azure Well-Architected Framework perspective on Azure Disk Storage

Azure managed disks are block-level storage volumes managed by Azure and used with Azure Virtual Machines. Managed disks are like a physical disk in an on-premises server but, virtualized. With managed disks, all you have to do is specify the disk size, the disk type, and provision the disk. Once you provision the disk, Azure handles the rest.

This article assumes that as an architect, you've reviewed the [storage options](/azure/architecture/guide/technology-choices/storage-options), and have chosen Azure Disk Storage as the storage service on which to run your workloads. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](/azure/well-architected/pillars).

**How to use this guide**

> [!IMPORTANT]
> Each section has a design checklist that presents architectural areas of concern along with design strategies.
> 
> Also included are recommendations on the technology capabilities that can help implement those strategies. The recommendations don't represent an exhaustive list of all configurations available for Azure Disk Storage and their dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environment.

## Reliability

### Design checklist

As you make design choices for Azure Disk Storage review the [Design review checklist for Reliability](/azure/well-architected/reliability/checklist).

> [!div class="checklist"]
>
> - **Use failure mode analysis**: Minimize points of failure by considering internal dependencies such as the availability of virtual networks or Azure Key Vault.
>
> - **Define reliability and recovery targets**: Review the [Azure Service Level Agreements (SLA)](https://www.microsoft.com/licensing/docs/view/Service-Level-Agreements-SLA-for-Online-Services?lang=1). Disk Storage SLA is impacted by the disk types that you attach to your VM. For the highest SLA, only use Ultra Disks, Premium SSD v2, or Premium SSD disks for OS and data disks. You can also increase the availability of your disks by using zone-redundant storage disks. Consider the impact of a regional outage, the potential for data loss, and the time required to restore access after an outage. Consider the availability of any internal dependencies that you identified as part of your failure mode analysis.
>
> - **Configure data redundancy**: For maximum durability, choose a configuration that copies data across availability zones or global regions. For maximum availability, choose a configuration that allows clients to access data during an outage.
>
> - **Create a recovery plan**: [Evaluate data protection features](/azure/virtual-machines/backup-and-disaster-recovery-for-azure-iaas-disks), backup and restore operations, or failover procedures. Decide whether to use Azure Backup, Azure Site Recovery, or to create your own backup solution using incremental disk snapshots or restore points. Using these backup solutions increases your costs.
>
> - **Monitor potential availability issues**: Subscribe to the [Azure Service Health Dashboard](https://azure.microsoft.com/status/). Use disk storage metrics in Azure Monitor to ensure your disks aren't regularly throttling. Manually check VMs to ensure attached disks aren't reaching their storage capacity.
>
> - **Strengthen the resiliency and recoverability of your workload**: Implement self-preservation and self-healing measures. Build capabilities into the solution by using infrastructure-based reliability patterns and software-based design patterns to handle component failures and transient errors. For Premium SSD managed disks, [enable on-demand bursting](/azure/virtual-machines/disks-enable-bursting) for eligible disks. This will help prevent those disks from throttling but increase costs.
>
> - **Review the best practices for achieving high availability with managed disks**: [Evaluate the configurations](/azure/virtual-machines/disks-high-availability) that best match the type of environment you have or would like to deploy. These recommendations help ensure a highly available configuration for your scenario.

### Recommendations

*Use these recommendations to optimize reliability:*

| **Recommendation** | **Benefit** |
|---|---|
| For maximum availability and durability, use a [zone-redundant storage](/azure/virtual-machines/disks-redundancy#zone-redundant-storage-for-managed-disks) (ZRS) disk. | A ZRS disk lets you recover from failures in availability zones. If a zone went down and your virtual machine (VM) wasn't affected, then your workloads continue running. But if your VM was affected by an outage and you want to recover before it's resolved, you can force detach your ZRS disks from the failed VM, freeing them to attach to another VM. |
| Implement one of the [available backup options](/azure/virtual-machines/backup-and-disaster-recovery-for-azure-iaas-disks). For managed solutions, use either [Azure Backup](/azure/backup/disk-backup-overview) or [Azure Site Recovery](/azure/site-recovery/site-recovery-overview). If you need to curate your own backup solution, use either [restore points](/azure/virtual-machines/create-restore-points) or [snapshots](/azure/virtual-machines/disks-incremental-snapshots?tabs=azure-cli). |  Identifying the ideal backup option for your needs lets you maximize your environments reliability. |
| Use [Availability sets](/azure/virtual-machines/availability-set-overview) when creating your VMs or disks. | Availability sets place resources in different [fault domains](/azure/virtual-machines/availability-set-overview?branch=main#how-do-availability-sets-work) for better reliability, reducing the chance of correlated failures bringing down your environment. |
| [Shared disks](/azure/virtual-machines/disks-shared) allow you to attach an individual managed disk to multiple VMs in a cluster simultaneously. | Shared disks let you continue to access data if a VM in your cluster goes down. |
| If you're managing your own snapshots, [copy them across regions](/azure/virtual-machines/disks-copy-incremental-snapshot-across-regions?tabs=azure-cli). | Easily transfer data from one region to another. If you can't use Azure Site Recovery, you can still use copy snapshots across regions to create disaster recovery backups in other regions. |

## Security

As you make design choices for Azure Disk Storage review the [Design review checklist for Security](/azure/well-architected/security/checklist).

### Design checklist

> [!div class="checklist"]
>
> - Review the [security baseline for Storage](/azure/well-architected/security/checklist).
>
> - **Limit the ability to export or import managed disks**. Limiting the ability to import or export managed disks increases the security of your data. You've a few ways to limit these capabilities. You can either create a custom RBAC role with the permissions necessary to import and export, you can use Entra ID authentication, setup private links, configure an Azure policy, or configure the network access policy. See [Restrict managed disks from being imported or exported](/azure/virtual-machines/disks-restrict-import-export-overview).
>
> - **Leverage encryption options.** By default, managed disks are encrypted with server-side encryption (SSE) which protects your data and helps meet organization and compliance commitments. There are other configurations and options, if you require them. You can use SSE with encryption keys managed by you, rather than Azure, you can enable [encryption at host](/azure/virtual-machines/disk-encryption?branch=main#encryption-at-host---end-to-end-encryption-for-your-vm-data), or you can enable double encryption at rest. See [Server-side encryption of Azure Disk Storage](/azure/virtual-machines/disk-encryption).
>
> - **Secure your SAS with Entra ID**: Microsoft Entra ID provides superior security and ease of use over Shared Key and shared access signature (SAS). Grant security principals only those permissions that are necessary for them to perform their tasks.
>
> - **Protect secrets** such as customer-managed keys and SAS tokens. These forms of authorization aren't generally recommended but if you're using them, make sure to rotate your keys, and set key expirations as early as practical, and store these secrets in secure ways.
>
> - **Detect threats**: Enable [Microsoft Defender for Cloud](/azure/defender-for-cloud/defender-for-cloud-introduction). Security alerts are triggered when anomalies in activity occur and are sent by email to subscription administrators, with details of suspicious activity and recommendations on how to investigate and remediate threats.
>
> - **Use tags and labels** for important disks. Applying tags and labels to important disks makes it easier to ensure you're applying the appropriate levels of protection to them.
>
> - **Harden all workload components**: Reduce extraneous surface area and tightening configurations to increase attacker cost. Properly secure any related resources being used with your managed disks, such as backup recovery vaults or Azure Key Vaults.

### Recommendations

Use these recommendations to optimize security:

| **Recommendation** | **Benefit** |
|---|---|
| Use [encryption at host](/azure/virtual-machines/disk-encryption?branch=main#encryption-at-host---end-to-end-encryption-for-your-vm-data) for your managed disks whenever possible. | [Encryption at host](/azure/virtual-machines/disk-encryption?branch=main#encryption-at-host---end-to-end-encryption-for-your-vm-data) provides end to end encryption for environments with it enabled. Encryption begins on your VM, and flows through to its attached disks. |
| Disable public network access to your managed disk | Using private links helps ensure more security for your environment. |
| Apply an [Azure Resource Manager lock](/azure/storage/common/lock-account-resource) on the disk | Locking a disk prevents it from being deleted and causing data loss. |
| Disable traffic to the public endpoints of your disk. [Create private endpoints](/azure/virtual-machines/disks-enable-private-links-for-import-export-portal) for clients that run in Azure. | Disabling traffic to the public endpoints causes traffic to travel over the Microsoft backbone network, which eliminates exposure from the public internet. |
| If possible, limit access to resources and functions by using Azure role-based access control (Azure RBAC). | With RBAC, there are no tokens or keys that could be compromised. The security principal (user, group, managed identity, or service principal) is authenticated by Microsoft Entra ID to return an OAuth 2.0 token. The token is used to authorize a request against the Disk service. |
| Microsoft discourages the use of SAS tokens. If you must create one, review this list of [SAS best practices](/azure/storage/common/storage-sas-overview) before you create and distribute it. | Best practices can help you prevent a SAS token from being leaked and quickly recover from leak should one occur. |
| [Disallow Shared Key authorization](/azure/storage/common/shared-key-authorization-prevent). This disables not only account key access, but also service and account SAS tokens as those types of tokens are based on account keys. | Only secured requests that are authorized with Microsoft Entra ID succeeds. |
| Consider using your own encryption key ([Customer-managed key](/azure/storage/common/customer-managed-keys-overview)) to protect the data in your managed disk. | A customer-managed key provides greater flexibility and control, if you need it. For example, you can store encryption keys in Azure Key Vault and automatically rotate them. |

## Cost optimization

As you make design choices for Azure Disk Storage review the [Design review checklist for Cost Optimization](/azure/well-architected/cost-optimization/checklist).

### Design checklist

> [!div class="checklist"]
>
> - **Understand how Azure Disk Storage is billed**: Different disk types are billed in different ways and have different features that can impact billing. To design the most cost optimized environment, read [Understand Azure Disk Storage billing](/azure/virtual-machines/disks-understand-billing). For exact billing, use the appropriate pricing page and apply the appropriate settings in that page. Start at the [managed disks pricing page](https://azure.microsoft.com/pricing/details/managed-disks/?cdn=disable).
>
> - **Estimate the cost of capacity and operations**: Model the costs associated with disk type, transactions (if applicable), and capabilites by using the [pricing calculator](https://azure.microsoft.com/pricing/calculator/). Use fields to compare the cost associated with various regions, account type, namespace type, and redundancy configurations.
>
> - **Choose a billing model**: Evaluate whether using [a commitment-based model](/azure/virtual-machines/disks-reserved-capacity) is more cost-efficient than using a consumption-based model. If you are unsure about how much capacity you'll need, start with a consumption-based model, monitor capacity metrics, and evaluate later.  
>
>
> - **Decide which features you need**: Some features such as [snapshots](/azure/virtual-machines/disks-incremental-snapshots) or [on-demand bursting](/azure/virtual-machines/disks-enable-bursting) incur additional transaction and capacity costs as well as other charges. For example, if you enable snapshots, you're billed for the amount of storage used by each snapshot. As you decide which capabilities your disks need, review the pricing and billing sections in articles that describe those capabilities.
>
> - **Create guardrails**: Create [budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) based on subscriptions and resource groups. Use governance policies to restrict resource types, configurations, and locations. Additionally, use role-based access control to block actions that can lead to overspending.
>
> - **Monitor costs** to ensure you stay within budgets, compare against forecasts, and see where overspending might have occurred. Use the [cost analysis](/azure/cost-management-billing/costs/quick-acm-cost-analysis) pane in the Azure Portal. You also can export cost data to a storage account and analyze that data by using Excel or Power BI.
>
> - **Monitor disk resources**: Search for unattached disks using the [sample scripts](/azure/virtual-machines/linux/find-unattached-disks).

### Recommendations

Use these recommendations to optimize for cost:

| **Recommendation** | **Benefit** |
|---|---|
| Carefully select the appropriate disk types for your workloads. Read about the [available disk types](/azure/virtual-machines/disks-types) and their features before you deploy an environment and then estimate costs using the [pricing calculator](https://azure.microsoft.com/pricing/calculator/). | One of the best ways to keep costs down is to plan for exactly what your requirements are, model the environment in the [pricing calculator](https://azure.microsoft.com/pricing/calculator/) to have a cost effective deployment. |
| Use [Reserved Capacity for Premium SSD disks](/azure/cost-management-billing/reservations/understand-disk-reservations?toc=%2Fazure%2Fvirtual-machines%2Ftoc.json). | Using Reserved Capacity for your Premium SSDs lowers the total cost of your environment since you receive a discount by prepaying for your capacity. |
| For existing disks, assess whether the features they offer can improve performance without switching to another disk size or type. Features, like [disk bursting](/azure/virtual-machines/disk-bursting?branch=main) or changing [performance tiers](/azure/virtual-machines/disks-change-performance?branch=main), can improve performance to your necessary levels. | Depending on your environment and needs, switching to a different disk type to achieve required performance levels, can incur more costs than enabling a feature that increases the performance of an existing disk, like [disk bursting](/azure/virtual-machines/disk-bursting?branch=main) or changing [performance tiers](/azure/virtual-machines/disks-change-performance?branch=main). These features also incur costs, but may incur less costs. |

## Performance efficiency

As you make design choices for Azure Disk Storage review the [Design review checklist for performance efficiency](/azure/well-architected/performance-efficiency/checklist).

### Design checklist

> [!div class="checklist"]
>
> - **Choose the optimal disk types**: Identifying the [disk types](/azure/virtual-machines/disks-types) you need before deploying your resources lets you maximize performance and cost efficiency. There are five disk types, [Ultra Disks](/azure/virtual-machines/disks-types#ultra-disks), [Premium SSD v2](/azure/virtual-machines/disks-types#premium-ssd-v2), [Premium SSD](/azure/virtual-machines/disks-types#premium-ssds), [Standard SSD](/azure/virtual-machines/disks-types#standard-ssds), and [Standard HDD](/azure/virtual-machines/disks-types#standard-hdds). For the highest performance, use Premium SSD for your VM's OS disk and use either Ultra Disks or Premium SSD v2 as your data disks.
>
> - **Reduce travel distance between client and server**: Place data in regions nearest to connecting clients (ideally in the same region). Default network configurations provide the best performance. Modify network settings only to improve security. In general, network settings won't decrease travel distance and won't improve performance.
>
> - **Collect performance data**: Monitor your disks and VMs to identify performance bottlenecks that occur from throttling. See [Storage IO Metrics](/azure/virtual-machines/disks-metrics).
>
> - **Benchmark your disks**: Create a test environment and use the [Benchmark a disk](/azure/virtual-machines/disks-benchmarks) to determine whether it meeting your needs and expectations.

### Recommendations

Use these recommendations to optimize for performance:

| **Recommendation** | **Benefit** |
|---|---|
| Create disks in the same region as the VM they'll be attached to. If clients from a different region don't require the same data, then create a separate disk in each region. | Reducing the physical distance between VMs and their disks, services, and on-premises clients, can improve performance and reduce network latency.  For applications hosted in Azure, this also reduces cost as bandwidth usage within a single region is free. |
| For workloads and solutions that require the best latency, e-commerce, or databases, use Premium SSD disks as your OS disk and either [Ultra Disks](/azure/virtual-machines/disks-types#ultra-disks) or [Premium SSD v2 disks](/azure/virtual-machines/disks-types#premium-ssd-v2) as your data disks. | This configuration offers the highest performance and the greatest reliability and highest SLA. |
| Use [Azure Metrics](/azure/virtual-machines/disks-metrics) to monitor your environment and ensure that your disks aren't being throttled. | Throttling which leads to suboptimal performance and issues like increased latency. Using Azure Metrics lets you identify disks that are being throttled. |
| To keep applications operating at their best, ensure they're on disks which aren't being throttled. For disks that are being throttled, evaluate whether changing to a larger disk size or changing to a more performant disk is better for your needs. For Premium SSD disks that are experiencing throttling, if you see short-term bursts of demand, [enable on-demand bursting](/azure/virtual-machines/disks-enable-bursting?tabs=azure-portal), for longer-term extended demand, [change the tier of the disk](/azure/virtual-machines/disks-change-performance) or evaluate if Premium SSD v2 or Ultra Disks better fit your needs. | Having applications on disks that aren't being throttled ensures they're operating at their best, without suboptimal performance or increased latency. |
| When uploading a VHD, use the [Add-AzVHD PowerShell](/azure/virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell) command. | The [Add-AzVHD PowerShell command](/azure/virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell) automates most of the upload process for you, greatly streamlining it. |
| For existing deployments that are either on-premises or in another public cloud provider, use [Azure Migrate](/azure/migrate/migrate-services-overview). | Azure Migrate can evaluate your deployment for you and provide curated suggestions for the best sizing of disks and VMs in a prospective Azure deployment. |

## **Operational excellence**

As you make design choices for Azure Disk Storage, review the [Design review checklist for Operational Excellence](/azure/well-architected/operational-excellence/checklist)

### Design checklist

> [!div class="checklist"]
>
> - **Create maintenance and emergency recovery plans**: Evaluate data protection features, backup and restore operations. Select backup solutions that allow you to recover from regional disasters.
>
> - **Create internal documentation**: Document your organization's standard practices. Incorporate existing Azure documentation to streamline your processes. Including attaching a disk to [Windows](/azure/virtual-machines/windows/attach-disk-ps) or [Linux](/azure/virtual-machines/linux/add-disk?tabs=ubuntu) VMs or expanding a disk on [Windows](/azure/virtual-machines/windows/expand-os-disk) or [Linux](/azure/virtual-machines/linux/expand-disks?tabs=ubuntu) VMs.
>
> - **Detect threats**: Enable [Microsoft Defender for Cloud](/azure/storage/common/azure-defender-storage-configure). Security alerts are triggered when anomalies in activity occur and are sent by email to subscription administrators, with details of suspicious activity and recommendations on how to investigate and remediate threats.

### Recommendations

Use these recommendations to optimize operational excellence:

| **Recommendation** | **Benefit** |
|---|---|
| Use Azure Monitor to [analyze metrics](/azure/virtual-machines/disks-metrics) and create alerts. | Azure Monitor provides insight in how your disks and VMs perform and you should use it to ensure your performance remains optimal. |
| Review the available [backup options for managed disks](/azure/virtual-machines/backup-and-disaster-recovery-for-azure-iaas-disks) | Knowing the available options allows you to select the configuration that best suits your needs. |

## Azure policies

Azure provides an extensive set of built-in policies related to Azure Disk Storage and its dependencies. Some of the preceding recommendations can be audited through Azure Policies. For example, you can check if:

- Public network access to your managed disks is disabled
- Azure Backup is enabled
- Double encryption is enabled
- Specific disk encryption sets are used with your disks
- Customer-managed keys are being used
- Managed disks are zone resilient
- Look up policy that notifies you in advance of a key expiring.
- Autorotate for customer-managed keys

For comprehensive governance, review the [Azure Policy built-in definitions for Azure Compute](/azure/governance/policy/samples/built-in-policies) and other policies that might impact the security of the Azure Disk Storage.

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your Azure Disk Storage.

- [Reliability](/azure/advisor/advisor-reference-cost-recommendations)

- [Security](/azure/defender-for-cloud/recommendations-reference)

- [Cost Optimization](/azure/advisor/advisor-reference-cost-recommendations)

- [Performance](/azure/advisor/advisor-reference-performance-recommendations)

- [Operational excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations)