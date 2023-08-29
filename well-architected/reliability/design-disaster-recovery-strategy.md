---
title: Recommendations for designing a disaster recovery strategy
description: Learn how to design a disaster recovery strategy.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for designing a disaster recovery strategy

**Applies to RE:09**

This guide describes the best practices for designing a reliable disaster recovery strategy for a workload. In order to meet internal SLOs set or even an SLA that you have guaranteed for your customers, you must have a robust and reliable disaster recovery strategy. Failures and other major issues are expected and the preparation you take to deal with these incidents will determine the amount of trust your customers have in your business to reliably deliver for them. A disaster recovery strategy is the backbone of preparation for major incidents.

**Definitions**


|Term  |Definition  |
|---------|---------|
|Failover     | The automated and/or manual shifting of production workload traffic from an unavailable region to an unaffected geographical region        |
|Failback     | The automated and/or manual shifting of production workload traffic from a failover region back to the primary region        |

## Key design strategies

This guide assumes that you have already performed the following tasks as part of your reliability planning:

-   Identified critical and non-critical flows (link to RE:01)

-   Performed failure mode analysis (FMA) for your flows (link to RE:02)

-   Identified reliability targets (link to RE:03)

-   Designed for reliability through redundancy (link to RE:04), scaling (link to RE:05), self-preservation (link to RE:06) and self-healing (link to RE:07)

-   Designed a robust testing strategy (link to RE:08)

A reliable disaster recovery (DR) strategy builds on a strong foundation achieved through a reliable workload architecture. Addressing reliability at every stage of building your workload will ensure that necessary pieces for optimized recovery are already in place before you start designing your DR strategy and will help ensure that reliability targets (like RTO and RPO) that have been defined for your workload are realistic and achievable.

The cornerstone of a reliable DR strategy for a workload is the DR plan. Your plan should be a "living" document that is routinely reviewed and updated as your environment evolves. The plan should be presented to appropriate teams (operations, technology leadership and business stakeholders) on a regular basis (every six months, for example) and it should be stored in a highly available, secure data store (OneDrive for Business, for example). Use the following recommendations to develop your DR plan:

-   Clearly define what constitutes a disaster, and therefore requires activation of the DR plan.

    -   Typically disasters are large-scale issues like regional outages, service outages (like Azure AD or Azure DNS), or severe malicious attacks (like ransomware or DDoS attacks)

    -   It is also helpful to identify failure modes that are not considered disasters (like a single resource failing, for example) so that operators do not mistakenly invoke their DR escalations

-   The DR plan should build upon your FMA documentation; refer back to the FMA documentation to ensure that your DR plan captures the failure modes and mitigation strategies for outages that are defined as disasters. As such, both sets of documents need to be updated in parallel for accuracy when the environment changes or when testing uncovers previously unexpected behaviors

    -   Whether you develop DR plans for non-production environments depends on your business requirements and cost impacts. For example, there are some scenarios that may require DR testing happens in non-production environments, or you may offer QA environments to certain customers for pre-release testing and there may be value in including that in your DR planning.

-   Clearly define roles and responsibilities within the workload team and understand any related external roles within your organization. Roles should include:

    -   Party responsible for declaring a disaster

    -   Party responsible for declaring incident closure

    -   Operations roles

    -   Testing and validation roles

    -   Internal and external communications roles

    -   Retrospective and RCA lead roles

-   Define the escalation paths that the workload team will follow to ensure that recovery status is communicated to stakeholders

-   Capture component-level recovery procedures, data estate-level recovery, and workload-wide recovery processes including a prescribed order of operations to ensure that components are recovered in the least impactful way (for example, databases are recovered and checked before the application is recovered).

    -   Each component-level recovery procedures should be detailed as a step-by-step guide with screenshots, if possible

    -   Include your team's responsibilities vs your cloud hosting provider's responsibilities (for example, Microsoft is responsible for restoring a PaaS service, but you are responsible for rehydrating data and applying your configuration to that service)

    -   Include prerequisites for running the procedure (for example, required scripts or credentials that need to be gathered)

    -   Capture the root cause of the incident and perform mitigation before recovery is started. For example, if the cause of the incident is a security issue, that issue must be mitigated before recovering the affected systems in your failover environment.

