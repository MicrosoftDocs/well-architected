---
title: Create architecture design diagrams
description: Learn about diagramming practices and types of architecture diagrams that you can create to communicate effectively.
author: ckittel
ms.author: chkittel
ms.date: 08/13/2025
ms.topic: concept-article
ms.update-cycle: 1095-days
---

# Architecture design diagrams

Architects often communicate through diagrams. Well‑designed visuals are a powerful tool that help implementers, security reviewers, and business stakeholders converge on a shared mental model, expose risks earlier, and reduce rework. To communicate with intention, an architect must select and often layer diagram types that match the message, audience, and lifecycle stage.

Ultimately, the choice of architecture diagram depends on what you're trying to convey and your audience's questions. Architects use multiple types of diagrams throughout design activities, requirements refinement, and stakeholder communication. Expect to maintain multiple diagrams across envisioning, design elaboration, threat modeling, implementation, operations, and governance.

## Diagramming practices

Effective diagrams convey substantial information without requiring extensive textual explanation. To avoid ambiguity and ensure clear communication, follow these recommendations:

**Use standard notations**. Use widely recognized symbols, icons, and presentation conventions to ensure good readability and consistent interpretation across different audiences.

**Avoid ambiguous lines**. Diagrams often show relationships between entities using lines. Be consistent in how you represent these relationships throughout your diagrams.

**Use directional arrows**. Lines without arrows make relationships unclear. Always use arrows, and when bi‑directional communication exists, either show two separate flows (preferred) or annotate a single arrow with request/response notes.

**Avoid bidirectional arrows**. Double arrows imply bidirectional dependencies, which can create confusion. Use single-ended arrows to represent the flow from the initiating component (client) to the dependency (server).

**Label everything clearly**. Provide clear, accurate, and meaningful labels for each icon, grouping container, and relationship. Label lines when relationships aren't immediately obvious from context.

**Maintain consistency**. Use standardized colors, casing, icons, icon sizes, line weights, line types, arrow heads, and border styles for similar elements. Apply the same taxonomy across every diagram in the solution set. Draw from existing organizational standards or taxonomies.

**Be accurate**. While diagrams are abstractions, don't sacrifice accuracy for unnecessary simplicity. For example, don't depict a PaaS service inside a subnet if it's actually accessed over a private endpoint. Inaccuracies in diagrams can lead to serious miscommunication and implementation delays or errors. Retire diagrams that no longer accurately answer an active stakeholder question.

**Include metadata**. Ensure each diagram contains metadata that provides essential context about its purpose, scope, and significance. Include elements such as title, description, last updated date, author, version, and external references. This information helps viewers understand the diagram's intent and freshness. After a diagram is first shared broadly, maintaining a linked change log helps returning viewers know what's changed.

**Use official icons and service names**. When representing specific technologies, always use the latest official icons and naming conventions. Don't stretch or recolor brand shapes arbitrarily. Don't substitute marketing logos for conceptual elements for example, using vendor logos for generic *API gateway* blocks.

For example, here are the icons for Microsoft services:

- [Azure architecture icons](/azure/architecture/icons/)
- [Microsoft 365 icons](/microsoft-365/solutions/architecture-icons-templates)
- [Microsoft Dynamics 365 icons](/dynamics365/get-started/icons)
- [Microsoft Entra ID architecture icons](/entra/architecture/architecture-icons)
- [Microsoft Fabric icons](/fabric/fundamentals/icons)
- [Microsoft Power Platform icons](/power-platform/guidance/icons)

**Provide a legend**. If you introduce border or line semantics, for example, solid is a synchronous call while dash is asynchronous, include a compact legend.

**Design for accessibility**. Ensure sufficient color contrast. Avoid relying solely on color to distinguish types, instead consistently pair color with pattern.

**Layer, don't overload**. Resist the urge to encode every subsystem, data classification, and runtime path in a single diagram. Provide progressive disclosure: a context diagram leads to a container diagram, which leads to a focused component or sequence diagram for a critical use case.

**Version control**. Store diagram source files in the same repository or documentation store as the workload's other versioned assets.

## Types of design diagrams

Workload architecture is complex and multidimensional. Different diagram types focus on specific aspects of the system, providing targeted levels of detail for each dimension. For example, flowcharts illustrate process flow, while entity-relationship diagrams show relationships between system components.

Using different diagram types enables comprehensive understanding across all architectural dimensions. This approach facilitates effective communication, problem-solving, and decision-making among diverse stakeholders with varying technical backgrounds and concerns. Your [architecture decision records](./architecture-decision-record.md) reference these diagrams to visualize the decision.

The types that follow include common cloud architecture communication artifacts plus several high‑value additions. The list of diagram types isn't exhaustive. In practice, many artifacts are hybrid or evolve. To visualize your workload, favor a minimal set of purposeful diagrams over creating every possible type. Start broad, progressively narrow, then apply scenario and cross‑cutting views.

### Context diagram

A context diagram presents the workload as a single black box in its external environment. It names the system, briefly states its purpose, and shows the external personas, upstream and downstream systems, and data sources or sinks that interact with it. Only the high level communication or integration paths appear; internal structure is deliberately omitted so the audience focuses on scope boundaries and dependencies, not implementation. Rely on generic shapes for external systems rather than product logos, and always include an explicit boundary so readers don't have to infer scope.

### High-level system or container diagram

A high-level system diagram provides a broad overview of an entire workload or a major subsection within a workload. It decomposes the workload into its main components, their relationships, and the general flow of data through the system. Arrows indicate the direction of interactions and dependencies.

