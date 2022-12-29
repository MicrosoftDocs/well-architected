---
title: Oracle workload performance efficiency
description: Oracle workload best practices for performance efficiency
author: ckittel
ms.author: kegorman
ms.date: 12/29/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---
# Oracle workload performance efficiency

Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. We recommend you review the Performance efficiency principles.

Oracle on Azure IaaS has components that are distributed across various Azure services. These components are the capacity planning targets for your application servers and database sizing choices. This includes load balancing configurations for even traffic distribution and Oracle-native solutions, like Oracle Data Guard for fast-start failover database setups.

## Optimize workload compute

Compute includes the hardware, number of cores, and memory but also the throughput. It’s important to know the demands of your workload. Therefore, regularly look on the AWR reports and statistics. It shows the actual needs of your workload so that you can right-size your environment.  Where possible and required you can add auto-scaling on the VMs. It can potentially be achieved through [Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets/overview).

## Optimize workload storage

By having the correct solution, you gain the ability to improve the performance of the existing capabilities and allow yourself to add new features. In general the storage should meet the input output operation per second (IOPS) but also the functionalities. Microsoft offers different solutions that can simplify the architecture.

## Optimize workload networking

Oracle applications usually have a high number of interfaces to other systems. Common communication paths are local storage, external storage, VMs in the network, and third-party applications. Optimizing the workload network with Proximity Placement Groups can bring higher performance.

### Understand Proximity Placement Groups

A proximity placement group is a logical grouping. It makes sure that Azure compute resources are physically located close to each other. Proximity placement groups are useful for workloads where low latency is a requirement, which is typical for Oracle Workloads.

Proximity placement groups reduce the distance between Oracle workloads. They can group different VM types under a single network spine. As the Azure footprint grows, a single availability zone may span multiple physical data centers. The distribution across data centers can create network latency impacting your Oracle application performance. The proximity of the VMs provides lower network latency.

The capabilities are ideal, but there are drawbacks to be aware of.
Proximity placement groups often limit your VM choices and make resizing VMs more difficult. Proximity placement groups bind VMs to a specific network spine. This binding limits the possible combinations of different VM types. The host hardware that is needed to run a certain VM type might not be present in the data center or under the network spine to which the proximity placement group was assigned. The availability of VM types can be severely restricted.

We recommend using proximity placement groups in two scenarios:
1. Use proximity placement groups in Azure regions where latency across zones is higher than recommended for the Oracle workload.
1. Use proximity placement groups for application volume group. The Application Volume Group feature of Azure NetApp Files (ANF) uses PPG to deploy ANF volumes close to the VM/compute cluster. We recommend using this feature as designed.

For more information, see:

- [Overview of proximity placement groups](/azure/virtual-machines/co-location)
- [Oracle and proximity placement groups](/azure/virtual-machines/workloads/Oracle/Oracle-proximity-placement-scenarios)

## Recommendations

Explore the following table of recommendations to optimize your Oracle database environments for performance efficiency:

| Recommendation | Benefit |
| --- | --- |
| Move from RMAN backups to Volume Snapshots | Databases larger than 1 TB can benefit from removal of streaming backup utilities, such as RMAN or Datapump (exports) and can save 20-40% IO used by these utilities.|
| Collect size of database, redo generation, and backup retention/size to determine the target minimal load on resources and the network. | AWR reports provide an insight into it. The numbers will help you to right-size your environment. |
| Determine if using premium SSD is best for the OS disk with the earlier mentioned sizing assessment in mind. | If using premium, enable host level read-only caching for datafiles. |
| Ensure host level caching is disabled for redo logs. | Higher Performance. |
| Test the VM chosen that the workload it was sized to support meets the needs once running in Azure. | Include implementing load balancing and autodetection so that as services are added or moved, the application can perform the necessary routing – it prevents you reaching high latencies. |
| Increase the size of the ExpressRoute gateway. | Upgrade to a [higher gateway SKU](/azure/expressroute/expressroute-about-virtual-network-gateways) for improved throughput performance between on-premises and Azure environment. |
| Move the right database files to higher IO storage | Use the AWR/Statspack report on IO Summary by file type to ensure that what you offer more IO to, is what will benefit from faster storage. |

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