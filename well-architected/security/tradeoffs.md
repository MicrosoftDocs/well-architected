---
title: Security tradeoffs
description: Learn about tradeoffs that you might encounter when you design workload architectures and operations for security.
author: ckittel
ms.author: chkittel
ms.date: 11/15/2023
ms.topic: conceptual
---

# Security tradeoffs

Security provides confidentiality, integrity, and availability assurances of a workload's system and its users' data. Security controls are required for the workload and for the software development and operational components of the system. When teams design and operate a workload, they can almost never compromise on security controls.

During the design phase of a workload, it's important to consider how decisions based on the [Security design principles](./principles.md) and the recommendations in the [Design review checklist for Security](./checklist.md) might influence the goals and optimizations of other pillars. Certain security decisions might benefit some pillars but constitute tradeoffs for others. This article describes example tradeoffs that a workload team might encounter when establishing security assurances.

## Security tradeoffs with Reliability

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Increased complexity.** The Reliability pillar prioritizes simplicity and recommnds that points of failure are minimized.

- Some security controls can increase the risk of misconfiguration, which could lead to service disruption. Examples of security controls that can introduce risk include network traffic rules, identity providers, virus scanning exclusions, and role-based or attribute-based access control assignments.

- Increased segmentation usually results in a more complex environment in terms of resource and network topology and operator access. This complexity can lead to more points of failure in processes and in workload execution.

- Workload security tooling is often incorporated into many layers of a workload's architecture, operations, and runtime requirements. These tools might introduce their own implications for resiliency, availability, and capacity planning. Failure to account for limitations in the tooling can lead to a reliability event, like SNAT port exhaustion on an egress firewall.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff:  Increased critical dependencies.** The Reliability pillar recommends minimizing critical dependencies. A workload that minimizes critical dependencies, especially external ones, has more control over its points of failure.

The Security pillar requires a workload to verify explicitly. Verification occurs via critical dependencies on key security components. If those components aren't available or if they malfunction, verification might not complete. This failure puts the workload in a degraded state. Some examples of these critical single-point-of-failure dependencies are:

- Ingress and egress firewalls.
- Certificate revocation lists.
- Accurate system time provided by a Network Time Protocol (NTP) server.
- Identity providers, like Microsoft Entra ID.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff:  Increased complexity of disaster recovery.** A workload must reliably recover from all forms of disaster.

- Security controls might affect recovery time objectives. This effect can be caused by the additional steps that are needed encrypt backed up data or by operational access delays created by site reliability triage.

- Security controls themselves, for example secret vaults and their contents or edge DDoS protection, need to be part of the disaster recovery plan of the workload and must be validated via recovery drills.

- Security or compliance requirements might limit data residency options or access control restrictions for backups, potentially further complicating recovery by segmenting even offline replicas.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Increased rate of change.** A workload that experiences runtime change is exposed to more risk of reliability impact due to that change.

- Stricter patching and update policies lead to more changes in a workload's production environment. This change comes from sources like these:
  - Application code being released more frequently because of updates in libraries or updates in base container images
  - Increased routine patching to operating systems
  - Staying current with versioned applications or data platforms
  - Applying vendor patches to software in the environment

- Rotation activities for keys, service principal credentials, and certificates increase the risk of transient issues due to the timing of the rotation and clients using the new value.

## Security tradeoffs with Cost Optimization

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Additional infrastructure.** One approach to cost optimizing a workload is to look for ways to reduce the diversity and quantity of components and increase density.

Some workload components or design decisions exist only to protect the security (confidentiality, integrity, and availability) of systems and data. These components, although they enhance the security of the environment, also increase costs. They must also be subject to cost optimization themselves. Some example sources for these security-centric additional resources or licensing costs are:

- Compute, network, and data segmentation for isolation, which sometimes involves running separate instances, preventing co-location and reducing density.
- Specialized observability tooling, like a SIEM that can perform aggregation and threat intelligence.
- Specialized networking appliances or capabilities, like firewalls or distributed denial-of-service prevention.
- Data classification tools that are required for capturing sensitivity and information-type labels.
- Specialized storage or compute capabilities to support encryption at rest and in transit, like an HSM or confidential-compute functionality.
- Dedicated testing environments and testing tools to validate that security controls are functioning and to uncover previously undiscovered gaps in coverage.

The preceding items often exist outside of production environments, in preproduction and disaster recovery resources.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Increased demand on infrastructure.** The Cost Optimization pillar prioritizes driving down demand on resources to enable the use of cheaper SKUs, fewer instances, or reduced consumption.

- *Premium SKUs*: Some security measures in cloud and vendor services that can benefit the security posture of a workload might only be found in more expensive SKUs or tiers.

- *Log storage*: Broad-coverage, higher-fidelity security monitoring and audit data increases storage costs. Security observability data is also often stored for longer periods of time than would typically be needed for operational insights.

- *Increased resource consumption*: In-process and on-host security controls can introduce additional demand for resources. Encryption for data at rest and in transit can also increase demand. Both scenarios can require higher instance counts or larger SKUs.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Increased process and operational costs.** Personnel process costs are part of the overall total cost of ownership and are factored into the workload's return on investment. Optimizing these costs is a recommendation of the Cost Optimization pillar.

