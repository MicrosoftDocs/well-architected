---
title: Recommendations for designing a disaster recovery strategy
description: Learn how to design a disaster recovery strategy for a workload. 
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for designing a disaster recovery strategy

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

|[RE:08](checklist.md)|Implement structured, tested, and documented business continuity and disaster recovery (BCDR) plans that align with the recovery targets. Plans must cover all components and the system as a whole.   |
|---|---|

This guide describes recommendations for designing a reliable disaster recovery strategy for a workload. To meet internal service-level objectives (SLOs) or even a service-level agreement (SLA) that you have guaranteed for your customers, you must have a robust and reliable disaster recovery strategy. Failures and other major issues are expected. Your preparations to deal with these incidents determine how much your customers can trust your business to reliably deliver for them. A disaster recovery strategy is the backbone of preparation for major incidents.

**Definitions**

|Term  |Definition  |
|---------|---------|
|Failover     | The automated and/or manual shifting of production workload traffic from an unavailable region to an unaffected geographical region.        |
|Failback     | The automated and/or manual shifting of production workload traffic from a failover region back to the primary region.        |

## Key design strategies

This guide assumes that you have already performed the following tasks as part of your reliability planning:

-   Identify [critical and noncritical flows](identify-flows.md).

-   Perform [failure mode analysis (FMA)](failure-mode-analysis.md) for your flows. 

-   Identify [reliability targets](metrics.md).

-   Design for reliability through [redundancy](redundancy.md), [scaling](scaling.md), [self-preservation, and self-healing](self-preservation.md).

-   Design a robust [testing strategy](testing-strategy.md).

A reliable disaster recovery (DR) strategy builds on the foundation of a reliable workload architecture. Address reliability at every stage of building your workload to ensure that necessary pieces for optimized recovery are in place before you start designing your DR strategy. This foundation ensures that your workload's reliability targets, like recovery time objective (RTO) and recovery point objective (RPO), are realistic and achievable.

## Maintain a disaster-recovery plan

The cornerstone of a reliable DR strategy for a workload is the *DR plan*. Your plan should be a living document that's routinely reviewed and updated as your environment evolves. Present the plan to the appropriate teams (operations, technology leadership, and business stakeholders) regularly (every six months, for example). Store it in a highly available, secure data store such as OneDrive for Business. 

Follow these recommendations to develop your DR plan:

-   Clearly define what constitutes a disaster and therefore requires activation of the DR plan.

    -   Disasters are large-scale issues. They might be regional outages, outages of services like Microsoft Entra ID or Azure DNS, or severe malicious attacks like ransomware attacks or DDoS attacks.

    -   Identify failure modes that aren't considered disasters, such as the failure of a single resource, so that operators don't mistakenly invoke their DR escalations.

-   Build the DR plan on your FMA documentation. Ensure that your DR plan captures the failure modes and mitigation strategies for outages that are defined as disasters. Update both your DR plan and your FMA documents in parallel so they're accurate when the environment changes or when testing uncovers unexpected behaviors.

    -   Whether you develop DR plans for nonproduction environments depends on your business requirements and cost impacts. For example, if you offer quality-assurance (QA) environments to certain customers for prerelease testing, you might want to include those environments in your DR planning.

-   Clearly define roles and responsibilities within the workload team and understand any related external roles within your organization. Roles should include:

    -   The party responsible for declaring a disaster.

    -   The party responsible for declaring incident closure.

    -   Operations roles.

    -   Testing and validation roles.

    -   Internal and external communications roles.

    -   Retrospective and root-cause analysis (RCA) lead roles.

-   Define the escalation paths that the workload team must follow to ensure that recovery status is communicated to stakeholders.

-   Capture component-level recovery procedures, data estate-level recovery, and workload-wide recovery processes. Include a prescribed order of operations to ensure that components are recovered in the least impactful way. For example, recover and check databases before you recover the application.

    -   Detail each component-level recovery procedure as a step-by-step guide. Include screenshots if possible.

    -   Define your team's responsibilities versus your cloud hosting provider's responsibilities. For example, Microsoft is responsible for restoring a PaaS (platform as a service), but you're responsible for rehydrating data and applying your configuration to the service.

    -   Include prerequisites for running the procedure. For example, list the required scripts or credentials that need to be gathered.

    -   Capture the root cause of the incident and perform mitigation before you start recovery. For example, if the cause of the incident is a security issue, mitigate that issue before you recover the affected systems in your failover environment.

-   Depending on the [redundancy design](highly-available-multi-region-design.md) for your workload, you might need to do significant post-failover work before you make the workload available to your customers again. Post-failover work could include DNS updates, database connection string updates, and traffic routing changes. Capture all of the post-failover work in your recovery procedures.

    > [!NOTE]
    > Your redundancy design might allow you to automatically recover from major incidents fully or partially, so be sure that your plan includes processes and procedures around these scenarios. For example, if you have a fully active-active design that spans [availability zones or regions](highly-available-multi-region-design.md), you might be able to transparently fail over automatically after an availability zone or regional outage and minimize the steps in your DR plan that need to be performed. Similarly, if you designed your workload by using [deployment stamps](/azure/architecture/patterns/deployment-stamp), you might suffer only a partial outage if the stamps are deployed zonally. In this case, your DR plan should cover how to recover stamps in unaffected zones or regions.

