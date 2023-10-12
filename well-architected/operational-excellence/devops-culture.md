---
title: Recommendations for embracing DevOps culture
description: Learn how to embrace DevOps culture principles and practices in your workload team to strengthen the team.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for embracing DevOps culture

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:** 

|[OE:01](checklist.md)| Determine workload team members' specializations, and integrate them into a robust set of practices to design, develop, deploy, and operate your workload to specification. Team members must have clarity in decision-making and responsibilities, value continuous improvement and optimization, and adopt a blameless culture that incorporates continuous learning.| 
|---|---| 

This guide describes the best practices for embracing DevOps culture principles and practices in your workload team to strengthen the team. Embracing DevOps culture builds a foundation for your workload operations that focuses on shared ownership, mutual respect and valuing the quality of work the team produces. A high performing team needs to be able to thrive in the system that they are in and DevOps culture provides a template for allowing that.

## Key design strategies 

A workload that is operated according to the Well-Architected Framework recommended practices starts with a team that adopts a culture of cohesiveness, shared ownership and responsibility, and continuous learning and improvement. This type of culture is often referred to as a DevOps culture. While each individual team member brings their own expertise and might focus on specific areas of workload operation, the team as a whole should be able to independently manage day-to-day, ad-hoc and emergency tasks with support from outside teams as needed. The workload team also must be able to work within the overall organizational requirements and collaborate with other teams with a mindset valuing shared knowledge. The following recommendations can help your workload team use DevOps culture practices to optimize the operation of your workload and add value to the organization at large.

-   **The team should run with a code of ethics based on mutual respect.** Everyone on the team is there because they have expertise that brings value to the team, so recognizing that as a core tenet of the team culture allows conversations to start from a psychologically safe place. Individuals should feel that they can offer honest opinions about aspects of the workload operations and be treated respectfully, first and foremost.

-   **Mutual respect also fosters a blameless culture.** When issues arise, the workload team takes ownership collaboratively and finds ways to improve rather than assigning blame and fracturing the team cohesiveness.

-   **Workload teams value their work and thus take ownership and responsibility for the workload.** The workload team ultimately has end-to-end responsibility for the operation of the workload. While there might be services from other teams that are required for certain aspects of the workload operation, the workload team is responsible for collaborating with other teams and ensuring that all functions are successfully performed. Regardless of the extent that the workload team is involved in the activities that other teams perform in support of the workload, the workload team must consider every function that is involved in the support of the workload as their responsibility. This mindset helps reinforce a common sense of ownership.

-   **Clearly define team roles and responsibilities and decision-making responsibilities.** Decision-making should be as democratic as practical, but structure needs to be in place so that decisions can be made efficiently. When there are differing opinions about a situation and a decision needs to be made, someone must own the responsibility of making the decision based on the evidence that they are presented. Whether individuals agree with the final decision or not, the decision will affect the workload that they own, so it's important that everyone feels heard and valued throughout the decision-making process.

-   **Use enablement teams to the workload team's advantage.** Some organizations have enablement teams like platform teams, architecture review boards or cloud centers of excellence. These teams help guide workload teams and provide standards that all workload teams need to follow to ensure that there is consistency in design and processes across all teams. By following a mindset of continuous learning and improvement, the workload team should feel empowered to have open lines of communication with enablement teams and to work collaboratively with them in an effort to improve processes and share knowledge.

-   **Team members learn from each other to develop a cross-functional team.** While there should be distinct functions that individuals should perform based on their expertise, strive for a team in which everyone is a specialist in their function and a generalist in all other functions so that team members can support each other when assistance is needed. This helps the team have a common appreciation for each other's expertise and encourages growth. This also helps everyone appreciate and understand the complexity of the entire workload, further fostering an embrace of continuous learning and improvement.

-   **Understand business, regulatory and other requirements and integrate them into your practices.** Workload teams do not operate in a vacuum. Your team will be subject to requirements enforced by the business and depending on the industry and geographic regions you operate in, there might be multiple other requirements that are imposed on your operations practices as well. Ensure that all workload team members understand the requirements that they must follow and the ramifications of a failure to meet those requirements. Adapt your practices to proactively ensure that you are complying with requirements by integrating testing mechanisms specifically targeting required functions. Likewise, some degree of governance of your workload will be imposed by your organization, so embrace that governance and use the requirements your business standardizes as guardrails to ensure that your practices can be seen as examples of the right way to operate.

-   **Regularly review your standard operating procedures within the team to foster discussions about areas of improvement.** Avoid complacency and encourage innovative thinking by embracing a philosophy that all standard operating procedures should be continuously reviewed and improved throughout the workload lifecycle. Team members should be empowered to offer opinions on improvements at any time, but dedicating time to review procedures will give everyone space to think about areas for improvement and have focused discussions about their ideas.

-   **Embrace safe experimentation.** Give team members access to sandbox environments and ensure that time is built into sprints to allow time for experimentation. When experimentation proves that a new function or component would offer tangible benefits to the workload, have documented standards that define how new functionality is integrated into the workload, aligned with your [safe deployment practices](safe-deployments.md).

## Azure facilitation

Microsoft publishes extensive documentation about DevOps culture in a dedicated [DevOps resource center](/devops/what-is-devops) and in the [Cloud Adoption Framework](/azure/cloud-adoption-framework/ready/considerations/devops-principles-and-practices).

## Tradeoffs

Having strictly defined roles and responsibilities in your team could result in a level of discomfort for some team members in performing functions outside of their responsibility. You should have open and honest discussions with the team about team structure and be open to making adjustments when they can help team cohesion.

<!-- ## Related links -->


## Operational Excellence checklist  

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Operational Excellence checklist](checklist.md) 