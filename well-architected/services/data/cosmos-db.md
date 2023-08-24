---
title: Review - Azure Cosmos DB for NoSQL
description: Design considerations and recommendations for each pillar as related to solutions using Azure Cosmos DB for NoSQL.
author: PageWriter-MSFT
ms.author: prwilk
ms.topic: conceptual
ms.date: 08/23/2023
products:
  - azure-cosmos-db
---

# Azure Well-Architected Framework review – Azure Cosmos DB for NoSQL

This article describes the best practices for Azure Cosmos DB for NoSQL. These best practices ensure that you can deploy solutions on Azure Cosmos DB that are efficient, reliable, secure, optimized for cost, and operationally excellent. This guidance focuses on the five pillars of architecture excellence in the [Well-Architected Framework](../../index.md):

- [Reliability](../../resiliency/index.yml)
- [Security](../../security/index.yml)
- [Cost optimization](../../cost/index.yml)
- [Operational excellence](../../devops/index.yml)
- [Performance efficiency](../../scalability/index.yml)

This review guide assumes that you have a working knowledge of Azure Cosmos DB and are well versed with its features. For more information, see [Azure Cosmos DB for NoSQL](/azure/cosmos-db/nosql/).

## Prerequisites

Understanding the Well-Architected Framework pillars can help produce a high-quality, stable, and efficient cloud architecture. We recommend that you start by reviewing your workload using the [Azure Well-Architected Framework Review assessment](/assessments/?id=azure-architecture-review&mode=pre-assessment).

For more context, review various reference architectures that reflect the considerations from this guide in their design. These architectures include, but aren't limited to:

- [Globally distributed mission-critical applications using Azure Cosmos DB](/azure/architecture/solution-ideas/articles/globally-distributed-mission-critical-applications-using-cosmos-db)
- [Serverless apps using Azure Cosmos DB](/azure/architecture/solution-ideas/articles/serverless-apps-using-cosmos-db)
- [Multi-region web app with Azure Cosmos DB replication](/azure/architecture/solution-ideas/articles/multi-region-web-app-cosmos-db-replication)

## Reliability

As with any cloud service, failures can occur both on the service and the workload side. It's impossible to prevent all potential failures, but it's a better goal to minimize the effects a single failing component can have on your entire workload. This section includes considerations and recommendations to minimize the consequences of a one-off failure.

### Design checklist

