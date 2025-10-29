---
title: Develop a disaster recovery plan for multi-region deployments
description: Learn about recommendations for creating a disaster recovery plan based on your reliability objectives. 
author: PageWriter-MSFT 
ms.author: prwilk 
ms.date: 09/19/2025
ms.topic: conceptual
ms.update-cycle: 1095-days  
---

# Develop a disaster recovery plan for multi-region deployments

As a cloud solution architect, you play a key part in ensuring that recovery from wide-scope failures is intentionally planned, designed, and documented as a disaster recovery (DR) plan. In case of failure, the quality of that plan influences whether the event is a temporary setback or becomes a reputational and financial crisis.

The plan should be based on strategies guided by business priorities and governed by measurable objectives. This article guides you through the process of developing a practical DR plan, starting with the foundational practices of restoring services to a mindset shift that defends business continuity under pressure.

## Terminology

Before you start developing your plan, we recommend that you familiarize yourself with a common vocabulary. Establish these terms as shared language to enable clear communication and coordinated decision-making when activating the DR plan. 

| Term | Definition |
|------|------------|
| **Active-active (hot standby)**| Two or more environments fully operational and serving live traffic simultaneously across multiple regions. If one environment fails, others continue handling the load with zero or near-zero disruption. |
| **Active-passive (cold standby)** | Environment that isn't running and requires provisioning and data restoration when activated. Lowest cost, longest recovery time. |
| **Active-passive (warm standby)** | Partially provisioned environment running minimal services that can scale up quickly during failures. |
| **Business continuity** | Strategies for ensuring critical operations continue during and after disruptions, encompassing DR, personnel, communication, and processes. |
| **Disaster recovery (DR) plan**| Detailed, executable procedures for recovering specific systems, including step-by-step actions, roles, responsibilities, failover sequences, and communication workflows. |
| **Disaster recovery (DR) strategy**| High-level approach defining goals, principles, and recovery posture for responding to catastrophic failures across workloads. |
| **DR activation** | Formal decision to initiate disaster recovery procedures, typically requiring executive authorization. |
| **Failback** | Process of returning workloads to the original primary environment after incident resolution. |
| **Failover** | Process of shifting workloads from primary to standby environment during a disaster. |
| **Recovery point objective (RPO)** | Amount of data loss that can be tolerated before incurring major losses. RPO determines how often you should back up essential data. |
| **Recovery time objective (RTO)** | Amount of time that it takes to restore essential access, data, and functionalities after a technology-related disaster. |

## What is disaster recovery?

Disaster recovery (DR) is a strategic and methodical approach to restoring systems or critical parts of them, after a major failure event.

In cloud environments, temporary failures are normal. These brief disruptions, often referred to as _blips_ or _transient faults_, might result in the unavailability of isolated components or unexpected drops in performance. They're typically resolved through platform resiliency features and built-in self-healing mechanisms without human intervention.

_Disasters_, however, are a different class of event. They're broad in scope, affect multiple systems or services simultaneously, and can bring the system to a halt. These events require external intervention and specialized roles, guided by a well-defined DR plan that can be activated when the system's built-in self-healing resilience isn't enough. Some examples include:

- Complete regional outages

- Loss of control plane access or service management capabilities

- Corrupted production environments due to malicious activity or critical misconfiguration

- Severe infrastructure failures affecting multiple tiers of the system

- Natural disasters or geopolitical events causing extended service unavailability

Unattended blips can escalate into full-scale disasters. While advanced monitoring and health modeling can help detect and mitigate these issues early, that topic is beyond the scope of this article. For more information, see [Health modeling](./health-modeling.md).

The ultimate goal of DR is to maintain business continuity within defined quantitative metrics. Think of the plan as a coordinated effort that requires predefined procedures, clear communication protocols, and executive-level decision-making.

## Select your criticality tier

Not all workloads (or parts of it) need a heroic recovery plan. Recovery should reflect its criticality. 

> [!IMPORTANT]
> Criticality is a business decision and it's your responsibility to help guide that decision. It depends on what your workload does, who relies on it, and what happens if it goes down. Azure doesn't decide what's mission critical, _you do_. If an outage would hit your revenue, damage customer trust, or put you out of compliance, then that's a critical system. Own that decision, and design with it in mind.

