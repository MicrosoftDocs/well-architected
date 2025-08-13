---
title: Architecture design diagrams
description: Learn about diagramming practices and types of architecture diagrams that you can create to communicate effectively.
author: ckittel
ms.author: chkittel
ms.date: 08/13/2025
ms.topic: conceptual
---

# Architecture design diagrams

Architects often communicate through diagrams. Diagrams are powerful communication tools that help implementers and stakeholders visualize the approach or explore specific, complex areas of a system. To communicate with intention, an architect must select the appropriate diagram type for each situation and audience.

The list of diagrams in this article isn't exhaustive. Diagrams are often a composite of multiple types.

Ultimately, the choice of architecture diagram depends on what you're trying to convey and your audience's needs. Architects use multiple types of diagrams throughout design activities, requirements refinement, and stakeholder communication.

## Diagramming practices

Effective diagrams convey substantial information without requiring extensive textual explanation. To avoid ambiguity and ensure clear communication, follow these recommendations:

**Use standard notations**. Use widely recognized symbols, icons, and presentation conventions to ensure good readability and consistent interpretation across different audiences.

**Avoid ambiguous lines**. Diagrams often show relationships between entities using lines. Be consistent in how you represent these relationships throughout your diagrams.

**Use directional arrows**. Lines without arrows make relationships unclear. Always use arrows to indicate direction, and label any lines without arrows to clarify the relationships they represent.

**Avoid bidirectional arrows**. Double arrows imply bidirectional dependencies, which can create confusion. Use single-ended arrows to represent the flow from the dependent component (client) to the dependency (server).

**Label everything clearly**. Provide clear, accurate, and meaningful labels for each icon, grouping container, and relationship. Label lines when relationships aren't immediately obvious from context.

**Maintain consistency**. Use standardized colors, casing, icons, icon sizes, line types, arrow heads, and other visual elements for similar components throughout a diagram. Maintain this consistency across all diagrams in your workload's design documentation. Draw from existing organizational standards or taxonomies.

**Be accurate**. While diagrams are abstractions, don't sacrifice accuracy for unnecessary simplicity. For example, don't show a service injected in a virtual network if it's actually deployed outside that network. Inaccuracies in diagrams can lead to serious miscommunication and implementation delays or errors.

**Include metadata**. Ensure each diagram contains metadata that provides essential context about its purpose, scope, and significance. Include elements such as title, description, last updated date, author, version, and external references. This information helps viewers understand the diagram's intent and freshness.

**Use official icons and service names**. When representing specific technologies, always use the latest official icons and naming conventions from the technology provider. This ensures accuracy and helps viewers immediately recognize the technologies being used.

For example, here are the icons for Microsoft services:

- [Azure architecture icons](/azure/architecture/icons/)
- [Microsoft 365 icons](/microsoft-365/solutions/architecture-icons-templates)
- [Microsoft Dynamics 365 icons](/dynamics365/get-started/icons)
- [Microsoft Entra ID architecture icons](/entra/architecture/architecture-icons)
- [Microsoft Fabric icons](/fabric/fundamentals/icons)
- [Microsoft Power Platform icons](/power-platform/guidance/icons)

## Types of design diagrams

Workload architecture is complex and multidimensional. Different diagram types focus on specific aspects of the system, providing targeted levels of detail for each dimension. For example, flowcharts illustrate process flow, while entity-relationship diagrams show relationships between system components.

Using different diagram types enables comprehensive understanding across all architectural dimensions. This approach facilitates effective communication, problem-solving, and decision-making among diverse stakeholders with varying technical backgrounds and concerns.

### High-level system diagram

A high-level system diagram provides a broad overview of an entire workload or a major subsection within a workload. It includes the main components, their relationships, and the general flow of data through the system. Arrows indicate the direction of interactions and dependencies.

These diagrams are excellent for establishing a common understanding among stakeholders before diving into deeper technical discussions. They're also valuable for executive and stakeholder communication where high-level understanding is more important than technical details.

