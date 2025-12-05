---
title: Develop an Incident Management Practice to Recover from Disruptions
description: Learn how to design comprehensive incident response processes for Azure workloads, including detection, containment, mitigation, and recovery strategies.
author: PageWriter-MSFT 
ms.author: prwilk 
ms.date: 11/18/2025
ms.topic: concept-article
ms.update-cycle: 1095-days  
---

# Create an effective incident management plan to manage disruptions

An *incident* is an unplanned event that disrupts, degrades, or threatens to disrupt the normal operation of a system. Incidents often negatively affect customers or a business. They exist on a spectrum, from transient or localized disruptions to widespread events or disasters. Examples of security incidents include data breaches, regulatory violations, malware, or identity compromises. Causes include hardware or infrastructure failures, resource limits, human errors like failed deployments or misconfigurations, or external factors like security attacks.

*Incident management (IcM)* provides a systematic approach to restore service during disruptions. It coordinates detection, investigation, mitigation, and resolution while maintaining clear communication and documenting insights for continuous improvement. Incident response follows the same playbook regardless of the incident type.

When you create your IcM plan, don't focus only on dashboards and runbooks. As an architect, design operations where people, processes, and tools work together under pressure to restore systems efficiently and without chaos. Your IcM strategy should scale based on severity, from rapid mitigation for minor incidents to coordinated cross-team efforts for major events. Response can also vary depending on the cause.

This article focuses on the phases of IcM, which include preparation, active incident response, post-incident review, and ongoing improvement. This guidance also includes an example to illustrate the design practices. Before you begin, review the key strategies and choose the ones that make sense for your business. For more information, see [OE:08 Architecture strategies for designing an IcM process](../operational-excellence/incident-response.md).

This article doesn't cover disasters that require specialized recovery efforts. For more information about handling such scenarios, see [Develop a disaster recovery plan for multi-region deployments](./disaster-recovery.md).

## Terminology

| Term | Definition |
|---|---|
| **Blast radius** | The scope of impact or affected area when an incident occurs, which containment strategies aim to limit. |
| **Break-glass accounts** | Emergency credentials that have elevated privileges used during critical incidents, strictly controlled with clear usage guidelines. |
| **Bridge team** | The triage team that assembles to investigate an incident, also called the *engineering bridge*. It consists of relevant technical experts and decision-makers. |
| **Containment** | The first step in incident remediation that isolates affected components to prevent the problem from spreading to other parts of the workload. |
| **Incident management (IcM)** | The structured process of detecting, triaging, mitigating, and resolving incidents while coordinating communication and capturing lessons learned. |
| **Last-known-good** | The last healthy state of the workload before an incident began, used as a reference point for rollback operations. |
| **Mitigation** | Actions to reduce or remove the impact of an incident, including rollback, fallback, bypass, or emergency fixes. |
| **Retrospective** | A blameless post-incident review process focused on identifying lessons learned and actionable improvements rather than assigning blame. |
| **Root cause analysis (RCA)** | A systematic process of investigating an incident to identify the underlying factors responsible for the problem and prevent recurrence. |
| **Severity level** | A classification system, like critical, high, medium, and low, that determines the appropriate response level based on business impact and affected users. |
| **Triage** | The process of analyzing and prioritizing incidents to determine severity, impact, and appropriate response actions. |

## Preparation

Before an incident happens, set up the foundation for effective response by designing observability, defining clear roles and processes, and preparing the tools and resources that your teams needs. Document the incident response plan and update and review it regularly.

1. Maintain accurate, up-to-date architecture diagrams that show all components and their interactions to help teams quickly identify bottlenecks or single points of failure. This approach enables faster troubleshooting during high-pressure situations.

1. Define the incident data that your monitoring stack must provide for the entire workload:

   - Collect end-to-end telemetry, including infrastructure and applications.

   - Enable structured logging for all components to support triage and investigation, and send logs to data sinks for analysis. If necessary, also forward logs to centrally managed sinks. Make sure that team members have time-limited, least-privilege access during incidents.

   - Create dashboards based on the workload health model. The dashboards show metrics and signals that your team monitors.

   - Configure actionable alerts that trigger notifications only when thresholds are exceeded and a potential incident is detected. Tune alerts to avoid too many alerts (noise) or too few alerts. For example, for live-site incidents, alerts should monitor critical metrics like central processing unit (CPU), memory, response times, and database performance to detect actionable problems that align with performance targets.
   
     Route alerts automatically to the right teams. For example, tier-1 support gets all alerts, while security engineers receive only security-related alerts.

   For more information, see [Recommendations for designing and creating an observability framework](../operational-excellence/observability.md).

