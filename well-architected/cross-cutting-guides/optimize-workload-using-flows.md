---
title: Optimize workload design using flows
description: Explore a detailed guide about optimizing workloads through structured flow design. This article provides an in-depth look at a three-step process for workload optimization, including defining flow structures, setting technical requirements, and designing flows to meet these specifications. It emphasizes the significance of aligning flows with business processes and use cases, and offers practical examples and best practices.
author: stephen-sumner
ms.author: ssumner
ms.date: 1/31/2024
ms.topic: conceptual
---

# Optimize workload design using flows

This article covers the targeted optimization of workloads using flows. Different components of a workload have varying requirements and levels of importance. By segmenting a workload into flows, you can prioritize different parts of a workload and better align workload investments with the importance of each flow.

This workload optimization process is iterative and involves three key steps: (1) define the flow structure within your workload, (2) define technical requirements, and (3) design the flow to meet the requirements (*see figure 1*).

:::image type="content" source="./_images/flow-design-process.svg" alt-text="Diagram that shows a three-step process with five actions. The first step is to define the flow. To define the flow, you need to understand prerequisites and document the flow. The second step is to define the flow requirements. To define flow requirements, you need to establish technical targets. The third step is to design the flow. To design the flow, you need to follow flow design best practices and develop and test the flow. There's an arrow from the build and test action back to the first action (understand prerequisites) that indicates iterations of this process." lightbox="./_images/flow-design-process.svg" border="false"::: *Figure 1: The process to optimize workloads using flows.*

## Define the flow

Before you can define flow requirements, you need to understand the business drivers for the flow. The prerequisites to defining a flow are identifying the business process and use case its supports. When you understand the prerequisites, you can start documenting the flow.

### Understand the prerequisites

Flows are sequences of actions that support workload functionality. There are two primary types of flows: user flows and system flows. User flows determine user interactions. System flows determine communication between workload components. Flows support business processes and use cases. A workload consists of multiple use cases. You need to identify the business process and use case the flow supports before documenting a flow (*see figure 2*).

:::image type="content" source="./_images/business-workload-process.svg" alt-text="Diagram that shows two boxes, stacked on top of each other. The top box represents a business process with segments marked Stage 1, Stage 2, and Stage n, indicating a sequence of stages in the business process. From each stage, three vertical arrows point downward to a row of three squares representing different use cases. Each square is labeled with Use case, Use case 2, and Use case n respectively. Each square contains a unique flowchart with labeled flows Flow 1, Flow 2, and Flow n. The use cases are all part of a single workload. Each stage of the business process is linked to a specific workload use case and each use case has its own flow." lightbox="./_images/business-workload-process.svg" border="false"::: *Figure 2: The relationship between business processes, use cases, flows, and workload.*

#### Identify the business process

A business process is a series of actions (stages) that fulfill a business requirement. Flows determine the sequence a user or data takes to accomplish each stage of a business process. For example, selling products online is a business process. The stages in this business process might be listing the product online, receiving orders, and delivering the product.

#### Identify the use case

A use case defines the functional requirements of a flow. You need to identify and understand the use case a flow supports before establishing the technical requirements of a flow. Each use case should support one stage in a business process (*see figure 2*). A use case should define the following attributes:

- *Purpose*: Clearly articulate the tasks or objectives, like enabling online purchases. This clarity guides the functional design and sets clear goals for flow design.

- *Criticality*: Assess the importance of the use case, ranging from routine to critical. The value assigned to a use case informs the prioritization and design of the flow. High-value use cases might require enhanced error handling, performance tuning, or user experience considerations.

- *Consumers*: Identify whether users (customers, staff) or system components are the primary consumers. This categorization determines whether it's a user flow or system flow and influences the design.

- *Events*: Define triggers or conditions that initiate and conclude the use case. These events define the flow's boundaries.

- *Execution*: Understand the operational frequency and variability of the use case to anticipate system load. You must design a flow to handle different execution scenarios.

- *Dependencies*: Identify interdependencies with other use cases for risk management. Recognizing a use case's dependencies aids in designing flows that integrate smoothly with other system parts. You need to ensure the availability of necessary inputs and compatibility of outputs with subsequent processes.

### Document the flow

Use the use case to document the flow. You should outline or map each action you need in a flow. Capture decision criteria and pathways. Identify interactions with other use cases. This outline serves as a blueprint for flow design and management. You also need to capture business information about the flow. Make sure to include the following details in the flow documentation:

- *Flow description*: A high-level description of the flow.

- *Business process*: The business process the flow supports.

- *Process owner*: The individual that owns the business process.

- *Stakeholders*: The individuals that you should inform or consult on flow status or changes.

