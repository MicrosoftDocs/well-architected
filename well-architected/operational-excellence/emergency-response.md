---
title: Recommendations for designing an emergency response strategy
description: Learn how to design an emergency response strategy for your workload. Set up a set of processes and procedures that everyone involved in the response will follow to ensure that the issue is handled in a calm, orderly manner.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for designing an emergency response strategy

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:**

|[OE:08](checklist.md)| Develop an effective emergency operations practice. Ensure that your workload emits meaningful health signals across infrastructure and code. Collect the resulting data and use it to generate actionable alerts that enact emergency responses via dashboards and queries. Clearly define human responsibilities, such as on-call rotations, incident management, emergency resource access, and running post-mortems.|
|---|---|

This guide describes the recommendations for designing an emergency response strategy. Issues will arise over the course of your workload’s lifecycle, and some of those issues will be critical enough to warrant declaring them emergencies. You must implement tightly controlled and focused processes and procedures that your team can follow to ensure that an issue is handled in a calm, orderly manner. Emergencies naturally raise everyone's stress levels and can lead to a chaotic environment if your team isn’t well-prepared. To help minimize stress and chaos, design a response strategy, share it with your organization, and perform regular response training.

## Key design strategies

Your emergency response strategy should be an orderly, well-defined set of processes and procedures. Each process and procedure should follow scripts to ensure that each step progresses towards quickly and safely resolving the issue. Think of the overall pattern of the strategy as the following phases:

- **Prerequisites**
  - **Develop an observability platform**
  - **Create an incident response plan**
- **Incident phases**
  - **Detection**
  - **Containment**
  - **Triage**
- **Post-incident phases**
  - **Root cause analysis (RCA)**
  - **Post-mortem**
- **Ongoing activity: Emergency response drills**

The following sections provide recommendations for each of these phases.

### Observability

To have a robust emergency response strategy, you need to have a robust observability platform in place. Your observability platform should have the following characteristics to support your emergency response strategy:

- **Holistic monitoring**: Ensure that you thoroughly monitor your workload from an infrastructure and application perspective.

- **Verbose logging**: Ensure that you enable verbose logging for your components to the extent possible to allow for successful investigations when you triage an issue. Structure logs so that they're easy to manage. Automatically send logs to data sinks to be prepared for analysis.

- **Useful dashboards**: Different teams are responsible for different aspects of the workload health. Create health model-based dashboards that are useful for different teams across your organization.

- **Actionable alerts**: Create alerts that are useful for your workload teams. Avoid alerts that your team can't act on or don't need to be acted on. Too many alerts of this kind can lead to people ignoring or blocking alert notifications.

- **Automatic notifications**: Ensure that appropriate teams are automatically notified for alerts that they need to act on. For example, your tier-1 support team should get notifications for all alerts, whereas your security engineers should only get alerts specific to security events.

For more information, see [Recommendations for designing and creating an observability framework](observability.md).

### Incident response plan

The foundation of an emergency response strategy is an incident response plan. Like a disaster recovery plan, an incident response plan needs to clearly and thoroughly define roles, responsibilities, and procedures. The plan should be a version-controlled document that's subject to regularly occurring reviews that ensure it's up to date.

Clearly define the following items in your plan:

#### Roles

Identify an incident response manager. This person owns the incident from initiation to remediation to root cause analysis processes. An incident response manager ensures that processes are followed and the appropriate parties are informed as the response team performs their work.
  
Identify a post-mortem leader. This individual ensures that post-mortems are performed soon after the incident is resolved. They produce a report that can be used for applying the learnings that come out of the incident.

#### Document processes and procedures

Your workload team should define and understand emergency criteria. In severe cases, declare a disaster, and initiate the disaster recovery plan. In less severe cases, the issue might not meet the criteria of a disaster. But you should still consider the issue an emergency, which necessitates initiating the emergency response plan. Emergencies can be issues that are internal to your workload, or they can be a result of an issue with a dependency of your workload. The support team must be able to determine whether an issue that's reported by external users meets the emergency criteria, even though they have no visibility into the underlying issue.

Precisely define communication and escalation plans. Ensure that your tier-1 support can easily find the appropriate teams to escalate issues to based on the type of alert notification they receive. Ensure that they know what type of communication is appropriate for internal and external parties. In the plans, include a list of the on-call schedule and staff.

Include containment and triage scripts in the overall plan. Teams follow these step-by-step procedures when they perform their containment and triage functions.

Definition of incident closure

#### Other items to include

Document emergency credentials, otherwise known as *break-glass accounts*. Include a step-by-step guide that describes how they should be used.

Create emergency response drill instructions, and keep a record of when drills have been performed.

Document any legal or regulatory measures necessary, for example communicating data breaches.

### Incident detection

When you have a well-designed observability platform that monitors for anomalies and automatically alerts on them, you can quickly detect issues and determine their severity. If the severity is deemed an emergency, the plan can be initiated. In some cases, the support team isn't notified via the observability platform. Customers might report issues to support by using the support team communication avenues. Or they might reach out to people that they regularly work with, like account executives or VPs. No matter how the support team is notified, they should always follow the same steps to validate the issue and determine the severity. Deviation from the response plan can add stress and confusion.

### Containment

The first step in issue remediation is to contain the issue to protect the rest of your workload. Containment strategies depend on the type of issue, but it usually involves removing the affected component from the workload flow paths. For example, you might shut down a resource or remove it from network routing paths. System administrators, engineers, and senior developers should work together to design containment strategies. The containment should limit the blast radius of issues and maintain workload functionality in a degraded state until the issue is resolved. If an affected component needs to be accessible to perform triage, it's vital that its access to the rest of the workload is blocked. If possible, you should only access the component via a path that's separate from the workload and the rest of the systems.

### Triage

After you successfully contain the issue, you can begin triage work. The steps that you follow during triage depend on the type of issue. The team  for a certain area of workload support should create the specific procedures for a type of incident. For example, security teams should triage security issues, and they should follow scripts that they develop. It's important that teams follow well-defined scripts as they work through their triage efforts. These scripts should be step-by-step processes that include rollback processes to undo changes that are ineffective or can cause additional issues. Use off-the-shelf log aggregation and analysis tools to efficiently investigate issues that need deep analysis. After the issue is resolved, follow well-defined processes to safely bring the affected component back into the workload flow paths.

### RCA reporting

The service-level agreements (SLAs) to your customers might dictate that you have to issue RCA reports within a certain time period. The incident owner should create the RCA reports. If that's not possible, another person that worked closely with the incident owner can create the RCA reports, which ensures an accurate accounting of the incident.

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

<!--
## Example

Placeholder for IaaS baseline architecture (WIP)
-->

## Related links

OE Observability guide

RE Monitoring guide

RE Self-preservation guide

## Next steps

We recommend that you review the Operational Excellence checklist to explore other concepts.

> [!div class="nextstepaction"]
> [Operational Excellence checklist](checklist.md)