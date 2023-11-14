---
title: Solution architect's fundamentals
description: Learn guiding principles that Well-Architected architects should follow to be effective in their function.
author: ckittel
ms.author: chkittel
ms.date: 11/15/2023
ms.topic: conceptual
---

# Solution architect's fundamentals

Every workload passes through a component and topology design process. This process is most intense at the inception of the workload, which includes designing for initial requirements and long-term success of the workload. Architecture is also designed when the workload changes over time and the organization adds, changes, or removes functionality.

Component and topology design is the primary function of an architect. Architects who focus on cloud-based and hybrid solutions are often called _cloud solution architects_. In some organizations, cloud solution architects exist in a centralized capacity within an enterprise architecture group. They can also focus on a specific workload.

A dedicated role can deliver the function of an architect. In some cases, trusted technical specialists (such as a workload engineering lead) can deliver the function of an architect. Or an organization might distribute the function among a small group of senior engineers who are associated with the workload.

Architects usually have experience in roles beyond system design. They might have:

- Been developers and operations team members.
- Worked with customer support teams.
- Developed an understanding of how a system is tested for quality assurance and user acceptance.
- Been through disaster recovery drills or incident responses.
- Been exposed to both incremental and large functional changes in workloads.
- Interpreted specifications and user-acceptance criteria.

Although the preceding list isn't exhaustive, those perspectives are an important aspect of what an architect brings to design duties. The Azure Well-Architected Framework assumes that these practices are in place for the most effective use of the guidance.

The following sections highlight the guiding principles that architects should follow to be effective in their function.

## Have a decision-making framework

A key aspect of design is using a consistent process to make decisions. An architect should approach both initial and incremental design with rigor.

**Identify expected decisions**. Use learned experiences to help with decision identification. Log all decisions that you plan to make.

**Make informed decisions**. Consider limitations, constraints, tradeoffs, effort, reversibility, and risk. Include supporting evidence from proofs of concept, along with technology documentation and guidance.

**Document decisions in an architecture decision record (ADR)**. Document the justification along with each decision.

**Follow up on implementation**. Communicate and implement all decisions. Learn from the implementation to help guide future decisions. Look for areas where a failure to identify decisions introduced risk.

## Know cloud design patterns

Cloud design patterns are a fundamental building block of architecture. Cloud-based architecture and application design are often an exercise in pattern recognition.

**Evaluate a workload's functional and nonfunctional requirements to recognize patterns**. Look for opportunities to map your design to use cases via standardized patterns.

## Be forward-thinking

Designing to achieve current requirements is a must, but it's important for an architect to predict the workload's evolution. Incorporating change in an implemented system is more expensive than changing the design before implementation.

To design a system that will last until its planned end of life, you must design the workload with architectural flexibility in mind. Avoid design cliffs when you can identify them.

**Growth model**. Predict how the workload's usage will grow or shrink over time.

**Compliance changes**. Take proactive measures if you expect the workload to be under compliance requirements in the future. This approach can reduce rework when following compliance becomes a requirement.

**Regional expansion**. Consider future expansion of the workload into multiple regions. A design that's limited to a single region will need to be heavily refactored for multiple-region deployment, and that can be a costly change. There's even more complexity if the workload design needs to accommodate multiple geographies with different compliance requirements. Make sure that your design factors in any reasonable prediction about regional expansion.

**Product roadmaps**. In your design, don't include components that are on the path to deprecation. Likewise, be careful when you include features in your design that are currently in a preview state. They might be released, but they might also be canceled. Being ahead of the curve by using preview features can be highly advantageous. Soon after the feature is released, the workload is prepared to go to production. But include preview features in your design only after you do a careful risk analysis. Ship only features that have a tolerated risk profile.

## Design for supportability

Design workloads with three key support perspectives:

**Cloud provider support**. The workload should operate within the supported configuration of your cloud provider to avoid disruptions when you're engaging platform support channels.

**Operational visibility**. The design should provide execution visibility for the workload operations team to prevent confusion during incident response.

**Customer support capabilities**. The design should meet user needs but also facilitate customer support functions. A design that hinders the support team's ability to investigate or to assist customers is inadequate.

## Maintain and enhance your skills

An architect's expertise is often rooted in practical experience. It's important to invest in expanding your skill set to keep up with the evolving cloud ecosystem.

**Education**. Seek opportunities for training and certification that technology providers offer for architects.

**Community participation**. Engage with peers through online and local architecture communities.

**Exploratory exercises**. Participate in organizationally sponsored hackathons or similar events to develop skills in unfamiliar areas.

## Collaborate for success

An architect should take advantage of the expertise of the cloud provider or implementation partner. Most  providers want your workload to succeed on their platform, and they often provide services such as architecture design review sessions or consultative sessions with their cloud solution architects. Seek opportunities for review and assistance within your vendor relationships.

## Be methodical in your design approach

Architecture frameworks support an architect by offering workload perspectives and methodological approaches. The Well-Architected Framework provides a comprehensive workload viewpoint. Architects can combine the Well-Architected Framework with other architecture frameworks, such as The Open Group Architecture Framework (TOGAF).

Use the principles, checklists, assessments, and reference materials in architecture frameworks to establish a process that fits the workload. Combine frameworks with personal techniques, such as mind mapping.

Architecture is about communication as much as it's about the end product. Make sure that you're optimizing for intentional decision-making, tradeoff acknowledgment, and clear communication in your established processes.

## Next steps

> [!div class="nextstepaction"]
> [Architect's checklist](checklist.md)
