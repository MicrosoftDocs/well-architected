---
title: Design review checklist for Operational Excellence
description: Learn how to incorporate an operational excellence approach in your workload for repeatable, reliable, and safe deployments of infrastructure and code.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: concept-article
ms.update-cycle: 1095-days  
---

# Design review checklist for Operational Excellence  

This checklist presents a set of recommendations to help you build a culture of operational excellence. Start with a DevOps approach to integrate specializations from multiple disciplines. This approach creates a rigorous design and development practice. This approach leads to repeatable, reliable, and safe deployments of infrastructure and code.

Prioritize human intervention in areas that benefit from it, and incorporate automation in other areas. Observability serves operational excellence by monitoring health events and also for validating the current workload design and implementation to inform future product development.

If you don't consider tradeoffs and recommendations for operational excellence, your workload might be at risk. Carefully consider the points covered in the following checklist to instill confidence in your design's success.

## Checklist

|&nbsp;|Code  |Recommendation  |
|-|-|-|
| &#9744; | [OE:01](devops-culture.md)  | **Define your standard practices to develop and operate your workload.** Foster a blameless culture that emphasizes continuous learning and prioritizes continuous improvement and optimization.|
| &#9744; | [OE:02](formalize-operations-tasks.md) | **Formalize the way you run routine, as needed, and emergency operational tasks**. Increase consistency and predictability by adopting industry-proven practices and approaches.   |
| &#9744; | [OE:03](formalize-development-practices.md) | **Formalize software ideation and planning processes.** Draw from established industry and organizational standards for team communication, requirements and design documentation, and software development processes.|
| &#9744; | [OE:04](tools-processes.md) | **Enhance software development and quality assurance** by implementing industry-standard practices. Ensure clear role definitions and consistent processes by standardizing tools, source control, design patterns, documentation, and style guides.|
| &#9744; | [OE:05](infrastructure-as-code-design.md) | **Use a standardized infrastructure as code (IaC) approach to prepare resources and configurations.** Use IaC to ensure consistent styles, modularization, and quality assurance. Prefer declarative over imperative approaches when practical. |
| &#9744; | [OE:06](workload-supply-chain.md) | **Build a workload supply chain that drives changes** through predictable, automated pipelines. Ensure these pipelines test and promote changes across all environments and quality gates. Incorporate comprehensive testing.|
| &#9744; | [OE:07](observability.md) <br> [OE:07](instrument-application.md)  | **Design and implement a monitoring system** to capture and expose telemetry, metrics, and logs from your infrastructure and code. Use this data to validate design choices and guide future design and business decisions.|
| &#9744; | [OE:08](incident-response.md) | Establish a clear, structured incident management process with defined roles, documented procedures, and architecture designed for rapid detection, diagnosis, and recovery. |
| &#9744; | [OE:09](automate-tasks.md) | **Automate tasks that are repetitive, procedural, and provide a clear return on investment.** Prefer off-the-shelf automation tools over custom solutions. Apply the Well-Architected Framework pillars to the design and implementation of all automation efforts.|
| &#9744; | [OE:10](enable-automation.md) | **Design and implement automation upfront** for tasks like lifecycle management, bootstrapping, and governance. Avoid retrofitting automation later. Simplify your design by adopting platform-native automation functionality.|
| &#9744; | [OE:11](safe-deployments.md) | **Clearly define your workload's safe deployment practices.** Focus on small, incremental releases with quality gates. Use modern deployment patterns and progressive exposure to manage risk. Plan for both routine and emergency deployments.|


## Next steps

We recommend that you review the Operational Excellence tradeoffs to explore other concepts.

> [!div class="nextstepaction"]
> [Operational Excellence tradeoffs](tradeoffs.md)
