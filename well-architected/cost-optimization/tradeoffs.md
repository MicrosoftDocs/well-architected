---
title: Cost Optimization tradeoffs
description: Learn about tradeoffs that you might encounter when you design workload architectures and operations for cost optimization.
author: ckittel
ms.author: chkittel
ms.date: 10/12/2023
ms.topic: conceptual
---

# Cost Optimization tradeoffs

Several factors come into play when designing a workload to maximize return on investment (ROI) under financial constraints. You first need clearly defined functional and non-functional requirements. A work and effort prioritization strategy are essential. The foundation is a team with a strong sense of financial responsibility. They should have a good understanding of available technology and their billing models.

After you understand the ROI of a workload, you can start improving it. To improve the ROI, you should consider how aligning to the [Cost Optimization design principles](./principles.md) and recommendations in the [Design review checklist for Cost Optimization](./checklist.md) might influence the efforts in other pillars. For cost optimization, it's important to avoid the trap of focusing on a cheaper solution. Choices that focus only on minimizing spend can increase the risk of undermining your workload's business goals and reputation. This article lists example tradeoffs that a workload team might encounter when considering the target setting, design, and operations for cost optimization.

## Cost Optimization tradeoffs with Reliability

The cost of a service disruption must be measured against the cost to prevent or recover from one. If the cost of disruptions exceeds the cost of reliability design, then you should invest more to prevent or mitigate disruptions. Conversely, the cost of the reliability efforts might be more than the cost of a disruption, including factors such as compliance requirements and reputation. Only then, should you consider strategic divestment in reliability design.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Reduced resiliency.** A workload employs resiliency measures to attempt to avoid and withstand specific types and quantities of malfunction.

- Cost-saving measures might underprovision a component or overconstrain its scaling, making it more likely to fail with sudden spikes in demand.

- Consolidating workload resources (*increasing density*) for cost optimization makes individual component more likely to fail during spikes in demand and maintenance operations (such as updates).

- Removing components that support *resiliency design patterns*, such as a message bus, and creating a direct dependency reduces self-preservation capabilities.

- Cost reductions made by *reducing redundancy* can cause a workload to be able to handle fewer concurrent malfunctions.

- Selecting *budget SKUs* may limit the maximum service level objective (SLO) the workload can reach.

- Setting hard spending limits can prevent a workload from scaling to meet legitimate demand.

- Without reliability testing tools or tests, the reliability of the workload is unknown and less likely to reach its reliability targets.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Limited recovery strategy.** A workload that is reliable has a tested incident response and recovery plan for disaster scenarios.

- Less *testing or drilling* of the workload's disaster recovery plan increases might affect the speed and effectiveness of recovery operations.

- Taking or retaining fewer backups decreases possible recovery points and increases the chance of losing data.

- Selecting a lower-cost support contract may increase workload recovery time due to potential delays in technical assistance.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Increased complexity.** The workload that uses straight forward approaches and avoids unnecessary or over-engineered complexity is, generally, the easier to manage reliability expectations against.

- Using cost-optimization cloud patterns can add new components, such as content delivery network (CDN), or shift duties to edge and client devices that a workload must provide reliability targets for.

- *Event-based scaling* can be more complex to tune and validate than resource-based scaling.

- Reducing *data volume and tiering data* through data lifecycle actions, possibly in conjunction with implementing aggregated data points before the lifecycle event, introduces new reliability factors to consider in the workload.

## Cost Optimization tradeoffs with Security

The cost of a compromise to confidentiality, integrity, and availability within a workload must always be balanced against the cost of the effort to prevent that compromise. The impact of a security incident can have a very wide range of reputation, legal, and monetary impact. Investing in security is a risk mitigation activity, and the cost of experiencing the risks must be rationalized relative to the investment. As a rule, don't compromise on security with cost optimizations below the point of responsible and agreed upon risk mitigation. Optimizing security costs by 'right sizing' solutions is an important optimization practice but be aware of tradeoffs such as these when doing so.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Reduced security controls.** Security controls are established across multiple layers, sometimes redundant, to provide a defense in depth.

A cost optimization tactic is to look for ways to remove components or processes that accrue unit or operational costs. Be aware that the choice to remove any security-centric items such items like the following examples for the sake of cost will have a security impact you'll need to carefully perform a risk analysis on.

- Reducing or simplifying authentication and authorization techniques compromises the 'verify explicitly' principle of zero-trust architecture. For example using a basic authentication scheme such as pre-shared keys over investing time to learn industry OAuth approaches or using simplified role-based access control assignments to reduce management overhead.

- Removing *encryption* in transit or rest to reduce costs on certificates and their operational processes will expose that data to potential integrity or confidentiality breaches.

- Removing or reducing *security scanning or inspecting tooling* or security testing because of the associated cost and time investment could directly impact the confidential, integrity, or availability that tooling and testing was in place to protect.

- Reducing the frequency of *security patching* because of operational time invested in cataloging and performing the patching will impact a workload's ability to address evolving threats.

- Removing network controls, such as firewalls, might fail to block malicious inbound and outbound traffic.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Increased workload surface area.** Security prefers a reduced and contained surface area to minimize attack vectors and minimize the management of security controls.

Cost-centric cloud design patterns sometimes necessitate the introduction of *additional components*. These additional components increase the surface area of the workload. Those components and the data within them must be secured, in possibly unique ways from other components and data already in the system and are often included in compliance scope. Some examples are using:

- Static content hosting to offload data to a new content delivery network component
- The valet key pattern to offload processing and secure access to resources to client compute
- Queue-based load leveling to smooth costs involves introducing a message bus

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Removed segmentation.** Security stresses strong segmentation to support applying targeted security controls and to control blast radius.

Sharing resources, such as in multi-tenancy situations or co-locating multiple applications on a shared application platform, is an approach to reduce costs through *increased density* and smaller management surface. This increased density can lead to security concerns such as the following.

- Lateral movement between components that share resources is easier. A security event that compromises the availability of the application platform host or an individual application would also have a larger blast radius.

- Colocated resources shared a workload identity and have less meaningful audit trails in access logs.

- Network security controls must be broad enough to cover all colocated resources, potentially violating the principle of least privilege for some resources.

- Co-locating disparate applications or data on to a shared host can lead to extending compliance requirements and security controls to applications or data that would have otherwise been out of scope, requiring additional security scrutiny and auditing effort on the colocated components.

## Cost Optimization tradeoffs with Operational Excellence

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Compromised software development lifecycle (SDLC) capacities.** A workload's SDLC process is established to provide rigor, consistency, specificity, and prioritization to change management within a workload.

- Reducing *testing efforts* to save time and the cost associated with test personnel, resources, and tooling can result in more bugs in production.

- Delaying *paying back technical debt* to focus personnel efforts on new features can lead to slower development cycles and overall reduced agility.

- Deprioritizing *documentation* to focus personnel efforts on product development can lead to longer onboarding time for new employees, impact incident response effectiveness, and compromise compliance requirements.

- Not *investing in training* leads to stagnated skills, reducing the team's ability to adopt newer technologies and practices.

- Removing *automation tooling* to save money can result in personnel spending more time on those tasks. It also increased the risk of errors and inconsistencies.

- Reducing planning efforts, such as scoping and activity prioritization, to cut expenses may raise the likelihood of rework due to vague specifications and poor implementation.

- Avoiding or reducing *continuous improvement* activities, like retrospectives and after-incident reports, to keep the workload team focused on delivery can overlook chances to optimize routine, unplanned, and emergency processes.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Reduced observability.** Observability is necessary to ensure the workload can have meaningful alerting and successful incident response.

- *Decreasing log and metric volume* to save on storage and transfer costs reduces system observability and can lead to:

  - Fewer data points to build alerts related to reliability, security, and performance.
  - Coverage gaps for incident response activities.
  - Limited observability in interactions or boundaries related to security or compliance.

- Cost-optimization design patterns can *add components* used in the workload in increase its complexity. The workload monitoring strategy must expand to include those new components. For example, the patterns might introduce flows that span multiple components or shift processes from the server to the client. This can increase the complexity of correlating and tracking them all.

- Reducing investment in *observability tooling and maintaining effective dashboards* can reduce the ability to learn from production, validate design choices, and inform product design. This reduction can also hamper incident response activities and make it harder to hit the recovery time objective and service level objective.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Deferred maintenance.** Keeping code, tooling, software packages, operating systems patched and up to date in a timely and orderly manor is an expectation of a workload team.

- Not renewing maintenance contracts with tooling vendors may result in missing new optimization features, bug resolutions, and potential security updates.

- Lengthening the time between *system patching* to save time can lead to missing bug fixes or not being protected against evolving security threats.

## Cost Optimization tradeoffs with Performance Efficiency

Cost Optimization and Performance Efficiency both strive to maximize the value for the workload's cost. Performance Efficiency attempts to meet performance targets without spending more than necessary. Cost Optimization strives to maximize the value of a workload's resources without exceeding performance targets.  As a result, cost optimization often improves performance efficiency. However, there are performance efficiency tradeoffs associated with cost optimization. These tradeoffs can make it harder to reach performance target and hinder ongoing performance optimization.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Under-provisioned or under-scaled resources.** A performance-efficient workload has enough resources available to serve demand without excessive unused overhead, even as usage patterns fluctuate.

- Reducing costs through *downsizing resources* can starve applications of resources, and the application may not be able to handle significant usage pattern fluctuations.

- *Limiting or delaying scaling* to cap or reduce costs might result in insufficient supply to meet the demand.

- Autoscale settings that *scale down aggressively* to reduce costs might leave a service unprepared for sudden spike in demand or cause frequent scaling fluctuations (flapping).

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Not optimized over time.** Evaluating the effects of functionality changes, usage patterns change, new technologies, and different approaches on the workload is one way to seek additional efficiency.

- Limiting the focus on *developing expertise* in performance optimization to prioritize delivery can overlook opportunities to improve resource usage efficiency.

- Removing access *performance testing or monitoring tools* not only increases the risk of undetected performance issues but limits the ability for a workload team to execute on measure-improve cycles.

- Neglecting areas prone to performance degradation, like data stores, can gradually deteriorate query performance and elevate overall system usage.

## Related links

Explore the tradeoffs for the other pillars:

- [Reliability tradeoffs](../reliability/tradeoffs.md)
- [Security tradeoffs](../security/tradeoffs.md)
- [Operational Excellence tradeoffs](../operational-excellence/tradeoffs.md)
- [Performance Efficiency tradeoffs](../performance-efficiency/tradeoffs.md)
