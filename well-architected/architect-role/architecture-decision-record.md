---
title: Architecture decision record
description: Learn about the benefits of creating an architecture decision record in the design process to document decisions, justifications, and implications.
author: ckittel
ms.author: chkittel
ms.date: 10/26/2023
ms.topic: conceptual
---

# Architecture decision record

An architecture decision record (ADR) is one of the most important deliverables of a solution architect. This record documents architectural decisions that you make throughout the design process. It also provides context-specific justifications and implications for each decision.

The ADR documents all key decisions, including alternatives that you ruled out. It incorporates requirements and constraints into the documented effects of a decision.

## Implement an ADR

Start the ADR at the onset of a workload and maintain it throughout the workload's lifespan. An ADR should be started for brownfield workloads, and if the data is available, it should be retroactively generated based on known past decisions. The ADR serves as an append-only log. It extends beyond the initial design to include design aspects against future functional and nonfunctional requirements. This log should be readily available and stored with the workload's documentation.

In general, an architect can help a workload team achieve recommendations of Operational Excellence by helping to establish and maintain a document and asset repository. Architects help teams place all of their assets into the repository. They also encourage the teams' unified adoption of a single source of truth to be used for reference, audits, and incident response.

## Next steps

> [!div class="nextstepaction"]
> [Architect's checklist](checklist.md)

## Additional resources

For template examples, visit the [Architectural Decision Records GitHub organization](https://adr.github.io/).
