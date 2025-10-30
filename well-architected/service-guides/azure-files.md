---
title: Architecture Best Practices for Azure Files
description: See Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure Files and Azure File Sync.
author: khdownie
ms.author: kendownie
ms.date: 09/08/2025
ms.topic: concept-article
ms.service: azure-waf
ms.subservice: waf-service-guide
products:
  - azure-file-storage
azure.category:
  - storage
---

# Architecture best practices for Azure Files

Azure Files is a Microsoft file storage solution for the cloud. Azure Files provides server message block (SMB) and network file system (NFS) file shares that you can mount to clients in the cloud, on-premises, or to both. You can also use Azure File Sync to cache SMB file shares on a local Windows server and tier infrequently used files to the cloud.

This article assumes that as an architect, you've reviewed the [storage options](/azure/architecture/guide/technology-choices/storage-options) and chosen Azure Files as the storage service on which to run your workloads. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](../pillars.md).

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, workloads, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the nature of your application and the criticality of its components. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Use failure mode analysis**: Minimize points of failure by considering internal dependencies such as the availability of virtual networks, Azure Key Vault, or Azure Content Delivery Network or Azure Front Door endpoints. Failures can occur if you need credentials to access Azure Files, and the credentials go missing from Key Vault. Or you might have a failure if your workloads use an endpoint that's based on a missing content delivery network. In these cases, you might need to configure your workloads to connect to an alternative endpoint. For general information about failure mode analysis, see [Recommendations for performing failure mode analysis](/azure/well-architected/reliability/failure-mode-analysis).
>
> - **Define reliability and recovery targets**: Review the [Azure service-level agreements (SLAs)](https://www.microsoft.com/licensing/docs/view/Service-Level-Agreements-SLA-for-Online-Services). Derive the service-level objective (SLO) for the storage account. For example, the redundancy configuration that you chose might affect the SLO. Consider the effect of a regional outage, the potential for data loss, and the time required to restore access after an outage. Also consider the availability of internal dependencies that you identified as part of your failure mode analysis.
>
> - **Configure data redundancy**: For maximum durability, choose a configuration that copies data across availability zones or global regions. For maximum availability, choose a configuration that allows clients to read data from the secondary region during an outage of the primary region.
>
> - **Design applications**: [Design your applications](/azure/storage/common/geo-redundant-design) to seamlessly shift so that they read data from a secondary region if the primary region is unavailable. This design consideration only applies to geo-redundant storage (GRS) and geo-zone-redundant storage (GZRS) configurations. Design your applications to properly handle outages, which reduces downtime for customers.
>
> - **Explore features to help you meet your recovery targets**: Make files restorable so that you can recover corrupted, edited, or deleted files.
>
> - **Create a recovery plan**: Consider data protection features, backup and restore operations, or failover procedures. Prepare for potential [data loss and data inconsistencies](/azure/storage/files/files-disaster-recovery#anticipate-data-loss) and the [time and cost of failing over](/azure/storage/common/storage-disaster-recovery-guidance#the-time-and-cost-of-failing-over). For more information, see [Recommendations for designing a disaster recovery strategy](/azure/well-architected/reliability/disaster-recovery).
>
> - **Monitor potential availability problems**: Subscribe to the [Azure Service Health dashboard](https://azure.microsoft.com/status/) to monitor potential availability problems. Use storage metrics and diagnostic logs in Azure Monitor to investigate alerts.

### Configuration recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|Configure your storage account for redundancy.<br><br> For maximum availability and durability, configure your account with [zone-redundant storage (ZRS)](/azure/storage/files/files-redundancy#zone-redundant-storage), [GRS](/azure/storage/files/files-redundancy#geo-redundant-storage), or [GZRS](/azure/storage/files/files-redundancy#geo-zone-redundant-storage).<br><br> Limited Azure regions support ZRS for [standard](/azure/storage/common/redundancy-regions-zrs#standard-storage-accounts) and [SSD (premium)](/azure/storage/files/redundancy-premium-file-shares) file shares. Only standard SMB accounts support GRS and GZRS. Premium SMB shares and NFS shares don't support GRS and GZRS.<br><br> Azure Files doesn't support read-access geo-redundant storage (RA-GRS) or read-access geo-zone-redundant storage (RA-GZRS). If you configure a storage account to use RA-GRS or RA-GZRS, the file shares are configured and billed as GRS or GZRS. | Redundancy protects your data against unexpected failures. The ZRS and GZRS configuration options replicate across various availability zones and enable applications to continue reading data during an outage. For more information, see [Durability and availability by outage scenario](/azure/storage/files/files-redundancy#durability-and-availability-by-outage-scenario) and [Durability and availability parameters](/azure/storage/files/files-redundancy#durability-and-availability-parameters).|
|Before you initiate a failover or failback, check the value of the [last synchronization time](/azure/storage/common/last-sync-time-get) property to [evaluate the potential for data loss](/azure/storage/files/files-disaster-recovery#anticipate-data-loss). This recommendation applies only to GRS and GZRS configurations. |This property helps you estimate how much data you might lose if you initiate an account failover.<br><br> All data and metadata that's written before the last synchronization time is available on the secondary region, but you might lose data and metadata that's written after the last synchronization time because it's not written to the secondary region.|
|As a part of your backup and recovery strategy, [enable soft delete](/azure/storage/files/storage-files-enable-soft-delete) and [use snapshots](/azure/storage/files/storage-snapshots-files) for point-in-time restore.<br><br> You can use [Azure Backup](/azure/backup/azure-file-share-backup-overview) to back up your SMB file shares. You can also use Azure File Sync to back up on-premises SMB file shares to an Azure file share. <br><br> Azure Backup also allows you to do a vaulted backup of Azure Files to protect your data from ransomware attacks or source data loss due to a malicious actor or rogue admin. By using vaulted backup, Azure Backup copies and stores data in the Recovery Services vault. This creates an offsite copy of data that you can retain for up to 99 years. Azure Backup creates and manages the recovery points as per the schedule and retention defined in the backup policy. [Learn more](/azure/backup/azure-file-share-backup-overview?tabs=vault-standard). | Soft delete works on a file share level to protect Azure file shares against accidental deletion.<br><br> Point-in-time restore protects against accidental deletion or corruption because you can restore file shares to an earlier state. For more information, see [Data protection overview](/azure/storage/files/files-data-protection-overview).|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of your file storage configuration.

Security requirements and recommendations vary depending on whether your workload uses the SMB or NFS protocol to access your file shares. So the following sections have separate design checklists and recommendations for SMB and NFS file shares.

As a best practice, you should keep SMB and NFS file shares in separate storage accounts because they have different security requirements. Use this approach to provide your workload with strong security and high flexibility.

### Workload design checklist for SMB file shares

Start your design strategy based on the [design review checklist for Security](../security/checklist.md). Identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review the security baseline for Azure Storage**: To get started, [review the security baseline for Storage](/security/benchmark/azure/baselines/storage-security-baseline).
>
> - **Consider using network controls to restrict ingress and egress traffic**: You might be comfortable exposing your storage account to the public internet under certain conditions, like if you use identity-based authentication to grant access to file shares. But we recommend that you use network controls to grant the minimum required level of access to users and applications. For more information, see [How to approach network security for your storage account](/azure/storage/common/storage-network-security#how-to-approach-network-security-for-your-storage-account).
>
> - **Reduce the attack surface**: Use encryption in transit and prevent access over non-secure (HTTP) connections to reduce the attack surface. Require clients to send and receive data by using the latest version of the Transport Layer Security (TLS) protocol.
>
> - **Minimize the use of storage account keys**: [Identity-based authentication](/azure/storage/files/storage-files-active-directory-overview) provides superior security compared to using a storage account key. But you must use a storage account key to get full administrative control of a file share, including the ability to take ownership of a file. Grant security principals only the necessary permissions that they need to perform their tasks.
>
> - **Protect sensitive information**: Protect sensitive information, such as storage account keys and passwords. We don't recommend that you use these forms of authorization, but if you do, you should make sure to rotate, expire, and store them securely.
>
> - **Detect threats**: Enable [Microsoft Defender for Storage](/azure/storage/common/azure-defender-storage-configure) to detect potentially harmful attempts to access or exploit your Azure file shares over SMB or FileREST protocols. Subscription administrators get email alerts with details of suspicious activity and recommendations about how to investigate and remediate threats. Defender for Storage doesn't support antivirus capabilities for Azure file shares. If you use Defender for Storage, transaction-heavy file shares incur significant costs, so consider opting out of Defender for Storage for specific storage accounts.

### Configuration recommendations for SMB file shares

|Recommendation|Benefit|
|------------------------------|-----------|
|[Apply an Azure Resource Manager lock](/azure/storage/common/lock-account-resource) on the storage account. | Lock the account to prevent accidental or malicious deletion of the storage account, which can cause data loss.|
|Open TCP port 445 outbound or set up a VPN gateway or Azure ExpressRoute connection for clients outside of Azure to access the file share. | SMB 3.x is an internet-safe protocol, but you might not have the ability to change organizational or ISP policies. You can use a VPN gateway or an ExpressRoute connection as an alternative option.|
|If you open port 445, be sure to disable SMBv1 on [Windows](/windows-server/storage/file-server/troubleshoot/detect-enable-and-disable-smbv1-v2-v3) and [Linux](/azure/storage/files/files-remove-smb1-linux) clients. Azure Files doesn't support SMB 1, but you should still disable it on your clients. | SMB 1 is an outdated, inefficient, and insecure protocol. Disable it on clients to improve your security posture. |
|Consider disabling public network access to your storage account. Enable public network access only if SMB clients and services that are external to Azure require access to your storage account.<br><br> If you disable public network access,[create a private endpoint](/azure/storage/files/storage-files-networking-endpoints#create-a-private-endpoint) for your storage account. Standard data processing rates for private endpoints apply. A private endpoint doesn't block connections to the public endpoint. You should still disable public network access as previously described.<br><br> If you don't require a static IP address for your file share and want to avoid the cost of private endpoints, you can instead [restrict public endpoint access](/azure/storage/files/storage-files-networking-endpoints#restrict-public-endpoint-access) to specific virtual networks and IP addresses. | Network traffic travels over the Microsoft backbone network instead of the public internet, which eliminates risk exposure from the public internet.|
|Enable firewall rules that limit access to specific virtual networks. Start with zero access, and then methodically and incrementally provide the least amount of access required for clients and services. | Minimize the risk of creating openings for attackers.|
|When possible, use [identity-based authentication](/azure/storage/files/storage-files-active-directory-overview) with AES-256 Kerberos ticket encryption to authorize access to SMB Azure file shares. | Use identity-based authentication to decrease the possibility of an attacker using a storage account key to access file shares.|
|If you use storage account keys, [store them in Key Vault](/azure/storage/common/storage-account-keys-manage#protect-your-access-keys), and make sure to regenerate them periodically.<br><br> You can completely disallow storage account key access to the file share by removing NTLMv2 from the share's SMB security settings. But you generally shouldn't remove NTLMv2 from the share's SMB security settings because administrators still need to use the account key for some tasks. | Use Key Vault to retrieve keys at runtime instead of saving them with your application. Key Vault also makes it easy to rotate your keys without interruption to your applications. Periodically rotate the account keys to reduce the risk of exposing your data to malicious attacks.|
|In most cases, you should enable the *Secure transfer required* option on all your storage accounts to enable encryption in transit for SMB file shares.<br><br> Don't enable this option if you need to allow very old clients to access the share. If you disable secure transfer, be sure to use network controls to restrict traffic. | This setting ensures that all requests that are made against the storage account take place over secure connections (HTTPS). Any requests made over HTTP will fail.|
|[Configure your storage account](/azure/storage/common/transport-layer-security-configure-minimum-version) so that TLS 1.2 is the minimum version for clients to send and receive data. | TLS 1.2 is more secure and faster than TLS 1.0 and 1.1, which don't support modern cryptographic algorithms and cipher suites.|
|Use only the most recent supported SMB protocol version (currently 3.1.1.), and use only AES-256-GCM for SMB channel encryption.<br><br> Azure Files exposes settings that you can use to toggle the SMB protocol and make it more compatible or more secure, depending on your organization's requirements. By default, all SMB versions are allowed. However, SMB 2.1 is disallowed if you enable *Require secure transfer* because SMB 2.1 doesn't support encryption of data in transit.<br><br> If you restrict these settings to a high level of security, some clients might not be able to connect to the file share. | SMB 3.1.1, released with Windows 10, contains important security and performance updates. AES-256-GCM offers more secure channel encryption.|

### Workload design checklist for NFS file shares

> [!div class="checklist"]
>
> - **Review the security baseline for Storage**: To get started, [review the security baseline for Storage](/security/benchmark/azure/baselines/storage-security-baseline).
>
> - **Understand your organization's security requirements**: NFS Azure file shares only support Linux clients that use the NFSv4.1 protocol, with support for most features from the 4.1 protocol specification. Some security features aren't supported, such as Kerberos authentication and access control lists (ACLs).
>
> - **Use network-level security and controls to restrict ingress and egress traffic**: Identity-based authentication isn't available for NFS Azure file shares, so you must use network-level security and controls to grant the minimum required level of access to users and applications. For more information, see [How to approach network security for your storage account](/azure/storage/common/storage-network-security).

### Configuration recommendations for NFS file shares

|Recommendation|Benefit|
|------------------------------|-----------|
|[Apply a Resource Manager](/azure/storage/common/lock-account-resource) lock on the storage account. | Lock the account to prevent accidental or malicious deletion of the storage account, which might cause data loss.|
|You must open port 2049 on the clients that you want to mount your NFS share to. | Open port 2049 to let clients communicate with the NFS Azure file share.|
|NFS Azure file shares are only accessible through restricted networks. So you must [create a private endpoint](/azure/storage/files/storage-files-networking-endpoints#create-a-private-endpoint) for your storage account or [restrict public endpoint access](/azure/storage/files/storage-files-networking-endpoints#restrict-public-endpoint-access) to selected virtual networks and IP addresses. We recommend that you create a private endpoint.<br><br> We recommend configuring network-level security for NFS shares. You can also enable [encryption in transit](/azure/storage/files/encryption-in-transit-for-nfs-shares). <br><br> Standard data processing rates apply for private endpoints. If you don't require a static IP address for your file share and want to avoid the cost of private endpoints, you can restrict public endpoint access instead. | Network traffic travels over the Microsoft backbone network instead of the public internet, which eliminates risk exposure from the public internet.|
|Consider disallowing storage account key access at the storage account level. You don't need this access to mount NFS file shares. But keep in mind that full administrative control of a file share, including the ability to take ownership of a file, requires use of a storage account key. | Disallow the use of storage account keys to make your storage account more secure.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to file storage and its environment.

### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that\'s allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time. 

> [!div class="checklist"]
>
> - **Decide whether your workload requires the performance of SSD (premium) file shares or if standard HDD storage is sufficient**: Determine your storage account type and billing model based on the type of storage that you need. If you require large amounts of input/output operations per second (IOPS), extremely fast data transfer speeds, or very low latency, then you should choose SSD Azure file shares. NFS Azure file shares are only available for the SSD media tier. NFS and SMB file shares are the same price for SSD.
>
> - **Create a storage account for your file share, and choose a redundancy level**: Choose either a standard (GPv2) or premium (FileStorage) account. The redundancy level that you choose affects cost. The more redundancy, the higher the cost. Locally redundant storage (LRS) is the most affordable. GRS is only available for standard SMB file shares. Standard file shares only show transaction information at the storage account level, so we recommend that you deploy only one file share in each storage account to ensure full billing visibility.
>
> - **Understand how your bill is calculated**: Azure Files provides three different billing models: [pay-as-you-go](/azure/storage/files/understanding-billing#pay-as-you-go-model), [provisioned v2](/azure/storage/files/understanding-billing#provisioned-v2-model), and [provisioned v1](/azure/storage/files/understanding-billing#provisioned-v1-model). The pay-as-you-go model can be cost efficient because you pay only for what you use; you don't need to overprovision or deprovision storage based on performance requirements or demand fluctuations. In the pay-as-you-go model, meters track the amount of data that's stored in the account, or the capacity, and the number and type of transactions based on your usage of that data. In the provisioned models, you specify and pay for a certain amount of capacity, IOPS, and throughput up front.
>	
>	But you might find it difficult to plan storage as part of a budgeting process because end-user consumption drives cost. With the provisioned models, transactions don't affect billing, so costs are easy to predict. But you pay for the provisioned storage capacity whether you use it or not. For a detailed breakdown of how costs are calculated, see [Understand Azure Files billing](/azure/storage/files/understanding-billing).
>
> - **Estimate the cost of capacity and operations**: You can use the Azure [pricing calculator](https://azure.microsoft.com/pricing/calculator/) to model the costs associated with data storage, ingress, and egress. Compare the cost associated with various regions, account types, and redundancy configurations. For more information, see [Azure Files pricing](https://azure.microsoft.com/pricing/details/storage/files/). You can also refer to the [File share total cost of ownership checklist](/azure/storage/files/understanding-billing#file-share-total-cost-of-ownership-checklist).
>
> - **Choose the most cost-effective access tier**: Standard SMB Azure file shares offer three access tiers: *transaction optimized*, *hot*, and *cool*. All three tiers are stored on the same standard storage hardware. The main difference for these three tiers is their data at rest storage prices, which are lower in cooler tiers, and the transaction prices, which are higher in cooler tiers. For more information, see [Differences in standard tiers](/azure/storage/files/understanding-billing#differences-in-standard-tiers).
>
> - **Decide which value-added services you need**: Azure Files supports integrations with [value-added services](/azure/storage/files/understanding-billing#value-added-services) such as Backup, Azure File Sync, and Defender for Storage. These solutions have their own licensing and product costs but are often considered part of the total cost of ownership for file storage. Consider [other cost aspects](/azure/storage/files/understanding-billing#azure-file-sync) if you use Azure File Sync.
>
> - **Create guardrails**: Create [budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) based on subscriptions and resource groups. Use governance policies to restrict resource types, configurations, and locations. Additionally, use role-based access control (RBAC) to block actions that can lead to overspending.
>
> - **Monitor costs**: Ensure costs stay within budgets, compare costs against forecasts, and see where overspending occurs. You can use the [cost analysis](/azure/cost-management-billing/costs/quick-acm-cost-analysis) pane in the Azure portal to monitor costs. You can also export cost data to a storage account, and use Excel or Power BI to analyze that data.
>
> - **Monitor usage**: Continuously monitor usage patterns to detect unused or underused storage accounts and file shares. Check for unexpected increases in capacity, which might indicate that you're collecting numerous log files or soft-deleted files. Develop a strategy for deleting files or moving files to more cost-effective access tiers.

### Configuration recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|When you migrate to standard Azure file shares, we recommend that you start in the *transaction-optimized* tier during the initial migration. Transaction usage during migration isn't typically indicative of normal transaction usage. This consideration doesn't apply for SSD file shares because the provisioned billing models don't charge for transactions. | Migrating to Azure Files is a temporary, transaction-heavy workload. Optimize the price for high-transaction workloads to help reduce migration costs.|
|After you migrate your workload, if you use standard file shares, carefully choose the most cost effective access tier for your file share: *hot*, *cool*, or *transaction optimized*.<br><br> After you operate for a few days or weeks with regular usage, you can insert your transaction counts in the [pricing calculator](https://azure.microsoft.com/pricing/calculator/) to figure out which tier best suits your workload.<br><br> Most customers should choose *cool* even if they actively use the share. But you should examine each share and compare the balance of storage capacity to transactions to determine your tier. If transaction costs make up a significant percentage of your bill, the savings from using the *cool* access tier often offsets this cost and minimizes the total overall cost.<br><br> We recommend that you move standard file shares between access tiers only when necessary to optimize for changes in your workload pattern. Each move incurs transactions. For more information, see [Switching between standard tiers](/azure/storage/files/understanding-billing#switching-between-standard-tiers). | Select the appropriate access tier for standard file shares to considerably reduce your costs.|
|If you use a provisioned billing model, ensure that you provision more than enough capacity and performance for your workload but not so much that you incur unnecessary costs. We recommend overprovisioning by two to three times. You can dynamically scale SSD file shares up or down depending on your storage and input/output (IO) performance characteristics. | Overprovision SSD file shares by a reasonable amount to help maintain performance and account for future growth and performance requirements.|
|Use Azure Files reservations, also referred to as reserved instances, to precommit to storage usage and get a discount. Use reservations for production workloads or dev/test workloads with consistent footprints. For more information, see [Optimize costs with storage reservations](/azure/storage/files/files-reserve-capacity).<br><br> Reservations don't include transaction, bandwidth, data transfer, and metadata storage charges. | Three-year reservations can provide a discount up to 36% on the total cost of file storage. Reservations don't affect performance.|
|Monitor snapshot usage. Snapshots incur charges, but they're billed based on the differential storage usage of each snapshot. You pay only for the difference in each snapshot. For more information, see [Snapshots](/azure/storage/files/understanding-billing#snapshots).<br><br> Azure File Sync takes share-level and file-level snapshots as part of regular usage, which can increase your total Azure Files bill. | Differential snapshots ensure that you're not billed multiple times for storing the same data. However, you should still monitor snapshot usage to help reduce your Azure Files bill.|
|Set retention periods for the soft-delete feature, especially when you first start using it. Consider starting with a short retention period to better understand how the feature affects your bill. The minimum recommended retention period is seven days.<br><br> When you soft delete file shares, they're billed as used capacity rather than provisioned capacity. And SSD file shares are billed at the snapshot rate while in the soft-delete state. Standard file shares are billed at the regular rate while in the soft-delete state. | Set a retention period so that soft-deleted files don't pile up and increase the cost of capacity. After the configured retention period, permanently deleted data doesn't incur cost.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to your file storage configuration.

> [!div class="checklist"]
>
> - **Create maintenance and emergency recovery plans**: Consider data protection features, backup and restore operations, and failover procedures. Prepare for potential [data loss and data inconsistencies](/azure/storage/files/files-disaster-recovery#anticipate-data-loss) and the [time and cost of failing over](/azure/storage/common/storage-disaster-recovery-guidance#the-time-and-cost-of-failing-over).
>
> - **Monitor the health of your storage account**: Create [Storage insights](/azure/storage/common/storage-insights-overview) dashboards to monitor availability, performance, and resiliency metrics. Set up alerts to identify and address problems in your system before your customers notice them. Use diagnostic settings to route resource logs to an Azure Monitor Logs workspace. Then you can query logs to investigate alerts more deeply.
>
> - **Periodically review file share activity**: Share activity can change over time. Move standard file shares to cooler access tiers, or you can provision or deprovision capacity for premium shares. When you move standard file shares to a different access tier, you incur a transaction charge. Move standard file shares only when needed to reduce your monthly bill.

### Configuration recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|Use infrastructure as code (IaC) to define the details of your storage accounts in [Azure Resource Manager templates (ARM templates)](/azure/azure-monitor/logs/resource-manager-workspace), [Bicep](/azure/azure-monitor/logs/resource-manager-workspace#template-file), or [Terraform](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/log_analytics_workspace.html). | You can use your existing DevOps processes to deploy new storage accounts, and use [Azure Policy](/azure/governance/policy/overview) to enforce their configuration.|
|Use Storage insights to track the health and performance of your storage accounts. Storage insights provides a unified view of the failures, performance, availability, and capacity for all your storage accounts. | You can track the health and operation of each of your accounts. Easily create dashboards and reports that stakeholders can use to track the health of your storage accounts.|
|Use Monitor to [analyze metrics](/azure/storage/files/analyze-files-metrics), such as availability, latency, and usage, and to [create alerts](/azure/storage/files/files-monitoring-alerts). | Monitor provides a view of availability, performance, and resiliency for your file shares.|
|Consider using [Azure File Sync Arc Extension](/azure/storage/file-sync/file-sync-extension) for hybrid environments with Arc-enabled Windows Servers. | Enables file synchronization deployment across multicloud and edge environments while centralizing management through Azure. This streamlines operations for distributed manufacturing, retail, or remote office scenarios where consistent file access is required across multiple locations with simplified Azure-based management.|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for your file storage configuration.

> [!div class="checklist"]
>
> - **Plan for scale**: Understand the [scalability and performance targets](/azure/storage/files/storage-files-scale-targets) for storage accounts, Azure Files, and Azure File Sync.
>
> - **Understand your application and usage patterns to achieve predictable performance**: Determine latency sensitivity, IOPS and throughput requirements, workload duration and frequency, and workload parallelization. Use Azure Files for multi-threaded applications to help you achieve the upper performance limits of a service. If most of your requests are metadata-centric, such as createfile, openfile, closefile, queryinfo, or querydirectory, the requests create poor latency that's higher than the read and write operations. If you have this problem and you're using SSD SMB file shares, enable [Metadata caching](/azure/storage/files/smb-performance?tabs=portal#metadata-caching-for-ssd-file-shares). You can also try separating the file share into multiple file shares within the same storage account.
>
> - **Choose the optimal storage account type**: If your workload requires large amounts of IOPS, extremely fast data transfer speeds, or very low latency, then you should choose premium (FileStorage) storage accounts. You can use a standard general-purpose v2 account for most SMB file share workloads. The primary tradeoff between the two storage account types is cost versus performance.
>	
>	The provisioned share size, IOPS, egress, ingress, and single-file limits determine SSD file share performance. SSD file shares also offer [burst credits](/azure/storage/files/understanding-billing#bursting) as an insurance policy if you need to temporarily exceed a file share's baseline IOPS limit.
>
> - **Create storage accounts in the same regions as connecting clients to reduce latency**: The farther you are from the Azure Files service, the greater the latency and the more difficult to achieve performance scale limits. This consideration is especially true when you access Azure Files from on-premises environments. If possible, ensure that your storage account and your clients are co-located in the same Azure region. Optimize for on-premises clients by minimizing network latency or by using an ExpressRoute connection to extend on-premises networks into the Microsoft cloud over a private connection.
>
> - **Collect performance data**: Monitor workload performance, including [latency](/azure/storage/files/analyze-files-metrics#monitor-latency), [availability](/azure/storage/files/analyze-files-metrics#monitor-availability), and [usage](/azure/storage/files/analyze-files-metrics#monitor-utilization) metrics. [Analyze logs](/azure/storage/files/storage-files-monitoring#analyze-logs-for-azure-files) to diagnose problems such as timeouts and throttling. [Create alerts](/azure/storage/files/files-monitoring-alerts) to notify you if a file share is being throttled, about to be throttled, or experiencing high latency.
>
> - **Optimize for hybrid deployments**: If you use Azure File Sync, sync performance depends on many factors: your Windows Server and the underlying disk configuration, network bandwidth between the server and the Azure storage, file size, total dataset size, and the activity on the dataset. To measure the performance of a solution that's based on Azure File Sync, determine the number of objects, such as files and directories, that you process per second.

### Configuration recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|Enable [SMB Multichannel](/azure/storage/files/smb-performance#smb-multichannel) for SSD SMB file shares. SMB Multichannel allows an SMB 3.1.1 client to establish multiple network connections to an SMB Azure file share.<br><br> SMB Multichannel only works when the feature is enabled on both client-side (your client) and service-side (Azure). On Windows clients, SMB Multichannel is enabled by default, but you need to enable it on your storage account. | Increase throughput and IOPS while reducing the total cost of ownership. Performance benefits increase with the number of files that distribute load.|
|Enable [Metadata caching](/azure/storage/files/smb-performance?tabs=portal#metadata-caching-for-ssd-file-shares) for SSD SMB file shares. Metadata caching reduces metadata latency and raises metadata scale limits. | Improves latency consistency and available IOPS, and boosts network throughput.|
|Use the [nconnect](/azure/storage/files/nfs-performance#nconnect) client-side mount option with NFS Azure file shares on Linux clients. Nconnect enables you to use more TCP connections between the client and the Azure Files premium service for NFSv4.1. | Increase performance at scale, and reduce the total cost of ownership for NFS file shares.|
|Make sure your file share or storage account isn't [being throttled](/troubleshoot/azure/azure-storage/files/performance/files-troubleshoot-performance#cause-1-share-or-storage-account-is-being-throttled), which can result in high latency, low throughput, or low IOPS. Requests are throttled when the IOPS, ingress, or egress limits are reached.<br><br> For standard storage accounts, throttling occurs at the account level. For premium file shares, throttling usually occurs at the share level. | Avoid throttling to provide the best possible client experience.|

## Azure policies

Azure provides an extensive set of built-in policies related to Azure Files. Some of the preceding recommendations can be audited through Azure policies. For example, you can check whether:

- Only requests from secure connections, such as HTTPS, are accepted.
- Shared key authorization is disabled.
- Network firewall rules are applied to the account.
- Diagnostic settings for Azure Files are set to stream resource logs to an Azure Monitor Logs workspace.
- Public network access is disabled.
- Azure File Sync is configured with private endpoints to use private DNS zones.

For comprehensive governance, review the [Azure Policy built-in definitions for storage](/azure/governance/policy/samples/built-in-policies#storage) and other policies that might affect the security of the compute layer.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Next step

For more information, see [Azure Files documentation](/azure/storage/files/).
