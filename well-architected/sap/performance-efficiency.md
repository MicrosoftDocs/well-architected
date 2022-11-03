---
title: SAP workload performance efficiency
description: SAP workload best practices for performance efficiency
author: stephen-sumner
ms.author: ssumner
ms.date: 11/11/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom: SAP
---

# SAP workload performance efficiency

Performance efficiency accelerates digital transformation with less. It’s about getting the most out of your SAP workload and meeting user demand without over or under provisioning resources. Performance affects productivity for internal applications, and for public facing applications, performance affects brands. Inefficient performance can degrade user experience or inflate costs.  Designing an SAP workload that can't meet user demand will slow the application. Overcompensating with too much compute power will drive up cost needlessly. Here are recommendations to drive performance efficiency.

## Optimize workload compute

Compute is the core that powers an SAP application. Compute includes the hardware, number of cores, and memory that serve as the foundational service for business processing. If you don’t optimize your compute configuration, an SAP workload will be unable to meet spikes in user demand or stay withing predefined budgets. It’s important to know the demands on your workload and match those demands with the compute you provision for your SAP workload.

**(1) Conduct reference sizing on-premises workload** - Reference sizing refers to checking the system configuration and resource utilization data of an on-premises SAP workload. The data will show the current compute needs of the workload that should be matched in Azure. To find this information, use the SAP OS Collector. SAP OS Collector retrieves system utilization information that can be reported via SAP transaction OS07N and the EarlyWatch Alert. Any system performance and statistics gathering tools can collect similar information.

**(2) Use SAP Quick Sizer for a new workload** - SAP Quick Sizer is a free web-based tool developed by SAP that translates business requirements into technical requirements. Use this tool when you build a new SAP workload to find the Azure VM with the correct network and storage throughput. For more information, see [SAP quick sizer](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer)

## Optimize workload storage

It’s important to choose the appropriate storage solutions to support the data needs of the SAP workload. The correct solution can improve the performance of existing capabilities and allow you to add new features. In general, storage needs to meet the IOPS and throughput requirements of the SAP database. For more information, see [storage types for an SAP workload](/azure/virtual-machines/workloads/sap/planning-guide-storage).

Below we highlight a few storage recommendations:

**(1) Configure storage for performance** - We've published a storage configuration guideline for SAP HANA databases. It covers production scenarios and a cost-conscious non-production variant. Following the recommended storage configurations will ensure the storage passes all SAP hardware and cloud measurement tool (HCMT) key performance indicators (KPIs). For more information, see [SAP HANA Azure virtual machine storage configurations](/azure/virtual-machines/workloads/sap/hana-vm-operations-storage).

**(2) Enable write accelerator** - Write accelerate is a disk capability for M-series VMs. It’s imperative to enable write accelerator on the disks associated with the /hana/log volume. It facilitates submillisecond writes latency for 4 KB and 16-KB blocks sizes. For more information, see [enable Azure write accelerator](/azure/virtual-machines/how-to-enable-write-accelerator).

**(3) Choose the right VM** - Choosing the right VM has cost and performance implications. The goal is to pick a storage VM that supports the IOPS and throughput requirements of the SAP workload. There are three critical areas to focus while selecting a VM:

- ***Number of vCPUs*** - The number of CPUs has a direct effect on the licenses on the database node. Most of the databases follow a core-based licensing model. Use the amount that meets your needs and adjust licensing agreements as necessary.
- ***Memory*** -  Memory is critical to application performance, and your SAP application can have high memory demands. In general, higher memory provides more memory-reads, less paging, and higher VM cost.
- ***Throughput*** - Throughput is important for an application hosted on one of the VMs to communicate with outside the VM by using its network interface cards (NICs).

## Optimize workload networking

An SAP workload has complex networking requirements. It needs to communicate with other workloads, local storage, external storage, NICs, internal VMs, external VMs, and third-party applications. Optimize workload networking to improve these communication channels to meet workload and application demand. If SAP network performance isn't considered, it will cause application performance issues.

**(1) Understand proximity placement groups** - Proximity placement groups reduce the distance between SAP workloads. They can group different VM types under a single network spine. As the Azure footprint grows, a single availability zone may span multiple physical data centers. The distribution across data centers can create network latency impacting your SAP application performance. The proximity of the VMs provides lower network latency. The capabilities are ideal, but there are drawbacks to be aware of:
Proximity placement groups often limit your VM choices and make resizing more difficult. Proximity placement groups bind VMs to a specific network spine. This binding limits the possible combinations of different VM types. The host hardware that is needed to run a certain VM type might not be present in the data centers or under the network spine to which the proximity placement group was assigned. The availability of VM types can be severely restricted.

We recommend using proximity in two scenarios:

- ***High-latency deployments*** - Use proximity placement groups in Azure regions where latency across zones is higher than recommended for the SAP workload.
- ***Application Volume Group*** - The Application Volume Group features of Azure NetApp Files uses PPG to deploy ANF volumes close to the VM/compute cluster. We recommend using this feature as designed.

For more information, see [SAP and proximity placement groups](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) and [general overview of proximity placement groups](/azure/virtual-machines/co-location).

**(2) Use accelerated networking** - Accelerated Network is default for most of the VM deployments and is recommended for every VM hosting an SAP workload. Accelerated Network improves the network performance by bypassing the physical switch. We recommend you enable Accelerated Networking on the Azure VMs running your SAP Application and Database. Accelerated Networking enables single root I/O virtualization (SR-IOV) and provides improved latency, jitter, and CPU utilization. You should test the latency between the SAP application server and database with the SAP ABAP report /SSA/CAT. It's one of the checklists as part of Inventory Checks for SAP Azure workbook. For more information, see, [accelerated networking](/azure/virtual-network/accelerated-networking-overview).

**(3) Use ExpressRoute GlobalReach** - ExpressRoute is a private and resilient way to connect your on-premises networks to different Azure regions. This feature allows you to link ExpressRoute circuits to make a private network between your on-premises networks. Global Reach should be used for SAP HANA Large Instance deployments to enable direct access from on-premises to your HANA Large Instance units deployed in different regions. ExpressRoute also allows HANA Large Instance units deployed in different regions to communicate with each other. For more information, see [ExpressRoute Global Reach](/azure/expressroute/expressroute-global-reach).

## Next Step

>[!div class="nextstepaction"]
>[Overview](./overview.md)

>[!div class="nextstepaction"]
>[Reliability](./reliability.md)

>[!div class="nextstepaction"]
>[Security](./security.md)

>[!div class="nextstepaction"]
>[Cost Optimization](./cost-optimization.md)

>[!div class="nextstepaction"]
>[Operational Excellence](./operational-excellence.md)
