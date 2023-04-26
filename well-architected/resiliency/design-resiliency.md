---
title: Resiliency and dependencies
description: Learn about using failure mode analysis to build reliability. Understand the effect of dependencies on failure.
author: martinekuan
ms.author: martinek
ms.date: 4/25/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-pillar-reliability
---

# Resiliency and dependencies

To avoid the risk of failure, building failure recovery into the system should be part of the architecture and design phases from the beginning. Dependencies are required for the application to fully operate.

## Key points

- Identify possible failure points in the system with failure mode analysis.
- Eliminate all single points of failure.
- Maintain a complete list of application dependencies.
- Ensure that applications can operate in the absence of their dependencies.
- Understand the service-level agreements (SLAs) of individual components in the system to define reliability targets.

## Build resiliency with failure mode analysis

Failure mode analysis (FMA) is a process for building resiliency into a system by identifying possible failure points in the system. FMA should be part of the architecture and design phases. Build failure recovery into the system from the beginning.

*Fault points* describe the elements in an application architecture that are capable of failing. *Fault modes* capture the various ways by which a fault point might fail. Identify all fault points and fault modes. To ensure that an application is resilient to end-to-end failures, it's essential that all fault points and fault modes are understood and operationalized. For more information, see [Failure mode analysis for Azure applications](/azure/architecture/resiliency/failure-mode-analysis).

Eliminate all single points of failure. A single point of failure describes a specific fault point that, if it were to fail, would bring down the entire application. Single points of failure introduce significant risk since any failure of such a component causes an application outage. For more information, see [Make all things redundant](/azure/architecture/guide/design-principles/redundancy).

> [!NOTE]
> Eliminate all *singletons*. A singleton describes a logical component in an application for which there can only be a single instance. This definition can apply to stateful architectural components or application code constructs.
>
> Singletons introduce a significant risk by creating single points of failure in the application design.

## Understand the impact of dependencies

*Internal* dependencies describe components in the application scope that are required for the application to fully operate. *External* dependencies capture required components outside the scope of the application, such as another application or third-party service. Dependencies might be categorized as either strong or weak based on whether the application is able to continue operating in a degraded fashion in their absence. For more information, see [Twelve-Factor App: Dependencies](https://12factor.net/dependencies).

You should maintain a complete list of application dependencies. Examples of typical dependencies include platform dependencies outside the scope of the application, such as Azure Active Directory, Azure ExpressRoute, or a central Network Virtual Appliance (NVA), and application dependencies such as APIs. For cost purposes, it's important to understand the price for these services and how the services are being charged. For more information, see [Develop a cost model](../cost/design-model.md).

You can map application dependencies either as a simple list or a document. Usually this decision is part of a design document or reference architecture.

- Understand the effect of an outage with each dependency.

  Strong dependencies play a critical role in application function and availability. Their absence has a significant effect. The absence of weak dependencies might only affect specific features and not affect overall availability. This distinction reflects the cost that is needed to maintain the high availability relationship between the service and its dependencies. Classifying dependencies as either strong or weak helps you identify which components are essential to the application.

- Maintain SLAs and support agreements for critical dependencies.

  A service-level agreement (SLA) represents a commitment around performance and availability of the application. Understanding the SLA of individual components in the system is essential to define reliability targets. Knowing the SLA of dependencies also provides a justification for more spending when making the dependencies highly available and with proper support contracts. The operational commitments of all external and internal dependencies should be understood to inform the broader application operations and health model.

  The usage of platform level dependencies, such as Azure Active Directory, must also be understood to ensure that the availability and recovery targets align with the targets of the application.

- Ensure that applications can operate in the absence of their dependencies.

  If the application has strong dependencies that it can't operate without, the availability and recovery targets of these dependencies should align with the targets of the application itself. Make an effort to minimize dependencies to achieve control over application reliability. For more information, see [Minimize coordination](/azure/architecture/guide/design-principles/minimize-coordination).

- Ensure that the lifecycle of the application is decoupled from its dependencies.

  If the application lifecycle is closely coupled with the lifecycle of its dependencies, it can limit the operational agility of the application. This fact is true particularly for new releases.

## Related links

- For information on fault points and fault modes, see [Failure mode analysis for Azure applications](/azure/architecture/resiliency/failure-mode-analysis).
- For information on single points of failure, see [Make all things redundant](/azure/architecture/guide/design-principles/redundancy).
- For information on minimizing dependencies, see [Minimize coordination](/azure/architecture/guide/design-principles/minimize-coordination).

## Next steps

> [!div class="nextstepaction"]
> [Best practices for designing reliability](./design-best-practices.md)

Go back to the main article: [Design for reliability](design-checklist.md)
