---
title: Oracle workloads on Azure infrastructure as a service (IaaS)
description: Learn about Well-Architected Framework best practices for an Oracle workload on Azure IaaS to help you create a performant, secure, and highly available solution.
author: jessiehaessler
ms.author: jhaessler
ms.topic: concept-article
ms.date: 05/15/2024
ms.service: azure-waf
ms.subservice: waf-workload-oracle
---

# Azure Well-Architected Oracle workloads on Azure infrastructure as a service (IaaS)

This article provides Well-Architected Framework best practices for an Oracle workload on Azure infrastructure as a service (IaaS). Application owners, Oracle database administrators, business stakeholders, and architects can use this guidance to create a technical foundation for their highly reliable solution for Oracle on Azure at scale.

## What is an Oracle workload?

In this guidance, the term *workload* refers to a collection of databases, such as [Oracle Exadata databases](/azure/azure-netapp-files/performance-oracle-multiple-volumes#decoupling-from-exadata), and application resources that support a common business goal or business process.

You must create Oracle workloads that are highly available and resilient to failure. These workloads can be customer relationship management applications, human resource applications, or customized applications that rely on Oracle WebLogic Server or Java. Oracle on-premises applications and platforms that you can move to the cloud include WebLogic Server, Siebel, PeopleSoft, JD Edwards, and E-Business Suite.

## What are the common challenges?

Microsoft Azure makes it easy to deploy and manage cloud solutions. But challenges can occur when you build and migrate Oracle workloads that are highly available and reliable on the Azure platform. For example:

- Oracle application architectures are complex. The dependencies between the application and database tier, such as version upgrades and patches, can increase complexity. 

- A reliable application design at scale requires knowledge about application versions and architectures, best practices from other customers, and expertise about dependencies on each layer. You need in-depth knowledge so that you can select the right technologies and properly configure them.
- Oracle database architectures differ from customer to customer. Exadata features, like smart scan and storage indexes, or Oracle Real Application Clusters (RAC) can introduce complexity. The automatic workload repository (AWR) provides insight into the actual usage of Exadata features and RAC setups, and ultimately database usage.

You must architect all Oracle workloads to handle failures with correlated or cascading impact. Reliability engineering is an important task within the entire architecture design.

### The Well-Architected Framework approach

We recommend that you structure your well-architected workload to meet specific reliability, security, cost optimization, operational excellence, and performance efficiency objectives. To enhance customer experiences and deliver consistency and reliability, follow architectural principles and guidelines that are specific to Oracle on Azure IaaS. This guidance addresses Oracle databases, like Exadata databases, and other Oracle applications.

The Well-Architected Framework pillars help you implement modularity, separate roles, and improve operational productivity. Use a well-architected approach to create application workloads without unnecessary complexities and unforeseen costs.

Use the Well-Architected Framework pillars to improve your Oracle on Azure IaaS workload in the following ways:

- **Reliability**: An Oracle workload requires resiliency at the architecture layer. Create a database and application architecture with high availability to process critical and noncritical business data.

- **Security**: An Oracle workload might contain business-critical data. Most Oracle applications also require a Secure Shell (SSH) port. Help secure your Oracle databases and applications with multiple security layers, including identity, access, input validation, data sovereignty, and encryption layers.
- **Cost optimization**: An Oracle workload requires that you bring your own license. You can generate an AWR report to optimize costs. You can use the AWR report to determine the virtual machine (VM) SKU and storage that you need to meet performance requirements. 
- **Operational excellence**: An Oracle workload requires monitoring to meet productivity requirements. Ensure that your Oracle workload meets operational excellence standards.
- **Performance efficiency**: An Oracle workload's life cycle consists mostly of operational tasks. Manage an Oracle workload so that it remains performant and meets customers' demands. You must also manage costs.

### Choose a migration approach

You can apply various migration approaches, depending on your migration requirements. Common migration tools include Oracle Data Guard, Oracle Recovery Manager (RMAN), Oracle GoldenGate, and Oracle Data Pump. We don't recommend Data Pump for a large volume of data.

As part of your migration, ensure that your character set is the same and has the same [endianness](https://en.wikipedia.org/wiki/Endianness) so that you can properly assess cross-platform migrations. For more information, see [Migrate Oracle workloads to Azure IaaS](/azure/cloud-adoption-framework/scenarios/oracle-iaas/oracle-migration-planning).

## What are the key design areas?

An Oracle on Azure IaaS migration includes the following design areas. Each design area focuses on technical decision points for the infrastructure components that are part of a workload and on the components' interaction with shared services.

|Design area|Summary|
|---|---|
|[Choose compute and storage](choose-compute-storage.md)|Learn how to rightsize your infrastructure to improve performance and cost efficiency. Use [Oracle Migration Assistant Tool (OMAT)](https://github.com/Azure/Oracle-Workloads-for-Azure/tree/main/omat) to select the right SKU for the database VM and the best-suited storage for the database, archive redo logs, and backup.|
|[Optimize business continuity and disaster recovery](optimize-business-continuity-disaster-recovery.md)|Get guidance about reliable failover architectures, including recovery point objective (RPO) and recovery time objective (RTO) requirements and high availability considerations.|
|[Optimize security](optimize-security.md)|Learn how to secure sensitive data, and create a reliable architecture where you can place your Oracle workload.|
|[Design Oracle applications](design-applications.md)|Learn about design considerations for your Oracle workload that can help you create a performant, reliable, secure, and highly available solution in the cloud.|
|[Monitor workloads](monitor-workloads.md)|Learn how to monitor tooling, so that you can quickly address developing problems and performance degradation.|

## Illustrative examples

The guidance provided within this series is based on a solution-oriented approach to illustrate key design considerations and recommendations. You can choose from several reference implementations to develop further solutions.

- [Reference architectures for Oracle database on Azure Virtual Machines](/azure/virtual-machines/workloads/oracle/oracle-reference-architecture#reference-architectures): Provides information about how to deploy a highly available Oracle database on Azure, including disaster recovery considerations.

- [Support for Oracle database workloads within Azure IaaS](https://github.com/Azure/Oracle-Workloads-for-Azure): Provides a repository that contains subfolders that represent multiple separate projects for Oracle workloads.

- [Overview of Oracle applications and solutions on Azure](/azure/virtual-machines/workloads/oracle/oracle-overview): Provides guidance to help you learn how to run Oracle solutions by using the Azure infrastructure.

## Next steps

Review the design principles.
> [!div class="nextstepaction"]
> [Review design principles](review-design-principles.md)

Take an assessment to evaluate your design choices.
> [!div class="nextstepaction"]
> [Azure Well-Architected Oracle on Azure IaaS workload assessment](/assessments/d3acb4d8-8045-4635-9a31-0bcb3b10724a)
