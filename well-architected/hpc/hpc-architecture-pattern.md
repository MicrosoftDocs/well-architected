---
title: Architecture pattern for HPC workloads on Azure
description: The design areas represent the architecturally significant topics that must be discussed and designed for when defining a target architecture
author: padmalathas
ms.author: prwilk
ms.date: 12/05/2025
ms.topic: concept-article
---

# Architecture Pattern for HPC Workloads on Azure

This article presents a foundational pattern for high-performance computing (HPC) architectures on Azure. Use this pattern as a starting point for your design process, then select components that align with your specific business and technical requirements.

## Design Considerations

Before selecting Azure services, evaluate the following characteristics to inform your architectural decisions:

| Characteristic | Key Questions |
|----------------|---------------|
| **Workload type** | Is the workload tightly coupled (requiring low-latency communication), loosely coupled (embarrassingly parallel), GPU-accelerated, memory-intensive, or data-intensive? |
| **Scale requirements** | What is the expected throughput for compute, storage, and network? How many concurrent jobs need to run? What is the peak demand? |
| **Performance needs** | What are the latency requirements for inter-node communication? What storage throughput is required? What is the acceptable job completion time? |
| **Data lifecycle** | How much data is generated per job? How long must results be retained? What are the access patterns for input and output data? |
| **Cost constraints** | What is the budget for compute resources? Can spot instances or preemptible capacity be used? What are the tradeoffs between performance and cost? |
| **Compliance requirements** | Are there data residency or sovereignty requirements? What audit and logging capabilities are needed? What security controls must be enforced? |

Based on these characteristics, classify and identify critical HPC resources. This exercise helps you track resource utilization and associated costs while focusing optimization efforts where they have the greatest impact. Organize resources into logical groups based on their function and lifetime.

## Baseline Architecture

The following diagram illustrates a typical pattern for HPC workloads on Azure. The architecture encompasses user access and job submission, orchestration and scheduling, compute clusters, high-performance interconnects, storage systems, and management infrastructure.

:::image type="content" source="./images/hpc-architecture.png" alt-text="Diagram that shows the typical architecture of an HPC workload." lightbox="./images/hpc-architecture.png":::

### Architecture Flow

1. **Users submit jobs** through web portals, command-line interfaces, or APIs
1. **Authentication systems** verify identities and authorize access to resources
1. **Job schedulers** receive requests and determine optimal placement based on resource availability and policies
1. **Compute nodes** execute the workload, communicating through high-performance interconnects
1. **Storage systems** provide data to running jobs and persist results
1. **Monitoring infrastructure** tracks performance, cost, and system health throughout execution

### Architecture Components

The architecture consists of six primary layers:

**User access and job submission:** Users interact with the HPC environment through web portals, command-line interfaces, or APIs. Authentication systems verify user identities and control access to resources. Common implementations include Open OnDemand for web-based access and SSH for direct command-line interaction.

**Orchestration and scheduling:** Job schedulers receive work requests and determine when and where each job should run. Resource managers track available capacity. Queue managers organize pending jobs based on priority and fairness policies. Policy engines enforce organizational rules. Azure CycleCloud supports popular schedulers including Slurm, PBS Pro, and Grid Engine.

**Compute resources:** Different node types serve different purposes:

  - CPU-focused nodes (HB-series, HX-series) handle tightly coupled parallel workloads
  - GPU-accelerated nodes (ND-series, NC-series) speed up massively parallel computations
  - Memory-optimized nodes support large in-memory processing requirements
  - Specialized configurations address domain-specific needs

**High-performance interconnect:** Low-latency network fabrics connect compute nodes and storage. InfiniBand networking (HDR and NDR) provides the bandwidth and latency characteristics required for tightly coupled MPI workloads. Technologies like RDMA (Remote Direct Memory Access) reduce communication overhead by bypassing the operating system.

**Storage architecture:** A tiered approach balances performance with cost:

  - Parallel file systems (Azure Managed Lustre, Azure NetApp Files) provide high throughput for active job operations
  - Blob storage holds working datasets and intermediate results
  - Archive storage maintains long-term data for compliance and analysis

**Management and monitoring:** Performance monitoring tracks resource utilization and identifies bottlenecks. Cost tracking helps organizations understand spending patterns. Alerting systems notify administrators of issues. Azure Monitor and Log Analytics provide native integration for HPC environments.

## Reference Architectures and Landing Zones

This baseline pattern provides a conceptual foundation. For detailed implementation guidance, reference architectures, and production-ready designs, explore the HPC resources in the Azure Architecture Center:

- **[HPC landing zone accelerator](/azure/cloud-adoption-framework/scenarios/azure-hpc/azure-hpc-landing-zone-accelerator)** — A deployment-ready architecture that implements Azure landing zone design principles for HPC workloads, including network topology, identity management, and governance controls.

- **[HPC cluster deployed in the cloud](/azure/architecture/solution-ideas/articles/hpc-cluster)** — Reference architecture for deploying HPC clusters using Azure CycleCloud with autoscaling compute nodes.

- **[Run reservoir simulation software on Azure](/azure/architecture/example-scenario/infrastructure/reservoir-simulation)** — Industry-specific example demonstrating HPC patterns for oil and gas workloads.

- **[Digital image-based modeling on Azure](/azure/architecture/example-scenario/infrastructure/image-modeling)** — Reference implementation for compute-intensive image processing workloads.

- **[HPC system and big-compute solutions](/azure/architecture/solution-ideas/articles/big-compute-with-azure-batch)** — Guidance for large-scale batch processing using Azure Batch.

These reference architectures provide Bicep templates, ARM templates, and detailed implementation steps that you can adapt for your specific requirements.
