---
title: VMware workloads on Azure
description: TBD9.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 01/26/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avs
---

# VMware workloads on Azure

This guidance is for **workload owners** who want to run their on-premises VMware technology stack on Azure. 

While it is possible to carry forward tools and processes used in an on-premise VMware environment, there are some important considerations when the environment is migrated to Azure. The provides prescriptive and best-practices for the key design areas keeping **Well-Architected pillars** as the technical foundation. 

The recommendations are centered around **Azure VMware Solution (AVS)**, a managed service for migrating on-premise VMware workloads to Azure. 

## What is a VMware workload?

The term _VMware workload_ refers to a vSphere-based enviroment that runs the VMware stack with management services such as NSX-T Data Center, vSAN, and HCX.

On Azure, a VMware workload can run in an AVS managed instance, which sets up a vSphere environment on Azure infrastructure and connects it to on-premises data center. The workload has all the necessary Azure compute, networking, storage, and management services needed to have consistency across on-premises and cloud environments.

## What are the common challenges?

// Add a simple bullet list.

## What are the key design areas?

The design areas focus on the technical decision points for infrastructure components that are part of of the workload and their interactivity with the shared services. 

|Design area|Summary|
|---|---|
|[Application](./avs-vmware-application.md)|TBD|
|[Infrastructure](./avs-infrastructure.md)|TBD|
|[Networking](./avs-networking.md)|TBD|
|[Monitoring](./avs-monitoring.md)|TBD|
|[Operations](./avs-operations.md)|TBD|

In an enterprise set up, your workload will share platform resources managed by central teams. The design areas can provide points of integration with those shared resources. That integration is described in [Integrate an VMware workload with Azure landing zones](avs-landing-zone-integration.md).

## Example workloads

The guidance is backed by examples that demostrate the architectural best practices. Use these implementations as a starting point for your workload.

- [Baseline architecture for Azure VMware Solution](/azure/cloud-adoption-framework/scenarios/azure-vmware/example-architectures)

    Focuses on the networking design area with various scenarios such as handling internet ingress/egress traffic, traffic to on-premises datacenter.

- [Azure VMware Solution landing zone accelerator](/azure/cloud-adoption-framework/scenarios/azure-vmware/enterprise-scale-landing-zone)

    An open-source collection of Azure Resource Manager and Bicep templates. It represents the strategic design path and target technical state for an Azure VMware Solution deployment.

## Assessment

Take a Well-Architected Azure VMware Solution (AVS) workload assessment to evaluate the choices of your workload.

> Refer to: [Well-Architected AVS assessment](./avs-assessment.md)

## Next step

Start by reviewing the design principles.

> [!div class="nextstepaction"]
> [Design principles](avs-design-principles.md)


