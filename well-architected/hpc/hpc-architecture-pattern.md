---
title: Architecture pattern for HPC workloads on Azure
description: Design areas address the critical decisions that determine whether your HPC workload can run jobs efficiently, handle large-scale parallel processing, and meet performance targets
author: padmalathas
ms.author: padmalathas
ms.date: 03/10/2026
ms.topic: concept-article
---

# Architecture Pattern for HPC Workloads on Azure

This article presents a foundational pattern for high-performance computing (HPC) architectures on Azure. Use this pattern as a starting point for your design process, then select components that align with your specific business and technical requirements.

## Design Considerations

Before selecting Azure services, familiarize yourself with the following characteristics to better inform your architectural decisions. Use the following questions to characterize your HPC workload. 

Based on these characteristics, classify and identify critical HPC resources. This exercise helps you track resource utilization and associated costs while focusing optimization efforts where they have the greatest impact. Organize resources into logical groups based on their function and lifetime.

| Characteristic | Key Questions | Design Implications |
| -------------- | ------------- | ------------------- |
| **Workload type** | Is the workload tightly coupled (requiring low-latency communication), loosely coupled (independent tasks with no inter-node communication), GPU-accelerated, memory-intensive, or data-intensive? | Determines whether to use MPI-based clusters, high-throughput schedulers, GPU-enabled VMs, or data-centric architectures, and identifies the target operating system (such as Linux or Windows Server) based on application compatibility. |
| **Scale requirements** | What is the expected throughput for compute, storage, and network? How many concurrent jobs need to run? What is the peak demand? | Influences cluster size, autoscaling strategy, and quota planning. |
| **Performance needs** | What are the latency requirements for inter-node communication? What storage throughput is required? What is the acceptable job completion time? | Drives network selection (for example, InfiniBand), storage choice, and VM family. |
| **Data lifecycle** | How much data is generated per job? How long must results be retained? What are the access patterns for input and output data? | Affects storage tiering, durability, and cost optimization. |
| **Cost constraints** | What is the budget for compute resources? Can spot instances or preemptible capacity be used? What are the tradeoffs between performance and cost? | Guides use of spot instances, scheduling policies, and job retry strategies. |
| **Compliance requirements** | Are there data residency or sovereignty requirements? What audit and logging capabilities are needed? What security controls must be enforced? | Determines region selection, encryption, logging, and access controls. |


## Basic Architecture

The following diagram illustrates a typical pattern for HPC workloads on Azure. The architecture encompasses user access and job submission, orchestration and scheduling, compute clusters, high-performance interconnects, storage systems, and management infrastructure.

:::image type="content" source="./media/hpc-architecture.png" alt-text="Screenshot of typical architecture of an HPC workload." lightbox="./media/hpc-architecture.png":::

### Architecture Flow

1. **Users submit jobs** through web portals, command-line interfaces, or APIs
1. **Authentication systems** verify identities and authorize access to resources
1. **Job schedulers** receive requests and determine optimal placement based on resource availability and policies
1. **Compute nodes** execute the workload, communicating through high-performance interconnects
1. **Storage systems** provide data to running jobs and persist results
1. **Monitoring infrastructure** tracks performance, cost, and system health throughout execution

----

### Architecture Components

The architecture consists of six primary layers:

#### User Access and Job Submission

| Component | Description |
|-----------|-------------|
| Web Portal | Browser-based interface for job submission, monitoring, and file management. |
| Command Line | Direct terminal access for scripting and automation through SSH or local shells. |
| API | Programmatic access via REST or GraphQL for integration with external systems and workflows. |

#### Orchestration and Scheduling

| Component | Description |
|-----------|-------------|
| Job Scheduler | Receives work requests and determines when and where each job should run. |
| Resource Manager | Tracks available compute capacity and allocates resources to jobs. |
| Queue Control | Organizes pending jobs based on priority, fairness policies, and resource requirements. |
| Workflow Engine | Coordinates multi-step pipelines and manages dependencies between jobs. |
| Capacity Planning | Forecasts resource demand and informs scaling decisions. |

#### High-Performance Interconnect

| Component | Description |
|-----------|-------------|
| Low-Latency Network | High-speed fabric connecting compute nodes with minimal communication delay. |
| High-Bandwidth Links | Network infrastructure supporting large data transfers between nodes. |
| Topology-Optimized Network | Network design that minimizes hops and latency for node-to-node communication. |

#### Storage Architecture

| Component | Description |
|-----------|-------------|
| Parallel File System | High-throughput shared storage optimized for concurrent access by many nodes. |
| Object Storage | Scalable storage for large datasets, intermediate results, and working data. |
| Cold Tier | Low-cost archival storage for long-term retention and compliance requirements. |

#### Management and Monitoring

| Component | Description |
|-----------|-------------|
| Provisioning | Automated deployment and configuration of compute nodes and infrastructure. |
| Telemetry | Collection of performance data, logs, and system metrics across the environment. |
| Monitoring | Real-time tracking of resource utilization, job status, and system health. |
| Alerting | Automated notifications for faults, threshold breaches, and operational issues. |
| Fault Detection | Identification of node failures, job errors, and infrastructure problems. |

#### Key Architectural Patterns

| Pattern | Description |
|---------|-------------|
| Batch Processing | Execution of large volumes of jobs with results delivered upon completion. |
| Interactive Visualization | Real-time rendering and analysis of simulation outputs and datasets. |
| Temporary Nodes | On-demand compute resources provisioned for peak workloads and released when idle. |
| Data Sharding | Distribution of large datasets across multiple storage nodes for parallel access. |

---

## Reference Architectures and Landing Zones

This baseline pattern provides a conceptual foundation. For detailed implementation guidance, reference architectures, and production-ready designs, explore the HPC resources in the Azure Architecture Center:

- **[HPC landing zone accelerator](/azure/cloud-adoption-framework/scenarios/azure-hpc/azure-hpc-landing-zone-accelerator)** — A deployment-ready architecture that implements Azure landing zone design principles for HPC workloads, including network topology, identity management, and governance controls.

- **[HPC cluster deployed in the cloud](/azure/architecture/solution-ideas/articles/hpc-cluster)** — Reference architecture for deploying HPC clusters using Azure CycleCloud with autoscaling compute nodes.

- **[Run reservoir simulation software on Azure](/azure/architecture/example-scenario/infrastructure/reservoir-simulation)** — Industry-specific example demonstrating HPC patterns for oil and gas workloads.

- **[Digital image-based modeling on Azure](/azure/architecture/example-scenario/infrastructure/image-modeling)** — Reference implementation for compute-intensive image processing workloads.

- **[HPC system and big-compute solutions](/azure/architecture/solution-ideas/articles/big-compute-with-azure-batch)** — Guidance for large-scale batch processing using Azure Batch.

These reference architectures provide Bicep templates, ARM templates, and detailed implementation steps that you can adapt for your specific requirements.
