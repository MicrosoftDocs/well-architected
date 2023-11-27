---
title: Recommendations for identifying and rating flows
description: Learn how to create a catalog of user and system flows for your workload to better understand the basis for your design decisions as they relate to reliability.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for identifying and rating flows

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

|[RE:02](checklist.md)| **Identify and prioritize user and system flows.** Use a criticality scale that's based on your business requirements to prioritize the flows. |
|---|---|

This guide describes the recommendations for creating a catalog of user and system flows for your workload. Understand and model your flows to ensure that your reliability efforts benefit the most critical parts of your workload. Deprioritize the flows that don't have stringent availability requirements.

Create this catalog as you start to design your workload architecture so you can better understand the basis for your design decisions as they relate to reliability.

This guide serves as a prerequisite for the other reliability guides in this series. The following sections provide recommendations for workload design decisions and operational processes and procedures that support the workload, like monitoring and alerting, testing, business continuity, and disaster recovery functions.

**Definitions**

| Term | Definition |
|---------|---------|
| User flow | The paths or sequences of actions that users take within an application or system. |
| System flow | The flow of information and processes within a system. The system automatically follows this flow to enable user flows or workload functionality. |

## Key design strategies

As you design your workload, you need to define and refine the user and system flows for your workload. User flows map the the user's journey through the application or system. They highlighting the user interface, interactions, decisions, and steps a user takes to accomplish a task. The perspective is user-centric, emphasizing user experience and interface design. System flows focus on internal processes, data movement, input and output processing, and interactions between various system components, backend services, and external APIs. The perspective is more technical than user.

Assign a measure of criticality to those flows. Perform this exercise with business stakeholders to ensure that you capture the business requirements in the context of flows.
<!--Existing content. Decide where to put.

- Business escalation path for problems, like security concerns, urgent updates, and failovers.
- Technical escalation path for incidents that affect the health of the flow.-->

### Identify all user and system flows

Identifying all user and system flows is about creating a comprehensive catalog of flows in the workload. It requires you to map out every user interaction and process within a system. To ensure a comprehensive understanding, you need to map both user and system flows end to end. This mapping is a prerequisite for identifying critical flows. Here are recommendations for identifying all user and system flows:

- *Interview stakeholders.* Stakeholders can provide valuable information to identify flows and even begin the process of mapping and prioritizing flows. Interview users, business analysts, and technical teams to gather insights about various operations and user interactions within the workload.

- *Review documentation.* In the design phase, you might not have documentation to review. However, if documentation exists, you should use it. Ask for system architecture diagrams, user manuals, and process descriptions to understand the intended functionality of the workload.

- *Observe the workload.* Monitor the system in operation, noting how users interact with it and how different components interact with each other. You should analyze system logs, performance metrics, and user activity logs to identify patterns, frequent tasks, and system responses.

- *List identified flows.* The outcome of identifying all flows should be a list of user flows and system flows that highlight the expected behavior of the system. Compile a list of all the flows you've identified, categorizing them into user flows (focusing on user interactions) and system flows (focusing on backend processes and data movement).

- *Define start and end points.* For each identified flow, clearly define where the flow starts and where it ends. For user flows, document each user interaction and its expected outcome and focus on the user experience and interface design. For system flows you need to identify its underlying triggers and expected outcomes.

- *Break down each flow.* Break down the flow into individual steps or stages, describing the actions, decisions, or processes that occur at each point.

- *Identify interactions and dependencies.* Note how each step interacts with other parts of the system, including dependencies on other flows or external systems. You should be able to pinpoint how flows integrate with and affect the workload and user experience. This dual approach provides a holistic view of your entire workload.

- *Visualize with diagrams.* Create flowcharts or diagrams to visually represent the sequence of steps. Tools like Microsoft Visio, Lucidchart, or even simple drawing tools can be used for this purpose. You can use UML Sequence Diagrams or Use Case Diagrams or less formal descriptive list in text format like in the examples below.

- *Update flow mapping iteratively.* Validate and refine your flow diagrams with feedback from stakeholders to ensure accuracy and completeness. Flow mapping is an iterative process. Flows might not be fully defined, or you might decide to combine flows based on business requirements. You might not be able to fully define all flows while the workload is in the design phase. You might choose to split and or combine related flows as you determine the interaction with business processes and business consequences. As the workload flows become more clearly defined, you should update the catalog of flows to match.  

