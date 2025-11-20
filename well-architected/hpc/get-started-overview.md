---
title: HPC workloads on Azure
description: Learn about architectural considerations, including common challenges and key design areas, for building and operating HPC workloads on Azure.
author: prwilk
ms.author: padmalathas
ms.date: 11/15/2025
ms.topic: concept-article
ms.update-cycle: 180-days  
---

# HPC workloads on Azure

This article addresses architectural challenges of designing high-performance computing (HPC) workloads. It focuses on computational intensity, parallel processing, data movement optimization, and large-scale operations. The recommendations are based on Azure Well-Architected Framework principles and include insights from successful Azure HPC implementations.

These articles are meant for workload owners and technical stakeholders like architects, HPC administrators, development leads, and IT leaders. Specialized HPC roles, such as computational scientists, researchers, and performance engineers, should also be aware of this guidance because collaboration across various roles and teams is a key aspect.

> [!NOTE]
> Azure offers various HPC services and infrastructure that you can integrate into your workload or build around. Depending on your business needs, you can choose between fully managed platform as a service (PaaS) solutions, infrastructure as a service (IaaS) solutions with HPC-optimized resources, or hybrid approaches.
>
> Specific Azure services and their capabilities are not covered here. We recommend that you refer to the respective product documentation for that information.

Also, certain workloads aren't in scope, such as:
- Standard enterprise applications that don't require specialized compute resources
- Web-scale applications focused primarily on horizontal scaling without tightly coupled parallel processing
- AI/ML workloads that are covered in the dedicated AI workload guidance

## What is an HPC workload?

In the context of Well-Architected Framework, an HPC workload processes computationally intensive tasks that require significant processing power, memory, storage, or network bandwidth beyond what standard computing resources can efficiently provide. It focuses on delivering high throughput, low latency, parallel processing capabilities, and optimizing for scientific, engineering, or data-intensive computing scenarios.

Apply the Well-Architected Framework pillars at every decision point to ensure that the system is reliable, secure, efficient, and cost-effective.

HPC workloads are different from traditional cloud workloads because they:
- **Require specialized hardware**: Often need high-core-count CPUs, GPUs, high-performance interconnects (InfiniBand, RoCE), and low-latency networking
- **Emphasize parallel processing**: Leverage message passing interface (MPI), multi-threading, or GPU acceleration to distribute work across many compute resources
- **Demand high-performance storage**: Need parallel file systems or high-IOPS storage to handle massive data volumes and concurrent access patterns
- **Have unique scaling patterns**: Scale both vertically (larger instances) and horizontally (more nodes) with tight coupling requirements
- **Require specialized scheduling**: Use workload managers (Slurm, PBS, LSF) to efficiently allocate resources and manage job queues

## Before you begin your design strategies

Consider these key points first.

### Familiarize yourself with the broad HPC workload categories

- **Tightly coupled parallel workloads**: Applications that require intensive inter-node communication, typically using MPI. Nodes must work synchronously, and the entire job runs at the speed of the slowest communication link.
  - Examples: Computational fluid dynamics (CFD), finite element analysis (FEA), molecular dynamics, weather modeling, crash simulations.
  - Requirements: Low-latency networks, proximity placement groups, specialized VM series.

- **Loosely coupled parallel workloads**: Applications that can run independently or with minimal inter-node communication. Each task or subset of tasks operates autonomously.
  - Examples: Monte Carlo simulations, parametric studies, rendering, genomics analysis, risk analysis, financial modeling.
  - Requirements: Standard networking is often sufficient, can use spot VMs for cost savings, easier to scale horizontally.

- **GPU-accelerated workloads**: Applications that leverage massively parallel GPU compute for acceleration.
  - Examples: Deep learning training, molecular dynamics, seismic processing, computer-aided engineering (CAE), visualization.
  - Requirements: GPU-enabled VMs, CUDA or ROCm software stacks, GPU-optimized storage.

- **Memory-intensive workloads**: Applications that require large amounts of memory per core for in-memory processing.
  - Examples: Genomic assembly, large-scale simulations, in-memory databases, real-time analytics
  - Requirements: Memory-optimized VMs, high memory-to-core ratios, potentially large-page support.