Over-engineering low-impact services wastes resources; under-preparing high-impact ones risks serious consequences. The key is right-sizing your recovery strategy based on business impact. Use the following classification tiers as a starting point to assess criticality and align your disaster recovery investments appropriately.

A common way to quantify tiers is through Service Level Objectives (SLOs), often expressed as 'five nines' (99.999%), 'four nines' (99.99%), and so on. These percentiles broadly represent the level of availability expected for a given workload. 

The most important metrics in DR strategy are Recovery Time Objective (RTO) and Recovery Point Objective (RPO), both quantified as time units. RTO defines how quickly a system must be restored after a disruption. It represents the organization’s tolerance for downtime. RPO defines how much data loss is acceptable and reflects how frequently data must be backed up.

This article assumes that your SLOs and recovery metrics have already been defined and won't cover how to calculate them. If you need guidance on establishing meaningful SLOs, refer to [Reliability metrics](../reliability/metrics.md).

:::image type="content" source="./_images/disaster-recovery-criticality-tiers.png" alt-text="A diagram that shows multi-region recovery targets." lightbox="./_images/disaster-recovery-criticality-tiers.png":::

#### Tier 0: Mission Critical

The mission-critical tier includes entire workloads or specific components where downtime isn't an option and cost saving is secondary to continuity. These systems are fundamental to the organization, directly driving revenue, safeguarding customer trust, or impacting lives. Common examples include financial platforms, healthcare systems, and security infrastructure.

This tier demands SLOs above 99.99%, with RTO measured in seconds and RPO approaching zero. To meet these requirements, an active-active, multi-region deployment is typically necessary, enabling instant recovery with no interruption to users.

When mission-critical systems fail, the consequences are immediate and significant: lost revenue, reputational damage, or regulatory exposure. 

#### Tier 1: Business Critical

Business-critical systems are essential to day-to-day operations and customer experience, but unlike mission-critical systems, they can tolerate brief periods of disruption, as long as recovery is fast and data loss is minimal. These systems are often driven by revenue incentive, such as e-commerce platforms, customer-facing applications, and partner portals.

They typically require SLOs around 99.95%, with RTO and RPO measured in minutes. A mix of active-active or warm standby deployments is often used to balance resiliency with cost.

While short outages may be survivable, extended downtime in this tier directly affects revenue, user satisfaction, and brand credibility. Predictability and speed of recovery is critical.

#### Tier 2: Business Operational

Business-operational systems support internal teams and processes. While not directly customer-facing, they're essential for productivity and operational continuity. Typical examples include reporting platforms, internal dashboards, and administrative tools.

These systems generally target SLOs around 99.9%, with RTO and RPO measured in hours. An active-passive with warm/cold deployment strategy is common, where secondary environments remain inactive until needed, optimizing for cost over recovery speed.

Outages in this tier may not immediately impact customers, but longer disruptions can slow down the business. Timely recovery is important, even if it's not immediate.

#### Tier 3: Administrative

Administrative systems are non-critical workloads that support background operations or serve low-urgency use cases. These typically include archival platforms, sandbox environments, training portals, or batch-processing tools where availability isn't time-sensitive.

With SLOs below 99.9%, these systems tolerate longer recovery windows, with RTO ranging from hours to days and RPO measured in hours. The most cost-effective approach here's typically backup and restore, minimizing ongoing infrastructure costs while still preserving recoverability.

While delays in this tier are generally acceptable, data integrity must still be protected. These systems may not stop the business if they go down, but losing them entirely could still create compliance risks or knowledge gaps over time.

## Classify your workload

Before you start your DR strategy, classify your workloads based on actual business impact and recovery requirements. 

Start by listing every user flow in your workload. Document what it does, who relies on it, and what happens if it goes down. Different flows within the same workload may require different disaster recovery strategies based on their individual business impact and criticality.

Work with your business stakeholders to get their sign off on these classifications. Confirm RTO and RPO targets based on real business consequences, not just technical opinions. Their commitment sets the foundation and your DR strategy builds on that. Without alignment on business risk, a technical response lacks direction. 

