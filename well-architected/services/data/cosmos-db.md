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

### Recommendations

| Recommendation | Benefit |
| --- | --- |
| Distribute your Azure Cosmos DB account across availability zones (when available). | Availability zones provide distinct power, networking, and cooling isolating hardware failures to a subset of your replicas. Using availability zones provides an RTO and RPO of 0. |
| Configure your Azure Cosmos DB account to span at least two regions. | Spanning multiple regions prevents your account from being entirely unavailable if there's an isolated region outage. |
| Enable service-managed failover for your account. | Service-managed failover allows Azure Cosmos DB to change the write region of a multiple-region account to preserve availability. Understand the tradeoffs with service-managed failover and plan for forced failover if necessary. |
| Validate availability by testing failover manually with service-managed failover temporarily disabled. | Temporarily disabling service-manage failover allows you to validate the end-to-end high availability of your application with a manual failover started using a script or the Azure portal. Afterwards, you can reenable service-managed failover. |

### Azure Policy definitions

- [Policy validate at least two regions](https://github.com/Azure/Community-Policy/blob/main/policyDefinitions/Cosmos%20DB/audit-geo-replication-for-azure-cosmos-db/azurepolicy.json)
- [Policy validate service-managed failover](https://github.com/Azure/Community-Policy/blob/main/policyDefinitions/Cosmos%20DB/audit-automatic-failover-for-azure-cosmos-db/azurepolicy.json)

## Next steps

> [!div class="nextstepaction"]
> [Deploy an Azure Cosmos DB for NoSQL account using the a Bicep template](/azure/cosmos-db/nosql/quickstart-template-bicep)
