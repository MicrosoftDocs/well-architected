---
title: Architecture Best Practices for Azure Blob Storage
description: See Azure Well-Architected Framework design considerations and configuration recommendations that are relevant to Azure Blob Storage.
author: normesta
ms.author: normesta
ms.date: 08/18/2025
ms.topic: concept-article
ms.service: azure-waf
ms.subservice: waf-service-guide
products:
  - azure-blob-storage
azure.category:
  - storage
---

# Architecture best practices for Azure Blob Storage

Azure Blob Storage is a Microsoft object storage solution for the cloud. Blob Storage is optimized to store massive amounts of unstructured data. Unstructured data is data that doesn't adhere to a specific data model or definition, such as text or binary data.

This article assumes that as an architect, you reviewed your [storage options](/azure/architecture/guide/technology-choices/storage-options) and chose Blob Storage as the storage service on which to run your workloads. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](../pillars.md).

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md).

> [!div class="checklist"]
>
> - **Use failure mode analysis**: Minimize points of failure by considering internal dependencies such as the availability of virtual networks, Azure Key Vault, or Azure Content Delivery Network or Azure Front Door endpoints. Failures can occur if credentials required by workloads to access Blob Storage go missing from Key Vault, or if workloads use an endpoint based on a content delivery network that's removed. In these cases, workloads might need to use an alternative endpoint to connect. For general information about failure mode analysis, see [Recommendations for performing failure mode analysis](/azure/well-architected/reliability/failure-mode-analysis).
>
> - **Define reliability and recovery targets**: Review the [Azure service-level agreements (SLAs)](https://www.microsoft.com/licensing/docs/view/Service-Level-Agreements-SLA-for-Online-Services). Derive the service-level objective (SLO) for the storage account. For example, the SLO might be affected by the redundancy configuration that you chose. Consider the effect of a regional outage, the potential for data loss, and the time required to restore access after an outage. Also consider the availability of any internal dependencies that you identified as part of your failure mode analysis.
>
> - **Configure data redundancy**: For maximum durability, choose a configuration that copies data across availability zones or global regions. For maximum availability, choose a configuration that allows clients to read data from the secondary region during an outage of the primary region.
>
> - **Design applications**: [Design applications](/azure/storage/common/geo-redundant-design) to switch to reading data from the secondary region if the primary region becomes unavailable for any reason. This requires read-access geo-redundant storage (RA-GRS) or read-access geo-zone-redundant storage (RA-GZRS) configurations. Both configurations provide read-only access to your data in the secondary region. Designing applications to handle outages reduces downtime for end users.
>
> - **Explore features to help you meet your recovery targets**: Make blobs restorable so that they can be recovered if they're corrupted, edited, or deleted by mistake.
>
> - **Create a recovery plan**: Consider data protection features, backup and restore operations, or failover procedures. Prepare for potential [data loss and data inconsistencies](/azure/storage/common/storage-disaster-recovery-guidance#anticipate-data-loss-and-inconsistencies) and the [time and cost of failing over](/azure/storage/common/storage-disaster-recovery-guidance#the-time-and-cost-of-failing-over). For more information, see [Recommendations for designing a disaster recovery strategy](/azure/well-architected/reliability/disaster-recovery).
>
> - **Monitor potential availability problems**: Subscribe to the [Azure Service Health dashboard](https://azure.microsoft.com/status/) to monitor potential availability problems. Use storage metrics in Azure Monitor and diagnostic logs to investigate alerts.

### Configuration recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|Configure your account for redundancy.<br><br> For maximum availability and durability, configure your account by using [zone-redundant storage (ZRS)](/azure/storage/common/storage-redundancy#zone-redundant-storage) or [GZRS](/azure/storage/common/storage-redundancy#geo-zone-redundant-storage). | Redundancy protects your data against unexpected failures. The ZRS and GZRS configuration options replicate across different availability zones and enable applications to continue reading data during an outage. For more information, see [Durability and availability by outage scenario](/azure/storage/common/storage-redundancy#durability-and-availability-by-outage-scenario) and [Durability and availability parameters](/azure/storage/common/storage-redundancy#durability-and-availability-parameters).|
|Before initiating a failover or failback, [evaluate the potential for data loss](/azure/storage/common/storage-disaster-recovery-guidance#anticipate-data-loss-and-inconsistencies) by checking the value of the [last synchronization time](/azure/storage/common/last-sync-time-get) property. This recommendation applies only to GRS and GZRS configurations. |This property helps you estimate how much data you might lose by initiating an account failover.<br><br> All data and metadata written before the last synchronization time is available on the secondary region, but data and metadata written after the last synchronization time might be lost because it's not written to the secondary region.|
| As a part of your backup and recovery strategy, enable the [container soft delete](/azure/storage/blobs/soft-delete-container-overview), [blob soft delete](/azure/storage/blobs/soft-delete-blob-overview), [versioning](/azure/storage/blobs/versioning-overview), and [point-in-time restore](/azure/storage/blobs/point-in-time-restore-overview) options. | The soft delete option enables a storage account to recover deleted containers and blobs.<br><br> The versioning option automatically tracks changes made to blobs. This option lets you restore a blob to a previous state.<br><br>The point-in-time restore option protects against accidental blob deletion or corruption and lets you restore block blob data to an earlier state.<br><br> For more information, see [Data protection overview](/azure/storage/blobs/data-protection-overview). |
| Configure [vaulted backup for Azure Blob](/azure/backup/blob-backup-overview?tabs=vaulted-backup#how-the-azure-blobs-backup-works) as a part of your backup strategy. | Vaulted backup enables you to protect the block blob data from ransomware, other malicious attacks, or source data loss. The data is copied and stored in the Backup vault (an offsite copy of data) that can be retained for up to 10 years. If any data loss happens on the source account, you can trigger a restore to an alternate account and get access to your data.  Learn more [about the supportability for vaulted backup using Azure Backup](/azure/backup/blob-backup-support-matrix?tabs=vaulted-backup). |


## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of your Blob Storage configuration.

### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md). Identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review the security baseline for Azure Storage**: To get started, first [review the security baseline for Storage](/security/benchmark/azure/baselines/storage-security-baseline).
>
> - **Use network controls to restrict ingress and egress traffic**: Disable all public traffic to the storage account. Use account network controls to grant the minimal level of access required by users and applications. For more information, see [How to approach network security for your storage account](/azure/storage/common/storage-network-security#how-to-approach-network-security-for-your-storage-account).
>
> - **Reduce the attack surface**: Preventing anonymous access, account key access, or access over non-secure (HTTP) connections can reduce the attack surface. Require clients to send and receive data by using the latest version of the Transport Layer Security (TLS) protocol.
>
> - **Authorize access without using passwords or keys**: Microsoft Entra ID provides superior security and ease of use compared to shared keys and shared access signatures. Grant security principals only those permissions that are necessary for them to do their tasks.
>
> - **Protect sensitive information**: Protect sensitive information such as account keys and shared access signature tokens. While these forms of authorization are generally not recommended, you should make sure to rotate, expire, and store them securely.
>
> - **Enable the secure transfer required option**: Enabling this setting for all your storage accounts ensures that all requests made against the storage account must take place over secure connections. Any requests made over HTTP fail.
>
> - **Protect critical objects**: Apply [immutability policies](/azure/storage/blobs/immutable-storage-overview) to protect critical objects. Policies protect blobs that are stored for legal, compliance, or other business purposes from being modified or deleted. Configure holds for set time periods or until restrictions are lifted by an administrator.
>
> - **Detect threats**: Enable [Microsoft Defender for Storage](/azure/storage/common/azure-defender-storage-configure) to detect threats. Security alerts are triggered when anomalies in activity occur. The alerts notify subscription administrators via email with details of suspicious activity and recommendations on how to investigate and remediate threats.

### Configuration recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|[Disable anonymous read access to containers and blob](/azure/storage/blobs/anonymous-read-access-overview). | When anonymous access is allowed for a storage account, a user that has the appropriate permissions can modify a container's anonymous access setting to enable anonymous access to the data in that container. |
|[Apply an Azure Resource Manager](/azure/storage/common/lock-account-resource) lock on the storage account. | Locking an account prevents it from being deleted and causing data loss.|
|[Disable traffic to the public endpoints](/azure/storage/common/storage-network-security#change-the-default-network-access-rule) of your storage account. Create [private endpoints](/azure/storage/common/storage-private-endpoints) for clients that run in Azure. Enable the public endpoint only if clients and services external to Azure require direct access to your storage account. Enable firewall rules that [limit access to specific virtual networks](/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network). | Start with zero access and then incrementally authorize the lowest levels of access required for clients and services to minimize the risk of creating unnecessary openings for attackers.|
|Authorize access by using Azure role-based access control (RBAC). | With RBAC, there are no passwords or keys that can be compromised. The security principal (user, group, managed identity, or service principal) is authenticated by Microsoft Entra ID to return an OAuth 2.0 token. The token is used to authorize a request against the Blob Storage service.|
|[Disallow shared key authorization](/azure/storage/common/shared-key-authorization-prevent). This disables not only account key access but also service and account shared access signature tokens because they're based on account keys. | Only secured requests that are authorized with Microsoft Entra ID are permitted. |
|We recommend that you don't use an account key. If you must use account keys, then [store them in Key Vault](/azure/storage/common/storage-account-keys-manage#protect-your-access-keys), and make sure that you regenerate them periodically. | Key Vault lets you retrieve keys at runtime, instead of saving them by using your application. Key Vault also makes it easy to rotate your keys without interruption to your applications. Rotating the account keys periodically reduces the risk of exposing your data to malicious attacks.|
|We recommend that you don't use shared access signature tokens. Define [SAS expiration policy](/azure/storage/common/sas-expiration-policy) and actions to control how such out-of-policy tokens are handled by logging their use or explicitly blocking them. <br><br>If you must create one, then review this list of [shared access signature best practices](/azure/storage/common/storage-sas-overview#best-practices-when-using-sas) before you create and distribute it. | Strong governance policy helps prevent overly long-lived or misconfigured tokens that could lead to security or compliance risks.  |
|[Configure your storage account](/azure/storage/common/transport-layer-security-configure-minimum-version) so clients can send and receive data by using the minimum version of TLS 1.2. | TLS 1.2 is more secure and faster than TLS 1.0 and 1.1, which don't support modern cryptographic algorithms and cipher suites.|
|Consider using your own encryption key to protect the data in your storage account. For more information, see [Customer-managed keys for Azure Storage encryption](/azure/storage/common/customer-managed-keys-overview). | Customer-managed keys provide greater flexibility and control. For example, you can store encryption keys in Key Vault and automatically rotate them. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget and business requirements.  

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Blob Storage and its environment.

### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Identify the meters that are used to calculate your bill**: Meters are used to track the amount of data stored in the account (data capacity) and the number and type of operations that are performed to write and read data. There are also meters associated with the use of optional features such as blob index tags, blob inventory, change feed support, encryption scopes, and SSH File Transfer Protocol (SFTP) support. For more information, see [How you're charged for Blob Storage](/azure/storage/common/storage-plan-manage-costs#how-youre-charged-for-azure-blob-storage).
>
> - **Understand the price of each meter**: Make sure to use the appropriate pricing page and apply the appropriate settings in that page. For more information, see [Finding the unit price for each meter](/azure/storage/common/storage-plan-manage-costs#finding-the-unit-price-for-each-meter). Consider the number of operations associated with each price. For example, the price associated with write and read operations applies to 10,000 operations. To determine the price of an individual operation, divide the listed price by 10,000.
>
> - **Estimate the cost of capacity and operations**: You can model the costs associated with data storage, ingress, and egress by using the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/). Use fields to compare the cost associated with various regions, account types, namespace types, and redundancy configurations. For certain scenarios, you can use sample calculations and worksheets available in Microsoft documentation. For example, you can [estimate the cost of archiving data](/azure/storage/blobs/archive-cost-estimation) or [estimate the cost of using the AzCopy command to transfer blobs](/azure/storage/blobs/azcopy-cost-estimation).
>
> - **Choose a billing model for capacity**: Evaluate whether using a [commitment-based model](/azure/storage/blobs/storage-blob-reserved-capacity) is more cost-efficient than using a consumption-based model. If you're unsure about how much capacity you need, you can start with a consumption-based model, monitor the capacity metrics, and then evaluate later.
>
> - **Choose an account type, a redundancy level, and a default access tier**: You must select a value for each of these settings when you create a storage account. All the values affect transaction charges and capacity charges. All these settings except for the account type can be changed after the account is created.
>
> - **Choose the most cost-effective default access tier**: Unless a tier is specified with each blob upload, blobs infer their access tier from the default access tier setting. A change to the default access tier setting of a storage account applies to all blobs in the account for which an access tier hasn't been explicitly set. This cost could be significant if you've collected a large number of blobs. For more information about how a tier change affects each existing blob, see [Changing a blob's access tier](/azure/storage/blobs/access-tiers-overview#changing-a-blobs-access-tier).
>
> - **Upload data directly to the most cost-efficient access tier**: For example, if the default access tier setting of your account is hot, but you're uploading files for archiving purposes, specify a cooler tier as the archive or a cold tier as part of your upload operation. After uploading blobs, use lifecycle management policies to move blobs to the most cost-efficient tiers based on usage metrics such as the last accessed time. Choosing the most optimal tier up front can reduce costs. If you change the tier of a block blob that you already uploaded, then you pay the cost of writing to the initial tier when you first upload the blob, and then pay the cost of writing to the desired tier.
>
> - **Have a plan for managing the data lifecycle**: Optimize transaction and capacity costs by taking advantage of access tiers and lifecycle management. Data used less often should be placed in cooler access tiers while data that's accessed often should be placed in warmer access tiers.
>
> - **Decide which features you need**: Some features such as versioning and blob soft delete incur additional transaction and capacity costs as well as other charges. Make sure to review the pricing and billing sections in articles that describe those capabilities when you choose which capabilities to add to your account.  
>
>   For example, if you enable the blob inventory feature, you're billed for the number of objects scanned. If you use blob index tags, you're billed for the number of index tags. If you enable SFTP support, you're billed an hourly charge, even if there are no SFTP transfers. If you decide against using a feature, confirm that the feature is disabled because some features are automatically enabled when you create the account.
>
> - **Create guardrails**: Create [budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) based on subscriptions and resource groups. Use governance policies to restrict resource types, configurations, and locations. Additionally, use RBAC to block actions that can lead to overspending.
>
> - **Monitor costs**: Ensure costs stay within budgets, compare costs against forecasts, and see where overspending occurs. You can use the [cost analysis](/azure/cost-management-billing/costs/quick-acm-cost-analysis) pane in the Azure portal to monitor costs. You also can export cost data to a storage account and analyze that data by using Excel or Power BI.
>
> - **Monitor usage**: Continuously monitor usage patterns and detect unused or underutilized accounts and containers. Use [Storage insights](/azure/storage/blobs/blob-storage-monitoring-scenarios#identify-storage-accounts-with-no-or-low-use) to identity accounts with no or low use. Enable blob inventory reports, and use tools such as [Azure Databricks](/azure/storage/blobs/storage-blob-calculate-container-statistics-databricks) or [Azure Synapse Analytics](/azure/storage/blobs/storage-blob-inventory-report-analytics) and Power BI to analyze cost data. Watch out for unexpected increases in capacity, which might indicate that you're collecting numerous log files, blob versions, or soft-deleted blobs. Develop a strategy for expiring or transitioning objects to more cost-effective access tiers.Have a plan for expiring objects or moving objects to more affordable access tiers.
>
> - **Consider Azure Storage Actions for automated data lifecycle management**: [Azure Storage Actions](/azure/storage-actions/overview#supported-regions) enables automated data lifecycle management across your storage accounts with improved regional availability. This allows for consistent automated data archiving, deletion, and tier transition policies across regions while maintaining data residency compliance.
### Configuration recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|[Pack small files into larger files](/azure/storage/blobs/access-tiers-best-practices#pack-small-files-before-moving-data-to-cooler-tiers) before moving them to cooler tiers. You can use file formats such as TAR or ZIP. | Cooler tiers have higher data transfer costs. By having fewer large files, you can reduce the number of operations required to transfer data.|
|Use standard-priority rehydration when rehydrating blobs from archive storage. Use high-priority rehydration only for emergency data restoration situations. For more information, see [Rehydrate an archived blob to an online tier](/azure/storage/blobs/archive-rehydrate-to-online-tier) | High-priority rehydration from the archive tier can lead to higher-than-normal bills.|
|Reduce the cost of using resource logs by choosing the appropriate log storage location and by managing log-retention periods. If you only plan to query logs occasionally (for example, querying logs for compliance auditing), consider sending resource logs to a storage account instead of sending them to an Azure Monitor Logs workspace. You can use a serverless query solution such as Azure Synapse Analytics to analyze logs. For more information, see [Optimize cost for infrequent queries](/azure/storage/blobs/blob-storage-monitoring-scenarios#optimize-cost-for-infrequent-queries). Use lifecycle management policies to delete or archive logs. | Storing resource logs in a storage account for later analysis can be a cheaper option. Using lifecycle management policies to manage log retention in a storage account prevents large numbers of logs files building up over time, which can lead to unnecessary capacity charges.|
|If you enable versioning, use a lifecycle management policy to automatically delete old blob versions. | Every write operation to a blob creates a new version. This increases capacity costs. You can keep costs in check by removing versions that you no longer need. |
|If you enable versioning, then place blobs that are frequently overwritten into an account that doesn't have versioning enabled. | Every time a blob is overwritten, a new version is added which leads to increased storage capacity charges. To reduce capacity charges, store frequently overwritten data in a separate storage account with versioning disabled. |
|If you enable soft delete, then place blobs that are frequently overwritten into an account that doesn't have soft delete enabled. Set retention periods. Consider starting with a short retention period to better understand how the feature affects your bill. The minimum recommended retention period is seven days. | Every time a blob is overwritten, a new snapshot is created. The cause of increased capacity charges might be difficult to access because the creation of these snapshots doesn't appear in logs. To reduce capacity charges, store frequently overwritten data in a separate storage account with soft delete disabled. A retention period keeps soft-deleted blobs from piling up and adding to the cost of capacity.|
|Enable SFTP support only when it's used to transfer data. | Enabling the SFTP endpoint incurs an hourly cost. By thoughtfully disabling SFTP support, and then enabling it as needed, you can avoid passive charges from accruing in your account.|
|Disable any encryption scopes that aren't needed to avoid unnecessary charges. | Encryptions scopes incur a per month charge.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to your Blob Storage configuration.

> [!div class="checklist"]
>
> - **Create maintenance and emergency recovery plans**: Consider data protection features, backup and restore operations, and failover procedures. Prepare for potential [data loss and data inconsistencies](/azure/storage/common/storage-disaster-recovery-guidance#anticipate-data-loss-and-inconsistencies) and the [time and cost of failing over](/azure/storage/common/storage-disaster-recovery-guidance#the-time-and-cost-of-failing-over).
>
> - **Monitor the health of your storage account**: Create [Storage insights](/azure/storage/common/storage-insights-overview) dashboards to monitor availability, performance, and resilience metrics. Set up alerts to identify and address problems in your system before your customers notice them. Use diagnostic settings to route resource logs to an Azure Monitor Logs workspace. Then you can query logs to investigate alerts more deeply.
>
> - **Enable blob inventory reports**: Enable blob inventory reports to review the retention, legal hold, or encryption status of your storage account contents. You can also use blob inventory reports to understand the total data size, age, tier distribution, or other attributes of your data. Use tools such as [Azure Databricks](/azure/storage/blobs/storage-blob-calculate-container-statistics-databricks) or [Azure Synapse Analytics](/azure/storage/blobs/storage-blob-inventory-report-analytics) and Power BI to better visualize inventory data and to create reports for stakeholders.
>
> - **Set up policies that delete blobs or move them to cost-efficient access tiers**: Create a lifecycle management policy with an initial set of conditions. Policy runs automatically delete or set the access tier of blobs based on the conditions you define. Periodically analyze container use by using Monitor metrics and blob inventory reports so that you can refine conditions to optimize cost efficiency.
>
> - **Ensure policy consistency across regions**. When data is stored globally, have data lifecycle management strategies that meet data residency requirements. Take advantage of the native automation capabilities that enable  consistent storage policies across regions.

### Configuration recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|Use infrastructure as code (IaC) to define the details of your storage accounts in [Azure Resource Manager templates (ARM templates)](/azure/azure-monitor/logs/resource-manager-workspace), [Bicep](/azure/azure-monitor/logs/resource-manager-workspace), or [Terraform](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/log_analytics_workspace.html). | You can use your existing DevOps processes to deploy new storage accounts, and use [Azure Policy](/azure/governance/policy/overview) to enforce their configuration.|
|Use Storage insights to track the health and performance of your storage accounts. Storage insights provides a unified view of the failures, performance, availability, and capacity for all your storage accounts. | You can track the health and operation of each of your accounts. Easily create dashboards and reports that stakeholders can use to track the health of your storage accounts.|
|Take advantage of regional automation capabilities with [Azure Storage Actions](/azure/storage-actions/overview) for unified data lifecycle management that automatically segments data based on access patterns and delete expired content according to retention policies while meeting data residency requirements. | Enables automated storage management and lifecycle policies across more geographic regions, reducing manual operational overhead for global organizations.|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for your Blob Storage configuration.

> [!div class="checklist"]
>
> - **Plan for scale**: Understand the scale targets for storage accounts.
>
> - **Choose the optimal storage account type**: If your workload requires high transaction rates, smaller objects, and a consistently low transaction latency, then consider using premium block blob storage accounts. A standard general-purpose v2 account is most appropriate in most cases.  
>
> - **Reduce travel distance between the client and server**: Place data in regions nearest to connecting clients (ideally in the same region). Optimize for clients in regions far away by using object replication or a content delivery network. Default network configurations provide the best performance. Modify network settings only to improve security. In general, network settings don't decrease travel distance and don't improve performance.
>
> - **Choose an efficient naming scheme**: Decrease the latency of listing, list, query, and read operations by using hash tag prefixes nearest the beginning of the blob partition key (account, container, virtual directory, or blob name). This scheme benefits mostly accounts that have a flat namespace.
>
> - **Optimize the performance of data clients**: [Choose a data transfer tool](/azure/storage/common/storage-choose-data-transfer-solution) that's most appropriate for the data size, transfer frequency, and bandwidth of your workloads. Some tools such as [AzCopy](/azure/storage/common/storage-use-azcopy-v10) are optimized for performance and require little intervention. Consider the [factors that influence latency](/azure/storage/blobs/storage-blobs-latency#factors-influencing-latency), and fine-tune performance by reviewing the performance optimization guidance that's published with each tool.
>
> - **Optimize the performance of custom code**: Consider using Storage SDKs instead of creating your own wrappers for blob REST operations. Azure SDKs are optimized for performance and provide mechanisms to fine-tune performance. Before creating an application, review the [performance and scalability checklist for Blob Storage](/azure/storage/blobs/storage-performance-checklist). Consider using [query acceleration](/azure/storage/blobs/data-lake-storage-query-acceleration) to filter out unwanted data during the storage request and keep clients from needlessly transferring data across the network.
>
> - **Collect performance data**: Monitor your storage account to identify performance bottlenecks that occur from throttling. For more information, see [Monitoring your storage service with Monitor Storage insights](/azure/storage/common/storage-insights-overview#view-from-azure-monitor). Use both metrics and logs. Metrics provide numbers such as throttling errors. Logs describe activity. If you see throttling metrics, you can use logs to identity which clients are receiving throttling errors. For more information, see [Auditing data plane operations](/azure/storage/blobs/blob-storage-monitoring-scenarios#auditing-data-plane-operations).

### Configuration recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|Provision storage accounts in the same region where dependent resources are placed. For applications that aren't hosted on Azure, such as mobile device apps or on-premises enterprise services, locate the storage account in a region nearer to those clients. For more information, see [Azure geographies](https://azure.microsoft.com/explore/global-infrastructure/geographies/#overview).<br><br>If clients from a different region don't require the same data, then create a separate account in each region.<br><br>If clients from a different region require only some data, consider using an object-replication policy to asynchronously copy relevant objects to a storage account in the other region. | Reducing the physical distance between the storage account and VMs, services, and on-premises clients can improve performance and reduce network latency. Reducing the physical distance also reduces cost for applications hosted in Azure because bandwidth usage within a single region is free.|
|For broad consumption by web clients (streaming video, audio, or static website content), consider using a content delivery network through Azure Front Door. | Content is delivered to clients faster because it uses the Microsoft global edge network with hundreds of global and local points of presence around the world.|
|Add a hash character sequence (such as three digits) as early as possible in the partition key of a blob. The partition key is the account name, container name, virtual directory name, and blob name. If you plan to use timestamps in names, then consider adding a seconds value to the beginning of that stamp. For more information, see [Partitioning](/azure/storage/blobs/storage-performance-checklist#partitioning).| Using a hash code or seconds value nearest the beginning of a partition key reduces the time required to list query and read blobs. |
|When uploading blobs or blocks, use a blob or block size that's greater than 256 KiB. | Blob or block sizes above 256 KiB takes advantage of performance enhancements in the platform made specifically for larger blobs and block sizes. |

## Tradeoffs

You might have to make design tradeoffs if you use the approaches in the pillar checklists. Here are some examples of advantages and drawbacks.

:::image type="icon" source="../_images/trade-off.svg"::: **Access tiers and cost optimization**

- **Cooler access tiers:** Moving data to cooler access tiers (cool, cold, or archive) can reduce storage costs, especially for infrequently accessed data. Archive tier offers the low storage cost but requires rehydration to access data, which takes time and incurs additional costs.

  Consider the disadvantages of cooler tiers, including higher access costs and retrieval times. Archive tier can take significant time for standard-priority rehydration. The cost of frequent tier changes can exceed storage savings if access patterns are unpredictable. For critical data that needs immediate access, hot tier provides fastest access but at higher storage costs. Evaluate your access patterns carefully to avoid unnecessary costs from frequent tier transitions and early delete penalties.

- **Lifecycle management policies:** Automated lifecycle policies can optimize costs by moving data to appropriate tiers based on age or access patterns. These policies reduce manual operational overhead and ensure consistent cost optimization across your storage accounts.

  As a disadvantage, overly aggressive lifecycle policies might move frequently accessed data to cooler tiers, resulting in higher access costs and slower performance. Policies that are too conservative might keep data in expensive tiers longer than necessary. Monitor your access patterns and adjust policies based on actual usage data rather than assumptions.

:::image type="icon" source="../_images/trade-off.svg"::: **Data redundancy and regional distribution**

A robust redundancy strategy ensures data durability and availability but involves cost and complexity tradeoffs. Geo-redundant storage (GRS) and geo-zone-redundant storage (GZRS) provide protection against regional outages but cost significantly more than locally redundant storage (LRS). Zone-redundant storage (ZRS) offers a middle ground with protection against availability zone failures within a region.

  Consider the disadvantages of higher redundancy levels. GRS and GZRS require data synchronization across regions, which can introduce slight delays in data consistency. The cost can be higher than LRS. For applications that can tolerate some data loss or have alternative backup strategies, LRS might provide sufficient protection at lower cost. Evaluate your recovery time objectives (RTO) and recovery point objectives (RPO) to determine the appropriate redundancy level.

## Azure policies

Azure provides an extensive set of built-in policies related to Blob Storage and its dependencies. Some of the preceding recommendations can be audited through Azure policies. For example, you can check if:

- Anonymous public read access to containers and blobs isn't enabled.
- Diagnostic settings for Blob Storage are set to stream resource logs to an Azure Monitor Logs workspace.
- Only requests from secure connections (HTTPS) are accepted.
- A shared access signature expiration policy is enabled.
- Cross-tenant object replication is disabled.
- Shared key authorization is disabled.
- Network firewall rules are applied to the account.

For comprehensive governance, review the [Azure Policy built-in definitions for Storage](/azure/governance/policy/samples/built-in-policies#storage) and other policies that might affect the security of the compute layer.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Next step

For more information about Blob Storage, see [Blob Storage documentation](/azure/storage/blobs/).

<!-- Updated: August 18, 2025 for Azure Update 498759 -->