- A more comprehensive and strict patch management regime leads to an increase in time and money spent on these routine tasks. This increase is often coupled with the expectation of investing in preparedness for ad hoc patching for zero-day exploits.

- Stricter access controls to reduce the risk of unauthorized access can lead to more complex user management and operational access.

- Training and awareness for security tools and processes take up employee time and also incur costs for materials, instructors, and possibly training environments.

- Complying with regulations might necessitate additional investments for audits and generating compliance reporting.

- Planning for and conducting drills for security-incident response preparedness takes time.

- Time needs to be allocated for designing and performing routine and ad hoc processes that are associated with security, like key or certificate rotation.

- The security validation of the SDLC usually requires specialized tools. Your organization might need to pay for these tools. Prioritizing and remediating issues found during testing also takes time.

- Hiring third-party security practitioners to perform white-box testing or testing that's performed without the knowledge of a system's internal workings (sometimes known as *black-box testing*), including penetration testing, incurs costs.

## Security tradeoffs with Operational Excellence

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Complications in observability and serviceability.** Operational Excellence requires architectures to be serviceable and observable. The most serviceable architectures are the ones that are the most transparent to everyone involved.

- Security benefits from extensive logging that provides high fidelity insight into the workload for alerting on deviations from baselines and for incident response. This logging can generate a significant volume of logs, which can make it harder to provide insights that are targeted at reliability or performance.

- When compliance guidelines for data masking are followed, specific segments of logs or even large amounts of tabular data are redacted to protect confidentiality. The team needs to evaluate how this observability gap might affect alerting or hinder incident response.

- Strong resource segmentation increases the complexity of observability by requiring additional cross-service distributed tracing and correlation for capturing flow traces. The segmentation also increases the surface area of compute and data to service.

- Some security controls impede access by design. During incident response, these controls can slow down workload operators' emergency access. Therefore, incident response plans need to include more emphasis on planning and drills in order to reach acceptable efficacy.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Decreased agility and increased complexity.** Workload teams measure their speed so that they can improve the quality, frequency, and efficiency of delivery activities over time. Workload complexity factors into the effort and risk involved in operations.

- Stricter change control and approval policies to reduce the risk of introducing security vulnerabilities can slow down the development and safe deployment of new features. However, the expectation of addressing security updates and patching can increase demand for more frequent deployments. Additionally, human-gated approval policies in operational processes can make it more difficult to automate those processes.

- Security testing results in findings that need to be prioritized, potentially blocking planned work.

- Routine, ad hoc, and emergency processes might require audit logging to meet compliance requirements, which increases the rigidity of running the processes.

- Workload teams might increase the complexity of identity management activities as the granularity of role definitions and assignments is increased.

- An increased number of routine operational tasks that are associated with security, like certificate management, increases the number of processes to automate.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Increased coordination efforts.** A team that minimizes external points of contact and review can control their operations and timeline more effectively.

- As external compliance requirements from the larger organization or from external entities increase, the complexity of achieving and proving compliance with auditors also increases.

- Security requires specialized skills that workload teams don't typically have. Those proficiencies are often sourced from the larger organization or from third parties. In both cases, coordination of effort, access, and responsibility needs to be established.

- Compliance or organizational requirements often require maintained communication plans for responsible disclosure of breaches. These plans must be factored into security coordination efforts.

## Security tradeoffs with Performance Efficiency

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Increased latency and overhead.** A performant workload reduces latency and overhead.

- Inspection security controls, like firewalls and content filters, are placed in the flows they secure. Those flows are subject to additional verification, which adds latency to requests.

- Identity controls require each invocation of a controlled component to be verified explicitly. This verification consumes compute cycles and might require network traversal for authorization.

- Encryption and decryption require dedicated compute cycles. These cycles increase the time and resources consumed by those flows. This increase is usually correlated with complexity of the algorithm and the generation of high-entropy and diverse initialization vectors (IV).

- As the extensiveness of logging increases, the impact on system resources and network bandwidth for streaming those logs can also increase.

- Resource segmentation frequently introduces network hops in the workload's architecture.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Increased chance of misconfiguration.** Reliably meeting performance targets depends on predictable implementations of the design.

A misconfiguration or over-extension of security controls can lead to performance impact because of inefficient configuration. Examples of security control configurations that can affect performance include:

- Firewall rule ordering, complexity, and quantity (granularity).

- The inclusion of key files in file integrity monitors or virus scanners, which can lead to lock contention.

- Web application firewalls performing deep packet inspection for languages or platforms that are irrelevant for the components that are being protected.

## Related links

Explore the tradeoffs for the other pillars:

- [Reliability tradeoffs](../reliability/tradeoffs.md)
- [Cost Optimization tradeoffs](../cost-optimization/tradeoffs.md)
- [Operational Excellence tradeoffs](../operational-excellence/tradeoffs.md)
- [Performance Efficiency tradeoffs](../performance-efficiency/tradeoffs.md)
