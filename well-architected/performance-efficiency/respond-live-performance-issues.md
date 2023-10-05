---
title: Recommendations for responding to live performance issues
description: Review recommendations for responding to live-site performance issues. See how to use root cause analysis, triage plans, and other steps to resolve problems. 
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for responding to live performance issues

**Applies to: PE 11**

This guide describes the best practices for responding to live performance issues. Response steps include identifying and addressing issues and bottlenecks that might affect the performance of a workload in real time. Addressing live issues allows for immediate detection and resolution of performance problems, and it helps ensure that the workload meets its performance requirements.

If you don't address live performance issues, various negative consequences can result. The workload can experience slowdowns, crashes, or unresponsiveness, resulting in a poor user experience. Productivity can also be affected. Users might be unable to perform their tasks efficiently, leading to reputation damage.

**Definitions**

| Term | Definition|
| --- | --- |
| Root cause analysis | A process for identifying the underlying factors that are responsible for a problem. |
| Data correlation | Aligning logs, metrics, and events from various parts of your workload to pinpoint underlying causes. |
| Self-healing | The ability to automatically repair issues without human intervention. |
| Self-prevention | Implementations within a workload to prevent potential issues and failures. |

## Key design strategies

When you experience a live performance issue, you need to be prepared with the right data and a plan to respond to the issue. This plan should include clear lines of communication and responsibilities. The goal is to use methods that allow you to quickly return to normal operations and learn from the event. You should try to prevent the same issue from recurring. Or if the issue comes up again, you should minimize its effect on performance issues. A key strategy is to identify preventive measures to incorporate into your workflow.

### Prepare for issues

The ideal response to live-site performance issues is precise and fast. Precision and speed in performance remediation require preparation. To effectively respond to live performance issues, it's crucial to monitor key performance metrics, identify the root cause of the issues, and implement appropriate solutions or optimizations. To take these steps, you might need to analyze workload logs, conduct performance testing, optimize code or configurations, and scale resources. The following examples outline a few critical areas of preparation:

- *Have accurate architecture diagrams.* Your architecture diagrams should include all components and show how they interact. Visual representation can help identify bottlenecks and single points of failure that can lead to performance degradation or unavailability. Ideally, you catch and remove these issues before they cause problems, but having an up-to-date diagram can help you pinpoint issues in high-stress moments.

- *Check data access.* Data and logs from monitoring processes are critical for responding to performance issues in real time and conducting root cause analyses. But it's important to maintain the integrity and confidentiality of the data. Responding to live-site performance issues often requires access to underlying data that might not be normally accessible. You need to ensure that personnel have access to the data that they need when issues arise. But you should only grant time-restricted, least-privilege access, and you should limit that access to authorized personnel.

- *Set automatic alerts.* Alerts can help you identify and address issues as soon as they occur. Alerts should generate notifications when workload performance deviates from performance baselines. Over time, you should tweak alert configurations to avoid generating too many or too few notifications. The monitoring solutions that you use need to collect enough data to generate alerts. These alerts should align with performance targets and established baselines. You should avoid generating alerts on issues that are relevant to your goals. Examples of alerts include degradations in CPU usage, memory, response times, and database performance.

### Create a triage plan

Planning for issues helps create better and faster responses to them. A triage plan is a strategy for responding to live-site performance issues. Most performance issues don't merit disaster recovery protocols, but they can affect workload functionality enough to require preplanning. Any triage plans that you create to respond to live performance issues should include the following components:

- *Identification and monitoring*. Implement a system to identify and monitor performance issues in real time. You should have a list of the contact information of people who are capable of making decisions or escalating issues to higher levels. The plan should also identify roles and responsibilities. It needs to document which accounts gain access to protected information and for how long.

- *Escalation process*. Define a clear escalation process to ensure that performance issues are escalated to the appropriate teams or individuals in a timely manner. The process definition should include contact information and guidelines for escalating issues.

- *Root cause analysis*. Develop a process for conducting a root cause analysis to identify the underlying cause of each performance issue. The process should involve analyzing logs and performance metrics and conducting diagnostic tests to pinpoint the source of each problem.

- *Prioritization*. Establish a prioritization framework to determine the severity of performance issues and prioritize them based on their effect on the workload and users.

- *Communication*. Create a communication plan to keep stakeholders informed about the status of performance issues and the progress of their resolution. Consider regular updates, status reports, and clear communication channels.

