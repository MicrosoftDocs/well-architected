---
title: Recommendations for responding to live-site performance issues
description: Review recommendations for responding to live-site performance issues.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for responding to live-site performance issues: PE 11

This guide describes the best practices for responding to live-site performance issues. An effective response to live-site performance is a multi-step process that starts with preparation. Triage should quickly identify the issue, fix the problem, address the root cause, and work to improve the workload and its operations. Learnings should help you recover from performance problems faster and uncovers methods to prevent future performance issues from occurring. Failure to adequately respond to ongoing performance issues prolongs performance degradation for users longer than needed.

## Definitions

|  Term|                                Definition|
|-|-|
 | Root cause analysis|                 A process to identify the underlying factors responsible for a problem.|
  |Data correlation |                   Aligning logs, metrics, and events from various parts of a workload to pinpoint underlying causes.|
  |Self-healing    |                    The automatic detection and resolution of issues within the workload itself without human intervention.|
  |Self-prevention|                     Implementations within a workload to prevent potential issues and failures.|

## Your responsibility

**Prepare for issues.** The ideal response to live-site performance issues is precise and fast. Precision and speed in performance remediation require preparation. The following examples outline a few critical areas of preparation:

-   **Have accurate architecture diagrams.** The architecture diagrams should indicate all components and how they interact. Visual representation can help identify bottlenecks and single points of failure that can lead to performance degradation or unavailability. Ideally, you catch and remove these issues before they cause issues, but having an up to date diagram can help you pinpoint issues in moments of higher stress.

-   **Ensure access to data.** Monitoring data and logs are critical for responding to performance issues in real time and conducting root causes analysis. However, it's important the data maintains integrity and confidentiality. Responding to live-site performance issues often requires access to underlying data that might not be normally accessible. You need to ensure personnel have access to the data needed when issues arise. You should enforce time-bound least privileges and limit access to authorized personnel.

-   **Create a triage plan.** A triage plan is a strategy for responding to live-site performance issues. Most performance issues don't merit disaster recovery protocols, but they can affect workload functionality enough to require preplanning. You should have a list of contact information. The plan should identify roles and responsibilities. It needs to document what accounts gain access to protected information and for how long. Planning for issues helps create better and faster responses to them.

**Set alerts.** Setting up automatic alerts is a crucial first step in responding to live-site performance issues. Alerts can help you identify and address issues as soon as they occur. Alerts should generate notifications when performance deviates from performance baselines. Over time, you should tweak alert configurations to avoid generating too many or too few notifications. Monitoring solutions needs to collect enough data to generate alerts. These alerts should align with performance targets and established baselines. You want to avoid generating alerts on issues that are relevant to your goals. Examples of alerts include degradations in CPU usage, memory, response times, and database performance.

**Engage vendor support.** Vendor support can be an essential step when dealing with ongoing performance issues. Vendors have the expertise, tools, resources, and experience to help fix issues on their products. Your support agreement with the supplier determines the level of support a vendor provides. You need to have contact information available for your personnel. Vendors may need access to data as well to effectively engage. You need to have a plan in place for authenticating and authorizing external or guest accounts to access monitoring data. It's often best to work in parallel with vendors. You should create a plan to have some team members collaborate with vendor support while others continue to triage and fix the performance issues. Vendor support can also make suggestions on how to help prevent and automate the response to similar events.

**Identity the root cause.** Monitoring data is invaluable when investigating and resolving performance-related incidents. Monitoring data provides a historical record of performance metrics. It allows you to analyze the root cause and identify contributing factors. You should use all relevant monitoring data to understand and fix the performance issue.

Root cause analysis requires hypothesis testing. After reviewing the monitoring data, you should list potential causes of the performance issue and test them. You should use a test environment to see if you can replicate the error. A strategy to conduct root causes analysis is called 5xWhy. In this strategy, you ask "why" five times to drill into issues and identify the fundamental cause. Data correlation is the key to identifying performance issues and causes. Correlating monitoring data helps conduct an effective root cause analysis of performance issues. A unified monitoring solution is helpful.

**Learn from the findings.** After you fix a live-site performance issue, you need to review what happened. The goal is to learn from performance issues, not just identify the problem. The best way to learn is to document the issue and how you fixed it. If a vendor helped, you should work with the vendor to enhance your documentation, train your team, and modify your workload accordingly. You want the documentation to indicate how you fixed the problem and how you'll prevent it from happening again.

**Learn from the analysis.** You should review the findings and extract ways to improve response times and prevent similar issues. For example, you can introduce automation that responds to common issues. Automation should add self-healing and self-prevention qualities to the workload. You could create refined alerts that help you respond earlier to indicators of performance issues.

## Azure facilitation

-   Azure Monitor is a unified monitoring platform that you should use to capture and analyze performance data.

## Tradeoff

-   Root cause analysis can be time consuming to identify, test and document. It also requires data collection and storage to correlate performance issues. The time and data could add significant work to the operations teams and cost to the workload.

-   Root cause analysis requires access to logs and data that could expose sensitive information.