1. Define key roles to ensure accountability, decision-making, and effective follow-up during and after incidents. Create appropriate approval processes and codify them in a clear decision tree. Define authorization levels for different types of severity, mitigation decisions, escalations paths, and other areas.

   Use the following examples as a starting point and adapt them to fit your team structure. 

   | Role | Responsibilities |
   |------|-----------------|
   | **Incident Response Manager** | Owns the incident from detection through resolution and RCA. Ensures that processes are followed, decisions are made, and the right people are informed. |
   | **Retrospective Leader** | Leads post-incident reviews, captures lessons learned, produces actionable reports, and ensures that findings are applied. |
   | **On-Call Engineer** | Actively mitigates and resolves incidents. Follows responsibilities for different incident types and collaborates with specialized teams as needed to ensure timely resolution of the incident. |

1. Define procedures for investigation:

   - Define categories for types of incidents—like operational, security, performance, and deployment incidents—and severity levels—like critical, high, medium, and low—that apply to your workload.

   - Document how to evaluate the severity, impact, and urgency of the incident. Consider user impact, affected systems, and business-critical functions. Based on the severity level, the team activates the disaster recovery plan for cases that meet disaster-level thresholds. Or they follow the standard response plan for less-severe incidents.

   - Define strategies that isolate or remove the affected component from workload flow paths, like shutting down a resource or rerouting traffic. Identify which roles have authority to take containment action.
    
     Set monitoring systems to automatically initiate containment for defined incidents. This approach keeps humans in the loop for critical decisions. System administrators, engineers, and senior developers should collaborate to limit the blast radius while maintaining degraded functionality. If a component must remain available for triage, strictly isolate its access from the rest of the workload.

   - Create guidelines for selecting mitigation strategies based on incident severity, like isolation, rollback, configuration changes, and workarounds.

   - Specify which teams or individuals handle the incident based on type and severity. Include escalation paths for when initial responders can't resolve the problem.

   - Outline what data to collect during triage, like logs, metrics, user reports, and alerts, to support investigation and decision-making. Also include who should have access to that data.

   > [!IMPORTANT]
   > Safeguard your emergency credentials, or break-glass accounts, by establishing clear rules. Determine who can use them, when, and exactly how. Pair those rules with emergency drills and track every run. Define how frequently the team should practice drills. During an incident, there's no time to figure it out in the moment. Drills provide adequate practice and refinement opportunities.

1. Set up your infrastructure to support resolution:

   - Parameterize pipelines. Enable deployment and recovery pipelines to accept specific versions for rapid rollback or to fix deployment.

   - Ensure data plane consistency. Keep keys, secrets, configurations, and state data aligned during recovery operations.

   - Automate infrastructure scaling. Adjust resources automatically to handle traffic shifts or increased load.

   - Implement self-healing. Safely automate responses to common incident patterns, but include proper monitoring and options for manual override.

1. Define communication processes. Document clear communication and escalation plans so that tier-1 support can quickly reach the right teams. Specify appropriate communication channels for internal and external stakeholders, and include on-call schedules and contact details.

1. Define the usage of an IcM tool and the standard operating procedures that it should capture in a simple workflow. The workflow includes four steps: create, acknowledge, mitigate, and resolve. The tool gives teams visibility, tracks progress, maintains accountability, and ensures consistent incident handling at all times. It centralizes all activities to support live site management and on-call rotations.
            
1. Define the criteria that officially mark an incident as closed:

   - Include clear resolution factors. Typically, resolution means that the system and services operate within service-level agreements (SLAs), performance and reliability return to acceptable levels, and immediate mitigation actions successfully complete.

   - Include validation checks to confirm full resolution. Use monitoring tools to check that the incident no longer affects the system and affected users no longer experience disruption.

   - Include communication requirements when you close an incident. Notify relevant stakeholders, including internal teams, support staff, and affected users, and provide a summary of actions taken along with ongoing work.

   - Include thorough documentation when you close an incident. Record all details in the IcM system. Include the trigger, containment steps, triage decisions, and the final resolution. Treat this documentation as the handoff for RCA and the retrospective to capture lessons learned.

   > [!IMPORTANT]
   > Design your operations so that only the designated authority, like the Incident Response Manager, can close an incident. Enforce strict checklists to block premature closure. Skipping steps can leave hidden problems unresolved, which can turn a *closed* incident into a repeat disaster.


