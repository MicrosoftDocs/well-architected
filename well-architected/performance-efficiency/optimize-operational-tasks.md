---
title: Recommendations for optimizing operational tasks
description: Learn about Well-Architected Framework recommendations for optimizing operational tasks.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing operational tasks

**Applies to this Azure Well-Architected Framework Performance Efficiency checklist recommendation:** 

|[PE:10](checklist.md)| Optimize operational tasks. Monitor and minimize the effects of the software development lifecycle and other routine operations on workload performance. These operations include virus scans, secret rotations, backups, reindexing databases, and deployments.| 
|---|---| 

This guide describes the recommendations for optimizing operational tasks. Optimizing operational tasks is the process of minimizing the effects of tasks that you perform as part of routing workload operations. Operations activities use the same compute resources as the workload itself. Failure to consider the effects of operations tasks can cause the workload to miss its performance targets. It can also negatively affect the performance of the workload for your customers.

**Definitions**

|Term|                           Definition|
|-|-|
|Blue-green deployment|   A deployment strategy that uses two identical environments and controls the direction of traffic to new deployments (green deployments).|
|Database index rebuilding|     A maintenance activity that drops and recreates an index.|
|Database index reorganization|      A maintenance activity that optimizes the current database index.|
|Database schema| The general structure of a database and its relationships to other data.|
|Deployment slot |    A feature of Azure App Service that enables you to deploy live apps with their own host names.|
|In-place upgrades|  The process of upgrading a component or an application without replacing it or migrating it to a new environment.|
|Infrastructure as code (IaC)     |         A descriptive model for defining and deploying infrastructure, including networks, virtual machines, load balancers, and connection topologies.|

## Key design strategies

You need to take measures to reduce the effects of the software development lifecycle and other routine operations on workload performance. The goal is to ensure that routine operations, like virus scans, secret rotations, backups, index optimization (reorganization or rebuilding), and deployments, don't significantly degrade the performance of the workload.

### Account for operational tasks

It's important to consider operational tasks when you set performance targets. By incorporating routine, regular, and ad-hoc tasks into performance targets, you can ensure that the workload operates efficiently. To account for operational tasks in performance targets, here are some key points to consider:

- *Identify operational tasks.* Identify and include relevant operational tasks in performance targets. Examples of routine tasks can include virus scanning, database index reorganization, database index rebuilding, disk or database backups, certificate rotations, patching an operating system, rotating passwords, rotating API keys, penetration testing, and audit reviews in production.

- *Evaluate performance targets.* Evaluate current performance targets and adjust them to account for operational tasks that are specific to the workload. Doing so ensures that performance targets align with the workload's operational requirements.

### Optimize deployments

Optimizing deployments refers to refining the process of releasing resources and code to guarantee seamless performance and minimal interruptions. It involves planning, effective resource distribution, and thorough testing of both the infrastructure-as-code (IaC) and the application code before they are introduced to a live environment. Deployment inadequacies can lead to reduced speed and efficiency of a workload, potential resource constraints, and a compromised user experience in the operational setting. To optimize deployments, consider these strategies:

**Assess acceptable downtime.** If downtime is acceptable, you can implement deployment strategies that prioritize speed and efficiency. However, it's important to carefully assess the effect of downtime on business requirements before you make that decision. On the other hand, if downtime isn't acceptable, you need to implement deployment strategies that ensure continuous availability of the workload. Consider using techniques like blue-green deployments or canary deployments, where you gradually roll out new versions of the workload while you monitor for issues. These strategies help minimize the effect of downtime and ensure a seamless user experience.

**Deploy at current instance count.** You should also avoid deployments that cause immediate scale operations. You shouldn't deploy resources into a live system with an instance count so low that it forces the system to immediately perform a scale operation. For example, your [infrastructure-as-code](/azure/cloud-adoption-framework/ready/considerations/infrastructure-as-code) (IaC) template might not match the number of instances that you need at the time of deployment. It might have an instance count of two, even though the current deployed environment is running eight instances. The deployment would remove six instances and negatively affect performance.

