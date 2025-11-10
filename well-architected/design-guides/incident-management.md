---
title: Incident response design guide for Azure workloads
description: Learn how to design comprehensive incident response processes for Azure workloads, including detection, containment, mitigation, and recovery strategies.
author: PageWriter-MSFT 
ms.author: prwilk 
ms.date: 11/08/2025
ms.topic: conceptual
ms.update-cycle: 1095-days  
---

# Incident management (IcM) design for Azure workloads

An _incident_ is an unplanned event that disrupts, degrades, or threatens to disrupt the normal operation of a system, resulting in customer or business impact. Incidents exist on a spectrum, from transient or localized disruptions to widespread events or disasters.Your incident management (IcM) strategy should scale with severity, from rapid mitigation for minor incidents to coordinated cross-team efforts for major events. Response can also vary depending on the cause, whether hardware or infrastructure failures, resource limits, human errors like failed deployments or misconfigurations, or external factors such as security attacks. Examples include data breaches, regulatory violations, malware, or identity compromises.

Despite the cause, incident response whether operational or security, follows the same playbook: detect, triage, mitigate, communicate, and review. As the IcM architect, don't just focus on dashboards and runbooks. Design operations where people, processes, and tools work together under pressure to restore systems efficiently and without chaos.

This article focuses on the phases of IcM, which includes preparation, active incident response, post-incident review, and ongoing improvement. It also includes example to illustrate the design practices. Before you begin, take some time to review the key strategies and pick the ones that make sense for your business. See [OE:08 Architecture strategies for designing an incident management (IcM) process](../operational-excellence/incident-response.md) for details.

This article doesn't cover disasters that require specialized recovery efforts. Details on handling such scenarios can be found in [Develop a disaster recovery plan for multi-region deployments](./disaster-recovery.md).

## Terminology

| Term | Definition |
|---|---|
| **Blast Radius** | The scope of impact or affected area when an incident occurs, which containment strategies aim to limit. |
| **Break-glass Accounts** | Emergency credentials with elevated privileges used during critical incidents, strictly controlled with clear usage guidelines. |
| **Bridge Team** | The triage team assembled to investigate an incident, also called the engineering bridge, consisting of relevant technical experts and decision-makers. |
| **Containment** | The first step in incident remediation that isolates affected components to prevent the issue from spreading to other parts of the workload. |
| **Incident Management (IcM)** | The structured process of detecting, triaging, mitigating, and resolving incidents while coordinating communication and capturing lessons learned. |
| **Last-known-good** | The last healthy state of the workload before an incident began, used as a reference point for rollback operations. |
| **Mitigation** | Actions taken to reduce or eliminate the impact of an incident, including rollback, fallback, bypass, or emergency fixes. |
| **Retrospective** | A blameless post-incident review process focused on identifying lessons learned and actionable improvements rather than assigning blame. |
| **Root Cause Analysis (RCA)** | A systematic process of investigating an incident to identify the underlying factors responsible for the problem and prevent recurrence. |
| **Severity Level** | Classification system (Critical, High, Medium, Low) that determines the appropriate response level based on business impact and affected users. |
| **Triage** | The process of analyzing and prioritizing incidents to determine severity, impact, and appropriate response actions. |

## Phase 1: Prepare

Before an incident happens, set up the foundation for effective response by designing observability, defining clear roles and processes, and preparing the tools and resources your teams will need. Document the incident response plan and keep it updated and reviewed regularly.

1. Maintain accurate, up-to-date architecture diagrams that show all components and their interactions help teams quickly identify bottlenecks or single points of failure, enabling faster troubleshooting during high-pressure situations.

