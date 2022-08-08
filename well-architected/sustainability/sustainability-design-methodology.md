---
title: Design methodology for sustainability workloads on Azure
description: Understand the architectural process of building a sustainable application on Microsoft Azure.
author: Zimmergren
ms.author: tozimmergren
ms.date: 07/27/2022
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

# Design methodology for sustainable workloads on Azure

[Intro paragraph]

## 1&mdash;Design for business requirements

Businesses globally have different requirements. Expect that the review considerations and design recommendations provided by this design methodology will yield different design decisions and trade-offs for different scenarios and organizations.

Establish your business requirements and priorities, then review the design methodologies in alignment with those requirements.

## 2&mdash;Evaluate the design areas using the design principles

Refer to the sustainability [design principles](sustainability-design-principles.md) and the design areas below for your sustainability workloads.

Decisions made within each design area will echo across other design areas. Review the considerations and recommendations in each design area to better understand the consequences and impact, and to understand any known trade-offs.

Design areas:

- [Application design](sustainability-application-design.md)
- [Application platform](sustainability-application-platform.md)
- [Deployment and testing](sustainability-deployment-testing.md)
- [Operational procedures](sustainability-operational-procedures.md)
- [Compute](sustainability-compute.md)
- [Storage](sustainability-storage.md)
- [Network and connectivity](sustainability-networking.md)
- [Security](sustainability-application-design.md)

## 3&mdash;Understanding your emissions

To lower our emissions, we need to understand how to measure our sustainability efforts.

### Measure and track carbon impact

Microsoft aligns with the [Green Software Foundation](https://greensoftware.foundation), responsible for creating the [Software Carbon Intensity](https://greensoftware.foundation/projects/software-carbon-intensity-sci-specification) (SCI) specification.

To measure the carbon impact of an application, the SCI is calculated like this:

```SCI = ((E*I)+M) per R```

Where:

- `E` = Energy consumed by a software system. Measured in kWh.
- `I` = Location-based marginal carbon emissions. Carbon emitted per kWh of energy, gCO2/kWh.
- `M` = Embodied emissions of a software system. Carbon emitted through the hardware on which the software is running.
- `R` = Functional unit, which is how the application scales; per extra user, per API-call, per service, etc.

With this knowledge, it's important to consider not only the application infrastructure and hardware, but also the user devices and how the application scales, as it can alter the environmental footprint considerably.

Read the full [SCI specification on GitHub](https://github.com/Green-Software-Foundation/software_carbon_intensity/blob/main/Software_Carbon_Intensity/Software_Carbon_Intensity_Specification.md).

#### Carbon tracking and reporting with the Emissions Impact Dashboard

Microsoft offers the [Emissions Impact Dashboard](https://www.microsoft.com/en-us/sustainability/emissions-impact-dashboard) for Azure and Microsoft 365, which help you measure your cloud-based emissions and carbon savings potential.

We recommend you use this tool to get the insights and transparency you need to understand your carbon footprint, and to measure and track emissions over time.

Download the [Emissions Impact Dashboard Power BI app for Azure](https://appsource.microsoft.com/product/power-bi/coi-sustainability.emissions_impact_dashboard) to get started.

#### Use a proxy solution to measure emissions

By designing a proxy solution architecture based on the SCI model, we can estimate the carbon emissions from our workloads.

Defining the proxies for applications can be done in different ways. For example, using these variables:

- Any know carbon emission of the infrastructure
- The cost of the infrastructure
- Edge services and infrastructure carbon emissions
- The number of users that are concurrently using the application
- Metrics of the application to inform us about the performance over time

By designing an equation using the above variables, you can create an approximation of the carbon score, helping you understand if you're building solutions that are sustainable.

There's also the aspect of application performance. You can link the performance to cost and carbon, and assume that their relation is a value. With this relation, you can simplify the view like this:

|Application performance|Application cost|Likely outcome|
|---|---|---|
|High|Unchanged|Optimized app|
|High|Lower|Optimized app|
|Unchanged/Lower|Higher|According to the green principles, a higher energy cost can be a cause for higher carbon emission. Therefore the app can be assumed to produce unnecessary carbon emissions.|
|High|High|The app may be producing unnecessary carbon|

Therefore, building a carbon score dashboard can make use of the following proxies:

- Cost
- Performance
- Carbon emissions of the infrastructure (if known/available)
- Usage over time (requests, users, API calls, etc.)
- Any extra measurement that is relevant to the application

## 4&mdash;The shared responsibility model for sustainability

Reducing emissions is a shared responsibility between the cloud provider and the customer designing and deploying applications on the platform.

### Ways to reduce emissions

In order to reduce carbon emissions, there are three possible solutions:

- Carbon neutralization; compensating carbon emissions
- Carbon avoidance; not emitting carbon in the first place
- Carbon removal; subtract carbon from the atmosphere

The goal of green software is to avoid emitting unnecessary emissions in the first place, hence actively working toward a more sustainable future. Further, _carbon removal_ is the preferred goal for removing emissions from our atmosphere.

Microsoft is committed to be [carbon negative by 2030](https://blogs.microsoft.com/blog/2020/01/16/microsoft-will-be-carbon-negative-by-2030/), and [by 2050 to have removed all the carbon](https://blogs.microsoft.com/blog/2021/10/27/supporting-our-customers-on-the-path-to-net-zero-the-microsoft-cloud-and-decarbonization/) the company has emitted since it was founded in 1975.

### A shared responsibility

As a cloud provider, Microsoft is responsible for the datacenters hosting your applications.

However, deploying an application in the Microsoft cloud doesn't automatically make it sustainable, even if the datacenters are being optimized for sustainability. Applications that aren't optimized may still emit more carbon than necessary.

The shared responsibility model for sustainable apps can be visualized like this:

:::image type="content" source="./images/sustainability-shared-responsibility-model.png" alt-text="Diagram showing the shared responsibility model for sustainability." border="false" lightbox="./images/sustainability-shared-responsibility-model.png":::

_Let's take an example._

You deploy an app to an Azure service, but your only utilize 10% of the allocated resources. The provisioned resources are underutilized, ultimately leading to unnecessary emissions.

You should consider scaling to an appropriate tier of the resource (rightsizing), or consider deploying more apps to the same provisioned resources.

Customers are recommended to make applications more efficient to utilize the datacenter capacity in the best way possible. Sustainability is a shared responsibility goal that needs to combine the efforts of both the cloud provider and the customers designing and implementing applications hosted in the global datacenters.

## Next steps

Review the design principles for sustainability.

> [!div class="nextstepaction"]
> [Design principles](sustainability-design-principles.md)