---
title: Performance Efficiency tradeoffs
description: Learn about tradeoffs that you might encounter when you design workload architectures and operations for performance efficiency.
author: ckittel
ms.author: chkittel
ms.date: 11/15/2023
ms.topic: conceptual
---

# Performance Efficiency tradeoffs

A workload that meets its performance targets without overprovisioning is efficient. The goal of performance efficiency is to have just enough supply to handle demand at all times. Key strategies for performance efficiency include proper use of code optimizations, design patterns, capacity planning, and scaling. Clear performance targets and testing underpin this pillar.

During the process of negotiating a workload's performance targets and designing a workload for performance efficiency, it's important to be aware of how the [Performance Efficiency design principles](./principles.md) and the recommendations in the [Design review checklist for Performance Efficiency](./checklist.md) might affect the optimization goals of other pillars. Certain performance efficiency decisions might benefit some pillars but constitute tradeoffs for others. This article lists example tradeoffs that a workload team might encounter when designing workload architecture and operations for performance efficiency.

## Performance Efficiency tradeoffs with Reliability

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Reduced replication and increased density.** A cornerstone of reliability is ensuring resilience by using replication and limiting the blast radius of malfunctions.

- A workload that achieves efficiency by delaying scaling until the last responsible moment closely meets demand but is vulnerable to unforeseen node failures and scaling delays.

- Consolidating workload resources can use excess capacity and improve efficiency. However, it increases the blast radius of a malfunction in the collocated component or application platform.

- Scaling in or scaling down to minimize surplus capacity can leave a workload underprovisioned during usage spikes, which leads to service disruptions.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Increased complexity.** Reliability prioritizes simplicity.

- Using autoscaling to balance workload supply against demand introduces variability in the workload's topology and adds a component that must work correctly for the system to be reliable. Autoscaling leads to triggering more application lifecycle events, like starting and stopping.

- Data partitioning and sharding help avoid performance issues in large or frequently accessed datasets. However, the implementation of these patterns increases complexity because (eventual) consistency needs to be maintained across additional resources.

- Denormalizing data for optimized access patterns can improve performance, but it introduces complexity because multiple representations of data need to be kept synchronized.

- Performance-centric cloud design patterns sometimes necessitate the introduction of additional components. The use of these components increases the surface area of the workload. The components then must themselves be made reliable to keep the whole workload reliable. Examples include:
  - A message bus for load leveling, which introduces a critical, stateful component.
  - A load balancer for autoscaled replicas, which requires reliable operation and the enlistment of replicas.
  - Offloading data to caches, which requires reliable cache invalidation approaches.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Testing and observation on active environments.** Avoiding the unnecessary use of production systems is a self-preservation approach for reliability.

- Performance testing in active environments, like the use of synthetic transactions, carries the risk of causing malfunctions due to the test actions or configurations.

- Workloads should be instrumented with an application performance monitoring (APM) system that provides information from active environments. The APM tooling is installed and configured in application code or in the hosting environment. Improper use, exceeding limitations, or misconfiguration of the tool can compromise its functionality and maintenance, potentially undermining reliability.

## Performance Efficiency tradeoffs with Security

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Reduction of security controls.** Security controls are established across multiple layers, sometimes redundantly, to provide defense in depth.

One performance optimization strategy is to remove or bypass components or processes that contribute to delays in a flow, especially when their processing time isn't justified. However, this strategy can compromise security and should be accompanied by a thorough risk analysis. Consider the following examples:

- Removing encryption in transit or at rest to improve transfer speeds exposes the data to potential integrity or confidentiality breaches.

- Removing or reducing security scanning or inspecting tools to reduce processing times can compromise the confidentiality, integrity, or availability that those tools protect.

- Decreasing the frequency of security patching to limit the performance impact can leave a workload more vulnerable to emerging threats.

- Removing firewall rules from network flows to improve network latency can allow undesirable communication.

- Minimizing data validation for quicker data processing might compromise data integrity, especially if inputs are malicious.

- Using less entropy in encryption or hashing algorithms, for example, on the initialization vector (IV), is more efficient but makes the encryption easier to crack.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Increased workload surface area.** Security prioritizes a reduced and contained surface area to minimize attack vectors and reduce the management of security controls.

Performance-centric cloud design patterns sometimes necessitate the introduction of additional components. These components increase the surface area of the workload. The new components must be secured, possibly in ways that aren't already used in the system, and they often increase the compliance scope. Consider these commonly added components:

- A message bus for load leveling

- A load balancer for autoscaled replicas

- Offloading data to caches, application delivery networks, or content delivery networks

- Offloading processing to background jobs or even client compute

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Removing segmentation.** The Security pillar prioritizes strong segmentation to enable fine-grained security controls and reduce blast radius.

Sharing resources is an approach for improving efficiency. It increases density to optimize capacity usage. Examples include multitenancy scenarios or combining disparate applications in an architecture on a common application platform. The increased density can lead to the following security concerns:

