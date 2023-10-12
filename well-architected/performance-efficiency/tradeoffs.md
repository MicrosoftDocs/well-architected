---
title: Performance Efficiency tradeoffs
description: Performance Efficiency tradeoffs in the Azure Well-Architected Framework
author: ckittel
ms.author: chkittel
ms.date: 10/12/2023
ms.topic: conceptual
---

# Performance Efficiency tradeoffs

A workload that meets its performance targets without overprovisioning is efficient. Performance efficiency is having just enough supply to handle demand at all times. Key strategies for performance efficiency include proper use of code optimizations, design patterns, capacity planning, and scaling. Clear performance targets and testing underpin this pillar.

When negotiating a workload's performance targets and while designing a workload for performance efficiency, it's important to be aware of how the [Performance Efficiency design principles](./principles.md) and recommendations in the [Design review checklist for Performance Efficiency](./checklist.md) might affect the optimization efforts of other pillars. Certain performance-efficiency decisions may benefit some pillars while being tradeoffs for others. This article lists example tradeoffs that a workload team might encounter when designing workload architecture and operations for performance efficiency.

## Performance Efficiency tradeoffs with Reliability

:::image type="icon" source="../_images/trade-off.svg"::: **Reduced replication and increased density.** A cornerstone of reliability is ensuring resilience through replication and limiting the blast radius of malfunctions.

- A workload that is efficient by delaying scaling to the last responsible moment closely meets demand but is vulnerable to unforeseen node failures and scaling delays.

- *Consolidating workload resources* can use excess capacity and improve efficiency. However, it increases the blast radius of a malfunction in the co-located component or application platform.

- Scaling in or scaling down to minimize surplus capacity can leave a workload *underprovisioned* during usage spikes, leading to service disruptions due to insufficient supply.

:::image type="icon" source="../_images/trade-off.svg"::: **Increased complexity.** Reliability favors simplicity.

- Using *autoscaling* to balance workload supply against demand introduces variability in its topology and adds a component that must work correctly for the system to be reliable. Autoscaling leads to triggering more application lifecycle events, such as "starting" and "stopping."

- *Data partitioning and sharding* helps avoid performance issues in large or frequently accessed data sets. However, the implementation of these patterns increases complexity through maintaining (eventual) consistency across additional resources.

- *Denormalizing data* for optimized access patterns can improve performance but introduce complexities in keeping multiple representations of data in sync.

- Performance-centric cloud design patterns sometimes necessitate the introduction of *additional components*. Additional components increases the surface area of the workload. Those components then must themselves be made reliable for the whole workload to be considered reliable. Some examples are:
  - Message bus for load leveling, which introduces a critical, stateful-component
  - Load balancer for auto-scaled replicas, which requires reliable operation and enlistment of replicas
  - Offloading data to caches, which require reliable cache invalidation approaches

:::image type="icon" source="../_images/trade-off.svg"::: **Testing and observation on active environments.** A self-preservation approach for reliability is to avoid unnecessary usage of production systems.

- Performance testing in active environments, like using synthetic transactions, carries the risk of causing malfunctions due to the test actions or configurations.

- Workloads should be instrumented with an application performance monitoring (APM) system to learn from active environments. The APM tooling is installed and configured in application code or the hosting environment. Improper use, exceeding limitations, or misconfiguration of the tool can compromise its functionality and maintenance, potentially undermining reliability.

## Performance Efficiency tradeoffs with Security

:::image type="icon" source="../_images/trade-off.svg"::: **Reduction of security controls.** Security controls are established across multiple layers, sometimes redundantly, to provide a defense in depth.

One performance optimization strategy is to remove or bypass components or processes that contribute to delays in a flow, especially when their processing time isn't justified. However, this can compromise security should be accompanied by a thorough risk analysis. Consider the following examples:

- Removing *encryption* in transit or rest to improve transfer speeds exposes that data to potential integrity or confidentiality breaches.
- Removing or reducing *security scanning or inspecting tools* to reduce processing times could compromise the confidential, integrity, or availability those tools protected.
- Decreasing the frequency of *security patching* to limit the performance impact can leave a workload more vulnerable to emerging threats.
- Removing *firewall rules* from network flows to improve network latency can allow undesirable communication.
- Minimizing *data validation* for quicker data processing might compromise data integrity, especially with malicious inputs.
- Using less entropy in encryption or hashing algorithm, for example on the initialization vector (IV), is more efficient but makes it easier to crack the encryption.

:::image type="icon" source="../_images/trade-off.svg"::: **Increased workload surface area.** Security prefers a reduced and contained surface area to minimize attack vectors and minimize the management of security controls.

Performance-centric cloud design patterns sometimes necessitate the introduction of *additional components*. More components increase the surface area of the workload. The new components must be secured in possibly unique ways from other components already in the system and are often expand the compliance scope. Consider these commonly added components:

- Message bus for load leveling
- Load balancer for auto-scaled replicas
- Offloading data to caches, application delivery networks (ADN), or content delivery networks (CDN)
- Offloading processing to background jobs or even client compute

:::image type="icon" source="../_images/trade-off.svg"::: **Removing segmentation.** Security stresses strong segmentation for fine-grained security controls and minimal blast radius.

Sharing resources is an approach to improve efficiency. It increases density to optimize capacity usage. Examples include multi-tenancy situations or combining disparate applications in an architecture to a common application platform. The increased density can lead to the following security concerns:

- Increased risk of unauthorized lateral movement from one tenet to another.
- A shared workload identity that violates the principle of least privilege and obscures individual audit trails in access logs.
- Perimeter security controls, for example network rules, that loosen to cover all colocated components, giving individual components more access than necessary.
- A compromise of the application platform host or an individual component with a larger blast radius due to easier access to the other colocated components.
- Colocating disparate components can lead to components out of scope for compliance being now considered in scope for compliance due to their shared host.

## Performance Efficiency tradeoffs with Cost Optimization

:::image type="icon" source="../_images/trade-off.svg"::: **Too much supply for demand.** Both Cost Optimization and Performance Efficiency stress the importance of having just enough supply to serve demand.

- *Overprovisioning* is a risk when trying to mitigate performance issues in a workload. The following examples are common sources of overprovisioning:
  - Initial capacity planning was misjudged by only focusing on peak load estimates, neglecting strategies for peak smoothing in the workload design.
  - Scaling up or out a resources as part of a troubleshooting step during incident response.

- *Misconfigured autoscaling* can happen in the best-intentioned scenarios. The following are examples of misconfigured autoscaling:
  - Scaling-up with minimal changes in demand or an extended cooldown period can incur more cost than demand requires.
  - Using autoscaling without a set upper limit can lead to uncontrolled growth due to system malfunctions or abuse, exceeding the expected workload requirements.

- Expanding into multiple regions can enhance performance by bringing workloads closer to the user, but it also adds complexity and resource duplication.

:::image type="icon" source="../_images/trade-off.svg"::: **More components.** A technique for cost optimization is to consolidate on a smaller number of resources by increasing density, removing duplication, and co-locating functionality.

- Performance-centric cloud design patterns sometimes necessitate the introduction of *extra components*. These extra components usually come with an overall cost increase for the workload. For example, you might include a message bus for load leveling or offload tasks to an application or content delivery network for improved response times.

- *Resource segmentation* allows different parts of a workload to have distinct performance characteristics, enabling independent tuning for each segment. However, it can increase the total ownership costs with multiple optimized segments rather than a single, generalized component.

:::image type="icon" source="../_images/trade-off.svg"::: **Increased investment on items not aligned with functional requirements.** One approach to cost optimization is evaluating the value provided by any solution deployed.

- When pursing peek performance, *premium services and SKUs* can help a workload meet performance targets. These services usually come at a higher cost and can come with extra features. They might be underutilized if many of the premium features aren't used specifically for meeting performance targets.

- A performant workload requires *telemetry data* for observability that must be transferred and stored. An increase in the performance telemetry collected can increase the cost of telemetry data transfer and storage.

- Performance testing activities add costs unassociated with the value of the production system. The following bullets are examples of performance testing costs:
  - Instantiating *environments* dedicated to performance-centric tests
  - Having specialized *performance tooling*
  - Spending the time executing performance tests

- Training team members for specialized performance optimization tasks or paying for performance tuning services adds to the cost of the workload.

## Performance Efficiency tradeoffs with Operational Excellence

:::image type="icon" source="../_images/trade-off.svg"::: **Reduced observability.** Observability is necessary to ensure the workload can have meaningful alerting and successful incident response.

- *Reducing log and metric volume* to reduce the processing time spent on collecting telemetry instead of other tasks reduces the overall observability in the system. The following bullets are examples of resulting reduced observability:
  - Limits the data points to build meaningful alerts
  - Leads to gaps in coverage for incident response activities
  - Limits observability in security or compliance sensitive interactions or boundaries

- As performance design patterns are implemented, the complexity of the workload often increases, adding components to critical flows. The workload monitoring strategy and performance monitoring must cover those components. A flow that spans multiple components or application boundaries introduces complexity in monitoring the performance of that flow. Flow performance needs to be correlated across all the interconnected components.

:::image type="icon" source="../_images/trade-off.svg"::: **Increased complexity in operations.** The complexity of the environment increases the complexity of the interactions and the chances of negative impact from routine, ad-hoc, and emergency operations.

- Improving performance efficiency through increased *density* elevates the risk in operational tasks. An error in a single process can have large blast radius.

- As performance design patterns are implemented, those patterns influence operational procedures such as backups, key rotations, and recovery strategies. For example, data partitioning and sharding may complicate routine tasks to ensure those tasks do not impact data consistency.

:::image type="icon" source="../_images/trade-off.svg"::: **Culture stress.** Operational Excellence is rooted in a culture of blamelessness, respect, and continuous improvement.

- Conducting *root cause analysis* on performance issues will identify deficiencies in processes or implementations that require correction. If team members are blamed for issues instead of using it as a team learning opportunity, it can severely hurt team morale.

- Routine and ad-hoc processes can affect workload performance. It's often preferable to perform these activities during *off-peak hours*. However, off-peak hours could be inconvenient or outside regular hours for the team members responsible or skilled for these tasks.

## Related links

Explore the tradeoffs for the other pillars:

- [Reliability tradeoffs](../reliability/tradeoffs.md)
- [Security tradeoffs](../security/tradeoffs.md)
- [Cost Optimization tradeoffs](../cost-optimization/tradeoffs.md)
- [Operational Excellence tradeoffs](../operational-excellence/tradeoffs.md)
