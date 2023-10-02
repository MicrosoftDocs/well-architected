---
title: Oracle on Azure IaaS workloads
description: See general guidance and resources for running Oracle on Azure IaaS.
author: jessiehaessler
ms.author: prwilk
ms.date:
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-oracle
---

# Get Started
**
This guidance is intended to provide **application owner**, **Oracle Database Administrator**, **business stakeholder**, **architects** best practices for well-architecting Oracle on IaaS.
You can use this workload documentation as your go-to resource for setting up approriate architecture for your Oracle workload. The section about Oracle workloads on Azure provides guidance that applies the Azure Well-Architected best practices as the technical foundation for building and operating a highly reliable solution on Azure at-scale.

Oracle on Azure is delivered in the infrastructure as a service (IaaS) cloud model. Another Option for Oracle on Azure IaaS as PaaS solution can be delivered through the use of [Tessell](https://www.tessell.com/azure/oracle) , which can be provisioned through the marketplace. 

## About Oracle Workloads

The term workload refers to a collection of database(s) including Exadata and application resources that support a common business goal or the execution of a common business process. Oracle workloads must be highly available on the platform, always be resilient to failures. 
These workloads could be Customer Relationship Management applications, Human Resources, customized applications that mostly relies on WebLogic Server and others. WebLogic Server, Siebel, Peoplesoft, JD Edwards and E-Business Suite are Oracle on-premise applications. 
Oracle applications on Azure and its approach are described within the Design Area.
An Oracle Workload therefore describes a collection of application resources and databases, 


## What are Common Challenges?

Microsoft Azure makes it easy to deploy and manage cloud solutions. However, building and migrating Oracle workloads that are highly available and reliable on the platform remains a challenge for these main reasons:
    - Oracle applications architectures are complex. The complexity relies on the dependencies between the application and database tier, version upgrades and patches. 
    - Designing a reliable application at scale requires knowledge about the application versions and architectures as well as dependencies on each layer. By providing in-depth knowledge, we can help you to select the right technologies and guide you through the configuration setup.
    - Oracle database architectures differ from customer to customer. Oracle Real Application Clustering or Exadata features like SmartScan, Storage Indexes put complexity into the setup. Automatic Workload Repositories provide a great insight into the actual usage of Exadata Features or RAC Setup’s. Microsoft Azure provides storage solutions for your challenge so that we can address most of your needs.
Summarized all Oracle workloads need to be architected to handle failures with correlated or cascading impact. Reliability Engineering is an important task within the entire architecture design.

## The Well-Architected Framework approach

A well-architected workload is structured to meet specific performance, reliability, security, and cost optimization and performance efficiency objectives. By following architectural principles and guidelines specific to Oracle on Azure IaaS, you can enhance end-user experiences and deliver consistency and reliability. This guidance addresses Oracle Databases including Exadata as a specific workload as well as Oracle applications.

The Well-Architected Framework pillars also aim to involve modularity, separation of roles, and a way to improve operational productivity. This approach results in application workloads that avoid unnecessary complexities and unforeseen costs.

Consider making your Oracle on Azure IaaS workload well-architected for the following reasons:

- **Reliability**. An Oracle workload requires resiliency at the architecture layer. You’ll learn how to create an Database & Application architecture with high availability to process critical and non-critical business data.
- **Security**. An Oracle workload can contain business critical data. Most of the Oracle applications also require an ssh port. You’ll learn to secure your Oracle Databases and Applications with multiple security layers, including identity, access, input validation, data sovereignty, and encryption.
- **Cost optimization**. An Oracle workload requires you to bring your own license. We begin to optimize your costs by conducting a Automatic Workload Repository (AWR) assessment. The AWR Assessments provides the ability to match you with the required VM shape and storage in order to meet performance requirements. 
- **Operational Excellence**. An Oracle workload needs to be monitored to meet productivity requirements. You’ll learn how to make sure that your Oracle workload meets user demands, being performant, while managing costs.
- **Performance Efficiency**. An Oracle workload spends most of its lifecycle in operations. You’ll learn how to manage an Oracle workload and to keep it running.

If you don't apply these principles to your Oracle on Azure IaaS architecture, you might encounter various operational, performance, and security challenges. 

### Choose a migration approach

There are various approaches of which you can choose from depending on your requirements. Common tools used by customers are Goldengate, Data Guard, RMAN, Data Pump. Data Pump however is not recommended for a big volume of data. 

## What are the key design areas?

Oracle on Azure IaaS follows the following design areas, which focus on the technical decision points for infrastructure components that are part of a workload and their interactivity with the shared services.

|Design area|Summary|
|---|---|
|[Compute and Storage](...)|Right-size your current environment is crucial. We provide the [OMAT tooling](https://github.com/Azure/Oracle-Workloads-for-Azure/tree/main/omat) and support doing so. This exercise is crucial for performance and cost efficiency and provides the right-sized architecture including the VM for the Database, best suited storage for the Database size, archive redologs and backup.|
|[Business Continuity and Disaster Recovery](...)|This section provides guidance on reliable failover architecture. It focuses on RPO and RTO requirements as well as High Availability.|
|[Security](...)|Considerations for identity methods for Oracle workloads|
|[Monitoring](...d)|In order to react quickly a monitoring tooling is essential. This section will provide you guidance.|
|[Operations](...)|Ensure that day-to-day management and maintenance of your Oracle on Azure IaaS environment remains secure, efficient, and reliable over time.|


## Assessment

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](...)

## Next steps

Start by reviewing design principles.

> [!div class="nextstepaction"]
> [Design principles](...)
