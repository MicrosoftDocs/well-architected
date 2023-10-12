---
title: Recommendations for designing an emergency response strategy
description: Learn how to set up emergency response processes and procedures that your team can follow to ensure that an issue is handled in a calm, orderly manner.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for designing an emergency response strategy

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:**

|[OE:08](checklist.md)| Develop an effective emergency operations practice. Ensure that your workload emits meaningful health signals across infrastructure and code. Collect the resulting data and use it to generate actionable alerts that enact emergency responses via dashboards and queries. Clearly define human responsibilities, such as on-call rotations, incident management, emergency resource access, and running post-mortems.|
|---|---|

This guide describes the recommendations for designing an emergency response strategy. Some issues that arise over the course of a workload's lifecycle are critical enough to warrant declaring them emergencies. You can implement tightly controlled and focused processes and procedures that your team can follow to ensure that an issue is handled in a calm, orderly manner. Emergencies naturally raise everyone's stress levels and can lead to a chaotic environment if your team isn't well-prepared. To help minimize stress and confusion, design a response strategy, share the response strategy with your organization, and perform regular emergency response training.

## Key design strategies

An emergency response strategy should be an orderly, well-defined set of processes and procedures. Each process and procedure should have scripts to ensure that each step progresses your team towards quickly and safely resolving an issue. To develop an emergency response strategy, consider the following overview:

- Prerequisites
  - Develop an observability platform
  - Create an incident response plan
- Incident phases
  - Detection
  - Containment
  - Triage
- Post-incident phases
  - Root cause analysis (RCA)
  - Postmortem
- Ongoing activity
  - Emergency response drills

The following sections provide recommendations for each of these phases.

### Observability

To have a robust emergency response strategy, you need to have a robust observability platform in place. Your observability platform should have the following characteristics:

- **Holistic monitoring**: Ensure that you thoroughly monitor your workload from an infrastructure and application perspective.

- **Verbose logging**: Enable verbose logging for your components to assist with investigations when you triage an issue. Structure logs so that they're easy to manage. Automatically send logs to data sinks to be prepared for analysis.

- **Useful dashboards**: Create health model-based dashboards that are tailored to each team across your organization. Different teams are responsible for different aspects of workload health.

- **Actionable alerts**: Create alerts that are useful for your workload teams. Avoid alerts that don't require action from your teams. Too many alerts of this kind can lead to people ignoring or blocking alert notifications.

- **Automatic notifications**: Ensure that appropriate teams automatically receive alerts that require action from them. For example, your tier-1 support team should get notifications for all alerts, whereas your security engineers should only get alerts for security events.

For more information, see [Recommendations for designing and creating an observability framework](observability.md).

### Incident response plan

The foundation of an emergency response strategy is an incident response plan. Like a disaster recovery plan, clearly and thoroughly define roles, responsibilities, and procedures for an incident response plan. The plan should be a version-controlled document that's subject to regular reviews that ensure it's up to date.

Clearly define the following components in your plan.

##### Roles

Identify an incident response manager. This person owns the incident from initiation to remediation to the root cause analysis. An incident response manager ensures that processes are followed and the appropriate parties are informed as the response team performs their work.
  
Identify a postmortem leader. This individual ensures that post-mortems are performed soon after the incident is resolved. They produce a report, which helps you apply the findings that come out of the incident.

##### Processes and procedures

Your workload team should define and understand emergency criteria. When your team determines that a case is severe, you can declare a disaster and initiate the disaster recovery plan. In less severe cases, the issue might not meet the criteria of a disaster. But you should still consider the issue an emergency, which necessitates initiating the emergency response plan. Emergencies can be issues that are internal to your workload, or they can be a result of an issue with a dependency of your workload. The support team must be able to determine whether an issue that's reported by external users meets the emergency criteria, even if they have no visibility into the underlying issue.

Precisely define communication and escalation plans. Based on the type of alert notification that they receive, ensure that your tier-1 support can easily contact the appropriate teams to escalate issues to. Ensure that they know which type of communication is appropriate for internal and external parties. In communication and escalation plans, include a list of the on-call schedule and staff.

In the overall plan, include containment and triage scripts. Teams follow these step-by-step procedures when they perform their containment and triage functions. Include a description of what defines an incident closure.

##### Other items to include

Document your emergency credentials, otherwise known as *break-glass accounts*. Include a step-by-step guide that describes how they should be used.

Create emergency response drill instructions, and keep a record of when drills have been performed.

Document any legal or regulatory measures necessary, for example communicating data breaches.

### Incident detection