Review these classifications regularly. As business needs evolve, update your DR plan accordingly.


## Watch out for these friction points

Here are some key friction points that you should be cautious about, otherwise DR planning can turn into a costly exercise without the right outcomes.

- **Mismatch between expectations and budget**. Set expectations properly so that stakeholders don't expect hot standby performance on a cold standby budget. The gap between RTO/RPO promises and budgets can lead to risk and disappointment.

- **Shared service dependencies can break your chain**. Your DR plan is only as effective as its weakest component. If your workloads depend on shared or third-party resources, which lack proper failover strategies, it can create vulnerabilities during a disaster.

- **DR activation criteria must be crystal clear**. Everyone listed in the accountability list must be clear on the criteria. Without this, there might be hesitation to initiate recovery, which can cause unnecessary delays.

- **Failback is just as important as failover**. While many focus on treating failover as a one-way cutover, sometimes failback is a viable option. However, returning operations to the primary site often involves more complexity. Make sure to plan and test failback procedures. A good guideline is to automate failover while managing failback through a controlled process.

## Optimize your recovery costs

The cost of disaster recovery scales with the criticality of the workload. 

- **Tier 0 (Mission Critical)** comes with the highest cost, and that's expected. Active-active deployments and redundant infrastructure significantly increase your spend in exchange for near-zero downtime. When it comes to cost optimization, your best options are standard practices like reserved instances or Azure Hybrid Benefit where applicable.

    Strive for simplicity in your design. Over-engineering beyond well-defined requirements is where hidden costs quietly build up. Keep in mind that foundational practices like infrastructure as code, automated deployments, and testing introduce upfront engineering effort. While that effort might compete with delivering new features, compromising investment in strong operations is just not an option. 

- **Tier 1 (Business Critical)** offers a balance, typically using warm standby environments that reduce cost. 

    Lower baseline capacity in the warm standby by deploying compute resources in the secondary region at partial scale and enabling auto-scaling to ramp up only when needed. This approach avoids paying for full capacity 24/7.
    
    Using manually triggered failover process with an orchestrated runbook to reduce complexity and ongoing operational costs compared to fully automated failover. Regular failover testing helps identify inefficiencies,

- **Tier 2 (Business Operational)** focuses on optimizing costs by using cold standby setups and pay-as-you-go options like spot instances and consumption pricing. Automate provisioning of PaaS compute in the secondary region only when needed to avoid paying for idle resources. Define clear disaster criteria and failover processes to prevent unnecessary failovers. Regular testing ensures recovery targets are met and highlights areas to trim costs.

- **Tier 3 (Administrative)** prioritizes cost savings by relying on backup and archival storage with longer recovery windows. Use replicated backups and Azure Backup vaults in a secondary region to protect persistent data without running standby infrastructure. Regularly test restore processes to ensure reliability while keeping expenses to a minimum.

Whatever your tier might be, use the right tooling to review costs. Use tools such as Microsoft Cost Management and Azure Advisor to monitor, forecast, and optimize spending across all tiers. Tag resources and set budget thresholds to make accountability and chargeback models easier to track. For information on Microsoft-recommended tags, see [Tagging mission-critical workloads](/azure/azure-resource-manager/management/tag-mission-critical-workload).

## Document your DR plan

A strong Disaster Recovery (DR) plan turns strategy into decisive action. Activation begins with a combination of automated alerts and human oversight. Observability tools flag potential issues such as performance slowdowns and trigger alerts for the operations team to investigate. They review dashboards for anomalies and assess the situation. If the issue appears broader or more severe, it's escalated and additional teams may be involved. After there's enough evidence, the DR lead formally declares a disaster, initiating a structured failover process to maintain system continuity.

To make this possible, every DR plan should include three essential components: a clear runbook, a well-defined communication plan, and a structured escalation path.

#### DR communication plan

A communication plan ensures that the right information reaches the right people at the right time during a disruption. It supports coordination, reduces confusion, and keeps stakeholders informed throughout the recovery process. Your plan should cover these aspects:

- **Activation criteria and approvals**. Establish what qualifies as a DR event. Identify who has authority to trigger the DR process. Document escalation paths and decision checkpoints.

- **Roles and responsibilities**. Define who's responsible for communicating, and to whom.

- **Key stakeholders**. Identify key internal and external audiences, such as, employees, leadership, partners, and customers.

- **Communication channels**. Establish primary and backup methods like email, SMS, and others. Also establish frequency of updates to those channels.

- **Notifications and templates**. Outline when to send updates and prepare pre-approved messaging templates for email, status page, and incident channels.

- **Escalation and continuity**. Ensure there's a structured way to escalate issues if someone is unavailable or things change quickly.
Communication plans must address both internal and external stakeholders with appropriate detail and frequency and using separate channels. Internal communication provides regular updates to executive leadership and business users, focusing on business impact, timelines, and resource needs. External communication coordinates with customers, partners, and regulatory bodies, and include current status and realistic timeframes for restoration.

#### DR runbook

A strong runbook replaces abstract strategies with structure and allows the team to respond under pressure. Make it clear, make it practical, and make sure it works. Start with a simple outline and build gradually. Collaborate with business, security, and operations to ensure full coverage.

- **Document failover and failback procedures**. Write step-by-step technical instructions for initiating failover. Reference tools and scripts to execute with links or references. Establish criteria for initiating failback and coordinated cutover steps.

    Develop a step-by-step process for failover execution:

    | Action | Owner | Criteria |
    |--------|-------|----------|
    | 1. Detect incident | Monitoring/Operations | The incident is triggered by alerts or user reports. |
    | 2. Assess severity | Incident Manager | Use the Incident Classification Table to determine severity level. |
    | 3. Declare outage (if needed) | Senior Ops/BCDR Lead | Declare outage for High and Critical severity incidents only. |
    | 4. Notify stakeholders | Communications Lead | Follow the established Communication Plan for notifications. |
    | 5. Initiate runbook execution | Operations Team | Initiate execution of the DR runbook, including automated runbooks and manual steps. |
    | 6. Prepare secondary infrastructure | Operations Team | Deploy and/or scale-up and validate infrastructure configuration in secondary. |
    | 7. Ensure data integrity | Operations Team | Restore data from backups, if needed, and validate data integrity and adherence to RPO. |
    | 8. Recover applications | Ops/QA Team | Deploy, if needed, and activate applications in secondary. Validate correct operation and all dependencies |
    | 9. Traffic cutover | Operations Team | Transition user to secondary environment. Update DNS records if necessary | 
    | 10. Close incident and document | Incident Manager | Conduct post-mortem and update incident records. |

    Similarly, create a failback decision and execution process (primary region available):

    | Action | Owner | Criteria/decision point |
    |--------|-------|-------------------------|
    | 1. Monitor primary region health | Operations/Cloud Team | Verify that the primary region passes all health checks and is fully operational.<br>Use automated monitoring tools and manual validation to confirm readiness. |
    | 2. Assess business impact | Application Owner/Business Continuity | Confirm business readiness for failback, including low-traffic windows and required approvals.<br>Coordinate with stakeholders to ensure timing aligns with business needs. |
    | 3. Review data synchronization | Database/Infra Team | Ensure that data in the secondary region is synchronized with the primary and meets RPO/RTO requirements.<br>Use replication status dashboards to verify data consistency. |
    | 4. Communicate failback plan | Incident Manager | Notify stakeholders of the planned failback, including timeline and potential impact.<br>Use email, Teams, or incident management tools for communication. |
    | 5. Prepare primary region | Infra/Cloud Team | Validate that infrastructure, security, and application components are ready in the primary region.<br>Run pre-failback checklists to ensure complete readiness. |
    | 6. Initiate failback | Operations/Cloud Team | Proceed only with approved change request and when all criteria are met.<br>Begin redirecting traffic and workloads to the primary region. |
    | 7. Monitor failback progress | Operations/Cloud Team | Monitor for errors, latency, or data loss during the transition process.<br>Use dashboards and alerting systems to track progress. |
    | 8. Validate application functionality | Application Owner/QA | Confirm that applications and services are fully functional in the primary region.<br>Run smoke tests and regression tests to validate functionality. |
    | 9. Finalize and close incident | Incident Manager | Ensure all systems are stable, stakeholders are informed, and documentation is updated.<br>Complete post-mortem analysis and capture lessons learned. |