**Use a blue-green deployment strategy.** Deployments can cause service interruptions and downtime. To mitigate these issues, select a deployment strategy that minimizes performance impact, like a blue-green deployment. These approaches allow for seamless transitions between environments and reduce the risk of service disruptions. When you use the blue-green deployment approach, you have two separate environments: the blue and green environments. If any issues or performance degradation is detected in the green environment, you can easily roll back to the stable blue environment. This strategy helps you ensure minimal downtime and allows you to maintain a high level of performance for your workload. To deploy by using the blue-green approach, follow these general steps:

- *Deploy the new environment.* Set up the new environment (green) alongside the existing environment (blue) with the updated version of your application.

- *Validate the new environment.* Deployments can introduce latency and increase response times. Consider prewarming instances before cutover. Prewarming involves preparing the new environment by simulating production-like traffic and workload to ensure that the environment is ready to handle the expected load. It helps minimize the effects on latency and response times. Thoroughly test and validate the new environment to ensure that it functions correctly and meets performance expectations. Testing helps warm up caches, establish database connections, and ensure that the environment is ready to handle the expected load.

- *Gradually shift traffic.* After the new environment is prewarmed and validated, gradually shift production traffic from the old environment (blue) to the new environment (green). Initially, direct a small percentage of traffic to the green environment and gradually increase it after verifying its stability and expected application health. You can use a global load balancer or traffic management mechanism. The controlled traffic shifting allows you to identify any performance issues early and take corrective actions before fully transitioning the workload to the new environment.

- *Monitor and optimize.* Deployments might use shared computing resources. Continuously monitor the performance and health of the new environment after you shift traffic. Make any necessary optimizations or adjustments to ensure the desired performance and user experience.

- *Remove the old environment.* After you successfully transition all traffic to the green environment, remove the blue environment from existing connections. This step helps optimize the cost of maintaining the old environment and ensures that new environments are free of configuration drift.

- *Repeat the process.* For future deployments, reverse the roles of the blue and green environments. Deploy changes to the new blue environment, validate them, orchestrate traffic transition, and decommission the old green environment.

**Use multiple builds.** Different types of builds can help you optimize build times and ensure the quality of deployments. For example, you can have continuous integration (CI) builds that trigger with every code commit. You could have nightly builds that run automated tests regularly, and release builds that are used for deploying to production. Each type of build should have a specific purpose, like continuous integration, automated testing, or production deployment. Testing and validation of the workload before deployment help identify and address issues or bugs early in the development process.

**Consider feature flags.** Feature flags are used in software development to control the visibility and behavior of certain features in an application. By using feature flags, developers can enable or disable specific features without needing to redeploy the application. Feature flags work by introducing conditional logic in the code that determines whether a feature should be enabled or disabled. This logic can be based on various factors, like user roles, user preferences, or specific conditions that are defined by the development team. By using feature flags, developers can gradually roll out new features to a subset of users or enable features for specific groups for testing (canary testing).

### Optimize upgrades

An in-place upgrade is an upgrade to an existing resource or application. In-place  upgrades can temporarily slow down or interrupt a workload. It's important to ensure that upgrades are compatible with the workload. Before you apply an upgrade, we recommend that you test it in a separate environment to identify any potential issues. Provide a rollback plan in case any issues arise during the upgrade process. It's crucial to take a complete backup of critical data and configurations before you apply the upgrade. Monitor the upgraded system closely after the upgrade to ensure that everything functions as expected. The backup allows you to restore to a good state if you need to. You should prioritize scheduling the upgrade during off-peak hours to minimize the effect on users and workload performance. Notify users in advance about the planned upgrade, including the expected downtime and any necessary actions they need to take.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Waiting to perform operations activities during off-peak hours can affect operational efficiency. It might be less convenient to have the personnel with the right skill set work during off-peak hours.

### Optimize tooling

Essential tools for file integrity monitoring, virus scanning, intrusion detection, and other operational tasks can affect workload performance. They consume compute resources and can add latency and performance overhead. You need to test and understand the effects your tools have on workload performance. Based on the test results, you should fine-tune tool configurations, adjust scan frequency, and reallocate compute resources. For virus scanning, you could create a relevant exclusion list to minimize the duration of scans.

