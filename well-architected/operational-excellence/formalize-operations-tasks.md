---
title: Architecture strategies for standardizing operations
description: Learn how to formalize routine and nonroutine tasks by using documentation, checklists, or automation.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: concept-article
---

# Architecture strategies for standardizing operations

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:**

|**OE:02**| Use standardization as a way to make routine, ad-hoc, and emergency operations consistent and predictable.|
|---|---|

Predictable processes lead to predictable outcomes. Teams must standardize how work is done and follow those patterns consistently. Where needed, they should implement automation intentionally to reduce variability.

This guide explains how teams should define and maintain standard processes across reliability, security, cost optimization, performance, and daily operations. The workload team owns these processes to manage and improve the workload, while aligning with organizational governance.


## Define actionable checklists for routine tasks

Break routine tasks into simple, discrete checklist items that are easy to follow and repeat. Each step should be clearly defined and actionable.

Consider a use case where the workload has dependency on an open-source library. Routine task is apply updates to get security patches, bug fixes, and functionality improvements. The checklist might include items like: 

> [!div class="checklist"]
>
> - Test the update in lower environments
> - Submit a change request for production
> - Deploy the update
> - Update documentation to reflect the new version

## Improvise checklists for emergency tasks

Emergency tasks differ by situation, but roles, responsibilities, and communication paths must always be clear. Teams should follow general standards and reusable checklists, even for new tasks.

For instance, an emergency fix might deploy a new resource. That deployment should use existing infrastructure-as-code standards and standard performance, security, and reliability tests. To accelerate the response, the checklist could be updated to include instructions on which low-risk, time-consuming items can be skipped with approval from authorized personnel.

For disaster recovery emergencies, follow the incident response plan exactly as defined to ensure fast, coordinated action. For details on this scenario, see [Create an effective incident management plan to manage disruptions](/azure/well-architected/design-guides/incident-management).

## Adopt industry-proven practices applicable to the workload

Use established practices to reduce the time your team spends creating processes and standards. Agile methods like Scrum, visualizing work with Kanban boards, and adopting a shift-left approach are proven to improve workflow consistency and efficiency. Mature organizations often rely on version-controlled SOPs, wikis, and operational manuals to enforce standards.

Rely on your team's experience to select practices that fit your workload lifecycle, and learn from other teams about approaches that have worked in similar contexts.

> :::image type="icon" source="../_images/ai.svg"::: **AI opportunity**: Adopting industry-standard practices can sometimes backfire. Teams may spend excessive effort on compliance, experience frustration, and delay delivering value while trying to follow best practices. AI can help detect when standards hinder rather than help. 
>
>   Consider implementing an AI-driven solution that tracks historical signals, such as time spent on standards, developer sentiment, and defect rates. Start with **LLMs** (large language models) to spot patterns, then train **predictive models** on historical data to anticipate when standards might impede workflow and take proactive action.

## Act early, stay proactive

Encourage workload teams to take full ownership of their workloads across security, reliability, and cost efficiency. Teams should proactively identify improvements and add them to their backlog rather than waiting for other teams to act.

This strategy is particularly important for reliability and security checks. Suppose the team doesn't perform exploratory testing or take ownership of early security checks, a vulnerability could remain undetected until the next monthly scan, potentially exposing the workload to risk.

## Take advantage of centralized standards and resources

Use established organizational requirements and cross-team standards instead of creating new processes from scratch. Adopt mandatory standards where they exist, and build your own only when necessary, aligning with other teams wherever possible. Document any differences between your team's processes and others' to simplify collaboration, especially at intersection points like security or compliance.

Pay attention to compliance requirements. Evaluate what extra steps might be needed in your procedure to fulfill those requirements. Understand applicable rules for your industry and region, embed them in your workflows, and regularly train the team to ensure consistent adherence.

## Implement automation, where practical

Use automation to help achieve consistency. Automate tasks that are repetitive and prone to human error to relieve management burden from your team. Look for opportunities to automate processes, like generating ITSM tickets, for example. For more information, see the [Recommendations for implementing automation](automate-tasks.md).

## Govern open-source use

Be intentional when adopting open-source tools. Define clear rules for when and how they can be used, ensuring alignment with organizational and compliance requirements. Decide whether workload team members can contribute to external open-source projects and whether in-house code can be shared with other teams.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Standards help maintain consistency but shouldn't be rigid. Follow procedures while leaving room for innovation so processes can evolve safely over time.

## Document standards and treat it as a living asset

Documentation should capture standard operating procedures and clearly define how work is done. Make documentation effective by creating by actionable checklists with references to relevant tools. 

Procedures must evolve as your workload and team evolve, and standards should be regularly reviewed and challenged to ensure they remain appropriate. Use templates and version control to maintain consistency and keep a record of updates. For example, store runbooks in a version-controlled repository and require scheduled reviews as part of the team's operational cadence. Include authorship and review dates, and highlight changes in procedures.

## Azure facilitation

Although there are no Azure products that directly facilitate the formalization of processes and procedures, Microsoft publishes lots of guidance on this topic. Use this guidance to understand industry-proven and recommended practices and to consider how to apply them to your workload.

The Well-Architected Framework also provides detailed guidance on the processes and procedures that should be codified to ensure that your workload and workload team run according to industry standards.

## Related links

- [Recommendations for implementing automation](automate-tasks.md)
- [Recommendations for enabling automation in your workload](enable-automation.md)
- [Recommendations for formalizing software development management practices](formalize-development-practices.md)

## Operational Excellence checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Operational Excellence checklist](checklist.md)
