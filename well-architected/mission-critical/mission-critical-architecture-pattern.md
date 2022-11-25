---
title: Architecture pattern for mission-critical workloads on Azure
description: The design areas represent the architecturally significant topics that must be discussed and designed for when defining a target architecture
author: calcof
ms.author: calcof
ms.date: 08/15/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
categories: 
  - databases
  - web
  - devops
  - security
  - networking
products: Azure
ms.custom:
  - mission-critical
---

# Architecture pattern for mission-critical workloads on Azure

This article describes a pattern that's commonly recommended for mission-critical architecture. The pattern describes the components and their characteristics. Also included are some examples that implement this pattern. 

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected mission-critical workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a mission-critical workload?](mission-critical-overview.md#what-is-a-mission-critical-workload)
>

## Mission-critical pattern

[Diagram showing a generic pattern for a mission-critical application.](./images/mission-critical-pattern.png)

### Key charactertistics



### Global resources 

##### Charactertistics

### Regional resources 

##### Charactertistics

### Regional stamp resources

##### Charactertistics

## Pattern examples

### Example HPC architectures

There are many different ways to design and implement your HPC architecture on Azure.  HPC applications can scale to thousands of compute cores, extend on-premises clusters, or run as a 100% cloud-native solution.

The following scenarios outline a few of the common ways HPC solutions are built.

<ul class="columns is-multiline has-margin-left-none has-margin-bottom-none has-padding-top-medium">
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <article class="card has-outline-hover is-relative is-fullheight">
            <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                <a class="is-undecorated is-full-height is-block"
                 href="https://learn.microsoft.com/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-intro">
                 <img role="presentation" alt="Diagram shows a baseline mission-critical application." src="https://learn.microsoft.com/azure/architecture/reference-architectures/containers/aks-mission-critical/images/mission-critical-architecture-online.png">
                </a>
             </figure>
             <div class="card-content has-text-overflow-ellipsis">
                 <div class="has-padding-bottom-none">
                     <h4 class="is-size-7 has-margin-top-none has-margin-bottom-none has-text-primary">Baseline architecture</h4>
                 </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>The workload is accessed over a public endpoint and does not require private network connectivity to other company resources.</p>
                    </div>
                </div>
            </article>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <article class="card has-outline-hover is-relative is-fullheight">
            <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
               <a class="is-undecorated is-full-height is-block"
                 href="https://learn.microsoft.com/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-network-architecture">
                 <img role="presentation" alt="Diagram shows the baseline architecture extended with network controls." src="https://learn.microsoft.com/azure/architecture/reference-architectures/containers/aks-mission-critical/images/mission-critical-architecture-network-highres.png">
                </a>
                </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h4 class="is-size-7 has-margin-top-none has-margin-bottom-none has-text-primary">Baseline with network controls</h4>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>The workload has strict network controls in place to prevent unauthorized public access from the internet to any of the workload resources.</p>
                    </div>
                </div>
            </article>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <article class="card has-outline-hover is-relative is-fullheight">
          <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
              <a class="is-undecorated is-full-height is-block"
              href="https://learn.microsoft.com/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-landing-zone">
                 <img role="presentation" alt="Diagram shows the baseline architecture deployed using Azure landing zones." src="https://learn.microsoft.com/azure/architecture/reference-architectures/containers/aks-mission-critical/images/mission-critical-architecture-landing-zone-high-res.png">
            </a>
          </figure>
          <div class="card-content has-text-overflow-ellipsis">
             <div class="has-padding-bottom-none">
                   <h4 class="is-size-7 has-margin-top-none has-margin-bottom-none has-text-primary">Baseline in Azure landing zones</h4>
             </div>
             <div class="is-size-7 has-margin-top-small has-line-height-reset">
                   <p>The workload uses centralized shared services, needs on-premises connectivity, and integrates with other workloads of an enterprise.</p>
             </div>
          </div>
       </article>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <article class="card has-outline-hover is-relative is-fullheight">
          <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
              <a class="is-undecorated is-full-height is-block"
              href="https://review.learn.microsoft.com/en-us/azure/architecture/industries/telecommunications/carrier-grade?branch=pr-en-us-8411">
                 <img role="presentation" alt="Diagram shows a carrier-grade voicemail solution." src="https://review.learn.microsoft.com/en-us/azure/architecture/industries/telecommunications/images/carrier-grade-architecture.png?branch=pr-en-us-8411">
            </a>
          </figure>
          <div class="card-content has-text-overflow-ellipsis">
             <div class="has-padding-bottom-none">
                   <h4 class="is-size-7 has-margin-top-none has-margin-bottom-none has-text-primary">Carrier-grade voicemail solution</h4>
             </div>
             <div class="is-size-7 has-margin-top-small has-line-height-reset">
                   <p>Within the telecommunications industry, mission-critical applications are referred to as carrier-grade systems. This example shows a voice mail solution.</p>
             </div>
          </div>
       </article>
    </li>         
</ul>

A mission-critical workload architecture is defined by the various design decisions required to ensure both functional and non-functional business-requirements are fully satisfied. The target architecture is therefore greatly influenced by the relevant business requirements, and as a result may vary between different application contexts.

### Baseline architecture
The image below represents a baseline reference architecture recommended for mission-critical workloads on Azure. It leverages a reference set of business requirements to achieve an optimized architecture for different target reliability tiers. The architecture is designed for a workload that is accessed over a public endpoint and does not require private network connectivity to other company resources.

![Mission-critical baseline reference architecture](/azure/architecture/reference-architectures/containers/aks-mission-critical/images/mission-critical-architecture-online.png)

> [Guidance for this reference architecture](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-intro).

### Baseline architecture with network controls

This image shows the baseline architecture with strict controls to stop attack vectors at the networking layer so that the overall reliability of the system isn't impacted. 

![Mission-critical baseline reference architecture with network controls](/azure/architecture/reference-architectures/containers/aks-mission-critical/images/mission-critical-architecture-network.svg)

> [Guidance for this reference architecture](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-network-architecture).


The [Mission-Critical Online](https://github.com/Azure/Mission-Critical-Online) and [Mission-Critical Connected](https://github.com/Azure/Mission-Critical-Connected) provide solution orientated showcases for this design methodology, demonstrating how this architecture pattern can be implemented alongside the operational wrappers required to maximize reliability and operational effectiveness.

Use these reference implementations to construct a sandbox application environment for validating key design decisions.

## Design areas
We recommend that you use the provided design guidance to navigate the key design decisions to reach an optimal solution. For information, see [What are the key design areas?](/azure/architecture/framework/mission-critical/mission-critical-overview#what-are-the-key-design-areas)

## Azure landing zone integration

[Azure landing zones](/azure/cloud-adoption-framework/ready/landing-zone/) provides prescriptive architectural guidance to define a reliable and scalable shared-service platform for enterprise Azure deployments with requisite centralized governance. 

This mission-critical workload series provides prescriptive architectural guidance to define a highly reliable application for mission-critical workloads that could be deployed within an Azure landing zone.

The mission-critical [reference implementations](mission-critical-overview.md#illustrative-examples) can integrate seamlessly within an Azure landing zone, and is deployable within both the *Online* or *Corp. Connected* Landing Zone formats as demonstrated within the image below.

![Mission-critical workload and landing zone integration](./images/mission-critical-landing-zones.gif "Mission-critical workload and landing zone integration")

It is crucial to understand and identify in which connectivity scenario a mission-critical application requires since Azure landing zones support different workload agnostic landing zones archetypes separated into different Management Group scopes.

> The mission-critical reference implementations are fully aligned with the Azure landing zones architectural approach and are immediately deployable within an *Online* or *Connected* Landing Zone subscription.

- In the context of an *Online* landing zone archetype, a mission-critical workload operates as a completely independent solution, without any direct corporate network connectivity to the rest of the Azure landing zone architecture. The application will, however, be further safeguarded through the [*policy-driven management*](/azure/cloud-adoption-framework/ready/enterprise-scale/dine-guidance) approach which is foundational to Azure landing zones, and will automatically integrate with centralized platform logging through policy.

  An *Online* deployment can only really consider a public application deployment since there is no private corporate connectivity provided.

- When deployed in a *Corp. Connected* Landing Zone context, the mission-critical workload takes a dependency on the Azure landing zone to provide connectivity resources which allow for integration with other applications and shared services. This necessitates some transformation on-top of the *Online* integration approach, because some foundational resources are expected to exist up-front as part of the shared-service platform. More specifically, the regional deployment stamp should not longer encompass an ephemeral Virtual Network or Azure Private DNS Zone since these will exist within the Azure landing zones *connectivity* subscription. 

  A *Corp. Connected* deployment can consider both a public or private application deployment.

## Next step

Review the best practices for architecting mission-critical application scenarios.

> [!div class="nextstepaction"]
> [Application design](./mission-critical-application-design.md)

