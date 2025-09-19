---
title: Reliability design principles
description: Understand the design principles of the reliability pillar.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/15/2023
ms.topic: conceptual
ms.update-cycle: 1095-days  
---

# Reliability design principles 

Outages and malfunctions are serious concerns for all workloads. A reliable workload must survive those events and **continue to consistently provide its intended functionality**. It must be **resilient** so that it can detect, withstand, and recover from failures within an acceptable time period. It must also be **available** so that users can access the workload during the promised time period at the promised quality level.  

It's not realistic to assume failures won't occur, especially when the workload is built to run on distributed systems. Some components might fail while others continue to operate. At some point, the user experience might be affected, which compromises business goals.  

Workload architectures should have **reliability assurances in application code, infrastructure, and operations**. Design choices shouldn't change the intent that's specified by business requirements. Such changes should be considered significant tradeoffs.

The **design principles** are intended to provide guidance for aspects of reliability that you should consider throughout the development lifecycle. Start with the recommended approaches and **justify the benefits for a set of requirements**. After you set your strategy, drive actions by using the [**Reliability checklist**](./checklist.md).

If you don't apply these principles to your design, the workload most likely won't be prepared to **anticipate or handle problems in production**. The outcome might be service disruptions that lead to financial loss. In the case of critical workloads, failing to apply these principles could jeopardize safety.

## Design for business requirements	 

|![Goal icon](../_images/goal.svg) Get clarity on the workload's scope, user growth, and most importantly promises the team has made to external customers and internal stakeholders.|
|--| 

Design isn't guesswork. This principle is about a deliberate process that brings alignment on user experience, design constraints, and on what success looks like and how it's measured. 

Set clear, achievable, and documented goals, negotiated with business stakeholders and grounded in a realistic investment and forecasting. These requirements will directly inform your architectural choices, from resiliency strategies to observability tooling and scaling plans.

|Approach|Benefit| 
|-|-|
|Focus on gathering information needed to define the **scope and depth of the solution**. Clarify constraints that influence business goals. Start with high-level questions, like: <br><br>- What level of resiliency, recovery, observability, and simplicity is required?<br>- Are there defined constraints related to cost, compliance, geography, or latency? <br><br> Based on that information, document what's good enough and simple to achieve. |Without understanding clear goals and boundaries, architecture becomes guesswork. Let business requirements set the frame so the solution can be intentionally resilient, scalable, observable, and appropriately simple.|
|Guide decision-making by translating business goals into **shared understanding of architectural trade-offs within real constraints**. For each design request, clearly present options that impact: <br><br>- Financial cost<br> - Engineering complexity<br>- Security considerations<br>- Operational overhead|This will help stakeholders understand the cost, complexity, and operational implications of their asks, and guiding them toward realistic, aligned outcomes.|
|**Prioritize uptime for each critical user flow**, over generic uptime. <br><br>Identify the user-facing capabilities and flow through the system, and for each one, assess its business value, usage patterns, and resilience requirements. Drive consensus at the flow level to ensure design decisions stay aligned business goals.|This conversation helps shift stakeholders away from demands, like '_the site must be up 100% of the time_' to practical, achievable expectations tied to real functionality.|
|**Anchor design choices around time horizons**. <br><br>Define the usage expectations with realistic forecasting. For example, what's the expected user load at launch? Is user growth expected to be linear, exponential, or uncertain.|The architecture will be able to adapt as the business grows. This approach impacts on how to think about elasticity, event-driven workflows, and and avoids unnecessary complexity or redesign at every stage.|
|**Factor in dependencies** that might limit the autonomy of the design, like organizational constraints. <br><br>Be aware of centralized infrastructure, security mandates, network routing policies, or platform decisions that directly impact what you can promise in terms of resiliency, availability, and recovery. |Understanding your dependency on shared services helps you design with realistic expectations for reliability. It ensures your RTO/RPO targets and SLAs are achievable and clearly communicated, avoiding overpromises and reducing surprises.|


