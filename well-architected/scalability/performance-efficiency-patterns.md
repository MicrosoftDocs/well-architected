---
title: Performance efficiency design patterns
description: Use these performance efficiency design patterns to address variable workloads and peaks in activity for cloud applications.
author: dragon119
ms.author: martinek
ms.date: 05/03/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-pillar-efficiency
ms.custom:
  - design-pattern
keywords:
  - design pattern
---

# Performance Efficiency design patterns

Performance efficiency is the ability of your workload to meet the demands placed on it by users in an efficient manner. You need to anticipate increases in load to meet business requirements. To achieve performance efficiency, it's important to consider how your application scales and to implement platform as a service (PaaS) offerings that have built-in scaling operations.

Performance efficiency depends on the system's ability either to handle increases in load without impact on performance, or for the available resources to be readily increased. Performance efficiency concerns not just compute instances, but other elements such as data storage, messaging infrastructure, and application architecture.

The following table summarizes architectural design patterns that particularly relate to performance efficiency.

|                           Pattern                            |                                                                        Summary                                                                         |
|--------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
|               [Cache-aside](/azure/architecture/patterns/cache-aside)               |                                                   Load data on demand into a cache from a data store.                                                   |
| [Choreography](/azure/architecture/patterns/choreography) | Have each component of the system participate in the decision-making process about the workflow of a business transaction, instead of relying on a central point of control. |
|                      [Command query responsibility segregation (CQRS)](/azure/architecture/patterns/cqrs)                      |                           Segregate operations that read data from operations that update data by using separate interfaces.                           |
|            [Event sourcing](/azure/architecture/patterns/event-sourcing)            |                     Use an append-only store to record the full series of events that describe actions taken on data in a domain.                      |
|         [Deployment stamps](/azure/architecture/patterns/deployment-stamp)          |                                      Deploy multiple independent copies of application components, including data stores.                              |
| [Geodes](/azure/architecture/patterns/geodes) | Deploy backend services into a set of geographical nodes, each of which can service any client request in any region. |
|               [Index table](/azure/architecture/patterns/index-table)               |                                Create indexes over the fields in data stores that are frequently referenced by queries.                                |
|         [Materialized view](/azure/architecture/patterns/materialized-view)         |       Generate prepopulated views over the data in one or more data stores when the data isn't ideally formatted for a required query. operations.        |
|            [Priority queue](/azure/architecture/patterns/priority-queue)            | Prioritize requests sent to services so that requests with a higher priority are received and processed more quickly than those with a lower priority. |
| [Queue-based load leveling](/azure/architecture/patterns/queue-based-load-leveling) |              Use a queue that acts as a buffer between a task and a service that it invokes in order to smooth intermittent heavy loads.               |
|                  [Sharding](/azure/architecture/patterns/sharding)                  |                                           Divide a data store into a set of horizontal partitions or shards.                                           |
|    [Static content hosting](/azure/architecture/patterns/static-content-hosting)    |                          Deploy static content to a cloud-based storage service that can deliver the assets directly to the client.                          |
|                [Throttling](/azure/architecture/patterns/throttling)                |                Control the consumption of resources used by an instance of an application, an individual tenant, or an entire service.                 |
