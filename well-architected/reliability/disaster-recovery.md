---
title: Architecture strategies for designing a disaster recovery strategy
description: Learn how to design a disaster recovery strategy for a workload. 
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: concept-article
---

# Architecture strategies for developing disaster recovery plans

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

|**RE:09**|Implement structured, tested, and documented disaster recovery (DR) plans that align with the recovery targets. Plans must cover all components and the system as a whole.   |
|---|---|

Disasters are significant incidents that require careful planning and proactive preparation by workloads and operations teams.

This article outlines the key strategies for disaster recovery, emphasizing resilient workload design, data integrity, and clearly defined objectives for failover and recovery. It focuses on the purpose and guiding principles of DR rather than step-by-step procedures. For detailed implementation guidance, including stepwise processes and playbooks, see the companion article: [Develop a disaster recovery plan for multi-region deployments](../design-guides/disaster-recovery.md).

**Definitions**

|Term  |Definition  |
|---------|---------|
|Active-passive cold standby | A DR deployment pattern where the secondary region has minimal or no infrastructure running until a disaster occurs, requiring full deployment during failover. |
|Active-passive warm standby | A DR deployment pattern where the secondary region has some infrastructure pre-deployed and running at reduced capacity, enabling faster failover than cold standby. |
|Backup | A copy of data stored separately from the primary system to enable data recovery if loss, corruption, or disaster occurs |
|Business criticality | The classification of workloads or components based on their importance to business operations, influencing recovery priorities and investment levels. |
|DR activation criteria | Predefined thresholds and conditions that determine when to declare a disaster and trigger recovery procedures. |
|DR drill | A planned exercise to test disaster recovery procedures and validate recovery capabilities under controlled conditions. |
|Failback     | The automated and/or manual shifting of production workload traffic from a failover region back to the primary region.        |
|Failover     | The automated and/or manual shifting of production workload traffic from an unavailable region to an unaffected geographical region.        |
|Point-in-time recovery | The ability to restore data to a specific moment in time, typically used to recover from data corruption or accidental changes. |
|Recovery Point Objective (RPO) | The maximum acceptable amount of data loss measured in time, defining how much data a business can afford to lose during a disaster. |
|Recovery Time Objective (RTO) | The maximum acceptable time to restore business operations after a disaster occurs. |
|Synchronous replication | Data replication where changes are written to multiple locations simultaneously, ensuring zero data loss but potentially higher latency. |
|Asynchronous replication | Data replication where changes are written to the primary location first and then copied to secondary locations, allowing for some data loss but lower latency. |
|War room | A centralized location or communication channel where key personnel coordinate during disaster recovery operations. |

## Prioritize by business impact

Categorize your workload by criticality tiers defined by your organization, such as mission-critical, business-critical, business operational, and so on. Recognize that each tier warrants a distinct level of investment, resilience, and recovery sequencing. If the workload contains multiple flows or components with varying criticalities, document the recovery approach for each to avoid ambiguity during an event. 

