---
title: Azure VMware Solution (AVS) workloads
description: TBD13.
author: Mahesh-MSFT
ms.author: maksh
ms.date: 04/20/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avs
---

# Integrate an VMware workload with Azure landing zones

Each organization manages their workloads and operates their cloud environment uniquely. The common models are: _decentralized_, _central_, _enterprise_, and _distributed_. 

The most important difference between the models is the level of ownership. In the decentralized model, the workload owners have autonomy without any central IT oversight for governance. For example, they manage their own networing, monitoring, and identity requirements. At the other end of the spectrum is the central model, where the workload owners adhere to the governance needs set by central IT teams.

The models are discussed in detail as part of [Cloud Adoption Framework: Cloud Operating Model](/azure/cloud-adoption-framework/operating-model/compare) guidance.

As a workload owner, it's important to understand the operating model that your organization has chosen. That choice influences the technical decisions that you're accountable for and the technical requirements that you drive to the central teams. 

## Azure landing zones

Azure landing zones is a conceptual architecture that depicts the overall cloud footprint for an organization. It has multiple subscriptions, each with a unique purpose. Some of those subscriptions (_Azure platform landing zones_) are owned by central teams. 

When an AVS private cloud is deployed either before workload gets migrated or newly deployed on it, it needs to interact with multiple external services. Some of those services might be owned by central teams as part of platform landing zones. For example, domain resolution, network connectivity, security and others. Interaction with these external services is a foundational concern. Without a shared responsibility mindset between the platform team and the workload team, workloads deployed on AVS private cloud may not be fully functional.

> To demonstrate the platform landing zones needed for a VMware workload to run, we've provided the [Azure VMware Solution (AVS) Landing Zone Accelerator](/azure/cloud-adoption-framework/scenarios/azure-vmware/ready). It demonstrates a solid platform foundation which accelerates migration from on-premise VMware environment to Azure VMware Solution (AVS) private cloud.

There's a separate subscription (_Azure application landing zone_) intended for workload owners. This is where your VMWare workload is deployed. It has access to platform landing zones that provides basic infrastructure needed to run the workload, such as networking, identity access management, policies, and monitoring.

Guidance on Well-architected is applicable to the VMware workload in the application landing zone. It describes the the technical recommendations needed for it to be governed and managed efficiently.

> To demonstrate the application landing zone for a VMware workload, we've provided a baseline reference architecture that shows the a [Baseline architecture for Azure VMware Solution](/azure/cloud-adoption-framework/scenarios/azure-vmware/example-architectures).

Workload density and maturity is minimal inside the AVS private cloud. It's expected to increase after the initial accelerator deployment. This guidance is applicable when the private cloud starts to have increased workload density and maturity, 

We recommend that you familiarize yourself with the concept of [Azure landing zones](/azure/cloud-adoption-framework/ready/landing-zone/).


## Design area integration

The design areas of the platform and application landing zones are tightly coupled. 

- [Design areas of the platform landing zone](/azure/cloud-adoption-framework/scenarios/azure-vmware/ready) describe the changes to the platform resources, which are needed for the workload. 

- [Design areas of the application landing zone](./avs-overview.md#what-are-the-key-design-areas) describe the technical specification of the workload. 





## Next Steps

[Well-architected VMware resources](avs-vmware-application.md)