1. Define the incident data that your monitoring stack must provide for the entire workload:

   - Collect end-to-end telemetry, including infrastructure and applications.

   - Enable structured logging for all components to support triage and investigation, and send logs to data sinks for analysis. If required, also forward them to centrally managed sinks. Make sure team members have time-limited, least-privilege access during incidents.

   - Create dashboards based on the workload health model, showing the metrics and signals your team is responsible for monitoring.

   - Configure actionable alerts that trigger notifications only when thresholds are exceeded and a potential incident is detected. They should be tuned to avoid noise (too many or too few alerts). For example, for live-site incidents, alerts should monitor critical metrics like CPU, memory, response times, and database performance to detect actionable issues that align with performance targets.
   
     Route alerts automatically to the right teams. For example, tier-1 support gets all alerts, while security engineers receive only security-related ones.

   For more information, see [Recommendations for designing and creating an observability framework](../operational-excellence/observability.md).

1. Define key roles to ensure accountability, decision-making, and effective follow-up during and after incidents. Create appropriate approval processes codify them in a clear decision tree. Define authorization levels for different types of severity, mitigation decisions, escalations paths and so on.

   Use these examples as a starting point and adapt them to fit your team structure. 

   | Role | Responsibilities |
   |------|-----------------|
   | **Incident Response Manager** | Owns the incident from detection through resolution and root cause analysis. Ensures processes are followed, decisions are made, and the right people are informed. |
   | **Retrospective Leader** | Leads post-incident reviews, captures lessons learned, produces actionable reports, and ensures findings are applied. |
   | **On-Call Engineer** | Actively mitigates and resolves incidents. Follows responsibilities for different incident types, ensuring timely involvement of the right teams. |

1. Define procedures for investigation:

   - Define categories for types of incidents (operational, security, performance, deployment) and severity levels (Critical, High, Medium, Low) applicable to your workload.

   - Document how to evaluate the severity, impact, and urgency of the incident. Consider user impact, affected systems, and business-critical functions. Based on the severity level, the team will activate the disaster recovery plan for cases that meet disaster-level thresholds, or follow the standard response plan for less severe incidents.

   - Define strategies that isolate or remove the affected component from workload flow paths, such as shutting down a resource or rerouting traffic. Identify which roles have authority to take containment action.
    
     Set monitoring systems to auto-initiate containment for defined incidents, keeping humans in the loop for critical decisions.  System administrators, engineers, and senior developers should collaborate to limit the blast radius while maintaining degraded functionality. If a component must remain accessible for triage, ensure its access is strictly isolated from the rest of the workload.

   - Create guidelines for selecting mitigation strategies based on incident severity, such as isolation, rollback, configuration changes, workarounds.

   - Specify which teams or individuals are responsible for handling the incident based on type and severity. Make sure to include escalation paths for when initial responders cannot resolve the issue.

   - Outline what data must be collected during triage (logs, metrics, user reports, alerts) to support investigation and decision-making. Also include who should have access to that data.

   > [!IMPORTANT]
   > Lock down your emergency credentials, or break-glass accounts, with clear rules: who can use them, when, and exactly how. Pair that with emergency drills and track every run. Define how frequently the team should practice drills. During an incident there's no time to figure it out on the fly. Drills provide adequate practice and refinement opportunities.

1. Set up your infrastructure to support resolution:

   - Parameterize pipelines: Enable deployment and recovery pipelines to accept specific versions for rapid rollback or fix deployment.

   - Ensure data plane consistency: Keep keys, secrets, configs, and state data aligned during recovery operations.

   - Automate infrastructure scaling: Adjust resources automatically to handle traffic shifts or increased load.

   - Implement self-healing: Safely automate responses to common incident patterns with monitoring and manual override.

1. Define communication processes. Document clear communication and escalation plans so tier-1 support can quickly reach the right teams. Specify appropriate communication channels for internal and external stakeholders, and include on-call schedules and contact details.

1. Define the usage of an IcM tool and the standard operating procedures it should capture in a simple workflow: create, acknowledge, mitigate, resolve. The tool's goal is to give teams visibility, track progress, maintain accountability, and ensure consistent incident handling around the clock, centralizing all activities to support live site management and on-call rotations.
            
