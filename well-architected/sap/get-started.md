---
title: SAP workload 
description: SAP workload best practices
author: stephen-sumner
ms.author: ssumner
ms.date: 01/12/2023
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom: SAP
---

# SAP workload

SAP is one of the world’s leading producers of software solutions for business management and customer operations. SAP provides a suite of powerful applications that you can configure to meet specific environment and organizational needs. These applications facilitate data processing and information flow across organizations and provide critical capabilities that drive key organizational functions.

SAP applications thrive on infrastructure that is tailored to maximize their capabilities and with cloud services designed to optimize SAP workload functionality. Microsoft has hosted SAP instances in its data centers for decades and has custom built infrastructure and services to run SAP and its applications as they were designed. We’ve used this SAP experience to create a concise set of best practices for evaluating, designing, and optimizing an SAP workload from premigration to operations. The goal of this guidance is to help you get the most benefits from each SAP workload.

:::image type="content" source="./images/sap-platform-workload.png" alt-text="Diagram that shows how to use this SAP workload guidance throughout the lifecycle of an SAP workload." lightbox="./images/sap-platform-workload.png" border="false":::
*Figure 1: Using SAP workload guidance throughout the lifecycle of an SAP workload.*

The guidance is designed for one SAP workload at a time. Anytime you want to optimize a specific SAP application (S4/HANA, NetWeaver, etc.) for Azure or in Azure, you should use this guidance. Work through the guidance as many times as you need to derive the benefits you expect. In operations, this guidance should be paired with the Well-Architected Review assessments and health checks. We address these tools in our guidance.

## SAP landing zone prerequisite

Before using this content, you should have an SAP platform landing zone in Azure. The platform landing zone provides shared services to one or more of your SAP workloads. If you don’t have a platform landing zone, you should use the SAP cloud adoption framework and deploy the SAP landing zone accelerator. The landing zone accelerator establishes the required foundation for your SAP workload. For more information, see [SAP cloud adoption framework](/azure/cloud-adoption-framework/scenarios/sap/).

We invite you to explore SAP workload design best practices and return to this content regularly throughout the lifecycle of your SAP workload. The content highlights critical areas of focus but also refers you to other documentation for deeper technical insight.

For more information, see:

- [Azure Center for SAP Solutions](/azure/center-sap-solutions/overview)
- [SAP workload in Azure](/azure/virtual-machines/workloads/sap/get-started)
- [SAP workload architectures](/azure/architecture/reference-architectures/sap/sap-overview)

## Next steps

> [!div class="nextstepaction"]
> [Design principles](design-principles.md)
