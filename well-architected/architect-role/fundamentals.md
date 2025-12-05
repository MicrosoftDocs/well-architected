---
title: A Well-Architected Architect
description: Learn guiding principles that Well-Architected architects should follow to be effective in their function.
author: ckittel
ms.author: chkittel
ms.date: 12/04/2025
ms.topic: concept-article
ms.update-cycle: 1095-days
---

# A Well-Architected Architect: Deliverables, Responsibilities, and Guiding Principles

A _cloud solution architect_ is responsible for guiding the component and topology design of workloads, ensuring they meet both initial requirements and long-term business goals. This role covers the full lifecycle of a workload, adapting the architecture as functionality evolves or organizational needs change.

As an architect, your job is to take the information from stakeholders, understand the business context, and shape a design that balances technical, operational, and business considerations. Your experience in development, operations, QA, disaster recovery, or managing large and incremental changes helps you make informed decisions, anticipate challenges, and create a system that stands the test of time. Your designs consider not just the "happy path" but also operational realities such as observability, supportability, and resilience. You uncover design trade-offs and accepted risks to prevent uncommunicated technical debt and keep stakeholders informed.

This article outlines the common checklist of deliverables and the guiding principles that make them achievable. 

## Architect's responsibilities

|&nbsp;|Deliverable task|
|---|---|
|&#9744;|[**Align technical strategy with business requirements**](./design-business-requirements.md) by following a 5-step process to gather the right information, negotiate realistic outcomes and stay aligned with business goals.|
|&#9744;|[**Develop an architecture design specification**](./architecture-design-specification.md) accompanied by diagrams as a structured packet. Ensure the specification meets functional and nonfunctional requirements gathered in the prior task and includes provisions for routine, ad hoc, and emergency operations.|
|&#9744;|[**Create architecture design diagrams**](./design-diagrams.md) that illustrate all system design aspects, from a broad overview to detailed dimensions like network and identity.|
|&#9744;|[**Maintain an architecture decision record (ADR)**](./architecture-decision-record.md) that captures the context, consequences, and justifications for architectural decisions. Document accepted tradeoffs, rejected options, and known shortcomings.|
|&#9744;|[**Validate critical assumptions with proof of concepts (PoCs)**](./collaboration.md#use-a-proof-of-concept-poc). Before finalizing a design, validate high-risk or novel components with working code. This prevents theoretical designs from failing in practice.|
|&#9744;|[**Collaborate with the workload and platform team during implementation**](./collaboration.md) to provide clarity and recommendations about the implementation sequence. This collaboration helps you maximize learning and make improvements from the outset. Renegotiate requirements with stakeholders if needed.|
|&#9744;|[**Support modeling exercises**](./ongoing-support.md#support-modeling-exercises) that provide contextualized information about workload concerns. Cover costs, application health, and other areas.|
|&#9744;|[**Provide optimization recommendations**](./ongoing-support.md#share-potential-improvements) based on observations of usage patterns and changes in workload functionality or cloud provider offerings.|
|&#9744;|[**Participate in audit, compliance, and confidence reviews**](./ongoing-support.md#assist-in-reviews) to provide a valuable perspective to external parties authorized to conduct reviews.|
|&#9744;|[**Be a consultant during change reviews**](./ongoing-support.md#review-proposed-changes) to provide insight into the estimated cost of change and its feasibility.|

Delivering these outputs requires following the core principles of the architect's role. The sections below highlight the key principles that make them possible.

## Be clear on business requirements

Before a cloud architect can design a solution, they must understand the outcomes the system needs to deliver and the business constraints that shape every decision. This requires creating clarity and alignment with stakeholders, around budgets, timelines, compliance obligations, performance expectations, and growth plans. Without this foundation, the design process can spiral into endless revisions and lead to frustration and disappointment.

The architect's role is to ask probing questions, ground requests in reality, and steer conversations toward obtaining goals.

## Have a decision-making framework

Start by identifying key decisions upfront. Draw on your past experience to anticipate where choices will matter most, and make sure to log them clearly.

