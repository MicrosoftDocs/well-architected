---
title: Design methodology for sustainability workloads on Azure
description: Understand the architectural process of building a sustainable application on Microsoft Azure.
author: Zimmergren
ms.author: tozimmergren
ms.date: 10/12/2022
ms.topic: conceptual
categories:
  - management-and-governance
  - web
ms.custom:
  - sustainability
  - ignite-2022
---

# Design methodology for sustainable workloads on Azure

Building a sustainable application on any cloud platform requires technical expertise and an understanding of sustainability guidelines in general and for your specific cloud platform.

This design methodology aims to help establish an understanding about producing more carbon efficient solutions, measuring your carbon impact, and ultimately reducing unnecessary energy usage and emissions.

## 1—Design for business requirements

Businesses globally have different requirements. Expect that the review considerations and design recommendations provided by this design methodology will yield different design decisions and trade-offs for different scenarios and organizations.

Establish your business requirements and priorities, then review the design methodologies in alignment with those requirements.

## 2—Evaluate the design areas using the design principles

Refer to the sustainability [design principles](sustainability-design-principles.md) and the design areas below for your sustainability workloads.

Decisions made within each design area will echo across other design areas. Review the considerations and recommendations in each design area to understand the consequences and impact and any known trade-offs.

Design areas:

- [Application design](sustainability-application-design.md)
- [Application platform](sustainability-application-platform.md)
- [Deployment and testing](sustainability-testing.md)
- [Operational procedures](sustainability-operational-procedures.md)
- [Storage](sustainability-storage.md)
- [Network and connectivity](sustainability-networking.md)
- [Security](sustainability-application-design.md)

## 3—Understanding your emissions

To lower your emissions, you need to understand how to measure your sustainability efforts.

### Briefly about emission scopes