When you have a well-designed observability platform that monitors for anomalies and automatically alerts on them, you can quickly detect issues and determine their severity. If the issue is deemed an emergency, the plan can be initiated. In some cases, the support team isn't notified via the observability platform. Customers might report issues to support by using support team communication avenues. Or they might reach out to people that they regularly work with, like account executives or VPs. No matter how the support team is notified, they should always follow the same steps to validate the issue and determine the severity. Deviation from the response plan can add stress and confusion.

### Containment

The first step in issue remediation is to contain the issue to protect the rest of your workload. A containment strategy depends on the type of issue, but it usually involves removing the affected component from the workload flow paths. For example, you might shut down a resource or remove it from network routing paths. System administrators, engineers, and senior developers should work together to design containment strategies. The containment should limit the blast radius of issues and maintain workload functionality in a degraded state until the issue is resolved. If an affected component needs to be accessible to perform triage, it's vital that its access to the rest of the workload is blocked. As much as possible, you should only access the component via a path that's separated from the workload and the rest of the systems.

### Triage

After you successfully contain the issue, you can begin triage work. The steps that you follow during triage depend on the type of issue. The team  for a certain area of workload support should create procedures for incidents that are related to their team. For example, security teams should triage security issues, and they should follow scripts that they develop. It's important that teams follow well-defined scripts as they work through their triage efforts. These scripts should be step-by-step processes that include rollback processes to undo changes that are ineffective or can cause other issues. Use off-the-shelf log aggregation and analysis tools to efficiently investigate issues that require deep analysis. After the issue is resolved, follow well-defined processes to safely bring the affected component back into the workload flow paths.

### RCA reporting

The service-level agreements (SLAs) to your customers might dictate that you have to issue RCA reports within a certain time period after the incident is resolved. The incident owner should create the RCA reports. If that's not possible, another person that worked closely with the incident owner can create the RCA reports, which ensures an accurate accounting of the incident.

### Incident post-mortems

An impartial individual should lead blameless post-mortems. In postmortem sessions, everyone shares their findings from an incident. Each team that was involved in the incident response should be represented by individuals that worked on the incident. Those individuals should come to the session prepared with examples of the areas that were successful and areas that can be improved. The session isn't a forum for assigning blame for the incident or issues that might have come up during the response. The postmortem leader should leave the session with a clear list of action items that focus on improvement, such as:

- **Improvements to the response plan.** Processes or procedures might need to be reevaluated and rewritten to better capture appropriate actions.

- **Improvements to the observability platform.** Thresholds might need to be reevaluated to catch the specific type of incident earlier, or new monitoring might need to be implemented to catch behavior that wasn't accounted for.

- **Improvements to the workload.** The incident might expose a vulnerability in the workload that must be addressed as a permanent remediation.

## Azure facilitation

[Azure Monitor](/azure/azure-monitor/overview) is a comprehensive solution for collecting, analyzing, and responding to monitoring data from cloud and on-premises environments. It includes a robust alerting platform that you can configure for [automatic notifications and other actions](/azure/azure-monitor/alerts/action-groups), like automatic scaling and other self-healing mechanisms.

Use Monitor to integrate machine learning. Automate and optimize incident triage and proactive measures. For more information, see [AIOps and machine learning in Monitor](/azure/azure-monitor/logs/aiops-machine-learning).

[Log Analytics](/azure/azure-monitor/logs/log-analytics-overview) is a robust analytics tool that's built into Monitor. You can use Log Analytics to run queries against aggregated logs and gain insights about your workload.

Microsoft offers Azure-related incident readiness training. For more information, see [Introduction to Azure incident readiness](/training/technical-support/intro-to-azure-incident-readiness/) and
[Incident readiness](/services-hub/unified/health/incident-readiness).

## Tradeoffs

An overly aggressive response strategy can lead to false alarms or unnecessary escalations.

Similarly, aggressively implementing automatic scaling or other self-healing actions to respond to threshold breaches can lead to unnecessary expenditures and management burden. You might not know the exact thresholds to set for alerting and automatic actions like scaling. Perform testing in lower environments and in production to help you determine the right thresholds for your requirements.

<!--
## Example

Placeholder for the IaaS baseline architecture (WIP)
-->

## Related links

- [Recommendations for designing and creating an observability framework](observability.md)
- [Recommendations for designing a reliable monitoring and alerting strategy](../reliability/monitoring-alerting-strategy.md)
- [Recommendations for self-healing and self-preservation](../reliability/self-preservation.md)

## Operational Excellence checklist  

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Operational Excellence checklist](checklist.md) 
