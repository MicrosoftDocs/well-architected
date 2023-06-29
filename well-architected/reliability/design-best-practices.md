---
title: Designing reliability in Azure apps
description: Learn best practices for designing reliability in Azure applications, such as ensuring connectivity, using zone-aware services, and designing for scalability.
author: martinekuan
ms.author: martinek
ms.date: 04/26/2023
ms.topic: conceptual
---

# Best practices for designing reliability in Azure applications

This article describes best practices to design Azure applications for reliability. These best practices are derived from our experience with Azure reliability and the experiences of customers like yourself.

During the architectural phase, implement practices that meet your business requirements, identify failure points, and minimize the scope of failures.

## Build availability targets and recovery targets into your design

A service-level agreement (SLA) is an availability target that represents a commitment around performance and availability of the application. Understanding the SLA of individual components within the system is essential in order to define reliability targets. Recovery targets identify how long the workload can be unavailable and how much data is acceptable to lose during a disaster.

Define target reports for the application and key scenarios. There might be penalties, such as financial charges, associated with failing to meet SLA commitments. The consequences of not satisfying availability targets should be fully understood.

## Ensure the application and data platforms meet your reliability requirements

Designing application platform and data platform resiliency and availability are critical to ensure overall application reliability.

## Ensure connectivity

To ensure connection availability and improve reliability with Azure services:

- Use a global load balancer to distribute traffic and failover across regions.
- For cross-premises connectivity with Azure ExpressRoute or VPN, ensure that there are redundant connections from different locations.
- Simulate a failure path to ensure that connectivity is available over alternative paths.
- Eliminate all single points of failure from the data path, both on-premises and hosted by Azure.

## Use zone-aware services

Zone-aware services can improve reliability and ensure availability during failure scenarios that affect a datacenter within a region. They can also be used to deploy gateway instances across zones for improved reliability and availability during failure scenarios that affect a datacenter within a region.

## Design resilience to respond to outages

Applications should be designed to operate even when affected by regional, zonal, service, or component failures across critical application scenarios and functionality. Application operations might experience reduced functionality or degraded performance during an outage.

## Perform a failure mode analysis

Failure mode analysis (FMA) builds resiliency into an application early in the design stage. It helps you identify the types of failures that your application might experience, the potential effects of each, and possible recovery strategies.

Have all single points of failure been eliminated? A single point of failure describes a specific fault-point that, if it were to fail, would bring down the entire application. Single points of failure introduce significant risk since any failure of this component causes an application outage.

Have all fault-points and fault-modes been identified? Fault-points describe the elements within an application architecture that are capable of failing, while fault-modes capture the various ways by which a fault-point might fail. To ensure that an application is resilient to end-to-end failures, it's essential that all fault-points and fault-modes are understood and operationalized.

## Understand the effect of an outage with each dependency

Strong dependencies play a critical role in application function and availability. Their absence has a significant effect, while the absence of weak dependencies might only affect specific features and not affect overall availability. Dependencies can be categorized as either strong or weak based on whether the application is able to continue operating in a degraded fashion in their absence.

## Design for scalability

A cloud application must be able to scale to accommodate changes in usage. Begin with discrete components and design the application to respond automatically to load changes whenever possible. Keep scaling limits in mind during design so you can expand easily in the future.

## Next steps

> [!div class="nextstepaction"]
> [Testing for reliability](./test-checklist.md)

Go back to the main article: [Design for reliability](design-checklist.md)
