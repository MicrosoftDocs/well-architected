---
title: Architecture decision record
description: Learn about the benefits of creating an architecture decision record in the design process to document decisions, justifications, and implications.
author: ckittel
ms.author: chkittel
ms.date: 10/10/2024
ms.topic: conceptual
---

# Architecture decision record

An architecture decision record (ADR) is one of the most important deliverables of a solution architect. This record documents architectural decisions that you make throughout the design process. It also provides context-specific justifications and implications for each decision.

The ADR documents all key decisions, including alternatives that you ruled out, for [architecturally significant requirements](https://wikipedia.org/wiki/Architecturally_significant_requirements). The log incorporates requirements and constraints into the documented effects of a decision.

## Implement an ADR

Start the ADR at the onset of a workload and maintain it throughout the workload's lifespan. An ADR should be started for brownfield workloads, and if the data is available, it should be retroactively generated based on known past decisions. The ADR serves as an append-only log. This record extends beyond the initial design to include design aspects against future functional and nonfunctional requirements.

### Suggested characteristics of an individual record

- Be consistent with the anatomy of every record. Design a record template and consistently follow it.

  A record should include consistent elements such as:

  - Problem statement with context
  - Options considered
  - Decision outcome
    - Include important tradeoffs made with this decision
    - Record the confidence level of the decision. Sometimes an architecturally significant decision is made with relatively low confidence. Documenting that low confidence status could prove useful for future reconsideration decisions.

- Break one decision into multiple if an architectural decision is going to result in multiple phases, such as short-term, mid-term, long-term approaches. Log each phase as its own decision record.

- Avoid hiding consequences of decisions intentionally or accidentally.

- Keep records pithy, assertive, on-topic, and factual.

- Avoid making decision records design guides. If more justification or design ideation is available, provide a link to a document as supplemental material, but the decision must be clear and stand alone without that material.

## Workload documentation repository

This log should be readily available and stored openly with the workload's documentation.

An architect can help a workload team achieve recommendations of Operational Excellence by helping to establish and maintain a document and asset repository. Architects help teams place all of their assets into the repository, such as this decision log. They also encourage the teams' unified adoption of a single source of truth to be used for reference, audits, and incident response.

## Next steps

> [!div class="nextstepaction"]
> [Architect's checklist](checklist.md)

## More resources

For template examples, visit the [Architectural Decision Records GitHub organization](https://adr.github.io/).
