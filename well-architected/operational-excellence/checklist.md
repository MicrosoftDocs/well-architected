---
title: Design review checklist for Operational Excellence
description: Use this checklist to build a culture of operational excellence.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Design review checklist for Operational Excellence  

This checklist presents a set of recommendations as a way for you to build a culture of operational excellence. This starts with a DevOps approach that integrates specializations from multiple disciplines to form a rigorous design and development practice which leads to repeatable, reliable, and safe deployments of infrastructure and code. The approach prioritizes human intelligence in areas that benefit from it and automates those that do not. Observability serves this approach not only as a tool for health events, but also validates the current workload design & implementation and informs future product development. If you haven't checked the boxes and considered the tradeoffs, the workload could be at risk. Careful consideration of all the points covered in the checklist will give you confidence in success. 

## Checklist

|&nbsp;|Code  |Recommendation  |
|-|-|-|
| &#9744; | [OE:&nsbp;01](devops-culture.md)  | Integrate workload team members' specializations into a robust set of practices that work together to design, develop, deploy, and operate the workload to specification. Team members must have clarity in decision making & responsibilities, value continuous improvement & optimization, and espouse a blameless culture through continuous learning.  |
| &#9744; | [OE:&nbsp;02](automate-tasks.md) | Formalize the execution of all routine, ad-hoc, and emergency operational tasks using documentation, checklists, or automation. Strive for consistency and predictability in team processes and deliverables through adopting industry-leading practices and approaches, such as adopting a “shift-left” ethos.   |
| &#9744; | OE:&nbsp;03 | Formalize the software ideation and planning process, drawing from established industry and organizational standards. Use a common, prioritized backlog and sufficiently detailed specifications. Drive continuous improvements in the planning process based on outcomes.  |
| &#9744; | OE:&nbsp;04 | Optimize the software development and quality assurance process by following industry proven practices for development and testing. Standardize across facets such as tooling, source control, application design patterns, documentation, and style guides for unambiguous execution of role.  |
| &#9744; | [OE:&nbsp;05](infrastructure-as-code-design.md) | Resources and their configuration should be provisioned through a standardized Infrastructure as Code (IaC) approach. Like other code, IaC should be similarly handled with consistent styles, appropriate modularization, and quality assurance. Prefer a declarative approach when available.  |
| &#9744; | [OE:&nbsp;06](workload-supply-chain.md) | Build a workload supply chain that drives all proposed changes through predictable, automated pipelines designed to test and promote those changes across environments. Optimize that supply chain to stay reliable, secure, cost effective, and performant.  |
| &#9744; | [OE:&nbsp;07](observability.md) | Design and implement an observability platform that is used to both confirm design choices and inform future design & business decisions. This platform captures and exposes operational telemetry, metrics, and logs emitted from across the breadth of the workload's infrastructure and code.  |
| &#9744; | [OE:&nbsp;08](emergency-response.md) | Build an effective emergency operations practice. This includes ensuring the workload emits meaningful health signals across infrastructure and code. That data is collected and used to raise actionable alerts to enact emergency response and is consumed through readily available dashboards and queries. Clearly define all human responsibilities such as on-call rotations, incident management, emergency resource access, and running post-mortems.  |
| &#9744; | OE:&nbsp;09 | Automate all tasks that do not benefit from the insight and adaptability of human intervention, are highly procedural, and have a shelf-life that will yield a return on the automation investment. Prefer off-the-shelf software for automation vs custom implementations where available. Treat all automation as workload components, applying Well-Architected pillars to their design and implementation.  |
| &#9744; | OE:&nbsp;10 | Design and implement the workload with the expectation of using automation for items such as lifecycle concerns, bootstrapping, and applying governance & compliance guardrails up front instead of retrofitting automation later. Prefer the use of automation features provided natively by your platform.  |
| &#9744; | OE:&nbsp;11 | Clearly define your workload's safe deployment practices. It should emphasis the ideals of small, incremental, quality-gated release methods, use modern deployment patterns, and progressive exposure techniques to control risk. It must account for both routine deployments and emergency (hotfix) deployments.   |
| &#9744; | [OE:&nbsp;12](mitigation-strategy.md)  | Have a deployment failure mitigation strategy defined to address unexpected mid-rollout issues with rapid recovery. Combine multiple approaches such as rollback, feature disablement, or using your deployment pattern's native capabilities.   |

## Next steps

We recommend that you review the Operational Excellence checklist to explore other concepts. 

> [!div class="nextstepaction"] 
> [Operational Excellence checklist](checklist.md) 