---
title: Testing and validation for carrier-grade workloads
description: This article provides an overview of the Testing and validation design area for carrier-grade workloads.
author: mikedell73
ms.author: mikedell
ms.date: 07/01/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.industry: telecommunications
products: 
- azure-app-service
- azure-traffic-manager
- cosmos-db
ms.custom:
  - carrier-grade
---

# Testing and validation for carrier-grade workloads

Continuous testing and validation can detect and help resolve issues before they become potentially life threatening. Consider well-known testing methodologies such as chaos testing. Testing should be conducted for the lifetime of the application because the deployment environment is complex and multi-layered.

Also, supportability must be strong throughout the application lifetime. Highly available systems rely on high quality support teams able to rapidly respond to and resolve issues in the field, conduct root cause analysis and look for systematic design flaws.

Proving that an application is well architected requires testing, ideally use a chaos testing framework to avoid testing bias. This methodology simulates failures of all dependent elements. Robust and regular testing should both prove the design and validate the original failure mode analysis.

A warning flag should be raised for any application or service for which the redundancy or resiliency measures can't be tested because it's considered *too risky*.

If redundancy and resiliency measures aren't tested, then the only valid assumption, from a safety-critical point of view, is that these measures aren't going to work when needed. Using common paths for software upgrades, configuration updates, and fault recovery, for example, provide a good mechanism for validating that measures will work.


## Clients

Common client libraries are also part of the end-to-end system and need equivalent analysis and testing. Software issues in common client code that simultaneously impacts a proportion of the system clients will impact overall availability in the same way as application server-side issues.

## Next step

Revisit the five pillars of architectural excellence to form a solid foundation for your carrier-grade workloads.

> [!div class="nextstepaction"]
> [ Azure Well-Architected Framework](../index.md)

