---
title: SAP workload application design
description: SAP workload application design
author: stephen-sumner
ms.author: ssumner
ms.date: 01/12/2023
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom: SAP
---

# SAP workload application design
***Cloud application design patterns that allow for scaling, and error handling.***

## Create architecture reliability

Creating a multi-tier architecture to support an SAP workload is essential for reliability. The number of tiers and architecture varies for each SAP application. Make sure to isolate application components from each other and create redundancy to achieve high availability. Where applicable, you should isolate the SAP Web Dispatcher, SAP Central Services, SAP App Server, SAPMNT Share and database. We have sample architectures for several different SAP applications you can use to inform your design. For more information, see:

- [SAP S/4HANA in Linux](/azure/architecture/guide/sap/sap-s4hana)
- [SAP BW/4HANA](/azure/architecture/reference-architectures/sap/run-sap-bw4hana-with-linux-virtual-machines)
- [SAP NetWeaver](/azure/architecture/guide/sap/sap-netweaver)

## Create SAP central services reliability

SAP central services (SCS) or ABAP SAP central services (ASCS) is the basis of SAP application communication. It consists of the message server and enqueue server. The central services layer is often a single point of failure and must be set up for high availability to achieve SAP application resiliency. To add redundancy, create a cluster of SAP central services with compatible shared storage technology supporting the cluster. Depending on the operating system and available shared storage technology in general availability or private/public preview, various options are available. Availability zones provide an opportunity to create a highly available ASCS architecture. For more information, see:

- [SAP workload configurations with Azure Availability Zones](/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)
- [High-availability architecture for an SAP ASCS/SCS instance on Windows](/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios#high-availability-architecture-for-an-sap-ascsscs-instance-on-windows)
- [High-availability architecture for an SAP ASCS/SCS instance on Linux](/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios#high-availability-architecture-for-an-sap-ascsscs-instance-on-linux)