1. Define the criteria that officially mark an incident as closed:

   - Include clear resolution factors. Typically, this means systems and services are operating within SLAs, performance and reliability have returned to acceptable levels, and any immediate mitigation actions have been successfully completed.

   - Include validation checks to ensure the issue is fully resolved. This involves confirming through monitoring tools that the impact has ended and verifying that affected users are no longer experiencing disruption.

   - Include communication requirements when closing an incident. Notify relevant stakeholders, including internal teams, support staff, and if needed, impacted users, and provide a summary of actions taken along with any ongoing work.

   - Include thorough documentation when closing an incident. Record all details in the incident management system, covering the trigger, containment steps, triage decisions, and the final resolution. This documentation should be considered as handoff for root cause analysis (RCA) and retrospective to capture lessons learned.

   > [!IMPORTANT]
   > Design your operations so that only the designated authority, such as the Incident Response Manager, can close an incident. Enforce strict checklists to block premature closure. Skipping steps can leave hidden issues unresolved, turning a "closed" incident into a repeat disaster.


## Phase 2: Detect, investigate, and respond

Phase 2 focuses on detecting and responding to incidents quickly and effectively. The goal is to identify issues early, assess their impact, and implement the right mitigation strategies while containing disruption. This phase also ensures that triage, resolution, and communication are coordinated, consistent, and accountable across all teams.

> [!IMPORTANT]
> Clear, consistent communication maintains control and clarity in high-stress situations.  Define exactly who speaks, what gets shared, and how often. Standardize update cadence, channels, and message formats so no one scrambles for information mid-crisis. Make sure every stakeholder, from engineers to executives, knows when to expect updates and when escalation is required.

1. Act immediately on the first sign of an issue, from alerts or user reports. Use observability and performance tools to correlate anomalies with system changes. The incident recipient sets up a triage (_bridge_) team with the right members, agreeing on communication mode, progress tracking, and access to incident assets.

1. Mobilize the engineering bridge to evaluate impact and severity. Evaluate the impact of the incident using your predefined severity classifications. Get data to justify criteria outlined in the incident reponse plan, such as number of users affected, business functions disrupted, security and compliance implications, and potential impact on customer trust and reputation. This assessment determines the appropriate response level and guides the next mitigation steps.

1. The investigation stage begins when the right engineering teams are engaged and start root cause analysis. This process involves deep technical analysis to pinpoint the cause and contain the impact. Engineers use observability data, telemetry dashboards, system logs, and change histories to trace anomalies and identify failure points. The focus is on isolating the problem quickly, validating hypotheses with real-time data, and developing a precise mitigation plan that restores service stability without introducing new risk.

    > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: The steps of a root cause analysis  can be time consuming. To correlate performance issues, you also need to collect and store data. The required time and infrastructure can add significant work to the operations teams and cost to the workload.

    > :::image type="icon" source="../_images/risk.svg"::: **Risk**: If you perform a root cause analysis without proper security guardrails, there's a risk that you expose sensitive information when you provide access to logs and data.

1. Follow the containment strategies to isolate affected components and limit the blast radius. You might take actions that:

   - Block access to affected components, using separate paths for triage. For example, shutting down a resource, throttling traffic, or disabling a failing microservice.

   - Limit the incident's reach to specific users, regions, or components.
   
   - Maintain workload functionality in a degraded state if possible.

1. Select an appropriate mitigation strategy. Choose mitigation approaches based on the current state of the workload, available resources, and immediate constraints. 

   The choice depends on factors such as infrastructure type, available bypass mechanisms, complexity of the fix, data sensitivity and compliance requirements, system dependencies, and recovery time objectives:

   - Restore systems to a last-known-good state, ensuring the team agrees on what that means and accounting for data or dependency complexities.

   - Reroute traffic to healthy systems to maintain availability during failures or regional disruptions.

   - Use feature flags or configuration changes to bypass faulty components while assessing tradeoffs and securing stakeholder approval.

   - Apply targeted fixes quickly but safely. Make sure the team maintains key quality checks even under pressure.

   > [!IMPORTANT]
   > Ensure that mitigation decisions follow predefined authorization rules. The Incident Manager should be accountable for all mitigation actions. Only authorized personnel should approve high-impact steps, and every action is documented. Keep the actions controlled, safe, and accountable while restoring the system.  

