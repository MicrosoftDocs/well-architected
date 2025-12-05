---
title: Solution architect's checklist
description: Learn about the deliverables of a Well-Architected architect. See how to translate functional and nonfunctional requirements into cloud design patterns.
author: ckittel
ms.author: chkittel
ms.date: 11/24/2025
ms.topic: concept-article
ms.update-cycle: 1095-days
---

# Solution architect's checklist

The responsibility of an architect is to **deliver designs and plans**. An architect isn't the implementor of a workload. The architect **translates functional and nonfunctional requirements into cloud design patterns** and fit-for-purpose components, while constantly balancing competing constraints.

An architect designs a workload that is flexible enough to adapt to change but durable enough to withstand the rigors of production. This involves not just the "happy path" but also operational aspects like observability, supportability, and resilience against failure. Architects must operate within business, financial, compliance, and organizational constraints, often making difficult trade-offs.

Architects disclose design shortcomings, identifying compromises and accepted risks. This disclosure prevents uncommunicated technical debt and ensures stakeholders aren't surprised by limitations later.

Architecture frameworks, like the Azure Well-Architected Framework, give architects a holistic perspective on system design. Well-Architected Framework artifacts include **design principles**, **checklists**, and **recommendations**. Combine these artifacts with resources like **decision trees**, **reference architectures**, and **assessments** to make informed decisions.

## Checklist

|&nbsp;|Deliverable tasks|
|---|---|
|&#9744;|[**Develop an architecture design specification**](./architecture-design-specification.md) accompanied by diagrams as a structured packet. Ensure the specification meets functional and nonfunctional requirements and includes provisions for routine, ad hoc, and emergency operations.|
|&#9744;|[**Create architecture design diagrams**](./design-diagrams.md) that illustrate all system design aspects, from a broad overview to detailed dimensions like network and identity.|
|&#9744;|[**Maintain an architecture decision record (ADR)**](./architecture-decision-record.md) that captures the context, consequences, and justifications for architectural decisions. Document accepted trade-offs, rejected options, and known shortcomings.|
|&#9744;|[**Validate critical assumptions with proof of concepts (PoCs)**](./collaboration.md#use-a-proof-of-concept-poc). Before finalizing a design, validate high-risk or novel components with working code. This prevents theoretical designs from failing in practice.|
|&#9744;|[**Collaborate with the workload and platform team during implementation**](./collaboration.md) to provide clarity and recommendations about the implementation sequence. This collaboration helps you maximize learning and make improvements from the outset. Renegotiate requirements with stakeholders if needed.|
|&#9744;|[**Support modeling exercises**](./ongoing-support.md#support-modeling-exercises) that provide contextualized information about workload concerns. Cover costs, application health, and other areas.|
|&#9744;|[**Provide optimization recommendations**](./ongoing-support.md#share-potential-improvements) based on observations of usage patterns and changes in workload functionality or cloud provider offerings.|
|&#9744;|[**Participate in audit, compliance, and confidence reviews**](./ongoing-support.md#assist-in-reviews) to provide a valuable perspective to external parties authorized to conduct reviews.|
|&#9744;|[**Be a consultant during change reviews**](./ongoing-support.md#review-proposed-changes) to provide insight into the estimated cost of change and its feasibility.|

## Next steps

Get started with the Well-Architected Framework pillars, and familiarize yourself with their key concepts.

> [!div class="nextstepaction"]
> [Azure Well-Architected Framework pillars](../pillars.md)