These criticality tiers influence the appropriate recovery objectives. Higher-criticality components demand faster recovery and more frequent data protection, while lower-criticality components can tolerate slower restoration. From these requirements, [derive clear RTO and RPO](./metrics.md#define-recovery-metrics) targets that align recovery expectations directly with business value.

## Define disaster thresholds

Make sure the team and business stakeholders understand exactly what counts as a disaster and what doesn't. Your strategy must differentiate between a full disaster, a major disruption, and a small issue that can be fixed quickly. Don't base this only on which components are failing. Instead, factor in how much the problem impacts users and the business as a whole.

After you define the thresholds that separate minor incidents from true DR situations, build them into your [health model](../design-guides/health-modeling.md). This way, the monitoring can spot early warning signs and the appropriate recovery processes are triggered. 

## Establish communication protocols

Without clear communication, even the best-designed disaster recovery plan can break down. Build a clear communication strategy that defines who makes decision, who gets informed, and how information flows during a DR event. 

Start by outlining roles and responsibilities within the workload team, as well as any external groups involved. These should include owners for declaring a disaster, closing an incident, running operations tasks, performing testing and validation, managing internal and external communication, and leading retrospectives or root-cause analysis.

Provide step-by-step instructions, list all prerequisites (such as scripts, credentials, and configurations), and define responsibilities between your team and the cloud provider. Ensure root-cause issues are addressed before recovery begins to prevent repeated failures.

Establish cross-functional war rooms to ensure the right people can coordinate quickly, and prepare communication channels and message templates in advance so teams aren't improvising under pressure. 

Define the escalation paths that the workload team must follow to ensure that recovery status is communicated to stakeholders.

## Design recovery-aware architecture

Your disaster recovery process should reflect how your workload is designed and, conversely, your recovery requirements should influence architectural decisions from the start. When designing your systems, consider how you intend to recover during a disaster and choose patterns (such as [active-passive cold standby](../design-guides/disaster-recovery.md#recovery-strategy-for-active-passive-cold-standby) or [active-passive warm standby](../design-guides/disaster-recovery.md#recovery-strategy-for-active-passive-warm-standby)) that ensure you can fully restore at least your critical paths. 

For data recovery, use replication strategies based on criticality. For example, synchronous across availability zones for high-priority data, asynchronous across regions for lower priority. Ensure consistency models support recovery, implement frequent full backups and point-in-time recovery, account for dependencies between data stores, and automate integrity checks during recovery.

> [!NOTE]
> Design workloads with containment and isolation to allow partial failover of individual components. This reduces complexity, cost, and RTO, and may maintain partial availability without declaring a full disaster. Test partial failovers separately and document when they should be triggered.

Prepare the secondary region in advance. Build a checklist so that the team is ready when an incident occurs, like: 

- Compute layer: For warm active/passive workloads, predeploy minimal compute resources to support rapid failover. 
- Network infrastructure: Replicate topology, including VNets, subnets, routes, firewalls, and security groups, and confirm all configurations.
- Identity and access management:  Duplicate account setups, RBAC permissions, and policies, ensuring the same security baseline as production.
- Monitoring: Deploy monitoring infrastructure with alerts and dashboards configured for visibility. 

Layer your recovery processes. Structure procedures at the component level, data estate level, and workload level. Define the proper sequence to minimize impact. For example, restore databases before applications that depend on them. Define scope based on business impact. Decide which environments, production and, if necessary, non-production, require DR coverage, considering customer impact and cost.

Keep failback strategy separate from failover.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** The need to fail back depends on the situation: for example, if traffic was rerouted between regions for performance, restoring it to the original region is important. In other cases, workloads may function fully regardless of which environment is active. If failback is not treated as a distinct, well-defined process separate from failover, teams may experience confusion, incomplete restoration, or prolonged downtime. 
>
> To mitigate that risk, create a failback plan, build and maintain your failback plan using the same principles as your DR plan, including mirroring any manual steps from failover. Failback may occur immediately or over days or weeks but treat it as a separate process.

Plan for post-failover work. Capture all tasks needed after failover, such as DNS updates, traffic routing, and connection string adjustments, to bring the workload fully back online.

## Implement robust backup strategies

Choose backup solutions tailored to each Azure service, define retention periods, and recognize that no single tool covers everything. Consider multi-region storage for cross-region recoverability, and for some resources, use redeployment from highly available repositories. Regularly test restores to validate backups, and review and update plans periodically, storing them securely and making them accessible to relevant teams.


## Practice with regular drills

A DR plan is only meaningful when validated under realistic conditions. Test multiple scenarios, including edge cases, and combine scheduled drills with surprise game days to see how systems and teams respond under pressure.

In your DR plan, include procedures and cadence for both tabletop exercises (dry runs in non-production) and production-level drills. Tabletop drills help the team practice their roles, build familiarity, and train new members, while production drills are the only way to verify that your plan meets RTO and RPO targets in real conditions. For processes outside your control, like DNS propagation, validates potential delays when evaluating recovery times.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Performing DR drills directly in production can introduce unexpected, potentially severe failures. Test recovery procedures in non-production environments first to validate safety and uncover issues before running drills in production.

Have a process in place that treats test results as inputs to improve the overall DR posture. For example, if a new operator is hesitant, review that procedure to ensure that it's clearly written.

Test and verify both overall and component-level RTO and RPO separately from full DR drills. Include scenarios such as moving data across regions or restoring from cold storage to ensure targets are achievable.

## Keep plans current and actionable

Treat the DR plan as a living document. Your plan should evolve as your environment changes and should be reviewed regularly with all relevant teams, operations, technology leadership, and business stakeholders, ideally every six months. 

The DR plan should supplement your FMA documentation, capturing how the system behaves during disasters and how to respond. As you discover new failure cases, through testing, monitoring, or real incidents, update your plan to include them. Ensure both your DR plan and FMA documentation are updated whenever the environment changes or testing uncovers unexpected behaviors. 

Refine procedures over time. Early in your DR practices, assume each procedure must run in sequence and allow extra time for unforeseen issues. As drills mature, identify which steps can safely run in parallel. Refinement should also capture architectural changes. Whenever the workload architecture changes, update the DR plan to clearly define any adjustments to activation criteria or recovery processes.

Plan for realistic recovery times. Use metrics from testing as the minimum time needed for recovery steps when scheduling drills or maintenance.

## Ensure accessibility during outages

Disaster recovery succeeds only when both the plan and the tools needed to execute it remains available under all failure conditions.

Store disaster recovery documentation, scripts, and recovery components in highly available, secure locations so they remain accessible even during regional outages. Protect all DR assets, including plans, credentials, certificates, and scripts, and replicate them across regions. Maintain offline or printed copies for worst case scenarios. Predeploy CI/CD pipelines in every region so they're ready to run immediately when needed.

## Automate with confidence

Automate deployment and recovery procedures in failover environments wherever possible, ensuring they meet RTO targets. Use declarative, idempotent scripts for reliability, and build safeguards like retry and circuit-breaker logic for any custom code. Predeploy and configure DevOps pipelines so deployments can start immediately, using automated end-to-end processes with manual approval gates only when needed. Ensure deployment timelines align with your recovery targets.

Apply manual approvals when necessary to balance speed with control. When manual steps are required, document them clearly and define roles and responsibilities.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Automation poses risks. Even with automation, trained operators must oversee recovery processes and intervene if issues arise. Use thorough DR drills to test every phase, validate recovery targets, and adjust failover thresholds to reduce the risk of false positives.

## Azure facilitation

Many Azure products have built-in failover capabilities. Familiarize yourself with these capabilities and include them in recovery procedures. See the [DR for Azure data platform series](/azure/architecture/data-guide/disaster-recovery/dr-for-azure-data-platform-overview) for guidance about preparing an enterprise data estate for DR.

For IaaS (infrastructure as a service) systems, use [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) to automate failover and recovery. Refer to the following articles for common PaaS products:

- [Azure App Service](/azure/app-service/overview-disaster-recovery)
- [Azure Container Apps](/azure/container-apps/disaster-recovery?tabs=azure-cli)
- [Azure Kubernetes Service](/azure/aks/operator-best-practices-multi-region)
- [Azure SQL Database](/azure/azure-sql/database/disaster-recovery-guidance)
- [Azure Event Hubs](/azure/event-hubs/event-hubs-geo-dr?tabs=portal)
- [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-how-to-geo-replication)

Many Azure products have built-in backup capabilities. Familiarize yourself with these capabilities and include them in recovery procedures.

For IaaS (infrastructure as a service) systems, use [Azure Backup](/azure/backup/backup-overview) to facilitate backup of VMs and VM related services and some data services. Refer to the following articles for common products:

- [Azure App Service](/azure/app-service/manage-backup)
- [Azure Kubernetes Service](/azure/backup/azure-kubernetes-service-cluster-backup)
- [Azure SQL Database](/azure/azure-sql/database/recovery-using-backups)
- [Azure Files](/azure/backup/backup-azure-files?tabs=recovery-services-vault)

## Related links

- [Develop a disaster recovery plan for multi-region deployments](../design-guides/disaster-recovery.md)

- [Recommendations for designing for redundancy](redundancy.md)

- [Recommendations for using availability zones and regions](../design-guides/regions-availability-zones.md)

## Reliability checklist  

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Reliability checklist](checklist.md)