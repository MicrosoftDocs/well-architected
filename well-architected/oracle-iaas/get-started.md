---
title: Get started on Oracle on Azure IaaS workloads
description: See general guidance and resources for running Oracle on Azure IaaS.
author: jessiehaessler
ms.author: jhaessler
ms.date: 04/15/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-oracle
---

# Get started on Oracle on Azure IaaS workloads

This guidance is intended to provide **Application Owners**, **Oracle Database Administrators**, **Business Stakeholders** and **Architects** with best practices for well-architected Oracle on Azure IaaS implementation.
You can use this workload documentation as your go-to resource for guidance on how to apply the Azure Well-Architected best practices as the technical foundation for building and operating a highly reliable solution for Oracle on Azure at-scale.

## About Oracle Workloads

The term workload refers to a collection of database(s) including [Exadata](/azure/azure-netapp-files/performance-oracle-multiple-volumes#decoupling-from-exadata) and application resources that support a common business goal or the execution of a common business process. Oracle workloads must be highly available and resilient to failure. 
These workloads could be Customer Relationship Management applications, Human Resources, customized applications that mostly rely on WebLogic Server or Java. WebLogic Server, Siebel, Peoplesoft, JD Edwards and E-Business Suite are Oracle on-premise applications that can be moved to the cloud. 

## What are the Common Challenges?

Microsoft Azure makes it easy to deploy and manage cloud solutions. However, building and migrating Oracle workloads that are highly available and reliable on the Azure platform remains a challenge for the following reasons:
- Oracle applications architectures are complex. The complexity is exacerbated by the dependencies between the application and database tier, version upgrades and patches. 
- Designing a reliable application at scale requires knowledge about the application versions and architectures, best practices from other customers as well as dependencies on each layer. It is important to have the required in-depth knowledge in order to select the right technologies and configure them correctly.
- Oracle database architectures differ from customer to customer. Oracle Real Application Cluster (RAC) or Exadata features like SmartScan, Storage Indexes can introduce complexity into the setup. Automatic Workload Repositories provide a great insight into the actual usage of Exadata Features or RAC Setups and ultimately Database utilization. 

All Oracle workloads need to be architected to handle failures with correlated or cascading impact. Reliability Engineering is an important task within the entire architecture design.

## The Well-Architected Framework approach

A well-architected workload is structured to meet specific performance, reliability, security, cost optimization and performance efficiency objectives. By following architectural principles and guidelines specific to Oracle on Azure IaaS, you can enhance end-user experiences and deliver consistency and reliability. This guidance addresses Oracle Databases including Exadata as a specific workload as well as other Oracle applications.

The Well-Architected Framework pillars also aim to involve modularity, separation of roles, and a way to improve operational productivity. This approach results in application workloads that avoid unnecessary complexities and unforeseen costs.

Consider making your Oracle on Azure IaaS workload well-architected for the following reasons:

- **Reliability**. An Oracle workload requires resiliency at the architecture layer. You’ll learn how to create an Database & Application architecture with high availability to process critical and non-critical business data.
- **Security**. An Oracle workload can contain business critical data. Most of the Oracle applications also require an ssh port. You’ll learn to secure your Oracle Databases and Applications with multiple security layers, including identity, access, input validation, data sovereignty and encryption.
- **Cost optimization**. An Oracle workload requires you to bring your own license. Begin optimizing your costs by conducting a Automatic Workload Repository (AWR) assessment. The AWR Assessment provides the ability to identify the required VM sku and storage in order to meet performance requirements. 
- **Operational Excellence**. An Oracle workload needs to be monitored to meet productivity requirements. The guidance within this pillar will guide you on how to make sure that your Oracle workload meets operational excellence standards.
- **Performance Efficiency**. An Oracle workload spends most of its lifecycle in operations. You’ll learn how to manage an Oracle workload to meet users' demands, being performant, while managing costs.

### Choose a migration approach

There are various approaches from which you can choose depending on the migration requirements. Common tools used by customers are Data Guard, RMAN, Goldengate, and Data Pump. Data Pump however is not recommended for a big volume of data. Whenever you plan to migrate it is recommended to check if the character set is the same and also has the same Endian. Cross-Platform migrations need to be assessed properly. 
The following documentation provides further information of [Migration planning](/azure/cloud-adoption-framework/scenarios/oracle-iaas/oracle-migration-planning).

## What are the key design areas?

Oracle on Azure IaaS includes the following design areas, which focus on the technical decision points for infrastructure components that are part of a workload and their interaction with the shared services.

|Design area|Summary|
|---|---|
|[Choose compute and storage](choose-compute-and-storage.md)|Right-sizing your infrastructure is crucial for performance and cost efficiency. The Oracle Migration Assistant Tool [OMAT](https://github.com/Azure/Oracle-Workloads-for-Azure/tree/main/omat) helps in selecting the right SKU for the database VM, and the best suited storage for the database, archive redologs and backup.|
|[Optimize business continuity and disaster recovery](optimize-business-continuity-disaster-recovery.md)|This section provides guidance on reliable failover architecture. It focuses on RPO and RTO requirements as well as High Availability.|
|[Optimize security](optimize-security.md)|Considerations for all aspects of security for Oracle workloads are discussed.|
|[Monitor workloads](monitor-workloads.md)|This section provides guidance on monitoring tooling which is essential in order to react quickly to developing issues and performance degradation.|
|[Design Oracle applications](design-applications.md)|This section provides guidance on what to consider when migrating typical Oracle applications to Azure.|

<!--## Assessment

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](...)-->

## Next steps

Start by reviewing design principles.

> [!div class="nextstepaction"]
> [Design principles](review-design-principles.md)
