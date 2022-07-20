---
title: Design principles for sustainability workloads on Azure
description: Understand the design principles for building a sustainable application on Microsoft Azure.
author: v-stacywray
ms.author: robbymillsap
ms.date: 07/12/2022
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

# Sustainability design principles
The principles of cloud sustainability provide a framework to record, report and reduce the environmental impact of your workload.

To achieve an increase in carbon efficiency, consider how your workload directly, and indirectly, can reduce carbon emissions through:

*	Using less physical and virtual resources
*	Using less energy
*	Using energy and resources more intelligently
*	Supporting older devices

It is important to effectively record, report, and reduce carbon emissions through actionable insights.
*	Gain transparency into your current carbon impact
*	Estimate savings 
*	Take action to accelerate progress

These critical design principles are used as lenses to assess the sustainability of an application deployed to Azure. These lenses provide a framework for the application assessment questions.

## Measure and track carbon impact
According to the Green Software Foundation, to which Microsoft’s view is aligned on green software, the measurement for the carbon impact of an application is Software Carbon Intensity (SCI), and is calculated as follows:
```SCI = ((E*I)+M) per R```

Where: 
*	E	= energy consumed by software (kWh)
*	I	= carbon emitted per kWh of energy, gCO2/kWh
*	M	= carbon emitted through the hardware that software is running on
*	R	= Functional Unit; this is how the application is scaling, per user, per service, etc.…

Therefore, you should take into account not just the application infrastructure, but also the user devices and how the application scales, because it might have a larger impact than thought.
Let’s now review the main principles of green software before modelling our well architected sustainable application.

## Principles of green software
The principles of green software are the starting point to understand the SCI model and how this will be included in our framework.

### Carbon Efficient
The application or software must emit the least amount of carbon possible. A carbon efficient cloud application is one that is optimized, and the starting point is the cost – streamlining the application infrastructure and cost will ensure that no unnecessary resources are wasted in the cloud to run the software. But this is, obviously not enough, as you might have cost optimized your application but still waste tons of resources that emit carbon for no reason.

### Electricity
The goal of this principle is that you build applications that are energy-efficient. This is a common pattern for mobile applications, since they must rely on a battery powered device and are usually optimizing its consumption. It is less common, however, for desktop or web applications, since until now, developers have never been asked to optimize the electricity consumption of their software.

### Carbon Aware Computing
We need to make the application aware of how much carbon it’s emitting. This way, we can react to specific conditions of energy supply, using demand shifting and demand shaping techniques:
-	Demand shifting means moving the workloads and resources to regions or datacenters where the energy supply is higher (and incidentally the cost is lower)
-	Demand shaping means changing the application’s behavior and appearance to match the energy supply in real-time. A good practice is to build a eco-version of the app and keep it as benchmark for demand shaping but also for carbon optimization.
### Embodied Carbon
Embodied carbon is the carbon that was emitted to build a device. Therefore, a sustainable application will make sure older devices are supported and will maximize the efficiency of each device.  The goal is to build hardware-efficient applications.
### Reducing emissions
In order to reduce carbon emissions, we have three possible solutions:
*	Carbon neutralization – compensating carbon emissions
*	Carbon avoidance – not emitting carbon in the first place
*	Carbon removal – subtract carbon from the atmosphere
Carbon removal is referred to as “the only way to bring our atmospheric levels back to normal and undo the damage already done.” The goal of green software is to avoid emitting unnecessary emissions in the first place.
### Measuring
Measuring carbon emissions of a cloud application is a complex task, as it involves the whole ecosystem of the software: from the cloud infrastructure (where we have the emissions dashboards to help us out), to the network path that is crossed, to the edge technology and user devices. With the SCI we are not targeting a discrete measurement of carbon emissions, but a score that will change over time and with our optimization techniques.
## Shared responsibility model for carbon
As mentioned earlier, an application’s impact on carbon emission is not solely limited to the cloud infrastructure but will span several technologies and points of usage. 
The first step of the cloud efficiency (cost + carbon) paradigm is optimizing the cost of the application. Please refer to the cost optimization pillar for additional info.

We are already familiar with all the cost management guidelines.
Optimizing carbon emissions means to make your applications and architectures carbon aware, score them against a benchmark or proxy to measure any improvement or status change, and take any available actions to reduce emissions according to the situation and workload.

Optimizing your workload for energy means being aware of their energy requirements and have the software perform the same features with less electricity consumption.
Once you have an optimized and streamlined application, it’s time to work on its carbon and energy impact.

The cloud provider is obviously responsible for all the datacentre portion of the app: Microsoft has a sustainability goal and most of its Azure datacentres are driving towards a zero-emission goal (**more info or link here).
But putting an application in the cloud does not mean that automatically it is sustainable. Let’s pick an example: 
*	An application which is very heavy on carbon is using X resources on Azure
*	Ten highly optimized and efficient applications can run on the same X resources on Azure.
This means that even if we are using resources that are carbon optimized, we should not waste them and in principle, try to use them in the most efficient possible way. Just as resiliency, sustainability is a shared responsibility goal that needs to combine the efforts of both the Azure cloud provider and the customer or partner that is designing and implementing the application.
## Cloud efficiency process overview
### Optimize workload to reduce direct factors
Reduce the impact of compute by right sizing resources, introduce dynamic autoscaling to allocate and deallocate resources when needed, modernize your application components towards PaaS and serverless technologies. Consider running batch workloads during quiet hours, and upgrade frameworks to improve efficiency.
### Reduce indirect factors
Scope 3 emissions account for a large carbon impact through application delivery, reduce network bandwidth through compression, consider using a Content Delivery Network (CDN), and reducing asset sizes.
### Continuously re-evaluate and improve
Schedule regular reviews of the Emissions Impact Dashboard and your SCI score, set measures and scorecards, document success and forecast future impact
