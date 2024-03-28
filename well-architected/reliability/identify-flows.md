---
title: Recommendations for identifying and rating flows
description: Learn how to create a catalog of user and system flows for your workload to better understand the basis for your design decisions as they relate to reliability.
author: claytonsiemens77
ms.author: csiemens
ms.reviewer: ssumner
ms.date: 12/01/2023
ms.topic: conceptual
---

# Recommendations for identifying and rating flows

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

|[RE:02](checklist.md)| **Identify and rate user and system flows.** Use a criticality scale based on your business requirements to prioritize the flows. |
|---|---|

This guide describes the recommendations for identifying and prioritizing workload flows. Identifying and prioritizing workload flows involves mapping user flows and system flows to determine their criticality to the organization. This practice ensures you identify and prioritize the most critical workload functionality to reduce the risk of damaging failures. Failure to identify and prioritize workload flows can lead to system breakdowns and compromised workload reliability.

**Definitions**

| Term | Definition |
|---------|---------|
| User flow | The paths or sequences of actions that users take within an application or system. |
| System flow | The flow of information and processes within a system. The system automatically follows this flow to enable user flows or workload functionality. |

## Key design strategies

When you design your workload, it's essential to define the user flows and system flows. User flows chart the movement of a user through your application. They focus on the user interface, interactions, decisions, and the steps required to complete a task. User flows provide a user-centric perspective on user experience and interface design. System flows chart the internal workings of your workload. They focus on data movement, input processing, output processing, and interactions among workload components, backend services, and external APIs. System flows indicate the intricate details of how the workload operates internally.

You should identify and define flows early in the design phase of your workload. It gives you a clearer understanding of what affects the reliability of your workload. It aligns your architectural decisions closely with the reliability goals of your workload.

### Identify all user and system flows

The output of identifying all user and system flows is a catalog of all the flows in your workload. This identification process requires you to map out every user interaction and process within a system from beginning to end. This mapping is a prerequisite for identifying critical flows. Here are recommendations for identifying all user and system flows in a workload:

- *Interview stakeholders.* Stakeholders can provide valuable information to identify flows, and they can even help you map and prioritize flows. You can also interview users, business analysts, and technical teams to gather insights about user interactions and dependencies within the workload.

- *Review documentation.* In the design phase, you might not have documentation to review. However, if documentation exists, you should use it. Ask for system architecture diagrams, user manuals, and process descriptions. These documents can help you understand the intended functionality of the workload and its individual flows.

- *Observe the workload.* Monitor the workload in operation, noting how users interact with it and how different components speak with each other. You should analyze system logs, performance metrics, and user activity logs to identify patterns, frequent tasks, and system responses.

- *List identified flows.* The interviews, documentation, and observation should enable you to identify all the flows in the workload. Compile a list of all the flows you identify and categorize them into user flows (focusing on user interactions) and system flows (focusing on backend processes and data movement).

- *Define flow start and end points.* For each identified flow, clearly define where the flow starts and where it ends. For user flows, document each user interaction and its expected outcome. Focus on the user experience and interface design. For system flows, you need to identify its underlying triggers and expected outcomes.

- *Break down each flow.* Break down each flow into individual steps, describing the actions, decisions, or processes that occur at each point. Note how each step interacts with other parts of the system, including dependencies on other flows or external systems. You should be able to pinpoint how flows integrate with and affect the workload and user experience. This dual approach provides a holistic view of your entire workload.

- *Document unique outputs.* Identify any alternative paths or exceptions within each flow, such as error handling or conditional branching. If a flow has multiple possible outcomes, you should add it to the catalog as distinct entries. For user flows, you should identify the intended behavior of the interaction. For system flows, you should identify the intended behavior of the process.