1. Apply the resolution. This step focuses on restoring the system to full operational state while preventing recurrence. Engineering teams apply verified fixes following team-specific, scripted procedures, using log analysis and monitoring tools to guide investigation. Rollback steps are included to undo ineffective changes, ensuring every action drives the system steadily toward complete recovery.

1. Generate Root Cause Analysis (RCA) report. After an incident is resolved, the RCA report within the SLA timeframe. The incident owner, or a closely involved team member if unavailable, should create the report to ensure accuracy. Follow a defined RCA template with clear guidelines on what information to include and share, or create and approve a new template with stakeholder review.

## Phase 3: Retrospective

Retrospectives should follow every incident. They provide critical learning opportunities, highlight weaknesses in response, deployment, or infrastructure, and inform improvements. Document action items and ensure they are tracked and implemented through the workload backlog.

The goal is not to assign blame but to identify actionable improvements. This process should be led by an impartial facilitator. All teams involved in the incident must be represented by individuals who worked on the response and come prepared with observations on successes and areas for improvement:

- Response plan enhancements: Update processes or procedures to ensure clearer, more effective actions.

- Observability improvements: Adjust thresholds, add monitoring, or implement alerts to detect similar incidents earlier.

- Workload remediation: Address vulnerabilities exposed during the incident for permanent fixes.


## Example: Deployment failure response

Let's look at a deployment-related incident. Even with careful planning and testing, deployments can sometimes introduce issues that affect system performance or user experience.

In this example, we'll look at how a workload team responds and detects the issue caused when team is rolling out a new feature to a subset of users.

1. **Detection**. The problem was noticed when error rates spiked in one of the canary rollout groups. The team immediately used their observability tools like application performance monitoring, logging, and telemetry linking users to rollout phases, to pinpoint the affected group.

    **Preparation**:

    The team had prepared for this scenario by building strong observability into their deployment process. They ran smoke tests and quality checks at each rollout phase and instrumented their application with logging and performance monitoring. Telemetry linked users to specific rollout groups, so they could quickly identify which version affected which users. They also scheduled deployments during working hours when full support was available, and ensured support staff knew how to escalate issues according to the emergency response plan. This preparation allowed them to detect the spike in error rates quickly and respond without delay.

1. **Mitigation**. The team quickly decided on a mitigation strategy, considering whether to roll back to the last-known-good version, fall back to the stable environment, bypass the problematic function with a feature flag, or deploy a hot fix. With approvals already defined in their decision tree, the team implemented the chosen action.

    **Preparation**: 

    Before the incident occurred, the team had defined a clear decision tree for handling deployment issues.
    
    - **Rollback**: Revert updated systems to the last-known-good configuration state. The workload team should define what last known good means, typically the last healthy state of the workload before the deployment began, which may not be the immediately prior application version. Rolling back can be complex, particularly when schema or data changes are involved. To reduce risk, schema updates should be additive rather than replacing records, allowing old and new data to coexist until deprecated records can be safely removed. Rollbacks may require careful planning and coordination across multiple teams.

    - **Fallback**: Remove updated systems from production traffic routing, directing all traffic to the stable stack. This low-risk strategy allows addressing deployment issues without further disruptions. With canary deployments, fallback can be complicated depending on infrastructure and application design. Adequate capacity on the stable stack should be ensured before switching traffic back. Fallback supports continued operation while isolating the problematic deployment.

    - **Bypass the offending function**: Use feature flags or runtime configuration properties to bypass the problematic functionality. This approach allows the rollout to continue while isolating the issue. Tradeoffs should be evaluated and communicated to stakeholders, including how long a degraded state can be tolerated and the estimated time to fully resolve the issue. Stakeholder approval should be obtained for the plan.

    - **Emergency deployment (hot fix)**: Deploy a hot fix mid-rollout to address the issue quickly. Hot fixes should follow safe deployment practices, including code promotion through environments and quality gate checks, but with accelerated timelines. Bake times and tests may be shortened or modified to speed deployment, while automated testing ensures reliability. Hot fixes require coordination and careful planning to minimize risk while resolving the issue promptly.

    After reviewing the tradeoffs, the team chose fallback over rollback. They determined that redirecting traffic to the stable stack would be faster and lower-risk than attempting a full rollback, which could have required refactoring parts of the workload, complex data and schema operations. The team confirmed that the stable stack had sufficient capacity to handle the full production load and that both versions of the workload could run in parallel safely.

