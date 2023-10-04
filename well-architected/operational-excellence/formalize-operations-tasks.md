---
title: Recommendations for formalizing routine and nonroutine tasks
description: Learn how to incorporate an operational excellence approach in your workload for repeatable, reliable, and safe deployments of infrastructure and code.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for formalizing routine and nonroutine tasks

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:** 

|[OE:02](checklist.md)| Formalize the way you run routine, as needed, and emergency operational tasks by using documentation, checklists, or automation. Strive for consistency and predictability for team processes and deliverables by adopting industry-leading practices and approaches, such as a *shift left* approach. | 
|---|---| 

This guide describes the recommendations for formalizing routine and nonroutine tasks. Efficient and successful workload teams have consistent and predictable workload management practices. Efficiency and consistency are optimized through adopting industry-proven tools and practices and by automating processes when practical. By being consistent about how routine, ad-hoc and emergency tasks are handled, you can minimize the risk of being unprepared when issues arise and by taking a continuous improvement approach to workload management, you can increase your team's consistency and efficiency throughout the workload's lifecycle.

## Key design strategies

Being consistent in your processes makes your work quality predictable and having predictable work quality makes workload support smoother. So, to achieve consistency in your processes, you need to be intentional and explicit about how processes are run in standard patterns and mitigate potential areas of unpredictability through strategies like automation and adopting a shift-left approach.

Standardizing your processes can take many shapes, and all of the ways that you might approach standardization are out of scope for this guide, but general recommendations can include:

-   The processes that you standardize should cover all aspects of workload management: reliability, security, cost optimization, performance and operational processes. The workload team should have as much ownership of the processes as is required to maintain and continuously improve the workload under the overall governance of the organization.

-   The documentation you produce that captures your standard operating procedures dictates how things will always be done, but the documentation itself is never final. Procedures should evolve as your workload evolves and as your team evolves. Standards need to be regularly reviewed and challenged to ensure that they are the right standards for where you are as a team at any given point in time. Your documentation should be version controlled to ensure that there is a record of reviews and updates and to help ensure that reviews are conducted on regularly scheduled intervals.

-   Break down tasks, whether they are routine, ad-hoc or emergency tasks, into easily understood checklist items. An example of a routine task might be the process of applying an update to an open-source dependency. The workload may need to use an open-source library like an SDK to use a third-party message service and this SDK should be updated regularly for security patches, bug fixes and functionality improvements. When an update is determined to be needed, the workload team may have a checklist that includes items like testing the update in lower environments, creating a change management request to deploy the update in production, and documentation updates to a wiki or knowledge base to ensure that the correct version is reflected. Each checklist item should be tightly focused on a discrete task that is clearly defined.

-   Ad-hoc and emergency tasks will be scenario-specific, but operators should still clearly understand their roles and responsibilities and how to interact within the workload team and with other teams in the organization to efficiently work through those types of tasks. For example, an ad-hoc task might be deploying a new type of resource that has been approved for enhancing the workload functionality, like a machine learning service. There may not be a fully realized checklist for deploying this type of resource and testing it, but there should be general checklists for adding new resources into your infrastructure-as-code templates and standards covering infrastructure testing for performance, security and reliability at each stage of your promotion chain, for example. Likewise, your [emergency response plan](emergency-response.md) should clearly define roles and responsibilities and general processes and procedures, and these must be adhered to in any emergency situation to ensure that emergencies are handled in an orderly and efficient manner.

    Ad-hoc and emergency operations are also good opportunities to learn from to improve your standard operating procedures. Ask the workload team to reflect on ways that the operations could have gone smoother and determine whether an update to existing processes could be beneficial going forward.

-   Minimize the time your team spends on inventing processes and standards by adopting industry-proven practices. Following Agile practices with Scrum organizing your work through Kanban boards, and adopting a shift-left ethos are all examples of practices that have been developed over many years and have proven effective for organizations of any size. Likewise, many mature organizations use version-controlled standard operating procedures, wikis, new employee manuals, and operations manuals to enforce consistency. Rely on the experience your team has to help make decisions on which practices fit your workload lifecycle management, and learn from other teams about the standards that have been successfully implemented to understand how particular practices fit into your organizational structure.

    A shift-left ethos in this context means that workload teams should be empowered to look for measures that can improve the security, reliability, and cost efficiency of the workload and add those improvement measures to their backlog rather than shifting responsibilities to outside teams to perform later. For example, performing exploratory testing may uncover an area for improvement in security that might not have been discovered until it was exposed by security scans that may happen monthly or less frequently. The workload team should be encouraged to take ownership of the workload from all aspects of its lifecycle and contribute to its continuous improvement proactively rather than relying on other teams.

-   Incorporate organizational requirements and cross-cutting functions into your standard operating procedures. Your organization may have standards for some processes that you will have to adopt and you may be empowered to develop your own standards for other processes, so look for ways to incorporate required standards into your processes to operate smoothly. Likewise, the processes that you own will likely intersect with other teams' processes, so strive to align standards to the extent practicable.

    Be sure to document ways that the workload team's processes and those of other teams may diverge, to ensure that the workload team can better work with other teams when there are intersection points. For example, central security teams may use tools and procedures to than the workload team so knowing what the differences are can help when the teams need to work together.

-   Incorporate compliance requirements into your standard operating procedures. Depending on your industry and the regions in which you operate, you may have strict requirements about how tasks are performed and documented, so ensure that you understand and incorporate those requirements as you build your standards. Ensure that the workload team is trained on those requirements regularly.

-   Embrace automation to help achieve consistency. Automate tasks [Recommendations for implementing automation](automate-tasks.md) that are repetitive and prone to human error to relieve management burden from your team. Additionally, look for opportunities to automate processes that can be done more efficiently, like auto-generating ITSM tickets for example.

-   Be intentional about your approach to open-source adoption. Standardize the rules about when using open-source tools is allowable, ensuring that you are aligning with organizational and compliance requirements. Additionally, you may want to create standards about workload team members contributing to open-source projects and whether to open your in-house code to other development teams in the organization.

## Azure facilitation

While there are no Azure products that can directly facilitate the formalization of processes and procedures. Microsoft publishes considerable content related to guidance on this topic that you can use to understand industry-proven recommended practices and consider how they may be applied to your workload.

See the Cloud Adoption Framework [Azure landing zone design areas](/azure/cloud-adoption-framework/ready/landing-zone/design-areas) and [DevOps considerations](/azure/cloud-adoption-framework/ready/considerations/devops-principles-and-practices) documentation to explore guidance on many areas of standardizing processes and procedures.

Additionally, the Well-Architected Framework exists to provide detailed guidance on the processes and procedures that should be codified to ensure that your workload and workload team are run according to industry standards.

## Tradeoff

Codifying standard operating procedures can carry the risk of leading to stagnation or complacency. Standards must be followed, but they must also not be set in stone. Strive to find a balance between strict adherence and allowing for innovation so that your processes can evolve safely over time.

## Related links

- [Recommendations for implementing automation](automate-tasks.md)
- [Recommendations for enabling automation in your workload](enable-automation.md)
- [Recommendations for formalizing software development management practices](formalize-development-practices.md)