- *Visualize with diagrams.* Create flowcharts or diagrams to visually represent the flow and its steps. You can use tools like Microsoft Visio, UML sequence diagrams, use-case diagrams, simple drawing tools, or a descriptive list in text format (*see [Example flow catalog](../cross-cutting-guides/optimize-workload-using-flows.md#flow-examples)*).

- *Update flow mapping iteratively.* Flow mapping is an iterative process. Flows can change, split, or combine, especially in the design phase. As the workload flows become more clearly defined, you should update the catalog of flows to match. Validate and refine your flow diagrams with feedback from stakeholders to ensure accuracy and completeness.

### Identify business processes for each flow

Business processes are a series of tasks to achieve an output, such as order fulfillment, customer service management, or inventory control. The identification of business processes for each flow involves mapping flows to one or more business processes. This mapping helps you understand the importance of each flow to the business.

You might have existing documentation or business plans that provide a mapping of flows to business processes. Sometimes user manuals, training materials, or system specifications can provide insights into the intended use and purpose of the workload and its flows. If not, you need to map flows to the business processes they support. Here are recommendations to identify business processes for each flow:

- *Use workload outputs.* You can use the workload outputs and flow breakdown to correlate flows with the business processes they support. First, review the outputs the workload generates. The output could be sales reports, data files, or completed tasks.

- *Conduct interviews.* Speak with team members and stakeholders who interact with the workload. You should ask specific questions about their daily tasks, how they use the workload, and what objectives they achieve with it. Technical teams often have a deeper understanding the workload structure and can provide insights into the business processes it supports.

- *Monitor workload usage.* For existing workloads, monitor the workload and look for patterns in usage that indicate underlying business processes, such as data entry, order processing, or customer interaction.

- *Connect the output to a business process.* Connect the dots from the flow outputs to the overall business process they support. For example, if a flow step involves processing customer orders, then it directly supports the business process of order fulfillment. Order fulfillment contributes to the business objective of maintaining customer satisfaction and generating revenue. Finally, use the flow breakdown to help determine which flow created the sales report.

### Identify process owners and stakeholders for each flow

The process owner for a flow is the individual that's responsible for the successful execution of a given process. They're responsible for that process and the flows that support it. You should identify the process owner for each workload flow. You should also identify the stakeholders for each flow. Stakeholders can be involved in the workload, have dependencies on a flow, or manage a dependency that the flow has.

You might have a responsibility assignment matrix (RAM) or RACI matrix that already identifies process owners and stakeholders. Typically, process owners are responsible or accountable for a process, and you consult or inform stakeholders.

### Identify escalation paths for each flow

The identification of escalation paths is about determining channels for escalating issues related to a flow. Issues that need escalation could be urgent updates, security concerns, degradations, or technical incidents. The goal of identifying an escalation path is to ensure timely and effective resolution of issues.

The escalation path you map out should start with the person or group most likely to resolve a particular issue. If this person or group can't resolve the issue, the escalation path should identify the next point of contact. The next point of contact has broader responsibilities and is able to coordinate mitigation strategies with more parts of the organization. The number of people on an escalation path varies by flow and organization. Too many people on an escalation path can slow the resolution efforts.

### Identify business impact of each flow

The identification of the business impact of each flow is essential for understanding how each flow contributes to key business objectives. Business impact could include revenue generation, customer satisfaction, or operational efficiency. By understanding both the positive and negative impact of each flow, you can prioritize efforts to ensure the reliability of the flows that matter the most to your business. It's important to consider the direct impact of flow failure and its indirect effect on other interconnected processes. Here are steps to identify the business impact of each flow:

- *Identify positive impact.* Determine the expected benefits when a flow runs as intended. The expected benefits could include improved efficiency, increased revenue, enhanced customer satisfaction, or any other positive effect on the business.

- *Identify negative impact.* Assess the potential negative impacts if a process fails or doesn't work as expected. Consider quantifying specific losses, such as revenue drops. Include subjective effects like damage to reputation, erosion of customer trust, or adverse effects on other related business processes.

- *Define capacity and availability assumptions.* Establish assumptions about the expected capacity and availability of each process. Consider factors like throughput per unit of time, expected business hours, and target percentage uptime. If there are expectations for recovery time objective (RTO) or recovery point objective (RPO), you should include these expectations. These assumptions help in understanding reliability requirements of each flow.

By systematically evaluating these aspects, you can gain a comprehensive view of how each flow impacts the business and make strategic decisions about reliability optimization.

### Assign a criticality rating to each flow

A detailed evaluation of flow importance relative to the overall business impacts allows you to assign a criticality rating to each flow. You can use quantitative or qualitative criticality ratings. The purpose is to sort the flows by priority and assign a label that allows you to identify the critical flows. This process is a logical continuation of identifying, mapping, and aligning with business processes and impact. Use the following criticality descriptions to assign your critical ratings:

- *High criticality*: High criticality flows are integral to core business functions. They directly affect critical aspects of a business such as customer experience, financial transactions, security protocols, human health, and safety. The failure or disruption of these flows could lead to significant immediate or long-term negative effects. Examples of negatives effects include loss of revenue, breach of trust, and legal issues. Prioritizing these flows ensures that the most crucial aspects of the workload are robust and resilient.

- *Medium criticality*: Medium criticality flows are important for the complete functionality of the system but don't directly interface with the customer or critical business operations. For example, if an issue disrupts an internal data processing flow, you can retry the data processing without immediate external effects. These flows are essential for smooth operations but offer a buffer in terms of immediate customer or financial effect, allowing for managed responses to issues.

- *Low criticality*: Low criticality flows don't have a direct or significant effect on the core business functions or customer experience. Examples include ancillary processes like nightly log transfers or optional user features such as feedback surveys. While these flows contribute to the overall system, their disruption is unlikely to cause significant immediate business or operational issues.

By following this structured approach to assigning criticality, you can effectively prioritize resources and focus on maintaining and enhancing the reliability and effectiveness of your most critical flows.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Higher expectations for reliability sometimes coincide with higher setup costs, operational costs, and management burden for operators. Ensure that stakeholders understand the potential cost increases of improving the reliability of critical flows.

## Organizational alignment

Cloud Adoption Framework provides guidance for workloads that require business criticality classification.

For more information, see [business criticality in cloud management](/azure/cloud-adoption-framework/manage/considerations/criticality).
