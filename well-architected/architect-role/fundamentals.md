---
title: Solution architect's fundamentals
description: Learn about the practice of being a Well-Architected architect.
author: ckittel
ms.author: chkittel
ms.date: 10/26/2023
ms.topic: conceptual
---

# Solution architect's fundamentals 

Every workload passes through a component and topology design process. This process is most intense at the inception of the workload, which includes designing for initial requirements and long term success of the workload. Architecture is also designed when the workload changes over time and functionality are added, changed, or removed. **Component and topology design is the primary function an architect**. Architects who focus on cloud-based and hybrid solutions are often known as _cloud solution architects_. 

In some organizations cloud solution architects can exist in a centralized capacity within an enterprise architecture group. Likewise, they can be tied to a specific workload. The function of an architect can be delivered by a dedicated role. Or in some cases delivered by trusted technical specialists, such as a workload engineering lead or distributed among a small group of senior engineers associated with the workload. 

Beside system design, architects usually have experience in other roles. They might have: 

- been developers and operations team members 
- experience working with customer support teams 
- an understanding of how a system is desired to be tested for quality assurance and user acceptance 
- been through disaster recovery drills or incident responses 
- been exposed to both incremental and large functional changes in workloads 
- interpreted specs and user story acceptance criteria 

While that isn’t an exhaustive list, those perspectives are an important aspect of what an architect brings to during design duties. The following sections highlight the guiding principles that architects should follow to be effective in their function.

## Have a decision-making framework 

The key aspect of design is making decisions. It should be done using a consistent process, approaching both initial and incremental design with rigor. 

**Identify and log decisions to be made**. Use learned experiences to help with decision identification. Log all decisions that need to be made. 

**Make a informed decision**. Consider limitations and constraints, tradeoffs, effort, reversibility, risk. Include supporting evidence from proof-of-concepts and technology documentation and guidance. 

**Document the decision in an architecture decision record (ADR)**. Document each decision along with justification.  

**Follow up on implementation**. All decisions must be communicated and implemented. Learn from  implementation for future decisions and look for areas that decisions weren't identified, and risk was introduced due to that. 

## Know standardized cloud design patterns 

Cloud design patterns are a fundamental building block of architecture. Cloud-based architecture and application design is often an exercise of pattern recognition. 

**Evaluate a workload’s functional and non-functional requirements to recognize patterns**. Look for opportunities to map your design to the use cases and include standard patterns in your design, which match the use case. 

## Be forward-thinking

Designing to achieve current requirements is a must, but it’s important for an architect to predict the workload's evolution. Incorporating change in an implemented system is relatively more expensive than changing the design before implementation. To design a system that's intended to last until its planned end of life, the workload must be designed with architectural flexibility in mind, or more specifically avoid design cliffs when they can be identified. 

**Growth model**. Know how this workload’s usage is predicted to grow or shrink over long periods of time. 

**Compliance changes**. Take proactive measures if the workload is expected to be under compliance requirements in the future. This can significantly reduce rework when following compliance becomes a requirement. 

**Regional expansion**. Consider future expansion of the workload into multiple regions. A design that's limited to a single region will need to be heavily refactored for multiregion deployment, and that can be a costly change. There's even more complexity if the workload design needs to accommodate multiple geographies with different compliance requirements. Factor in any reasonable prediction about regional expansion into your design. 

**Product roadmaps**. Don’t include components in your design, which are on the path to deprecation. Likewise, features currently in a preview state will be eventually released (or not). Being ahead of the curve by using preview features can be highly advantageous. The workload will be prepared to go to production soon after the feature released. Include preview features in your design only after doing careful risk analysis, and only ship features with a tolerated risk profile. 


## Design for supportability

Workloads should be designed with three key support perspectives:

**Cloud provider support**. The workload should operate within the supported configuration of your cloud provider to avoid disruptions when engaging platform support channels.

**Operational visibility**. The design should provide execution visibility for the workload operations team to prevent confusion during incident response.

**Customer support capabilities**. The design shouldn't only meet user needs but also facilitate customer support functions. A design that hinders the support team’s ability to investigate or assist customers is inadequate. 

## Maintain and enhance your skills

An architect’s expertise is often rooted in practical experience. It’s important for an architect to invest in expanding their skill set that's in line with evolving cloud ecosystem.  

**Training and certification**. Seek opportunities for training and certification in architect tracks offered by technology providers and their ecosystem. 

**Community participation**. Engage with peers through online and local architecture communities.

**Exploratory exercises**. Participate in organizationally sponsored hackathons or similar events to develop skills in unfamiliar areas. 

## Collaborate for success 

An architect should take advantage of the expertise of the cloud provider or implementation partner. Most  providers want your workload’s success on their platform and often provide services such as architecture design review sessions or consultative sessions with their cloud solution architects. Seek opportunities within your vendor relationships for their review and assistance. 

## Be methodical in your design approach

Architecture frameworks support an architect by offering workload perspectives and methodological approaches. The Azure Well-Architected Framework provides a comprehensive workload viewpoint. Architects can combine WAF with other architecture frameworks, such as The Open Group Architecture Framework (TOGAF) as necessary. Architects should use the principles, checklists, assessments, and reference materials provided by architecture frameworks to establish a process that's right-sized for the workload. 

Combining frameworks with personal techniques, such as mind-mapping, is recommended.

Architecture is about communication, not the end product. Make sure you’re optimizing for clear and intentional decision making, tradeoff acknowledgement, and clear communication in your established processes.

## Next steps

> [!div class="nextstepaction"]
> [Architect's checklist](checklist.md)