- **Data-intensive workloads**: Applications where data I/O and movement are the primary bottlenecks rather than compute.
  - Examples: Geospatial analysis, video processing, log analysis, bioinformatics pipelines
  - Requirements: High-throughput storage, parallel file systems, data locality optimization.

### Evaluate your cloud versus on-premises options

If your workload has predictable, continuous compute requirements and you already have infrastructure, maintaining on-premises HPC might be cost-effective. But if you need burst capacity, rapid provisioning, access to the latest hardware, or have variable workloads, Azure HPC solutions offer significant advantages.

When you choose between cloud HPC and on-premises infrastructure, consider these factors:

| Aspect    | Description | Benefits  |
| :---------| :---------- | :-------- |
| **Capital vs. operational expenditure**| Shift from upfront hardware costs to pay-as-you-go. | Improves cash flow, and reduces financial risk. |
| **Scalability**| Scale up or scale down resources as needed. | Handles peak demand and optimizes cost and performance.|
| **Hardware currency**| Access the latest CPU, GPU, and interconnects. | Improves performance and efficiency with hardware. |
| **Geographic distribution**| Deploy workloads across multiple regions. | Ensures compliance and disaster recovery. |
| **Time to solution**| Provision clusters in minutes, not months. | Accelerates R&D and speeds innovation. |
| **Total cost of ownership**| Improves maintenance and administrative overhead. | Lowers overall costs and simplifies operations. |

> [!IMPORTANT]
> Migrating to cloud, requires careful planning around data transfer, network architecture, licensing models, and application optimization. Many organizations adopt a hybrid approach, maintaining on-premises resources for steady-state workloads while using cloud for bursting and specialized requirements.

### Determine your deployment and management model

**Self-managed infrastructure**: You control the entire stack from VMs to applications, providing maximum flexibility but requiring significant operational expertise. 
- Best for: Organizations with existing HPC expertise, complex custom configurations, specific compliance requirements

**Managed services**: Use Azure services or marketplace solutions to reduce operational burden.
- Best for: Organizations wanting to focus on workloads rather than infrastructure, teams without deep HPC operations experience

**Hybrid approaches**: Combine on-premises infrastructure with cloud resources for burst capacity or specialized workloads.
- Best for: Organizations with existing on-premises investments wanting flexible capacity, regulatory requirements for some data on-premises

## What are the common challenges?
| Challenges | Summary |
| :--------- | :------ |
| Network Performance | Tightly coupled jobs need low latency and high bandwidth for efficiency. |
| Licensing Complexity | Software licenses add cost and require careful management.
| Data Transfer Overhead | Moving large datasets is slow and resource-heavy, creating bottlenecks.
| Storage Throughput | Parallel I/O needs high-performance file systems, standard storage falls short. |
| Resource Allocation | Efficient scheduling and job placement are complex and ongoing. |
| Application Scalability | Apps don’t always scale linearly, tuning and redesign may be needed. |
| Cost Predictability | Specialized hardware can drive costs beyond budget without controls. |
| Workforce Expertise | HPC demands rare skills in programming, networking, and optimization. |
| Job Reliability | Long jobs risk failures; checkpointing and fault tolerance add complexity. |
| Security vs. Performance | Strong security can slow workloads, balance compliance with speed. |

## How to use this guidance

✔ **Start with Design methodology**, which outlines the rationale and recurring themes across technical and operational areas. This systematic approach helps to define requirements and design strategies. Revisit this methodology when you face uncertain choices to stay aligned with the workload's overall goals.

✔ **Proceed to Design principles** to see how the design methodology aligns with the core Well-Architected Framework pillars. Consider your growth trajectory and scaling needs. Evaluate the underlying principles for all pillars collectively, including the tradeoffs between performance, cost, security, and operational complexity.

✔ **Focus on the design areas** that have the biggest effect on your solution. Each area includes considerations and recommendations to guide you through the design decisions specific to HPC workloads.

✔ **Use the Assessment Review Tool** to evaluate the readiness of your optimized HPC workload in production and identify areas for improvement.

## Typical architecture pattern and design areas

The following description illustrates how an HPC workload operates on Azure, from job submission through execution to results storage and analysis.

