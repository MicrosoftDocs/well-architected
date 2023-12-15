---
title: Mission-critical workloads
description: Overview of mission-critical workloads on Azure that are highly reliable.
author: calcof
ms.author: calcof
ms.date: 08/15/2022
ms.topic: conceptual
categories:
  - management-and-governance
  - web

---

# Mission-critical workloads

This section strives to address the challenges of designing mission-critical workloads on Azure. The guidance is based on lessons learned from reviewing numerous customer applications and first-party solutions. This section provides actionable and authoritative guidance that applies Well-Architected best practices as the technical foundation for building and operating a highly reliable solution on Azure at-scale.

## What is a mission-critical workload?

The term _workload_ refers to a collection of application resources that support a common business goal or the execution of a common business process, with multiple services, such as APIs and data stores, working together to deliver specific end-to-end functionality.

The term _mission-critical_ refers to a criticality scale that covers significant financial cost (business-critical) or human cost (safety-critical) associated with unavailability or underperformance.

A _mission-critical workload_ therefore describes a collection of application resources, which must be highly reliable on the platform. The workload must always be available, resilient to failures, and operational.

## Video: Mission-critical workloads on Azure

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE52ryK]

## What are the common challenges?

Microsoft Azure makes it easy to deploy and manage cloud solutions. However, building mission-critical workloads that are highly reliable on the platform remains a challenge for these main reasons:

- Designing a reliable application at scale is complex. It requires extensive platform knowledge to select the right technologies _and_ optimally configure them to deliver end-to-end functionality.

- Failure is inevitable in any complex distributed system, and the solution must therefore be architected to handle failures with correlated or cascading impact. This is a change in mindset for many developers and architects entering the cloud from an on-premises environment; reliability engineering is no longer an infrastructure subject, but should be a first-class concern within the application development process.

- Operationalizing mission-critical workloads requires a high degree of engineering rigor and maturity throughout the end-to-end engineering lifecycle as well as the ability to learn from failure.

## Is mission-critical only about reliability?

While the primary focus of mission-critical workloads is [Reliability](/azure/well-architected/#reliability), other pillars of the Well-Architected Framework are equally important when building and operating a mission-critical workload on Azure.  

- [Security](/azure/well-architected/security/): how a workload mitigates security threats, such as Distributed Denial of Service (DDoS) attacks, will have a significant bearing on overall reliability.

- [Operational Excellence](/azure/well-architected/devops/): how a workload is able to effectively respond to operational issues will have a direct impact on application availability. 

- [Performance Efficiency](/azure/well-architected/scalability/): availability is more than simple uptime, but rather a consistent level of application service and performance relative to a known healthy state.

Achieving high reliability imposes significant cost tradeoffs, which may not be justifiable for every workload scenario. It is therefore recommended that design decisions be driven by business requirements.

## What are the key design areas?

Mission-critical guidance within this series is composed of architectural considerations and recommendations orientated around these key design areas.

![Mission-critical design areas](./images/mission-critical-design-areas.svg "Mission-critical design areas")

The design areas are interrelated and decisions made within one area can impact or influence decisions across the entire design. We recommend that readers familiarize themselves with these design areas, reviewing provided considerations and recommendations to better understand the consequences of encompassed decisions. For example, to define a target architecture it's critical to determine how best to monitor application health across key components. In this instance, the reader should review the **health modeling** design area, using the outlined recommendations to help drive decisions.

|Design area|Summary|
|---|---|
|[**Application design**](mission-critical-application-design.md)|The use of a scale-unit architecture in the context of building a highly reliable application. Also explores the cloud application design patterns that allow for scaling, and error handling.|
|[**Application platform**](mission-critical-application-platform.md)| Decision factors and recommendations related to the selection, design, and configuration of an appropriate application hosting platform, application dependencies, frameworks, and libraries.|
|[**Data platform**](mission-critical-data-platform.md)|Choices in data store technologies, informed by evaluating the required&mdash;volume, velocity, variety, veracity. |
|[**Networking and connectivity**](mission-critical-networking-connectivity.md)|Network topology concepts at an application level, considering requisite connectivity and redundant traffic management. Critical recommendations intended to inform the design of a secure and scalable global network topology.|
|[**Health modeling and observability**](mission-critical-health-modeling.md)|Processes to define a robust health model, mapping quantified application health states through observability and operational constructs to achieve operational maturity.|
|[**Deployment and testing**](mission-critical-deployment-testing.md)| Eradicate downtime and maintain application health for deployment operations, providing key considerations and recommendations intended to inform the design of optimal CI/CD pipelines for a mission-critical application.|
|[**Security**](mission-critical-security.md)|Protect the application against threats intended to directly or indirectly compromise its reliability.|
|[**Operational procedures**](mission-critical-operational-procedures.md)|Adoption of DevOps and related deployment methods is used to drive effective and consistent operational procedures.|

## Illustrative examples

The guidance provided within this series is based on a solution-orientated approach to illustrate key design considerations and recommendations. There are several reference implementations available that can be used as a basis for further solution development.

- [Baseline architecture of an internet-facing application](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-intro)&mdash;Provides a foundation for building a cloud-native, highly scalable, internet-facing application on Microsoft Azure. The workload is accessed over a public endpoint and doesn't require private network connectivity to a surrounding organizational technical estate.

  > Refer to the implementation: [Mission-Critical Online](https://github.com/Azure/Mission-Critical-Online)

- [Baseline architecture of an internet-facing application with network controls](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-network-architecture)&mdash;Extends the baseline architecture with strict network controls in place to prevent unauthorized public access from the internet to any of the workload resources.

- [Baseline architecture in an Azure landing zone](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-landing-zone)&mdash;Provides a foundation for building a corporate-connected cloud-native application on Microsoft Azure using existing network infrastructure and private endpoints. The workload requires private connectivity to other organizational resources and takes a dependency on pre-provided Virtual Networks for connectivity to other organizational resources. This use case is intended for scenarios that require integration with a broader organizational technical estate for either public-facing or internal-facing workloads.

  > Refer to the implementation: [Mission-Critical Connected](https://github.com/Azure/Mission-Critical-Connected)
  
## Industry scenarios

The mission-critical guidance within this series forms an industry agnostic design methodology which can be applied across a multitude of different industry contexts. The following list provides specific examples where the mission-critical design methodology has been applied and tailored to a particular industry scenario.  

  - [Carrier-grade](/azure/well-architected/carrier-grade/carrier-grade-get-started) within the telecommunications industry

A carrier-grade workload pivots on both business-critical and safety-critical aspects, where there's a fundamental requirement to be operational with only minutes or even seconds of downtime per calendar year. Failure to achieve this uptime requirement can result in extensive loss of life, incur significant fines, or contractual penalties.

## Next step

Start by reviewing the design methodology for mission-critical application scenarios.

> [!div class="nextstepaction"]
> [Design methodology](mission-critical-design-methodology.md)
