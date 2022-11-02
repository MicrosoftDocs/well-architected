---
title: SAP workload 
description: SAP workload best practices
author: stephen-sumner
ms.author: ssumner
ms.date: 11/11/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom: SAP
---

# SAP workload best practices

SAP is one of the world’s leading producers of software solutions for business management and customer operations. SAP provides a suite of powerful applications that you can configure to meet specific environment and organizational needs. These applications facilitate data processing and information flow across organizations and provide critical capabilities that drive key organizational functions. SAP applications need an infrastructure tailored to maximize their capabilities and workload services that optimize functionality. Microsoft has hosted SAP instances in its data centers for decades and has designed infrastructure and services specifically to run SAP and its applications.

WAF guidance is iteratively applicable throughout the lifecycle of an individual SAP workload. We gathered best practices for evaluating, designing, and optimizing an SAP workload from premigration to operations and collected them here. The guidance is based our knowledge of SAP with the goal of helping you derive the most benefits from each SAP workload. You should apply the lessons here to a single SAP workload at a time. Any time you want to optimize a specific SAP application, such as HANA or NetWeaver, for or in Azure, you should use this guidance to iteratively evaluate each SAP workload at each phase in the journey. Work through it as many times as you need to derive the benefits you expect. In operations, WAF assessments should be a regular part of your routine and paired with SAP health checks.

![The relationship between WAF guidance and an SAP workload]()
*Figure 1: The relationship between WAF guidance and an SAP workload*

Before using this content, you should have an SAP platform landing zone in Azure. The platform landing zone provides shared services to one or more of your SAP workloads. If you don’t have a platform landing zone, you can follow our [SAP cloud adoption framework]( /azure/cloud-adoption-framework/scenarios/sap/) and deploy our SAP landing zone accelerator to build the required foundation for your SAP workload.

We built this guidance around five pillars of architectural excellence. The table below lists each pillar and provides a general summary of the articles in this set.

| WAF Pillar | Summary |
| --- | --- |
| Reliability |An SAP workload requires resiliency at the architecture layer. You’ll learn how to create an SAP application with high availability to process critical business data.  |
| Security| An SAP workload contains critical business data. You’ll learn to secure your SAP applications with multiple security layers, including identity, access, input validation, data sovereignty, and encryption.|
| Cost Optimization | An SAP workload has several architecture layers and numerous resources supporting it. You’ll learn how to make sure your SAP application deployment meets performance expectations while reducing the total cost of ownership.|
| Performance Efficiency | An SAP workload needs to be high performing to meet productivity requirements. You’ll learn how to make sure that your SAP workload meets user demands while managing costs.|
| Operational Excellence | An SAP workload spends most of its lifecycle in operations. You’ll learn how to manage an SAP workload and to keep it running.|

We invite you to explore SAP workload design best practices and return to this content regularly throughout the lifecycle of your SAP workload. The content highlights critical areas of focus but also refers you to other documentation for deeper technical insight, including [SAP workload architectures]( /azure/architecture/reference-architectures/sap/sap-overview).

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