-   Depending on the redundancy design (link to re04-active-passive) for your workload, you may have significant post-failover work to do before making the workload available to your customers again. Be sure to capture all of the post-failover work in your recovery procedures. Post-failover work could include things like DNS updates, database connection string updates and traffic routing changes.

    > [!NOTE]
    > Your redundancy design may allow you to automatically recover from major incidents fully or partially, so be sure that your plan has processes and procedures documented specifically around these scenarios. For example, if you have a fully active/active design that spans availability zones (link to re04 az guide) or regions (link to re04-active-passive) you may be able to transparently failover automatically after a availability zone or regional outage and minimize the steps in your DR plan that need to be performed. Similarly, if you have designed your workload using [deployment stamps](https://learn.microsoft.com/azure/architecture/patterns/deployment-stamp), you may only suffer a partial outage if the stamps are deployed zonally. In this case, your DR plan should cover how to recover stamps in unaffected zones or regions.

-   If you need to redeploy your app in the failover environment, use tooling to automate the deployment process as much as possible. Ensure that your DevOps pipelines have been pre-deployed and configured in the failover environments, so that you are able to immediately begin your app deployments. Automated end-to-end deployments, with manual approval gates where necessary, should be used to ensure a consistent and efficient deployment process. The time it takes for a full deployment needs to align with your recovery targets.

    -   When manual intervention is required at any stage of the deployment process, those manual steps must be documented with roles and responsibilities clearly defined.

-   Try to automate as much of the procedure as you can. In your scripts, use declarative programming as it allows idempotence. When using declarative programming isn't possible, be mindful about developing and executing your custom code., Make use of retry and circuit breaker logic to ensure that you don't waste time waiting on scripts that are stuck on a broken task. You will only run these scripts in emergency situations, so make sure that incorrectly developed scripts will not cause additional damage or slow down your recovery process.

    > [!NOTE]
    > Automation comes with risks that you should be mindful of. Trained operators need to monitor the automated processes carefully and be ready to intervene if any process encounters issues, Also, to minimize the risk of automation reacting to false positives, be thorough in your DR drills, testing all phases of the plan: simulate detection to generate alerting and then move through the entire recovery procedure. Remember that your DR drills should validate or inform the updating of your recovery target metrics, so if you find that your automation is susceptible to false positives, you may need to increase your failover thresholds.

-   It is best to separate the failback plan from the DR plan to avoid any potential confusion with the DR procedures. The failback plan should follow all of the same development and maintenance best practices as the DR plan and should be structured in the same way. Ensure that any manual steps that were necessary for failover are mirrored in the failback plan. Failback may happen very quickly following failover or it may take days or weeks, so it should be considered a separate process from failover.

    -   The necessity to failback is situational. If you are routing traffic between regions for performance reasons, failing back the load originally in the failed-over region is important, but in other cases, you may have designed your workload to be able to fully function regardless of which production environment it is located in at any given time.

Equally as important as a well-developed DR plan is a well-developed DR testing practice. Many industries have compliance frameworks that specifically require a certain number of DR drills to be performed regularly; but no matter what industry you operate in, having regular DR drills is paramount to your success. The following recommendations apply to running successful DR drills:

-   Perform at least one production DR drill per year. Tabletop (dry runs) or non-production drills are helpful to ensure that the involved parties are familiar with their roles and responsibilities and to ensure that operators are building familiarity ("muscle memory") by going through recovery processes, but only production drills truly test the validity of the DR plan and the RTO and RPO metrics.

-   Use tabletop drills to not only build familiarity for seasoned operators, but also to educate new operators on DR processes and procedures. Senior operators should take time to let new operators perform their role, while watching over their shoulders to catch opportunities for improvements. When a new operator is hesitant or confused by a step in a procedure, it is a good time to review that procedure to ensure that it is clearly written.

## Azure facilitation

Many Azure products have built-in failover capabilities. Be sure that you are familiar with these capabilities and that they are included in recovery procedures. For IaaS systems, use [Azure Site Recovery](https://learn.microsoft.com/azure/site-recovery/site-recovery-overview) to automate your failover and recovery. Refer to the following product documentation articles for these common PaaS products:

[Azure App Service](https://learn.microsoft.com/azure/app-service/overview-disaster-recovery)

[Azure Container Apps](https://learn.microsoft.com/azure/container-apps/disaster-recovery?tabs=azure-cli)

[Azure Kubernetes Service](https://learn.microsoft.com/azure/aks/operator-best-practices-multi-region)

[Azure SQL DB](https://learn.microsoft.com/azure/azure-sql/database/disaster-recovery-guidance?view=azuresql)

[Azure Event Hubs](https://learn.microsoft.com/azure/event-hubs/event-hubs-geo-dr?tabs=portal)

[Azure Cache for Redis](https://learn.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication)

## Tradeoff

Performing DR drills in production can potentially cause unanticipated catastrophic failures. Be sure to test recovery procedures in non-production environments during your initial deployments and give your team as long of a maintenance window as possible when performing drills. The recovery metrics that you capture during your testing (link to RE:08) should be used as *minimum time necessary* allotments when planning your maintenance window. As your DR drill practices mature, you will learn which procedures can be run in parallel and which must be run in sequence, but early in your practice, you should assume that every procedure must be run in sequence and you should assume that you will need extra time in each step to account for unanticipated issues.

## Example 

Refer to the [DR for Azure data platform](https://learn.microsoft.com/azure/architecture/data-guide/disaster-recovery/dr-for-azure-data-platform-overview) series for thorough guidance on preparing an enterprise data estate for DR.

## Related links

-   Link to re04-redundancy

-   Link to re04-active-passive

-   Link to WAF AZ guide
