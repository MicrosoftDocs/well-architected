---
title: Azure Well-Architected Framework perspective on Azure Files
description: See Azure Well-Architected Framework design considerations and configuration recommendations that are relevant to Azure Files and Azure File Sync.
author: khdownie
ms.author: kendownie
ms.date: 05/02/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-service-guide
products:
  - azure-file-storage
categories:
  - storage
---

# Azure Well-Architected Framework perspective on Azure Files

Azure Files is a Microsoft file storage solution for the cloud. Azure file shares can be mounted concurrently by cloud or on-premises deployments. You can also use Azure File Sync to cache SMB file shares on a local Windows server and tier less frequently used files to the cloud.

This article assumes that as an architect, you reviewed your [storage options](/azure/architecture/guide/technology-choices/storage-options) and chose Azure Files as the storage service on which to run your workloads. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](../pillars.md).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a _design checklist_ that presents architectural areas of concern along with design strategies.
>
> Also included are _recommendations_ on the technology capabilities that can help implement those strategies. The recommendations don't represent an exhaustive list of all configurations available for Azure Files and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environments.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md).

> [!div class="checklist"]
>
> - **Use failure mode analysis**: Minimize points of failure by considering internal dependencies such as the availability of virtual networks, Azure Key Vault, or Azure Content Delivery Network or Azure Front Door endpoints. Failures can occur if credentials required by workloads to access Azure Files go missing from Key Vault, or if workloads use an endpoint based on a content delivery network that's removed. In these cases, workloads might need to use an alternative endpoint to connect. For general information about failure mode analysis, see [Recommendations for performing failure mode analysis](/azure/well-architected/reliability/failure-mode-analysis).
>
> - **Define reliability and recovery targets**: Review the [Azure service-level agreements (SLAs)](https://www.microsoft.com/licensing/docs/view/Service-Level-Agreements-SLA-for-Online-Services). Derive the service-level objective (SLO) for the storage account. For example, the SLO might be affected by the redundancy configuration that you chose. Consider the effect of a regional outage, the potential for data loss, and the time required to restore access after an outage. Also consider the availability of any internal dependencies that you identified as part of your failure mode analysis.
>
> - **Configure data redundancy**: For maximum durability, choose a configuration that copies data across availability zones or global regions. For maximum availability, choose a configuration that allows clients to read data from the secondary region during an outage of the primary region.
>
> - **Design applications**: [Design applications](/azure/storage/common/geo-redundant-design) to seamlessly shift to reading data from the secondary region if the primary region becomes unavailable for any reason. This only applies to geo-redundant storage (GRS) and geo-zone-redundant storage (GZRS) configurations. Designing applications to handle outages reduces downtime for end users.
>
> - **Explore features to help you meet your recovery targets**: Make files restorable so that they can be recovered if they're corrupted, edited, or deleted by mistake.
>
> - **Create a recovery plan**: Consider data protection features, backup and restore operations, or failover procedures. Prepare for potential [data loss and data inconsistencies](/azure/storage/files/files-disaster-recovery#anticipate-data-loss) and the [time and cost of failing over](/azure/storage/common/storage-disaster-recovery-guidance#the-time-and-cost-of-failing-over). For more information, see [Recommendations for designing a disaster recovery strategy](/azure/well-architected/reliability/disaster-recovery).
>
> - **Monitor potential availability problems**: Subscribe to the [Azure Service Health dashboard](https://azure.microsoft.com/status/) to monitor potential availability problems. Use storage metrics in Azure Monitor and diagnostic logs to investigate alerts.

### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|Configure your storage account for redundancy.<br><br> For maximum availability and durability, configure your account with [zone-redundant storage (ZRS)](/azure/storage/files/files-redundancy#zone-redundant-storage), [geo-redundant storage (GRS)](/azure/storage/files/files-redundancy#geo-redundant-storage), or [geo-zone-redundant storage (GZRS)](/azure/storage/files/files-redundancy#geo-zone-redundant-storage).<br><br> ZRS is only available in certain Azure regions for [standard](/azure/storage/common/redundancy-regions-zrs#standard-storage-accounts) and [premium](/azure/storage/files/redundancy-premium-file-shares) file shares. GRS and GZRS are only supported for standard SMB accounts; they aren’t supported for premium SMB shares or for NFS shares.<br><br> Azure Files doesn't support read-access geo-redundant storage (RA-GRS) or read-access geo-zone-redundant storage (RA-GZRS). If a storage account is configured to use RA-GRS or RA-GZRS, the file shares will be configured and billed as GRS or GZRS. | Redundancy protects your data against unexpected failures. The ZRS and GZRS configuration options replicate across different availability zones and enable applications to continue reading data during an outage. For more information, see [Durability and availability by outage scenario](/azure/storage/files/files-redundancy#durability-and-availability-by-outage-scenario) and [Durability and availability parameters](/azure/storage/files/files-redundancy#durability-and-availability-parameters).|
|Before initiating a failover or failback, [evaluate the potential for data loss](/azure/storage/files/files-disaster-recovery#anticipate-data-loss) by checking the value of the [last synchronization time](/azure/storage/common/last-sync-time-get) property. This recommendation applies only to GRS and GZRS configurations. |This property helps you estimate how much data you might lose by initiating an account failover.<br><br> All data and metadata written before the last synchronization time is available on the secondary region, but data and metadata written after the last synchronization time might be lost because it's not written to the secondary region.|
| As a part of your backup and recovery strategy, [enable soft delete](/azure/storage/files/storage-files-enable-soft-delete) and [use snapshots](/azure/storage/files/storage-snapshots-files) for point-in-time restore.<br><br> You can use [Azure Backup](/azure/backup/azure-file-share-backup-overview?toc=%2Fazure%2Fstorage%2Ffiles%2Ftoc.json&tabs=snapshot) to back up your SMB file shares. You can also use Azure File Sync to back up on-premises SMB file shares to an Azure file share. | Soft delete works on a file share level to protect Azure file shares against accidental deletion.<br><br> Point-in-time restore protects against accidental deletion or corruption by enabling you to restore file shares to an earlier state. See [Data protection overview](/azure/storage/files/files-data-protection-overview). |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of your file storage configuration.

Security requirements and recommendations vary depending on whether your workload uses the SMB or NFS protocol to access your file shares. Therefore, we’ve provided separate design checklists and recommendations for SMB and NFS file shares.

As a best practice, you should keep SMB and NFS file shares in separate storage accounts because they have different security requirements. Although not required, this will allow you to have the strongest security while retaining the most flexibility.

### Design checklist for SMB file shares

Start your design strategy based on the [design review checklist for Security](../security/checklist.md). Identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review the security baseline for Azure Storage**: To get started, first [review the security baseline for Storage](/security/benchmark/azure/baselines/storage-security-baseline).
>
> - **Consider using network controls to restrict ingress and egress traffic**: Some SMB customers are comfortable having their storage account exposed to the public internet, for example if they’re using identity-based authentication to grant access to file shares. However, it’s usually advisable to use network controls to grant the minimal level of access required by users and applications. For more information, see [How to approach network security for your storage account](/azure/storage/common/storage-network-security#how-to-approach-network-security-for-your-storage-account).
>
> - **Reduce the attack surface**: Using encryption-in-transit and preventing access over non-secure (HTTP) connections can reduce the attack surface. Require clients to send and receive data by using the latest version of the Transport Layer Security (TLS) protocol.
>
> - **Minimize the use of storage account keys**: [Identity-based authentication](/azure/storage/files/storage-files-active-directory-overview) provides superior security over using the storage account key. However, full administrative control of a file share, including the ability to take ownership of a file, requires using the storage account key. Grant security principals only those permissions that are necessary for them to perform their tasks.
>
> - **Protect sensitive information**: Protect sensitive information such as account keys and passwords. While these forms of authorization are generally not recommended, you should make sure to rotate, expire, and store them securely.
>
> - **Detect threats**: Enable [Microsoft Defender for Storage](/azure/storage/common/azure-defender-storage-configure) to detect potentially harmful attempts to access or exploit your Azure file shares over SMB or FileREST. Security alerts are emailed to subscription administrators, with details of suspicious activity and recommendations on how to investigate and remediate threats. Microsoft Defender for Storage doesn't support antivirus capabilities for Azure file shares. Transaction-heavy file shares will incur significant costs using Microsoft Defender for Storage, so you might wish to opt-out of Microsoft Defender for Storage for specific storage accounts.

### Recommendations for SMB file shares

|Recommendation|Benefit|
|------------------------------|-----------|
|[Apply an Azure Resource Manager](/azure/storage/common/lock-account-resource) lock on the storage account. | Locking an account prevents it from being deleted and causing data loss.|
|Either open TCP port 445 outbound or set up a VPN or Azure ExpressRoute connection for clients outside of Azure to access the file share. | Although SMB 3.x is an internet-safe protocol, organizational or ISP policies might not be possible to change. The ability to use VPN or ExpressRoute gives you other options.|
|If you open port 445, be sure to disable SMBv1 on [Windows](/windows-server/storage/file-server/troubleshoot/detect-enable-and-disable-smbv1-v2-v3?toc=%2Fazure%2Fstorage%2Ffiles%2Ftoc.json&tabs=server) and [Linux](/azure/storage/files/files-remove-smb1-linux) clients. Azure Files doesn't support SMB 1, but you should still disable it on your clients. | SMB 1 is an outdated, inefficient, and insecure protocol. Disabling it on clients improves your security posture. |
|Consider disabling public network access to your storage account(s). Enable public network access only if SMB clients and services external to Azure require access to your storage account.<br><br> If you disable public network access, you’ll need to [create a private endpoint](/azure/storage/files/storage-files-networking-endpoints?tabs=azure-portal#create-a-private-endpoint) for your storage account. Standard data processing rates for private endpoint will apply.  Creating a private endpoint doesn’t block connections to the public endpoint. You should still disable public network access as described above.<br><br> If you don't require a static IP address for your file share and want to avoid the cost of private endpoint(s), you can instead [restrict public endpoint access](/azure/storage/files/storage-files-networking-endpoints?tabs=azure-portal#restrict-public-endpoint-access) to specific virtual networks and IP addresses. | Network traffic travels over the Microsoft backbone network instead of the public internet, which eliminates risk exposure from the public internet.|
|Enable firewall rules that limit access to specific virtual networks. Start with zero access, and then methodically and incrementally provide the least amount of access required for clients and services. | Minimizes the risk of creating openings for attackers.|
|When possible, authorize access to SMB Azure file shares only by using [identity-based authentication](/azure/storage/files/storage-files-active-directory-overview) with AES-256 Kerberos ticket encryption. | Using identity-based authentication decreases the possibility that the storage account key could be compromised and used to access file shares.|
|If you use storage account keys, then [store them in Azure Key Vault](/azure/storage/common/storage-account-keys-manage#protect-your-access-keys), and make sure that you regenerate them periodically.<br><br> It’s possible to completely disallow storage account key access to the file share by removing NTLMv2 from the share’s SMB security settings. However, most customers won’t want to do this because admins will still need to use the account key for some tasks. | Azure Key Vault lets you retrieve keys at runtime, instead of saving them with your application. Azure Key Vault also makes it easy to rotate your keys without interruption to your applications. Rotating the account keys periodically reduces the risk of exposing your data to malicious attacks.|
|In most cases, you should enable the **Secure transfer required** option on all your storage accounts to enable encryption-in-transit for SMB file shares.<br><br> The only reason not to do this would be if you need to allow very old clients to access the share. If you disable secure transfer, be sure to use network controls to restrict traffic. | This setting ensures that all requests made against the storage account must take place over secure connections (HTTPS). Any requests made over HTTP will fail.|
|[Configure your storage account](/azure/storage/common/transport-layer-security-configure-minimum-version) so clients can send and receive data by using the minimum version of TLS 1.2. | TLS 1.2 is more secure and faster than TLS 1.0 and 1.1, which don't support modern cryptographic algorithms and cipher suites.|
|Use only the most recent supported SMB protocol version (currently 3.1.1.) and use only AES-256-GCM for SMB channel encryption.<br><br> Azure Files exposes settings that let you toggle the SMB protocol to be more compatible or more secure, depending on your organization's requirements. By default, all SMB versions are allowed, although SMB 2.1 is disallowed if **Require secure transfer** is enabled because SMB 2.1 doesn’t support encryption in transit.<br><br> Be aware that restricting these settings to be more secure might prevent some clients from being able to connect to the file share. | SMB 3.1.1, released with Windows 10, contains important security and performance updates. AES-256-GCM offers more secure channel encryption.|

### Design checklist for NFS file shares

> [!div class="checklist"]
>
> - **Review the security baseline for Azure Storage**: To get started, first [review the security baseline for Storage](/security/benchmark/azure/baselines/storage-security-baseline).
>
> - **Understand your organization's security requirements**: NFS Azure file shares only supports Linux clients using the NFSv4.1 protocol, with support for most features from the 4.1 protocol specification. Some security features such as Kerberos authentication, ACLs, and encryption-in-transit aren't supported.  
>
> - **Use network-level security and controls to restrict ingress and egress traffic**: Identity-based authentication isn’t currently available for NFS Azure file shares, so it’s critical to use network-level security and controls to grant the minimum level of access required by users and applications. For more information, see [How to approach network security for your storage account](/azure/storage/common/storage-network-security).

### Recommendations for NFS file shares

|Recommendation|Benefit|
|------------------------------|-----------|
|[Apply an Azure Resource Manager](/azure/storage/common/lock-account-resource) lock on the storage account. | Locking an account prevents it from being deleted and causing data loss.|
|You must open port 2049 on the client(s) you want to mount your NFS share to. | Opening port 2049 allows clients to communicate with the NFS Azure file share.|
|NFS Azure file shares are only accessible via restricted networks. This means you must either [create a private endpoint](/azure/storage/files/storage-files-networking-endpoints?tabs=azure-portal#create-a-private-endpoint) for your storage account (recommended) or [restrict public endpoint access](/azure/storage/files/storage-files-networking-endpoints?tabs=azure-portal#restrict-public-endpoint-access) to selected virtual networks and IP addresses.<br><br> Configuring network-level security is mandatory for NFS shares because Azure Files doesn't currently support encryption-in-transit with the NFS protocol. You'll need to [disable](/azure/storage/files/storage-files-how-to-mount-nfs-shares?tabs=portal#disable-secure-transfer) the **Require secure transfer** setting on the storage account in order to use NFS Azure file shares.<br><br> Standard data processing rates will apply for private endpoints. If you don't require a static IP address for your file share and want to avoid the cost of private endpoint(s), you can restrict public endpoint access instead. | Network traffic travels over the Microsoft backbone network instead of the public internet, which eliminates risk exposure from the public internet.|
|Consider disallowing storage account key access at the storage account level. You don’t need it to mount NFS file shares. However, keep in mind that full administrative control of a file share, including the ability to take ownership of a file, requires using the storage account key. | Disallowing the use of storage account keys makes your storage account more secure.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget and business requirements.  

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to file storage and its environment.

### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Decide whether your workload requires the performance of premium file shares (SSD) or if standard HDD storage will be sufficient**: Answer this question first, as it will determine your storage account type and billing model. If you require large amounts of IOPS, extremely fast data transfer speeds, or very low latency, then you should choose premium Azure file shares. NFS Azure file shares are only available on premium. There’s no cost difference between NFS and SMB file shares on the premium tier.
>
> - **Create a storage account for your file share and choose a redundancy level**: Choose either standard (GPv2) or premium (FileStorage) account kind. The redundancy level you choose will affect your bill; the more redundancy, the higher the cost. Locally redundant storage (LRS) is the most affordable. Geo-redundant storage is only available for standard SMB file shares. Because standard file shares only show transaction information at the storage account level, we recommend deploying only one file share in each storage account to ensure full visibility into billing.
>
> - **Understand how your bill will be calculated**: Standard Azure file shares offer a [pay-as-you-go model](/azure/storage/files/understanding-billing#pay-as-you-go-model), while premium shares use a [provisioned model](/azure/storage/files/understanding-billing#provisioned-model) in which you specify and pay for a certain amount of capacity, IOPS, and throughput up front. In the pay-as-you-go model, meters track the amount of data stored in the account (capacity), as well the number and type of transactions based on your usage of that data. The pay-as-you-go model can be cost efficient because you pay only for what you use; you don’t need to overprovision or deprovision based on performance requirements or demand fluctuations. On the other hand, it can be difficult to plan as part of a budgeting process, because cost is driven by end-user consumption. With the provisioned model, transactions don’t affect billing, so costs are easy to predict. However, you pay for the provisioned storage capacity whether you use it or not. See [Understand Azure Files Billing](/azure/storage/files/understanding-billing) for a detailed breakdown of how costs are calculated.
>
> - **Estimate the cost of capacity and operations**: You can model the costs associated with data storage, ingress, and egress by using the Azure [pricing calculator](https://azure.microsoft.com/pricing/calculator/). Use fields to compare the cost associated with various regions, account type, and redundancy configurations. See [Azure Files pricing](https://azure.microsoft.com/pricing/details/storage/files/).
>
> - **Choose the most cost-effective access tier**: Standard SMB Azure file shares offer three access tiers: **transaction optimized**, **hot**, and **cool**. All three tiers are stored on the exact same standard storage hardware. The main difference for these three tiers is their data at-rest storage prices, which are lower in cooler tiers, and the transaction prices, which are higher in the cooler tiers. For more information, see [Differences in standard tiers](/azure/storage/files/understanding-billing#differences-in-standard-tiers).
>
> - **Decide which value-added services you need**: Azure Files supports integrations with [value-added services](/azure/storage/files/understanding-billing#value-added-services) such as Azure Backup, Azure File Sync, and Microsoft Defender for Storage. These solutions have their own licensing and product costs, but are often considered part of the total cost of ownership for file storage. If you’re using Azure File Sync, there are [additional cost aspects](/azure/storage/files/understanding-billing#azure-file-sync) to consider.
>
> - **Create guardrails**: Create [budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) based on subscriptions and resource groups. Use governance policies to restrict resource types, configurations, and locations. Additionally, use RBAC to block actions that can lead to overspending.
>
> - **Monitor costs**: Ensure costs stay within budgets, compare costs against forecasts, and see where overspending occurs. You can use the [cost analysis](/azure/cost-management-billing/costs/quick-acm-cost-analysis) pane in the Azure portal to monitor costs. You also can export cost data to a storage account and analyze that data by using Excel or Power BI.
>
> - **Monitor usage**: Continuously monitor usage patterns to detect unused or underutilized storage accounts and file shares. Watch out for unexpected increases in capacity, which might indicate that you're collecting numerous log files or soft-deleted files. Develop a strategy for deleting files or moving files to more cost-effective access tiers.

### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|When migrating to standard Azure file shares, we recommend starting in the **transaction optimized** tier during the initial migration. Transaction usage during migration isn't typically indicative of normal transaction usage. This doesn’t apply for premium file shares because the provisioned billing model doesn’t charge for transactions. | Migrating to Azure Files is a temporary, transaction-heavy workload. Optimizing the price for high-transaction workloads helps reduce migration costs.|
|When migration is complete, if using standard file shares, carefully choose the most cost-effective access tier for your file share: **hot**, **cool**, or **transaction optimized**.<br><br> After you've operated for a few days or weeks with regular usage, you can plug your transaction counts into the [pricing calculator](https://azure.microsoft.com/pricing/calculator/) to figure out which tier is best suited for your workload.<br><br> Most customers will be best off choosing **cool** even if they’re actively using the share, but it’s best to check by examining each share and comparing the balance of storage capacity to transactions. Don’t worry if transaction costs make up a significant percentage of the bill; usually, the savings from using the **cool** access tier will offset this and minimize the total overall cost.<br><br> We recommend not moving standard file shares between access tiers any more than needed to optimize for changes in your workload pattern, as you’ll incur transactions for each move. For more information, see [Switching between standard tiers](/azure/storage/files/understanding-billing#switching-between-standard-tiers). | Selecting the appropriate access tier for standard file shares allows you to considerably reduce your costs.|
|If using premium shares, ensure that you’ve provisioned more than enough capacity/performance for your workload, but not so much that you’re paying unnecessary costs. We recommend overprovisioning by 2-3x. Premium file shares can be dynamically scaled up or down depending on your storage and IO performance characteristics. | Overprovisioning premium file shares by a reasonable amount helps maintain performance while accounting for future growth and performance requirements.|
|Use Azure Files Reservations (also referred to as reserved instances) to achieve a discount on storage by pre-committing to storage utilization. Use Reservations for any production workload, or dev/test workloads with consistent footprints. For more information, see [Optimize costs with storage reservations](/azure/storage/files/files-reserve-capacity).<br><br> Transaction, bandwidth, data transfer, and metadata storage charges aren't included in the Reservation. | Three-year reservations can provide a significant discount on the total cost of file storage – up to 36%. Reservations have no side effects on performance.|
|Monitor snapshot usage. Although Snapshots do incur charges, they’re always billed based on the differential storage utilization of each snapshot, meaning that you pay only for what's different in each snapshot. For details, see [Snapshots](/azure/storage/files/understanding-billing#snapshots).<br><br> Azure File Sync takes share and file-level snapshots as part of regular usage, which can contribute noticeably to the total Azure Files bill. | Differential snapshots ensure that you’re not billed multiple times for storing the same data. However, monitor snapshot usage is still a best practice that can help reduce your Azure Files bill.|
|Set retention periods for soft delete, especially when you first start using it. Consider starting with a short retention period to better understand how the feature affects your bill. The minimum recommended retention period is seven days.<br><br> Both standard and premium file shares are billed on the used capacity when soft deleted, rather than provisioned capacity. Additionally, premium file shares are billed at the snapshot rate while in the soft delete state. Standard file shares are billed at the regular rate while in the soft delete state. | Setting a retention period keeps soft-deleted files from piling up and adding to the cost of capacity. You won't be charged for data that is permanently deleted after the configured retention period.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to your file storage configuration.

> [!div class="checklist"]
>
> - **Create maintenance and emergency recovery plans**: Consider data protection features, backup and restore operations, and failover procedures. Prepare for potential [data loss and data inconsistencies](/azure/storage/files/files-disaster-recovery#anticipate-data-loss) and the [time and cost of failing over](/azure/storage/common/storage-disaster-recovery-guidance#the-time-and-cost-of-failing-over).
>
> - **Monitor the health of your storage account**: Create [Storage insights](/azure/storage/common/storage-insights-overview) dashboards to monitor availability, performance, and resilience metrics. Set up alerts to identify and address problems in your system before your customers notice them. Use diagnostic settings to route resource logs to an Azure Monitor Logs workspace. Then you can query logs to investigate alerts more deeply.
>
> - **Periodically review file share activity**: Share activity can change over time. Move standard file shares to cooler access tiers or provision/deprovision capacity for premium shares, if needed. Understand that you’ll be charged transactions for moving to a different access tier for standard file shares, so only do this when needed to reduce your monthly bill.

### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|Use infrastructure as code (IaC) to define the details of your storage accounts in [Azure Resource Manager templates (ARM templates)](/azure/azure-monitor/logs/resource-manager-workspace), [Bicep](/azure/azure-monitor/logs/resource-manager-workspace), or [Terraform](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/log_analytics_workspace.html). | You can use your existing DevOps processes to deploy new storage accounts, and use [Azure Policy](/azure/governance/policy/overview) to enforce their configuration.|
|Use Storage insights to track the health and performance of your storage accounts. Storage insights provides a unified view of the failures, performance, availability, and capacity for all your storage accounts. | You can track the health and operation of each of your accounts. Easily create dashboards and reports that stakeholders can use to track the health of your storage accounts.|
|Use Azure Monitor to [analyze metrics](/azure/storage/files/analyze-files-metrics?tabs=azure-portal) such as availability, latency, and utilization, and to [create alerts](/azure/storage/files/files-monitoring-alerts). | Azure Monitor provides a view of availability, performance, and resilience for your file shares.|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for your file storage configuration.

> [!div class="checklist"]
>
> - **Plan for scale**: Understand the [scalability and performance targets](/azure/storage/files/storage-files-scale-targets) for storage accounts and for Azure Files and Azure File Sync.
>
> - **Understand your application and usage patterns to achieve predictable performance**: Determine latency sensitivity, IOPS and throughput requirements, workload duration and frequency, and workload parallelization (if any). Azure Files is best suited for multi-threaded applications, and this will help you achieve the upper performance limits of the service. If the majority of your requests are metadata-centric (such as createfile, openfile, closefile, queryinfo, or querydirectory), then latency will be worse than that of read/write operations. If this is the case, consider separating the file share into multiple file shares within the same storage account.
>
> - **Choose the optimal storage account type**: If your workload requires large amounts of IOPS, extremely fast data transfer speeds, or very low latency, then you should choose premium (FileStorage) storage accounts. A standard general-purpose v2 account is appropriate for most SMB file share workloads. The primary tradeoff between the two storage account types is cost versus performance. Premium share performance is bound by provisioned share size (IOPS/egress/ingress) and single file limits. For details, see [Understanding provisioning for premium file shares](/azure/storage/files/understanding-billing#provisioned-model). Premium file shares also offer [burst credits](/azure/storage/files/understanding-billing#bursting) as an insurance policy if you need to temporarily exceed a premium file share's baseline IOPS limit.
>
> - **Reduce latency by creating storage accounts in the same regions as connecting clients**: The farther you are away from the Azure Files service, the slower the latency experience will be, and the more difficult it will be to achieve performance scale limits. This is especially true when accessing Azure Files from on premises. If possible, ensure that your storage account and your clients are co-located in the same Azure region. Optimize for on-premises clients by minimizing network latency or using an Azure ExpressRoute to extend on-premises networks into the Microsoft cloud over a private connection.
>
> - **Collect performance data**: Monitor workload performance, including [latency](/azure/storage/files/analyze-files-metrics?tabs=azure-portal#monitor-latency), [availability](/azure/storage/files/analyze-files-metrics?tabs=azure-portal#monitor-availability), and [utilization](/azure/storage/files/analyze-files-metrics?tabs=azure-portal#monitor-utilization) metrics. [Analyze logs](/azure/storage/files/storage-files-monitoring#analyze-logs-for-azure-files) to diagnose issues such as timeout and throttling. [Create alerts](/azure/storage/files/files-monitoring-alerts) that will notify you if a file share is being throttled, about to be throttled, or experiencing high latency.
>
> - **Optimize for hybrid deployments**: If you're using Azure File Sync, the effective sync performance depends upon a number of factors: your Windows Server and the underlying disk configuration, network bandwidth between the server and the Azure storage, file size, total dataset size, and the activity on the dataset. The performance of an Azure File Sync-based solution is best measured in the number of objects (files and directories) processed per second.

### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|Enable [SMB Multichannel](/azure/storage/files/smb-performance#smb-multichannel) for premium SMB file shares. SMB Multichannel allows an SMB 3.1.1 client to establish multiple network connections to an SMB Azure file share.<br><br> SMB Multichannel only works when the feature is enabled on both client-side (your client) and service-side (Azure). On Windows clients, SMB Multichannel is enabled by default, but you’ll need to enable it on your storage account. | SMB Multichannel can increase throughput and IOPS while reducing total cost of ownership. Performance benefits increase with the number of files distributing load.|
|Use the [nconnect](/azure/storage/files/nfs-performance#nconnect) client-side mount option with NFS Azure file shares on Linux clients. Nconnect enables you to use more TCP connections between the client and the Azure Premium Files service for NFSv4.1. | Increases performance at scale while reducing total cost of ownership for NFS file shares.|
|Make sure your file share or storage account isn’t [being throttled](/troubleshoot/azure/azure-storage/files/performance/files-troubleshoot-performance?toc=%2Fazure%2Fstorage%2Ffiles%2Ftoc.json&tabs=windows#cause-1-share-or-storage-account-is-being-throttled), which can result in high latency, low throughput, or low IOPS. Requests are throttled when the IOPS, ingress, or egress limits are reached.<br><br> For standard storage accounts, throttling occurs at the account level. For premium file shares, throttling usually occurs at the share level. | Avoiding throttling will provide the best possible client experience.|

## Azure policies

Azure provides an extensive set of built-in policies related to Azure Files. Some of the preceding recommendations can be audited through Azure policies. For example, you can check if:

- Only requests from secure connections (HTTPS) are accepted.
- Shared key authorization is disabled.
- Network firewall rules are applied to the account.
- Diagnostic settings for Azure Files are set to stream resource logs to an Azure Monitor Logs workspace.
- Public network access is disabled.
- Azure File Sync is configured with private endpoints and/or to use private DNS zones.

For comprehensive governance, review the [Azure Policy built-in definitions for Storage](/azure/governance/policy/samples/built-in-policies#storage) and other policies that might affect the security of the compute layer.

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of Azure Files.

- [Reliability](/azure/advisor/advisor-high-availability-recommendations)
- [Security](/azure/defender-for-cloud/recommendations-reference)
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations)
- [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations)

## Next step

For more information about Azure Files, see [Azure Files documentation](/azure/storage/files/).
