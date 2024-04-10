---
title: Azure Well-Architected Framework perspective on Azure Blob Storage
description: See Azure Well-Architected Framework design considerations and configuration recommendations that are relevant to Azure Blob Storage.
author: normesta
ms.author: normesta
ms.date: 04/10/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-service-guide
products:
  - azure-blob-storage
categories:
  - storage
---

# Azure Well-Architected Framework perspective on Azure Blob Storage

Azure Blob Storage is Microsoft's object storage solution for the cloud. Blob Storage is optimized for storing massive amounts of unstructured data. Unstructured data is data that doesn't adhere to a particular data model or definition, such as text or binary data.

This article assumes that as an architect, you've reviewed the [storage options](/azure/architecture/guide/technology-choices/storage-options), and have chosen Azure Blob Storage as the storage service on which to run your workloads. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](../pillars.md).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a _design checklist_ that presents architectural areas of concern along with design strategies.
>
> Also included are _recommendations_ on the technology capabilities that can help implement those strategies. The recommendations don't represent an exhaustive list of all configurations available for Azure Blob Storage and their dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environment.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Design checklist

Start your design strategy based on the [**design review checklist for Reliability**](../reliability/checklist.md).

> [!div class="checklist"]
>
> - **Use failure mode analysis** to minimize points of failure. Consider internal dependencies such as the availability of virtual networks, Azure Key Vault, or Azure CDN or Azure Front Door endpoints. For example, failures occur if credentials required by workloads to access Blob Storage go missing from Azure Key Vault or if workloads use an endpoint based on a CDN, and that CDN is removed, then workloads might need to connect by using an alternative endpoint. For general information about failure mode analysis, see [Recommendations for performing failure mode analysis](/azure/well-architected/reliability/failure-mode-analysis).
>
> - **Define reliability and recovery targets.** Review the [Azure Service Level Agreements (SLA)](https://www.microsoft.com/licensing/docs/view/Service-Level-Agreements-SLA-for-Online-Services). Derive the Service Level Objective (SLO) for the storage account. For example, SLO might be impacted by the redundancy configuration that you choose. Consider the impact of a regional outage, the potential for data loss and the time required to restore access after an outage. Consider the availability of any internal dependencies that you identified as part of your failure mode analysis.
>
> - **Configure data redundancy.** For maximum durability, choose a configuration that copies data across availability zones or global regions. For maximum availability, choose a configuration that allows clients to read data from the secondary region during an outage of the primary region.
>
> - [Design applications](/azure/storage/common/geo-redundant-design?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&bc=%2Fazure%2Fstorage%2Fblobs%2Fbreadcrumb%2Ftoc.json) to seamlessly shift to reading data from the secondary region if the primary region becomes unavailable for any reason (Applies only to GRS and GZRS configurations). Designing applications to handle outages reduces downtime for end users.
>
> - **Explore features that can help meet your recovery targets.** Make blobs restorable so that they can recovered if they become corrupt or are edited or deleted by mistake.
>
> - **Create a recovery plan.** Consider data protection features, backup and restore operations, or failover procedures. Prepare for potential [data loss and data](/azure/storage/common/storage-disaster-recovery-guidance#anticipate-data-loss-and-inconsistencies) inconsistencies as well as the [time and cost of failing over](/azure/storage/common/storage-disaster-recovery-guidance#the-time-and-cost-of-failing-over). For general guidance, see [Recommendations for designing a disaster recovery strategy](/azure/well-architected/reliability/disaster-recovery).
>
> - **Monitor potential availability issues** by subscribing to the [Azure Service Health Dashboard](https://azure.microsoft.com/status/). Use storage metrics in Azure Monitor and diagnostic logs to investigate alerts.

### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|Configure your account for redundancy.<br><br>For maximum availability and durability, configure your account with [zone-redundant storage (ZRS)](/azure/storage/common/storage-redundancy?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&bc=%2Fazure%2Fstorage%2Fblobs%2Fbreadcrumb%2Ftoc.json#zone-redundant-storage) or [geo-zone-redundant storage (GZRS)](/azure/storage/common/storage-redundancy?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&bc=%2Fazure%2Fstorage%2Fblobs%2Fbreadcrumb%2Ftoc.json#geo-zone-redundant-storage). | Redundancy protects your data against unexpected failures. The ZRS and GZRS configuration options replicate across different availability zones and enable applications to continue reading data during an outage. See [Durability and availability by outage scenario and Durability and availability parameters](/azure/storage/common/storage-redundancy?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&bc=%2Fazure%2Fstorage%2Fblobs%2Fbreadcrumb%2Ftoc.json).|
|Prior to initiating a failover or failback, [evaluate the potential for data loss](/azure/storage/common/storage-disaster-recovery-guidance#anticipate-data-loss-and-inconsistencies) by checking the value of the [Last Sync Time](/azure/storage/common/last-sync-time-get?tabs=azure-powershell) property. Applies only to GRS and GZRS configurations. |This property helps you to estimate how much data you'll lose by initiating an account failover.<br><br>All data and metadata written prior to the last sync time is available on the secondary, while data and metadata written after the last sync time might not have been written to the secondary and might be lost.|
| As a part of your backup and recovery strategy, enable [container soft delete](/azure/storage/blobs/soft-delete-container-overview), [blob soft delete](/azure/storage/blobs/soft-delete-blob-overview), [versioning](/azure/storage/blobs/versioning-overview), and [point-in-time restore](/azure/storage/blobs/point-in-time-restore-overview). | Soft delete enables a storage account to recover deleted containers and blobs.<br><br>The versioning feature automatically tracks changes made to blobs. This gives you the ability to restore a blob to a previous state.<br><br>Point-in-time restore protects against accidental deletion or corruption by enabling you to restore block blob data to an earlier state.<br><br>See [Data protection overview](/azure/storage/blobs/data-protection-overview). |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of your Azure Blob Storage configuration.

### Design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md). Identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - Review the [security baseline for Storage](/security/benchmark/azure/baselines/storage-security-baseline).
>
> - **Use network controls to restrict ingress and egress traffic.** Disable all public traffic  to the storage account. Use a methodical approach to grant the minimal access required by users, applications, and services. Use account network controls to grant the minimal level of access required by users and applications. See [How to approach network security for your storage account](/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&bc=%2Fazure%2Fstorage%2Fblobs%2Fbreadcrumb%2Ftoc.json&tabs=azure-portal#how-to-approach-network-security-for-your-storage-account).
>
> - **Reduce the attack surface** by preventing anonymous access, account key access, or access over non-secure (HTTP) connections. Require that clients send and receive data by using the latest version of the Transport Layer Security (TLS) protocol.
>
> - **Authorize access without using passwords or keys.** Microsoft Entra ID provides superior security and ease of use over Shared Key and shared access signature (SAS). Grant security principals only those permissions that are necessary for them to perform their tasks.
>
> - **Protect secrets** such as account keys and SAS tokens. While these forms of authorization are generally not recommended, you should make sure to rotate, expire, and store them in secure ways.
>
> - **Enable the Secure transfer required** option on all your storage accounts. This setting ensures that all requests made against the storage account must take place over secure connections. Any requests made over HTTP will fail.
>
> - **Protect critical objects**  by applying [immutability policies](/azure/storage/blobs/immutable-storage-overview). Policies protect blobs that are stored for legal, compliance, or other business purposes from being modified or deleted. Configure holds for set time periods or until restriction is lifted by an administrator.
>
> - **Detect threats** by enabling [Microsoft Defender for Storage](/azure/storage/common/azure-defender-storage-configure?tabs=enable-subscription). Security alerts are triggered when anomalies in activity occur and are sent by email to subscription administrators, with details of suspicious activity and recommendations on how to investigate and remediate threats.

### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|[Disable anonymous read access to containers and blob](/azure/storage/blobs/anonymous-read-access-overview). | When anonymous access is allowed for a storage account, a user with the appropriate permissions  can modify a container's anonymous access setting to enable anonymous access to the data in that container. |
|[Apply an Azure Resource Manager](/azure/storage/common/lock-account-resource?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&bc=%2Fazure%2Fstorage%2Fblobs%2Fbreadcrumb%2Ftoc.json&tabs=portal) lock on the storage account. | Locking an account prevents it from being deleted and causing data loss.|
|Disable traffic to the public endpoints of your storage account. Create private endpoints for clients that run in Azure. Enable the public endpoint only if clients and services external to Azure require direct access to your storage account. Enable firewall rules that [limit access to specific virtual networks](/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&bc=%2Fazure%2Fstorage%2Fblobs%2Fbreadcrumb%2Ftoc.json&tabs=azure-portal#grant-access-from-a-virtual-network). | Starting with zero access, and then methodically, and incrementally providing the least amount of access required for clients and services, minimizes the risk of creating unnecessary openings for attackers.|
|Authorize access by using Azure role-based access control (Azure RBAC). | With RBAC, there are no passwords or keys that could be compromised. The security principal (user, group, managed identity, or service principal) is authenticated by Microsoft Entra ID to return an OAuth 2.0 token. The token is used to authorize a request against the Blob service.|
|[Disallow Shared Key authorization](/azure/storage/common/shared-key-authorization-prevent?tabs=portal). This disables not only account key access, but also service and account SAS tokens as those types of tokens are based on account keys. | Only secured requests that are authorized with Microsoft Entra ID will succeed. |
|Microsoft discourages the use of an account key. If you must use account keys, then [store them in Azure Key Vault](/azure/storage/common/storage-account-keys-manage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&bc=%2Fazure%2Fstorage%2Fblobs%2Fbreadcrumb%2Ftoc.json&tabs=azure-portal#protect-your-access-keys), and make sure that you regenerate them periodically. | Azure Key Vault lets you retrieve keys at runtime, instead of saving them with your application. Azure Key Vault also makes it easy to rotate your keys without interruption to your applications. Rotating the account keys periodically reduces the risk of exposing your data to malicious attacks.|
|Evaluate whether you need SAS tokens to secure access to Blob resources. Microsoft discourages the use of SAS tokens. If you must create one, then review this list of [SAS best practices](/azure/storage/common/storage-sas-overview#best-practices-when-using-sas) before you create and distribute it. | Best practices can help you prevent a SAS token from being leaked and quickly recover from leak should one occur.  |
|[Configure your storage account](/azure/storage/common/transport-layer-security-configure-minimum-version?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&bc=%2Fazure%2Fstorage%2Fblobs%2Fbreadcrumb%2Ftoc.json&tabs=portal) to require that clients send and receive data by using minimum version of TLS 1.2. | TLS 1.2 is more secure and faster than TLS 1.0 and 1.1, which don't support modern cryptographic algorithms and cipher suites.|
|Consider using your own encryption key to protect the data in your storage account. See [Customer-managed keys for Azure Storage encryption](/azure/storage/common/customer-managed-keys-overview?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&bc=%2Fazure%2Fstorage%2Fblobs%2Fbreadcrumb%2Ftoc.json). | Customer-managed keys provide greater flexibility and control. For example, you can store encryption keys in Azure Key Vault and automatically rotate them. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure Blob Storage and its environment.

### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Identify the billing meters that are used to calculate your bill.** Meters are used to track the amount of data stored in the account (capacity), and the number and type of operations that are executed to write and read data. There are also meters associated with the use of optional features such as blob index tags, blob inventory, change feed support, encryption scopes, and SSH File Transfer Protocol (SFTP) support. See [How you're charged for Azure Blob Storage](/azure/storage/common/storage-plan-manage-costs?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&bc=%2Fazure%2Fstorage%2Fblobs%2Fbreadcrumb%2Ftoc.json#how-youre-charged-for-azure-blob-storage).
>
> - **Understand the price of each meter.** Make sure to use the appropriate pricing page and apply the appropriate settings in that page. See [Finding the unit price for each meter](/azure/storage/common/storage-plan-manage-costs?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&bc=%2Fazure%2Fstorage%2Fblobs%2Fbreadcrumb%2Ftoc.json#finding-the-unit-price-for-each-meter). Consider the number of operations associated with each price. For example, the price associated with write and read operations applies to 10,000 operations. The price of an individual operation would be the listed price divided by 10,000.
>
> - **Estimate the cost of capacity and operations.** You can model the costs associated with data storage, ingress, and egress by using the [pricing calculator](https://azure.microsoft.com/pricing/calculator/). Use fields to compare the cost associated with various regions, account type, namespace type, and redundancy configurations.
>
>   For certain scenarios, you can use sample calculations and worksheets available in Microsoft documentation. For example, use [this guide](/azure/storage/blobs/archive-cost-estimation) to model the cost of archiving data, and [this guide](/azure/storage/blobs/azcopy-cost-estimation) to model the cost of AzCopy data transfers.
>
> - **Choose a billing model for capacity.** Evaluate whether using a [commitment-based model](/azure/storage/blobs/storage-blob-reserved-capacity) is more cost-efficient than using a consumption-based model. If you are unsure about how much capacity you'll need, you can start with a consumption-based model, monitor capacity metrics, and evaluate later.  
>
> - **Choose an account type, a redundancy level, and a default access tier.** You must select a value for each of these settings when you create a storage account, and each   of them impact both transaction and capacity charges. All these settings except for the account type can be changed after the account is created.
>
> - **Have a plan for how you will manage the lifecycle of data.** Optimize transaction and capacity costs by leveraging access tiers and lifecycle management. Data used less often should be placed in cooler access tiers while data that is accessed often should be placed in warmer access tiers.
>
> - **Decide which features you need.** Some features such as versioning and blob soft delete incur additional transaction and capacity costs as well as other charges. As you choose which capabilities to add to your account, make sure to review the pricing and billing sections in articles that describe those capabilities.  
>
>   For example, if you enable blob Inventory, you're billed for the number of objects scanned. If you use Blob index tags, you're billed for the number of index tags. If you enable SSH File Transfer Protocol (SFTP) support, you're billed an hourly charge even if there are no SFTP transfers. If you decide against using a feature, confirm that the feature is disabled, as some of them are enabled automatically when you create the account.
>
> - **Create guardrails.** Create [budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) based on subscriptions and resource groups. Use governance policies to restrict resource types, configurations, and locations. Additionally, use role-based access control to block actions that can lead to overspending.
>
> - **Monitor costs** to ensure they stay within budgets, compare against forecasts, and see where overspending might have occurred. You can use the [cost analysis](/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) pane in the Azure Portal. You also can export cost data to a storage account and analyze that data by using Excel or Power BI.
>
> - **Monitor usage.** Continuously monitor usage patterns and detect unused or underutilized accounts and containers. Use [Storage insights](/azure/storage/blobs/blob-storage-monitoring-scenarios#identify-storage-accounts-with-no-or-low-use) to identity accounts with no or low use. Enable blob inventory reports, and use tools such as [Azure Databricks](/azure/storage/blobs/storage-blob-calculate-container-statistics-databricks) or [Azure Synapse](/azure/storage/blobs/storage-blob-inventory-report-analytics) and Power BI to analyze cost data. Watch out for unexpected increases in capacity. This could mean that you are collecting large numbers of log files, blob versions, or soft-deleted blobs or versions. Have a plan for expiring objects or moving objects to more affordable access tiers.

### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|Carefully choose the most cost-effective [default access tier](/azure/storage/blobs/access-tiers-overview#default-account-access-tier-setting) for your scenarios. | Unless a tier is specified with each blob upload, blobs infer their access tier from the default access tier setting. A change to the default access tier setting of a storage account applies to all blobs in the account for which an access tier hasn't been explicitly set. If you've collected a large number of blobs, this cost could be significant. To learn about how a tier change would impact each existing blob, see [Changing a blob's access tier](/azure/storage/blobs/access-tiers-overview#changing-a-blobs-access-tier).|
|Upload data directly to the most cost-efficient access tier.<br><br>For example, if the default access tier setting of your account is hot, but you're uploading files for archive purposes, then as part of your upload operation, specify a cooler tier as the archive or cold tier. | Choosing the most optimal tier up front can reduce costs. If you change the tier of a block blob that you've already uploaded, then you'll pay the cost of writing to the initial tier when you first upload the blob, and then pay the cost of writing to the desired tier. |
|Use lifecycle management policies to move blobs to the most cost-efficient tiers based on usage metrics such as the last accessed time. | Placing blobs into the appropriate tier optimizes what you spend on transaction and capacity charges.|
|[Pack small files into larger ones](/azure/storage/blobs/access-tiers-best-practices#pack-small-files-before-moving-data-to-cooler-tiers) before moving them to cooler tiers. You can use file formats such as TAR or ZIP. | Cooler tiers have higher data transfer costs. By having fewer large files, you'll reduce the number of operations required to transfer data.|
|Use standard priority when rehydrating blobs from archive storage. Use high-priority rehydration only for emergency data restoration situations. | High priority rehydration from archive can lead to higher-than-normal bills.|
|Reduce the cost of using resource logs by choosing the appropriate log storage location, and by managing log retention periods. If you only plan to query logs occasionally (for example, query logs for compliance auditing), consider sending resource logs to a storage account instead of sending them to a Log Analytics workspace. To analyze logs, you could use a serverless query solution such as Azure Synapse. See [Optimize cost for infrequent queries](/azure/storage/blobs/blob-storage-monitoring-scenarios#optimize-cost-for-infrequent-queries). Use lifecycle management policies to delete or archive them. If you configure a diagnostic setting that sends resource logs to a Log Analytics workspace, make sure to configure a retention period in that workspace. | Storing resource logs in a storage account for later analysis can be a cheaper option. Setting a retention period in a Log Analytics workspace avoids extra charges related to the use of Log Analytics. Using lifecycle management policies to manage log retention in a storage account prevents large numbers of logs files building up over time which can lead to unnecessary capacity charges.|
|If you enable versioning, use a lifecycle management policy to automatically delete old versions. | Every write operation to a blob creates a new version. This increases capacity costs. You can keep costs in check by removing versions that you no longer need. |
|If you enable versioning, consider placing blobs that are frequently overwritten into an account that doesn't have versioning enabled. | Every time a blob is overwritten, a new version is added which leads to increased storage capacity charges. To reduce capacity charges, store frequently overwritten data in a separate storage account with versioning disabled. |
|If you enable soft delete, consider placing blobs that are frequently overwritten into an account that doesn't have soft delete enabled. Set retention periods. Consider starting with a short retention period to better understand how the feature affects your bill. The minimum recommended retention period is seven days. | Every time a blob is overwritten a new snapshot is created. The cause of increased capacity charges might be difficult to access because the creation of these snapshots doesn't appear in logs. To reduce capacity charges, store frequently overwritten data in a separate storage account with soft delete disabled. A retention period keeps soft-deleted blobs from piling up and adding to the cost of capacity.|
|Enable SSH File Transfer Protocol (SFTP) support only when it's used to transfer data. | Enabling the SFTP endpoint incurs an hourly cost. By thoughtfully disabling SFTP support, and then enabling it as needed, you can avoid passive charges from accruing in your account.|
|Disable any encryption scopes that aren't needed to avoid unnecessary charges. | Encryptions scopes incur a per month charge.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to you Azure Blob Storage configuration.

> [!div class="checklist"]
>
> - **Create maintenance and emergency recovery plans.** Consider data protection features, backup and restore operations, or failover procedures. Prepare for potential [data loss and data inconsistencies](/azure/storage/common/storage-disaster-recovery-guidance?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&bc=%2Fazure%2Fstorage%2Fblobs%2Fbreadcrumb%2Ftoc.json#anticipate-data-loss-and-inconsistencies) as well as the [time and cost of failing over](/azure/storage/common/storage-disaster-recovery-guidance?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&bc=%2Fazure%2Fstorage%2Fblobs%2Fbreadcrumb%2Ftoc.json#the-time-and-cost-of-failing-over).

> - **Monitor the health of your storage account.** Create [Storage insights](/azure/storage/common/storage-insights-overview?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&bc=%2Fazure%2Fstorage%2Fblobs%2Fbreadcrumb%2Ftoc.json) dashboards to monitor availability, performance, and resilience metrics. Set up alerts to identify and address issues in your system before your customers notice them.  Use diagnostic settings to route resource logs to a Log Analytic workspace. Then, you can query logs to investigate alerts more deeply. 
>
> - **Enable Blob Inventory reports** to review retention, legal hold or encryption status of your storage account contents, or you can use it to understand the total data size, age, tier distribution, or other attributes of your data. Use tools such as [Azure Databricks](/azure/storage/blobs/storage-blob-calculate-container-statistics-databricks) or [Azure Synapse](/azure/storage/blobs/storage-blob-inventory-report-analytics) and Power BI to better visualize inventory data and to create reports for other stakeholders.
>
> - **Set up policies that delete blobs or move them to cost-efficient access tiers.** Create a lifecycle management policy with an initial set of conditions. Policy runs automatically delete or set the access tier of blobs based on the conditions you define. Periodically analyze container use by using Azure Monitor metrics and Blob Inventory reports so that you can refine conditions to optimize cost efficiency.  

### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|Use infrastructure as code (IaC) to define the details of your storage accounts in [ARM templates](/azure/azure-monitor/logs/resource-manager-workspace), [Azure BICEP](/azure/azure-monitor/logs/resource-manager-workspace), or [Terraform](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/log_analytics_workspace.html). | You can use your existing DevOps processes to deploy new storage accounts and [Azure Policy](/azure/governance/policy/overview) to enforce their configuration.|
|Use Storage insights to track the health and performance of your storage accounts. Storage insights provides a unified view of the failures, performance, availability, and capacity for all your storage account. | You can track the health and operation of each of your accounts. Easily create dashboards and reports that stakeholders can use to track the health of your storage accounts.|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for your Azure Blob Storage configuration.

> [!div class="checklist"]
>
> - **Plan for scale.** Understand the scale targets for storage accounts.
>
> - **Choose the optimal storage account type.** If your workload requires high transaction rates, smaller objects, and a consistently low transaction latency, then consider using premium block blob storage accounts. In most cases, a standard general-purpose v2 account is most appropriate.  
>
> - **Reduce travel distance between client and server.** Place data in regions nearest to connecting clients (ideally in the same region). Optimize for clients in regions far away by using object replication or a Content Delivery Network (CDN). Default network configurations provide the best performance. Modify network settings only to improve security. In general, network settings won't decrease travel distance and won't improve performance.
>
> - **Choose an efficient naming scheme.** Decrease the latency of listing, list, query, and read operations by using hash tag prefixes nearest the beginning of the blob partition key (account, container, virtual directory, or blob name).  This scheme benefits mostly accounts that have a flat namespace. 
>
> - **Optimize the performance of data clients.** [Choose a data transfer tool](/azure/storage/common/storage-choose-data-transfer-solution?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&bc=%2Fazure%2Fstorage%2Fblobs%2Fbreadcrumb%2Ftoc.json) that is most appropriate for the data size, transfer frequency, and bandwidth of your workloads. Some tools such as [AzCopy](/azure/storage/common/storage-use-azcopy-v10?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&bc=%2Fazure%2Fstorage%2Fblobs%2Fbreadcrumb%2Ftoc.json) are optimized for performance and require little intervention. Consider the [Factors influencing latency](/azure/storage/blobs/storage-blobs-latency#factors-influencing-latency), and fine-tune performance by reviewing the performance optimization guidance that is published with each tool.
>
> - **Optimize the performance of custom code.** Consider using Azure Storage SDKs instead of creating your own wrappers for Blob REST operations. Azure SDKs are optimized for performance, and they provide mechanisms to fine-tune performance. Before creating an application, review the [Performance and scalability checklist for Blob storage](/azure/storage/blobs/storage-performance-checklist). Consider using [query acceleration](/azure/storage/blobs/data-lake-storage-query-acceleration) to filter out unwanted data during the storage request and keep clients from needlessly transferring data across the network.
>
> - **Collect performance data.** Monitor your storage account to identify performance bottlenecks that occur from throttling. See [Monitoring your storage service with Azure Monitor Storage insights](/azure/storage/common/storage-insights-overview?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#view-from-azure-monitor). Use both metrics and logs. Metrics provide numbers such as throttling errors. Logs describe activity. If you see throttling metrics, you can use logs to identity which clients are receiving throttling errors. See [Auditing data plane operations](/azure/storage/blobs/blob-storage-monitoring-scenarios#auditing-data-plane-operations).

### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|Provision storage accounts in the same region where dependent resources are placed. For applications that **are not** hosted within Azure, such as mobile device apps or on premises enterprise services, locate the storage account in a region nearer to those clients.<br><br>If clients from a different region don't require the same data, then create a separate account in each region.<br><br>If clients from a different region require only some data, consider using an object replication policy to asynchronously copy relevant objects to a storage account in the other region. | Reducing the physical distance between the storage account and VMs, services, and on-premises clients, can improve performance and reduce network latency.  For applications hosted in Azure, this also reduces cost as bandwidth usage within a single region is free.|
|For broad consumption by web clients (streaming video, audio, static website content), consider using a Content Delivery Network (CDN) through Azure Front Door. | Content is delivered to clients faster as it uses Microsoft's global edge network with hundreds of global and local points of presence around the world..|
|Add a hash character sequence (such as three-digits) as early as possible in the partition key of a blob. The partition key is the concatenation of the account name, container name, virtual directory name and blob name. If you plan to use timestamps in names, then consider adding a seconds value to the beginning of that stamp. See [Partitioning](/azure/storage/blobs/storage-performance-checklist#partitioning).| Using a hash code or seconds value nearest the beginning of a partition key reduces the time required to list query and read blobs. |
|When uploading blobs or blocks, use a blob or block size that is greater than 256 KiB. | Blob or block sizes above 256 KiB uses the performance enhancements of [high-throughput block blobs](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/).|

## Azure policies

Azure provides an extensive set of built-in policies related to Azure Blob Storage and its dependencies. Some of the preceding recommendations can be audited through Azure Policies. For example, you can check if:

- Anonymous public read access to containers and blobs is not enabled.
- Diagnostic settings for the Blob Service are set to stream resource logs to a Log Analytics workspace.
- Only requests from secure connections (HTTPS) are accepted.
- Shared access signature (SAS) expiration policy is enabled.
- Cross tenant object replication is disabled.
- Shared Key authorization is disabled.
- Network firewall rules have been applied to the account

For comprehensive governance, review the [Azure Policy built-in definitions for Azure Storage](/azure/governance/policy/samples/built-in-policies#storage) and other policies that might affect the security of the compute layer.

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your Azure Blob Storage.

- [Reliability](/azure/advisor/advisor-high-availability-recommendations)
- [Security](/azure/defender-for-cloud/recommendations-reference)
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations)
- [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations)

## Next steps

See [Azure Blob Storage documentation](/azure/storage/blobs/)