## Detect, investigate, and respond

Phase 2 focuses on detecting and responding to incidents quickly and effectively. This phase identifies problems early, assesses their impact, and implements the right mitigation strategies while containing disruption. This phase also ensures that triage, resolution, and communication are coordinated, consistent, and accountable across all teams.

> [!IMPORTANT]
> Clear, consistent communication maintains control and clarity in high-stress situations. Define exactly who speaks, what gets shared, and how often. Standardize update cadence, channels, and message formats so that no one scrambles for information during the crisis. Make sure that every stakeholder, from engineers to executives, knows when to expect updates and when escalation is required.

1. Act immediately when alerts or user reports indicate a problem. Use observability and performance tools to correlate anomalies with system changes. The incident recipient sets up a triage (*bridge*) team with the right members and agrees on communication mode, progress tracking, and access to incident assets.

1. Mobilize the engineering bridge to evaluate the impact and severity. Evaluate the impact of the incident by using your predefined severity classifications. Use data to justify criteria outlined in the incident response plan, like the number of users affected, business functions disrupted, security and compliance implications, and potential impact on customer trust and reputation. This assessment determines the appropriate response level and guides the next mitigation steps.

1. The investigation stage begins when the right engineering teams are engaged and start an RCA. This process involves deep technical analysis to pinpoint the cause and contain the impact. Engineers use observability data, telemetry dashboards, system logs, and change histories to trace anomalies and identify failure points. They focus on isolating the problem quickly, validating hypotheses by using real-time data, and developing a precise mitigation plan that restores service stability without introducing new risk.

    > :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** An RCA can take significant time. To correlate performance problems, you must collect and store data. The required time and infrastructure can add extra work to the operations teams and cost to the workload.

    > :::image type="icon" source="../_images/risk.svg"::: **Risk:** If you perform an RCA without proper security guardrails, you might expose sensitive information when you provide access to logs and data.

1. Follow the containment strategies to isolate affected components and limit the blast radius. You might take the following actions:

   - Block access to affected components by using separate paths for triage. For example, you might shut down a resource, throttle traffic, or disable a failing microservice.

   - Limit the incident's reach to specific users, regions, or components.
   
   - Maintain workload functionality in a degraded state if possible.



1. <a name="mitigation-strategies"></a>Select an appropriate mitigation strategy. Choose mitigation approaches based on the current state of the workload, available resources, and immediate constraints. 

   Your choice depends on factors like infrastructure type, available bypass mechanisms, complexity of the fix, data sensitivity and compliance requirements, system dependencies, and recovery time objectives (RTOs).

   - **Rollback:** Revert updated systems to the last-known-good configuration state. The workload team should define what last known good means. It typically refers to the last healthy state of the workload before the deployment began, which might not be the immediately prior application version. Rolling back can be complex, especially when schema or data changes are involved. To reduce risk, make schema updates additive instead of replacing records. The old and new data can coexist until you can safely remove deprecated records. Rollbacks might require careful planning and coordination across multiple teams.

   - **Fallback:** Remove updated systems from production traffic routing and direct all traffic to the stable stack. This low-risk strategy addresses deployment problems without causing further disruptions. Fallback in canary deployments can be complicated depending on infrastructure and application design. Ensure adequate capacity on the stable stack before switching traffic back. Fallback supports continued operation and isolates the problematic deployment.

   - **Bypass the offending function:** Use feature flags or runtime configuration properties to bypass the problematic functionality. This approach lets the rollout continue and isolates the problem. Evaluate trade-offs and communicate them to stakeholders. Include how long you can tolerate a degraded state and the estimated time to fully resolve the problem. Obtain stakeholder approval for the plan.

   - **Emergency deployment (hot fix):** Deploy a hot fix during the rollout to address the problem quickly. Follow safe deployment practices, including code promotion through environments and quality gate checks, but accelerate timelines. Shorten or modify bake times and tests to speed deployment. Use automated testing to ensure reliability. Hot fixes require coordination and careful planning to minimize risk and resolve the problem promptly.

   > [!IMPORTANT]
   > Ensure that mitigation decisions follow predefined authorization rules. The Incident Manager should handle all mitigation actions. Require authorized personnel to approve high-impact steps and document every action. Keep the actions controlled, safe, and accountable while you restore the system.

