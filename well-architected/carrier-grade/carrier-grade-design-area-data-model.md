---
title: Data modeling for carrier-grade workloads
description: This article provides an overview of the Data model design area for carrier-grade workloads.
author: mikedell73
ms.author: mikedell
ms.date: 08/23/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.industry: telecommunications
products: 
- azure-app-service
- azure-traffic-manager
- cosmos-db
ms.custom:
  - carrier-grade
---

# Data modeling for carrier-grade workloads

Enterprise applications deployed in a single region can typically ignore this model of application design and safely delegate responsibility to the database layer to make data reliably available. This behavior isn't the case for carrier-grade applications, which must be deployed across multiple regions. Multi-region deployment forces the application architect to consider the compromises they're willing to accept for their data.

## CAP theorem

Databases can provide three key properties for the data they manage for an application, known as CAP:
  - Consistency: A data read returns the most recent write.
  - Availability: Every request receives a valid response.
  - Partition tolerance: The system continues to operate despite delay or total loss of communication between elements.  

The CAP theorem states that a database layer can't provide all three of these properties for the same data at the same time in the presence of network partitions. The architect needs to make explicit design decisions about which of the CAP properties to favor under which conditions, and how to deal with the edge cases.

According to the CAP theorem, any database can only guarantee two out of three possible properties for the same data at the same time in the presence of network partition.  

Multi-region deployment means partition tolerance becomes significant. In most cases, carrier-grade architects prioritize partition tolerance and availability over consistency. For each type of data, the architect must consider what tradeoffs they're willing to make, considering the edge cases.

For example, consider the database of system users. Is it acceptable for the user database to drop to read-only if there's a network partition? This behavior prioritizes consistency and read availability over write availability. This prioritization may not be suitable if it's unacceptable for a user to access an isolated site after their permissions are revoked elsewhere. The described scenario would require all database access to be blocked if there's a partition, which prioritizes write availability over read availability.

> [!NOTE]
> The compromises made can be different for different databases within the same application, since the databases are likely to have different usage profiles.

Where consistency is the compromise, the application must cope with data inconsistency artifacts by using conflict-free replicated data types (CRDTs). The use of CRDTs requires discipline in the application design and implementation. Their use means data merges following partition events can be handled automatically by the data layer without human intervention or complex application-level logic.

> [!IMPORTANT]
> More details on data platform choices for your mission-critical workload is available [here](/azure/architecture/framework/mission-critical/mission-critical-data-platform)

The architect must also understand the tradeoffs in the data model, which were made within the dependent services. Those tradeoffs impact the service delivered to their application because those tradeoffs may not align with the application-level requirements.

## Next step

Review the health modeling design area for carrier-grade workloads.

> [!div class="nextstepaction"]
> [Design area: Health modeling](./carrier-grade-design-area-health-modeling.md)