- *Document exceptions and variations.* Identify and map out any alternative paths or exceptions within each flow, such as error handling or conditional branching. You do not need to identify all possible outcomes of each flow. For user flows, you should identify the intended end-to-end behavior of the interaction. For system flows, you should identify the intended end-to-end behavior of the process. If a flow has multiple possible outcomes, you should add it to the catalog as distinct entries.

### Identify business processes for each flow

Business processes are a series of tasks to achieve an output, such as order fulfillment, customer service management, or inventory control. Identifying business processes for each flow involves mapping flows to one or more business processes. This mapping helps you understand the importance of each flow to the business.

Existing documentation or business plans should provide a mapping of flows to business processes. T might be related documents like user manuals, training materials, or system specifications that can provide insights into the intended use and purpose of the workload. If not, you need to do that mapping. Here are recommendations to identify business processes for each flow:

- *Conduct interviews.* Speak with team members and stakeholders who interact with the workload. You should ask specific questions about their daily tasks, how they use the workload, and what objectives they achieve with it. Technical staff often have a deeper understanding of how the workload structure and can provide insights into the business processes it supports.

- *Monitor workload usage.* Monitor how the workload and look for patterns in usage that indicate underlying business processes, such as data entry, order processing, or customer interaction.

- *Analyze workload outputs.* Review the outputs or results generated by the workload. This could include reports, data files, or completed tasks. Determine what business objectives these outputs are contributing to.

### Identify process owners and stakeholders

- The process owner for a flow is the individual that's responsible for the successful exection of a given process, and often maps directly to the porcess owner for the related business process. They will also be responsible for making critical decisions.
- Stakeholders should also be identified. Stakeholders may be others in the organization that have dependencies on, or that manage dependencies for a particular flow.
- Some organizations will already be tracking these dependencies in a responsibility assignment matrix (RAM) or RACI matrix. Process owners are typically assigned as Responsible or Accountable for a process, and stakeholders are those who are Consulted or Informed on the process.

#### Identify escalation paths

  - For each flow, identify the business escalation path for urgent updates, security concerns, and notification of degredation or failure.
  - for each flow, identify the technical escalation path for incidents affecting the health of the flow and necessary remediation.

### Identify business consequences

- Business consequesnces are the positive or negative outcomes that are possible when a process works properly, or when it fails.
- Identify the expeted benefits of the process running as expected.
- Define assumptions about what capacity might be expected for the process in terms of throughput per unit of time, or availability (business hours, percent uptime, etc).
- Identify the expected negative impact if the process does not work as expected. This can be quantified specifically in terms of lost revenue, or more subjectively in terms of reputation, customer trust, or the failure of related processes.

### Assign criticality

- Criticality and priority.

  Assign criticality to your flows to help you design your architecture. Identify the components that need higher levels of resiliency and security based on the business consequences identified above. To get started with criticality, use a *high*, *medium*, and *low* rating system that's based on the potential consequences when a given flow is unavailable. These criticality ratings are defined as:

- **High**: A flow that affects the customer experience, finances, security, or human health or safety.

- **Medium**: A flow that's necessary for the workload to fully function but doesn't directly affect the customer. For example, a flow might have a medium criticality rating if you can retry the flow after you resolve the problem without disrupting the customer experience.

- **Low**: A flow that's not directly tied to the functionality of the workload. For example, a nightly log transfer to a monitoring system or a pop-up survey feature might have a low criticality rating.

## Organizational alignment

Cloud Adoption Framework provides guidance for workloads that require a business criticality classification.

For more information, see [business criticality in cloud management](/azure/cloud-adoption-framework/manage/considerations/criticality).

## Tradeoffs

Ensure that stakeholders understand that higher expectations for reliability sometimes coincide with higher setup costs, operational costs, and management burden for operators.

## Example

The following example provides a complete scenario and illustrates important points to help you define your flows.

This scenario uses the [reliable web app pattern reference architecture](/azure/architecture/web-apps/guides/reliable-web-app/dotnet/plan-implementation) as the basis for the example flows.

