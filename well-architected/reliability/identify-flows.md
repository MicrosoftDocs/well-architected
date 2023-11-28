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

|[RE:02](checklist.md)| **Identify and prioritize user and system flows.** Use a criticality scale that's based on your business requirements to prioritize the flows. |
|---|---|

This guide describes the recommendations for identifying and prioritizing workload flows. Identifying and prioritizing workload flows involves systematically mapping and evaluating the various user paths and system processes within a workload to determine their business value and impact on overall operations. This practice ensures you identify and prioritize the most critical workload functionality to reducing the risk of damaging failures. Failure to identify and prioritize workload flows can lead to system breakdowns and compromised workload reliability. This guide serves as a prerequisite for the other reliability guides.

**Definitions**

| Term | Definition |
|---------|---------|
| User flow | The paths or sequences of actions that users take within an application or system. |
| System flow | The flow of information and processes within a system. The system automatically follows this flow to enable user flows or workload functionality. |

## Key design strategies

When you design your workload, it's essential to define and refine both the user and system flows. User flows chart the journey of a user through your application or system. They underscore the user interface, interactions, decisions, and the steps required to complete a task. This user-centric perspective prioritizes user experience and interface design.

System flows focus into the internal workings of your application. It encompasses data movement, input processing, output processing, and interactions among system components, backend services, and external APIs. This technical perspective focuses on the intricate details of how the system operates internally.

By understanding these flows, you can strategically enhance the reliability of the most crucial aspects of your workload. This approach allows you to prioritize flows with stringent availability requirements, while appropriately deprioritizing those with less critical demands.

You should identify and define flows early in the design phase of your workload. It grants you a clearer understanding of the foundational elements that influence your design choices, especially in relation to the reliability of your workload. This proactive planning facilitates a more robust and dependable workload. It aligns your architectural decisions closely with the reliability goals of your workload.

### Identify all user and system flows

Identifying all user and system flows is about creating a comprehensive catalog of flows in the workload. It requires you to map out every user interaction and process within a system. To ensure a comprehensive understanding, you need to map both user and system flows end-to-end. This mapping is a prerequisite for identifying critical flows. Here are recommendations for identifying all user and system flows:

- *Interview stakeholders.* Stakeholders can provide valuable information to identify flows and even begin the process of mapping and prioritizing flows. Interview users, business analysts, and technical teams to gather insights about various operations and user interactions within the workload.

- *Review documentation.* In the design phase, you might not have documentation to review. However, if documentation exists, you should use it. Ask for system architecture diagrams, user manuals, and process descriptions to understand the intended functionality of the workload.

- *Observe the workload.* Monitor the system in operation, noting how users interact with it and how different components interact with each other. You should analyze system logs, performance metrics, and user activity logs to identify patterns, frequent tasks, and system responses.

- *List identified flows.* The outcome of identifying all flows should be a list of user flows and system flows that highlight the expected behavior of the system. Compile a list of all the flows you've identified, categorizing them into user flows (focusing on user interactions) and system flows (focusing on backend processes and data movement).

- *Define start and end points.* For each identified flow, clearly define where the flow starts and where it ends. For user flows, document each user interaction and its expected outcome and focus on the user experience and interface design. For system flows you need to identify its underlying triggers and expected outcomes.

- *Break down each flow.* Break down the flow into individual steps or stages, describing the actions, decisions, or processes that occur at each point. Note how each step interacts with other parts of the system, including dependencies on other flows or external systems. You should be able to pinpoint how flows integrate with and affect the workload and user experience. This dual approach provides a holistic view of your entire workload.

- *Document outputs.* Identify and map out any alternative paths or exceptions within each flow, such as error handling or conditional branching. You do not need to identify all possible outcomes of each flow. For user flows, you should identify the intended end-to-end behavior of the interaction. For system flows, you should identify the intended end-to-end behavior of the process. If a flow has multiple possible outcomes, you should add it to the catalog as distinct entries.

- *Visualize with diagrams.* Create flowcharts or diagrams to visually represent the sequence of steps. Tools like Microsoft Visio, Lucidchart, or even simple drawing tools can be used for this purpose. You can use UML Sequence Diagrams or Use Case Diagrams or less formal descriptive list in text format like in the examples below.

- *Update flow mapping iteratively.* Validate and refine your flow diagrams with feedback from stakeholders to ensure accuracy and completeness. Flow mapping is an iterative process. Flows might not be fully defined, or you might decide to combine flows based on business requirements. You might not be able to fully define all flows while the workload is in the design phase. You might choose to split and or combine related flows as you determine the interaction with business processes and business consequences. As the workload flows become more clearly defined, you should update the catalog of flows to match.  

### Identify business processes for each flow

Business processes are a series of tasks to achieve an output, such as order fulfillment, customer service management, or inventory control. Identifying business processes for each flow involves mapping flows to one or more business processes. This mapping helps you understand the importance of each flow to the business.

Existing documentation or business plans should provide a mapping of flows to business processes. There might be related documents like user manuals, training materials, or system specifications that can provide insights into the intended use and purpose of the workload. If not, you need to do that mapping. Here are recommendations to identify business processes for each flow:

