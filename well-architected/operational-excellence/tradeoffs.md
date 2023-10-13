---
title: Operational Excellence tradeoffs
description: Learn about tradeoffs that you might encounter when you design workload architectures and operations for operational excellence.
author: ferantivero
ms.author: chkittel
ms.date: 11/15/2023
ms.topic: conceptual
---

# Operational Excellence tradeoffs

Operational Excellence provides workload quality through the implementation of clear team standards, understood responsibility and accountability, attention to customer outcomes, and team cohesion. The implentation of these goals is rooted in DevOps, which recommends minimizing process variance, reducing human error, and ultimately increasing the return of value for the workload. That value isn't just measured against the functional requirements served by the components of the workload. It's also measured by the value that the team delivers in striving for improvement.

During the design phase of a workload and over its lifecycle, as continuous improvement steps are taken, it's important to consider how decisions based on the [Operational Excellence design principles](./principles.md) and the recommendations in the [Design review checklist for Operational Excellence](./checklist.md) might influence the goals and optimizations of other pillars. Certain decisions might benefit some pillars but constitute tradeoffs for others. This article describes example tradeoffs that a workload team might encounter when designing workload architecture and operations.

## Operational Excellence tradeoffs with Reliability

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Increased complexity.** Reliability prioritizes simplicity, because simple design minimizes misconfiguration and reduces unexpected interactions.

- Safe deployment strategies often require some amount of forward and backward compatibility between application logic and data in the workload. This added complexity increases the testing burden and can lead to complexities or integrity issues with the workload's data.

- Highly layered, modularized, or parameterized infrastructure as code can increase the chance of accidental misconfiguration because of the complexity of the interaction between the code components.

- Cloud design patterns that benefit operations sometimes necessitate the introduction of additional components, for example, the use of an external configuration store or the coordination of sidecar deployments in a containerized application platform. The additional components increase the points of interaction in the system, increasing the surface area for malfunction or misconfiguration.

- Workload components that are designed to independently evolve to support agile development and hosting introduce dependencies on service discovery, or even DNS as a layer of indirection. Service discovery might lack responsiveness to change, and malfunction can be hard to diagnose.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Increased potentially destabilizing activities.** The Reliability pillar encourages the avoidance of activities or design choices that can destabilize a system and lead to disruptions, outages, or malfunctions.

- Deploying small, incremental changes is a technique for mitigating risk, but those small changes are also expected to be delivered to production more frequently. Deployments can destabilize a system, so as the rate of deployment increases, so does this risk.

- A culture that measures itself with velocity metrics like deployments per week and uses automation that can facilitate introducing changes at a faster pace is also likely to perform more deployments in a shorter period.

- Increasing density to simplify operations by reducing the number of control and observability surfaces can also lead to an increased availability risk because malfunction or misconfiguration increases the impact radius of a destabilizing event.

## Operational Excellence tradeoffs with Security

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Increased surface area.** The Security pillar recommends a reduced workload surface area in terms of components and exposure to operations. This reduction minimizes attack vectors and produces a smaller scope for security control and testing.

- Components that surround the workload and support its operations, like automation or a custom control plane, must also be in scope for regular security hardening and testing.

- Routine, ad hoc, and emergency operations increase the points of contact with the workload. A Zero Trust approach requires that these processes are considered attack vectors and must be included in the security controls and validation for the workload.

- The observability platform of the system collects logs and metrics about the workload, which can be a valuable source of information disclosure. Therefore, the workload's security needs to extend to protect data sinks from internal and external threats.

- Build agents, externalized configuration and feature toggle stores, and side-by-side deployment approaches all increase the application surface area that requires security.

- A higher deployment frequency caused small, incremental changes or by "get current, stay current" efforts results in more security testing in the software development lifecycle.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Increased desire for transparency.** A secure workload is based on designs that protect the confidentiality of data that flows through the components of the system.

Observability platforms ingest data of all types to gain insights into a workload's health and behavior. As teams try to attain higher fidelity in observability data, there's an increased risk that data classification controls, like data masking, of the source systems don't extend to the logs and log sinks of the observability platform.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Reduced segmentation.** A key security approach for isolating access and function is to design a strong segmentation strategy. This design is implemented through resource isolation and identity controls.

- Collocating disparate application components into shared compute, network, and data resources to make management easier reverses segmentation or makes role-based segmentation harder to achieve. Collocated components might also need to share a workload identity, which can lead to over-assignment of permissions or a lack of traceability.

- Collecting all logs from across the system in a unified log sink can make querying and building alerts easier. However, doing so can also make it harder or impossible to provide row-based security in order to treat sensitive data with the required audit controls.

- Simplifying the management of attribute-based or role-based security by reducing the granularity of roles and their assignments can lead to inappropriately broad permissions.

## Operational Excellence tradeoffs with Cost Optimization

The Operational Excellence pillar never recommends activities that reduce productivity or jeopardize a workload's return on investment. Recommendations that seem to shift focus from delivery activities take into account long-term best interests for the workload and team. If your workload is nearing its sunset date, it probably doesn't make sense to invest highly in recommendations that trigger these tradeoffs.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Increased resource spending.** A major cost driver for a workload is the cost of its resources. Deploying fewer resources, right-sizing resources, and reducing consumption generally helps keep costs low.