- Increased risk of unauthorized lateral movement from one tenant to another.

- A shared workload identity that violates the principle of least privilege and obscures individual audit trails in access logs.

- Perimeter security controls, for example network rules, that are reduced to cover all collocated components, giving individual components more access than necessary.

- A compromise of the application platform host or an individual component due to a larger blast radius. This increase is caused by easier access to collocated components.

- Collocating disparate components leading to more components in scope for compliance because of their shared host.

## Performance Efficiency tradeoffs with Cost Optimization

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Too much supply for demand.** Both Cost Optimization and Performance Efficiency prioritize having just enough supply to serve demand.

- Overprovisioning is a risk when teams try to mitigate performance issues in a workload. Some common causes of overprovisioning include:
  - Initial capacity planning was misjudged because the team focused only on peak load estimates, neglecting strategies for peak smoothing in the workload design.
  - Scaling a resource up or out during a troubleshooting step of an incident response.

- Autoscaling can be misconfigured. Some examples of misconfigured autoscaling include:
  - Scaling up with minimal changes in demand or an extended cooldown period can incur more cost than demand requires.
  - Using autoscaling without a set upper limit can lead to uncontrolled growth due to system malfunctions or abuse and exceed the expected workload requirements.

- Expanding into multiple regions can enhance performance by bringing workloads closer to the user, but it also adds complexity and resource duplication.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: More components.** One cost optimization technique is to consolidate with a smaller number of resources by increasing density, removing duplication, and collocating functionality.

- Performance-centric cloud design patterns sometimes necessitate the introduction of extra components. These extra components usually lead to an overall cost increase for the workload. For example, you might include a message bus for load leveling or offload tasks to an application or content delivery network for improved response times.

- Resource segmentation allows different parts of a workload to have distinct performance characteristics, enabling independent tuning for each segment. However, it can increase the total ownership costs because it requires multiple optimized segments rather than a single, generalized component.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Increased investment on items that aren't aligned with functional requirements.** One approach to cost optimization is evaluating the value provided by any solution that's deployed.

- Premium services and SKUs can help a workload meet performance targets. These services usually cost more and can provide extra features. They might be underutilized if many of the premium features aren't used specifically for meeting performance targets.

- A performant workload requires telemetry data for observability that must be transferred and stored. An increase in the performance telemetry being collected can increase the cost of telemetry data transfer and storage.

- Performance testing activities add costs that aren't associated with the value of the production system. Examples of performance testing costs include:
  - Instantiating environments that are dedicated to performance-centric tests.
  - Using specialized performance tooling.
  - Spending time to run the tests.

- Training team members for specialized performance optimization tasks or paying for performance tuning services adds to the cost of a workload.

## Performance Efficiency tradeoffs with Operational Excellence

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Reduced observability.** Observability is necessary to provide a workload with meaningful alerting and help ensure successful incident response.

- Reducing log and metric volume to reduce the processing time spent on collecting telemetry instead of other tasks reduces the overall observability of the system. Some examples of the resulting reduced observability include:
  - It limits the data points that are used to build meaningful alerts.
  - It leads to gaps in coverage for incident response activities.
  - It limits observability in security-sensitive or compliance-sensitive interactions and boundaries.

- When performance design patterns are implemented, the complexity of the workload often increases. Components are added to critical flows. The workload monitoring strategy and performance monitoring must include those components. When a flow spans multiple components or application boundaries, the complexity of monitoring the performance of that flow increases. Flow performance needs to be correlated across all the interconnected components.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Increased complexity in operations.** A complex environment has more complex interactions and a higher likelihood of a negative impact from routine, ad hoc, and emergency operations.

- Improving performance efficiency by increasing density elevates the risk in operational tasks. An error in a single process can have a large blast radius.

- As performance design patterns are implemented, they influence operational procedures like backups, key rotations, and recovery strategies. For example, data partitioning and sharding can complicate routine tasks when teams try to ensure that those tasks don't affect data consistency.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Culture stress.** Operational Excellence is rooted in a culture of blamelessness, respect, and continuous improvement.

- Conducting root cause analysis of performance issues identifies deficiencies in processes or implementations that require correction. The team should consider the exercise a learning opportunity. If team members are blamed for issues, morale can be affected.

- Routine and ad hoc processes can affect workload performance. It's often best to perform these activities during off-peak hours. However, off-peak hours can be inconvenient or outside of regular hours for the team members who are responsible for or skilled in these tasks.

## Related links

Explore the tradeoffs for the other pillars:

- [Reliability tradeoffs](../reliability/tradeoffs.md)
- [Security tradeoffs](../security/tradeoffs.md)
- [Cost Optimization tradeoffs](../cost-optimization/tradeoffs.md)
- [Operational Excellence tradeoffs](../operational-excellence/tradeoffs.md)
