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

## Optimize workload compute

**(2) Use SAP Quick Sizer for a new workload**


For more information, see [SAP quick sizer](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer)

## Optimize workload storage

For more information, see [storage types for an SAP workload](/azure/virtual-machines/workloads/sap/planning-guide-storage).


**(2) Configure storage for performance**

For more information, see [SAP HANA Azure virtual machine storage configurations](/azure/virtual-machines/workloads/sap/hana-vm-operations-storage).

**(3) Enable write accelerator**


For more information, see [enable Azure write accelerator](/azure/virtual-machines/how-to-enable-write-accelerator).

## Optimize workload networking

**(1) Understand proximity placement groups**

For more information, see:

- [SAP and proximity placement groups](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) (detailed documentation )
- [General overview of proximity placement groups](/azure/virtual-machines/co-location)


**(2) Use accelerated networking**


For more information, see,
[Accelerated networking](/azure/virtual-network/accelerated-networking-overview))


**(3) Use ExpressRoute GlobalReach**



For more information, see:

- [ExpressRoute Global Reach](/azure/expressroute/expressroute-global-reach)
- [HANA Large Instances](/azure/architecture/solution-ideas/articles/sap-s4-hana-on-hli-with-ha-and-dr)


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
