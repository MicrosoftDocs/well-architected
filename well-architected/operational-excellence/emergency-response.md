---
title: Emergency response recommendations
description: Learn how to design an emergensy response strategy for your workload. Set up a set of processes and procedures that everyone involved in the response will follow to ensure that the issue is handled in a calm, orderly manner.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for designing an emergency response strategy

**Applies to: OE 08**

This guide describes the best practices for designing an emergency response strategy. It is inevitable that issues will arise over the course of your workload’s lifecycle and that some of these issues will be critical enough to warrant declaring them emergencies. In these cases, you need a very tightly controlled and focused set of processes and procedures that everyone involved in the response will follow to ensure that the issue is handled in a calm, orderly manner. Emergencies naturally raise the stress of everyone involved and can lead to a chaotic environment if the team isn’t well-prepared, so designing your response strategy, sharing it with your organization, and training on it regularly will help you decrease stress and chaos.

## Key design strategies

Your emergency response strategy should be an orderly, well-defined set of processes and procedures that follow scripts to ensure that each step is part of a positive progression towards quickly and safely resolving the issue. The overall pattern of the strategy can be thought of as the following phases:

- **Prerequisites**
  - **Build an observability platform**
  - **Build an incident response plan**
- **Incident phases**
  - **Detection**
  - **Containment**
  - **Triage**
- **Post-incident phases**
  - **Root cause analysis (RCA)**
  - **Post-mortem:** 
- **Ongoing activity: emergency response drills**

The following sections in this guide provide recommendations for each of these phases.

### Observability

To have a robust emergency response strategy, you need to have a robust observability platform in place. Your observability platform should have the following characteristics to support your emergency response strategy:

- **Holistic monitoring**: Ensure that you are thoroughly monitoring your workload from an infrastructure and application perspective
- **Verbose logging:** Ensure that you have enabled verbose logging for your components to the extent possible to allow for successful investigations when you are triaging an issue. Logs should be structured so they are easy to manage, and they should automatically be sent to data sinks to be prepared for analysis.
- **Useful dashboards**: Different teams will be interested in different aspects of the workload health, so strive to build health model-based dashboards that are useful for the different teams across the organization.
- **Actionable alerts:** Create alerts that are useful for your workload teams – meaning, avoid alerts that cannot be acted upon, or do not need to be acted upon as having those kinds of alerts can lead to people ignoring or blocking alert notifications.
- **Automatic notifications:** Ensure that the appropriate teams are automatically notified for alerts that they will need to be engaged on. For example, your Tier 1 support team should get notifications for all alerts, whereas your security engineers should only get alerts specific to security events.

See the observability guide (link to oe07-observability) for detailed guidance on designing and building an observability framework.

### Incident response plan

The foundation of your emergency response strategy is your incident response plan. Like your disaster recovery plan, your incident response plan needs to be very clear and thorough about roles, responsibilities, and procedures to follow. The plan should be a version-controlled document that is subject to regularly occurring reviews to ensure that it is kept up to date. 

The following items should be clearly defined in your plan:

- **Roles to be defined**
  - An incident response manager should be identified. This individual owns the incident from initiation to remediation and root cause analysis processes. Furthermore, this person is responsible for ensuring that processes are followed, and the appropriate parties are kept informed as the response team does their work. 
  - A post-mortem leader should be identified. This individual ensures that post-mortems are performed soon after the incident is resolved and produces a report that can be used for applying the learnings that come out of the incident.
- **Processes and procedures to be documented**
  - Emergency criteria should be defined and understood by the workload team. In more severe cases, a disaster will be declared, and the disaster recovery plan should be initiated. In less severe cases, the issue might not meet the criteria of a disaster, but should still be considered an emergency, which necessitates the initiation of your emergency response plan. Remember that emergencies can be issues that are internal to your workload or they can be a result of an issue with a dependency of your workload, so the support team needs to be able to determine whether an issue that is reported by external users meets the emergency criteria, even though they have no visibility into the underlying issue.
  - Communication and escalation plans should be well-defined. Ensure that your Tier 1 support can easily find the appropriate teams to escalate issues to based on the type of alert notification they receive and that they know what type of communication is appropriate for internal and external parties. Ensure that the on-call schedule and staff lists are included or linked to in these plans.
  - Containment and triage scripts should be included in the overall plan. These are the step-by-step procedures that teams will follow when performing their containment and triage functions.
  - Definition of incident closure
- **Other items to include**
  - Emergency credentials, otherwise known as “break-glass accounts” should be documented along with a step-by-step guide describing how they are to be used.
  - Emergency response drill instructions and a record of when drills have been performed.
  - Any legal or regulatory measures necessary, like communicating data breaches for example.

