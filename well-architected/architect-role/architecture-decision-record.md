---
title: Maintain an architecture decision record (ADR) 
description: Learn about the benefits of creating an architecture decision record in the design process to document decisions, justifications, and implications.
author: ckittel
ms.author: chkittel
ms.date: 04/10/2026
ms.topic: concept-article
ms.update-cycle: 1095-days
---

# Maintain an architecture decision record (ADR)

An architecture decision record (ADR) is one of the most important deliverables of a solution architect. Your architecture is the accumulation of its decisions, so the ADR is effectively a record of how and why the system came to be its current shape.

The ADR documents all key decisions, including alternatives that you ruled out, for [architecturally significant requirements](https://wikipedia.org/wiki/Architecturally_significant_requirements). Only include choices that affect the system's structure, key quality attributes, or are difficult to reverse. Each entry captures the context, justifications, and implications of a decision, incorporating requirements and constraints into the documented effects of the decision.

## Implement an ADR

Start the ADR at the onset of a workload and maintain it throughout the workload's lifespan. An ADR should be started for brownfield workloads, and if the data is available, it should be retroactively generated based on known past decisions. The ADR serves as an append-only log. Don't go back and edit accepted records. If a decision changes, write a new record that supersedes the original and link the two together. This approach preserves the history of your thinking and makes it clear when and why the direction shifted. The record extends beyond the initial design to include design aspects against future functional and nonfunctional requirements.

### Suggested characteristics of an individual record

- Be consistent with the anatomy of every record. Design a record template and consistently follow it.

  A record should include consistent elements such as:

  - Problem statement with context
  - Options considered
  - Decision outcome
    - Include important tradeoffs made with this decision
    - Record the confidence level of the decision. Sometimes an architecturally significant decision is made with relatively low confidence. Documenting that low confidence status could prove useful for future reconsideration decisions.
  - Status, such as *Proposed*, *Accepted*, or *Superseded*. Tracking status makes the current state of each decision clear, especially as the number of decisions grows.

- Break one decision into multiple if an architectural decision is going to result in multiple phases, such as short-term, mid-term, long-term approaches. Log each phase as its own decision record.

- Avoid hiding consequences of decisions intentionally or accidentally.

- Always include context and rationale. A record without justification loses its value over time as stakeholders can't evaluate whether the decision still applies when circumstances change.

- Keep records pithy, assertive, on-topic, and factual.

- Avoid making decision records design guides. If more justification or design ideation is available, provide a link to a document as supplemental material, but the decision must be clear and stand alone without that material.

## Workload documentation repository

This log should be readily available and stored openly with the workload's documentation. A decision that's made but never recorded will likely be forgotten, leading to repeated debates or later changes that unknowingly contradict the original intent.

An architect can help a workload team achieve recommendations of Operational Excellence by helping to establish and maintain a document and asset repository. Architects help teams place all of their assets into the repository, such as this decision log. They also encourage the teams' unified adoption of a single source of truth to be used for reference, audits, and incident response.

## Next steps

> [!div class="nextstepaction"]
> [Architect's checklist](checklist.md)

## More resources

For template examples, visit the [Architectural Decision Records GitHub organization](https://adr.github.io/).
