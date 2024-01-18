# How to design flows in a workload

The article provides a comprehensive guide for designing flows within a workload. Flows are sequences of actions aimed at achieving specific outcomes. Flow design determines the efficiency of user interactions (user flows) and system communication (system flows). Proper flow design improves how you build new flows and optimize existing flows.

## Why flows matter

Flows enables a more detailed analysis of both functional and technical requirements. Different parts of the workload often have different functional and technical requirements. Addressing workload requirements per flow provides a granular approach to meeting different requirements. It facilitates targeted optimization for each flow and helps you meet the specific reliability, security, cost, operational, and performance targets across a workload load.

## How to design flows


### Define the flow

Defining flows is the process of understanding the flows that your workload needs. It's essential to building new flows properly and optimizing existing flows efficiently. To define flows, you need to define both the business process and use case it supports (*see figure 1*).

:::image type="content" source="./_images/business-workload-process.svg" alt-text="Diagram that shows two boxes, stacked on top of each other. The top box represents a "Business process" with segments marked "Stage 1", "Stage 2", and "Stage n", indicating a sequence of stages in the business process. From each stage, three vertical arrows point downward to a row of three squares representing different use cases. Each square is labeled with "Use case 1", "Use case 2", and "Use case n" respectively. In side each square is a unique flowchart with labeled flows "Flow 1", "Flow 2", and "Flow n". The use cases are all part of a single "Workload". Each stage of the business process is linked to a specific workload use case and each use case has its own flow." lightbox="./_images/business-workload-process.svg" border="false"::: *Figure 1: The relationship between business processes, use cases, flows, and workload.*

#### Define the business process

You need to define or identify the business process the flow supports. A business process is a series of actions (stages) that fulfill a business requirement. Flows determine the sequence a user or data takes to accomplish each stage. For example, selling products online is a business process. The stages in this business process might be listing the product online, receiving orders, and delivering the product. A flow supports each of these stages.

#### Define the use case

Use cases are integral in capturing functional requirements aligned with business process needs. Each use case supports a specific stage in a business process, ensuring focused and efficient functionality. Key elements of a use case include:

- Purpose: Clearly articulate the tasks or objectives, like enabling online purchases. This clarity guides the functional design and sets clear goals.

- Value: Assess the importance of the use case, ranging from routine to critical. This assessment aids in prioritizing and resource allocation, ensuring that critical operations receive appropriate focus and support.

- Consumers: Identify whether users (customers, staff) or system components are the primary consumers. This categorization (user flow or system flow) influences the design and interaction approach.

- Events: Define triggers or conditions that initiate and conclude the use case, outlining the operational scope.

- Execution: Understand operational frequency and variability to anticipate system load, guiding scalability and performance design.

- Dependencies: Identify interdependencies with other use cases or components for seamless integration and risk management.

#### Document the flow definition

Flow definition documents the detailed mapping of steps, decisions, and interactions within a use case, from start to finish:

- Map Sequence: Detail each action in the flow, ensuring alignment with the use case's purpose and objectives.

- Map Decision Points: Outline decision-making criteria and resultant pathways within the flow.

- Map Intersection Points: Identify interactions with other use cases or system components, ensuring smooth data exchange and integration.

- Exception Handling: Define responses to anomalies or unexpected events, enhancing robustness and continuity.

This comprehensive flow definition serves as a blueprint for implementation and ongoing management.

### Identify the requirements of the flow

INTRO NEEDED

#### Functional requirements

Functional requirements of a flow are directly derived from its use case definition:

- Value: The value assigned to a use case informs the prioritization and design of the flow. High-value use cases may require enhanced error handling, performance tuning, or user experience considerations.

- Consumers: Understanding the flow's users or interacting components is crucial. This knowledge shapes design decisions like interface complexity, terminology, and interaction style. For user flows, it enhances user experience design; for system flows, it ensures component compatibility.

- Events: Events defining the start and end of a use case shape the flow's boundaries. They also inform frequency and timing considerations, impacting scalability and capacity planning.

- Execution: The variability and frequency of a use case's execution dictate the flow's robustness and scalability. Flows should be designed to handle different scenarios and loads efficiently.

- Dependencies: Recognizing a use case's dependencies aids in designing flows that integrate smoothly with other system parts. Ensuring the availability of necessary inputs and compatibility of outputs with subsequent processes is vital.

By aligning functional requirements with the use case definition, flows are designed to effectively support specific business processes and needs.

### Technical requirements

The functional requirements derived from the use cases lay the groundwork for establishing specific technical requirements across different pillars: reliability, security, cost optimization, operational excellence, and performance efficiency. These pillars form a framework for setting clear, measurable targets that align with business and organizational objectives.

- *Reliability*: Assess each flow's importance and set reliability targets accordingly. Determine performance thresholds and establish clear service level agreements (SLAs) and objectives (SLOs). Higher criticality flows require more stringent reliability targets.

- *Security*: Analyze the security needs of each flow based on data sensitivity and user activities. Implement and continuously update security measures to meet these needs, while ensuring compliance with regulatory standards.

- *Cost Optimization*: Understand the demands of each flow for effective resource allocation. Set targets to balance cost with performance, ensuring that resources are used efficiently and in alignment with business priorities.

- *Operational Excellence*: Define performance indicators for each flow, focusing on effective monitoring and troubleshooting. Targets should ensure efficient resource use and alignment with organizational goals.

- *Performance Efficiency*: Base performance targets on the initial requirements of each flow. Ensure that essential flows receive adequate resources and continuously adjust targets to meet evolving demands and enhance user experiences.

## Design flow

All well-designed flows share common characteristics. A well-designed flow not only facilitates smooth operation but can also support the objectives of each WAF pillar. Here are the core attributes that every developer should incorporate into their flow designs:

- *Scoped*: Identify distinct starting and ending points for each flow. Clear boundaries help in understanding the scope of the flow and in managing user expectations or system interactions.

- *Logical:* Design your flows with a logical order of steps*.* This ensures that each action follows naturally from the previous one, enhancing efficiency and understandability.

- *Defined*: Incorporate specific conditions that trigger or guide each step in a flow. This precision ensures that the flow responds accurately to user inputs, data changes, or system states.

- *Reliable*: Build error handling and exception paths into your flows. Effective error management prevents disruption and maintains flow integrity under unexpected circumstances.

- *Scalable*: Ensure that your flows can handle varying loads and adapt to growing or shrinking user bases or data volumes without compromising performance or reliability.

- *Secure*: Embed security measures within the flow, safeguarding data and user interactions against unauthorized access and threats.

- *Efficient*: Optimize for the most efficient path, reducing unnecessary steps and ensuring that resources are used judiciously.

- *User-centric* (for user flows): Align the flow design with user expectations and behaviors, making it intuitive and reducing the learning curve for new users.

- *Maintainable*: Design flows that are easy to update and maintain, with clear documentation and modular components that can be modified without affecting the entire workload.

## Flow examples