### Incident detection

By having a well-designed observability platform that monitors for anomalies and alerts on them with automated notifications, you will be able to quickly detect issues and determine their severity. If the severity is deemed an emergency, the plan can be initiated. In some cases, the support team will not be notified through the observability platform. Customers may report issues to support over the support team communication avenues or they may reach out to individuals that they work with regularly, like account executives or VPs for example. No matter how the support team is notified, they should always follow the same steps to validate the issue and determine the severity. Deviation from their processes leads to a breakdown in the response plan and can add additional stress and chaos.

### Containment

The first step in the remediation of the issue is to contain it to protect the rest of your workload. Containment strategies depend on the type of issue, but in general, it involves removing the affected component(s) from the workload flow paths. This can mean shutting down a resource or removing it from network routing paths, for example. System administrators, engineers and senior developers should work together to design containment strategies that limit the blast radius of issues and allow for the workload to maintain functionality in a degraded state until the issue is resolved. If an affected component needs to be accessible to perform triage, it is vital that its access to the rest of the workload is blocked and you are only accessing it through a path that is separated from the workload and the rest of your systems to the extent possible.

### Triage

After you have successfully contained the issue, you can begin the triage work. The steps that you follow during triage will depend on the type of issue. Procedures specific to the type of incident should be built by teams responsible for different areas of workload support. For example, security issues should be triaged by security teams following scripts that they have developed. The important piece for building your plan is that teams follow well-defined scripts as they work through their triage efforts. These scripts should be step-by-step processes that include rollback processes to undo changes that are ineffective or can cause additional issues. Use off-the-shelf log aggregation and analysis tools to efficiently investigate issues that need deeper analysis. Once the issue has been resolved, ensure that there are well-defined processes to bring the affected component(s) back into the workload flow paths safely. 

### RCA reporting

Your SLAs to your customers may dictate that you issue RCA reports within a certain timeframe of issue resolution. The individuals responsible for creating the RCA reports should be the incident owner or they should be another individual that was involved throughout the incident working closely with the incident owner to ensure that an accurate accounting of the incident is recorded.

### Incident post-mortems

An impartial individual should lead blameless post-mortems. These sessions are meant to shine a light on what learnings can come out of the incident. Each team that was involved in the incident response should be represented by individuals that worked on the incident. Those individuals should come prepared to the session with examples of the things that they were involved in that went right and that can be improved on. There needs to be a common understanding that the session is not the right forum for assigning blame for the incident itself or issues that may have come up during the response. The post-mortem leader should come out of the session with a clear list of action items that focus on improvement. Improvement can include one or more of the following:

- Improvements to the response plan – processes or procedures may need to be reevaluated and rewritten to better capture appropriate actions.
- Improvements to the observability platform – thresholds may need to be reevaluated to catch the specific type of incident earlier or new monitoring may need to be enabled to catch behavior that had not been accounted for previously.
- Improvements to the workload – the incident may expose a vulnerability in the workload that must be addressed as a permanent remediation.

## Azure facilitation

[Azure Monitor](/azure/azure-monitor/overview)  is a comprehensive monitoring solution for collecting, analyzing, and responding to monitoring data from your cloud and on-premises environments. It includes a robust alerting platform that can be configured for [automatic notifications and other actions](/azure/azure-monitor/alerts/action-groups), like auto-scaling and other self-healing mechanisms.

[Log analytics](/azure/azure-monitor/logs/log-analytics-overview) is a robust analytics tool built-in to Azure Monitor that you can use to run queries against aggregated logs to gain insights about your workload.

Integrate machine learning with Azure Monitor to automate and optimize incident triage and proactive measures: [AIOps and machine learning in Azure Monitor](/azure/azure-monitor/logs/aiops-machine-learning) 

Microsoft offers Azure-related incident readiness trainings: [Introduction to Azure Incident Readiness](/training/technical-support/intro-to-azure-incident-readiness/)

[Incident readiness](/services-hub/unified/health/incident-readiness)

## Tradeoffs

Be mindful that an overly aggressive response strategy may lead to instances of false alarms or unnecessary escalations. 

Similarly, aggressively implementing automatic scaling or other self-healing actions to respond to threshold breaches can lead to unnecessary expenditures and management burden. You may not know the exact thresholds to set for your alerting and automatic actions like scaling, testing in lower environments and in production will help you determine the right thresholds for your requirements.

## Example

_Hold for IaaS_ _baseline architecture (WIP)_

## Related links

OE Observability guide

RE Monitoring guide

RE Self-preservation guide

## Next steps