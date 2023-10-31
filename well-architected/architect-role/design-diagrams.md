---
title: Architecture design diagrams
description: Learn about creating effective architecture diagrams.
author: ckittel
ms.author: chkittel
ms.date: 10/26/2023
ms.topic: conceptual
---

# Architecture design diagrams

Architects often communicate through diagrams. Diagrams are powerful communication tool for implementors and stakeholders to see the broad vision or dive in deep into highly sensitive or nuanced area of the system. There are numerous types of ways to communicate intent with various types of diagrams. An architect must select which diagrams are useful in which situations to communicate with intention.

This list of diagrams presented in this article isn't exhaustive. Expect diagrams to be a composite of multiple of these types. Ultimately, the choice of architecture diagram depends on what you're trying to communicate and the audience profile. Multiple types of diagrams are used throughout the design, requirements refining, and communication activities performed by an architect. 

## Diagramming practices

Diagrams present substantial information without the need for textual explanation. Avoid ambiguity in diagrams. Here are some recommendations:

**Use standard notations**. Use widely recognized symbols, icons, and presentation conventions for good readability and interpretation of the diagram.

**Avoid ambiguous lines**. Diagrams often show relationships between entities represented as a line. Be consistent in how the lines are used.

**Avoid lines without an arrow**. It's hard to know what the relationship is without direction. All lines without arrows should be labeled to denote the relationship.

**Avoid lines with double arrows**. Double arrows imply a bi-directional dependency. Prefer using a single ended arrow to represent the flow from client to server.

**Label everything**. Provide clear, accurate, and meaningful labels for each icon. Label lines when the relationship isn't clear.

**Maintain consistency**. Use consistent colors, casing, icons, icon sizes, line types, arrow heads, and other standardized represenation throughout the diagram, and across all related diagrams, to represent similar elements. Draw from existing data or taxonomy.

**Be accurate**. Diagrams are abstractions, but don't sacrifice accuracy in the process. For example, don't represent a service in virtual network that doesn't exist within a virtual network. As a communication tool, miscommunication through inaccuracies need to be avoid.

**Include metadata**. Ensure the diagram contains metadata that provides essential information about the diagram itself, its purpose, and context to help viewers understand the scope and significances of the diagram. Include items such as, Title, Description, Last Updated Date, Author, external references.

**Use official icons and service names**: When representing specific technology, always use the latest icons provided by your technology provider to represent the subject. If identifying the technology is important, use the official name for the service. Use the official [Azure architecture icons](/azure/architecture/icons/) for diagrams involving Azure components.

## Types of design diagrams

Workload architecture is complex and multidimensional. Each dimension type focuses on a specific aspect of the system, providing level of detail that's specific to that dimension. For instance, flow charts illustrate process flow, while entity-relation diagrams depict relationships between system components. Having different types of diagrams allows for comprehensive understanding of different dimension. It helps in effective communication, problem-solving, and decision-making among stakeholders.

##### High-level system diagram

This diagram serves as a broad overview of a whole workload or even a broad overview of a subsection within. It includes the main components, their relationship to each other with arrows repenting the direction of interaction, and the rough order in which data flows through the system. These are good for reaching common understanding so that you can start deeper discussions or for general stakeholder communication.



##### Block diagram

This diagram breaks down a workload into its major functional blocks. The blocks are usually technology agnostic and instead refer to the functionality being performed instead of a specific component itself. For example, it may reference a "messaging bus" instead of a specific message bus technology. This diagram can help explain a system's structure and data & processing flow without distracting with fine-grain details.



##### Component diagram

This diagram works like the block diagram but replaces generic functionality blocks with specific technology. Its intent is a detailed view with the goal of communicating the system's individual specific technology components and their relationships, such as client/server. They're a sort of visual bill-of-material for the scope of the diagram.



##### Deployment diagram

This diagram focuses on the deployment of infrastructure, COTS software, and custom code across the workload. It shows how the software and code is distributed across the various hosting infrastructure.



##### Data flow diagram (DFD)

This diagram illustrates the flow of data within a system, which is useful when modeling data-centric systems. It visualizes how data moves through a system. On a diagram like this, it's a good idea to note if data is moved in batch or in real time to remove ambiguity.


##### Sequence diagram

This type of diagram depicts the communication exchanges between different workload components over time. They illustrate client-server relationships and their synchronous or asynchronous nature. The also highlight dependencies in these exchanges and evaluating fault scenarios within them.



##### User flow diagram

This type of diagram focuses on a scoped interaction between workloads users or actors and the workload. It's helpful for clarifying and visualizing functional requirements across various ways a user and its data interacts with the system.



##### Entity-relationship diagram (ERD)

An ERD is a modeling diagram that is used to represent the structure of a database or other storage systems. It shows the relationship between entities (such as tables) through industry standard attributes and association symbology.



##### Network diagram

A network diagram illustrates the solution from the perspective of the network that it runs upon or interacts with. These diagrams are useful in visualizing the workload's network segmentation, network points of failure, and key network transitions such as Internet egress and ingress points. Network diagrams usually have a life well past implementation, and are key diagrams used in audits and incident response.



##### State diagram

A state diagram is a specialized visualization the shows the different states that a flow or even an individual component can be in and how it transitions between states in response to conditions or events.



##### Flowchart

While it's not architecture diagram specifically, flowcharts are another way to bring clarity to a design. Flowcharts are often useful when they represent complex workflows or logic and can be effectively used to help refine requirements and can help drive implementation choices.


## Next steps

> [!div class="nextstepaction"]
> [Architect's checklist](checklist.md)
