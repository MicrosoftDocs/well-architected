---
title: SAP workload cost optimization
description: SAP workload best practices for cost optimization 
author: stephen-sumner
ms.author: ssumner
ms.date: 11/11/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom: SAP
---
# SAP workload cost optimization

## Optimize workload compute and storage

For more information, see:

- [SAP on Azure](https://azure.microsoft.com/solutions/sap/#overview)
- [SAP NetWeaver](/azure/virtual-machines/workloads/sap/planning-guide)
- [SAP HANA install](/azure/virtual-machines/workloads/sap/hana-get-started)
- [SAP HANA configuration](/azure/virtual-machines/workloads/sap/hana-vm-operations)


**(1) Optimize workload compute**

***Choose the right VM type***

For more information, see [SAP-certified configurations](https://azure.microsoft.com/solutions/sap/azure-solutions/#certified-infrastructure).


***Optimize throughout migration***


***Optimize in operations***


For more information, see [Azure Advisor cost optimization](/azure/advisor/advisor-cost-recommendations).
- *Stop a non-critical SAP workload* - For more information, see [automate VM start and stop](/azure/automation/automation-solution-vm-management).
- *Use Reserved Instances* - For more information, see [Azure Reservations]((/azure/cost-management-billing/reservations/save-compute-costs-reservations).
- *Use the Azure Hybrid Benefit* - For more information, see [hybrid licensing benefit](https://azure.microsoft.com/pricing/hybrid-benefit/#calculator).

**(2) Optimize workload storage**

***Use reserved capacity storage type***

For more information see:

- [Azure disk reserved capacity](/azure/virtual-machines/disks-reserved-capacity)
- [Blob storage reserved capacity](/azure/storage/blobs/storage-blob-reserved-capacity?toc=%2Fazure%2Fcost-management-billing%2Freservations%2Ftoc.json)
- [Azure Backup Storage reserved capacity](/azure/backup/backup-azure-reserved-pricing-optimize-cost)


## Optimize the SAP application

**(3) Identify legacy systems**

For more information, see:
- [Azure Cost Management](/azure/cost-management-billing/costs/cost-mgt-best-practices)

## Next Step

>[!div class="nextstepaction"]
>[Overview](./overview.md)

>[!div class="nextstepaction"]
>[Reliability](./reliability.md)

>[!div class="nextstepaction"]
>[Security](./security.md)

>[!div class="nextstepaction"]
>[Operational Excellence](./operational-excellence.md)

>[!div class="nextstepaction"]
>[Performance Efficiency](./performance-efficiency.md)
