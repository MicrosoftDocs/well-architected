---
title: Carrier-grade design area - Testing and validation
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

# Design area: Testing and validation for carrier-grade workloads

Proving that an application is well architected requires testing, ideally using a chaos testing framework, which simulates failures of all dependent elements. Robust and regular testing should both prove the design and validate the original failure mode analysis.

A warning flag should be raised for any application or service for which the redundancy or resiliency measures can't be tested because it's considered *too risky*.

If redundancy and resiliency measures aren't tested, then the only valid assumption, from a safety-critical point of view, is that these measures aren't going to work when needed. Using common paths for software upgrades, configuration updates, and fault recovery, for example, provide a good mechanism for validating that measures will work.

## The risks of *lift* and *shift*

Telecommunication companies have well-architected applications that deliver the expected behavior on their existing infrastructure. However, care should be taken before assuming that porting these applications *as is* to a public cloud infrastructure won't impact their resiliency.

The existing applications make a set of assumptions about their underlying infrastructure, which are unlikely to remain true when moving from on-premises to public cloud. The architect must check that they still hold and adjust infrastructure design to accommodate the new reality. The architect should also look for opportunities where the new infrastructure removes limitations that existed on-premises.

For example, upgrade of on-premises systems must happen in place because it's not viable to maintain sufficient hardware to create a new deployment alongside and slowly transition in a safe manner. This upgrade path generates a host of requirements for how upgrades and rollbacks are managed. These requirements lead to complexity and mean that upgrades are infrequent and only permitted in carefully managed maintenance windows.

In public cloud, it's reasonable to create a new deployment in parallel with the existing deployment. This process creates the opportunity for major simplifications in the application's operational design and improvements in the user's experience, and expectations.

## Clients

Common client libraries are also part of the end-to-end system and need equivalent analysis and testing. Software issues in common client code that simultaneously impacts a proportion of the system clients will impact overall availability in the same way as application server-side issues.

## Next step

Review the architecture pattern for carrier-grade workloads.

> [!div class="nextstepaction"]
> [Architecture pattern](./carrier-grade-architecture-pattern.md)

