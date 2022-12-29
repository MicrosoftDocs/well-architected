---
title: Oracle workloads
description: Oracle workloads best practices
author: ckittel
ms.author: kegorman
ms.date: 12/29/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---

# Oracle workload best practices

This article strives to address Oracle workloads comprising not only Oracle Databases, but also applications such as Siebel, Peoplesoft, JD Edwards, Hyperion, E-Business Suite or customized Weblogic applications.

Customers around the globe are looking for hosting Oracle Workloads on Azure. For that reason, Microsoft provides solutions for each workload and coherent requirements. The most requirements customers asked for are around network, reducing latencies, and uto-scaling solutions.

This article also provides guidance for Oracle Workloads on Azure that applies Well-Architected best practices as the technical foundation for building and operating a highly reliable and scalable solution on Azure.

## About Oracle workloads

The term workload refers to a collection of database and application resources that support a common business goal or the execution of a common business process. These workloads could be Customer Relationship Management applications, Human Resources, customized application that mostly relies on Weblogic and others. Weblogic, Siebel, Peoplesoft, Hyperion, JD Edwars and E-Business Suite are Oracle on-premises applications.  

Oracle Applications on Azure and its approach are described in Section Application Design.

An Oracle Workload therefore describes a collection of application resources and databases, which must be highly available on the platform. The workload must always be resilient to failures.

## What are the common challenges

Microsoft Azure makes it easy to deploy and manage cloud solutions. However, building and migrating Oracle Workloads that are highly available and reliable on the platform remains a challenge for these main reasons:

- **Oracle applications architectures are complex**. The complexity relies on the dependencies between the application and database tier, Version upgrades and patches. Siebel has an architecture change between the version IP16 and IP17. The architecture change results in adjustments on the application and database tier to configure application servers on the database layer.
- **Designing a reliable application at scale requires knowledge about the application versions and architectures as well as dependencies on each layer**. By providing in-depth knowledge, we can help you to select the right technologies and guide you through the configuration setup.
- **Oracle database architectures differ from customer to customer**. Oracle RAC, Exadata features like SmartScan put complexity into the setup. Automatic Workload Repositories provide a great insight into the actual usage of Exadata Features or RAC Setup’s. Microsoft Azure provides solutions for every challenge so that we can address most of your needs.  

All Oracle Workloads need to be architected to handle failures with correlated or cascading impact. Reliability Engineering is an important task within the entire architecture design.

## Are Oracle Workloads Only About Reliability?

While the primary focus in on Reliability and creating highly available applications and databases other pillars of the Well-Architected Framework are equally important during the architecture design and migration phase.

- **Security**: how user access applications and the overall workload mitigates security threats, such as Distributed Denial of Service (DDoS) attacks, will have a significant bearing on overall reliability.
- **Operational Excellence**: potentially reached throughput or Memory on the database respond to operational issues will have a direct impact on application availability. Latencies should be eliminated as much as possible.
- **Performance Efficiency**: availability is more than simple uptime, but rather a consistent level of application service and performance relative to a known healthy state.

Achieving high reliability imposes significant cost tradeoffs, which may not be justifiable for every workload scenario. It's therefore recommended that design decisions be driven by business requirements.

## What are the key design areas?

Oracle on Azure guidance within this series is composed of architectural considerations and recommendations orientated around these [key design areas](/azure/architecture/framework/mission-critical/mission-critical-overview).

:::image type="content" source="./images/mission-critical-design-areas.svg" alt-text="Diagram that shows how to use this Oracle workload guidance throughout the lifecycle of an Oracle workload." lightbox="./images/mission-critical-design-areas.svg" border="false":::

| Design area | Summary |
| --- | --- |
| Application design |The use of a scale-unit architecture in the context of lift and shift highly reliable Oracle Applications. Also explores the cloud application design patterns that allow for scaling, and error handling. Next to this explore application dependencies. |
| Data Platform | Oracle on Azure offer different Virtual Machine sizes. The choice is based on the statistics of an AWR report delivered by the customer. AWR should always be handed in on peak-load.|
| Security | Protect the application and the database against threats intended to compromise its reliability directly or indirectly.|
| Networking & Connectivity | Network topology concepts at an application level. Includes required ports, latency. Critical recommendations intended to inform the design of a secure and scalable global network topology.|
| Operational Procedures | Adoption of Infrastructure as Code and related deployment methods is used to drive effective and consistent operational procedures.|
| Health Modeling | Processes to define a robust health model, mapping quantified application health states through observability and operational constructs to achieve operational maturity.|
| Deployment & Testing | Eradicate downtime and maintain application health for deployment operations, providing key considerations and recommendations intended to inform the design of optimal upgrades of application and database.|

We invite you to explore Oracle workload design best practices and return to this content regularly throughout the lifecycle of your Oracle workload. The content highlights critical areas of focus but also refers you to other documentation for deeper technical insight.

## Illustrative Examples

The Oracle workload guidance provided within this series is based on a solution-orientated approach to illustrate key design considerations and recommendations. There are several reference implementations and scripts available.

For more information, see the [Oracle reference architecture](/azure/virtual-machines/workloads/oracle/oracle-reference-architecture).



We invite you to explore Oracle workload design best practices and return to this content regularly throughout the lifecycle of your Oracle workload. The content highlights critical areas of focus but also refers you to other documentation for deeper technical insight.

For more information, see:

- [Azure Center for Oracle Solutions]( /azure/center-Oracle-solutions/overview)
- [Oracle workload in Azure](/azure/virtual-machines/workloads/Oracle/get-started)
- [Oracle workload architectures](/azure/architecture/reference-architectures/Oracle/Oracle-overview)

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