At Microsoft, we segment our greenhouse gas (GHG) emissions into three categories, consistent with the [Greenhouse Gas Protocol](https://ghgprotocol.org/).

- **Scope 1 emissions**: direct emissions that your activities create.
- **Scope 2 emissions**: indirect emissions that result from the production of the electricity or heat you use.
- **Scope 3 emissions**: indirect emissions from all other activities you're engaged in. For a business, these Scope 3 emissions can be extensive. They must be accounted for across its supply chain, materials in its buildings, employee business travel, and the life cycle of its products (including the electricity customers consume when using the products). A company's Scope 3 emissions are often far more significant than its Scope 1 and 2 emissions combined.

As a customer, the context of Scope 3 emissions can be network configuration and delivery, power consumption, and devices outside the data center. If an application uses excess bandwidth or packet size, it will impact from when the traffic leaves the data center, through the various hops on the internet, down to the end-user device. Reducing network bandwidth, therefore, can have a significant impact throughout the delivery chain. The same considerations apply to compute resources, data storage, application platform decisions, application design, and more.

Find more in-depth details and definitions in [Azure's Scope 3 Methodology White Paper](https://download.microsoft.com/download/7/2/8/72830831-5d64-4f5c-9f51-e6e38ab1dd55/Microsoft_Scope_3_Emissions.pdf), published in 2021.

### Measure and track carbon impact

Microsoft aligns with the [Green Software Foundation](https://greensoftware.foundation), responsible for creating the [Software Carbon Intensity](https://learn.greensoftware.foundation/practitioner/measurement#the-sci-equation) (SCI) specification.

To measure the carbon impact of an application, the GSF provided a scoring methodology called SCI, calculated as follows:

```SCI = ((E*I)+M) per R```

Where:

- `E` = Energy consumed by a software system. Measured in kWh.
- `I` = Location-based marginal carbon emissions. Carbon emitted per kWh of energy, gCO2/kWh.
- `M` = Embodied emissions of a software system. Carbon that is emitted through the hardware on which the software is running.
- `R` = Functional unit, which is how the application scales; per extra user, per API call, per service, etc.

With this knowledge, it's essential to consider not only the application infrastructure and hardware but also the user devices and application scalability, as it can alter the environmental footprint considerably.

Read the full [SCI specification on GitHub](https://github.com/Green-Software-Foundation/software_carbon_intensity/blob/main/Software_Carbon_Intensity/Software_Carbon_Intensity_Specification.md).

#### Carbon tracking and reporting with the Emissions Impact Dashboard

Microsoft offers the [Emissions Impact Dashboard](https://www.microsoft.com/sustainability/emissions-impact-dashboard) for Azure and Microsoft 365, which helps you measure your cloud-based emissions and carbon savings potential.

We recommend you use this tool to get the insights and transparency you need to understand your carbon footprint and to measure and track emissions over time.

Download the [Emissions Impact Dashboard Power BI app for Azure](https://appsource.microsoft.com/product/power-bi/coi-sustainability.emissions_impact_dashboard) to get started.

#### Leverage the Microsoft Sustainability Manager

Customers using [Microsoft Cloud for Sustainability](/industry/sustainability/overview) can leverage [Microsoft Sustainability Manager](/industry/sustainability/sustainability-manager-overview). This extensible solution unifies data intelligence and provides comprehensive, integrated, and automated sustainability management for organizations at any stage of their sustainability journey. It automates manual processes, enabling organizations to record, report, and reduce their emissions more efficiently.

#### Use a proxy solution to measure emissions

One way of estimating the carbon emissions from workloads is to design a proxy solution architecture based on the SCI model [as described above](sustainability-design-methodology.md#measure-and-track-carbon-impact).

Defining the proxies for applications can be done in different ways. For example, using these variables:

- Any known carbon emission of the infrastructure
- The cost of the infrastructure
- Edge services and infrastructure carbon emissions
- The number of users that are concurrently using the application
- Metrics of the application to inform us about the performance over time

By designing an equation using the above variables, you can estimate the carbon score (an approximation), helping you understand if you're building sustainable solutions.

There's also the aspect of application performance. You can link performance to cost and carbon and assume this relationship yields a value. With this relation, you can simplify the view like this:

|Application performance|Application cost|Likely outcome|
|---|---|---|
|High|Unchanged|Optimized app|
|High|Lower|Optimized app|
|Unchanged/Lower|Higher|According to the green principles, a higher energy cost can cause higher carbon emissions. Therefore, you can assume that the app produces unnecessary carbon emissions.|
|High|High|The app may be producing unnecessary carbon|

Therefore, building a carbon score dashboard can make use of the following proxies:

- Cost
- Performance
- Carbon emissions of the infrastructure (if known/available)
- Usage over time (requests, users, API calls, etc.)
- Any extra measurement that is relevant to the application

## 4—The shared responsibility model for sustainability

Reducing emissions is a shared responsibility between the cloud provider and the customer designing and deploying applications on the platform.

### Ways to reduce emissions

Reducing carbon emissions can happen with three possible solutions:

- Carbon neutralization; compensating carbon emissions
- Carbon avoidance; not emitting carbon in the first place
- Carbon removal; subtract carbon from the atmosphere

The goal of green software is to avoid unnecessary emissions in the first place, hence actively working toward a more sustainable future. Further, _carbon removal_ is the preferred goal for removing emissions from our atmosphere.

Microsoft is committed to being [carbon negative by 2030](https://blogs.microsoft.com/blog/2020/01/16/microsoft-will-be-carbon-negative-by-2030/), and [by 2050 to have removed all the carbon](https://blogs.microsoft.com/blog/2021/10/27/supporting-our-customers-on-the-path-to-net-zero-the-microsoft-cloud-and-decarbonization/) the company has emitted since it was founded in 1975.

### A shared responsibility

As a cloud provider, Microsoft is responsible for the data centers hosting your applications.

However, deploying an application in the Microsoft cloud doesn't automatically make it sustainable, even if the data centers are optimized for sustainability. Applications that aren't optimized may still emit more carbon than necessary.

_Let's take an example._

You deploy an app to an Azure service, but you only utilize 10% of the allocated resources. The provisioned resources are underutilized, ultimately leading to unnecessary emissions.

It would help if you considered scaling to an appropriate tier of the resource (rightsizing) or deploying more apps to the same provisioned resources.

We recommend making applications more efficient to utilize the data center capacity in the best way possible. Sustainability is a shared responsibility goal that must combine the efforts of the cloud provider and the customers in designing and implementing applications.

## Next steps

Review the design principles for sustainability.

> [!div class="nextstepaction"]
> [Design principles](sustainability-design-principles.md)
