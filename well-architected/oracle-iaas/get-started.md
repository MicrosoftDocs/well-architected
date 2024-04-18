---
title: Create an Oracle workload on Azure
description: See general guidance and resources for running Oracle on Azure IaaS.
author: jessiehaessler
ms.author: jhaessler
ms.date: 04/15/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-oracle
---

# Create an Oracle workload on Azure

This article provides Well-Architected Framework best practices for an Oracle workload on Azure infrastructure as a service (IaaS). Application owners, Oracle database administrators, business stakeholders, and architects can use this guidance to create a technical foundation for their highly reliable solution for Oracle on Azure at-scale.

## What is an Oracle workload?

The term *workload* refers to a collection of databases, including [Exadata](/azure/azure-netapp-files/performance-oracle-multiple-volumes#decoupling-from-exadata) and application resources, that support a common business goal or the execution of a common business process. Oracle workloads must be highly available and resilient to failure. These workloads can be customer relationship management applications, human resource applications, or customized applications that rely on Oracle WebLogic Server or Java. Oracle on-premises applications that you can move to the cloud include WebLogic Server, Siebel, Peoplesoft, JD Edwards, and E-Business Suite. 

## What are the common challenges?

Microsoft Azure makes it easy to deploy and manage cloud solutions. But challenges can occur when you build and migrate Oracle workloads that are highly available and reliable on the Azure platform. For example:

- Oracle application architectures are complex. The dependencies between the application and database tier, such as version upgrades and patches, can increase the complexity. 

- A reliable application design at scale requires knowledge about application versions and architectures, best practices from other customers, and expertise about dependencies on each layer. You need in-depth knowledge, so you can select the right technologies and properly configure them.
- Oracle database architectures differ from customer to customer. Oracle Exadata features like smart scan and storage indexes or Oracle Real Application Clusters (RAC) can introduce complexity into the setup. The automatic workload repository provides insight into the actual usage of Exadata features and RAC setups, and ultimately database usage. 

You must architect all Oracle workloads to handle failures with correlated or cascading impact. Reliability engineering is an important task within the entire architecture design.

## The Well-Architected Framework approach

We recommend that you structure your well-architected workload to meet specific performance, reliability, security, cost optimization, and performance efficiency objectives. Follow architectural principles and guidelines that are specific to Oracle on Azure IaaS to enhance end-user experiences and deliver consistency and reliability. This guidance addresses Oracle databases including Exadata as a specific workload as well as other Oracle applications.

The Well-Architected Framework pillars help you implement modularity, separate roles, and improve operational productivity. Use this approach to create application workloads that avoid unnecessary complexities and unforeseen costs.

Consider making your Oracle on Azure IaaS workload well-architected for the following reasons:

- **Reliability**. An Oracle workload requires resiliency at the architecture layer. You’ll learn how to create a database and application architecture with high availability to process critical and non-critical business data.

- **Security**. An Oracle workload might contain business-critical data. Most Oracle applications also require a Secure Shell (SSH) port. You must secure your Oracle databases and applications with multiple security layers, including identity, access, input validation, data sovereignty, and encryption layers.
- **Cost optimization**. An Oracle workload requires that you bring your own license. You can generate an automatic workload repository (AWR) report to optimize costs. You can use the AWR report to determine the virtual machine (VM) SKU and storage that you need to meet performance requirements. 
- **Operational excellence**. An Oracle workload requires that you monitor it to meet productivity requirements. You must ensure that your Oracle workload meets operational excellence standards.
- **Performance efficiency**. An Oracle workload's life cycle consists mostly of operational tasks. You must manage an Oracle workload so it's performant and meets users' demands, and you must also manage costs.

### Choose a migration approach

There are various migration approaches that you can apply, depending on your migration requirements. Common tools include Data Guard, Recovery Manager (RMAN), GoldenGate, and Data Pump. We don't recommend Data Pump for a large volume of data. It's also important to ensure that your character set is the same and has the same endian so that cross-platform migrations can be properly assessed.

For more information, see [Migration planning](/azure/cloud-adoption-framework/scenarios/oracle-iaas/oracle-migration-planning).

## What are the key design areas?

Oracle on Azure IaaS includes the following design areas. Each design area focuses on technical decision points for the infrastructure components that are part of a workload and the component's interaction with shared services.

|Design area|Summary|
|---|---|
|[Choose compute and storage](choose-compute-storage.md)|Learn how to right-size your infrastructure to improve performance and cost efficiency. Use Oracle Migration Assistant Tool [OMAT](https://github.com/Azure/Oracle-Workloads-for-Azure/tree/main/omat) to select the right SKU for the database VM and the best-suited storage for the database, archive redo logs, and backup.|
|[Optimize business continuity and disaster recovery](optimize-business-continuity-disaster-recovery.md)|Get guidance about reliable failover architectures, including recovery point objective (RPO) and recovery time objective (RTO) requirements and high availability considerations.|
|[Optimize security](optimize-security.md)|Learn how to secure sensitive data, and create a reliable architecture to place your Oracle workload.|
|[Design Oracle applications](design-applications.md)|Learn about design considerations for your Oracle workload that can help you create a performant, reliable, secure, and highly available solution in the cloud.|
|[Monitor workloads](monitor-workloads.md)|Learn how to monitor tooling so you can quickly address developing problems and performance degradation.|

<!--## Assessment

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](...)-->

## Next step

> [!div class="nextstepaction"]
> [Review design principles](review-design-principles.md)