1. Apply the resolution. This step focuses on restoring the system to full operational state while preventing recurrence. Engineering teams apply verified fixes that follow team-specific, scripted procedures. They use log analysis and monitoring tools to guide investigation. Rollback steps undo ineffective changes to ensure that every action drives the system steadily toward complete recovery.

1. Generate an RCA report. After you resolve an incident, generate the RCA report within the SLA timeframe. The incident owner, or a closely involved team member if the owner is unavailable, should create the report to ensure accuracy. Follow a defined RCA template that has clear guidelines about what information to include and share, or create and approve a new template through stakeholder review.

## Post-incident activities

Do retrospectives after every incident. They provide critical learning opportunities, highlight weaknesses in response, deployment, or infrastructure, and inform improvements. Document action items and track them in a backlog for iterative implementation.

The goal isn't to assign blame but to identify actionable improvements. An impartial facilitator should lead this process. Individuals who worked on the response must represent each team involved in the incident. They must come prepared with observations about successes and areas for improvement:

- **Response plan enhancements:** Update processes or procedures to ensure clearer, more effective actions.

- **Observability improvements:** Adjust thresholds, add monitoring, or implement alerts to detect similar incidents earlier.

- **Workload remediation:** Address vulnerabilities exposed during the incident for permanent fixes.

## Example: Deployment failure response

The following example describes a deployment-related incident. Even with careful planning and testing, deployments can sometimes introduce problems that affect system performance or user experience.

In this example, a workload team rolls out a search enhancement feature that includes multiple components:

- A new search API endpoint that has enhanced filtering functionality
- An updated database schema
- A redesigned user interface (UI) search widget
- New caching logic

The team does the following steps to detect, mitigate, and resolve the incident:

1. **Detection:** The team notices a problem when error rates spike in one of the canary rollout groups. The team immediately uses their observability tools, like application performance monitoring (APM), logging, and telemetry that links users to rollout phases, to pinpoint the affected group.

    The team prepared for this scenario by building strong observability into their deployment process. They ran smoke tests and quality checks at each rollout phase and instrumented their application with logging, tracing, and performance metrics. Telemetry links users to specific rollout groups, so they can quickly identify which version affected which users. They also scheduled deployments during working hours when full support was available, and ensured that support staff knew how to escalate problems according to the emergency response plan. This preparation allows them to detect the spike in error rates quickly and respond without delay.

