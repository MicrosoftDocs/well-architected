---
title: Azure Well-Architected Framework perspective on Azure Disk Storage
description: See Azure Well-Architected Framework design considerations and configuration recommendations that are relevant to Azure Disk Storage.
author: roygara
ms.author: rogarana
ms.date: 09/04/2024
ms.topic: conceptual
ms.service: azure-waf
ms.subservice: waf-service-guide
products:
  - azure-disk-storage
azure.category:
  - storage
---

# Azure Well-Architected Framework perspective on Azure Blob Storage

Azure managed disks are block-level storage volumes managed by Azure and used with Azure Virtual Machines.  Managed disks are like a physical disk in an on-premises server but, virtualized. With managed disks, all you have to do is specify the disk size, the disk type, and provision the disk. Once you provision the disk, Azure handles the rest.

This article assumes that as an architect, you've reviewed the [storage options](/azure/architecture/guide/technology-choices/storage-options), and have chosen Azure Disk Storage as the storage service on which to run your workloads. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](/azure/well-architected/service-guides/virtual-machines).

**How to use this guide**

Each section has a design checklist that presents architectural areas of concern along with design strategies.

Also included are recommendations on the technology capabilities that can help implement those strategies. The recommendations don't represent an exhaustive list of all configurations available for Azure Disk Storage and their dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environment.

## Reliability

### Design checklist

As you make design choices for Azure Disk Storage review the [Design review checklist for Reliability](/azure/well-architected/reliability/checklist).

Use failure mode analysis to minimize points of failure. Consider internal dependencies such as the availability of virtual networks or Azure Key Vault.