- *Documentation*. Document the triage plan, including all its steps, processes, and best practices. This documentation should be easily accessible to the team members who are involved in responding to performance issues.

### Identify and resolve issues

Resolving live performance issues involves identifying and addressing any factors that can cause performance degradation or inefficiencies in a live workload. Data that you collect during monitoring is invaluable when you investigate and resolve performance-related incidents. This data provides a historical record of performance metrics. When you have monitoring data available, you can analyze root causes and identify contributing factors. You should use all relevant monitoring data to understand and fix each performance issue.

**Use root cause analysis.** Root cause analysis requires hypothesis testing. After you review monitoring data, you should list potential causes of the performance issue and test them. To conduct a root cause analysis on a live performance issue, you can follow these steps:

1. *Gather information*. Collect as much information as possible about the performance issue. Examples include error messages, logs, performance metrics, and any other relevant data.

1. *Define the problem*. Clearly define the problem by identifying the symptoms and the effect that the problem has on the workload or users.

1. *Investigate potential causes*. Narrow down the scope of the analysis by identifying the specific component or area of the workload where the performance issue is occurring. Identify potential causes of the performance issue based on the gathered information. This process can involve analyzing code, configuration settings, infrastructure, or external dependencies.

1. *Correlate data*. Dive deeper into the collected data to identify patterns, anomalies, or correlations that might contribute to the performance issue. Data correlation is key to identifying performance issues and causes. It can involve reviewing logs, analyzing performance metrics, and conducting tests.

1. *Test hypotheses*. Formulate hypotheses based on the potential causes that you identify. Conduct tests to validate or refute your hypotheses. You should use a test environment to see whether you can replicate the error.

1. *Implement solutions*. Once you identify a root cause, develop and implement solutions to address the performance issue.

1. *Monitor and validate*. After you implement the solutions, continuously monitor the workload to ensure that the performance issue is resolved. Validate the effectiveness of the solutions by monitoring performance metrics and user feedback.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: The steps of a root cause analysis, such as identifying possible causes, testing hypotheses, and documenting the analysis, can be time consuming. To correlate performance issues, you also need to collect and store data. The required time and infrastructure can add significant work to the operations teams and cost to the workload.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: If you perform a root cause analysis without proper security guardrails, there's a risk that you expose sensitive information when you provide access to logs and data.

**Engage vendor support.** Vendor support can be an essential step when you deal with ongoing performance issues. Vendors have the expertise, tools, resources, and experience to help fix issues with their products. Your support agreement with your supplier determines the level of support a vendor provides.

It's often best to work in parallel with vendors. You should create a plan to have some team members collaborate with vendor support while others continue to triage and fix performance issues. Vendor support teams can also make suggestions on how to help prevent and automate responses to similar events.

You need to have contact information available for your personnel. Vendors might also need access to data to effectively engage in problem-solving. You need to have a plan in place for authenticating and authorizing external or guest accounts to access monitoring data.

### Learn from findings

After you fix a live-site performance issue, you need to review what happened. The goal is to learn from performance issues, not just identify problems. The best way to learn is through documentation. Document each issue and explain how to fix it. If a vendor helped, work with the vendor to enhance your documentation, train your team, and modify your workload accordingly.

The documentation should indicate how to prevent each problem from happening again. One way to avoid recurring problems is to introduce automation to respond to common issues. Automation should add self-healing and self-prevention qualities to a workload. Along with the automation, you can create refined alerts that help you respond early to performance issue indicators.

## Azure facilitation

Azure provides several tools to help you respond to live performance issues:

- Azure Monitor is a comprehensive monitoring solution that provides insights into the performance and health of your applications and infrastructure. Monitor offers features such as metrics, logs, alerts, and dashboards to help you monitor and diagnose performance issues.

- Application Insights is an application performance management (APM) service that helps developers and DevOps professionals monitor live applications. It automatically detects performance anomalies, collects application-level logs and events, and provides analytics tools to diagnose issues.

- Log Analytics is a service that collects and analyzes log data from various sources, including applications, virtual machines, and Azure resources. When you use Log Analytics, you can query and analyze log data to gain insights into the performance and behavior of your applications.

## Related links

- [Recommendations for self-healing and self-preservation](../reliability/self-preservation.md)

## Next steps

We recommend that you review the Performance Efficiency checklist to explore other concepts.

> [!div class="nextstepaction"]
> [Performance Efficiency checklist](checklist.md)
