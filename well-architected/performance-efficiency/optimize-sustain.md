---
title: Sustain performance efficiency over time
description: Learn to sustain performance efficiency over time. Optimize autoscaling, separate out critical workload, right-size your resources, and remote antipatterns.
author: martinekuan
ms.author: martinek
ms.date: 5/04/2023
ms.topic: conceptual
categories:
  - management-and-governance
---

# Sustain performance efficiency over time

Performance is an indication of the responsiveness of a system to execute any action within a given time interval. As workloads and requirements change over time, you need to continuously ensure that systems don't start to under-perform. This article explains some steps you can take to help make sure your systems are continuously running at an optimum level.

## Optimize autoscaling

[Autoscaling](./design-scale.md#use-autoscaling-to-manage-load-increases-and-decreases) is the process of dynamically allocating resources to match performance requirements. As the volume of work grows, an application may need more resources to maintain the desired performance levels and satisfy service-level agreements (SLAs). As demand slackens, and the extra resources are no longer needed, they can be deallocated to minimize costs.

To prevent a system from attempting to scale out excessively, consider limiting the maximum number of instances that can be automatically added. Doing so helps you avoid the costs associated with running potentially many thousands of instances. Most autoscaling mechanisms allow you to specify the minimum and maximum number of instances for a rule. Also consider gracefully degrading the functionality that the system provides if the maximum number of instances have been deployed and the system is still overloaded.

But autoscaling might not always be the most appropriate mechanism to handle a sudden burst in workload. It takes time to provision and start new instances of a service or add resources to a system, and the peak demand may have passed by the time these extra resources have been made available. In this scenario, it may be better to throttle the service. For more information, see [Throttling pattern](/azure/architecture/patterns/throttling).

If you do need the capacity to process all requests when the volume fluctuates rapidly, you can use an aggressive autoscaling strategy. Aggressive autoscaling does increase costs, but it adds the extra instances you need more quickly. You can also use a scheduled policy that starts a sufficient number of instances to meet the maximum load before that load is expected.

## Separate out critical workload

Some data is accessed more frequently than other data. If you store data in its own partition depending on its usage pattern, your system can run more efficiently. Operations that affect more than one partition can run in parallel.

[Partitioning](./optimize-partition.md) data can improve the availability of applications by ensuring that the entire dataset doesn't constitute a single point of failure and that subsets of the dataset can be managed independently. For this reason, you might want to separate out critical workload from noncritical. If one instance fails, only the data in that partition is unavailable. Operations on other partitions can continue. For managed PaaS data stores, this consideration is less relevant, because these services are designed with built-in redundancy.

To learn more, see [Data partitioning](/azure/architecture/best-practices/data-partitioning).

## Right-size your resources

Changes to the resources that support a workload may affect the architecture of the workload. When this situation happens, other considerations are required to minimize the effect on end users and business functions. One of these considerations is right-sizing, which is about controlling cost by continuously monitoring and adjusting size of your instances to meet needs. Cost shouldn't necessarily be the main decision-making factor. Choosing the least expensive option could expose the workload to performance and availability risks.

For example, when you consider pricing and sizing resources hosted in Azure, right-sizing virtual machines (VMs) is best practice. Choosing the right storage type for data can save your organization several thousands of dollars every month. Microsoft offers many options, and each VM type has specific features and different combinations of CPU, memory, and disks.

To learn more about right sizing best practices with VMs, see [Best practice: Right-size VMs](/azure/cloud-adoption-framework/govern/cost-management/best-practices#best-practice-right-size-vms).

### For accountability purposes

Identify right-size opportunities by reviewing your current resource utilization and performance requirements across the environment. Then, modify each resource to use the smallest instance or SKU that can support the performance requirements of each resource.

For other best practices, see [Best practices by team and accountability](/azure/cloud-adoption-framework/govern/cost-management/best-practices#best-practices-by-team-and-accountability).

### For operational cost management purposes

Review your environment's current resource utilization and performance requirements. Then, identify resources that have remained underutilized (generally more than 90 days). Also, right-size provisioned SKUs by modifying underutilized resources to use the smallest instance or SKU that can support the performance requirements of each resource. Finally, right-size redundancy. If the resource doesn't require a high degree of redundancy, remove geo-redundant storage.

For other best practices, see [Operational cost management best practices](/azure/cloud-adoption-framework/govern/cost-management/best-practices#operational-cost-management-best-practices).

## Remove antipatterns

A performance antipattern is a common practice that is likely to cause scalability problems when an application is under pressure. Some of the following common antipatterns can occur when a system offloads too much processing to a data store:

* Moving resource-intensive tasks onto background threads.
* Continually sending many small network requests.
* Failing to cache data.

Antipatterns can cause decreased response time, high latency, slow I/O calls, and other performance issues.

Many factors can cause an antipattern to occur. Sometimes an application inherits a design that worked on-premises, but doesn't scale in the cloud. Or, an application might start with a clean design, but as new features are added, one or more of these antipatterns can appear.

Removing antipatterns can improve performance efficiency. But removal isn't a straight-forward task, because sometimes the problem only manifests under certain circumstances. Instrumentation and logging are key to finding the root cause, but you also have to know what to look for. To learn more about common antipatterns and how to identify and fix them, see [Catalog of antipatterns](/azure/architecture/antipatterns/#catalog-of-antipatterns).
