---
title: Sustainability workloads
description: Overview of sustainability workloads on Azure that are highly reliable.
author: Zimmergren
ms.author: tozimmergren
ms.date: 10/12/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
categories:
  - management-and-governance
  - web
products: Azure
ms.custom:
  - sustainability
---

# Sustainable workloads

This section of the Microsoft Azure Well-Architected Framework aims to address the challenges of building sustainable workloads on Azure. Review the provided guidance that applies Well-Architected best practices as a technical foundation for building and operating sustainable solutions on Azure.

We encourage you to also read more about the [Microsoft Cloud for Sustainability](https://www.microsoft.com/sustainability/cloud) for opportunities to leverage the capabilities of that platform as part of your solution architecture. However, guidance found in this article series is focused on _any_ solutions you're building or operating on Azure.

Additionally, read about [The Carbon Benefits of Cloud Computing: a Study of the Microsoft Cloud](https://www.microsoft.com/download/details.aspx?id=56950) to learn more about how Azure is more energy efficient and carbon efficient than on-premises solutions.

## What is a sustainable workload?

The term _workload_ refers to a collection of application resources that support a common business goal or the execution of a common business process, with multiple services, such as APIs and data stores, working together to deliver specific end-to-end functionality.

With _sustainability_, we refer to the environmental impact of our workloads.

A _sustainable workload_ therefore describes the practice of designing solutions that maximize utilization while minimizing waste, ultimately reducing the footprint on the environment.

### Cloud efficiency overview

Making workloads more cloud efficient requires combining efforts around cost optimization, reducing carbon emissions, and optimizing energy consumption. Optimizing the application's cost is the initial step in making workloads more sustainable.

Here's a conceptual overview of cloud efficiency in this context:

:::image type="content" source="./images/sustainability-shared-responsibility-cloud-efficiency.svg" alt-text="Diagram showing how to achieve cloud efficiency." border="false":::

Scoring and [measuring the cloud efficiency](sustainability-design-methodology.md#3understanding-your-emissions) is essential to understand whether changes tracked over time have any impact.

Learn about building more sustainable and efficient workloads by starting with the design area for sustainable [Application Design](sustainability-application-design.md).

## What are the common challenges?

Building and designing sustainable workloads on Microsoft Azure can be challenging for these main reasons:

- Understanding if your workloads are in alignment with sustainability targets. It requires assessments of current workloads to determine if they're designed in a sustainable way.
- Designing workloads that are natively environmentally friendly and optimized.
- Measuring and tracking the emissions of your workloads.

## Is sustainability only about performance and cost?

While [performance efficiency](/azure/architecture/framework/scalability/) and [cost optimization](/azure/architecture/framework/cost/) are areas of strong focus for designing sustainable workloads, the other pillars of the Well-Architected Framework are equally important when building long-term sustainable workloads on Azure.

- [Security](/azure/architecture/framework/security/): how the security appliances in a workload are optimized and designed to auto-scale will have an impact on the environment.
- [Reliability](/azure/architecture/framework/resiliency/): designing reliable workloads that meet sustainability guidelines from the Green Software Foundation can greatly reduce the workloads' carbon and electricity footprint.
- [Operational Excellence](/azure/architecture/framework/devops/): how a workload is able to effectively respond to operational issues can ultimately reduce carbon emissions.

## What are the key design areas?

Sustainable guidance within this series is composed of architectural considerations and recommendations oriented around these key design areas.

Decisions made in one design area can impact or influence decisions across the entire design. The focus is ultimately on building a sustainable solution to minimize the footprint and impact on the environment.

|Design area|Description|
|---|---|
|[Application design](sustainability-application-design.md)|Cloud application patterns that allow for designing sustainable workloads.|
|[Application platform](sustainability-application-platform.md)|Choices around hosting environment, dependencies, frameworks, and libraries.|
|[Testing](sustainability-testing.md)|Strategies for CI/CD pipelines and automation, and how to deliver more sustainable software testing.|
|[Operational procedures](sustainability-operational-procedures.md)|Processes related to sustainable operations.|
|[Storage](sustainability-storage.md)|Design choices for making the data storage options more sustainable.|
|[Network and connectivity](sustainability-networking.md)|Networking considerations that can help reduce traffic and amount of data transmitted to and from the application.|
|[Security](sustainability-security.md)|Relevant recommendations to design more efficient security solutions on Azure.|

We recommend that readers familiarize themselves with these design areas, reviewing provided considerations and recommendations to better understand the consequences of encompassed decisions.

## Next step

Review the sustainability design methodology.

> [!div class="nextstepaction"]
> [Design methodology](sustainability-design-methodology.md)
