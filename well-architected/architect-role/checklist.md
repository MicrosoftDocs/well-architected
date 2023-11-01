---
title: Solution architect's checklist
description: Learn about the deliverables of a Well-Architected architect.
author: ckittel
ms.author: chkittel
ms.date: 11/15/2023
ms.topic: conceptual
---

# Solution architect's checklist

The responsibility of an architect is to **deliver designs and plans**. Keep in mind that an architect isn't the implementor of a workload. The architect **translates functional and non-functional requirements to cloud design patterns** and fit-for-purpose components. The architect also designs a workload that's flexible enough to adapt when needed, but also durable enough to weather the planned life of the functionality.

Also included in the design are the operational aspects of the workload, including observability and supportability, and accounting for undesirable situations such as disaster recovery. Finally, the design must be constrained by all business, financial, compliance, and organizational requirements.

Architecture frameworks, such as the Azure Well-Architected Framework, help architects get a holistic perspective on system design. Well-Architected Framework artifacts (**design principles**, **checklists**, **recommendations**) should be combined with other resources: **decision trees**, **reference architectures**, and **assessments** to make informed decisions to support the requirements of the workload.

## Checklist

|&nbsp;|Deliverable tasks|
|---|---|
|&#9744;|[**Develop an architecture design specification**](./architecture-design-specification.md)  accompanied by diagrams as a structured packet. The specification must meet the workload’s functional and nonfunctional requirements and include provisions for routine, ad-hoc, and emergency operations.|
|&#9744;|[**Create architecture design diagrams**](./design-diagrams.md) that illustrate system design from a broad overview to detailed dimensions, such as network, identity, and others.|
|&#9744;|[**Maintain an architecture decision record (ADR)**](./architecture-decision-record.md) with justifications for architectural decisions made during the design process.|
|&#9744;|[**Collaborate with the workload team during implementation**](./collaboration.md) to provide clarity and recommendations around the sequence of implementation to maximize learning and improve from the outset. Also renegotiate requirements with stakeholders, if needed.|
|&#9744;|[**Support modeling exercises**](./ongoing-support.md#support-modeling-exercises) that provide contextualized information about workload concern, whether that's cost, application health, security, and others.|
|&#9744;|[**Provide optimization recommendations**](./ongoing-support.md#share-potential-improvements) that based on the observed from usage patterns and changes in workload functionality or cloud provider changes.|
|&#9744;|[**Participate in audit, compliance, and confidence reviews**](./ongoing-support.md#assist-in-reviews) to provide valuable perspective to external parties who have the authority to conduct reviews.|
|&#9744;|[**Be a consultant during change reviews**](./ongoing-support.md#review-proposed-changes) to provide insight into estimated cost of change and its feasibility.|

## Next steps

Get started with the Well-Architected Framework pillars, and familiarize yourself with the key concepts.

> [!div class="nextstepaction"]
> [Azure Well-Architected Framework pillars](../pillars.md)
