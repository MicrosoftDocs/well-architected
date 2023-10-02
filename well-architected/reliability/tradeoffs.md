---
title: Reliability tradeoffs
description: Reliability tradeoffs in the Azure Well-Architected Framework
author: ckittel
ms.author: chkittel
ms.date: 10/02/2023
ms.topic: conceptual
---

# Reliability tradeoffs

A reliable workload consistently meets its defined reliability objectives. It strives to reach established resiliency targets, ideally by circumventing events that impact reliability. But practically, the workload must tolerate and control the impact of such events and maintain operations at a predetermined level during active malfunction. Even during disaster, a reliable workload must recover to a specific state within a period of time, both of which are agreed upon among the stakeholders. An incident response plan to have rapid detection and recovery is vital.

During the design phase of a workload, it's important to consider how decisions based on the [Reliability design principles](./principles.md) and recommendations in the [Design review checklist for Reliability](./checklist.md) might influence the goals and optimization efforts of other pillars. Certain decisions may benefit some pillars, while being tradeoffs for others. This article lists example tradeoffs that a workload team might encounter when designing workload architecture and operations for reliability.

## Reliability tradeoffs with Security

:::image type="icon" source="../_images/trade-off.svg"::: **Increased workload surface area.** Security prefers a reduced and contained surface area to minimize attack vectors and minimize the management of security controls.

- Reliability is often obtained using _replication_. Replication can be component level, data level, or even a geographic level. Replicas, by design, increase the surface area of a workload. From a security perspective, a reduced and contained surface area is preferred to minimize potential attack vectors and streamline the management of security controls.
- Similarly, _disaster recovery_ solutions, such as backups, increase the workload's surface area. However, they are often isolated from the workload's runtime. This requires the implementation of additional security controls, which might be specific to the disaster recovery solution.
- To achieve reliability goals, _additional components_ might be needed for the architecture, increasing the surface area. For example, a message bus to make requests resilient. This increased complexity increases the surface area of the workload by adding new components that must be secured, in possibly unique ways from other components already in the system. Typically, these components are accompanied by _additional code and libraries_ to support their use or general reliability patterns, which also increases the applications' surface area.

:::image type="icon" source="../_images/trade-off.svg"::: **Emergency access security control bypass.** Security prefers that all controls remain active in both normal and stressed systems.

- When a workload is experiencing a reliability event that is being addressed under active _incident response_, urgency might act as inappropriate pressure for workload teams to bypass security controls that are optimized for routine access. Likewise, the incident response could see the team temporarily disabling a security control as part of troubleshooting, which leaves the workload further exposed and risks not being re-enabled post incident.
- _Troubleshooting activities_ could lead to temporary disablement of security protocols leaving an already stressed system potentially leaving it exposed to additional security risks. There's also the risk of the security protocols not being reestablished promptly.

:::image type="icon" source="../_images/trade-off.svg"::: **Old software versions.** Security encourages a "get current, stay current" approach to vendor security patches.

- Applying _security patches or software updates_ can potentially disrupt the target component, causing unavailability during the software change. Delaying or avoiding patching might avoid the potential reliability risks, it leaves the system unprotected against evolving threats.
- This is extended to the _workload's code_ itself, including application code using old libraries and containers using old base images. If updating and deploying application code is viewed as an unmitigated reliability risk, it exposes the application to additional security risks over time.

## Reliability tradeoffs with Cost Optimization

:::image type="icon" source="../_images/trade-off.svg"::: **Increased implementation redundancy or waste.** A cost optimized workload minimizes underutilized resources and avoids over-provisioning resources.

- _Replication_ is a key strategy for reliability, specifically to have enough replication to handle a specific number of concurrent node failures. The tolerance for more concurrent node failures requires a higher replica count, leading to increased costs.
- _Over-provisioning_ is another technique to be able to absorb unexpected load on a system that could otherwise lead to a reliability event. Any excess capacity that is not utilized is considered wasteful.
- If a workload uses a _disaster recovery solution_ that excessively satisfies the workload's recovery point and time objectives, the excess will lead to higher costs through waste.
- Workload deployments themselves are a potential source for reliability impact, and that impact is often mitigated through redundancy at deployment time using a _deployment strategy_ such as blue/green. This transient duplication of resources during safe deployment typically increases the overall cost of the workload during those periods. Costs increase with frequency of deployments.