:::image type="content" source="./images/hpc-architecture.png" alt-text="Diagram that shows the typical architecture of an HPC workload." lightbox="./images/hpc-architecture.png"::

The architecture highlights the integration of different components to enable efficient job scheduling, parallel execution, high-performance storage access, and monitoring in HPC solutions. It includes modules such as user access and job submission, orchestration and scheduling, compute clusters, high-performance interconnects, parallel file systems, data management, and monitoring systems.

**Key flow:**
1. Users submit jobs through web portals, command-line interfaces, or APIs to a central job scheduler
2. The scheduler allocates resources based on job requirements, queue policies, and cluster availability
3. Compute nodes execute workloads with access to shared parallel file systems for input/output operations
4. High-speed interconnects enable efficient MPI communication for tightly coupled parallel jobs
5. Monitoring systems track job progress, resource utilization, and system health
6. Results are written to storage tiers (hot, warm, cold) based on access patterns and retention policies
7. Completed jobs release resources back to the scheduler for the next workload

The following table describes some key design areas related to HPC workloads.

| Design areas |
|---|
| **Compute infrastructure**:  Select processing resources based on workload characteristics. Evaluate CPU, GPU, memory, and specialized accelerator requirements. Balance performance needs with cost constraints and availability. |
| **Network architecture**: Design high-performance interconnects for parallel communication. Implement low-latency topologies, optimize bandwidth utilization, and minimize communication overhead between compute nodes. |
| **Storage architecture**: Architect parallel file systems and tiered storage for concurrent access patterns. Balance throughput, capacity, and cost across hot, warm, and cold data tiers. |
| **Workload orchestration**: Implement scheduling systems to manage job queues, resource allocation, and priority policies. Design for efficient resource utilization and fair access across users and projects. |
| **Security and governance**: Establish authentication, authorization, and data protection mechanisms. Implement network isolation, encryption, audit controls, and compliance frameworks without compromising performance. |
| **Operations and monitoring**: Deploy automated provisioning, configuration management, and continuous monitoring. Track resource utilization, job metrics, system health, and cost attribution for operational efficiency. |
| **Cost optimization**: Implement cost management with budget alerts, resource tagging, right-sizing recommendations, spot instance strategies, and automated scale-down policies. |
| **Performance optimization**: Profile applications to identify bottlenecks and optimize for target infrastructure. Implement tuning strategies, establish performance baselines, and continuously evaluate efficiency improvements. |
| **Disaster recovery and business continuity**: Design backup strategies for critical data, implement checkpointing for long-running jobs, and establish recovery procedures. Consider multi-region deployments for mission-critical workloads and document failover processes. |
| **Hybrid and multi-cloud strategies**: Integrate Azure HPC resources with on-premises infrastructure using Azure Arc, ExpressRoute, or VPN connections. Design workload distribution policies, unified identity management, and consistent monitoring across environments. |
| **Application optimization**: Profile and optimize applications for Azure infrastructure characteristics. Consider compiler optimizations, library selections (Intel MPI, OpenMPI), containerization strategies, and Azure-specific tuning. Establish performance baselines and benchmarking practices. |

> [!TIP]
> Every architectural decision involves a range of considerations and a set of acknowledged compromises that balance different aspects of the framework. These tradeoffs are indicated throughout the guidance with specific examples like cost versus performance, security versus usability, or simplicity versus flexibility.

## Next steps

- Review the Design methodology for HPC workloads to understand the systematic approach to HPC architecture
- Explore Design principles for HPC workloads to see how Well-Architected Framework pillars apply to HPC scenarios
- Deep dive into specific design areas most relevant to your workload requirements
- Complete the HPC workload assessment to evaluate your architecture and identify improvement opportunities

## Related resources

- [Azure HPC documentation](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing)
- [Azure CycleCloud](https://docs.microsoft.com/azure/cyclecloud/)
- [Azure Batch](https://docs.microsoft.com/azure/batch/)
- [Azure HPC VM sizes](https://docs.microsoft.com/azure/virtual-machines/sizes-hpc)
- [Azure high-performance storage options](https://docs.microsoft.com/azure/architecture/guide/storage/storage-start-here)
