---
title: Architecture pattern for HPC workloads on Azure
description: The design areas represent the architecturally significant topics that must be discussed and designed for when defining a target architecture
author: padmalathas
ms.author: prwilk
ms.date: 12/04/2025
ms.topic: concept-article
---

# Architecture pattern for HPC workloads on Azure

This article presents a key pattern for HPC architectures on Azure. Apply this pattern when you start your design process, and then select components that are best suited for your business requirements. The article recommends a north star design approach and includes considerations for different HPC deployment patterns.

We recommend that you evaluate [**the key design areas**](/azure/well-architected/hpc/hpc-workloads-overview#what-are-the-key-design-areas), define the critical user and system flows that use the underlying components, and develop an understanding of HPC resources and their configuration while keeping in mind the following characteristics.

:::image type="content" source="./images/hpc-architecture.png" alt-text="Diagram that shows the typical architecture of an HPC workload." lightbox="./images/hpc-architecture.png":::


|Characteristic|Considerations|
|---|---|
| **Workload type** | Is the workload tightly coupled (requiring low-latency communication), loosely coupled (embarrassingly parallel), GPU-accelerated, memory-intensive, or data-intensive? |
| **Scale requirements** | What is the expected throughput for compute, storage, and network? How many concurrent jobs need to run? What is the peak demand? |
| **Performance needs** | What are the latency requirements for inter-node communication? What storage throughput is required? What is the acceptable job completion time? |
| **Data lifecycle** | How much data is generated per job? How long must results be retained? What are the access patterns for input and output data? |
| **Cost constraints** | What is the budget for compute resources? Can spot instances or preemptible capacity be used? What are the tradeoffs between performance and cost? |
| **Compliance requirements** | Are there data residency or sovereignty requirements? What audit and logging capabilities are needed? What security controls must be enforced? |

Based on the preceding characteristics, classify and identify critical HPC resources. That activity can help track resource utilization and associated costs, while helping you focus optimization efforts where they matter most. We recommend that you organize resources into logical groups based on their function and lifetime.

## Global resources

Certain resources are globally shared across all regional deployments. Common examples include resources that distribute traffic across multiple regions, manage user authentication and authorization, provide centralized monitoring and logging, and maintain global configuration.

| Characteristic | Considerations |
|---|---|
| **Resource lifecycle** | These resources are expected to be long living, persisting across the entire HPC environment lifecycle. They're managed with in-place updates and should support zero-downtime operations to avoid disrupting running jobs. |
| **Data persistence** | These resources store global state such as user accounts, authentication tokens, job metadata, and aggregated monitoring data that needs to be accessible across all regional clusters. |
| **Distribution model** | The resources should be globally accessible with appropriate latency characteristics. They need to communicate effectively with regional compute clusters while maintaining security boundaries and access controls. |
| **Inter-resource dependencies** | These resources should minimize dependencies on regional components to avoid creating global failure points. Replicate critical authentication and configuration data across regions to ensure continuous availability. |
| **Aggregate capacity** | These resources handle combined load from all regional clusters. They must scale to accommodate the total number of users, jobs, and monitoring data points across the entire HPC environment. |
| **Resilience strategy** | Regional compute clusters depend on these global resources for authentication and configuration. Design global resources with high availability and disaster recovery to prevent system-wide disruptions. |

## Regional compute cluster resources

The regional cluster contains the compute nodes, schedulers, and supporting infrastructure that execute HPC jobs. A region typically hosts one or more independent clusters that can be scaled based on demand.

| Characteristic | Considerations |
|---|---|
| **Node provisioning model** | Compute nodes are ephemeral and can be added or removed dynamically based on job queue depth and demand patterns. The cluster management infrastructure persists longer but should support rapid scaling for burst workloads. |
| **Job state management** | Compute nodes should be stateless to enable elastic scaling. All job input data, output results, and checkpoints must reside in shared storage systems accessible to any node in the cluster. |
| **Cluster isolation** | Clusters communicate with regional storage and high-performance networks. Design clusters to operate independently without dependencies on other clusters or regions to maintain fault isolation during failures. |
| **External service dependencies** | Cluster resources depend on regional storage, job schedulers, and monitoring systems. Minimize external dependencies beyond these core services to reduce the blast radius of failures. |
| **Computational throughput** | Determine cluster capacity through benchmarking with representative workloads. Consider maximum job concurrency, node-to-node communication bandwidth, and the ability to handle workload spikes from failover scenarios. |
| **Cluster recovery approach** | Because compute nodes are ephemeral and stateless, recovery involves provisioning replacement nodes rather than repairing failed hardware. Failed clusters can be rapidly rebuilt from infrastructure templates. |

## Regional supporting resources

A region contains resources that support the compute clusters but outlive individual compute nodes. Examples include parallel file systems, job schedulers, monitoring systems, and network infrastructure.

| Characteristic | Considerations |
|---|---|
| **Infrastructure persistence** | These resources outlive ephemeral compute nodes and persist across cluster scaling events. They remain operational throughout individual job lifecycles and continue functioning as compute capacity scales up and down. |
| **Storage and queue state** | Regional resources store job queues, file system data, performance metrics, and operational logs. This state is region-specific and serves the local compute clusters without requiring global distribution. |
| **Netowrk boundaries** | The resources serve compute clusters within their region and don't require global distribution. They communicate with local clusters and global resources but avoid cross-region traffic to minimize latency. |
| **Service integration** | These resources integrate with global resources for authentication, centralized monitoring, and configuration management. They operate independently from other regional deployments to prevent cascading failures. |
| **Regional capacity planning** | Size regional resources to handle aggregate demand from all compute clusters within the region. Plan for peak workload scenarios including burst jobs and potential failover traffic from other regions. |
| **Data protection strategy** | Regional resources require backup and recovery strategies appropriate to their function. Critical data like job metadata and user files should be replicated or backed up to enable recovery after regional failures. |

## Talk about illustrations from the HPC-CAF.
The following architecture illustrates the typical pattern for HPC workloads. The architecture includes user access and job submission, orchestration and scheduling, compute clusters, high-performance interconnects, storage systems, and management infrastructure.

:::image type="content" source="./images/hpc-architecture.png" alt-text="Diagram that shows the typical architecture of an HPC workload." lightbox="./images/hpc-architecture.png":::

Should we talk about architecture flow and architecture components after this?
