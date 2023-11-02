---
title: Solution architect's checklist
description: Learn about the deliverables of a Well-Architected architect. See how to translate functional and nonfunctional requirements into cloud design patterns.
author: ckittel
ms.author: chkittel
ms.date: 11/15/2023
ms.topic: conceptual
---

# Solution architect's checklist

The responsibility of an architect is to **deliver designs and plans**. Keep in mind that an architect isn't the implementor of a workload. The architect **translates functional and nonfunctional requirements into cloud design patterns** and fit-for-purpose components. The architect also designs a workload that's flexible enough to adapt when needed, but durable enough to weather the planned life of the functionality.

Also included in the design are the operational aspects of the workload, including observability and supportability, and accounting for undesirable situations such as disaster recovery. Finally, the design must be constrained by all business, financial, compliance, and organizational requirements.

Architecture frameworks, such as the Azure Well-Architected Framework, help to give architects a holistic perspective on system design. The Well-Architected Framework artifacts include elements like **design principles**, **checklists**, and **recommendations**. To support the requirements of a workload, these artifacts should be combined with other resources, such as **decision trees**, **reference architectures**, and **assessments**, to make informed decisions.

## Checklist

|&nbsp;|Deliverable tasks|
|---|---|
|&#9744;|[**Develop an architecture design specification**](./architecture-design-specification.md) that's accompanied by diagrams as a structured packet. The specification must meet the workload's functional and nonfunctional requirements and include provisions for routine, ad hoc, and emergency operations.|
|&#9744;|[**Create architecture design diagrams**](./design-diagrams.md) that illustrate all aspects of system design, from a broad overview to detailed dimensions such as network and identity.|
|&#9744;|[**Maintain an architecture decision record (ADR)**](./architecture-decision-record.md) that contains justifications for architectural decisions that are made during the design process.|
|&#9744;|[**Collaborate with the workload team during implementation**](./collaboration.md) to provide clarity and recommendations about the implementation sequence. This collaboration helps you maximize learning and make improvements from the outset. Also renegotiate requirements with stakeholders, if needed.|
|&#9744;|[**Support modeling exercises**](./ongoing-support.md#support-modeling-exercises) that provide contextualized information about workload concerns. The contextualized information can cover costs, application health, and other areas.|
|&#9744;|[**Provide optimization recommendations**](./ongoing-support.md#share-potential-improvements) that are based on observations of usage patterns and changes in workload functionality or cloud provider changes.|
|&#9744;|[**Participate in audit, compliance, and confidence reviews**](./ongoing-support.md#assist-in-reviews) to provide a valuable perspective to external parties who have the authority to conduct reviews.|
|&#9744;|[**Be a consultant during change reviews**](./ongoing-support.md#review-proposed-changes) to provide insight into the estimated cost of change and its feasibility.|

## Next steps

Get started with the Well-Architected Framework pillars, and familiarize yourself with their key concepts.

> [!div class="nextstepaction"]
> [Azure Well-Architected Framework pillars](../pillars.md)