1. **Resolution**.  The team executed the fallback procedure: traffic was shifted away from the updated environment, isolating the problematic deployment. This allowed the team to address the underlying issue without impacting the majority of users. Meanwhile, monitoring continued on both the stable and fallback environments to ensure performance and reliability.

    Communication was a key part of the mitigation plan. Stakeholders were kept informed of the decision and its implications, including the expected timeline for resolving the issue in the isolated environment. The team standardized the cadence for providing status updates during deployment incidents so stakeholders knew when to expect progress reports and issue updates. If direct communication with end users was necessary, the team clarified the type and level of detail appropriate to share, while also ensuring that any other requirements for deployment incident communications were followed. This structured approach minimized confusion and helped maintain confidence in the response process.

1. **Retrospective**. After the deployment incident was fully mitigated, the team conducted a retrospective to capture lessons learned and improve future processes. The session included everyone involved in the rollout, from developers and SREs to support and stakeholder representatives. The team reviewed the sequence of events, from detection through mitigation, to understand what went well and where gaps existed.

1. **Post-incident improvements**. From the retrospective, the team implemented several operational improvements to make future deployments safer and mitigations more reliable:

   - **Parameterized deployment pipeline**: The pipeline was updated to accept distinct versions, enabling easy deployment of last-known-good configurations without manual intervention.

   - **Consistency across resources**: Management and data planes were standardized to ensure keys, secrets, configurations, and database state data were properly accounted for during rollbacks or roll-forwards. Infrastructure scaling for the last-known-good deployment was reviewed and adjusted as needed.

   - **Smaller, frequent changes**: The team shifted toward smaller, incremental deployments, reducing the delta between successive versions and making mitigation simpler and lower-risk.

   - **Failure mode analysis (FMA) of pipelines**: CI/CD pipelines were analyzed to identify potential failure points that could complicate mitigation. Weak points were remediated to ensure smoother rollbacks or roll-forwards.

   - **Automated rollback with caution**: Where supported, automatic rollback functionality was tested thoroughly before being enabled. Triggers were carefully designed to deploy only necessary changes and activate only when needed.

   - **Use of platform capabilities**: Backups, point-in-time restores, and VM checkpoints were incorporated into mitigation planning to simplify recovery and reduce risk.

   - **Regular testing and drills**: The team established a practice of frequent testing for the full deployment failure mitigation strategy. Chaos engineering and fault injection tests were introduced to simulate failure scenarios and validate mitigation processes.

## Azure facilitation

Microsoft offers Azure-related incident readiness training. For more information, see [Introduction to Azure incident readiness](/training/technical-support/intro-to-azure-incident-readiness/) and
[Incident readiness](/services-hub/unified/health/incident-readiness). 

[Azure Monitor](/azure/azure-monitor/overview) is a comprehensive solution for collecting, analyzing, and responding to monitoring data from cloud and on-premises environments. It includes a robust alerting platform that you can configure for [automatic notifications and other actions](/azure/azure-monitor/alerts/action-groups), like automatic scaling and other self-healing mechanisms.

Use Monitor to integrate machine learning. Automate and optimize incident triage and proactive measures. For more information, see [AIOps and machine learning in Monitor](/azure/azure-monitor/logs/aiops-machine-learning).

