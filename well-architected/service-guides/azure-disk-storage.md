---
title: Architecture Best Practices for Azure Disk Storage
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant to Azure Disk Storage.
author: roygara
ms.author: rogarana
ms.date: 8/8/2025
ms.topic: concept-article
ms.service: azure-waf
ms.subservice: waf-service-guide
products:
  - azure-disk-storage
azure.category:
  - storage
---

# Architecture best practices for Azure Disk Storage

Azure managed disks are a type of Azure Disk Storage that simplify the management of storage for Azure Virtual Machines. Managed disks are block-level storage volumes that Azure manages. They're similar to physical disks in an on-premises server, but they operate in a virtual environment. When you use a managed disk, you must specify the disk size type and configure the disk. After you configure the disk, Azure manages subsequent operations and maintenance tasks.

This article assumes that as an architect, you've reviewed the [storage options](/azure/architecture/guide/technology-choices/storage-options) and chose Azure Disk Storage as the storage service for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](../pillars.md).

This guide focuses on how to make decisions about Azure managed disks. But managed disks are a critical dependency of Azure Virtual Machines. As a prerequisite, read and implement the recommendations in [Azure Well-Architected Framework perspective on Virtual Machines and scale sets](virtual-machines.md).

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:

- Azure Disk Storage

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the features and capabilities of Azure Disk Storage. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review best practices to achieve high availability with managed disks.** [Optimize your application for high availability](/azure/virtual-machines/disks-high-availability) by considering these recommendations and how they relate to the configuration of your managed disks and virtual machines (VMs).
>
> - **Define reliability and recovery targets.** Review the [Azure service-level agreements (SLAs)](https://www.microsoft.com/licensing/docs/view/Service-Level-Agreements-SLA-for-Online-Services). The disk types that you attach to your VM affect the VM SLA. For the highest SLA, only use Azure Ultra Disk Storage, Azure Premium SSD v2, or Premium SSD disks for OS and data disks. For guidance about calculating your reliability targets, see [Recommendations for defining reliability targets](/azure/well-architected/reliability/metrics).
>
> - **Create a recovery plan.** [Evaluate data-protection features](/azure/virtual-machines/backup-and-disaster-recovery-for-azure-iaas-disks), backup and restore operations, and failover procedures. Decide whether to use Azure Backup, Azure Site Recovery, or create your own backup solution by using incremental disk snapshots or restore points. A custom backup solution increases your costs.
>
> - **Monitor potential availability problems.** Subscribe to the [Azure Service Health dashboard](https://azure.microsoft.com/status/). Use disk storage metrics in Azure Monitor to help prevent disk throttling. Manually check VMs to ensure that attached disks don't reach their storage capacity. For guidance about how to integrate these metrics into your overall workload health monitoring strategy, see [Health modeling for workloads](/azure/well-architected/design-guides/health-modeling).
>
> - **Use failure mode analysis.** Consider internal dependencies, such as the availability of virtual networks or Azure Key Vault, to help minimize points of failure.

### Configuration recommendations

| **Recommendation** | **Benefit** |
|---|---|
|Distribute [VMs and disks across multiple availability zones](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#design-considerations-for-availability-zones). Use a zone-redundant virtual machine scale set in flexible orchestration mode, or deploy VMs and disks across three availability zones.<br></br>Use [zone balancing](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#zone-balancing) to equally spread the instances across zones.| You provision VM and disk instances in physically separate locations within each Azure region. Each location is tolerant to local failures. Depending on resource availability, you might have an uneven number of instances across zones. <br></br> Zone balancing supports availability by making sure that, if one zone is down, the other zones have sufficient instances. Two instances in each zone provide a buffer during upgrades. |
| Use [Ultra Disk Storage, Premium SSD v2, and Premium SSD disks](/azure/virtual-machines/disks-high-availability#use-ultra-disks-premium-ssd-v2-or-premium-ssd). | Single-instance VMs that use Premium SSD OS disks and Ultra Disk Storage, Premium SSD v2, or Premium SSD data disks have the highest uptime SLA. |
| For maximum availability and durability, use a [zone-redundant storage](/azure/virtual-machines/disks-redundancy#zone-redundant-storage-for-managed-disks) (ZRS) disk, especially when you [share disks between VMs](/azure/virtual-machines/disks-high-availability#use-zrs-disks-when-sharing-disks-between-vms). | ZRS disks minimize the effect of a failure in an availability zone and increase recoverability from such zonal failures.<br></br>If a zone fails and your VM remains active, workloads on ZRS disks continue to run. But if an outage does affect your VM, and you want to recover disks before the outage resolves, you can force-detach your ZRS disks from the failed VM. Then the ZRS disks can attach to a different VM. <br></br> When you share a disk among multiple VMs, use a ZRS disk to prevent the shared disk from becoming a single point of failure. |
| Implement one of the [backup options](/azure/virtual-machines/backup-and-disaster-recovery-for-azure-iaas-disks). For managed solutions, use [Backup](/azure/backup/disk-backup-overview) or [Site Recovery](/azure/site-recovery/site-recovery-overview). If you need to curate your own backup solution, use [restore points](/azure/virtual-machines/create-restore-points) or [snapshots](/azure/virtual-machines/disks-incremental-snapshots). |  Identify the ideal backup option for your needs to help maximize your environment's recoverability. |
| If you manage your own snapshots, [copy them across regions by using scripts](/azure/virtual-machines/disks-copy-incremental-snapshot-across-regions). | Use scripts to simplify transferring data from one region to another.<br></br> Use this option if you can't use Site Recovery. You can still create disaster recovery backups in other regions when you use this option. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](../security/principles.md) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure Disk Storage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve your security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Limit the ability to export or import managed disks.** Use this approach to increase the security of your data. To limit export or import capabilities, you can use one of these methods:
>    - Create a custom role-based access control (RBAC) role that has the permissions necessary to import and export.
>    - Use Microsoft Entra ID authentication.
>    - Set up private links.
>    - Configure an Azure policy.
>    - Configure a network access policy.
>
>   For more information, see [Restrict managed disks from being imported or exported](/azure/virtual-machines/disks-restrict-import-export-overview).
>
> - **Take advantage of encryption options.** By default, managed disks are encrypted with server-side encryption (SSE), which helps you protect your data and meet organization and compliance commitments. You might need other configurations and options. You can:
>    - Use SSE with encryption keys that you manage.
>    - Enable [encryption at host](/azure/virtual-machines/disk-encryption#encryption-at-host---end-to-end-encryption-for-your-vm-data).
>    - Enable double encryption at rest.
>
>   For more information, see [Server-side encryption of Azure Disk Storage](/azure/virtual-machines/disk-encryption).
>
> - **Secure your shared access signature (SAS) with Microsoft Entra ID.** Microsoft Entra ID provides extra security compared to a shared key and SAS, and it's easier to use. Grant security principals only necessary permissions to perform their tasks.
>
> - **Protect secrets.** Protect secrets, such as customer-managed keys and SAS tokens. We generally don't recommend these forms of authorization. But if you use them, make sure to rotate your keys, set key expirations as early as practical, and securely store these secrets.
>
> - **Detect threats.** Enable [Microsoft Defender for Cloud](/azure/defender-for-cloud/defender-for-cloud-introduction) so that you can trigger security alerts when anomalies in activity occur. Defender for Cloud notifies subscription administrators by email. The email includes details about the suspicious activity and recommendations to investigate and remediate threats.
>
> - **Use tags and labels.** Apply tags and labels to important disks to help ensure that you apply the appropriate levels of protection to the disks.
>
> - **Harden all workload components.** Reduce extraneous surface area and tighten configurations to help reduce the likelihood of attacks. Properly secure any related resources that you use with your managed disks, such as backup recovery vaults or Azure key vaults.

### Configuration recommendations

| **Recommendation** | **Benefit** |
|---|---|
| Use [encryption at host](/azure/virtual-machines/disk-encryption#encryption-at-host---end-to-end-encryption-for-your-vm-data) for your managed disks whenever possible. | Encryption at host provides end-to-end encryption for environments that have it enabled. Encryption begins on your VM and flows through to its attached disks. |
| Apply an [Azure Resource Manager lock](/azure/storage/common/lock-account-resource) on the disk. | Lock a disk to help prevent it from being deleted so that you don't lose data. |
| Disable traffic to the public endpoints of your disk. [Create private endpoints](/azure/virtual-machines/disks-enable-private-links-for-import-export-portal) for clients that run in Azure. | Disable traffic to the public endpoints so that traffic travels over the Microsoft backbone network, which helps eliminate exposure to the public internet. |
| If possible, use Azure RBAC to limit access to resources and functions. | Use RBAC to avoid tokens or keys that could be compromised. Microsoft Entra ID authenticates the security principal, such as a user, group, managed identity, or service principal. Microsoft Entra ID returns an OAuth 2.0 token. The token is used to authorize a request against the disk service. |
| Microsoft discourages the use of SAS tokens. If you must create one, review this list of [SAS best practices](/azure/storage/common/storage-sas-overview) before you create and distribute it.<br></br>Set the expiration of SAS tokens to 60 days or less. | Best practices can help you prevent a SAS token from being leaked and quickly recover from a leak if one occurs. |
| Consider using your own encryption key, or a [customer-managed key](/azure/storage/common/customer-managed-keys-overview), to help protect the data in your managed disk. | A customer-managed key provides greater flexibility and control if you need it. For example, you can store encryption keys in Key Vault and automatically rotate them. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure Disk Storage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Understand how Azure Disk Storage is billed.** Different disk types are billed in different ways and have different features that can affect billing. To design the most cost-optimized environment, see [Understand Azure Disk Storage billing](/azure/virtual-machines/disks-understand-billing). For exact billing, find the specific pricing details and apply the appropriate settings. For more information, see [Managed disks pricing](https://azure.microsoft.com/pricing/details/managed-disks/).
>
> - **Estimate the cost of capacity and operations.** Use the [pricing calculator](https://azure.microsoft.com/pricing/calculator/) to model the costs that are associated with disk types, transactions, and capabilities. Compare the costs that are associated with various regions, account types, namespace types, and redundancy configurations.
>
> - **Choose a billing model.** Evaluate whether a [commitment-based model](/azure/virtual-machines/disks-reserved-capacity) is more cost efficient than a consumption-based model. If you don't know how much capacity you need, start with a consumption-based model, monitor capacity metrics, and evaluate your choice later.  
>
> - **Decide which features you need.** Some features, such as [snapshots](/azure/virtual-machines/disks-incremental-snapshots) or [on-demand bursting](/azure/virtual-machines/disks-enable-bursting), incur extra transaction costs, capacity costs, and other charges. For example, if you enable snapshots, you're billed for the amount of storage that each snapshot uses. When you decide which capabilities your disks need, review the pricing and billing details for those capabilities.
>
> - **Create guardrails.** Create [budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) based on subscriptions and resource groups. Use governance policies to restrict resource types, configurations, and locations. You can also use RBAC to block actions that can lead to overspending.
>
> - **Monitor costs.** to ensure that you stay within budgets, compare costs against forecasts, and see where overspending might have occurred. Use the [cost analysis](/azure/cost-management-billing/costs/quick-acm-cost-analysis) feature in the Azure portal. You also can export cost data to a storage account and use Excel or Power BI to analyze that data.
>
> - **Monitor disk resources.** Use [sample scripts](/azure/virtual-machines/linux/find-unattached-disks) to search for unattached disks.

### Configuration recommendations

| **Recommendation** | **Benefit** |
|---|---|
| Carefully select the appropriate disk types for your workloads. Understand the [available disk types](/azure/virtual-machines/disks-types) and their features before you deploy an environment. Then use the [pricing calculator](https://azure.microsoft.com/pricing/calculator/) to estimate costs. | One of the best ways to reduce costs is to plan for your requirements, and use the [pricing calculator](https://azure.microsoft.com/pricing/calculator/) to model the environment. |
| Use [reserved capacity for Premium SSD disks](/azure/cost-management-billing/reservations/understand-disk-reservations). | Reserved capacity for Premium SSDs reduces the total cost of your environment because you prepay for your capacity at a discount. |
| Assess whether the features that existing disks offer can improve performance without switching to another disk size or type. Features like [disk bursting](/azure/virtual-machines/disk-bursting) or changing [performance tiers](/azure/virtual-machines/disks-change-performance) could improve performance to levels that meet your needs. | Depending on your environment and needs, enabling features to improve your disk performance can be more cost effective than switching to a different disk type. These features incur costs but might incur less costs than different disk types. |
| Directly adjust the performance of your [Ultra Disk Storage](/azure/virtual-machines/disks-enable-ultra-ssd#adjust-the-performance-of-an-ultra-disk) and [Premium SSD v2](/azure/virtual-machines/disks-deploy-premium-v2#adjust-disk-performance) disks to fit your performance needs. | These two disk types support a set number of adjustments to the disk's performance within a 24 hour period. This setting allows your workloads to be cost efficient while meeting your performance needs. You can increase performance (increase cost) to meet higher demand and then lower performance (decrease cost) when the increase is no longer needed.<br></br>For example, a transaction-intensive database might need a large amount of input/output operations per second (IOPS) at a small size. Or a gaming application might need a large amount of IOPS but only during peak hours.  |
| Use [Live Resize](https://aka.ms/LiveResizePublicLink) for Premium SSD v2 and Ultra NVMe disks to dynamically expand capacity without downtime. | Live Resize enables cost-effective storage scaling by allowing you to start with smaller disk sizes and expand capacity based on actual demand patterns. This eliminates the traditional approach of over-provisioning storage capacity upfront, reducing costs while maintaining operational continuity. For example, an e-commerce platform can start with smaller disks and expand storage during peak seasons without database downtime or application interruptions. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals towards the operational requirements of the workload.

### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure Disk Storage.

> [!div class="checklist"]
>
> - **Create maintenance and emergency recovery plans.** Evaluate data-protection features, backup operations, and restore operations. Select backup solutions that you can use to recover from regional disasters.
>
> - **Create internal documentation.** Document your organization's standard practices. Incorporate existing Azure documentation to streamline your processes. Include documentation about attaching a disk to [Windows](/azure/virtual-machines/windows/attach-disk-ps) or [Linux](/azure/virtual-machines/linux/add-disk) VMs or expanding a disk on [Windows](/azure/virtual-machines/windows/expand-os-disk) or [Linux](/azure/virtual-machines/linux/expand-disks) VMs.
>
> - **Detect threats.** Enable [Defender for Cloud](/azure/defender-for-cloud/defender-for-cloud-introduction) so that you can trigger security alerts when anomalies in activity occur. Defender for Cloud notifies subscription administrators by email. The email includes details about the suspicious activity and recommendations to investigate and remediate threats.

### Configuration recommendations

| **Recommendation** | **Benefit** |
|---|---|
| Use Azure Monitor to [analyze metrics](/azure/virtual-machines/disks-metrics) and create alerts. | Azure Monitor provides insight about how your disks and VMs perform. Use these metrics to ensure that your performance remains optimal. |
| Review the available [backup options for managed disks](/azure/virtual-machines/backup-and-disaster-recovery-for-azure-iaas-disks). | Understand the available options so that you can select the configuration that best suits your needs. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Azure Disk Storage.

> [!div class="checklist"]
>
> - **Choose optimal disk types.** Identify the [disk types](/azure/virtual-machines/disks-types) that you need before you deploy your resources. This approach helps you maximize performance and cost efficiency. The five disk types include [Ultra Disk Storage](/azure/virtual-machines/disks-types#ultra-disks), [Premium SSD v2](/azure/virtual-machines/disks-types#premium-ssd-v2), [Premium SSD](/azure/virtual-machines/disks-types#premium-ssds), [Azure Standard SSD](/azure/virtual-machines/disks-types#standard-ssds), and [Azure Standard HDD](/azure/virtual-machines/disks-types#standard-hdds). For the highest performance, use Premium SSD for your VM's OS disk, and use Ultra Disk Storage or Premium SSD v2 for your data disks.
>
> - **Reduce the travel distance between the client and server.** Place data in regions that are closest to connecting clients, ideally in the same region. Default network configurations provide the best performance. Modify network settings only to improve security. In general, network settings don't decrease travel distance and don't improve performance.
>
> - **Collect performance data.** Monitor your disks and VMs to identify performance bottlenecks that occur from throttling. For more information, see [Storage IO metrics](/azure/virtual-machines/disks-metrics).
>
> - **Benchmark your disks.** Create a test environment and determine whether it meets your needs and expectations. For more information, see [Benchmark a disk](/azure/virtual-machines/disks-benchmarks).

### Configuration recommendations

| **Recommendation** | **Benefit** |
|---|---|
| Create disks in the same region as the VM that you attach them to. If clients from a different region don't require the same data, create a separate disk in each region. | Reduce the physical distance between VMs and their disks, services, and on-premises clients to help improve performance and reduce network latency. <br></br> This approach also reduces cost for applications that you host in Azure because bandwidth usage within a single region is free. |
| For workloads and solutions that require the lowest latency, such as e-commerce workloads or databases, use a Premium SSD OS disk and [Ultra Disk Storage](/azure/virtual-machines/disks-types#ultra-disks) or [Premium SSD v2](/azure/virtual-machines/disks-types#premium-ssd-v2) data disks. | This configuration offers the best reliability and highest SLA and performance. |
| Use [Azure metrics](/azure/virtual-machines/disks-metrics) to monitor your environment and help prevent disk throttling. | Use Azure metrics to identify disks that are being throttled and address them. Throttling leads to suboptimal performance and problems like increased latency. |
| For disks that are being throttled, evaluate whether changing to a larger disk size or changing to a more performant disk is better for your needs. <br></br> For Premium SSD disks that are being throttled, if you have short-term bursts of demand, [enable on-demand bursting](/azure/virtual-machines/disks-enable-bursting). For longer-term extended demand, [change the tier of the disk](/azure/virtual-machines/disks-change-performance) or evaluate whether [Premium SSD v2](/azure/virtual-machines/disks-types#premium-ssd-v2) or [Ultra Disk Storage](/azure/virtual-machines/disks-types#ultra-disks) disks better fit your needs. | Place applications on disks that aren't being throttled to help ensure optimal performance without increased latency. |
| Review the [expand without downtime](/azure/virtual-machines/linux/expand-disks?tabs=ubuntu#expand-without-downtime) limitations, to ensure your data disks qualify. Expanding without downtime allows you to dynamically increase your storage capacity without deallocating attached VMs. <br><br>Start with smaller disk sizes and expand based on actual demand to implement just-in-time capacity provisioning strategies. | Dynamic disk expansion eliminates downtime traditionally associated with storage expansion while enabling elastic storage scaling. <br><br> You can reduce upfront costs by avoiding overprovisioning and increasing the size of your disk if you need the additional capacity or performance. |
| When you upload a virtual hard disk (VHD), use the [Add-AzVHD Azure PowerShell](/azure/virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell) command. | The *Add-AzVHD* Azure PowerShell command automates most of the upload process to help streamline the process. |
| For existing deployments that are on-premises or in another public cloud provider, use [Azure Migrate and Modernize](/azure/migrate/migrate-services-overview). | Azure Migrate and Modernize can evaluate your deployment and provide curated suggestions for the best sizing of disks and VMs in a prospective Azure deployment. |

## Tradeoffs

You might have to make design tradeoffs if you use the approaches in the pillar checklists. Here are some examples of advantages and drawbacks.

:::image type="icon" source="../_images/trade-off.svg"::: **Disk type selection and performance versus cost**

- **Higher performance disk types:** Premium SSD v2 and Ultra Disk Storage provide the highest performance with customizable IOPS and throughput. These disk types offer higher availability with lower latency, making them ideal for mission-critical workloads. Premium SSDs provide built-in redundancy and zone-redundant storage options.

  However, Premium disk types can cost significantly more than Standard SSDs or HDDs. Ultra Disk Storage and Premium SSD v2 have limited regional availability and specific VM type requirements. For workloads with predictable, moderate performance needs, the additional cost might not justify the performance gains. Evaluate whether Standard SSD or even Standard HDD can meet your performance requirements at a lower cost.

- **Lower cost disk types:** Standard SSD and Standard HDD provide cost-effective storage for development, testing, and workloads with less demanding performance requirements. These options help reduce overall infrastructure costs and are suitable for backup storage, archival data, or applications that don't require high IOPS.

  These disk types have lower performance ceilings, higher latency, and reduced SLAs compared to premium options. Standard HDDs are particularly unsuitable for performance-sensitive workloads and can become bottlenecks in production environments.


## Azure policies

Azure provides an extensive set of built-in policies related to Azure Disk Storage and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Public network access to your managed disks is disabled.
- Backup is enabled.
- Double encryption is enabled.
- Specific disk encryption sets are used with your disks.
- Customer-managed keys are used.
- Managed disks are zone resilient.
- Notification policies for key expiration are configured.
- Autorotate for customer-managed keys is enabled.

For comprehensive governance, review the [Azure Policy built-in definitions for Azure compute](/azure/governance/policy/samples/built-in-policies) and other policies that might impact the security of the storage infrastructure.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [Azure Virtual Machines baseline architecture](/azure/architecture/virtual-machines/baseline).

## Next step

> [!div class="nextstepaction"]
> [Azure Virtual Machines baseline architecture](/azure/architecture/virtual-machines/baseline)

<!-- Updated: August 8, 2025 for Azure Update 495106 -->
