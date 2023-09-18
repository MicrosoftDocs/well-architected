---
title: Recommendations for responding to live performance issues
description: Review recommendations for responding to live-site performance issues.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for responding to live performance issues: PE 11

This guide describes the best practices for responding to live performance issues. Responding to live performance issues involves identifying and addressing any issues or bottlenecks that may be affecting the performance of a workload in real-time. It allows for immediate detection and resolution of performance problems, and it helps ensure the workload meets its performance requirements.

If live performance issues aren't addressed, it can lead to various negative consequences. The workload may experience slowdowns, crashes, or unresponsiveness, resulting in a poor user experience. It can also affect productivity. Users may be unable to perform their tasks efficiently, leading to reputation damage.

**Definitions**

|  Term|                                Definition|
|-|-|
 | Root cause analysis|                 A process to identify the underlying factors responsible for a problem.|
  |Data correlation |                   Aligning logs, metrics, and events from various parts of a workload to pinpoint underlying causes.|
  |Self-healing    |                    The automatic detection and resolution of issues within the workload itself without human intervention.|
  |Self-prevention|                     Implementations within a workload to prevent potential issues and failures.|

## Key strategies

When dealing with live performance issues, you need to be prepared with the right data and a plan to respond to the issue. This plan should include clear lines of communication and responsibilities. The goal is to use methods that allow you to quickly return to normal operations and learn from those events. You should be able to prevent the same issue from recurring or minimize the effects of performance issues when it happens again. Identify preventive measures to incorporate into your workflow.

### Prepare for issues

The ideal response to live-site performance issues is precise and fast. Precision and speed in performance remediation require preparation. To effectively respond to live performance issues, it's crucial to monitor key performance metrics, identify the root cause of the issues, and implement appropriate solutions or optimizations. It may involve analyzing workload logs, conducting performance testing, optimizing code or configurations, and scaling resources as needed. The following examples outline a few critical areas of preparation:

- *Have accurate architecture diagrams.* The architecture diagrams should indicate all components and how they interact. Visual representation can help identify bottlenecks and single points of failure that can lead to performance degradation or unavailability. Ideally, you catch and remove these issues before they cause issues, but having an up to date diagram can help you pinpoint issues in moments of higher stress.
- *Check data access.* Monitoring data and logs are critical for responding to performance issues in real time and conducting root causes analysis. However, it's important the data maintains integrity and confidentiality. Responding to live-site performance issues often requires access to underlying data that might not be normally accessible. You need to ensure personnel have access to the data needed when issues arise. You should enforce time-bound least privileges and limit access to authorized personnel.
- *Set automatic alerts.* Alerts can help you identify and address issues as soon as they occur. Alerts should generate notifications when performance deviates from performance baselines. Over time, you should tweak alert configurations to avoid generating too many or too few notifications. Monitoring solutions needs to collect enough data to generate alerts. These alerts should align with performance targets and established baselines. You want to avoid generating alerts on issues that are relevant to your goals. Examples of alerts include degradations in CPU usage, memory, response times, and database performance.

### Create a triage plan

Planning for issues helps create better and faster responses to them. A triage plan is a strategy for responding to live-site performance issues. Most performance issues don't merit disaster recovery protocols, but they can affect workload functionality enough to require preplanning. When creating a triage plan to respond to live performance issues, the plan should include the following components:

- *Identification and monitoring:* Implement a system to identify and monitor performance issues in real-time. You should have a list of contact information. The plan should identify roles and responsibilities. It needs to document what accounts gain access to protected information and for how long.
- *Escalation process:* Define a clear escalation process to ensure that performance issues are escalated to the appropriate teams or individuals in a timely manner. This process should include contact information and guidelines for escalating issues.
- *Root cause analysis:* Develop a process for conducting root cause analysis to identify the underlying causes of performance issues. It involves analyzing logs, performance metrics, and conducting diagnostic tests to pinpoint the source of the problem.
- *Prioritization:* Establish a prioritization framework to determine the severity of performance issues and prioritize them based on their effect on the workload and users.
- *Communication:* Create a communication plan to keep stakeholders informed about the status of performance issues and the progress of their resolution. Consider regular updates, status reports, and clear communication channels.
- *Documentation:* Document the triage plan, including all the steps, processes, and best practices. This documentation should be easily accessible to the team members involved in responding to performance issues.