- *Use flow breakdowns and outputs.* Using the breakdown and output of each workload flow step, you can correlate flows to underlying business processes. By examining the output of each flow, you can determine which business activity or process it feeds into or supports. Review the outputs or results generated by the workload and figure out which flow creates it. This could include reports, data files, or completed tasks. Next, determine what business objectives these outputs are contributing to. For instance, a step that generates a sales report in the workload might support the business process of sales analysis.

- *Conduct interviews.* Speak with team members and stakeholders who interact with the workload. You should ask specific questions about their daily tasks, how they use the workload, and what objectives they achieve with it. Technical staff often have a deeper understanding of how the workload is structured and can provide insights into the business processes it supports.

- *Monitor workload usage.* For existing workloads, monitor the workload and look for patterns in usage that indicate underlying business processes, such as data entry, order processing, or customer interaction.

- *Connect output to business process.* Connect the dots from the flow outputs to the overall business process they support. For example, if a flow step involves processing customer orders, this directly supports the business process of order fulfillment, which in turn contributes to the business objective of maintaining customer satisfaction and generating revenue.

### Identify process owners and stakeholders for each flow

The process owner for a flow is the individual that's responsible for the successful execution of a given process, and often maps directly to the process owner for the related business process. They are responsible for making critical decisions. You should identify the process owner for each workload flow.

You should also identify the stakeholders for each flow. Stakeholders have dependencies on a flow, or they manage dependencies of the workload flow.

<!--Identify process owners and stakeholders for each flow -->

You might have a responsibility assignment matrix (RAM) or RACI matrix that contains this information. Typically, process owners are responsible or accountable for a process, and stakeholders are consulted or informed.

#### Identify escalation paths for each flow

Identifying escalation paths is about determining the specific procedures and channels for escalating issues related to a flow, such as urgent updates, security concerns, degradations, or technical incidents. The goal is to ensure timely and effective resolution. You need to identify clear lines of responsibility and response mechanisms for incidents affecting the health of the flow and the remediation of these incidents.

### Identify business outcomes of each flow
<!-- what should this be?-->

Identifying the business outcomes of each flow is essential for understanding and agreeing upon the technical and business impact if business targets aren't met. This process involves a thorough analysis of how each flow within a workload contributes to or detracts from key business objectives, such as revenue, customer satisfaction, or operational efficiency. By understanding both the positive and negative outcomes of each flow, you can prioritize efforts to ensure the reliability of the most critical flows. Here are steps to identify the business consequences of each flow:

- *Identify positive outcomes.* Determine the expected benefits when a flow runs as intended. The expected benefits could include improved efficiency, increased revenue, enhanced customer satisfaction, or any other positive impact on the business.

- *Define capacity and availability assumptions.* Establish assumptions about the expected capacity and availability of each process. Consider factors like throughput per unit of time, expected business hours, and target percentage uptime. If there are expectations for Recovery Time Objective (RTO) or Recovery Point Objective (RPO) they should be included. These assumptions help in understanding the scalability and reliability requirements of each flow.

- *Identify negative outcomes.* Assess the potential negative impacts if a process fails or does not work as expected. Consider quantifying specific losses, such as revenue drops. Include subjective effects like damage to reputation, erosion of customer trust, or adverse effects on other related business processes.

By systematically evaluating these aspects, you can gain a comprehensive view of how each flow impacts the business, guiding you in making strategic decisions about enhancing the reliability and effectiveness of your workload.

### Assign criticality ratings

Assigning criticality to each flow involves a detailed evaluation of their importance relative to the overall business objectives and potential impacts. This process is a logical continuation of identifying, mapping, and aligning with business processes. It's important to assign these criticality ratings considering not just the direct consequences of a flow's unavailability but its indirect impact on other interconnected processes and the overall system integrity. Use the following criticality descriptions to assign your critical ratings:

- *High criticality*: These are flows integral to core business functions, directly impacting critical aspects such as customer experience, financial transactions, security protocols, or scenarios involving human health and safety. The failure or disruption of these flows could lead to significant immediate or long-term negative effects, such as loss of revenue, breach of trust, or legal ramifications. Prioritizing these flows ensures that the most crucial aspects of the workload are robust and resilient.

- *Medium criticality*: Flows classified under this category are important for the complete functionality of the system but do not directly interface with the customer or critical business operations. An example might be internal data processing workflows which, if disrupted, can be retried or compensated for without immediate external impact. These flows are essential for smooth operations but offer a buffer in terms of immediate customer or financial impact, allowing for managed responses to issues.

- *Low criticality*: These are flows that, while part of the workload, do not have a direct or significant impact on the core business functions or customer experience. Examples include ancillary processes like nightly log transfers or optional user features such as feedback surveys. While these flows contribute to the overall system, their disruption is unlikely to cause significant immediate business or operational issues.

By following this structured approach to assigning criticality, you can effectively prioritize resources and focus on maintaining and enhancing the reliability and effectiveness of your most critical flows.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Higher expectations for reliability sometimes coincide with higher setup costs, operational costs, and management burden for operators. Ensure that stakeholders understand the potential cost increases of improving the reliability of critical flows.

## Organizational alignment

Cloud Adoption Framework provides guidance for workloads that require a business criticality classification.

For more information, see [business criticality in cloud management](/azure/cloud-adoption-framework/manage/considerations/criticality).

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
