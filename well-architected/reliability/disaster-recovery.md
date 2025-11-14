---
title: Architecture strategies for designing a disaster recovery strategy
description: Learn how to design a disaster recovery strategy for a workload. 
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: concept-article
---

# Architecture strategies for designing a disaster recovery strategy

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

|**RE:09**|Implement structured, tested, and documented business continuity and disaster recovery (BCDR) plans that align with the recovery targets. Plans must cover all components and the system as a whole.   |
|---|---|

This guide describes recommendations for designing a reliable disaster recovery strategy for a workload. To meet internal service-level objectives (SLOs) or even a service-level agreement (SLA) that you have guaranteed for your customers, you must have a robust and reliable disaster recovery strategy. Failures and other major issues are expected. Your preparations to deal with these incidents determine how much your customers can trust your business to reliably deliver for them. A disaster recovery strategy is the backbone of preparation for major incidents.

**Definitions**

|Term  |Definition  |
|---------|---------|
|Failover     | The automated and/or manual shifting of production workload traffic from an unavailable region to an unaffected geographical region.        |
|Failback     | The automated and/or manual shifting of production workload traffic from a failover region back to the primary region.        |


This guide assumes that you have already performed the following tasks as part of your reliability planning:

-   Identify [critical and noncritical flows](identify-flows.md).

-   Perform [failure mode analysis (FMA)](failure-mode-analysis.md) for your flows. 

-   Identify [reliability targets](metrics.md).

-   Design for reliability through [redundancy](redundancy.md), [scaling](scaling.md), [self-preservation, and self-healing](self-preservation.md).

-   Design a robust [testing strategy](testing-strategy.md).

A reliable disaster recovery (DR) strategy builds on the foundation of a reliable workload architecture. Address reliability at every stage of building your workload to ensure that necessary pieces for optimized recovery are in place before you start designing your DR strategy. This foundation ensures that your workload's reliability targets, like recovery time objective (RTO) and recovery point objective (RPO), are realistic and achievable.

## Factor in the criticality 

Categorize your workload by criticality tiers defined by your organization, such as mission-critical, business-critical, business operational, and so on. Recognize that each tier warrants a distinct level of investment, resilience, and recovery sequencing. If the workload contains multiple flows or components with varying criticalities, document the recovery approach for each to avoid ambiguity during an event. 