- Implementing safe deployment practices, even if the changes are relatively small, can lead to an increase in the number of resources concurrently deployed. These patterns require the deployment of multiple concurrent instances of the application or infrastructure component so that traffic can be shifted in a controlled way. This is punctuated in a workload that uses an immutable infrastructure approach.

- Additional workload components need to be introduced to implement operationally aligned cloud design patterns or workload automation. For example, to support deployment agility, a gateway routing component could be introduced; to support better configuration management, an external configuration store could be introduced; to support tenant lifecycle events a control plane is built. These resources also then further influence the costs of preproduction environments.

- Increasing the number of preproduction environments to improve the development and testing experience through isolation will necessarily increase the number of resources. These resources, not being used to deliver supply against production demand, drives up cost for the solution.

- Raising the parity of preproduction environments in terms of resource count, SKUs, and data volumes with the production environment brings with it better assurances in quality assurance process. As parity increases, so does the cost.

- While not directly a resource, for observability platforms to be effective, telemetry data needs to be persisted. Most operational data stores have pricing based on a combination of ingestion rates and volume. This generally means that as the amount of low-latency, high-diversity telemetry increases so too will the costs. For multi-region deployments, these operational data sinks are expected to be found per-region, so any per-resource costs become a factor.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Decreased focus on delivery activities.** Workload team members deliver increased workload value by efficiently performing tasks aligned to their capabilities.

- Workload teams that spend time building and refining a healthy and responsible support structure and incident response are providing valuable service to its users. As the effort increases in support functions, such as formal on-call rotations, usually with a corresponding change in business critically, the costs of these activities will increase. This increase can come from direct staff increase or can come indirectly from shifted attention away from delivery activities to supporting functions.

- Training is a critical part of a workload team's personal continuous improvement process. This training can be formal or self-directed through personal enrichment time. As the amount of training time increases, the amount of time available to participate in direct development of the workload decreases. The investment in training is diminished when the training is not role-based and nor specifically relevant to the workload or its future.

- Standardized routine operational tasks to protect the reliability, security, and performance efficiency of a workload takes time to define, refine, and execute, which is not time directly spent on delivery. Some examples of these are comprehensive change impact analysis, change control processes, thorough testing, and increased patch management. As the frequency, comprehensiveness, or operational burden of the tasks increases, so will the time needed to be invested.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Increased tooling demands and diversity.** Cost optimization looks to reduce tooling sprawl, perform vendor consolidation, and prefers a 'right sized' approach to all tooling purchases.

A workload team purchases tools and hardware to support activities performed across the entire software development lifecycle (SDLC); from planning and design through development and testing and into monitoring. The marketplace for tooling in this space is ever-growing and exists at various price points usually aligned with their features and capabilities. Outside of free offerings, this tooling comes with initial licensing costs, that might be per-seat or site-wide, and often ongoing maintenance contracts. New vendor relationships might also need to be established. Here are some examples of where tooling or hardware spending is expected in association with the principles of Operational Excellence:

- Requirements and backlog management
- Architecture design tooling
- UI/UX design tooling
- Code and asset hosting
- Code and low-code development environments
- Automation tooling
- Development and quality assurance workstations
- Development and deployment pipelines
- Test execution and tracking
- Observability tooling

## Operational Excellence tradeoffs with Performance Efficiency

:::image type="icon" source="../_images/trade-off.svg"::: **Increased resource utilization.** Performance Efficiency strives to allocate as much of the available compute and network possible to perform the requirements of the workload.

- A workload's observability framework requires that the components in the architecture allocate time and resources to create, collect, and steam logs and metrics. These data points ensure effective alerting and monitoring is possible for reliability, security, and performance. As the level of instrumentation increases, the strain to system resources might likewise increase.

- Some deployment models, such as blue/green, that the workload uses for safe deployment practices might introduce side-by-side deployments on the production application platform, requiring preemptive scaling to have sufficient supply available to meet future demand or leaving a mostly dormant deployment in place for a period to support rollback.

:::image type="icon" source="../_images/trade-off.svg"::: **Increased latency.** Performant workloads look for ways to reduce time spent and resources consumed performing their functionality.

- Many deployment models require the use of gateway routing access patterns which can introduce latency, which draws against the performance target budget for the related flows.

- Some cloud design patterns that support "independent change over time" approaches to support the ideals of incremental improvements can introduce additional latency through the traversal of additional components. This latency can come through the introduction of gateways, messaging brokers, or anti-correction layers.

## Related links

Explore the tradeoffs for the other pillars:

- [Reliability tradeoffs](../reliability/tradeoffs.md)
- [Security tradeoffs](../security/tradeoffs.md)
- [Cost Optimization tradeoffs](../cost-optimization/tradeoffs.md)
- [Performance Efficiency tradeoffs](../performance-efficiency/tradeoffs.md)
