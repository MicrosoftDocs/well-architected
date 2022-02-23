---
title: Mission-critical workloads
description: Overview of mission-critical solutions on Azure that are highly reliable.
author: calcof
ms.author: calcof
ms.date: 02/28/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
categories:
  - management-and-governance
  - web
products: Azure
ms.custom:
  - mission-critical
---
# Mission-critical workloads

This section of the [Microsoft Azure Well-Architected Framework](/azure/architecture/framework) strives to address the challenges of building mission-critical workloads on Azure. The guidance is based on lessons learned after reviewing numerous customer applications and first-party solutions. This section provides actionable and authoritative guidance that applies Well-Architected best practices as the technical foundation for building and operating a highly reliable solution on Azure at-scale.

## What is a mission-critical workload?

The term _workload_ refers to a collection of application resources that serve a particular technical or business purpose, with multiple services, APIs, and data stores working together to deliver specific end-to-end functionality.

A _mission-critical workload_ is a workload that must be highly reliable on the platform. This means the workload should be resilient to most failures and always be operational and available.

Consider a web catalog as a simple example of a workload. End users can browse through a catalog of items, see details of an item, and post ratings and comments for items. One approach is to divide the work into these components:

- Frontend web application used by both requestors and reviewers.
- Backend APIs to accept the request and return to the caller.
- Worker application that communicates with the data store.
- Data layer to store data related to each transaction.

In this example, the workload can fail at several points. The frontend might not be able to handle an increase in load, the API might not handle errors properly, resources might not be available due to an outage.

If the business requirements allow for lower reliability targets, occasional failures might be acceptable. However, if the requirements demand a much higher level of reliability and operational rigor; the design decisions will be different, resulting in a significantly different target architecture. For example, a mission-critical application with a 99.999% availability Service Level Objective (SLO) may need components to be distributed globally than another application with an SLO of 99.9%.

## What are the common challenges?

Microsoft Azure makes it easy to deploy and manage cloud solutions. However, building mission-critical workloads that are highly reliable on the platform remains a challenge for these main reasons:

- Designing a reliable application at scale is complex. It requires extensive platform knowledge to select the right technologies _and_ optimally configure them to deliver end-to-end functionality.

- Failure is inevitable in any complex distributed system, and the solution must therefore be architected to handle failures with correlated or cascading impact. This is a change in mindset for many developers and architects entering the cloud from an on-premises environment; reliability engineering is no longer an infrastructure topic, but should be a first-class concern within the application development process.

- Operationalizing mission-critical workloads requires a high degree of engineering rigor and maturity throughout the end-to-end engineering lifecycle as well as the ability to learn from failure.

## Is it only about reliability?

While the primary focus is [Reliability](/azure/architecture/framework/#reliability), other pillars of the Well-Architected Framework&mdash;[Peformance Efficiency](/azure/architecture/framework/scalability/), [Operational Excellence](/azure/architecture/framework/devops/), [Security](/azure/architecture/framework/security/), and [Cost Optimization](/azure/architecture/framework/cost/), are equally important for building and operating a mission-critical workload on Azure at-scale.  

A common approach is a globally distributed and highly scalable architecture. However, achieving high reliability comes at a development cost and may not be justifiable for every workload. So, we highly recommend that design decisions are driven by business requirements but informed by the opinionated guidance provided in this section.

## What are the key design areas?

Mission-critical guidance is composed of architectural considerations and recommendations around these key design areas.

![AlwaysOn Design Areas](./images/alwayson-design-areas.png "AlwaysOn Critical Design Areas")

- **Application design**&mdash;Cloud application design patterns that allow for scaling, and error handling. 
- **Application platform**&mdash;Hosting environment choices, application dependencies, frameworks, and libraries.
- **Data platform**&mdash;Choices in data store technologies by evaluating the volume, velocity, variety, and veracity characteristics.
- **Networking and Connectivity**&mdash;Network topology considerations at an application level, considering requisite connectivity and redundant traffic management.
- **Health Modelling**&mdash;Observability considerations through raw monitoring logs and metrics to determine the overall health.
- **Deployment and testing**&mdash;Strategies for CI/CD pipelines and automation considerations. Incorporating testing scenarios, such as unit, build, static, security, integration, regression, UX, performance, capacity and failure injection (chaos).
- **Security**&mdash;Mitigation of attack vectors through Microsoft Zero Trust model.
- **Operational procedures**&mdash; Processes related to deployment, key management, patching and updates.

## Illustrative examples

The guidance is based on a solution-orientated approach to illustrate the key design considerations and recommendations. 

There are two foundational implementations that serve as a basis for further solution development.

> [!IMPORTANT]
> The reference implementations are part of an open source project available on GitHub.  
> ![GitHub logo](./../_images/github.svg) [AlwaysOn open source project](http://github.com/azure/alwayson).

- [Azure AlwaysOn Foundational Online](https://github.com/azure/alwayson-foundational-online)

  The application is accessed over a public endpoint and doesn't require private network connectivity to a surrounding organizational technical estate. The pipeline deploys the application Azure Subscription security and compliance guardrails and has no network connectivity requirements. 

- [Azure AlwaysOn Foundational Connected](https://github.com/azure/alwayson-foundational-connected) 

  The workload needs connectivity to other company resources. The pipeline deploys the application Azure Subscription security and compliance guardrails and takes a dependency on pre-provided Virtual Networks for connectivity to other company resources. This is usually achieved through a hub-and-spoke model through peering. This use case is intended for scenarios that require integration with an organizational technical estate for either public-facing or internal-facing workloads.

## Next step

Start by reviewing the design methodology for mission-critical application scenarios.

> [!div class="nextstepaction"]
> [Design methodology](./alwayson-design-methodology.md)
