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

Achieving high reliability imposes significant cost tradeoffs, which may not be justifiable for every workload scenario. It is therefore recommended that design decisions be driven by business requirements.

## What are the key design areas?

Oracle on Azure guidance within this series is composed of architectural considerations and recommendations orientated around these key design areas.