## Design for resilience 

|![Goal icon](../_images/goal.svg) The workload must continue to operate with full or reduced functionality.|
|-|

You should expect that component malfunctions, platform outages, performance degradations, limited resource availability, and other faults will occur. Build resiliency in the system so that it's *fault-tolerant and can degrade gracefully*. 

|Approach|Benefit| 
|-|-|
|**Distinguish components that are on the critical path** from those that can function in a degraded state.|Not all components of the workload need to be equally reliable. Determining criticality helps you design according to the **criticality of each component**. You **won't overengineer resiliency for components** that could slightly deteriorate the user experience, as opposed to components that can cause end-to-end problems if they fail. <br><br>The design can be efficient in allocating resources to critical components. You can also implement fault isolation strategies so that if a noncritical component fails or enters a degraded state, it can be isolated to prevent cascading failures.   |
|**Identify potential failure points in the system**, especially for the critical components, and determine the effect on user flows.|You can **analyze the failure cases, blast radius, and intensity of fault**: full or partial outage. This analysis influences the design of error handling capabilities at the component level.   |
|**Build self-preservation capabilities** by using design patterns correctly and modularizing the design to isolate faults.|The system will be able to **prevent a problem from affecting downstream components**. The system will be able to mitigate transient and permanent failures, performance bottlenecks, and other problems that might affect reliability.<br><br>You'll also be able to **minimize the blast radius**.| 
|**Add the capability to scale out the critical components** (application and infrastructure) by considering the capacity constraints of services in the supported regions. |The workload will be able to **handle variable capacity spikes and fluctuations**. This capability is crucial when there's an unexpected load on the system, like a surge in valid usage. If the workload is designed to scale out over multiple regions it can even overcome potential temporary resource capacity constraints or other issues impacting in a single region.|
|**Build redundancy in layers and resiliency on various application tiers.**<br><br>Aim for redundancy in physical utilities and immediate data replication. Also aim for redundancy in the functional layer that covers services, operations, and personnel.|Redundancy helps **minimize single points of failure**. For example, if there's a component, zonal, or regional outage, redundant deployment (in active-active or active-passive) allows you to meet uptime targets. <br><br>Adding intermediaries prevents direct dependency between components and improves buffering. Both of these benefits harden the resiliency of the system.|
|**Overprovision to immediately mitigate individual failure** of redundant instances and to buffer against runaway resource consumption.|Higher investment in overprovisioning **increases resiliency**.<br><br>The system will continue to operate at full utility during an active failure even before scaling operations can start to **remediate the failure**. Likewise, you can reduce the risk of unexpected runaway resource consumption claiming your planned buffer, gaining critical triage time, before system faults or aggressive scaling occurs.|

## Design for recovery 

|![Goal icon](../_images/goal.svg) The workload must be able to anticipate and recover from most failures, of all magnitudes, with minimal disruption to the user experience and business objectives.  |
|-|

Even highly resilient systems need *disaster preparedness approaches*, in both architecture design and workload operations. On the data layer, you should have strategies that can repair workload state in case of corruption. 

|Approach|Benefit| 
|-|-|
|**Have structured, tested, and documented recovery plans** that are aligned with the negotiated recovery targets. Plans must cover all components in addition to the system as a whole.|A well-defined process leads to a **quick recovery** that can prevent negative impact on the finances and reputation of your business. Conducting regular recovery drills tests the process of recovering system components, data, and failover and failback steps to **avoid confusion when time and data integrity are key** measures of success.|  
|Ensure that you can **repair data** of all stateful components within your recovery targets.|Backups are essential to getting the **system back to a working state** by using a trusted recovery point, like the last-known good state.  <br><br>Immutable and transactionally consistent backups ensure that data can't be altered, and that the restored data isn't corrupted.| 
|Implement **automated self-healing capabilities** in the design. |This automation **reduces risks from external factors**, like human intervention, and shortens the break-fix cycle. |
|Replace stateless components with **immutable ephemeral units**. |Building ephemeral units that you can spin up and destroy on demand provides **repeatability and consistency**. Use side-by-side deployment models to make the transition to the new units incremental, minimizing disruptions.|   

