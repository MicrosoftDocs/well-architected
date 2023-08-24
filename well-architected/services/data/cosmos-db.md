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

- [Enable Microsoft Defender](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/MDC_Microsoft_Defender_Azure_Cosmos_DB_Audit.json)
- [Require a virtual network service endpoint](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json)
- [Disable local authentication](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_DisableLocalAuth_AuditDeny.json)
- [Require firewall rules](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json)

## Cost optimization



## Next steps

> [!div class="nextstepaction"]
> [Deploy an Azure Cosmos DB for NoSQL account using the a Bicep template](/azure/cosmos-db/nosql/quickstart-template-bicep)