> [!div class="checklist"]
>
> - Review the default behavior of your selected developer SDK for transient issues. Review potential configuration options to configure retry rates, secondary write locations, and consistency levels.
> - Evaluate the multi-region and single-region write strategies for your workload. For single-region write, design your workload to have at least a second read region for failover. For multi-region write, compare the tradeoffs in complexity and consistency against the advantages of writing to multiple regions. Review [expectations during a regional outage for single-region and multi-region write accounts](/azure/cosmos-db/high-availability#what-to-expect-during-a-region-outage).
> - Design your database account deployment so it spans at least two regions in Azure. Additionally, distribute your account across multiple availability zones when offered within your Azure region.
> - Enable [service-managed failover](/azure/cosmos-db/high-availability#availability) for your account.
> - Design an end-to-end test of high availability for your application.
> - Consider how your selected [consistency level](/azure/cosmos-db/consistency-levels) and replication mode [impacts the Recovery point objective (RPO)](/azure/cosmos-db/consistency-levels#rto) in a region-wide outage.
> - Walk through [common backup processes](/azure/cosmos-db/continuous-backup-restore-introduction) including, but not limited to; point-in-time restore, recovering from accidental destructive operations, restoring deleted resources, and restoring to another region at a point-in-time.
> - Explore the [designing resilient applications guide](/azure/cosmos-db/nosql/conceptual-resilient-sdk-applications), review the [default retry policy](/azure/architecture/best-practices/retry-service-specific#azure-cosmos-db) for the SDKs, and plan for [custom handling for specific transient errors](/azure/cosmos-db/nosql/conceptual-resilient-sdk-applications#should-my-application-retry-on-errors). These guides will give best practices to make application code resilient to transient errors.
>

### Recommendations

| Recommendation | Benefit |
| --- | --- |
| Distribute your Azure Cosmos DB account across availability zones (when available). | Availability zones provide distinct power, networking, and cooling isolating hardware failures to a subset of your replicas. Using availability zones provides an RTO and RPO of 0. |
| Configure your Azure Cosmos DB account to span at least two regions. | Spanning multiple regions prevents your account from being entirely unavailable if there's an isolated region outage. |
| Enable service-managed failover for your account. | Service-managed failover allows Azure Cosmos DB to change the write region of a multiple-region account to preserve availability. Understand the tradeoffs with service-managed failover and plan for forced failover if necessary. |
| Validate availability by testing failover manually with service-managed failover temporarily disabled. | Temporarily disabling service-manage failover allows you to validate the end-to-end high availability of your application with a manual failover started using a script or the Azure portal. Afterwards, you can reenable service-managed failover. |

### Azure Policy definitions

- [Policy: Require at least two regions](https://github.com/Azure/Community-Policy/blob/main/policyDefinitions/Cosmos%20DB/audit-geo-replication-for-azure-cosmos-db/azurepolicy.json)
- [Policy: Enable service-managed failover](https://github.com/Azure/Community-Policy/blob/main/policyDefinitions/Cosmos%20DB/audit-automatic-failover-for-azure-cosmos-db/azurepolicy.json)
- [Policy: Require specific deployment regions](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_Locations_Deny.json)

## Security

Security is a critical part of any architecture that can be easily overlooked for convenience. Bolster the security of your final workload by considering various security best practices up-front before the first resource or proof of concept is created. This section includes considerations and recommendations to reduce the number of security vulnerabilities for your final workload.

### Design checklist

> [!div class="checklist"]
>
> - Reduce surface attack area by designing to use private endpoints in accordance with the [security baseline](/en-us/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline#network-security) for Azure Cosmos DB.
> - Create roles, groups, and assignments for control-plane access to your account per the principle of [least-privilege access](/en-us/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline#privileged-access). Consider [disabling key-based authentication](/azure/cosmos-db/how-to-setup-rbac#disable-local-auth).
> - Assess service-level [compliance](/azure/cosmos-db/compliance) and [certifications](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) in the context of current global personal data requirements.
> - [Encrypt data](/azure/cosmos-db/database-encryption-at-rest) at-rest or in-motion using service-managed keys or customer-managed keys (CMKs).
> - Audit user access, security breaches, and resource operations with [control plane logs.](/azure/cosmos-db/audit-control-plane-logs).
> - Monitor data egress, data changes, usage, and latency with [data plane metrics](/azure/cosmos-db/use-metrics).
>

### Recommendations

| Recommendation | Benefit |
| --- | --- |
| Implement, at a minimum, the data protection and identity management security baselines. | Go through the [security baseline](/en-us/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline) including [identity management](/en-us/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline#identity-management) and [data protection](/en-us/security/benchmark/azure/baselines/azure-cosmos-db-security-baseline#data-protection). Implement the recommendations to secure your Azure Cosmos DB account. |
| Disable public endpoints and use private endpoints whenever possible. | Avoid leaving unnecessary or unused public endpoints available for surface area attacks to your account. |
| Use role-based access control to limit control-plane access to specific identities and groups and within the scope of well-defined assignments. | Use role-based access control to prevent unintended access to your account. Assign appropriate roles and permissions to users or applications accessing Azure Cosmos DB. |
| Create virtual network endpoints and rules to limit access to the account. | Implement virtual network service endpoints and firewall rules to restrict access to your Azure Cosmos DB account. Use network security groups (NSGs) to control inbound and outbound traffic to and from the Azure Cosmos DB resources. Limiting access to trusted networks and applying appropriate network security measures helps protect your data from unauthorized access. |
| Follow best software development practices for secure access to data. | Follow secure coding practices and perform secure code reviews when developing applications that interact with Azure Cosmos DB. Protect against common security vulnerabilities like injection attacks, cross-site scripting (XSS), or insecure direct object references (IDOR). Implement input validation, [parameterized queries](/azure/cosmos-db/nosql/query/parameterized-queries), and appropriate error handling for common [HTTP status codes](/en-us/rest/api/cosmos-db/http-status-codes-for-cosmosdb) to prevent security risks. |
| Monitor control-plane logs for breaches. | Monitoring helps you track access patterns and audit logs, ensuring that your database remains secure and compliant with relevant data protection regulations. Monitoring data-plane metrics can also help identify unfamiliar patterns that may reveal a security breach. |
| Enable Microsoft Defender for Azure Cosmos DB | Microsoft Defender detects attempts to exploit databases in your Azure Cosmos DB for NoSQL account. Defender detects potential SQL injections, suspicious access patterns, and other potential exploitation. |

### Azure Policy definitions

- [Policy: Enable Microsoft Defender](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/MDC_Microsoft_Defender_Azure_Cosmos_DB_Audit.json)
- [Policy: Require a virtual network service endpoint](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json)
- [Policy: Disable local authentication](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_DisableLocalAuth_AuditDeny.json)
- [Policy: Require firewall rules](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json)

## Cost optimization

Your workload's characteristics and the implementation of your solution can influence the final cost of running in Azure. Consider main drivers like your partitioning strategy, consistency level, replication, and write type when designing your workload. When sizing your workload, consider the read/write nature of your data, the size of average items, normalization, and TTL. This section includes considerations and recommendations to streamline costs for your workload.

> [!div class="checklist"]
>
> - Design an indexing policy that's considers the operations and queries you commonly make in your workload.
> - Determine a partition key or set of partition keys that has a value that doesn't change with high cardinality. Use the [existing guidance and best practices](/azure/cosmos-db/partitioning-overview#choose-partitionkey) to help select an appropriate partition key.
> - Select a throughput allocation schema that's appropriate for your workload. Review the benefits of standard and autoscale throughput distributed at the database or container level. Also, consider serverless when appropriate. [Review your workload's traffic patterns](/azure/cosmos-db/how-to-choose-offer#understand-your-traffic-patterns) in the context of selecting a throughput allocation scheme.
> - Consider consistency levels as they relate to your workload. Also, consider if client sessions should alter the default consistency level.
> - Calculate the expected overall data storage for your workload. The size of items, indexes, and metadata all influence your data storage cost. Calcuate the impact of replication on storage costs.
> - Create a strategy to automatically remove older items that are no longer used or necessary. If required, export these items to a lower-cost storage solution before they are removed.
> - Evaluate your most common queries that minimize cross-partition lookups. Use this information to inform the process of selecting a partition key or customizing an indexing policy.
>

### Recommendations

| Recommendation | Benefit |
| --- | --- |
| Monitor RU/s utilization and patterns. | Use metrics to monitor RU consumption from the very beginning of your solution. Use queries and other data research techniques to find antipatterns in your application code. |
| Customize your indexing policy to map to your workload. | The default indexing policy indexes all paths in an item, and this policy can have significant impacts to RU consumption and costs. Use an indexing policy designed based on only the paths that you need to index for your common queries. For write-heavy workloads, disable automatic indexing of columns not used in queries. |
| Select partition key\[s\] that are ideal for your workload. | The partition key\[s\] should distribute throughput consumption and data storage evenly across logical partitions. The selection should also minimize the number of unbounded cross-partition queries. Avoid hot partitions that receive a disproportionate amount of traffic, as unbalance partitions can increase throughput costs and transient errors. Use the most common search queries to determine potential partition key\[s\] that likely executes only single-partition or bounded cross-partition queries. |
| Use serverless or provisioned throughput, manual provisioning or autoscale, at the database or container level when appropriate for your workload. | [Compare the provisioned throughput types](/azure/cosmos-db/how-to-choose-offer) and select the appropriate option for your workload. Generally, smaller and dev/test workloads may benefit from serverless throughput or manual shared throughput at the database level. Larger, mission-critical workloads may benefit from autoscale throughput assigned at the database or container level. |
| Configure the [default consistency level](/azure/cosmos-db/consistency-levels#configure-the-default-consistency-level) for your application. When appropriate, [override the default consistency level](/azure/cosmos-db/nosql/how-to-manage-consistency#override-the-default-consistency-level) in client sessions. | You may not always need to change the standard default consistency level or override it in client sessions. Consider the higher costs associated with reads at stronger consistency levels. |
| For dev/test workloads, use the Azure Cosmos DB emulator. | The [Azure Cosmos DB emulator](/azure/cosmos-db/local-emulator) is an option for dev/test and continuous integration that can save on the costs of these common workloads for your development team. The emulator is also available as a [Docker container image](/azure/cosmos-db/docker-emulator-linux). |
| Use transactional batch operations | Use batch operations in client-side SDKS for inserting, updating, or deleting multiple documents in a single transaction request. This step can reduce the number of individual requests and can eventually lead to better throughput efficiency. |
| Use projection to reduce throughput costs of large query result sets. | Author queries to only project the minimal number of fields required from a result set. If calculations on fields are necessary, evaluate the throughput cost of performing those calculations server-side versus client-side. |
| Avoid using unbounded cross-partition queries. | Evaluate and author queries to ensure they search within a single logical partition whenever possible. Use query filters to control which logical partitions the query targets. If a query must search across logical partitions, bound the query to only search a subset of logical partitions instead of a full scan. |
| Implement time-to-live (TTL) to remove unused items. | Use TTL to automatically delete data that's no longer needed. Manage storage costs by removing expired or obsolete data. If necessary, export the expired data to a lower-cost storage solution. |

### Azure Policy definitions

- [Policy: Restrict the maximum allowed throughput](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_MaxThroughput_Deny.json)

## Next steps

> [!div class="nextstepaction"]
> [Deploy an Azure Cosmos DB for NoSQL account using the a Bicep template](/azure/cosmos-db/nosql/quickstart-template-bicep)