- **Establish health validation and readiness checks**. Define how you verify service functionality post-failover. Include application-level, infrastructure, and data integrity checks.

- **Plan post-recovery and review**. Outline actions to clean up temporary environments. Document data reconciliation if applicable. Schedule root cause analysis and DR debrief.

> [!TIP]
> Treat your DR runbook like production code: version it and make it accessible. Use version control tools like Git or a versioned wiki to track updates and ensure accuracy over time. Just as important, make sure the runbook is always reachable, even during an outage. Store it in multiple formats, including offline or printable versions, so teams can access it when it matters most.


#### DR escalation plan

During disaster recovery, speed and clarity are everything. An escalation plan ensures that when things aren't going according to plan, the right people are alerted quickly. 

- **Escalation triggers.** Define exactly when to escalate, whether it's a missed recovery milestone, a critical system failure, or an unresponsive vendor.

- **Chain of command.** In the identified list of roles and contacts, lay out in what order to contact. That is, how to escalate issues through primary, secondary, and backup personnel. Also include response time expectations. For example, call the DR manager within 15 minutes on phone or emergency communications platform.

- **Incident severity levels.** Categorize incidents by impact so that minor issues don't clog the system, and major ones get immediate attention from leadership.

Here's an example template: 

| Severity level | Description | Examples | Outage declaration trigger | Stakeholders notified |
|----------------|-------------|----------|---------------------------|----------------------|
| Low | Minor service degradation | Brief latency spike | No formal declaration | Operations Team |
| Medium | Partial service degradation | Single service errors | Incident logged, under observation | Ops, Business Leads |
| High | Major outage, widespread impact | Multi-service failure | Formal outage declaration | All stakeholders, Customers |
| Critical | Total loss, business-critical | Complete regional Azure failure | Immediate declaration, C-level | All stakeholders, Exec Team |

## Test regularly and improve the plan

Disaster recovery is an operational discipline. A DR plan that's never tested stays theoretical and unproven.

- Rehearse the runbook at a regular cadence to simulate scenarios and clarify team roles.  

- Schedule full or partial failover drills to validate actual recovery steps and timings. A planned failover simulates a regional outage to practice smooth system transitions. Tools like Azure Chaos Studio are used to test unplanned failures and see how systems respond.

- After every test, check data to confirm nothing was lost or corrupted. Capture any gaps or issues discovered, then update your architecture and runbooks promptly.


## Recovery strategy for backup and restore

Backup and restore strategies must be part of all recovery strategies. 

#### Suggested actions

Use this as a foundation for all the tiers. Each step should include a clear objective and a way to validate its effectiveness.


| Actions | Configuration | Validation |
|---------|---------------|------------|
| **Configure backup policies and retention** | - Configure backup schedules and retention periods for infrastructure and databases aligned with RPO requirements<br> - Use [Azure Backup](/azure/backup) for VMs, Azure Files, and Blob Storage<br> - Store backups in secondary region, such as by using Geo-Redundant Backup Vault where available | - Test backup policy execution<br> - Verify backup completion and integrity<br> - Validate retention policy enforcement |
| **Implement cost-effective storage tiers** | - Use Archive or Cool storage tiers for infrequently accessed data<br> - Apply backup tiering policies to transition older backups to lower-cost options<br>* Configure compression and deduplication to minimize storage costs | - Review storage cost optimization reports<br> - Verify tiering policy execution<br> - Test data retrieval from different storage tiers |
| **Document restore procedures** | - Maintain runbooks with detailed recovery steps<br> - Define target environments for restoration<br> - Include contact lists for approvals and escalations | - Test restore procedure documentation accuracy<br> - Verify contact information currency<br> - Test escalation paths and approval workflows |
| **Monitor backup costs and compliance** | - Set budget thresholds for backup-related resources<br> - Apply backup-specific tags to enable proper tracking<br> - Configure retention policies to meet regulatory compliance requirements | - Review backup cost reports monthly<br> - Verify budget threshold effectiveness<br> - Audit compliance with retention policies |
| **Maintain and audit backup systems** | * Perform quarterly audits of backup requirements<br> - Retire obsolete systems and adjust policies<br> - Review and update RPO/RTO requirements based on business and technology changes | * Verify audit findings are addressed<br> - Confirm retired systems are properly decommissioned<br> - Validate RPO/RTO requirement changes are feasible |