-   If you need to redeploy your app in the failover environment, use tooling to automate the deployment process as much as possible. Ensure that your DevOps pipelines have been predeployed and configured in the failover environments so that you can immediately begin your app deployments. Use automated end-to-end deployments, with manual approval gates where necessary, to ensure a consistent and efficient deployment process. The full deployment duration needs to align with your recovery targets.

    -   When a stage of the deployment process requires manual intervention, document the manual steps. Clearly define roles and responsibilities.

-   Automate as much of the procedure as you can. In your scripts, use declarative programming because it allows idempotence. When you can't use declarative programming, be mindful about developing and running your custom code. Use retry logic and circuit breaker logic to avoid wasting time on scripts that are stuck on a broken task. Because you run these scripts only in emergencies, you don't want incorrectly developed scripts to cause more damage or slow down your recovery process.

    > [!NOTE]
    > Automation poses risks. Trained operators need to monitor the automated processes carefully and intervene if any process encounters issues. To minimize the risk that automation will react to false positives, be thorough in your DR drills. Test all phases of the plan. Simulate detection to generate alerting, and then move through the entire recovery procedure. 
    > 
    > Remember that your DR drills should validate or inform updates to your recovery target metrics. If you find that your automation is susceptible to false positives, you might need to increase your failover thresholds.

-   Separate the failback plan from the DR plan to avoid potential confusion with the DR procedures. The failback plan should follow all of the DR plan's development and maintenance recommendations and should be structured in the same way. Any manual steps that were necessary for failover should be mirrored in the failback plan. Failback might happen quickly after failover, or it might take days or weeks. Consider failback as separate from failover.

    -   The need to fail back is situational. If you're routing traffic between regions for performance reasons, failing back the load originally in the failed-over region is important. In other cases, you might have designed your workload to function fully regardless of which production environment it's located in at any time.

## Conduct disaster-recovery drills

A DR testing practice is as important as a well-developed DR plan. Many industries have compliance frameworks that require a specified number of DR drills to be performed regularly. Regardless of your industry, regular DR drills are paramount to your success. 

Follow these recommendations for successful DR drills:

-   Perform at least one production DR drill per year. Tabletop (dry run) drills or nonproduction drills help ensure that the involved parties are familiar with their roles and responsibilities. These drills also help operators build familiarity ("muscle memory") by following recovery processes. But only production drills truly test the validity of the DR plan and the RTO and RPO metrics.

-   Use tabletop drills not only to build familiarity for seasoned operators but also to educate new operators about DR processes and procedures. Senior operators should take time to let new operators perform their role and should watch for improvement opportunities. If a new operator is hesitant or confused by a step in a procedure, review that procedure to ensure that it's clearly written.

## Azure facilitation

Many Azure products have built-in failover capabilities. Familiarize yourself with these capabilities and include them in recovery procedures. 

For IaaS (infrastructure as a service) systems, use [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) to automate failover and recovery. Refer to the following articles for common PaaS products:

- [Azure App Service](/azure/app-service/overview-disaster-recovery)

- [Azure Container Apps](/azure/container-apps/disaster-recovery?tabs=azure-cli)

- [Azure Kubernetes Service](/azure/aks/operator-best-practices-multi-region)

- [Azure SQL Database](/azure/azure-sql/database/disaster-recovery-guidance)

- [Azure Event Hubs](/azure/event-hubs/event-hubs-geo-dr?tabs=portal)

- [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-how-to-geo-replication)

## Tradeoffs

Performing DR drills in production can cause unexpected catastrophic failures. Be sure to test recovery procedures in nonproduction environments during your initial deployments. 

Give your team as much maintenance time as possible during drills. When planning for maintenance time, use the recovery metrics that you capture during [testing](testing-strategy.md) as *minimum time necessary* allotments. 

As your DR drill practices mature, you learn which procedures you can run in parallel and which you must run in sequence. Early in your drill practices, assume that every procedure must be run in sequence and that you need extra time in each step to handle unanticipated issues.

## Example 

See the [DR for Azure data platform series](/azure/architecture/data-guide/disaster-recovery/dr-for-azure-data-platform-overview) for guidance about preparing an enterprise data estate for DR.

## Related links

-   [Recommendations for designing for redundancy](redundancy.md)

-   [Recommendations for highly available multi-region design](highly-available-multi-region-design.md)

-   [Recommendations for using availability zones and regions](regions-availability-zones.md)

## Reliability checklist  

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Reliability checklist](checklist.md) 
