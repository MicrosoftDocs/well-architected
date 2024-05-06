---
title: Architecture design diagrams
description: Learn about diagramming practices and types of architecture diagrams that you can create to communicate effectively.
author: ckittel
ms.author: chkittel
ms.date: 11/15/2023
ms.topic: conceptual
---

# Architecture design diagrams

Architects often communicate through diagrams. Diagrams are powerful communication tools that help implementers and stakeholders see the broad vision or dive deep into highly sensitive or nuanced areas of a system. To communicate with intention, an architect must select which diagram is useful in each situation.

The list of diagrams in this article isn't exhaustive. Diagrams are often a composite of multiple types.

Ultimately, the choice of architecture diagram depends on what you're trying to convey and the audience profile. An architect uses multiple types of diagrams throughout activities for design, refinement of requirements, and communication.

## Diagramming practices

Diagrams present substantial information without the need for textual explanation. Avoid ambiguity in diagrams. Here are some recommendations:

**Use standard notations**. Use widely recognized symbols, icons, and presentation conventions for good readability and interpretation of a diagram.

**Avoid ambiguous lines**. Diagrams often show relationships between entities represented as lines. Be consistent in how you use the lines.

**Avoid lines without arrows**. It's hard to know what the relationship is without direction, so use arrows. Label all lines without arrows to denote the relationships.

**Avoid lines with double arrows**. Double arrows imply a bidirectional dependency. Prefer using a single-ended arrow to represent the flow from client to server.

**Label everything**. Provide clear, accurate, and meaningful labels for each icon. Label lines when the relationships aren't clear.

**Maintain consistency**. Use standardized colors, casing, icons, icon sizes, line types, arrow heads, and other representations for similar elements throughout a diagram and across related diagrams. Draw from existing data or taxonomies.

**Be accurate**. Diagrams are abstractions, but don't sacrifice accuracy in the process. For example, don't represent a service in a virtual network if it's not present in that virtual network. A diagram is a communication tool, so you need to avoid miscommunication from inaccuracies.

**Include metadata**. Ensure that a diagram contains metadata that provides essential information about the purpose of the diagram. Metadata also gives context to help viewers understand the diagram's scope and significance. Include items such as title, description, last updated date, author, and external references.

**Use official icons and service names**. When you're representing a specific technology, use the latest official icons from your technology provider. If identifying the technology is important, use the official name for the service.

For example, here are the icons for Microsoft services:

- [Azure architecture icons](/azure/architecture/icons/)
- [Microsoft 365 icons](/microsoft-365/solutions/architecture-icons-templates)
- [Microsoft Dynamics 365 icons](/dynamics365/get-started/icons)
- [Microsoft Power Platform icons](/power-platform/guidance/icons)

## Types of design diagrams

Workload architecture is complex and multidimensional. Each dimension type focuses on a specific aspect of the system by providing a level of detail that's specific to that dimension. For instance, flowcharts illustrate process flow. Entity-relationship diagrams depict relationships between system components.

Having different types of diagrams allows for a comprehensive understanding of dimensions. It helps encourage effective communication, problem-solving, and decision-making among stakeholders.

##### High-level system diagram

A high-level system diagram serves as a broad overview of a whole workload or of a subsection within a workload. It includes the main components, their relationships to each other, and the rough order in which data flows through the system. Arrows show the direction of interaction.

These diagrams are good for reaching a common understanding so that you can start deeper discussions or for stakeholder communication.

##### Block diagram

A block diagram breaks down a workload into its major functional blocks. The blocks are usually technology agnostic. They refer to the functionality that's being performed instead of a specific component.

For example, a block diagram might reference a "messaging bus" instead of a specific message-bus technology. This type of diagram can help explain a system's structure, data flow, and processing flow without distracting the audience with fine details.

##### Component diagram

A component diagram works like a block diagram but replaces generic functionality blocks with specific technologies. It presents a detailed view with the goal of communicating the system's individual technology components and their relationships, such as client/server. These diagrams are a sort of visual bill of materials for the scope of the diagram.

##### Deployment diagram

A deployment diagram focuses on the deployment of infrastructure, commercial off-the-shelf (COTS) software, and custom code across the workload. It shows how the software and code are distributed across the hosting infrastructure.

##### Data-flow diagram

A data-flow diagram (DFD) illustrates how data moves through a system, which is useful when you're modeling data-centric systems. In a diagram like this, it's a good idea to note if data is moved in batches or in real time to remove ambiguity.

##### Sequence diagram

A sequence diagram depicts the communication exchanges between workload components over time. It illustrates client/server relationships and their synchronous or asynchronous nature. It also highlights dependencies in these exchanges and evaluates fault scenarios within them.

##### User-flow diagram

A user-flow diagram focuses on a scoped interaction between workloads, users, or actors and the workload. It's helpful for clarifying and visualizing functional requirements across various ways that a user and the user's data interact with the system.

##### Entity-relationship diagram

An entity-relationship diagram (ERD) is a modeling diagram that represents the structure of a database or another storage system. It shows the relationship between entities (such as tables) through industry-standard attributes and association symbology.

##### Network diagram

A network diagram illustrates the solution from the perspective of the network that it runs on or interacts with. These diagrams are useful in visualizing the workload's network segmentation, network points of failure, and key network transitions such as internet egress and ingress points.

Network diagrams usually have a life past implementation. They're often used in audits and incident response.

##### State diagram

A state diagram is a specialized visualization. It shows the state that a flow (or an individual component) is in. It also shows how the flow transitions between states in response to conditions or events.

##### Flowchart

Although it's not an architecture diagram specifically, a flowchart is another way to bring clarity to a design. Flowcharts are often useful when they represent complex workflows or logic. You can use them to help refine requirements and to help drive implementation choices.

## Next steps

> [!div class="nextstepaction"]
> [Architect's checklist](checklist.md)