### Optimize database operations

Optimizing database operations refers to the process of refining and fine-tuning database tasks to ensure maximum efficiency and minimal resource utilization. These operations include tasks like backups, schema changes, performance tuning, and monitoring. Efficient database operations lead to faster query responses, reduced system overhead, and an overall smoother user experience. 

Schema changes involve modifying the structure of a database, such as adding or altering tables, columns, or indexes. These changes might require extra processing and resource utilization during the deployment process, potentially affecting the overall performance of the workload. Schema changes can disrupt performance to active queries, indexes, or transactions or cause data to be unavailable.

To minimize these effects, you should plan and test schema changes in a nonproduction environment. You can use various deployment techniques to implement schema updates. You should also use available schema changing tools to optimize the process. Archiving data and partitioning can help reduce the effects of schema changes.

### Optimize backups

Backups consume workload resources like processing power, network bandwidth, and disk I/O. You need to test and select a backup strategy that minimizes these effects. You should perform backups during off-peak hours when you can. Your strategy should include incremental backups instead of full backups each time. Snapshots can be less resource intensive than backups. You should consider built-in platform backup and restore features rather than building a custom solution. You need to test these options and use a combination that provides the best performance for your workload.

### Optimize monitoring and debugging

Excessive or poorly implemented logging, telemetry, instrumentation, and distributed tracing capture and collection can affect performance. Likewise, convenience features like remote debugging can also affect performance. You need to measure and know their performance effects on the environment. You don't want these processes to degrade performance. You should configure or disable any processes whose performance effects outweigh their benefits.

## Azure facilitation

**Accounting for operational tasks**: Azure DevOps is a set of development tools and services that enable teams to plan, develop, test, and deliver software efficiently. It includes features like version control, continuous integration and delivery, project management, and more. Azure provides service-to-service integration that minimizes the effects of many operational tasks. For example, services that integrate with Azure Key Vault often support seamless certificate rotation or secret rotation that minimizes effects on performance.

**Optimizing deployments**: App Service provides [deployment slots](/azure/app-service/deploy-staging-slots). You can use deployment slots to deploy code to a nonproduction environment. You can swap app content and configuration elements between two deployment slots. For example, you can switch app content from a nonproduction slot to the production slot.

Azure Front Door and Azure Traffic Manager enable you to implement a [blue-green deployment strategy](/azure/architecture/guide/aks/blue-green-deployment-for-aks). Some Azure compute services also support advanced deployment strategies like blue-green deployments. You can combine those services with your traffic shifting or instance warming strategy to mitigate the performance effects of deployment.

**Optimizing database operations**: [Azure SQL Database](/azure/azure-sql/database/automated-backups-overview) automatically takes full backups, differential backups, and transaction log backups. [Azure Cosmos DB](/azure/cosmos-db/online-backup-and-restore) automatically takes backups of your data at regular intervals. The automatic backups are taken without affecting the performance or availability of database operations. Azure Cosmos DB stores the backups in a separate storage service.

**Optimizing backups**: Some Azure data services support low-to-no performance impact for point-in-time recovery and indexing. Azure Backup is a reliable and scalable cloud-based backup solution that enables you to protect your data and applications. It provides features like incremental backups, compression, and encryption to minimize the effects on performance during backup operations. Azure Site Recovery helps you protect your applications by replicating them to a secondary location. It provides continuous replication and automated failover capabilities to minimize the downtime and performance impacts during backup and disaster recovery operations.

## Related links

- [Infrastructure as code](/azure/cloud-adoption-framework/ready/considerations/infrastructure-as-code)
- [Deployment slots](/azure/app-service/deploy-staging-slots)
- [Blue-green deployment strategy](/azure/architecture/guide/aks/blue-green-deployment-for-aks)
- [Azure SQL Database](/azure/azure-sql/database/automated-backups-overview)
- [Azure Cosmos DB](/azure/cosmos-db/online-backup-and-restore)

## Performance Efficiency checklist  

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Performance Efficiency checklist](checklist.md) 
