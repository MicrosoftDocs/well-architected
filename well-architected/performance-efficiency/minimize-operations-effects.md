---
title: Recommendations for operational tasks
description: Learn how to minimize the effects of operations on performance.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing operational tasks: PE 10

This guide describes the recommendations for optimizing operational tasks and minimizing their effects on the workload. Operations activities use the same compute resources as the workload itself. Failure to consider the effects of operations tasks can cause the workload to miss its performance targets. It can also negatively affect the performance of the workload for end users.

**Definitions**

  |Terms|                           Definitions|
|-|-|
|  Infrastructure as code     |         A descriptive model to define and deploy infrastructure, such as networks, virtual machines, load balancers, and connection topologies.|
 | Database index reorganization |      A maintenance activity that optimizes the current database index.|
  |Database index rebuilding|     A maintenance activity that drops and recreates the index|
  |Deployment slot |    A feature in Azure App Service where you can deploy live apps with their own host names.|
  |Blue-green deployment|   A deployment strategy that uses two identical environments and controls the direction of traffic to new deployments (green).|
  |In-place upgrades|  Upgrading a component or software without replacing it or migrating to a new environment.|
  |Database schema| The general structure of a relational database and its relationships to other data.|

## Key strategies

You should optimize any tasks that have a performance impact. You can shorten the duration of the tasks. For example, virus scans can exclude trusted or out-of-scope files. You can tune the tasks to mitigate effects on users. For example, you can adjust the frequency of scans. If neither is a viable option, you should align operational tasks with off-peak hours when possible. Example tasks include reorganizing or rebuilding a database index, new deployments, data compaction, and data tiering.

### Account for operational tasks

Performance targets should include operations activities. You likely need to lower any current performance targets that don't account for operations tasks. You should consider routine, regular, and ad-hoc tasks. Examples of routine tasks include virus scanning, reindexing databases, disk or database backups, certificate rotations, patching an operating system (OS), rotating passwords, rotating API keys, penetration testing, and audit reviews in production. You should consider configuration changes at an OS or Azure resource level.

### Optimize deployments

Tune your deployment strategy to minimize performance impact. Deployments can negatively affect workload performance in several ways. Deployments can cause service interruptions and downtime. They can introduce latency and increase response times. It takes time for new instances to start up and apply configurations. Deployments can also consume shared computing resources. You need to create a deployment strategy that mitigates these performance concerns. You should select a deployment strategy that minimizes performance effects, such as a blue-green deployment or pre-warming instances before cutover. Deployments that replace live resources should either use traffic shifting or prewarmed at the current instance count.

- *Deploy at current instance count:* You should also avoid deployments that cause immediate scale operations. You shouldn't deploy resources into a live system with an instance count so low that it forces the system to immediately perform a scale operation. For example, your [infrastructure-as-code](/azure/cloud-adoption-framework/ready/considerations/infrastructure-as-code) (IaC) template might not match the number of instances needs at the time of deployment. It might have an instance count of two, but the current deployed environment is running at eight. The deployment would remove six instances and negatively impact performance.
- *Traffic shifting:* Create multiple versions of your application deployment, each representing a different version of the application. Configure a virtual service (for example, Isito) that defines routing rules for clients to target the different versions of the application. Use the weight attribute in the virtual service to specify the percentage of traffic that should be routed to each version. Update the virtual service to gradually shift the traffic from the old version to the new version by adjusting the weight attribute. Monitor the traffic and gradually increase the weight for the new version while decreasing the weight for the old version until all traffic is routed to the new version.

- *Prewarm instances:* Pre-warm application instances after every fresh deployment so that it is ready to serve user traffic. Before replacing live resources, deploy the new version of the application with the same number of instances as the current live resources. Allow the new instances to warm up by handling a portion of the traffic alongside the existing instances. Monitor the performance and stability of the new instances during the warm-up period.
Once the new instances are fully warmed up and performing well, replace the live resources with the new instances.

### Optimize upgrades

In-place upgrades can temporarily slow down or interrupt a workload. You should ensure upgrades are compatible with the workload. Testing the upgrade in a separate environment can help identify potential issues. You should take a complete backup of critical data and configurations before applying the upgrade so you can restore to a good state if needed. It's also helpful to communicate the upgrade to keep users informed.

### Optimize tooling

Essential tools for file integrity monitoring, virus scanning, intrusion detection, and other operational tasks can affect workload performance. They consume compute resources and can add latency and performance overhead. You need to test and understand the effects your tools have on workload performance. Based on the test results, you should fine-tune tool configurations, adjust scan frequency, and reallocate compute resources. For virus scanning, you could create a relevant exclusion list to minimize the duration of scans.

### Optimize database operations

**Minimize effects of backups.** Backups consume workload resources, such as processing power, network bandwidth, and disk I/O. You need to test and select a backup strategy that minimizes these effects. You should perform backups off-peak hours when possible. The strategy should include incremental backup instead of full backups each time. Snapshots can be less resource intensive than backups. You should consider built-in platform backup and restore features over building a custom solution. You need to test these options and use a combination that provides the best performance for your workload.

**Minimize effects of schema changes.** Schema and data migration changes can disrupt performance to active queries, indexes, transactions or cause data unavailability. To minimize these effects, you should plan and test schema changes in a non-production environment. You can use different deployment techniques to implement schema updates. You should also use available schema changing tools to optimize the process. Archiving data and partitioning can help reduce the effects of schema changes.

### Optimize monitoring and debugging

Excessive or poorly implemented logging, telemetry, instrumentation, and distributed tracing capture and collection can affect performance. Likewise, convenience features such as remote debugging can also impact performance. You need to measure and know their performance effects on the environment. You don\'t want these processes to degrade performance. You should configure or disable any processes whose performance affects outweigh their benefits.

## Azure facilitation

-   Some Azure compute services support advanced deployment strategies such as blue-green deployments. You can combine those services with your traffic shifting or instance warming strategy to mitigate the performance effects of deployment.

-   Some Azure data services support low-to-no performance impact for point-in-time recovery and indexing.

-   Services that integrate with Azure Key Vault often support a seamless certificate rotation or secrets rotation that minimize performance impact.

-   Azure App Service has [deployment slots](/azure/app-service/deploy-staging-slots?tabs=portal). Deployment slots allow you to deploy code to a non-production environment. You can swap app content and configurations elements between two deployment slots, such as a non-production slot to the production slot.

-   Azure Front Door and Traffic Manager allow you to a implement [blue-green deployment strategy](/azure/architecture/guide/aks/blue-green-deployment-for-aks).

-   [Azure SQL Database](/azure/azure-sql/database/automated-backups-overview) automatically takes full backups, differential backups, and transaction log backups.

-   [Azure Cosmos DB](/azure/cosmos-db/online-backup-and-restore) automatically takes backups of your data at regular intervals. The automatic backups are taken without affecting the performance or availability of the database operations. Cosmos DB stores the backups in a separate storage service. 

## Tradeoff

Waiting to perform operations activities during off-peak hours can affect operational efficiency. It might be less convenient to have the personnel with the right skillset work during off-peak hours.
