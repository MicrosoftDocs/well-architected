---
title: SAP workload 
description: SAP workload best practices
author: stephen-sumner
ms.author: ssumner
ms.date: 11/14/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom: SAP
---

# SAP workload best practices

SAP is one of the world’s leading producers of software solutions for business management and customer operations. SAP provides a suite of powerful applications that you can configure to meet specific environment and organizational needs. These applications facilitate data processing and information flow across organizations and provide critical capabilities that drive key organizational functions.

SAP applications thrive on infrastructure that is tailored to maximize their capabilities and with cloud services designed to optimize SAP workload functionality. Microsoft has hosted SAP instances in its data centers for decades and has custom built infrastructure and services to run SAP and its applications as they were designed. We’ve used this SAP experience to create a concise set of best practices for evaluating, designing, and optimizing an SAP workload from premigration to operations. The goal of this guidance is to help you get the most benefits from each SAP workload.

:::image type="content" source="./images/sap-platform-workload.png" alt-text="Using SAP workload guidance throughout the lifecycle of an SAP workload." lightbox="./images/sap-platform-workload.png" border="false":::
*Figure 1: Using SAP workload guidance throughout the lifecycle of an SAP workload.*

The guidance is designed for one SAP workload at a time. Anytime you want to optimize a specific SAP application (S4/HANA, NetWeaver, etc.) for Azure or in Azure, you should use this guidance. Work through the guidance as many times as you need to derive the benefits you expect. In operations, this guidance should be paired with the Well-Architected Review assessments and health checks. We address these tools in our guidance.

## SAP landing zone prerequisite

Before using this content, you should have an SAP platform landing zone in Azure. The platform landing zone provides shared services to one or more of your SAP workloads. If you don’t have a platform landing zone, you should use the SAP cloud adoption framework and deploy the SAP landing zone accelerator to deploy the required foundation for your SAP workload. For more information, see [SAP cloud adoption framework](/azure/cloud-adoption-framework/scenarios/sap/).

## Guidance overview

We built this guidance around the Azure Well-Architected Framework and its five pillars of architectural excellence. The table below lists each pillar and provides a general summary of the articles in this set.

| Well-architected framework pillar | Summary |
| --- | --- |
| Reliability |An SAP workload requires resiliency at the architecture layer. You’ll learn how to create an SAP application with high availability to process critical business data. |
| Security| An SAP workload contains critical business data. You’ll learn to secure your SAP applications with multiple security layers, including identity, access, input validation, data sovereignty, and encryption.|
| Cost Optimization | An SAP workload has several architecture layers and numerous resources supporting it. You’ll learn how to make sure your SAP application deployment meets performance expectations while reducing the total cost of ownership.|
| Performance Efficiency | An SAP workload needs to be high performing resources to meet productivity requirements. You’ll learn how to make sure that your SAP workload meets user demands while managing costs.|
| Operational Excellence | An SAP workload spends most of its lifecycle in operations. You’ll learn how to manage an SAP workload and to keep it running.|

We invite you to explore SAP workload design best practices and return to this content regularly throughout the lifecycle of your SAP workload. The content highlights critical areas of focus but also refers you to other documentation for deeper technical insight.

For more information, see:

- [Azure Center for SAP Solutions]( /azure/center-sap-solutions/overview)
- [SAP workload in Azure](/azure/virtual-machines/workloads/sap/get-started)
- [SAP workload architectures](/azure/architecture/reference-architectures/sap/sap-overview)

## Next Steps

>[!div class="nextstepaction"]
>[Reliability](./reliability.md)

>[!div class="nextstepaction"]
>[Security](./security.md)

>[!div class="nextstepaction"]
>[Cost Optimization](./cost-optimization.md)

>[!div class="nextstepaction"]
>[Operational Excellence](./operational-excellence.md)

>[!div class="nextstepaction"]
>[Performance Efficiency](./performance-efficiency.md)
