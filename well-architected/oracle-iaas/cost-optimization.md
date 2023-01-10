---
title: Oracle workload cost optimization
description: Oracle workload best practices for cost optimization
author: ckittel
ms.author: kegorman
ms.date: 12/29/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---
# Oracle workload cost optimization

Cost optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies. We recommend you review the [Cost optimization design principle.](../../well-architected/cost/checklist.md)

## Optimize workload compute costs

AWR and statspack statistics show your actual utilization. Based on your utilization, we help you to choose the right SKU to right-size according to your individual setup. Virtual Machines provide the computer power for your Oracle workload and have a cost effect that we can easily address. Below we provide some guidance to optimize your costs by choosing the right virtual machine type.

### Choose the right VM type

Oracle workloads tend to be low utilized on the CPU but high on the memory. Next to the utilization of compute power and memory, it's important that the throughput fit into the VM as well. Addressing the fact that very low compute power is used, Microsoft offers you [constrained VM sizes.](/azure/virtual-machines/constrained-vcpu)

To match the Oracle workload into a Virtual Machine, we recommend to either use the [E series](/azure/virtual-machines/edv5-edsv5-series) or [M series](/azure/virtual-machines/mv2-series). Both E and M series sizes are memory optimized and able to handle a high throughput.


## Optimize workload storage

All Oracle applications have a Shared File Storage. Data from the web tier but also from the database tier regularly sends the updated data into the File Storage. From there, the data is sent to back up storage. Storage can also have an effect of the performance, availability, and recoverability of your Oracle workload.

Set up a storage architecture that meets your requirements and allocating cost savings accordingly.

The following provides some guidance to set up a well-architected storage solution:

**(1) Use reserved capacity storage type** - There are several types of storage to choose from based on the requirements. Managed Disks, Blob Storage, and Azure Backup Storage will support you in various combinations. As every application has a file storage in the application tier, a cost optimization can be reached by having a File Storage in Place. Network attached storage offers an opportunity for workloads with high throughputs. In this case it's Azure NetApp Files (ANF). Note that ANF is a third-Party Storage Solution (Silk, Flashgrid Storage).

The above storage options come with storage reservation options that lower overall costs on the persistent data layer. For more information, please visit:

- [Azure disk reserved capacity](/azure/virtual-machines/disks-reserved-capacity)
- [Azure NetApp Files](/azure/azure-netapp-files/azure-netapp-files-introduction)
- [Azure Backup Storage reserved capacity](/azure/backup/backup-azure-reserved-pricing-optimize-cost)
- [About Azure VM Backup](/azure/backup/backup-azure-vms-introduction)

**(2) Use lifecycle management policies** - Next to reserved capacity you should define a data retention period. An Oracle database backup can be large and add cost to the storage costs if it isn't optimized. We recommend creating a lifecycle policy that meets the recovery time objective (RTO) and the recovery point objective (RPO) of your Oracle workload.

## Recommendations
Explore the following table of recommendations to optimize your Oracle on Azure IaaS for Cost optimization:

| Recommendation | Benefit |
| --- | --- |
| Familiarize yourself with right-sizing of Oracle databases | Based on our experiences most Oracle environments are over-provisioned. Learn how to take AWR or [Oracle Statspack](https://techcommunity.microsoft.com/t5/data-architecture-blog/sizing-out-oracle-workloads-for-azure-using-an-oracle-statspack/ba-p/2054539) data and provide a right-size of the database(s) environment. Deliver expected performance without wasting resources. |
| Determine the most optimal pricing on storage that meets the workload requirements | Define the lifecycle management policy according to your requirements to the RTO and RPO. If the data reaches an age consider that the policy should move into Premium, Standard, Cold, Archive storage based on its age and business requirements. |
| Determine the IO and backup demands when using network attached storage | Some intense IO workloads might require an NFS. Other workloads might not require an NFS. There are situations where sizing and IO will result with higher tiers of storage having lower cost.|
| Monitor costs and create budget alerts | The Azure portal equips you with various options and possibilities to monitor the costs. Alerts will help you. |
| Before scaling up hardware, ensure you understand what is causing performance bottlenecks and if it’s at the database or hardware level | Optimizing workloads can save a considerable amount over time and potentially also CPUs that you actually don't need. Make sure to regularly take a look on the AWR reports. When there are performance issues, it can also be caused on a storage level. Make sure to understand issues first before scaling up. |
| Familiarize yourself with the Oracle licensing FAQ | Be aware of what products are supported in Azure and how licensing works in Azure as a supported cloud for Oracle. |

## Next Step

>[!div class="nextstepaction"]
>[Operational Excellence](./operational-excellence.md)