### Identity and resolve the issue

Identifying and resolving live performance issues involves identifying and addressing any factors that may be causing performance degradation or inefficiencies in a live workload. Monitoring data is invaluable when investigating and resolving performance-related incidents. It provides a historical record of performance metrics and allows you to analyze the root cause and identify contributing factors. You should use all relevant monitoring data to understand and fix the performance issue.

**Use root cause analysis.** Root cause analysis requires hypothesis testing. After reviewing the monitoring data, you should list potential causes of the performance issue and test them. To conduct a root cause analysis on a live performance issue, you can follow these steps:

- *Gather information:* Collect as much information as possible about the performance issue. It can include error messages, logs, performance metrics, and any other relevant data.
- *Define the problem:* Clearly define the problem by identifying the symptoms and the effect it has on the workload or users.
- *Investigate potential causes:* Narrow down the scope of the analysis by identifying the specific component or area of the workload where the performance issue is occurring. Identify potential causes of the performance issue based on the gathered information. It can involve analyzing code, configuration settings, infrastructure, or external dependencies.
- *Correlate data:* Dive deeper into the data collected to identify patterns, anomalies, or correlations that may be contributing to the performance issue. Data correlation is key to identifying performance issues and causes. It can involve reviewing logs, analyzing performance metrics, and conducting tests.
- *Test hypotheses:* Formulate hypotheses based on the potential causes identified and conduct tests to validate or refute them. You should use a test environment to see if you can replicate the error.
- *Implement solutions:* Once you identify the root cause, develop and implement solutions to address the performance issue.
- *Monitor and validate:* Continuously monitor the workload after implementing the solutions to ensure that the performance issue is resolved. Validate the effectiveness of the solutions by monitoring performance metrics and user feedback.

:::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff:** Root cause analysis can be time consuming to identify, test and document. It also requires data collection and storage to correlate performance issues. The time and data could add significant work to the operations teams and cost to the workload.

:::image type="icon" source="../_images/risk.svg"::: **Risk:** Root cause analysis without the proper security guardrails provides access to logs and data that could expose sensitive information.

**Engage vendor support.** Vendor support can be an essential step when dealing with ongoing performance issues. Vendors have the expertise, tools, resources, and experience to help fix issues on their products. Your support agreement with the supplier determines the level of support a vendor provides. You need to have contact information available for your personnel. Vendors may need access to data as well to effectively engage. You need to have a plan in place for authenticating and authorizing external or guest accounts to access monitoring data. It's often best to work in parallel with vendors. You should create a plan to have some team members collaborate with vendor support while others continue to triage and fix the performance issues. Vendor support can also make suggestions on how to help prevent and automate the response to similar events.

### Learn from the findings

After you fix a live-site performance issue, you need to review what happened. The goal is to learn from performance issues, not just identify the problem. The best way to learn is through documentation. Document the issue and how you fixed it. If a vendor helped, you should work with the vendor to enhance your documentation, train your team, and modify your workload accordingly. The documentation should indicate how you fixed the problem and how you prevent it from happening again. For example, you can introduce automation that responds to common issues. Automation should add self-healing and self-prevention qualities to the workload. You could create refined alerts that help you respond earlier to indicators of performance issues.

## Azure facilitation

Azure provides several tools to help respond to live performance issues. Here are some of the tools. Azure Monitor is a comprehensive monitoring solution that provides insights into the performance and health of your applications and infrastructure. It offers features such as metrics, logs, alerts, and dashboards to help you monitor and diagnose performance issues.

Application Insights is an Application Performance Management (APM) service that helps developers and DevOps professionals monitor live applications. It automatically detects performance anomalies, collects application-level logs and events, and provides analytics tools to diagnose issues.

Azure Log Analytics is a service that collects and analyzes log data from various sources, including applications, virtual machines, and Azure resources. It allows you to query and analyze log data to gain insights into the performance and behavior of your applications.
