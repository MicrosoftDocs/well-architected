---
title: Effective workload flow design
description: Discover expert strategies for designing efficient flows within workloads. Learn about the importance of flow design, its iterative process, and best practices. Explore our guide to defining flows, identifying requirements, and building optimized, user-centric flows that align with reliability, security, and performance goals. Ideal for IT professionals and business analysts seeking to enhance operational efficiency and workload management.
author: stephen-sumner
ms.author: ssumner
ms.date: 1/31/2024
ms.topic: conceptual
---

# Effective workload flow design

The article provides a guide for designing flows within a workload. Flows are sequences of actions aimed at achieving specific outcomes. Flow design determines the efficiency of user interactions (user flows) and system communication (system flows). Proper flow design improves how you build new flows and optimize existing flows.

## The importance of flows in workload design

Dividing a workload into flows allows you design and optimize a workload more efficiently. Different parts of a workload have different needs. The reliability, security, cost, operations, and performance needs aren't uniform across the workload. By focusing on individual flows, you can prioritize different parts of the workload and meet those needs efficiently.

## How to design a flow

Flow design starts with defining the flow and includes identifying its requirements and building it. Flow design is inherently iterative. You need iterations to refine flow design. Certain aspects that are theoretical at the start become clearer as you go. You should repeat the flow design process iteratively for the lifecycle of the flow (*see figure 1*).

:::image type="content" source="./_images/flow-design-process.svg" alt-text="Diagram that shows a seven step process divided into three stages. The first stage is define the flow. To define the flow, you need to understand prerequisites and document the flow. The second stage is to identify the flow requirements. To identify flow requirements you need to identify the functional requirements and identify the technical requirements. The third stage is to build the flow. To build the flow, you need to follow flow design best practices and build and test the flow." lightbox="./_images/flow-design-process.svg" border="false"::: *Figure 1: The flow design process.*

### Define the flow

Defining flows is about understanding the flows you need to design. You must identify the business process and use case (prerequisites) a flow supports before you can document the flow's definition (*see figure 2*).

:::image type="content" source="./_images/business-workload-process.svg" alt-text="Diagram that shows two boxes, stacked on top of each other. The top box represents a "Business process" with segments marked "Stage 1," "Stage 2," and "Stage n," indicating a sequence of stages in the business process. From each stage, three vertical arrows point downward to a row of three squares representing different use cases. Each square is labeled with "Use case, " "Use case 2," and "Use case n" respectively. Each square contains a unique flowchart with labeled flows "Flow 1," "Flow 2," and "Flow n." The use cases are all part of a single "Workload." Each stage of the business process is linked to a specific workload use case and each use case has its own flow." lightbox="./_images/business-workload-process.svg" border="false"::: *Figure 2: The relationship between business processes, use cases, flows, and workload.*

#### Understand the prerequisites

The prerequisites to defining a flow is understanding the business process and use case the flow supports. A business process is a series of actions (stages) that fulfill a business requirement. Flows determine the sequence a user or data takes to accomplish each stage of a business process. For example, selling products online is a business process. The stages in this business process might be listing the product online, receiving orders, and delivering the product.

From business processes, uses cases emerge. Each use case should support a stage in a business process. Use cases define the functional requirements of flows. A use case should include the following elements:

- *Purpose*: Clearly articulate the tasks or objectives, like enabling online purchases. This clarity guides the functional design and sets clear goals.

- *Criticality*: Assess the importance of the use case, ranging from routine to critical. This assessment aids in prioritizing and resource allocation, ensuring that critical operations receive appropriate focus and support.

- *Consumers*: Identify whether users (customers, staff) or system components are the primary consumers. This categorization (user flow or system flow) influences the design and interaction approach.

- *Events*: Define triggers or conditions that initiate and conclude the use case, outlining the operational scope.

- *Execution*: Understand operational frequency and variability to anticipate system load, guiding scalability and performance design.

- *Dependencies*: Identify interdependencies with other use cases or components for seamless integration and risk management.

#### Document the flow definition

Based on the use case, you should document the flow definition. The flow definition is an outline of each action in the flow. Ensure alignment with the use case's purpose and objectives. Outline decision-making criteria and resultant pathways within the flow. Identify interactions with other use cases or system components, ensuring smooth data exchange and integration. This flow definition serves as a blueprint for flow design and management.

