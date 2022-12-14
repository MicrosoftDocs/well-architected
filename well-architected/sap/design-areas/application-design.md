---
title: SAP workload application design
description: SAP workload application design
author: stephen-sumner
ms.author: ssumner
ms.date: 12/19/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom: SAP
---

# SAP workload application design

(**NEED AN INTRO**)

***Cloud application design patterns that allow for scaling, and error handling.***

## SAP application cost

Optimizing your SAP application can lower the total cost of ownership without reducing capabilities. The goal is to generate the maximum return on investment (ROI). Here are ways to optimize an SAP application.

**Identify application responsibility.** Optimizing an SAP Application should be the responsibility of the customer business application team. Having someone or a group responsible for costs will help drive decisions that optimize costs over the lifecycle of the SAP workload.

**Rationalize and rearchitect.** You should consider rationalizing or rearchitecting the SAP application, especially during migrations. S4 HANA often replaces older SAP applications that can be added as a legacy system. The SAP WAF assessment can help validate rearchitecting efforts and should be conducted on a periodic basis. For more information, see [Azure Well-Architected Review](/assessments/).

**Minimize investment in legacy systems.** You should host a legacy SAP application on minimum-supported architecture to help reduce cost. A legacy application is slower and less performant. Any legacy systems that remain after rationalizing and rearchitecting should receive the minimum spend possible and be retired when appropriate. For more information, see [Azure Cost Management](/azure/cost-management-billing/costs/cost-mgt-best-practices).

## SAP application reliability

**Use a multi-tier architecture.** Creating a multi-tier architecture to support an SAP workload is essential for reliability. The number of tiers and architecture varies for each SAP application. Make sure to isolate application components from each other and create redundancy to achieve high availability. Where applicable, you should isolate the SAP Web Dispatcher, SAP Central Services, SAP App Server, SAPMNT Share and database. We have sample architectures for several different SAP applications you can use to inform your design. For more information, see:

- [SAP S/4HANA in Linux](/azure/architecture/guide/sap/sap-s4hana)
- [SAP BW/4HANA](/azure/architecture/reference-architectures/sap/run-sap-bw4hana-with-linux-virtual-machines)
- [SAP NetWeaver](/azure/architecture/guide/sap/sap-netweaver)

**Configure SAP central services reliability.** SAP central services (SCS) or ABAP SAP central services (ASCS) is the basis of SAP application communication. It consists of the message server and enqueue server. The central services layer is often a single point of failure and must be set up for high availability to achieve SAP application resiliency. To add redundancy, create a cluster of SAP central services with compatible shared storage technology supporting the cluster. Depending on the operating system and available shared storage technology in general availability or private/public preview, various options are available. Availability zones provide an opportunity to create a highly available ASCS architecture. For more information, see:

- [SAP workload configurations with Azure Availability Zones](/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)
- [High-availability architecture for an SAP ASCS/SCS instance on Windows](/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios#high-availability-architecture-for-an-sap-ascsscs-instance-on-windows)
- [High-availability architecture for an SAP ASCS/SCS instance on Linux](/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios#high-availability-architecture-for-an-sap-ascsscs-instance-on-linux)

## Next steps

> [!div class="nextstepaction"]
> [Application platform](./application-platform.md)

> [!div class="nextstepaction"]
> [Data platform](./data-platform.md)

> [!div class="nextstepaction"]
> [Networking and connectivity](./networking-and-connectivity.md)

> [!div class="nextstepaction"]
> [Operational procedures](./operational-procedures.md)

> [!div class="nextstepaction"]
> [Security](./security.md)
