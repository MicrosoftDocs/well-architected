---
title: Reliability patterns
titleSuffix: Cloud Design Patterns
description: Learn about availability, high availability, and resiliency as factors that are related to reliability patterns.
author: martinekuan
ms.author: martinek
ms.date: 05/02/2023
ms.topic: conceptual
ms.custom:
  - design-pattern
keywords:
  - design pattern
---

# Reliability patterns

The document offers an insightful and comprehensive resource for understanding the principles and techniques behind building highly reliable and resilient systems in the cloud. Covering a wide range of topics such as fault tolerance, redundancy, monitoring, and recovery, this guide equips architects and developers with essential knowledge to design robust solutions that can withstand failures and deliver continuous availability.

## Availability

Availability is measured as a percentage of uptime, and defines the proportion of time that a system is functional and working. Availability is affected by system errors, infrastructure problems, malicious attacks, and system load. Cloud applications typically provide users with a service-level agreement (SLA), which means that applications must be designed and implemented to maximize availability.

| Pattern | Summary |
|:--------|:--------|
| [Deployment Stamps](/azure/architecture/patterns/deployment-stamp) | Deploy multiple independent copies of application components, including data stores. |
| [Geode](/azure/architecture/patterns/geodes) | Deploy backend services into a set of geographical nodes, each of which can service any client request in any region. |
| [Health Endpoint Monitoring](/azure/architecture/patterns/health-endpoint-monitoring) | Implement functional checks in an application that external tools can access through exposed endpoints at regular intervals. |
| [Queue-Based Load Leveling](/azure/architecture/patterns/queue-based-load-leveling) | Use a queue that acts as a buffer between a task and a service that it invokes to smooth intermittent heavy loads. |
| [Rate Limit Pattern](/azure/architecture/patterns/rate-limiting-pattern) | Limiting pattern to help you avoid or minimize throttling errors related to these throttling limits and to help you more accurately predict throughput. |
| [Throttling](/azure/architecture/patterns/throttling) | Control the consumption of resources by an instance of an application, an individual tenant, or an entire service. |

To mitigate against availability risks from malicious distributed denial of service (DDoS) attacks, implement the native [Azure DDoS protection](/azure/virtual-network/ddos-protection-overview) service or a third-party capability.

## High availability

Azure infrastructure is composed of geographies, regions, and availability zones. These divisions limit the radius of a failure and therefore limit potential effect on customer applications and data. The Azure availability zones construct was developed to provide a software and networking solution to protect against datacenter failures and to provide increased high availability. With high availability architecture, there's a balance between high resilience, low latency, and cost.

| Pattern | Summary |
|:--------|:--------|
| [Deployment Stamps](/azure/architecture/patterns/deployment-stamp) | Deploy multiple independent copies of application components, including data stores. |
| [Geode](/azure/architecture/patterns/geodes) | Deploy backend services into a set of geographical nodes. Each node can service any client request in any region. |
| [Health Endpoint Monitoring](/azure/architecture/patterns/health-endpoint-monitoring) | Implement functional checks in an application that external tools can access through exposed endpoints at regular intervals. |
| [Bulkhead](/azure/architecture/patterns/bulkhead) | Isolate elements of an application into pools. If one element fails, the others continue to function. |
| [Circuit Breaker](/azure/architecture/patterns/circuit-breaker) | Handle faults that might take a variable amount of time to fix when connecting to a remote service or resource. |

## Resiliency

Resiliency is the ability of a system to gracefully handle and recover from failures, both inadvertent and malicious.

In cloud hosting, applications are often multi-tenant, use shared platform services, compete for resources and bandwidth, communicate over the Internet, and run on commodity hardware. This situation means there's an increased likelihood for both transient and permanent faults to arise. The connected nature of the internet and the rise in sophistication and volume of attacks increase the likelihood of a security disruption.

To detect failures and recovering quickly and efficiently, it's necessary to maintain resiliency.

| Pattern | Summary |
|:--------|:--------|
| [Bulkhead](/azure/architecture/patterns/bulkhead) | Isolate elements of an application into pools. If one element fails, the others continue to function. |
| [Circuit Breaker](/azure/architecture/patterns/circuit-breaker) | Handle faults that might take a variable amount of time to fix when connecting to a remote service or resource. |
| [Compensating Transaction](/azure/architecture/patterns/compensating-transaction) | Undo the work performed by a series of steps, which together define an eventually consistent operation. |
| [Health Endpoint Monitoring](/azure/architecture/patterns/health-endpoint-monitoring) | Implement functional checks in an application that external tools can access through exposed endpoints at regular intervals. |
| [Leader Election](/azure/architecture/patterns/leader-election) | Coordinate the actions performed by a collection of collaborating task instances in a distributed application by electing one instance as the leader. The leader assumes responsibility for managing the other instances. |
| [Queue-Based Load Leveling](/azure/architecture/patterns/queue-based-load-leveling) | Use a queue that acts as a buffer between a task and a service that it invokes. This queue smooths intermittent heavy loads. |
| [Retry](/azure/architecture/patterns/retry) | Enable an application to handle anticipated, temporary failures when it tries to connect to a service or network resource by transparently retrying an operation that's previously failed. |
| [Scheduler Agent Supervisor](/azure/architecture/patterns/scheduler-agent-supervisor) | Coordinate a set of actions across a distributed set of services and other remote resources. |
