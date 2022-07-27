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

Business globally have different requirements. Expect that the review considerations and design recommendations provided by this design methodology will yield different design decisions and trade-offs for different scenarios and organizations.

Establish your business requirements and priorities, then review the design methodologies in alignment with those requirements.

## 2&mdash;Evaluate the design areas using the design principles

Refer to the critical [design principles](sustainability-design-principles.md) and the rudimentary design areas below, which are correlative design decisions for your sustainability workloads.

Decisions made within each design area will echo across other design areas. Review the considerations and recommendations in each design area to better understand the consequences and impact, and to understand trade-offs within the design areas.

Design areas:

- [Compute](sustainability-compute.md)
- [Operations](sustainability-operations.md)
- [Deployment &amp; DevOps](sustainability-deployment-devops.md)
- [Networking](sustainability-networking.md)
- [Storage](sustainability-storage.md)
- [Application Design](sustainability-application-design.md)
- [Security](sustainability-application-design.md)

## 3&mdash;Refer to the sustainability design principles

> TODO: Add a link back to the design principles page.
> Add the actual principles/recommendations on that page, too, for now.

## 4&mdash;Understanding your emissions and responsibilities

### Measure and track carbon impact

According to the Green Software Foundation, to which Microsoft's view is aligned on green software, the measurement for the carbon impact of an application is Software Carbon Intensity (SCI), and is calculated as follows:

```SCI = ((E*I)+M) per R```

Where:

- E = energy consumed by software (kWh)
- I = carbon emitted per kWh of energy, gCO2/kWh
- M = carbon emitted through the hardware that software is running on
- R = Functional Unit, which is how the application is scaling: per user, per service, etc.

Therefore, you should take into account not just the application infrastructure, but also the user devices and how the application scales, because it might have a larger impact than thought.

Review the main [design principles for sustainable workloads on Azure](sustainability-design-principles.md) to learn how you can build and optimize your solutions in alignment with the Green Software Foundation.

### Shared responsibility model for carbon

As mentioned earlier, an application’s impact on carbon emission isn't solely limited to the cloud infrastructure but will span several technologies and points of usage.
The first step of the cloud efficiency (cost + carbon) paradigm is optimizing the cost of the application. Refer to the cost optimization pillar for more info.

We're already familiar with all the cost management guidelines.
Optimizing carbon emissions means to make your applications and architectures carbon aware, score them against a benchmark or proxy to measure any improvement or status change, and take any available actions to reduce emissions according to the situation and workload.

Optimizing your workload for energy means being aware of their energy requirements and have the software perform the same features with less electricity consumption.
Once you have an optimized and streamlined application, it’s time to work on its carbon and energy impact.

The cloud provider is responsible for all the datacenter portion of the app: Microsoft has a sustainability goal and most of its Azure datacenters are driving towards a zero-emission goal (**more info or link here).
But putting an application in the cloud doesn't mean that automatically it's sustainable. Let’s pick an example:

- An application that is heavy on carbon is using X resources on Azure
- 10 highly optimized and efficient applications can run on the same X resources on Azure.

This means that even if we're using resources that are carbon optimized, we shouldn't waste them and in principle, try to use them in the most efficient possible way. Just as resiliency, sustainability is a shared responsibility goal that needs to combine the efforts of both the Azure cloud provider and the customer or partner that is designing and implementing the application.

## 5&mdash;Workload optimization to reduce environmental impact

### Reduce direct factors

Reduce the impact of compute by right sizing resources, introduce dynamic autoscaling to allocate and deallocate resources when needed, modernize your application components towards PaaS and serverless technologies. Consider running batch workloads during quiet hours, and upgrade frameworks to improve efficiency.

### Reduce indirect factors

Scope 3 emissions account for a large carbon impact through application delivery, reduce network bandwidth through compression, consider using a Content Delivery Network (CDN), and reducing asset sizes.

### Continuously reevaluate and improve

Schedule regular reviews of the Emissions Impact Dashboard and your SCI score, set measures and scorecards, document success and forecast future impact.

## 6&mdash;Proxy implementations for sustainability

### Defining proxies for your application

The carbon emission azure spend is certainly your starting point, but of course is not enough. Until we have clear carbon values available for each software and infrastructure, we can rely on proxies to create a score model that resembles the SCI.

In general, the approach should be one to build a proxy’s equation with a few variables:

- The carbon emission of the infrastructure, if available
- The cost of the infrastructure
- The edge services and/or infrastructure’s carbon emissions
- The number of users that are concurrently hooked into the application
- Ideally, the time when users connect, as electricity production varies with time
- Any other peculiar metric of the app that can tell us how its performance is changing across time

Building this equation into a score that can also reflect the number of users, represents the closest approximation to a carbon score, and this will be your benchmark for any further change and improvement towards the greenness of the app.

There is also another aspect that is linked to the application performance metrics. If we link performances to cost and carbon and assume that their relation is a value, then we can assume that:

- When performances are higher, but costs are the same = we have optimized the app
- When costs are lower, but performances are the same = we have optimized the app
- When perf are up and costs are also up = we are also using more carbon emissions
- When costs are up even at lower or equal performances = we assume we are using more carbon emissions (or the energy cost is higher, which according to principles is also cause for higher carbon emissions

Your carbon score dashboard can therefore make use of the following proxies:

- Cost
- Performance
- Carbon emissions – where available
- Usage (requests, calls, users) across time
- Any additional measurement that is relevant to the application

A sample architecture of these proxies can be summarized with the following pic:

> Design for the architecture.

### The app eco-mode version

Once you know what points of carbon failure your app architecture might have, and are able to score its impact with proxies, you should understand what actions a different/higher carbon score might trigger:

- For example, I might have a situation where the energy production is at an all-time high and is therefore very expensive to produce
- Another example could be that electricity is simply not available – if you think of the war in Ukraine, we had lots of customers migrating quickly to the cloud because their datacenters were shutting down, so one compelling event for carbon saving is no electricity at all! This might apply to on-prem apps and CDNs and edge technologies as well.
- Can your app deal with sudden unavailability of the edge infrastructure?
- How can you ensure a decent quality of customer experience to your users?

Once you are able to build these failure points, the next step is to decide what actions make your application resilient to carbon spikes.

You should be able to build a simpler, smaller, cheaper, greenest version of the full application, that you can revert to in case of any spike in carbon emissions, or simply train your end-user to opt for the eco version by choice, providing a “green button” where people declare they are ok with a leaner interface, less graphics, and limited features in exchange of saving a bunch of carbon emissions.

If you opt to involve the users, this might involve a cultural change along with the technical one:

- You can specify what the impact of this choice is: “by using the green version you are saving x amount of carbon”, or “bringing our carbon score to Y”
- You can understand the user behavior and modify the eco version to reflect their choices (maybe they use 10%of the features, and they can be enough for the green version)
- Slowly the full version gets optimized for emission as well and ideally, they should be able to converge in a few months/years.

## Next steps
