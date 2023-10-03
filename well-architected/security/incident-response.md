---
title: Incident response recommendations for security
description: Learn about incident response recommendations for security. Apply a systematic approach to reduce the time to identify, manage, and mitigate security incidents that may threaten the confidentiality, integrity, or availability of software systems and data.
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for security incidence response

**Applies to Well-Architected Framework Security checklist recommendation:**

|[SE:12](checklist.md)|Define and test effective incident response operational procedures that cover a spectrum of incidents, from localized to disaster recovery. Procedures must state clear ownership for execution.|
|---|---|

If there's a security compromise to the system, a systematic incident response approach helps to reduce the time to identify, manage, and mitigate security incidents that may threaten the confidentiality, integrity, or availability of software systems and data. 

Most enterprises have a central function of security operations center (SOC). That team mostly handles incidents. However, the workload has a responsibility as well. This guide provides recommendations for the workload team to rapidly detect and triage potential attacks so that your team can investigate real attacks quickly.

**Definitions** 

| Terms | Definition |
|---|---|
|Incident |An event that indicates an unauthorized access to the system.|
|Incident response| The process to detect, respond, and mitigate risks associated with an incident.|
|Alert |A notification that contains information about an incident. |
|Alert fidelity|The quality of an alert. High fidelity alerts contain enough security context to take immediate actions. Low fidelity alerts lack information or contain noise.|
|False positive|A false alarm that indicates an incident but it didn't happen. |
|Triage|A part of incident response that analyzes security issues and prioritizes the mitigation.  |

## Key design strategies

Incident response operations start when there's a potential compromise signal or alert. 

High fidelity alerts contain enough security context that makes it easier for analysts to make decisions. The higher the fidelity the lower the false positives. This guide assumes that the alerting system filters low fidelity signals, therefore focuses on high fidelity alerts that could potentially indicate a real incident. 

### Assign incident notification

Security alerts need to reach the right people in your workload team and organization. Establish a designated point of contact from the workload team to receive incident notifications. These notifications should include as much information as possible about the resource that's compromised and system. The alert must include the next steps to expedite actions.

It's recommended that incident notification and actions are logged and managed through specialized tooling primarily for the purposes of keeping an audit trail. By using standard tools, you're more likely to preserve evidence that might be required for any subsequent legal investigations. Explore automation opportunities to send notifications based on the responsibilities of the accountable parties. Keep a clear chain of communication and reporting during an incident.

Take advantage of the security information event management (SIEM) and security orchestration automated response (SOAR) solutions provided by the organization. Otherwise, it's recommended that you procure incident management tools and encourage the organization to standardize it for all workload teams.

### Discover with a triage team

The notified team member is responsible for setting up the triage process by involving the right set of people based on the available data. The triage team (often called the _bridge team_) must agree on the mode and process of communication. Does this incident require asynchronous discussions or have bridge calls? How should the team track and communicate the progress of investigations?  Where can the team access incident assets? 

Incident response is a crucial reason for keeping documentation up to date. This includes architectural layout of the system, information at a component level, privacy or security classification, owners, and key points of contact. If the information is inaccurate or outdated, the bridge team will waste valuable time trying to understand how the system works, who is responsible for each part, and what the impact of the event might be.

For further investigations, involve the right people with the right focus. This might involve the functions of incident manager, security officer, and workload centric leads. At the same time, to keep the triage focused, exclude people who are outside the scope of problem space.  There might be separate teams investigating the incident. A team that initially investigates the issue and tries to mitigate the incident. Another specialized team might perform forensics for a deeper investigation to ascertain wider issues. The workload environment can be quarantined for the forensics team to do their investigations. In some cases, they might be the same team.

In this initial phase, the triage team is responsible for determining the potential vector and its impact to confidentiality, integrity, and availability (CIA) of the system. 

Within the categories of CIA, assign an initial severity level that indicates the depth of damage and urgency of remediation. This level is expected to change over time as more information is known through several levels of triage. 

In the discovery phase, it's also important to decide on an immediate course of action and communication. Are there any changes to the running state of the system? How can the attack be contained to stop further exploitation. Does the team need to send out internal or external communication, such as responsible disclosure? Consider detection and response time. You might have legal obligations to report some types of breaches to a regulatory authority within a prescribed time period, which is often measured in hours or days.

If the decision is to shut down the system, the next steps will eventually lead to the workload's disaster recovery process. 

If the resolution isn't to shut down the system, determine how to remediate the incident without impacting the functionality of the system. 

### Recover from the incident

A security incident should be treated like a disaster. If the remediation requires complete recovery, use proper disaster recovery (DR) mechanisms from a security perspective. The recovery process must prevent chances of recurrence.  Otherwise, recovery from a corrupted backup will only reintroduce the issue, redeploying a system with the same vulnerability will just lead to the same incident. Validate failover and failback steps and processes. 

