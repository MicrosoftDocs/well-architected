---
title: Tradeoffs for cost
description: Learn about tradeoffs you might make when optimizing a workload for cost, such as with reliability, performance efficiency, security, or operational excellence.
author: martinekuan
ms.author: martinek
ms.date: 04/21/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-pillar-optimization
products:
  - azure
  - azure-cost-management
categories:
  - cost-optimization
ms.custom:
  - article
  - internal-intro
---

# Tradeoffs for cost

As you design a workload, consider tradeoffs between cost optimization and other aspects of the design, such as security, scalability, resilience, and operability.

**What is most important for the business: lowest cost, no downtime, high throughput?**
***
An optimal design doesn't equate to a low-cost design. You might make risky choices in favor of a cheaper solution.

## Cost versus reliability

Cost has a direct correlation with reliability.

**Does the cost of high availability components exceed the acceptable downtime?**
***

Overall service level agreement (SLA), Recovery Time Objective (RTO), and Recovery Point Objective (RPO) might lead to expensive design choices. If your service SLAs, RTOs, and RPOs times are short, greater investment is inevitable for high availability and disaster recovery options.

For example, to support high availability, you choose to host the application across regions. This choice is costlier than single region because of the replication costs or the need for provisioning extra nodes. Data transfer between regions also adds cost.

If the cost of high availability exceeds the cost of downtime, you can save by using Azure platform-managed replication and then recover data from the backup storage.

For resiliency, availability, and reliability considerations, see the [Reliability](../resiliency/index.yml) pillar.

## Cost versus performance efficiency

Boosting performance leads to higher costs.

Many factors affect performance.

* **Fixed or consumption-based provisioning**. Avoid cost estimating a workload at consistently high utilization. Consumption-based pricing is more expensive than the equivalent provisioned pricing. Smooth out the peaks to get a consistent flow of compute and data. Ideally, use manual scaling and autoscaling to find the right balance. Scaling up is more expensive than scaling out.

* **Azure regions**. Cost scales directly with the number of regions. Locating resources in cheaper regions shouldn't negate the cost of network ingress and egress or by degraded application performance because of increased latency.

* **Caching**. Every payload's render cycle consumes both compute and memory. Use caching to reduce load on servers and save with precanned storage and bandwidth costs. The savings can be dramatic, especially for static content services.

  * While caching can reduce cost, there are some performance tradeoffs. For example, Azure Traffic Manager pricing is based on the number of DNS queries that reach the service. Reduce that number through caching and configure how often the cache refreshes. Relying on the cache that isn't frequently updated causes longer user failover times if an endpoint is unavailable.

* **Batch or real-time processing**. Using dedicated resources for batch processing of long-running jobs increases the cost. You can lower cost by provisioning Spot VMs but be prepared for the job to be interrupted every time Azure evicts the VM.

For performance considerations, see the [Performance efficiency](../scalability/overview.md) pillar.

## Cost versus security

Increasing security of the workload increases costs.

As a rule, don't compromise on security. With certain workloads, you can't avoid security costs. For example, for specific security and compliance requirements, deploying to differentiated regions is more expensive. Premium security features can also increase the cost. There are areas where you can reduce costs by using native security features. For example, avoid implementing custom roles if you can use built-in roles.

For security considerations, see the [Security pillar](../security/overview.md).

## Cost versus operational excellence

Investing in systems monitoring and automation might increase the cost initially but over time reduces cost. Integrate IT operations processes like user or application access provisioning, incident response, and disaster recovery with the workload.

Cost of maintaining infrastructure is more expensive. With platform as a service (PaaS) or software as a service (SaaS) services, infrastructure, platform management services, and other operational efficiencies are included in the service pricing.

For operational considerations, see the [Operational excellence](../devops/overview.md) pillar.