## Design for operations 

|![Goal icon](../_images/goal.svg) Shift left in operations to anticipate failure conditions.  |
|-|

*Test failures early and often* in the development lifecycle, and determine the impact of performance on reliability. For the sake of root cause analysis and postmortems, you need to have shared visibility, across teams, of dependency status and ongoing failures. *Insights, diagnostics, and alerts from observable systems are fundamental* to effective incident management and continuous improvement.  

|Approach|Benefit|
|-|-|
|**Build observable systems** that can correlate telemetry.|Monitoring and diagnostics are crucial operations. If something fails, you need to know that it failed, **when it failed**, and **why it failed**. Observability at the component level is fundamental, but aggregated observability of components and correlated user flows provides a holistic view of health status. This data is required to enable site-reliability engineers to prioritize their efforts for remediation.| 
|**Predict potential malfunctions and anomalous behavior.** Make active reliability failures visible by using prioritized and actionable alerts. <br><br>Invest in reliable processes and infrastructure that leads to quicker triage.| Site reliability engineers can be notified immediately so that they can **mitigate ongoing live site incidents** and proactively mitigate potential failures identified by predictive alerts before they become live incidents. |
|**Simulate failures** and run tests in production and pre-production environments.|It's beneficial to experience failures in production so you can set **realistic expectations for recovery**. This allows you to make design choices that gracefully respond to failures. Also, it enables you to test the thresholds you set for business metrics. |
|Build components with **automation in mind**, and automate as much as you can.| Automation **minimizes the potential for human error**, bringing **consistency** to testing, deployment, and operations.| 
|Factor in **routine operations and their impact** on the stability of the system. |The workload might be subject to ongoing operations, like application revisions, security and compliance audits, component upgrades, and backup processes. Scrutinizing those changes ensures the **stability of the system**. |
|Continuously **learn from incidents in production**.|Based on the incidents, you can determine the impact and oversights in design and operations that might go unnoticed in preproduction. Ultimately, you'll be able to **drive improvements** based on real-life incidents.  |

## Keep it simple 

|![Goal icon](../_images/goal.svg) Avoid overengineering the architecture design, application code, and operations.  |
|-|

It's often what you remove rather than what you add that leads to the most reliable solutions. *Simplicity reduces the surface area for control*, minimizing inefficiencies and potential misconfigurations or unexpected interactions. On the other hand, oversimplification can introduce single points of failure. Maintain a balanced approach.

|Approach|Benefit| 
|-|-|
|Add components to your architecture only if they help you achieve target business values. **Keep the critical path lean**. |Designing for business requirements can lead to a straightforward solution that's **easy to implement and manage**. Avoid having too many critical components, because each one is a significant point of failure. |
|**Establish standards** in code implementation, deployment, and processes, and document them. Identify opportunities to enforce those standards by using automated validations.|Standards provide **consistency and minimize human errors**. Approaches like standard naming conventions and code style guides can help you maintain quality and make assets easy to identify during troubleshooting. |
|Evaluate whether theoretical approaches translate to **pragmatic design** that applies to your use cases.|Application code that's too granular can lead to unnecessary interdependence, extra operations, and **difficult maintenance**. |
|**Develop just enough code**.|You'll be able to prevent problems that are the result of **inefficient implementations**, like unexpected resource consumption, user or dataflow failures, and code bugs.<br><br>Conversely, reliability problems should lead to code reviews to ensure that code is resilient enough to handle the problems.| 
|**Take advantage of platform-provided features** and prebuilt assets that can help you effectively meet business targets.|This approach **minimizes development time**. It also enables you to **rely on tried and tested practices** that have been used with similar workloads. |

## Next steps

> [!div class="nextstepaction"]
> [Reliability checklist](checklist.md)