Use this diagram after context alignment to expose macro structure, hosting models such as PaaS or self-managed, and external dependencies. These diagrams are excellent for establishing a common understanding among stakeholders before diving into deeper technical discussions. They're also valuable for executive and stakeholder communication where high-level understanding is more important than technical details.

### Block or functional diagram

A block diagram breaks down a workload into major functional capabilities using technology agnostic blocks, focusing on the functionality being performed rather than the specific component.

For example, a block diagram might reference an *order queue* or *messaging bus* instead of specifying a particular message-bus technology like Azure Service Bus or Apache Kafka. This abstraction level helps explain a system's structure, data flow, and processing flow without overwhelming the audience with implementation specifics, making it ideal for early domain modeling discussions and requirements gathering.

### Component diagram

A component diagram builds upon block diagrams by replacing generic functional blocks with specific technologies and integration points. It presents a detailed view that communicates the system's concrete technology and their relationships, such as client-server interactions. These diagrams serve as a visual bill of materials for the architecture, showing exactly which technologies will be implemented.

### Deployment diagram

A deployment diagram focuses on how infrastructure, commercial off-the-shelf (COTS) software, and custom code are deployed into the hosting environment. It shows the mapping between software components and the physical or virtual infrastructure that hosts them.

These diagrams are useful for DevOps planning, environment setup, and understanding the operational aspects of the architecture. They help teams visualize scaling boundaries, deployment units, infrastructure dependencies, and environments.

### Data-flow diagram (DFD)

A data-flow diagram (DFD) illustrates how data moves, transforms, is stored, and exits the system. Emphasize sources, sinks, transformation stages, classification (public, confidential, regulated), and whether movement is batch, streaming, or near real time. It helps with data lineage analysis, governance controls, and performance bottleneck identification.

Security threat modeling, such as the [STRIDE model](https://wikipedia.org/wiki/STRIDE_model), uses a specialized DFD. The diagram shows processes, data stores, external entities, trust boundaries, and data flows crossing those boundaries. It focuses on annotating flows with protocols and encryption, and highlights assets requiring protection. This illustration drives mitigation identification and security control validation.

### Sequence diagram

A sequence diagram depicts temporal ordering of interactions for a single use case or scenario. For example, *user places order*. It illustrates client-server relationships and clearly shows whether interactions are synchronous or asynchronous. These diagrams also highlight dependencies in communication patterns and help evaluate potential fault scenarios within component interactions. Sequence diagrams are particularly valuable for API design.

### User-flow or journey diagram

A user-flow diagram shows end‑to‑end steps a user or persona takes across interfaces and services. It visualizes the user journey through the system, showing how users and their data interact with various components and processes. These diagrams are helpful for clarifying functional requirements, validating user experience design, and ensuring that all user scenarios are properly addressed in the architecture. Annotate with performance or service-level expectations on critical flows.

### Entity-relationship diagram (ERD)

An entity-relationship diagram (ERD) represents logical or physical data model structure: entities (tables and collections), attributes, keys, and cardinalities. Use logical ERDs for domain alignment and physical ERDs for implementation detail (indexes, partitioning). Sometimes added details such as sharding ranges can be communicated in this diagram. These diagrams help developers understand data relationships and constraints before implementation begins.

### Network connectivity diagram

A network diagram illustrates the workload from a network infrastructure perspective, showing where components communicate across network boundaries. These diagrams visualize network segmentation, potential network failure points, and critical network transitions such as internet ingress and egress points. A workload can benefit from having one network diagram that focuses on east-west traffic and another network diagram for north-south traffic.

Network diagrams often have extended utility beyond the initial implementation phase. They're frequently referenced during security audits, compliance reviews, and incident response activities, making them valuable long term documentation assets.

### State diagram

A state diagram is a specialized visualization that shows possible states of a domain object, workflow, or subsystem. It includes the conditions or events that trigger transitions between states. For example, describing how an *order* progresses from draft, submitted, reviewed, fulfilled, to closed.

State diagrams help highlight potential concurrency concerns, transition handling, and compensating transaction needs. This helps reduce the likelihood of unanticipated state change behaviors in production.

### Flowchart and activity diagram

Flowcharts and activity diagrams bring clarity to complex workflows, decision logic, and business processes within your workload. They're useful for representing approval processes and conditional branching scenarios, which helps you document operational runbooks, business process automation, and incident response flows.

### Other specialized diagrams

| Type | When it adds distinct value | Focus |
|------|-----------------------------|-------|
| Availability & resilience map | During disaster recovery (DR) planning and service level objective (SLO) reviews | Show redundancy, failover paths, RPO/RTO annotations. |
| Compliance data residency map | Regulated workloads | Show data location, replication, classification, retention. |
| Identity & access flow diagram | Security and compliance reviews | Show authentication and authorization flows. Identify where token issuance occurs, where trust boundaries change, and where on-behalf-of flows occur. |

## Specification-based diagraming

Several open specifications exist that you can base diagrams on. Adopting one is a workload team decision; do so only if it adds required shared vocabulary to reduce ambiguity. If your current visual communication approach already works, avoid adding process weight just for formality. Even when you don't adopt a specification, selectively borrowing proven conventions (layering, cardinality notation, event labeling, legend patterns) can raise clarity and consistency across your diagram set.

Examples of diagraming and modeling specifications:

- [Business Process Model and Notation](https://www.omg.org/spec/BPMN)
- [C4 model](https://c4model.com)
- [Decision Model and Notation](https://www.omg.org/spec/DMN)
- [Unified Modeling Language (UML)](https://www.omg.org/spec/UML)

## Next steps

> [!div class="nextstepaction"]
> [Architect's checklist](checklist.md)

<!-- Updated: August 13, 2025 for a full review -->
