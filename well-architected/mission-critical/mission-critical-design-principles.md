---
title: Mission-critical workloads - AlwaysOn design principles
description: Key design principles that serve as a compass for subsequent design decisions across technical domains and the critical design areas.
author: calcof
ms.author: calcof
ms.date: 02/28/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
categories: web
products: azure
ms.custom:
  - mission-critical
  - alwayson
---

# Design principles

The five key design principles serve as a compass for subsequent architecture decisions across technical domains and the critical design areas. We highly recommend that you familiarize yourselves with these principles to better understand their impact and the trade-offs associated with non-adherence.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected mission-critical workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [What is a mission-critical workload?](mission-critical-overview.md#what-is-a-mission-critical-workload).
>
> ![GitHub logo](./../_images/github.svg) [AlwaysOn open source project](http://github.com/azure/alwayson)
>
> The [reference implementations](mission-critical-overview.md#illustrative-examples) are part of an open source project available on GitHub. The code assets illustrate the implementations associated with the design principles highlighted in this article.


At the core, design decisions for a mission critical architecture is based on the quality pillars of the Azure Well-Architected Framework&mdash;[Reliability](/azure/architecture/framework/#reliability), [Peformance Efficiency](/azure/architecture/framework/scalability/), [Operational Excellence](/azure/architecture/framework/devops/), [Security](/azure/architecture/framework/security/). This article reinforces the importance of the design principles for each pillar in the context of building a reliable and scalable workload.

![Mission critical design principles](./images/mission-critical-design-principles.png "Mission critical Design Principles")

This article extends the guidance to include principles for achieving a cloud native design.

## Reliability

A mission critical workload strives for *maximum reliability*. Design for the most reliable solution, ensuring trade-offs are properly understood.

|Design principle|Considerations|
|---|---|
|[**Design for failure**](/azure/architecture/framework/resiliency/principles#design-for-failure)|Evaluate potential failures in the critical path to avoid single points of failure by building redundancy. This principle not only applies to each component of the architecture but also the entire regional deployment.|
|[**Observe application health**](/azure/architecture/framework/resiliency/principles#observe-application-health)|Detect and predict issues early by monitoring application health against a known baseline. In case of failures, have remedial actions ready to address the issues.|
|[**Drive automation**](/azure/architecture/framework/resiliency/principles#drive-automation)|Minimize errors caused by human interactions by automating actions related to testing, deployment, and management.|
|[**Design for self-healing**](/azure/architecture/framework/resiliency/principles#design-for-self-healing)|Build  capabilities in the application design to gracefully recover on its own without needing external intervention. |


## Performance Efficiency

A mission critical workload strives for _sustainable performance and scalability_. Design for scalability across the end-to-end solution without performance bottlenecks.

|Design principle|Considerations|
|---|---|
|[**Design for scale-out**](/azure/architecture/framework/resiliency/principles#design-for-scale-out)|Identity the scale-out requirements for each component in the architecture. As load increases, the workload may experience resource exhaustion. To be highly reliable, the application must be able to handle sudden surges. Aggressively add capacity through scale units. |
|[**Model capacity**](/azure/architecture/framework/scalability/test-checklist)|Conduct load and performance tests to determine the expected performance under various load profiles as load can be impacted by several factors. This capacity model enables testing variations to ensure that your application can scale. It will help you determine how components perform in relation to each other, therefore enabling system-wide capacity allocation planning. Also, ensure that all regions can adequately scale to support total load, should one region fail.|
|[**Test and experiment often**](/azure/architecture/framework/scalability/performance-test)|Test each major change and on a regular cadence. Perform tests in Dev/Test environments and run a subset of critical tests against the production environment. Ongoing testing validates existing thresholds, targets and assumptions and will help to quickly identify risks to resiliency and availability.|
|[**Baseline performance and identify bottlenecks**](/azure/architecture/framework/scalability/test-tools#identify-baselines-and-goals-for-performance)| Establish baselines to stay on track and measure progress with detailed telemetry from every component. This will help you identify bottlenecks within the system and the components that need to be scaled. Baselines can influence decisions made during the capacity planning.|
|[**Reduce overhead with managed compute services**](/azure/architecture/guide/design-principles/managed-services)| Opt for containerized and serverless architectures to reduce the ongoing administrative and operational overhead of designing, operating, and scaling applications. These services shift infrastructure deployment and maintenance to the managed service provider.|

## Operational Excellence

A mission critical workload strives to _build operations as part of the overall design_. Incorporate engineering practices that are meant last with robust and assertive operational management.

|Design principle|Considerations|
|---|---|
|[**Loosely coupled components**](/azure/architecture/framework/devops/principles#use-loosely-coupled-architecture)|Enable independent and on-demand testing, deployments, and updates to components of the application while minimizing inter-team dependencies for support, services, resources, or approvals.|
|[**Optimize build and release process**](/azure/architecture/framework/devops/principles#optimize-build-and-release-processes)|Automate build and release processes to increase the velocity of deploying updates, bringing repeatability and consistency across environments. Automation shortens the feedback loop from developers pushing changes to getting automated near instantaneous insights on code quality, test coverage, security, and performance, which increases developer productivity and team velocity.|
|[**Understand operational health**](/azure/architecture/framework/devops/principles#understand-operational-health)|Have diagnostic instrumentation of all components and resources to enable ongoing observability of logs, metrics and traces. This approach will help quantify application health for availability and performance requirements.|
|[**Rehearse recovery and practice failure**](/azure/architecture/framework/devops/principles#rehearse-recovery-and-practice-failure)|Conduct business continuity (BC) and disaster recovery (DR) planning and practice drills periodically. Learnings from drills can iteratively improve plans and procedures to maximize resiliency in the event of unplanned downtime.|
|[**Embrace continuous operational improvement**](/azure/architecture/framework/devops/principles#embrace-continuous-operational-improvement)|Prioritize routine improvement of the system and user experience, using a health model to understand and measure operational efficiency with feedback mechanisms to enable application teams to understand and address gaps in an iterative manner.|

## Security
A mission critical workload strives to be _always secure_. Design for end-to-end security with the Zero-Trust model to maintain application stability and ensure availability.

|Design principle|Considerations|
|---|---|
|[**Monitor the security of the entire solution and plan incident responses**](/azure/architecture/framework/security/security-principles#monitor-system-security-plan-incident-response)|Correlate security and audit events to identify active threats. Centralizing all log data, in a Security Information and Event Management (SIEM) too, provides a single point of observability making it easier to consume data for hunting activities, querying, and statistical evaluation.|
|[**Model and test against potential threats**](/azure/architecture/framework/devops/principles#optimize-build-and-release-processes)|Ensure appropriate resource hardening and establish procedures to identify and mitigate known threats, using penetration testing to verify threat mitigation, as well as static code analysis and code scanning.|
|[**Identify and protect endpoints**](/azure/architecture/framework/devops/principles#understand-operational-health)|Monitor and protect internal and external endpoints through platform security capabilities and appliances. Common attack vectors like Distributed Denial-Of-Service (DDoS) can take down a system making it unavailable.|
|[**Protect against code level vulnerabilities**](/azure/architecture/framework/devops/principles#rehearse-recovery-and-practice-failure)|Identify code-level vulnerabilities and mitigate by incorporating security patching into operational lifecycles for all parts of the codebase.|
|[**Automate and use least privilege**](/azure/architecture/framework/devops/principles#embrace-continuous-operational-improvement)|Minimize the need for human interaction through automation and implement least privilege across both the application and control plane to protect against data exfiltration attacks.|
|[**Classify and encrypt data**](/azure/architecture/framework/security/security-principles#classify-and-encrypt-data)|Categorize data based on your business needs, compliance requirements, and the type of data. Apply encryption on data in transit and at rest. Make sure keys and certificates are stored securely and managed properly. |

## Cloud native design

- [**Azure-native managed services**](/azure/architecture/guide/design-principles/managed-services) - Azure-native managed services are prioritized due to their lower administrative and operational overhead as well as tight integration with consistent configuration and instrumentation across the application stack.

- [**Roadmap alignment**](/azure/architecture/guide/design-principles/design-for-evolution) - Incorporate upcoming new and improved Azure service capabilities as they become Generally Available (GA) to stay close to the leading edge of Azure.

- **Embrace preview capabilities and mitigate known gaps** - While Generally Available (GA) services are prioritized for supportability, Azure service previews are actively explored for rapid incorporation, providing technical and actionable feedback to Azure product groups to address gaps.

- **Azure Landing Zone alignment** - Deployable within an [Azure Landing Zone](/azure/cloud-adoption-framework/ready/landing-zone) and aligned to the Azure Landing Zone design methodology, but also fully functional and deployable in a bare environment outside of a landing zone.

## Next step

Explore the eight design areas that provide critical considerations and recommendations that are significant for building a mission-critical workload.

> [!div class="nextstepaction"]
> [Design areas](./mission-critical-design-areas.md)