If the decision is to allow the system to function, Measure the impact on the running parts of the system. Continue to monitor the system to make sure other reliability and performance targets are met or readjusted with proper degradation processes. Privacy shouldn't be compromised due to mitigation.

Diagnosis is an interactive process until the vector and a potential fix and fallback, is identified. After this, the team works on remediation, identifying and applying the right fix within an acceptable period. 

Recovery metrics measure the time taken to fix the issue. In the event of a shutdown, there might be an urgency regarding the remediation times. It takes time to stabilize the system in applying fixes, patches, testing and deploying the updates. Determine containment strategies to prevent further damage and the spread of the incident.  Develop eradication procedures to completely remove the threat from the environment.

> ![Tradeoff icon](../_images/trade-off.svg) **Tradeoff**: There's a tradeoff between reliability targets and remediation times. During an incident it's likely that you won't be meeting other nonfunctional or functional requirements. For example, you might need to disable parts of your system while you investigate the incident, or you might even need to take the entire system offline until you determine the scope of the incident. Business decision makers need to make an explicit decision about acceptable targets during the incident. The playbook must clearly specify the accountable role for that decision.

### Learn from the incident

An incident always uncovers gaps or vulnerable points in design or implementation. It's an improvement opportunity driven by lessons learnt in technical aspects, product development processes including testing, automation, and the effectiveness of the incident response process. Maintain detailed incident records, including actions taken, timelines and findings. 

It's highly recommended that you conduct structured post incident reviews, such as root-cause analysis, retrospectives, and so on. The outcome of those reviews must be tracked and prioritized for execution. 

Improvement plans should include updates to security drills and testing. This should work with BCDR drills and security compromise should ideally be a scenario for doing a BCDR drill. Drills are a good way to validate how the documented processes work. There shouldn't be multiple playbooks. Instead use a single source that can be adjusted based on the size of the item, how widespread or localized the impact is. Always optimize keeping in mind that drills are based on hypothetical situations. The drills must be conducted in a low-risk environment and should include the learning phase. 

Conduct post-incident reviews (postmortems) to identify weaknesses in the response process and areas for improvement.  Update the Incident Response plan (IRP) and any security controls based on the lessons learned from the incident.

### Send necessary communication

Have a communication plan in place to acknowledge with the users if there's a disruption. Also, inform the internal stakeholders about the remediation and improvements. There might be changes to workload's security baseline to prevent future incidents. 

Generate incident reports for internal use and if necessary for regulatory compliance and/or legal purposes. Also, adopt a report standard format that is adopted for all Incidents by the SOC team (a document template with defined sections). Ensure every incident has a report associated with it before closing.

## Azure facilitation

To learn more about establishing a designated point of contact to receive Azure incident notifications from Microsoft Defender for Cloud, see [Configure email notifications for security alerts](/azure/security-center/security-center-provide-security-contact-details).

**Microsoft Sentinel** is a security information event management (SIEM) and security orchestration automated response (SOAR) solution. It's a single solution for alert detection, threat visibility, proactive hunting, and threat response. For more information, see [What is Microsoft Sentinel? | Microsoft Learn](/azure/sentinel/overview).

Ensure administrator contact information in the Azure enrollment portal includes contact information that will notify security operations directly or rapidly through an internal process. For more information, see [Update notification settings](/azure/cost-management-billing/manage/ea-portal-administration).

## Organizational alignment

Cloud Adoption Framework provides guidance of incident response planning and security operations.

[Security operations - Cloud Adoption Framework | Microsoft Learn](/azure/cloud-adoption-framework/secure/security-operations)

## Related links

[What is a security operations center (SOC)? | Microsoft Security](https://www.microsoft.com/en-us/security/business/security-101/what-is-a-security-operations-center-soc)

[Create incidents from alerts in Microsoft Sentinel | Microsoft Learn](/azure/sentinel/create-incidents-from-alerts)

[Navigate and investigate incidents in Microsoft Sentinel | Microsoft Learn](/azure/sentinel/investigate-incidents)

[Conduct end-to-end threat hunting with Hunts - Microsoft Sentinel | Microsoft Learn](/azure/sentinel/hunts)

[Introduction to automation in Microsoft Sentinel | Microsoft Learn](/azure/sentinel/automation)

[Introduction to Azure Incident Readiness - Training | Microsoft Learn](/training/technical-support/intro-to-azure-incident-readiness/)

[Microsoft Azure incident readiness | Microsoft Learn](/services-hub/unified/health/ir-azure)

[Microsoft cloud security benchmark - Incident Response | Microsoft Learn](/security/benchmark/azure/mcsb-incident-response)

[Incident response overview | Microsoft Learn](/security/operations/incident-response-overview)

## Security checklist

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"]
[Security checklist](checklist.md)