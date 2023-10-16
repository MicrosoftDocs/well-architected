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

Some workload components or design decisions exist only to protect the security (confidentiality, integrity, and availability) of systems and data. These components, although they enhance the security of the environment, also become a driver for cost. They must also be subject to cost optimization themselves. Some example sources for these security-centric additional resources or licensing costs are:

- Compute, network, and data segmentation for isolation sometimes involves running separate instances, avoiding co-location and density
- Specialized observability tooling, such as a SIEM that can perform aggregation and threat intelligence
- Specialized networking appliances or capabilities, such as firewalls or distributed denial-of-service prevention
- Data classification tools required to capture the sensitivity and information type labels
- Specialized storage or compute capabilities to support encryption at rest and in transit, such as an HSM or confidential compute offerings
- Dedicated testing environments and testing tools to both validate security controls are functioning and to discover previously undiscovered gaps in coverage
- Items above often extend beyond production environments, and into pre-production and disaster recovery resources as well

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Increased demand on infrastructure.** Cost Optimization looks to drive down demand on resources to enable the usage of cheeper SKUs, fewer instances, or reduced consumption.

- *Premium SKUs*: Some security measures in cloud and vendor offerings that can benefit the security posture of a workload might only be found in higher cost SKUs or tiers.

- *Log storage*: Increased storage costs for broad-coverage, higher fidelity security monitoring and audit data. Security observability data is also often stored for longer periods of time than what is typically needed for operational insights.

- *Increased resource consumption*: In-process and on-host security controls can introduce additional demand for resources. Likewise, so can encryption for data at rest and in transit. In both cases potentially needing larger instance count or larger SKUs.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Increased process and operational costs.** Personnel process costs are part of the overall total cost of ownership and are factored into the workload's return on investment. Optimizing these costs is a recommendation of Cost Optimization.

- A more comprehensive and strict patch management regime leads to an increase in time and money spent on these routine tasks. This is often coupled with expectation of investing in preparedness for ad-hoc patching for zero-day exploits.

- Stricter access controls to reduce the risk of unauthorized access can lead to more complex user management and operational access.

- Security training and awareness for security tools and processes come at a cost not only to personnel time but also for materials, instructors, and potentially training environments.

- Complying with regulation might necessitate additional investments in supporting audits and generating compliance reporting.

- Building security incident response preparedness takes an investment of time through planning and drilling.

- Time to design and execute routine and ad-hoc processes associated with security, such as key or certificate rotation, needs to be allocated.

- Security validation as part of the SDLC usually brings specialized, sometimes paid, tooling. Results of tests also need time dedicated to both the prioritization of the results and the remediation of them.

- Hiring third-party security practitioners to perform white-box or black-box testing, including penetration testing services.

## Security tradeoffs with Operational Excellence

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Complications in observability and serviceability.** Operational Excellence requires architectures to be serviceable and observable. The most "serviceable" architectures are the ones that are the most transparent to everyone involved.

- Security benefits from having extensive logging to provide high fidelity insight into the workload for both alerting on deviations from baselines and for incident response. This can generate a significant volume of logs, which can make it harder to provide insights targeted at reliability or performance.

- When data masking compliance guidelines are followed, specific segments of logs or even large amounts of tabular data are redacted to protect confidentiality. A workload needs to evaluate how this observability gap might impact the ability to alert or be a hinderance in incident response.

- Strong resource segmentation increases observability complexity through requiring additional cross-service distributed tracing and correlation to capture flow traces. The segmentation also increases the surface area of compute and data to service.

- Some security controls, by design, impede access. During incident response, these controls may slow emergency access by workload operators. As such, incident response plans require more emphasis on planning and drilling to reach acceptable efficacy.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Decreased agility and increased complexity.** Workload teams measure their velocity so they can improve quality, frequency, and efficiency of delivery activities over time. Workload complexity factors into effort and risk involved in operations.

- Stricter change control and approval policies to reduce the risk of introducing security vulnerabilities can slow down the development and safe deployment of new features, yet the expectation of addressing security updates and patching could increase demand for more frequent deployments. Additionally, human-gated approval policies in operational processes could make it more difficult to automate that process.

- Security testing will result in findings that need to be prioritized, potentially blocking planned work.

- Routine, ad-hoc, and emergency processes might require audit logging for compliance requirements, increasing the rigidity of executing the process.

- Workload teams that increase complexity for identity management activities as the granularity of role definitions and assignments are increased.

- Increased number of routine operational tasks associated with security, such as certificate management, increase the number of processes to automate.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Increased coordination efforts.** A team that minimizes external points of contact and review can control their operations and timeline more effectively.

- As external compliance requirements increase, either from the organizational level or from external entities, so does the complexity of being able to achieve and prove compliance with auditors.

- Security involves specialties not typically found on workload teams. Those proficiencies are then often sourced from the organization or from third parties. In both cases, coordination of effort, access, and responsibility needs to be established.

- Compliance or organizational requirements often require maintained communications plans for responsible disclosure of breach, which must be factored into security coordination efforts.

## Security tradeoffs with Performance Efficiency

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Increased latency and overhead.** A workload that is performant reduces latency and optimizes by removing overhead.

- Inspection security controls, such as firewalls and content filters, are placed in line with the flows they are securing. Those flows are then subject to additional verification, which adds latency to those requests.

- Identity controls require each invocation of the controlled component to be verified explicitly, which consume extra compute cycles and could require network traversal for authorization.

- Encryption and decryption need dedicated compute cycles which requires additional time and system resources spent on those flows. Factors for this increase are usually correlated with algorithm complexity and generation of high-entropy and diverse initialization vectors (IV).

- As the extensiveness of logging increases so can the impact on system resources and network bandwidth to stream those logs.

- Resource segmentation will often introduce additional network hops in the workload's architecture.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: **Increased chance of misconfiguration.** Reliably obtaining performance targets depends on predictable implementations of the design.

A misconfiguration or over-extending of security controls can lead to performance impact through an inefficient configuration. Some examples of where security control configuration could impact performance are:

- Firewall rule ordering, complexity, and quantity (granularity)
- Not excluding key files from file integrity monitors or virus scanners leading to lock contention
- Web application firewalls doing deep packet inspection for languages or platforms that are irrelevant for the components being protected

## Related links

Explore the tradeoffs for the other pillars:

- [Reliability tradeoffs](../reliability/tradeoffs.md)
- [Cost Optimization tradeoffs](../cost-optimization/tradeoffs.md)
- [Operational Excellence tradeoffs](../operational-excellence/tradeoffs.md)
- [Performance Efficiency tradeoffs](../performance-efficiency/tradeoffs.md)