When it's time to decide, be deliberate. Weigh constraints, trade-offs, effort, reversibility, and risk. Tools like decision trees, feature comparisons, and benchmarks can guide you, but they don't replace your judgment. Combine this guidance with evidence from proofs of concept and testing to make informed choices.

Document every decision in an Architecture Decision Record (ADR), including your reasoning and justification.  Make sure your decisions are communicated, applied, and reflected in the system.

Finally, follow up on what the implementation might look like, through a proof of concept. Pay attention to outcomes and learn from them. Notice where decisions that weren't made introduced risk, and use those insights to guide future architecture work.

## Know cloud design patterns

Cloud design patterns should be at your fingertips. As an architect, you need to recognize them quickly and apply them instinctively.

When reviewing functional and nonfunctional requirements, map them to the right patterns. Use proven cloud design patterns to guide your workload, simplify decisions, reduce risk, and accelerate delivery. The more fluent you are with these patterns, the more naturally they shape effective designs. Well-Architected framework recommends these patterns for its pillars:

- [Architecture design patterns that support reliability](../reliability/design-patterns.md)
- [Architecture design patterns that support security](../security/design-patterns.md)
- [Architecture design patterns that support cost optimization](../cost-optimization/design-patterns.md)
- [Architecture design patterns that support operational excellence](../operational-excellence/design-patterns.md)
- [Architecture design patterns that support performance efficiency](../performance-efficiency/design-patterns.md)

## Be forward-thinking

Architect for change, not just current requirements. It's far cheaper to anticipate evolution in your design than to retrofit a live system. Focus on flexibility and avoid design cliffs that could block future growth. Common areas to be aware of:

- Anticipate how workload usage may increase or decrease over time.
- Plan ahead for potential future regulations that could affect your workload.
- Design for possible multi-region deployment and differing geographic requirements.
- Don't use deprecated components and carefully assess the risks of using preview features.

## Design for supportability

When designing workloads, consider support from three angles. Ensure the system operates within your cloud provider's supported configurations to avoid disruptions when engaging platform support. Provide operational visibility so your team can understand and respond to incidents quickly. Finally, design with customer support in mind, making it easy for support teams to investigate issues and assist users without friction.

## Stay hands-on and keep learning

Architecture is more than theory and your effectiveness comes from curiosity, effort, and real-world experience. Decision should be grounded in what you've learned by experimenting, solving problems, and working directly with technology. Explore unfamiliar tools, build prototypes, and learn by doing. Strengthen your foundation through training and certifications, and broaden your perspective by engaging with peers, joining communities, and participating in hands-on events like hackathons.

You can also self-assess by asking yourself questions such as:

- What architectural problem am I solving, and why does it matter to the business?
- What gaps or shortcomings in existing designs or documentation am I addressing?
- What evidence or analysis supports my proposed approach?
- What tradeoffs, risks, or design cliffs have I identified?
- Which pillars of the Well-Architected Framework are most relevant in this design, and why?

## Collaborate for success

As an architect, you can't work in isolation. Use the expertise available through your cloud provider. Most providers want your workload to succeed and offer valuable resources, architecture reviews, design consultations, and direct access to experienced solution architects. Make full use of these relationships to validate your design, uncover blind spots, and strengthen your overall approach.

In many organizations, workload teams rely on platform teams for shared infrastructure and services, similar to the Azure landing zone model. When your architecture depends on this partnership, work closely with the platform team to deliver a solution that meets longer term goals.

## Be methodical in your design approach

A disciplined approach leads to better architecture. Use established frameworks like the Azure Well-Architected Framework and TOGAF to guide your process; their principles, checklists, and assessments help you make consistent, well-informed decisions. Supplement them with resources such as decision trees, reference architectures, and benchmarking tools to further strengthen your design choices.

Define a repeatable process for every workload, supported by these frameworks and your own techniques, such as mind mapping or structured decision logs.

Architecture is as much about clarity as design. Be deliberate in how you make decisions, surface tradeoffs, and communicate your rationale so stakeholders understand the path you're taking.

## Next steps

> [!div class="nextstepaction"]
> [Azure Well-Architected Pillars](../pillars.md)
