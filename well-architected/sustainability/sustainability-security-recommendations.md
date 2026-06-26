---
title: Security recommendations for sustainable workloads on Azure
description: Consolidated security-focused recommendations mapped from sustainable workload design areas on Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 06/25/2026
ms.topic: concept-article
---

# Security recommendations for sustainable workloads on Azure

A secure system isn't just protected from threats. It also operates more efficiently by preventing unnecessary or malicious use of resources. When workloads are exposed or poorly protected, bad actors can drive them into inefficient states through abuse, automation, or exploitation. This inefficiency leads to extra compute, network, and storage consumption.

This article examines sustainability through the lens of the Security pillar and answers these design questions:

- How do you protect workloads without introducing unnecessary resource consumption?
- How do you prevent malicious and unintended usage from increasing workload waste?


## Prevent malicious activity from driving unnecessary resource consumption

Security controls play an important role in preventing workloads from being used in ways that increase unnecessary resource consumption. Without proper protection, malicious or unintended traffic can significantly inflate compute, network, and storage usage without delivering any business value.

For example, a distributed denial of service (DDoS) attack can force a system to process large volumes of unwanted requests. This attack triggers autoscaling, increased network throughput, and excessive logging. Even when systems remain available, they might consume far more resources than under normal operation.

Security controls help contain this waste by stopping unwanted activity as early as possible. Network-level protections such as firewalls, DDoS protection, and edge filtering can block traffic before it reaches application workloads. Policy enforcement mechanisms such as identity controls can further restrict excessive or abnormal usage patterns at the application boundary.

| Recommendation                                                                     | Sustainability benefit                                                       |
| ---------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| Block malicious traffic as early as possible by using edge or network-level controls. | Reduces unnecessary compute and network usage caused by unwanted requests.   |
| Use rate limiting and throttling to control excessive or abusive request patterns. | Prevents workload overload and avoids unnecessary scaling events.            |
| Apply identity-based access controls to restrict unauthorized usage.               | Reduces unnecessary processing of invalid or malicious requests.             |
| Design security layers to filter traffic early rather than at application level.   | Minimizes downstream resource consumption and improves efficiency.           |
| Avoid excessive overlapping security layers unless required.                       | Reduces redundant processing and avoids unnecessary infrastructure overhead. |

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Security isn't free. Adding more inspection layers, routing hops, or enforcement points can increase processing overhead. If overdesigned, security architecture itself can introduce extra resource consumption and complexity. The goal is to stop malicious activity early without shifting unnecessary work deeper into the system.

## Security hardening improves sustainability by reducing workload footprint

Security hardening and sustainability go hand in hand because both aim to reduce unnecessary activity in a system and improve how efficiently it operates under real-world conditions. When you harden a workload, it consumes fewer compute, network, and storage resources.

In practice, weakly hardened systems often carry hidden inefficiencies. Exploitation attempts, misconfigurations, and exposed attack surfaces can lead to repeated request patterns, scanning activity, abnormal traffic spikes, and recovery operations. These behaviors consume resources without delivering business value and can also trigger scaling, logging, and remediation workflows that further increase system load.

| Recommendation                                                                                       | Sustainability benefit                                                                |
| ---------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| Harden networking components to reduce exposure and filter unwanted traffic early.                   | Lowers unnecessary network and compute usage caused by malicious or invalid requests. |
| Harden identity and access controls to ensure only legitimate usage patterns are allowed.            | Reduces wasted processing from unauthorized or abusive activity.                      |
| Harden cloud resource configurations to eliminate weak points and misconfigurations.                 | Prevents exploitation-driven workload spikes and reduces abnormal system behavior.    |
| Harden application and code assets to prevent vulnerabilities from being exploited.                  | Avoids resource-heavy attack patterns and unstable execution paths.                   |
| Harden operational and management access paths to reduce administrative misuse or accidental impact. | Improves system stability and reduces unnecessary operational workload.               |

## Reduce security data collection and processing overhead

Security monitoring can also become a hidden source of unnecessary resource consumption. Collecting and processing all available logs and telemetry can significantly increase compute, storage, and network usage without improving security outcomes.

In many systems, security data is forwarded to centralized SIEM platforms without filtering at the source. This creates additional processing layers, increases data movement across services or regions, and results in large volumes of stored telemetry that may never be used. Over time, this can increase both operational overhead and environmental impact.

| Recommendation                                                                              | Sustainability benefit                                                            |
| ------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| Filter logs before ingestion into SIEM systems.                                             | Reduces unnecessary data processing, storage, and network transfer.               |
| Collect only security signals required for detection and analysis.                          | Minimizes telemetry volume and avoids processing unused data.                     |
| Use cloud-native data collection rules to control and refine telemetry intake.              | Prevents over-collection and reduces continuous ingestion overhead.               |
| Avoid forwarding full raw datasets when aggregated or filtered signals are sufficient.      | Reduces cross-system data movement and associated resource consumption.           |
| Design security monitoring pipelines with purpose, not full-fidelity collection by default. | Lowers compute and storage demand while maintaining required security visibility. |


## Improve centralized network security efficiency

Designs that rely on centralized inspection or hub-and-spoke routing can increase the distance data must travel and introduce additional processing at intermediate points. Even when these models improve control and visibility, they can also increase network overhead and resource consumption due to repeated traversal and inspection.

As traffic scales, these additional hops compound into higher bandwidth usage, more processing load on security layers, and increased system-wide inefficiency. In contrast, designs that support localized enforcement reduce unnecessary movement of data across the network.

| Recommendation                                                                                              | Sustainability benefit                                                |
| ----------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| Apply security controls closer to the source of traffic instead of relying on centralized inspection paths. | Reduces unnecessary network traversal and lowers data movement costs. |
| Avoid hub-and-spoke routing for traffic that does not require centralized inspection.                       | Minimizes repeated data movement across network layers and regions.   |
| Use cloud-native security services that enable local analysis and enforcement.                              | Reduces cross-system data transfer and improves network efficiency.   |
| Design traffic flows to minimize intermediate hops introduced purely for security inspection.               | Lowers bandwidth usage and reduces overall infrastructure load.       |


## Related guidance

- [Well-Architected Framework - Security pillar](/azure/well-architected/security/)

