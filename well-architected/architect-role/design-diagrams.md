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

## Diagramming practices 

Diagrams present substantial information without the need for textual explanation. Avoid ambiguity in diagrams. Here are some recommendations:

**Avoid ambiguous lines**. Diagrams often show relationships between entities represented as a line. Be consistent in how the lines are used. 

**Avoid lines without an arrow**.  It’s hard to know what the relationship is without direction.  All lines without arrows should be labeled to denote the relationship. 

**Avoid lines with double arrows**. Double arrows imply a bi-directional dependency. Prefer using a single ended arrow to represent the flow from client to server. 

## Types of design diagrams

Workload architecture is complex and multidimensional. Each dimension type focuses on a specific aspect of the system, providing level of detail that's specific to that dimension. For instance, flow charts illustrate process flow, while entity-relation diagrams depict relationships between system components. Having different types of diagrams allows for comprehensive understanding of different dimension. It helps in effective communication, problem-solving, and decision-making among stakeholders.

##### High-level system diagram

This diagram serves as a broad overview of a whole workload or even a broad overview of a subsection within. It includes the main components, their relationship to each other with arrows repenting the direction of interaction, and the rough order in which data flows through the system. These are good for reaching common understanding so that you can start deeper discussions or for general stakeholder communication. 

//example

##### Block diagram

This diagram breaks down a workload into its major functional blocks. The blocks are usually technology agnostic and instead refer to the functionality being performed instead of a specific component itself.  For example, it may reference a “messaging bus” instead of a specific message bus technology. This diagram can help explain a system’s structure and data & processing flow without distracting with fine-grain details. 

//example

##### Component diagram

This diagram works like the block diagram but replaces generic functionality blocks with specific technology. Its intent is a detailed view with the goal of communicating the system’s individual specific technology components and their relationships, such as client/server. They're a sort of visual bill-of-material for the scope of the diagram. 

//example

##### Deployment diagram

This diagram focuses on the deployment of infrastructure, COTS software, and custom code across the workload. It shows how the software and code is distributed across the various hosting infrastructure. 

//example

##### Data flow diagram (DFD)

This diagram illustrates the flow of data within a system, which is useful when modeling data-centric systems. It visualizes how data moves through a system. On a diagram like this, it’s a good idea to note if data is moved in batch or in real time to remove ambiguity. 

< example here> 

##### Sequence diagram

This type of diagram depicts the communication exchanges between different workload components over time. They illustrate client-server relationships and their synchronous or asynchronous nature. The also highlight dependencies in these exchanges and evaluating fault scenarios within them. 

//example

##### User flow diagram

This type of diagram focuses on a scoped interaction between workloads users or actors and the workload. It’s helpful for clarifying and visualizing functional requirements across various ways a user and its data interacts with the system. 

//example

##### Entity-relationship diagram (ERD)

An ERD is a modeling diagram that is used to represent the structure of a database or other storage systems. It shows the relationship between entities (such as tables) through industry standard attributes and association symbology. 

//example

##### Network diagram

A network diagram illustrates the solution from the perspective of the network that it runs upon or interacts with. These diagrams are useful in visualizing the workload’s network segmentation, network points of failure, and key network transitions such as Internet egress and ingress points. Network diagrams usually have a life well past implementation, and are key diagrams used in audits and incident response. 

//example

##### State diagram

A state diagram is a specialized visualization the shows the different states that a flow or even an individual component can be in and how it transitions between states in response to conditions or events. 

//example

##### Flowchart

While it’s not architecture diagram specifically, flowcharts are another way to bring clarity to a design. Flowcharts are often useful when they represent complex workflows or logic and can be effectively used to help refine requirements and can help drive implementation choices. 

//example

This list of diagrams isn’t exhaustive and sometimes diagrams end up as a composite of elements from multiple of these types. Ultimately, the choice of architecture diagram depends on what you’re trying to communicate and to what audience. Multiple types of diagrams are used throughout the design, requirements refining, and communication activities performed by an architect. Visualizations can do numerous heavy lifting in communications. 


## Next steps

> [!div class="nextstepaction"]
> [Architect's checklist](checklist.md)