These criticality tiers influence the appropriate recovery objectives. Higher-criticality components demand faster recovery and more frequent data protection, while lower-criticality components can tolerate slower restoration. From these requirements, [derive clear RTO and RPO](./metrics.md#define-recovery-metrics) targets that align recovery expectations directly with business value.

## Set clear criteria DR activation

Make sure the team and business stakeholders understand exactly what counts as a disaster and what doesn't. Your strategy must differenciate between a full disaster, a major disruption, and a small issue that can be fixed quickly. Don't base this only on which components are failing. Instead, factor in how much the problem impacts users and the business as a whole.

After you define the thresholds that separate minor incidents from true DR situations, build them into your [health model](../design-guides/health-modeling.md). This way, the monitoring can spot early warning signs and the appropriate recovery processes are triggered. 

## Plan for communication and escalation

Without clear communication, even the best-designed disaster recovery plan can break down. Build a clear communication strategy that defines who makes decisions, who gets informed, and how information flows during a DR event. 

Start by outlining roles and responsibilities within the workload team, as well as any external groups involved. These should include owners for declaring a disaster, closing an incident, running operations tasks, performing testing and validation, managing internal and external communication, and leading retrospectives or root-cause analysis.

Establish cross-functional war rooms to ensure the right people can coordinate quickly, and prepare communication channels and message templates in advance so teams aren't improvising under pressure. 

 Define the escalation paths that the workload team must follow to ensure that recovery status is communicated to stakeholders.

## Test the DR plan as a routine practice

Testing the DR plan works in real-world situations. Test multiple scenarios, including unlikely edge cases, and combine scheduled drills with surprise to see how systems and teams respond under pressure.

In your DR plan, include procedures and cadence for both tabletop exercises (dry runs in non-production) and production-level drills. Tabletop drills help the team practice their roles, build familiarity, and train new members, while production drills are the only way to truly verify that your plan meets RTO and RPO targets in real conditions. For processes outside your control, like DNS propagation, validates potential delays when evaluating recovery times.

Have a process in place that treats test results as inputs to improve the overall DR posture. For example, if a new operator is hesitant, review that procedure to ensure that it's clearly written.

## Maintain the DR plan

Treat the DR plan as a living document. Your plan should evolve as your environment changes and should be reviewed regularly with all relevant teams, operations, technology leadership, and business stakeholders, ideally every six months. Store it securely in a highly available location so it's always accessible when needed.

The DR plan should supplement your FMA documentation, capturing how the system behaves during disasters and how to respond. As you discover new failure cases, through testing, monitoring, or real incidents, update your plan to include them. Ensure both your DR plan and FMA documentation are updated whenever the environment changes or testing uncovers unexpected behaviors.

Reflect architectural changes. Whenever the workload architecture changes, update the DR plan to clearly define any adjustments to activation criteria or recovery processes.

Plan realistic maintenance time. Use metrics from testing as the minimum time needed for recovery steps when scheduling drills or maintenance.

Refine procedures over time. Early in your DR practices, assume each procedure must run in sequence and allow extra time for unforeseen issues. As drills mature, identify which steps can safely run in parallel.

## Align recovery with the architecture design

Your disaster recovery process should be designed with the architecture of your workloads in mind. Align your plan with architectural design: active-active deployments, zonal redundancy, or  [deployment stamps](/azure/architecture/patterns/deployment-stamp) may allow partial or automated recovery. For more information, see [Architecture strategies for using availability zones and regions](../design-guides/regions-availability-zones.md). 

Layer your recovery processes. Structure procedures at the component level, data estate level, and workload level. Define the proper sequence to minimize impact. For example, restore databases before applications that depend on them.

Define scope based on business impact. Decide which environments, production and, if necessary, non-production, require DR coverage, considering customer impact and cost.

Automate deployment and recovery procedures in failover environments wherever possible, ensuring they meet RTO targets. Use declarative, idempotent scripts for reliability, and build safeguards like retry and circuit-breaker logic for any custom code. 

Use automation to redeploy applications in failover environments wherever possible. Predeploy and configure DevOps pipelines so deployments can start immediately, using automated end-to-end processes with manual approval gates only when needed. Ensure deployment timelines align with your recovery targets.

Apply manual approvals  when necessary to balance speed with control. When manual steps are required, document them clearly and define roles and responsibilities.

> [!NOTE]
> Automation poses risks. Even with automation, trained operators must oversee recovery processes and intervene if issues arise. Use thorough DR drills to test every phase, validate recovery targets, and adjust failover thresholds to reduce the risk of false positives.

Detail procedures clearly. Provide step-by-step instructions, prerequisites (scripts, credentials, configurations), and assigned responsibilities between your team and cloud providers. Include root-cause mitigation before recovery to prevent recurring failures.

Plan for post-failover work. Capture all tasks needed after failover, such as DNS updates, traffic routing, and connection string adjustments, to bring the workload fully back online.


## Keep failback strategy separate from failover
> [!IMPORTANT]
> Failback is a deliberate, context-driven process, not merely a reversal of failover. Keep failback distinct from the DR plan to prevent confusion. Build and maintain it using the same principles as your DR plan, including mirroring any manual steps from failover. Failback may occur immediately or over days or weeks—treat it as a separate process.
>
> The need to fail back depends on the situation: for example, if traffic was rerouted between regions for performance, restoring it to the original region is important. In other cases, workloads may function fully regardless of which environment is active.


### Considerations

- Performing DR drills in production can cause unexpected catastrophic failures. Be sure to test recovery procedures in nonproduction environments during your initial deployments.

- Give your team as much maintenance time as possible during drills. When planning for maintenance time, use the recovery metrics that you capture during [testing](testing-strategy.md) as *minimum time necessary* allotments.

- As your DR drill practices mature, you learn which procedures you can run in parallel and which you must run in sequence. Early in your drill practices, assume that every procedure must be run in sequence and that you need extra time in each step to handle unanticipated issues.

## Define and maintain Backup Plans for resources within critical flows

Backup is an important part of your overall recovery process. Oftentimes it is just a part of your environment that needs recovery. DR plans are usually application or even region wide. Accidental or malicious deletion of data, file corruption, malware, and targeted ransomware attacks can all affect the availability of your workload. Having solid backup plans for each part of your environment is just as important as having an effective DR plan, as a DR plan depends on a solid backup plan to be effective. Like your DR plan, backup plans also need to be agreed upon by the appropriate levels of management, revisited regularly for possible updates and documented in a highly available, secure data store.

-   Determine appropriate backup solutions for the different Azure services that are part of the critical paths within your workload.

-   Define required retention periods for each different service.

-   Understand that one tool may not work for everything. Azure Backup tools can cover many resource types but not all.

-   Sometimes the best option to restore certain types of objects is a redeployment from some level type of highly available repository. (Azure DevOps, GitHub, or others)

-   Data services have different requirements than application related objects.

-   Be sure to consider a multi-region storage strategy for your backup data to create cross-region recoverability.

-   Run regular, scheduled test restores of backup data to ensure that services are working as expected.

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

-   [Recommendations for designing for redundancy](redundancy.md)

-   [Recommendations for using availability zones and regions](../design-guides/regions-availability-zones.md)

## Reliability checklist  

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Reliability checklist](checklist.md) 
