---
title: SAP workload networking and connectivity
description: SAP workload networking and connectivity
author: stephen-sumner
ms.author: ssumner
ms.date: 12/15/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom: SAP
---

## Networking performance

An SAP workload needs to communicate with other workloads. Common communication paths are to local storage, external storage, NICs, VMs in the network, VMs in other networks, and third-party applications. Optimize workload networking to improve these communication channels to meet workload and application demand. If SAP network performance isn't considered, it will cause application performance issues.

**Understand proximity placement groups** - Proximity placement groups reduce the distance between SAP workloads. They can group different VM types under a single network spine. As the Azure footprint grows, a single availability zone may span multiple physical data centers. The distribution across data centers can create network latency impacting your SAP application performance. The proximity of the VMs provides lower network latency.

The capabilities are ideal, but there are drawbacks to be aware of.
Proximity placement groups often limit your VM choices and make resizing VMs more difficult. Proximity placement groups bind VMs to a specific network spine. This binding limits the possible combinations of different VM types. The host hardware that is needed to run a certain VM type might not be present in the data center or under the network spine to which the proximity placement group was assigned. The availability of VM types can be severely restricted.

We recommend using proximity placement groups in two scenarios. (1) Use proximity placement groups in Azure regions where latency across zones is higher than recommended for the SAP workload. (2) Use proximity placement groups for application volume group. The Application Volume Group feature of Azure NetApp Files (ANF) uses PPG to deploy ANF volumes close to the VM/compute cluster. We recommend using this feature as designed. For more information, see:

- [Overview of proximity placement groups](/azure/virtual-machines/co-location)
- [SAP and proximity placement groups](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)

**Use accelerated networking** - Accelerated Network is default for most of the VM deployments and is recommended for every VM hosting an SAP workload. Accelerated Network improves the network performance by bypassing the physical switch. We recommend you enable Accelerated Networking on the Azure VMs running your SAP Application and Database. Accelerated networking provides improved latency, jitter, and CPU utilization. You should test the latency between the SAP application server and database with the SAP ABAP report /SSA/CAT. It's an Inventory Check for the SAP Azure Workbook. For more information, see [accelerated networking overview](/azure/virtual-network/accelerated-networking-overview).

**Use ExpressRoute GlobalReach** - ExpressRoute is a private and resilient way to connect your on-premises networks to different Azure regions. This feature allows you to link ExpressRoute circuits to make a private network between your on-premises networks. Global Reach should be used for SAP HANA Large Instance deployments to enable direct access from on-premises to your HANA Large Instance units deployed in different regions. For more information, see [ExpressRoute Global Reach](/azure/expressroute/expressroute-global-reach).

## Next steps

> [!div class="nextstepaction"]
> [Application design](design-areas/application-design.md)

> [!div class="nextstepaction"]
> [Application platform](design-areas/application-platform.md)

> [!div class="nextstepaction"]
> [Data platform](design-areas/data-platform.md)

> [!div class="nextstepaction"]
> [Operational procedures](design-areas/operational-procedures.md)

> [!div class="nextstepaction"]
> [Security](design-areas/security.md)