:::image type="icon" source="../_images/trade-off.svg"::: **Increased investment in operations not aligned with functional requirements.** One approach to cost optimization is evaluating the value provided by any solution deployed.

- A reliable system requires observability to be established. Monitoring systems require _observability data_ transfer and collection. With more monitoring capabilities, the frequency and volume of data increase, leading to additional costs.
- Reliability affordances in workloads require _testing and drilling exercises_. Test design and execution takes time and potentially specialized tooling, leading to extra costs.
- Workloads with high reliability targets often have a _rapid response_ process that requires technical team members to be part of a formal on-call rotation. This requires additional personnel costs or lost opportunity costs due to directed attention, and potential tooling costs to manage the process.
- _Support contracts_ with technology providers are a key component of a reliable workload. Support contracts that are not utilized due to over-provisioning of the level of care offered is workload waste.

## Reliability tradeoffs with Operational Excellence

:::image type="icon" source="../_images/trade-off.svg"::: **Increased operational complexity.** Operational Excellence, like reliability itself, favors simplicity.

- Reliability usually increases the complexity of a workload. As the complexity of a workload increases, so too can the operational aspects of the workload to support the added components and processes in terms of _deployment coordination_ and configuration surface area.
- Having a comprehensive monitoring strategy for a workload is a key part of operational excellence. Introducing additional components into an architecture to implement reliability design patterns results in more data sources to manage, increasing the complexity of implementing distributed tracing and observability.

:::image type="icon" source="../_images/trade-off.svg"::: **Increased effort to generate team knowledge and awareness.** Operational Excellence recommends having a maintained documentation repository on procedures and topologies.

- As a workload becomes more robust by adding additional reliability-centric components, more time is required to maintain _operational procedures and artifact documentation_.
- The training needs to grow in relation to the components within the workload. This affects the ramp-up time for onboarding and expands the knowledge necessary to track _product roadmaps and service-level guidance._

## Reliability tradeoffs for Performance Efficiency

:::image type="icon" source="../_images/trade-off.svg"::: **Increased latency.** Performance Efficiency requires a system to achieve performance targets for user and data flows.

- Reliability patterns often involve _data replication_ to survive replica malfunction. Replication introduces additional latency for reliable data write operations, consuming a part of the performance budget for a specific user or data flow.
- Reliability sometimes employs various forms of _resource balancing_ to distribute or redistribute load to healthy replicas. A dedicated component responsible for balancing usually impacts the performance of the request or process being balanced.
- _Distributing components_ across geographical boundaries or availability zones to survive a scoped impact introduces network latency in the communication between components that span those availability boundaries.
- Extensive practices are used to _observe the health of a workload_. While monitoring is critical for reliability, instrumentation is not always a net-zero effect on system performance; as observability increases, performance might decrease.

:::image type="icon" source="../_images/trade-off.svg"::: **Increased over-provisioning.** Performance Efficiency discourages over-provisioning, instead encourages running with just enough resources to satisfy demand.

- Autoscaling operations are not instantaneous and therefore cannot reliably handle a sudden and dramatic spike in demand that isn't able to be shaped or smoothed. As such, over-provisioning through either larger or _more instances_ are a critical reliability tactic to account for this lag between demand signal and supply creation. Unused capacity counters the efficiency aspect of performance efficiency.
- Sometimes a component cannot be scaled in reaction to demand, and that demand is not fully predictable. _Large instances_ used to cover the 'worst case' leads to over-provisioning waste for situations that are outside that use case.

## Related resources

Explore the tradeoffs for the other pillars.

- [Security tradeoffs](../security/tradeoffs.md)
- [Cost optimization tradeoffs](../cost-optimization/tradeoffs.md)
- [Operational excellence tradeoffs](../operational-excellence/tradeoffs.md)
- [Performance efficiency tradeoffs](../performance-efficiency/tradeoffs.md)