You also need to capture basic information about the flow. Make sure to include the following details in the flow documentation:

- *Flow description*: A high-level description of the flow.

- *Business processes*: The business process the flow supports.

- *Process owner*: The individual that owns the business process.

- *Stakeholders*: The individuals that you should inform or consult on flow status or changes.

- *Escalation paths*: The individuals or groups you should contact to resolve issues. It's a sequence of people. The scope of individual responsibility grows with each person on the path.

- *Business impact*: The importance of this flow to the business.

- *Criticality rating*: A qualitative label that indicates the relative importance of the flow.

For more information, see [Flow examples](#flow-examples).

### Identify the requirements of the flow

Identifying the requirements of a flow involves determining the specific functional and technical requirements that a flow must meet to support its use case. It ensures the flow meets the needs of the use case and determines the design of the flow.

#### Identify the functional requirements of the flow

Functional requirements of a flow are directly derived from its use case definition:

- *Criticality*: The value assigned to a use case informs the prioritization and design of the flow. High-value use cases might require enhanced error handling, performance tuning, or user experience considerations.

- *Consumers*: Understanding the flow's users or interacting components is crucial. This knowledge shapes design decisions like interface complexity, terminology, and interaction style. For user flows, it enhances user experience design. For system flows, it ensures component compatibility.

- *Events*: Events defining the start and end of a use case shape the flow's boundaries. They also inform frequency and timing considerations, impacting scalability and capacity planning.

- *Execution*: The variability and frequency of a use case's execution dictate the flow's scalability. Flows should be designed to handle different scenarios and loads efficiently.

- *Dependencies*: Recognizing a use case's dependencies aids in designing flows that integrate smoothly with other system parts. Ensuring the availability of necessary inputs and compatibility of outputs with subsequent processes is vital.

Aligning functional requirements with the use case definition allows you to design flows that effectively support specific business processes and needs.

#### Identify the technical requirements of the flow

The functional requirements derived from the use cases lay the groundwork for establishing specific technical requirements. Define specific, measurable targets for the flow across the five pillars of the Well-Architected Framework (WAF). These pillars form a framework for setting clear, measurable targets that align with business and organizational objectives.

- *Reliability*: Assess each flow's importance and set reliability targets accordingly. Determine performance thresholds and establish clear service level agreements (SLAs) and objectives (SLOs). Higher criticality flows require more stringent reliability targets.

- *Security*: Analyze the security needs of each flow based on data sensitivity and user activities. Implement and continuously update security measures to meet these needs, while ensuring compliance with regulatory standards.

- *Cost*: Understand the demands of each flow for effective resource allocation. Set targets to balance cost with performance, ensuring that resources are used efficiently and in alignment with business priorities.

- *Operations*: Define performance indicators for each flow, focusing on effective monitoring and troubleshooting. Targets should ensure efficient resource use and alignment with organizational goals.

- *Performance*: Base performance targets on the initial requirements of each flow. Ensure that essential flows receive adequate resources and continuously adjust targets to meet evolving demands and enhance user experiences.

## Build the flow

Designing a flow to meet targets across the five pillars involves a comprehensive approach that integrates these aspects into every stage of the design and implementation process.

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

### Build and test the flow

- *Select technologies*: Choose technologies that align with the set targets in terms of reliability, security, and performance.

- *Develop flow*: Build the flow according to the design, keeping the set targets in mind.

- *Test flow*: Conduct testing to ensure the flow meets targets. Iterate as needed to meet targets.

- *Monitor*: Implement monitoring tools to track resource usage and costs.

Periodically review the flow against set targets and industry standards. Use feedback from monitoring and audits to improve the flow. Adjust targets and processes as necessary to align with changing business needs or technological advancements.

## Next steps

Repeat the process define here throughout the lifecycle of the flow. As you iterate on the flow design, use the Well-Architected Framework to optimize flows from the perspective of each pillar:

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

- *Business impact*: This flow is important for making new concerts available on sales platforms, directly influencing the main revenue stream of the business. When call-center employees are unable to create concerts due to unavailability of this flow, it negatively impacts both revenue and the company's reputation. However, high availability isn't essential for this process since concerts are typically scheduled in advance on a weekly basis. The sales department specified a requirement of 95% availability for this process and is agreeable to downtime outside of business hours for maintenance purposes.

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