:::image type="content" source="media/identify-flows/relecloud-flow.png" alt-text="Diagram that shows an example flow that's based on Relecloud." border="false" lightbox="media/identify-flows/relecloud-flow.png":::

### Workload

This example workload is line-of-business app called Relecloud that provides a concert ticket purchasing service. The web app is hosted in Azure using PaaS services like Azure App Service, Azure SQL database and Azure Cache for Redis.

#### User flows

The following user flows comprise the example architecture.

##### User flow 1: Create upcoming concerts

Call center employees use the application to create an upcoming concert.

- **Business processes that this flow supports**: This flow supports the *purchasing ticket* process, but in an asynchronous way that makes it less critical.

- **Process owner**: The sales department.

- **Business escalation path**: Stakeholders.

- **Technical escalation path**: Platform or application teams.

- **Business expectations**: This flow doesn't require high availability because concerts are proactively created on a weekly basis.

- **Business consequences**: If this flow isn't available when a call center employee needs to create a concert, it affects revenue and reputation.

- **Criticality**: Medium.

##### User flow 2: Search concerts

Call center employees use the application to search for upcoming concerts.

- **Business processes that this flow supports**: This flow supports the *purchasing ticket* process, but call center employees can opt to list concerts if the search function isn't available.

- **Process owner**: The UX (user experience) department.

- **Business escalation path**: Stakeholders.

- **Technical escalation path**: Platform or application teams.

- **Business expectations**: This flow doesn't require high availability because call center employees can also list concerts.

- **Business consequences**: If this flow isn't available when a call center employee searches for a concert, it affects the application's reputation. The call center employee's experience might degrade and it might affect productivity because the employee spends more time on a call.

- **Criticality**: Low.

##### User flow 3: Get a list of the concerts

Call center employees use the application to get a list of concerts.

- **Business processes that this flow supports**: This flow directly supports the *purchasing ticket* process.

- **Process owner**: The sales department.

- **Business escalation path**: Stakeholders.

- **Technical escalation path**: Platform or application teams.

- **Business expectations**: This flow requires high availability because call center employees can't purchase tickets if this flow isn't working properly.

- **Business consequences**: If this flow isn't available, it directly affects revenue and reputation.

- **Criticality**: High.

##### User flow 4: Purchase ticket

Call center employees use the application (the *authentication and authorization* process) to buy tickets for an upcoming concert (the *list upcoming concerts* process) on behalf of Relecloud customers.

- **Business processes that this flow supports**: This flow is the core feature and flow of the application.

- **Process owner**: The sales department.

- **Business escalation path**: Stakeholders.

- **Technical escalation path**: Platform or application teams.

- **Business expectations**: This flow requires high availability because customers can't purchase tickets if this flow isn't working properly.

- **Business consequences**: If this flow isn't available, it directly affects revenue and reputation.

- **Criticality**: High.

##### User flow 5: Authentication and authorization

Call center employees securely sign in to the application and are provided with proper roles so that they can purchase tickets on behalf of Relecloud customers.

- **Business processes that this flow supports**: This flow directly supports the *purchasing ticket* process. Without this functionality, call center employees can't sign in to the application to buy tickets.

- **Process owner**: The sales department.

- **Business escalation path**: Stakeholders.

- **Technical escalation path**: Platform or application teams.

- **Business expectations**: This flow requires high availability because call center employees can't purchase tickets if this flow isn't working properly.

- **Business consequences**: If this flow isn't available, it directly affects revenue and reputation.

- **Criticality**: High.

### System flow: Collect telemetry

To understand state changes in the production system, web application and API instances are used to collect and send information, errors, and warnings. This data helps the operations team perform anomaly detection, troubleshooting, and profiling.

- **Business processes that this flow supports**: This flow doesn't support any business processes, but it provides important data for the operations team.

- **Process owner**: The operations team.

- **Business escalation path**: The operations team.

- **Technical escalation path**: The operations team.

- **Business expectations**: This flow requires as much redundancy and resiliency as possible. The operations team should strive to recover this flow from failure, so they don't miss important information and warnings.

- **Business consequences**: If this flow doesn't meet the expected availability, there's a risk of missing problems in production, which can cause serious consequences.

- **Criticality**: Medium

## Reliability checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Reliability checklist](checklist.md)
