---
title: Azure Well-Architected mission-critical workload
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
# Azure Well-Architected mission-critical workload

This section of the [Microsoft Azure Well-Architected Framework](/azure/architecture/framework) strives to address the challenge of building mission-critical workloads on Azure. It contains actionable and authoritative guidance that applies Well-Architected best practices as the technical foundation. 

## What is a mission-critical workload?

The term _workload_ refers to a set of components on a cloud platform, which collectively provides end-to-end functionality to one or multiple clients. A workload can be a collection of cloud resources that serve a single or multiple applications, APIs, and data stores working together to deliver a specific functionality. 

An example of a workload is a simple web catalog where end users can browse through a catalog of items, see details of an item, and post ratings and comments for items. The architecture can have these main components:

- Frontend web application used by both requestors and reviewers.
- Backend APIs to accept the request and return to the caller.
- Worker application that communicates with the data store.
- Data layer to store data related to each transaction 

In this example, the web catalog can fail at several points. The frontend might not be able to handle an increase in load, the API might not handle an error, resources might not be available due to an outage. 

If the business requirements allow for lower reliability targets, occasional failures might be acceptable. However, if business requirements demand a much higher level of reliability and operational rigor, the design decisions will be different resulting in a different target architecture.

A _mission-critical workload_ is a workload that is highly reliable on the platform. This means the workload should be resilient to most failures and always be operational and available. For example, a mission-critical application with a 99.999% availability Service Level Objective (SLO) may need components to be distributed globally than another application with an SLO of 99.9%. 

## What are the common challenges?

Failure is inevitable in any complex distributed system and designing such a workload is challenging because,

- Designing at scale is complex. It requires extensive platform knowledge about selecting the right technologies _and_ optimally configuring them to deliver the end-to-end functionality.
- Designing with focus on handling failures and evaluating the correlated or cascading impact requires a change in mindset for many developers and architects who are new to the cloud. 
- Operationalizing requires a high degree of engineering rigor and maturity throughout the end-to-end engineering lifecycle and the ability to learn from failure.

## Is it only about reliability?

While the primary focus is [Reliability](/azure/architecture/framework/#reliability), other pillars of the Well-Architected Framework&mdash;[Peformance Efficiency](/azure/architecture/framework/scalability/), [Operational Excellence](/azure/architecture/framework/devops/), [Security](/azure/architecture/framework/security/), and [Cost Optimization](/azure/architecture/framework/cost/), are equally important for building and operating a mission-critical workload on Azure at-scale.  

A common approach for mission-critical workload is a globally distributed and highly scalable architecture. However, achieving high reliability comes at a development cost and may not be justifiable for every workload. So, we highly recommend that design decisions are driven by business requirements but informed by the opinionated guidance provided in this section.

## What are the key design areas?

Mission-critical guidance is composed of architectural considerations and recommendations around these key design areas.

![AlwaysOn Design Areas](./images/alwayson-design-areas.png "AlwaysOn Critical Design Areas")

|Design area|About|
|---|---|
|Application design| Cloud application design patterns that allow for scaling, and error handling. 	|
|Hosting infrastructure| Hosting environment choices, application dependencies, frameworks, and libraries.	|
|Networking| Network topology considerations at an application level, considering requisite connectivity and redundant traffic management.|
|Data storage|Choices in data store technologies by evaluating the volume, velocity, variety, and veracity characteristics.|
|Monitoring|Observability considerations through raw monitoring logs and metrics to determine the overall health.	|
|Deployment| Strategies for CI/CD pipelines and automation considerations.	|
|Testing| Scenarios, such as unit, build, static, security, integration, regression, UX, performance, capacity and failure injection (chaos). |
|Security|Mitigation of attack vectors through Microsoft Zero Trust model.|
|Operations|Processes related to deployment, key management, patching and updates.|

## Are there any illustrative examples?

The guidance is based on a solution-orientated approach to illustrate the key design considerations and recommendations of mission-critical application. 

> [!IMPORTANT]
> The reference implementations are part of an open source project available on GitHub.  
> ![GitHub logo](./../_images/github.png) [AlwaysOn open source project](http://github.com/azure/alwayson).

There are two foundational implementations that serve as a basis for further solution development:

- [Azure AlwaysOn Foundational Online](https://github.com/azure/alwayson-foundational-online)

  The application is accessed over a public endpoint and doesn't require private network connectivity to a surrounding organizational technical estate. The pipeline deploys the application Azure Subscription security and compliance guardrails and has no network connectivity requirements. 

- [Azure AlwaysOn Foundational Connected](https://github.com/azure/alwayson-foundational-connected) 

  The workload needs connectivity to other company resources. The pipeline deploys the application Azure Subscription security and compliance guardrails and takes a dependency on pre-provided Virtual Networks for connectivity to other company resources. This is usually achieved through a hub-and-spoke model through peering. This use case is intended for scenarios that require integration with an organizational technical estate for either public-facing or internal-facing workloads.


## Next step

Review the design methodology for mission-critical application scenarios.

> [!div class="nextstepaction"]
> [Design methodology](./alwayson-design-methodology.md)



Compared to reviewing the whole Azure landscape of an organization, this focus allows us to go deeper into the workload and architecture and provide more relevant recommendations, which are quite often transferrable to other workloads within the same customer as well.
![Azure Well-Architected mission-critical workload](./images/alwayson-waf-banner.png "Always On Mission Critical Applications")