### Block diagram

A block diagram breaks down a workload into its major functional blocks. These blocks are typically technology agnostic, focusing on the functionality being performed rather than the specific component.

For example, a block diagram might reference a "messaging bus" instead of specifying a particular message-bus technology like Azure Service Bus or Apache Kafka. This abstraction level helps explain a system's structure, data flow, and processing flow without overwhelming the audience with implementation specifics, making it ideal for early design discussions and requirements gathering.

### Component diagram

A component diagram builds upon block diagrams by replacing generic functional blocks with specific technologies. It presents a detailed view that communicates the system's individual technology components and their relationships, such as client-server interactions. These diagrams serve as a visual bill of materials for the architecture, showing exactly which technologies will be implemented.

### Deployment diagram

A deployment diagram focuses on how infrastructure, commercial off-the-shelf (COTS) software, and custom code are deployed into the hosting environment. It shows the mapping between software components and the physical or virtual infrastructure that hosts them.

These diagrams are crucial for DevOps planning, environment setup, and understanding the operational aspects of the architecture. They help teams visualize scaling boundaries, deployment units, and infrastructure dependencies.

### Data-flow diagram

A data-flow diagram (DFD) illustrates how data moves through a system, making it particularly useful when modeling data-centric architectures. These diagrams should clearly indicate whether data moves in batches or in real-time to eliminate ambiguity about processing patterns and timing requirements. DFDs are allow you to communicate data lineage, identifying potential bottlenecks, and ensuring data governance requirements are met throughout the system.

### Sequence diagram

A sequence diagram depicts the chronological communication exchanges between workload components over time. It illustrates client-server relationships and clearly shows whether interactions are synchronous or asynchronous. These diagrams also highlight dependencies in communication patterns and help evaluate potential fault scenarios within component interactions. Sequence diagrams are particularly valuable for API design.

### User-flow diagram

A user-flow diagram focuses on specific interactions between users, external actors, and the workload. It visualizes the user journey through the system, showing how users and their data interact with various components and processes. These diagrams are helpful for clarifying functional requirements, validating user experience design, and ensuring that all user scenarios are properly addressed in the architecture.

### Entity-relationship diagram

An entity-relationship diagram (ERD) is a specialized modeling diagram that represents the structure of databases or other data storage systems. It shows relationships between entities (such as tables, collections, or data objects) using industry-standard attributes and association symbols. ERDs are common for database design and data modeling activities. They help developers understand data relationships and constraints before implementation begins.

### Network diagram

A network diagram illustrates the solution from a network infrastructure perspective, showing how components communicate across network boundaries. These diagrams visualize network segmentation, potential network failure points, and critical network transitions such as internet ingress and egress points. A workload can benefit from having one network diagram that focus on east-west traffic and another network diagram that focuses on north-south traffic.

Network diagrams often have extended utility beyond the initial implementation phase. They're frequently referenced during security audits, compliance reviews, and incident response activities, making them valuable longterm documentation assets.

### State diagram

A state diagram is a specialized visualization that shows the various states that a workflow or individual component can be in, along with the conditions or events that trigger transitions between states. These diagrams are particularly useful for modeling complex business processes, lifecycles, and error handling scenarios.

State diagrams help ensure that all possible system states are considered and properly handled in the implementation, reducing the likelihood of unexpected behaviors in production.

### Flowchart

While not strictly an architecture diagram, flowcharts are valuable tools for bringing clarity to complex workflows, decision logic, and business processes within your architecture. They're particularly useful for representing approval processes and conditional branching scenarios.

Flowcharts helping teams refine requirements, drive implementation choices, and document operational procedures. They're also valuable additions to incident response plans, where they can highlight key decision points and their associated actions or notification channels.

## Next steps

> [!div class="nextstepaction"]
> [Architect's checklist](checklist.md)