- Define reliability and recovery targets. Review the [Azure Service Level Agreements (SLA)](https://www.microsoft.com/licensing/docs/view/Service-Level-Agreements-SLA-for-Online-Services?lang=1). Disk Storage SLA is impacted by the disk types that you attach to your VM. For the highest SLA, only use Ultra Disks, Premium SSD v2, or Premium SSD disks for OS and data disks. You can also increase the availability of your disks by using zone-redundant storage disks. Consider the impact of a regional outage, the potential for data loss and the time required to restore access after an outage. Consider the availability of any internal dependencies that you identified as part of your failure mode analysis.
- Configure data redundancy. For maximum durability, choose a configuration that copies data across availability zones or global regions. For maximum availability, choose a configuration that allows clients to access data during an outage. 
- Create a recovery plan. Consider data protection features, backup and restore operations, or failover procedures. Decide whether to use Azure Backup, Azure Site Recovery, or to create your own backup solution using incremental disk snapshots or restore points. for potential [data loss and data inconsistencies](/azure/storage/common/storage-disaster-recovery-guidance) as well as the [time and cost of failing over](/azure/storage/common/storage-disaster-recovery-guidance). Using these backup solutions increases your costs.
- Monitor potential availability issues by subscribing to the [Azure Service Health Dashboard](https://azure.microsoft.com/status/). Use disk storage metrics in Azure Monitor to ensure your disks aren't regularly throttling. Manually check VMs to ensure attached disks aren't reaching their storage capacity. 
- Strengthen the resiliency and recoverability of your workload by implementing self-preservation and self-healing measures. Build capabilities into the solution by using infrastructure-based reliability patterns and software-based design patterns to handle component failures and transient errors. For Premium SSD managed disks, enable on-demand bursting for eligible disks. This will help prevent those disks from throttling but, will increase costs.

### Recommendations

*Use these recommendations* *to* *optimize* *reliability:*

| **Recommendation** | **Benefit** |
|---|---|
| For maximum availability and durability, use a [zone-redundant storage](/azure/storage/common/storage-redundancy) (ZRS) disk. | Both configuration options replicate across different availability zones and enable applications to continue reading data during an outage. See [Durability and availability by outage scenario](/azure/storage/common/storage-redundancy) and [Durability and availability parameters](/azure/storage/common/storage-redundancy). |
| Decide on and implement a backup solution | For managed solutions, you should use either Azure Backup or Azure Site Recovery. If you need to curate your own backup solution, use either restore points or snapshots. As a managed solution, Azure Backup is the ideal option for backing up your data. For taking existing data and creating other VMs from it, restore points is ideal, but if you only want to preserve data, incremental snapshots will be suitable.See [Backup and disaster recovery for Azure managed disks](/azure/virtual-machines/backup-and-disaster-recovery-for-azure-iaas-disks). |
| Use AV sets when creating your VMs | Increase the availability of your disk in the event of outages. |
| Shared disks allow you to continue to access data if a VM in the cluster goes down. | |
| If you're managing your own snapshots, copy them across regions on your own. | Easily transfer data from one region to another. If you can't use Azure Site Recovery, you can still use this for creating DR backups in other regions. |

## Security

*As you make design choices for* *Azure* *Disk Storage review the* [Design review checklist for Security](/azure/well-architected/security/checklist)*.*

### Design checklist

- Review the [security baseline for Storage](https://microsoft.sharepoint.com/teams/AzureStorageContent/Shared%20Documents/General/Content%20Plans/WAF/Azure%20security%20baseline%20for%20Storage).
-  **Limit** **the ability to export or import managed disks**. Limiting the ability to import or export managed disks increases the security of your data. You've a few ways to limit these capabilities. You can either create a custom RBAC role with the permissions necessary to import and export, you can use Entra ID authentication, setup private links, configure an Azure policy, or configure the network access policy. See [Restrict managed disks from being imported or exported](/azure/virtual-machines/disks-restrict-import-export-overview).
- **Leverage** **encryption options.** By default, managed disks are encrypted with server-side encryption (SSE) which protects your data and helps meet organization and compliance commitments. There are other configurations and options, if you require them. You can use SSE with encryption keys managed by you, rather than Azure, you can enable encryption at host, or you can enable double encryption at rest. See [Server-side encryption of Azure Disk Storage](/azure/virtual-machines/disk-encryption). 
- **Secure your SAS with Entra ID**. Microsoft Entra ID provides superior security and ease of use over Shared Key and shared access signature (SAS). Grant security principals only those permissions that are necessary for them to perform their tasks. 
- **Protect secrets** such as customer-managed keys and SAS tokens. These forms of authorization aren't generally recommended but if you're using them, make sure to rotate your keys, and set key expirations as early as practical, and store these secrets in secure ways.
- **Detect threats** by enabling [Microsoft Defender for Cloud](/azure/defender-for-cloud/defender-for-cloud-introduction). Security alerts are triggered when anomalies in activity occur and are sent by email to subscription administrators, with details of suspicious activity and recommendations on how to investigate and remediate threats.
- **Use tags and labels** for important disks. Applying tags and labels to important disks makes it easier to ensure you're applying the appropriate levels of protection to them.
- **Harden all workload components** by reducing extraneous surface area and tightening configurations to increase attacker cost. Properly secure any related resources being used with your managed disks, such as backup recovery vaults or Azure Key Vaults.

### Recommendations

Use these recommendations to optimize security:

| **Recommendation** | **Benefit** |
|---|---|
| Use encryption at host for your managed disks whenever possible. | Encryption at host provides end to end encryption for environments with it enabled. Encryption begins on your VM, and flows through to its attached disks. |
| Disable public network access to your managed disk | Using private links helps ensure more security for your environment. |
| Apply an [Azure Resource Manager lock](/azure/storage/common/lock-account-resource) on the disk | Locking a disk prevents it from being deleted and causing data loss. |
| Disable traffic to the public endpoints of your disk. Create private endpoints for clients that run in Azure. | Network traffic travels over the Microsoft backbone network which eliminates exposure from the public internet. |
| If possible, authorize access only by using Azure role-based access control (Azure RBAC). | With RBAC, there are no tokens or keys that could be compromised. The security principal (user, group, managed identity, or service principal) is authenticated by Microsoft Entra ID to return an OAuth 2.0 token. The token is used to authorize a request against the Disk service. |
| Microsoft discourages the use of SAS tokens. If you must create one, then review this list of [SAS best practices](/azure/storage/common/storage-sas-overview)** before you create and distribute it.** | Best practices can help you prevent a SAS token from being leaked and quickly recover from leak should one occur. |
| [Disallow Shared Key authorization](/azure/storage/common/shared-key-authorization-prevent). This disables not only account key access, but also service and account SAS tokens as those types of tokens are based on account keys. | Only secured requests that are authorized with Microsoft Entra ID will succeed. |
| Consider using your own encryption key to protect the data in your storage account. | [Customer-managed keys for Azure Storage encryption](/azure/storage/common/customer-managed-keys-overview) provides greater flexibility and control. For example, you can store encryption keys in Azure Key Vault and automatically rotate them. |
| | |

## **Cost optimization**

As you make design choices for Azure Disk Storage review the [Design review checklist for Cost Optimization](/azure/well-architected/cost-optimization/checklist).

### Design checklist

- Understand how Azure Disk Storage is billed. Different disk types are billed in different ways and have different features that can impact billing. To design the most cost optimized environment, read [Understand Azure Disk Storage billing](/azure/virtual-machines/disks-understand-billing).For exact billing, use the appropriate pricing page and apply the appropriate settings in that page. Start at the [managed disks pricing page](https://azure.microsoft.com/en-us/pricing/details/managed-disks/?cdn=disable).
- Estimate the cost of capacity and operations. You can model the costs associated with disk type, transactions (if applicable), and capabilites by using the [pricing calculator](https://azure.microsoft.com/en-us/pricing/calculator/). Use fields to compare the cost associated with various regions, account type, namespace type, and redundancy configurations.
- Choose a billing model for capacity. Evaluate whether using [a commitment-based model](/azure/storage/blobs/storage-blob-reserved-capacity) is more cost-efficient than using a consumption-based model. If you are unsure about how much capacity you'll need, you can start with a consumption-based model, monitor capacity metrics, and evaluate later.  
- Choose an account type, a redundancy level, and a default access tier. You must select a value for each of these settings when you create a storage account, and each of them impact both transaction and capacity charges. All these settings except for the account type can be changed after the account is created. 
- Decide which features you need. Some features such as snapshots and on-demand bursting incur additional transaction and capacity costs as well as other charges. As you choose which capabilities to add to your disks, make sure to review the pricing and billing sections in articles that describe those capabilities.  

For example, if you enable snapshots, you're billed for the amount of storage used by each snapshot.

* **Create guardrails**. Create [budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) based on subscriptions and resource groups. Use governance policies to restrict resource types, configurations, and locations. Additionally, use role-based access control to block actions that can lead to overspending.
* **Monitor costs** to ensure they stay within budgets, compare against forecasts, and see where overspending might have occurred. You can use the [cost analysis](/azure/cost-management-billing/costs/quick-acm-cost-analysis) pane in the Azure Portal. You also can export cost data to a storage account and analyze that data by using Excel or Power BI. 
* **Monitor** **disk resources**. Search for unattached disks using the sample script. 

### Recommendations

Use these recommendations to optimize for cost:

| **Recommendation** | **Benefit** |
|---|---|
| Carefully select the appropriate disk types for your workloads. Read about the available disk types and their features before you deploy an environment and then estimate costs using the Azure calculator. | One of the best ways to keep costs down is to plan for exactly what your requirements are, model the environment in the Azure calculator to have a cost effective deployment. |
| Use Reserved Capacity for Premium SSD disks. | Using Reserved Capacity for your Premium SSDs will lower the total cost of your environment since you'll receive a discount by pre-paying for your capacity. |
| For existing disks, assess whether the features they offer can improve performance without switching to another disk size or type. | Depending on your environment and needs, switching to a different disk type can incur more costs than enabling a feature that increases the performance of an existing disk, like disk bursting or changing performance tiers. |

## Performance efficiency

*As you make design choices for Azure* *Disk* *Storage review the* [Design review checklist for performance efficiency](/azure/well-architected/performance-efficiency/checklist)*.*

### Design checklist

* **Choose the optimal** **disk** **types**. Identifying the [disk types](/azure/virtual-machines/disks-types) you need before deploying your resources lets you maximize performance and cost efficiency. There are five disk types, Ultra Disks, Premium SSD v2, Premium SSD, Standard SSD, and Standard HDD. For the highest performance, use Premium SSD for your VM's OS disk and use either Ultra Disks or Premium SSD v2 as your data disks.
* **Reduce** **travel** **distance between** **client** **and** **server**. Place data in regions nearest to connecting clients (ideally in the same region). Default network configurations provide the best performance. Modify network settings only to improve security. In general, network settings won't decrease travel distance and won't improve performance.
* **Optimize** **the performance of** **data clients.** [Choose a data transfer tool](/azure/storage/common/storage-choose-data-transfer-solution) that is most appropriate for the data size, transfer frequency, and bandwidth of your workloads. Some tools such as [AzCopy](/azure/storage/common/storage-use-azcopy-v10) are optimized for performance and require little intervention. Consider the [Factors influencing latency](/azure/storage/blobs/storage-blobs-latency), and fine-tune performance by reviewing the performance optimization guidance that is published with each tool. 
* **Collect performance data.** Monitor your disks and VMs to identify performance bottlenecks that occur from throttling. See [Storage IO Metrics](/azure/virtual-machines/disks-metrics). 

Use these recommendations to optimize for performance:

| **Recommendation** | **Benefit** |
|---|---|
| Create disks in the same region as the VM they'll be attached to.If clients from a different region don't require the same data, then create a separate account in each region. If clients from a different region require only some data, consider using an object replication policy to asynchronously copy relevant objects to a storage account in the other region.  | Reducing the physical distance between VMs and their disks, services, and on-premises clients, can improve performance and reduce network latency.  For applications hosted in Azure, this also reduces cost as bandwidth usage within a single region is free. |
| For workloads and solutions that require the best latency, e-commerce or databases, use Premium SSD disks as your OS disk and either Ultra Disks or Premium SSD v2 disks as your data disks. | This configuration offers the highest performance as well as the greatest reliability and highest SLA. |
| Make sure your disk isn't being throttled, which leads to suboptimal performance and issues like increased latency. | Applications have the best experience when they're not on disks being throttled. |
| Add a hash character sequence (such as three-digits) as early as possible in the partition key of a blob. The partition key is the concatenation of the account name, container name, virtual directory name and blob name. If you plan to use timestamps in names, then consider adding a seconds value to the beginning of that stamp. | Using a hash code or seconds value nearest the beginning of a partition key reduces the time required to list query and read blobs. See [Partitioning](/azure/storage/blobs/storage-performance-checklist). |
| When uploading a VHD, use the [Add-AzVHD PowerShell](/azure/virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell) command. | The [Add-AzVHD PowerShell command](/azure/virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell) automates most of the upload process for you, greatly streamlining it. |

## **Operational excellence**

As you make design choices for Azure Disk Storage, review the [Design review checklist for Operational Excellence](/azure/well-architected/operational-excellence/checklist)

### Design checklist

- **Create maintenance and emergency recovery plans.** Consider data protection features, backup and restore operations. Select backup solutions that allow you to recover from regional disasters. 
- Create internal documentation on your organization's standard practices. Incorporate existing Azure documentation to streamline your processes. Including attaching a disk to [Windows](/azure/virtual-machines/windows/attach-disk-ps) or [Linux](/azure/virtual-machines/linux/add-disk?tabs=ubuntu) VMs or expanding a disk on [Windows](/azure/virtual-machines/windows/expand-os-disk) or [Linux](/azure/virtual-machines/linux/expand-disks?tabs=ubuntu) VMs.
- **Detect threats** by enabling [Microsoft Defender for Cloud](/azure/storage/common/azure-defender-storage-configure). Security alerts are triggered when anomalies in activity occur and are sent by email to subscription administrators, with details of suspicious activity and recommendations on how to investigate and remediate threats.

### Recommendations

Use these recommendations to optimize operational excellence:

| **Recommendation** | **Benefit** |
|---|---|
| Use Azure Monitor to analyze metrics and create alerts. | Azure Monitor provides insight in how your disks and VMs perform and you should use it to ensure your performance remains optimal. |

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