## Recovery strategy for active-passive (cold standby)

Active-passive cold standby deployments keep the secondary region's compute resources stopped until needed. This approach is ideal for Tier 2 or Tier 3 workloads, where RTOs can tolerate longer delays but recovery must still be reliable and repeatable.

The primary region handles all production traffic while the secondary region maintains infrastructure readiness with minimal running resources, requiring manual activation during disaster scenarios.

#### Suggested actions

Build on [recovery strategies for backup and restore](#recovery-strategy-for-backup-and-restore) and cover these points. Extend it as necessary.

| Actions | Configuration | Validation |
|---------|---------------|------------|
| **Set up primary and secondary regions** | - Deploy full-scale workload in primary region<br> - Duplicate network topology, policies, and configurations from primary<br> - Ensure RBAC, security baselines, monitoring agents, and policies are consistent<br> - Deploy infrastructure as code with compute resources stopped | - Verify secondary region infrastructure readiness<br> - Test policy consistency across regions<br> - Validate network connectivity and routing | 
| **Set up cross-region data replication** | - Enable built-in replication for Azure SQL Database, PostgreSQL, MySQL, Cosmos DB<br> - Enable GZRS or RA-GZRS for paired-region storage replication<br> - Configure object replication for Blob Storage in non-paired regions<br> - Set up multi-region read/write with configurable consistency levels for [Azure Cosmos DB](../service-guides/cosmos-db.md)<br> - Configure replication lag monitoring and data consistency validation processes | - Test data synchronization lag against RPO targets<br> - Verify replication health and latency metrics<br> - Validate data consistency between regions<br> - Validate data integrity during failover scenarios | 
| **Secure DR environment and maintain compliance** | - Replicate data residency requirements in all regions<br> - Maintain identity and access parity between regions<br> - Test for audit trail continuity during and after failover | - Audit security configurations across regions<br> - Test identity failover scenarios<br> - Verify compliance during DR events<br> - Validate audit log continuity | 
|**Automate provisioning and prepare operations** | - Define IaC templates for automated resource provisioning environment as needed<br> - Include service configurations, scaling parameters, and dependencies<br> - Pre-define scale targets to meet full load when activated<br> - For IaC: Bicep, Terraform, ARM templates<br> - Deployment automation: CI/CD pipelines for both regions<br>- Runbooks: Automated and manual steps for invoking DR procedures | - Test automated provisioning procedures<br> - Verify compute startup times meet RTO targets<br> - Validate service dependency activation sequences<br> - Test IaC deployment consistency across regions<br> - Validate runbook execution and timing | 
| **Monitor readiness and health** | - Set alerts on replication health and latency metrics<br> - Monitor secondary region infrastructure status<br> - Track activation readiness across all components<br> - Implement health checks for replication status<br> - Configure alerts for auto-scale events and traffic routing<br>- Ensure observability tools cover both regions | - Test monitoring and alerting systems<br> - Verify infrastructure health checks<br> - Validate readiness indicators accuracy<br> - Validate observability coverage | 
|**Configure load balancer with manual failover** | - [Azure Front Door](../service-guides/azure-front-door.md) or [Traffic Manager](../service-guides/azure-traffic-manager.md) with priority-based routing<br> - Route all production traffic to primary region under normal conditions<br> - Manually trigger traffic redirection upon failover | - Test traffic failover scenarios<br> - Verify routing priority configurations<br> - Validate DNS propagation and cutover times | 
| **Define manual failover runbook and criteria** | - Establish clear failover triggers and activation criteria<br> - Document manual activation steps including compute startup and DNS updates<br> - Include rollback procedures for failed or temporary activations | - Test failover runbook execution<br> - Verify manual activation procedures<br> - Validate rollback processes and timing | 
| **Test restore processes regularly** | - Schedule periodic restore drills to validate backup integrity<br> - Include restoration to staging environments<br> - Log time taken and compare against RTO targets | - Execute quarterly restore drills<br> - Verify data consistency post-restore<br> - Document performance against RTO targets | 


## Recovery strategy for active-passive (warm standby)

Active-passive warm standby deployments balance cost and resilience by maintaining a minimally provisioned secondary environment that can scale quickly during failure events. This approach reduces downtime while avoiding the full cost of always-on redundancy across regions.

The primary region handles all production traffic under normal conditions, while the secondary region runs with minimal resources and scales up only when activated for disaster recovery.

#### Suggested actions

Build on [recovery strategies for active-passive (cold standby)](#recovery-strategy-for-active-passive-cold-standby) and cover these points. Extend it as necessary.

| Actions | Configuration | Validation |
|---------|---------------|------------|
| **Configure secondary region at partial scale** | - Deploy minimal viable compute footprint in secondary region | - Verify secondary region infrastructure readiness | 
| **Enable auto-scaling in secondary region** | - Configure auto-scale rules to increase compute resources post-failover<br> - Set appropriate scaling thresholds and limits<br> - Define startup sequences for dependent services | - Test scaling behavior during failover drills<br> - Verify resource availability during scale-up | 
| **Configure automated failover procedures** | - Enable auto-failover where supported<br> - Document manual failover procedures for other services<br> - Create orchestrated failover runbooks with step-by-step processes | - Test automated failover mechanisms<br> - Validate manual failover procedures<br> - Verify runbook execution timing and accuracy | 
| **Configure load balancer with auto failover** | - [Azure Front Door](../service-guides/azure-front-door.md) or [Traffic Manager](../service-guides/azure-traffic-manager.md) with automatic failover enabled<br> - Configure health checks for automatic traffic redirection | - Test traffic failover scenarios<br> - Verify routing priority configurations | 

## Recovery strategy for active-active deployments 

Active-active deployments maximize service availability by running multiple workload instances across regions, with each instance actively handling production traffic. This design eliminates downtime and enables instant failover but it also demands precise planning to manage consistency, routing, and cost across distributed systems.

Choose one of two deployment approaches:

- **Active-active (at capacity)**: Mirrored deployment stamps or geodes in two or more regions, where each region handles a share of the production load and can scale up to absorb full load during regional failure.

-  **Active-active (overprovisioned)**: Mirrored deployment stamps or geodes in two or more regions. Each region is at full scale at all times to independently handle 100% of traffic.

> [!NOTE]
> In active-active scenarios, when a failure occurs, user experience may remain unaffected as the load shifts to the remaining instances. However, disaster recovery efforts are still necessary to restore the failed instance.

#### Suggested actions

Build on the [recovery strategies for active-passive (warm standby)](#recovery-strategy-for-active-active-deployments). Extend it as necessary.

| Actions | Configuration | Validation |
|---------|---------------|------------|
|**Configure secondary region at full capacity** | - Deploy secondary region at full-scale capacity, at par with primary | - Validate secondary region is able to **instantly** support full load when primary fails | 
|**Configure load balancer to distribute traffic across active instances** | - [Azure Front Door](../service-guides/azure-front-door.md) or [Traffic Manager](../service-guides/azure-traffic-manager.md) with latency-based or weighted routing<br> - Health probes configured per endpoint<br> - Automatic rerouting of traffic upon failure detection | - Test failover scenarios across regions<br> - Verify health probe accuracy and response times<br> - Validate traffic routing during simulated outages | 
|**Configure load distribution behavior** | - Document load threshold each region handles during normal operation<br> - Expected performance and scale-up behavior if peer region fails<br> - Define system behavior under single-region failure, partial service disruption, network partitioning | - Test regional failure scenarios under load<br> - Verify performance degradation limits<br> - Test network partition handling | 


## Next steps

- [RE:09 Architecture strategies for designing a disaster recovery strategy](../reliability/disaster-recovery.md)

- [RE:05 Architecture strategies for designing for redundancy](../reliability/redundancy.md)

