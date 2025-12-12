---
title: Design Principles for HPC Workloads on Azure
description: Review the design principles of the Azure Well-Architected Framework. Learn how to apply these principles to an HPC workload.
author: padmalathas
ms.author: prwilk
ms.date: 12/12/2025
ms.topic: concept-article
ms.update-cycle: 180-days  
---

# Design Principles for HPC Workloads

The HPC design methodology is underpinned by five key design principles derived from the Well-Architected Framework pillars. These principles serve as a compass for subsequent design decisions across critical design areas. We highly recommend that you familiarize yourself with these principles to better understand their impact and the trade-offs associated with non-adherence.

> [!NOTE]
> There are obvious cost trade-offs associated with maximizing performance and reliability. Design decisions should be driven by business requirements and acceptable trade-offs between performance, reliability, and cost.

## Reliability

| Design Principle | Considerations |
|------------------|----------------|
| **Define recovery objectives** | Establish how much work can be lost and how quickly the environment must be restored after failures. These targets inform backup frequency, checkpoint strategies, and failover procedures. |
| **Implement job checkpointing** | Long-running jobs should periodically save progress to durable storage, enabling them to resume from the last saved state rather than restarting from the beginning after failures. |
| **Design for node failure** | Expect that individual compute nodes may fail during job execution. Ensure the system can detect failures and either reassign work automatically or minimize lost progress. |
| **Ensure data durability** | Protect valuable input datasets and results through replication or backups. Match protection levels to data value—critical results need stronger protection than temporary working files. |
| **Plan for site-level resilience** | For critical workloads, establish procedures to resume computation at an alternate location. Maintain offsite data copies and document configurations for recovery. |

## Performance Efficiency

| Design Principle | Considerations |
|------------------|----------------|
| **Match hardware to workload needs** | Different workloads have different bottlenecks—some need fast processors, others need large memory, fast storage, or GPU accelerators. Profile applications and select hardware accordingly. |
| **Minimize communication delays** | When jobs require frequent coordination between nodes, network speed becomes critical. Deploy high-speed interconnects and keep communicating nodes physically close together. |
| **Optimize storage for parallel access** | When many nodes read or write data simultaneously, standard storage becomes a bottleneck. Use storage systems designed for high concurrent throughput. |
| **Right-size clusters using benchmarks** | Test with representative workloads to find the optimal balance of resources. Avoid both over-provisioning (wasted cost) and under-provisioning (poor performance). |
| **Standardize compute environments** | Deploy all nodes from identical, pre-configured images with required software installed. Consistent environments prevent job failures and ensure reproducible performance. |

## Operational Excellence

| Design Principle | Considerations |
|------------------|----------------|
| **Automate cluster provisioning** | Define infrastructure as code to enable rapid, repeatable deployment. Automation reduces setup errors and time spent on manual configuration. |
| **Integrate familiar job schedulers** | Support existing tools and workflows that teams already use. Users should submit jobs the same way regardless of where workloads run. |
| **Apply software development practices** | Version control application code, automate testing, and manage deployments systematically. This improves software quality and catches bugs before they crash long-running jobs. |
| **Orchestrate end-to-end workflows** | Automate the full pipeline from data preparation through computation to results delivery. This eliminates manual handoffs and ensures reproducible execution. |
| **Expose capabilities through APIs** | Enable programmatic job submission, monitoring, and integration with other systems. This allows HPC to function as part of larger automated workflows. |

## Security

| Design Principle | Considerations |
|------------------|----------------|
| **Implement network segmentation** | Isolate compute resources from general networks. Restrict access to authorized users and limit communication to required pathways. |
| **Enforce role-based access control** | Grant users only the permissions they need. Separate privileges between user groups and restrict administrative actions to authorized operators. |
| **Meet compliance requirements** | Validate that the environment meets applicable regulatory standards when processing sensitive or regulated data. Implement required controls and audit trails. |
| **Secure the compute layer** | Ensure appropriate isolation between users and workloads. Restrict and audit privileged access to compute nodes. |
| **Protect data in transit and at rest** | Encrypt sensitive data during storage and transfer. Implement access controls to ensure only authorized users and jobs can access protected data. |

## Cost Optimization

| Design Principle | Considerations |
|------------------|----------------|
| **Implement storage lifecycle management** | Automatically move aging data from high-performance storage to lower-cost tiers based on access patterns. Balance performance needs against storage costs. |
| **Right-size compute for actual usage** | Profile applications to understand true resource consumption. Avoid paying for capabilities that workloads won't use. |
| **Scale dynamically with demand** | Provision resources when jobs are waiting and release them when queues are empty. Align infrastructure costs with actual usage rather than peak capacity. |
| **Select appropriate orchestration** | Evaluate managed services versus self-managed schedulers based on total cost including operational overhead. Match the approach to workload patterns. |
| **Leverage discounted capacity** | For workloads that can tolerate interruption, use lower-cost preemptible compute. Implement checkpointing so interrupted jobs can resume without losing progress. |

## Next Steps

Design areas are interconnected and changes in one area may impact others. Start with the area most critical to your workload, then review how choices create trade-offs across the architecture.