[Log Analytics](/azure/azure-monitor/logs/log-analytics-overview) is a robust analytics tool that's built into Monitor. You can use Log Analytics to run queries against aggregated logs and gain insights about your workload.

[Application Insights](/azure/azure-monitor/app/app-insights-overview) is an extension of Monitor that provides application performance monitoring (APM) features.

[Microsoft Sentinel](/azure/sentinel/overview) is an SIEM and SOAR solution. It's a single solution for alert detection, threat visibility, proactive hunting, and threat response.

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) provides build and release services to support CI/CD of your applications.

[Azure Test Plans](/azure/devops/test/overview) is a browser-based test management solution that's easy to use. This solution offers capabilities that are required for planned manual testing, user acceptance testing, and exploratory testing. Azure Test Plans also provides a way for you to gather feedback from stakeholders.

[Azure Logic Apps](/azure/logic-apps/manage-logic-apps-with-azure-portal) is a cloud-based platform for running automated workflows that integrate apps, data, services, and systems. You can use Logic Apps to create a new version of your application whenever an update is made to it. Azure maintains a history of the versions and can revert or promote any previous version.

- Many Azure database services provide point-in-time restore functionality that can help you when you need to roll back:

  - [Azure SQL Database](/azure/azure-sql/database/recovery-using-backups)
  - [Azure SQL Managed Instance](/azure/azure-sql/managed-instance/point-in-time-restore)
  - [Azure Cosmos DB](/azure/cosmos-db/continuous-backup-restore-introduction)
  - [Azure Database for MySQL](/azure/mysql/flexible-server/concepts-backup-restore)
  - [Azure Database for PostgreSQL](/azure/postgresql/flexible-server/concepts-backup-restore)

- [Azure Chaos Studio Preview](/azure/chaos-studio/chaos-studio-overview) is a managed service that uses chaos engineering to help you measure, understand, and improve your cloud application and service resilience.

#### Related links

- [Progressive experimentation with feature flags](/devops/operate/progressive-experimentation-feature-flags)
- [Recommendations for designing and creating an observability framework](observability.md)
- [Recommendations for designing an emergency response strategy](emergency-response.md)
- [Recommendations for designing a reliability testing strategy](../reliability/testing-strategy.md)
- [Recommendations for designing a workload development supply chain](workload-supply-chain.md)
- [Recommendations for performing failure mode analysis](../reliability/failure-mode-analysis.md)
- [Recommendations for safe deployment practices](safe-deployments.md)
- [Recommendations for self-healing and self-preservation](../reliability/self-preservation.md)

## DUMP ZONE

Strategies:

An overly aggressive response strategy can lead to false alarms or unnecessary escalations. Balance the need for rapid response with the cost of disrupting normal operations.

Similarly, aggressively implementing automatic scaling or other self-healing actions to respond to threshold breaches can lead to unnecessary expenditures and management burden. You might not know the exact thresholds to set for alerting and automatic actions like scaling. Perform testing in lower environments and in production to help you determine the right thresholds for your requirements.

Support team members need to be able to perform their normal duties and also support emergencies. You might need to increase head count to help ensure that the support team is properly staffed and able to carry out all required duties.

Being able to fall back typically means that you need sufficient infrastructure capacity to handle two versions of your workload configuration at the same time. Your workload teams also need to be able to support two versions in production at the same time.

Being able to roll back effectively might involve refactoring elements of your workload. For example, you might need to decouple functions or change your data model.


#### Engage vendor support

Vendor support can be an essential step when you deal with ongoing performance issues. Vendors have the expertise, tools, resources, and experience to help fix issues with their products. Your support agreement with your supplier determines the level of support a vendor provides.

It's often best to work in parallel with vendors. You should create a plan to have some team members collaborate with vendor support while others continue to triage and fix performance issues. Vendor support teams can also make suggestions on how to help prevent and automate responses to similar events.

You need to have contact information available for your personnel. Vendors might also need access to data to effectively engage in problem-solving. You need to have a plan in place for authenticating and authorizing external or guest accounts to access monitoring data.