- *Escalation paths*: The individuals or groups you should contact to resolve issues. It's a sequence of people. The scope of individual responsibility grows with each person on the path.

- *Business impact*: The importance of this flow to the business.

- *Criticality rating*: A qualitative label that indicates the relative importance of the flow.

For more information, see [Flow examples](#flow-examples).

## Define flow requirements

Utilize the use case to establish the technical targets of the flow. Define measurable targets for the flow that align to the five pillars of the Well-Architected Framework (WAF). These pillars provide a framework for setting technical targets:

- *Reliability targets*: Assess each flow's importance and set reliability targets accordingly. Determine performance thresholds and establish clear service level agreements (SLAs) and objectives (SLOs). Higher criticality flows require more stringent reliability targets.

- *Security targets*: Analyze the security needs of each flow based on data sensitivity and user activities. Implement and continuously update security measures to meet these needs while ensuring compliance with regulatory standards.

- *Cost targets*: Understand the demands of each flow for effective resource allocation. Set targets to balance cost with performance. Ensure resource usage aligns with business priorities.

- *Operational targets*: Define metrics for effective monitoring and troubleshooting. Targets should ensure efficient resource use and alignment with organizational goals.

- *Performance targets*: Base performance targets on the initial requirements of each flow. Ensure that essential flows receive adequate resources and continuously adjust targets to meet evolving demands and enhance user experiences.

## Design the flow

Design the flow to meet the technical targets. You should familiarize yourself with flow design best practices so that you achieve the right result. Build and test the flow. Iterate on the design until it meets the technical targets you established.

### Follow flow design best practices

As you design a flow, follow flow design best practices. A well-designed flow has the following attributes:

- *Scoped*: Identify distinct starting and ending points for each flow. Clear boundaries help optimize user or system interactions.

- *Logical:* Design your flows with a logical order of steps. Optimize for the most efficient path and reduce unnecessary steps.

- *Maintainable*: Design flows that are easy to update and maintain. Use modular components that you can modify without affecting the entire workload.

- *Defined*: Incorporate specific conditions that trigger or guide each step in a flow. This precision ensures that the flow responds accurately to user inputs, data changes, or system states.

- *Reliable*: Build error handling and exception paths into your flows. Effective error management prevents disruption and maintains flow integrity under unexpected circumstances.

- *Scalable*: Ensure it can handle varying loads and adapt to growing or shrinking user bases or data volumes.

- *Secure*: Embed security measures within the flow. Protect data and user interactions against unauthorized access and threats.

- *Efficient*: Plan for efficient use of resources without over-provisioning. Keep cost optimization in mind.

- *User-centric*: For user flows, align the flow design with user expectations and behaviors. Make it intuitive and reduce the learning curve for new users.

### Develop and test the flow

Develop the flow to meet technical targets and test it to ensure it meets its requirements. This process validates that the flow operates as intended, efficiently handles its tasks, and meets the technical targets. Here's guidance to build and test a flow:

- *Select technologies*: Choose technologies that align with the set targets in terms of reliability, security, and performance.

- *Develop flow*: Build the flow according to the design, keeping the set targets in mind.

- *Test flow*: Conduct testing to ensure the flow meets targets. Iterate as needed to meet targets.

- *Monitor*: Implement monitoring tools to track resource usage and costs.

Periodically review the flow against set targets and industry standards. Use feedback from monitoring and audits to improve the flow. Adjust targets and processes as necessary to align with changing business needs or technological advancements.

## Optimize flows

Repeat the process defined in this article throughout the lifecycle of the flow. As you iterate on the flow design, use the Well-Architected Framework to optimize flows from the perspective of each pillar:

- [Flow reliability](/azure/well-architected/reliability/identify-flows)
- [Flow security](/azure/well-architected/security/secure-development-lifecycle#requirements-phase)
- [Flow cost optimization](/azure/well-architected/cost-optimization/optimize-flow-costs)
- [Flow operational excellence](/azure/well-architected/operational-excellence/observability#key-design-strategies)
- [Flow performance efficiency](/azure/well-architected/performance-efficiency/prioritize-critical-flows)

## Flow examples

Here are a few flow examples to help you design your flows. The examples use the [reliable web app pattern reference architecture](/azure/architecture/web-apps/guides/reliable-web-app/dotnet/plan-implementation) as the basis and shows the documentation you should have on each flow.

:::image type="content" source="./_images/relecloud-flow.png" alt-text="Diagram that shows an example flow based on Relecloud." border="false" lightbox="./_images/relecloud-flow.png":::

### User flow 1: Create upcoming concerts

*Flow description*: Call-center employees use the application to create an upcoming concert.

- *Business processes*: This flow supports the *purchasing ticket* process, but it's asynchronous, lowering its criticality.

- *Process owner*: Director of Sales.

- *Stakeholders*: Sales department, concert planning and operations, platform team, and application team.

- *Escalation paths*: Application team, platform team, then sales department.

- *Business impact*: This flow is important for making new concerts available on sales platforms, directly influencing the main revenue stream of the business. When call-center employees are unable to create concerts due to the unavailability of this flow, it negatively impacts both revenue and the company's reputation. However, high availability isn't essential for this process since concerts are typically scheduled in advance on a weekly basis. The sales department specified a requirement of 95% availability for this process and is agreeable to downtime outside of business hours for maintenance purposes.

- *Criticality rating*: Low.

### User flow 2: Search concerts

*Flow description*: Call-center employees use the application to search for upcoming concerts.

- *Business processes*: This flow supports the *purchasing ticket* process, but call-center employees can opt to list all concerts if the search function isn't available.

- *Process owner*: The user experience (UX) department.

- *Stakeholders*: Sales department, platform team, and application team.

- *Escalation path*: Application team, platform team, sales department manager on-call.

- *Business impact*: This flow allows call-center employees to quickly find concerts and is part of the normal sales process. High availability of this flow isn't critical since employees have the capability to list concerts even in its absence. It does degrade the call-center employee's experience might degrade and affect productivity. Customers could experience frustration due to increased wait times or delays. The sales department requested a 99% availability of this flow during regular business hours.

- *Criticality rating*: Medium.

### User flow 3: Get a list of the concerts

*Flow description*: Call-center employees use the application to get a list of concerts.

- *Business processes*: This flow directly supports the *purchasing ticket* process.

- *Process owner*: Director of Platform.

- *Stakeholders*: Sales department, platform team, data team.

- *Escalation path*: Data team, data team on-call engineer, platform team on-call engineer.

- *Business impact*: This flow is integral to the critical path of revenue-generating transactions for the business. High availability is essential, as call-center employees rely on this flow to process ticket purchases. In recognition of its importance, the business mandates a 99.9% uptime for this flow, which includes extended business hours.

- *Criticality rating*: High.

### User flow 4: Purchase ticket

*Flow description*: Call-center employees use the application (the *authentication and authorization* process) to buy tickets for an upcoming concert (the *list upcoming concerts* process) on behalf of Relecloud customers.

- *Business processes*: This flow is the core feature and flow of the application.

- *Process owner*: Director of Sales.

- *Stakeholders*: Sales department and all technical teams.

- *Escalation path*: Application team on-call engineer, platform team on-call engineer, data team on-call engineer, Chief Operating Officer.

- *Business impact*: High availability of this flow is crucial, as it directly enables customer ticket purchases. Any malfunction or unavailability of this flow can significantly impact both revenue and the company's reputation. The business set a stringent requirement for this vital process, expecting 99.9% uptime, even during extended business hours.

- *Criticality rating*: High.

### User flow 5: Authentication and authorization

*Flow description*: Call-center employees securely sign in to the application. Administrators provide them with the proper roles to purchase tickets on behalf of Relecloud customers.

- *Business processes*: This flow directly supports the *purchasing ticket* process. Without this functionality, call-center employees can't sign into the application to buy tickets.

- *Process owner*: Platform team.

- *Stakeholders*: Platform team, operations team, and sales department.

- *Escalation path*: Platform team on-call engineer, Chief Operating Officer.

- *Business impact*: This flow requires high availability because call-center employees can't purchase tickets if this flow isn't working properly. If this flow isn't available, it directly affects revenue and reputation. It's a key process that the business expects 99.9% uptime for, including during extended business hours.

- *Criticality rating*: High.

### System flow: Collect telemetry

*Flow description*: To understand state changes in the production system, web application and API instances collect and send information, errors, and warnings. This data helps the operations team perform anomaly detection, troubleshooting, and profiling.

- *Business processes*: This flow doesn't support any business processes, but it provides important data for the operations team.

- *Process owner*: Director of Operations.

- *Stakeholders*: Operations team, platform team, and data team.

- *Escalation path*: Operations team (24/7), data team on-call engineer.

- *Business impact*: This flow is essential for the business's monitoring and continuous improvement efforts. It needs to be as redundant and resilient as possible. The operations team is responsible for quickly restoring this flow after any failure to avoid missing critical information and warnings. If the flow fails to achieve the expected availability, there's a risk of overlooking production issues, potentially leading to severe consequences. To mitigate this risk, the operations department aims for 99% uptime, 24/7. They must schedule maintenance-related downtime at least 48 hours in advance.

- *Criticality rating*: Medium.
