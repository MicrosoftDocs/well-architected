---
title: Recommendations for formalizing routine and nonroutine tasks
description: Learn how to formalize routine and nonroutine tasks by using documentation, checklists, or automation.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for formalizing routine and nonroutine tasks

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:**

|[OE:02](checklist.md)| Formalize the way you run routine, as needed, and emergency operational tasks by using documentation, checklists, or automation. Strive for consistency and predictability for team processes and deliverables by adopting industry-leading practices and approaches, such as a shift left approach. |
|---|---|

This guide describes the recommendations for formalizing routine and nonroutine tasks. Efficient and successful workload teams have consistent and predictable workload management practices. Optimize efficiency and consistency by adopting industry-proven tools and practices and by automating processes when it's practical. By being consistent about how routine, improvised, and emergency tasks are handled, you minimize the risk of being unprepared when issues arise. By taking a continuous improvement approach to workload management, you increase your team's consistency and efficiency throughout the workload's lifecycle.

## Key design strategies

Consistent processes make your work quality predictable, and predictable work quality makes workload support smoother. To achieve consistency in your processes, you need to be intentional and explicit about how you run processes in standard patterns. Use strategies like automation and shift-left approaches to minimize potential areas of unpredictability.

Process standardization takes many shapes. Describing every way that you might approach standardization is out of scope for this guide, but some general recommendations include:

- The processes that you standardize should cover all aspects of workload management: reliability, security, cost optimization, performance, and operational processes. The workload team should have as much ownership of the processes as they need to maintain and continuously improve the workload under the overall governance of the organization.

- The documentation that you produce captures your standard operating procedures and dictates how things are done, but the documentation is never final. Procedures should evolve as your workload and your team evolve. Regularly review and challenge standards to ensure that they're the right standards for your team right now. Your documentation should be version controlled to ensure that there's a record of reviews and updates and to ensure that the team conducts reviews at regularly scheduled intervals.

- Break down routine, improvised, and emergency tasks into checklist items that are easy to understand. An example of a routine task is the process of applying an update to an open-source dependency. The workload might need to use an open-source library like an SDK to use a third-party message service, and this SDK should be updated regularly for security patches, bug fixes, and functionality improvements.

   When you determine that you need an update, the workload team might have a checklist that includes items like testing the update in lower environments, creating a change management request to deploy the update in production, and updating documentation, such as a wiki or knowledge base, to ensure that they reflect the correct version. Focus each checklist item on a discrete task that is clearly defined.

- Improvised and emergency tasks are scenario-specific, but operators should still clearly understand their roles and responsibilities. They need to know how to interact with the workload team and with other teams in the organization to efficiently work through those types of tasks.

   For example, an improvised task might deploy a new type of resource, such as a machine learning service, that has been approved for enhancing the workload functionality. There might not be a fully realized checklist for the deployment and testing of this type of resource. However, there should be general checklists for adding new resources to your infrastructure as code templates and standards that cover infrastructure testing for performance, security, and reliability at each stage of your promotion chain.

   Likewise, your [emergency response plan](emergency-response.md) should clearly define roles and responsibilities and general processes and procedures. You must adhere to this plan in emergency situations to ensure that you handle them efficiently.

   Improvised and emergency operations are also good opportunities to learn how to improve your standard operating procedures. Ask the workload team to reflect on ways that the operations could have gone smoother, and determine whether an update to existing processes could be beneficial going forward.

- Adopt industry-proven practices to minimize the time your team spends on inventing processes and standards. Following Agile practices with Scrum, organizing your work through Kanban boards, and adopting a shift-left ethos are all examples of practices that have been developed over many years and have been proven effective for organizations of any size. Many mature organizations use version-controlled standard operating procedures, wikis, new employee manuals, and operations manuals to enforce consistency.

   Rely on your team's experience to decide which practices fit your workload lifecycle management. Learn from other teams about the standards that they've successfully implemented to understand how particular practices fit into your organizational structure.

   In this context, a shift-left ethos means that workload teams should be empowered to look for measures that can improve the security, reliability, and cost efficiency of the workload. Then, they add those improvement measures to their own backlog, rather than shifting the responsibility to outside teams.

   For example, an exploratory test might uncover an area for improvement in security that might not have been discovered until it was exposed by security scans, which happen monthly or even less frequently. Encourage the workload team to take ownership of the workload in all aspects of its lifecycle and to contribute to its continuous improvement proactively, rather than relying on other teams.

- Incorporate organizational requirements and cross-cutting functions into your standard operating procedures. Your organization might have standards for some processes that you should adopt. However, you might also be empowered to develop your own standards for other processes, so look for ways to incorporate required standards into your processes to operate smoothly. The processes that you own are likely to intersect with other teams' processes, so strive to align standards to a practicable extent.

   Document where the workload team's and other team's processes diverge to ensure that the workload team can work better with other teams when there are intersection points. Central security teams might use different tools and procedures than the workload team, and the teams can collaborate more easily if they're aware of those differences.

- Incorporate compliance requirements into your standard operating procedures. Depending on your industry and the regions in which you operate, there might be strict requirements about how to perform and document tasks. Make sure that you understand and incorporate those requirements while you build your standards. Regularly train the workload team on those requirements.

- Embrace automation to help achieve consistency. Automate tasks that are repetitive and prone to human error to relieve management burden from your team. Look for opportunities to automate processes, like generating ITSM tickets, for example. For more information, see the [Recommendations for implementing automation](automate-tasks.md).

- Be intentional about your approach to open-source adoption. Standardize the rules about when the use of open-source tools is allowed, and ensure that you align with organizational and compliance requirements. You might want to create standards about workload team members' contributions to open-source projects and decide whether to open your in-house code to other development teams in the organization.

## Azure facilitation

Although there are no Azure products that directly facilitate the formalization of processes and procedures, Microsoft publishes lots of guidance-related content on this topic. Use that guidance to understand industry-proven and recommended practices and to consider how to apply them to your workload.

To explore the guidance on many areas of standardizing processes and procedures, see the Cloud Adoption Framework [Azure landing zone design areas](/azure/cloud-adoption-framework/ready/landing-zone/design-areas) and [DevOps considerations](/azure/cloud-adoption-framework/ready/considerations/devops-principles-and-practices) documentation.

The Well-Architected Framework also provides detailed guidance on the processes and procedures that should be codified to ensure that your workload and workload team run according to industry standards.

## Tradeoffs

Codifying standard operating procedures can carry the risk of leading to stagnation or complacency. Standards must be followed, but they must also not be set in stone. Strive to find a balance between strict adherence and allowance for innovation so that your processes can evolve safely over time.

## Related links

- [Recommendations for implementing automation](automate-tasks.md)
- [Recommendations for enabling automation in your workload](enable-automation.md)
- [Recommendations for formalizing software development management practices](formalize-development-practices.md)

## Operational Excellence checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Operational Excellence checklist](checklist.md)
