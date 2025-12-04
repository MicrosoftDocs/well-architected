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
| **Lifetime** | These resources are expected to be long living. Their lifetime spans the life of the system or longer. They're managed with in-place updates and should support zero-downtime operations. |
| **State** | These resources often store global state such as user accounts, authentication tokens, job metadata, and monitoring data that needs to be accessible across all regions. |
| **Reach** | The resources should be globally accessible with appropriate latency characteristics. They need to communicate effectively with regional resources while maintaining security boundaries. |
| **Dependencies** | The resources should minimize dependencies on regional resources to avoid creating global failure points. Replicate critical data across regions to ensure availability. |
| **Scale limits** | These resources handle aggregate load from all regions. They must scale to accommodate the combined throughput of the entire system. |
| **Availability and disaster recovery** | Regional resources depend on global resources. Configure global resources with high availability and disaster recovery to protect system-wide health. |

## Regional compute cluster resources

The regional cluster contains the compute nodes, schedulers, and supporting infrastructure that execute HPC jobs. A region typically hosts one or more independent clusters that can be scaled based on demand.

| Characteristic | Considerations |
|---|---|
| **Lifetime** | Compute nodes are ephemeral and can be added or removed dynamically based on workload demand. The cluster management infrastructure persists longer but should support rapid scaling. |
| **State** | Compute nodes should be stateless. All persistent state lives in storage systems or databases outside the compute cluster. This enables rapid scaling and recovery from failures. |
| **Reach** | Clusters communicate with regional storage and networking resources. Avoid dependencies on other regions or other clusters to maintain independence and fault isolation. |
| **Dependencies** | Cluster resources depend on regional storage, networking, and monitoring systems. Minimize external dependencies to reduce failure domains. |
| **Scale limits** | Determine cluster throughput through testing with representative workloads. Consider how quickly the cluster can scale up to handle burst demand or recover from failures in other clusters. |
| **Availability and disaster recovery** | Because compute nodes are ephemeral, recovery involves provisioning new nodes rather than repairing failed ones. Failed clusters can be rebuilt from configuration templates. |

## Regional supporting resources

A region contains resources that support the compute clusters but outlive individual compute nodes. Examples include parallel file systems, job schedulers, monitoring systems, and network infrastructure.

| Characteristic | Considerations |
|---|---|
| **Lifetime** | These resources share the lifetime of the region and outlive ephemeral compute nodes. They persist across cluster scaling events and individual job executions. |
| **State** | Regional resources may store state such as job queues, file system data, logs, and metrics. This state is specific to the region and doesn't need to be shared globally. |
| **Reach** | The resources don't need to be globally distributed. They serve the compute clusters within their region. Avoid cross-region communication except to global resources. |
| **Dependencies** | These resources can depend on global resources for authentication, configuration, or monitoring aggregation. They shouldn't depend on other regional resources. |
| **Scale limits** | Size regional resources to handle the aggregate demand from all clusters within the region. Plan for peak load scenarios including failover from other regions. |
| **Availability and disaster recovery** | Regional resources require backup and recovery strategies. Critical data should be replicated or backed up to enable recovery after regional failures. |

## Baseline architecture for HPC workloads

The following architecture illustrates the typical pattern for HPC workloads. The architecture includes user access and job submission, orchestration and scheduling, compute clusters, high-performance interconnects, storage systems, and management infrastructure.

:::image type="content" source="./images/hpc-architecture.png" alt-text="Diagram that shows the typical architecture of an HPC workload." lightbox="./images/hpc-architecture.png":::