1. **Mitigation:** The team quickly decides on a mitigation strategy. They consider whether to roll back to the last-known-good version, fall back to the stable environment, bypass the problematic function by using a feature flag, or deploy a hot fix. The decision tree and approval process are already defined.

   After the team reviews all [available strategies](#mitigation-strategies), they narrow their choice to either rollback or fallback, and ultimately decide on fallback. They determine that redirecting traffic to the stable stack is faster and lower risk than a full rollback, which could require complex data and schema operations. The team confirms that the stable stack has enough capacity to handle the full production load and they can isolate the updated systems from production traffic routing.

   The team bundled multiple interdependent changes together, including the API, database schema, UI components, and caching logic, so identifying the specific component that caused the errors is more challenging and time consuming than if they had deployed each change separately.

1. **Resolution:**  The team implements the fallback procedure. They shift traffic away from the updated environment to isolate the problematic deployment. The team can address the underlying problem without affecting most users.

   The fallback implementation reveals operational gaps. They find outdated contact information for two key team members, and one engineer in the escalation chain left the company months earlier. The team loses time figuring out the responsible individual. When they try to remove the updated deployment from the load balancer, the documentation references an outdated load balancer configuration that changed in a recent Azure update. They have to find the correct procedure under time pressure.

    Communication is a key part of the mitigation plan. The team informs stakeholders of the decision and its implications, including the expected timeline to resolve the problem in the isolated environment. The team already standardized the cadence for providing status updates during deployment incidents, so stakeholders know when to expect progress reports and updates. The team also defined the type and level of detail to share with users and ensured compliance with other requirements for deployment incident communications. This structured approach minimizes confusion and helps maintain confidence in the response process.

1. **Retrospective:** After the team mitigates the deployment incident, they do a retrospective to capture lessons learned and improve future processes. The session includes everyone involved in the rollout, from developers and operators to support and stakeholder representatives. The team reviews the sequence of events, from detection through mitigation, to understand what went well and where gaps existed.

     A key takeaway is that bundling the search API changes, database schema updates, UI redesign, and caching layer changes complicated both troubleshooting and recovery efforts.

1. **Post-incident improvements:** From the retrospective, the team implements several operational improvements to make future deployments safer and mitigations more reliable:

   - **Smaller, frequent changes:** The team shifts toward smaller, incremental deployments to reduce the delta between successive versions and make mitigation simpler and lower risk.

      For the search enhancement specifically, the team decides to deploy each component independently. They now deploy the database schema changes with backward-compatible changes first, then the API endpoint updates, and then other changes. This approach enables the team to validate each layer independently before they add the next layer, and more easily isolate problems to a specific component.

   - **Regular testing and drills:** The team establishes a practice of frequent testing for the full deployment failure mitigation strategy. They introduce chaos engineering and fault injection tests to simulate failure scenarios and validate mitigation processes. These regular drills would have caught the problems encountered during the incident, including outdated contact information and outdated runbook procedures.

## Azure facilitation

- Microsoft provides Azure-related incident readiness training. For more information, see [Introduction to Azure incident readiness](/training/technical-support/intro-to-azure-incident-readiness/) and [Incident readiness](/services-hub/unified/health/incident-readiness).

- [Azure Monitor](/azure/azure-monitor/overview) is a solution for collecting, analyzing, and responding to monitoring data from cloud and on-premises environments. It includes an alerting platform that you can configure for [automatic notifications and other actions](/azure/azure-monitor/alerts/action-groups), like autoscaling and other self-healing mechanisms.

  Use Azure Monitor to integrate machine learning. Automate and optimize incident triage and proactive measures. For more information, see [AI operations and machine learning in Azure Monitor](/azure/azure-monitor/logs/aiops-machine-learning).

  - [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview) is an analytics tool in Azure Monitor. You can use Log Analytics to run queries against aggregated logs and gain insights about your workload.

  - [Application Insights](/azure/azure-monitor/app/app-insights-overview) is an extension of Azure Monitor that provides APM features.

- [Microsoft Sentinel](/azure/sentinel/overview) is a security information and event management (SIEM) and security orchestration, automation, and response (SOAR) solution. It's a single solution for alert detection, threat visibility, proactive hunting, and threat response.

- [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) provides build and release services to support continuous integration and continuous delivery (CI/CD) of your applications.

- [Azure Test Plans](/azure/devops/test/overview) is a browser-based test management solution. This solution provides capabilities required for planned manual testing, user acceptance testing, and exploratory testing. Azure Test Plans also provides a way for you to gather feedback from stakeholders.

- [Azure Logic Apps](/azure/logic-apps/manage-logic-apps-with-azure-portal) is a cloud-based platform for running automated workflows that integrate apps, data, services, and systems. You can use Logic Apps to create a new version of your application when it gets an update. Azure maintains a history of the versions and can revert or promote any previous version.

- Many Azure database services provide point-in-time restore (PITR) functionality that can help you when you need to roll back:

  - [Azure SQL Database](/azure/azure-sql/database/recovery-using-backups)
  - [Azure SQL Managed Instance](/azure/azure-sql/managed-instance/point-in-time-restore)
  - [Azure Cosmos DB](/azure/cosmos-db/continuous-backup-restore-introduction)
  - [Azure Database for MySQL](/azure/mysql/flexible-server/concepts-backup-restore)
  - [Azure Database for PostgreSQL](/azure/postgresql/flexible-server/concepts-backup-restore)

- [Azure Chaos Studio](/azure/chaos-studio/chaos-studio-overview) is a managed service that uses chaos engineering to help you measure, understand, and improve your cloud application and service resilience.

## Related links

- [Recommendations for designing an incident response strategy](../operational-excellence/incident-response.md)
- [Develop a disaster recovery plan for multi-region deployments](./disaster-recovery.md)
