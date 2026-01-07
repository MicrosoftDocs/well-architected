---
title: HPC workloads on Azure
description: Learn about architectural considerations, including common challenges and key design areas, for building and operating HPC workloads on Azure.
author: prwilk
ms.author: padmalathas
ms.date: 01/06/2026
ms.topic: concept-article
ms.update-cycle: 180-days  
---

# HPC workloads on Azure

This article addresses architectural challenges of designing high-performance computing (HPC) workloads. It focuses on design strategies for all the characteristics of HPC workloads. The recommendations are based on Azure Well-Architected Framework principles and include insights from successful Azure HPC implementations on Azure.

These articles are meant for workload owners and technical stakeholders like architects, HPC administrators, development leads, and IT leaders. Specialized HPC roles, such as computational scientists, researchers, and performance engineers, should also be aware of this guidance because collaboration across various roles and teams is a key aspect.

> [!NOTE]
> Specific Azure services and their capabilities are not covered here. We recommend that you refer to the respective product documentation for that information.

For example, engineers testing how a new aircraft design handles wind resistance need to run thousands of calculations across the entire structure. Researchers studying how proteins fold to develop new medicines process enormous datasets that require specialized computing power. Financial analysts running market simulations explore millions of scenarios to understand risk. Weather forecasters process global atmospheric data to predict storms days in advance.

These scenarios share common needs: they require significant processing capacity, handle large volumes of data, often need specialized hardware like graphics processors, and benefit from distributing work across many machines. If your workload takes too long on regular computers, requires specialized processing capabilities, or involves running the same analysis across many variations, this guidance can help you design an effective solution.

This guidance is designed for workloads that need more computational power than standard applications can provide. Consider using HPC when your work involves processing massive amounts of data, running complex calculations that take hours or days, or solving problems that require many computers working together simultaneously.

## What is an HPC workload?

In the preceding example shown, the workload must be capable of handling computationally intensive tasks. It requires significant processing power, memory, storage, or network bandwidth beyond what standard computing resources can efficiently provide. It focuses on delivering high throughput, low latency, parallel processing capabilities, and optimizing for scientific, engineering, or data-intensive computing scenarios. HPC workloads differ from traditional cloud workloads in ways that create both opportunities and challenges.

**Opportunities:**
- **Massive computational scale**: Process complex problems in hours or days that would take months or years on standard systems, accelerating research and innovation timelines
- **Parallel efficiency**: Distribute work across hundreds or thousands of compute resources simultaneously, enabling breakthrough discoveries in science and engineering
- **Specialized performance**: Leverage purpose-built hardware like GPUs and high-speed interconnects to achieve performance levels impossible with general-purpose infrastructure
- **Flexible resource allocation**: Scale resources dynamically to match workload demands, paying only for capacity when needed rather than maintaining idle infrastructure

**Challenges:**
- **Higher infrastructure costs**: Specialized hardware like high-core-count CPUs, GPUs, and low-latency networking (InfiniBand, RoCE) significantly increase resource expenses compared to standard compute
- **Operational complexity**: Require sophisticated workload managers (Slurm, PBS, LSF), parallel file systems, and specialized scheduling policies that demand expert knowledge to configure and maintain
- **Application constraints**: Many applications require substantial refactoring or rewriting to take advantage of parallel processing capabilities, creating development overhead
- **Storage demands**: Massive data volumes and concurrent access patterns necessitate expensive parallel file systems and high-IOPS storage that standard solutions cannot provide
- **Steep learning curve**: Teams need specialized skills in parallel programming (MPI, multi-threading), performance tuning, and HPC-specific technologies that are scarce and expensive to acquire

### Familiarize yourself with the broad HPC workload categories

To get the most value from this guidance, start by familiarizing yourself with the common HPC workload categories and identifying the one that best aligns with the solution you’re building.

| Workload Type | Description | Use Case | Recommended |
|-------------- | ----------- | -------- | ----------- |
| Tightly coupled (constant communication) | All computers must constantly talk to each other while solving a problem. | Simulating how air flows around an aircraft wing, predicting tomorrow's weather, testing how a car crumples in a crash, or modeling how molecules interact in drug development. | Extremely fast network connections between computers, keeping computers physically close together, and specialized high-performance hardware. |
| Loosely coupled (work independently) | Work divides into separate pieces that don't need to coordinate. | Running thousands of "what-if" scenarios for investment portfolios, rendering frames for animated movies, analyzing DNA sequences, or testing different design variations for a product. | Standard network connections are usually fine, can use lower-cost computing that might occasionally get interrupted, and can easily add more computers when needed. |
| GPU-accelerated | Uses graphics processors because they're exceptionally good at doing millions of simple calculations at once. | Training AI systems, processing seismic data to find oil deposits, running chemistry simulations, analyzing medical images, or creating engineering visualizations. | Computers with powerful graphics processors, specialized software that knows how to use these processors, and storage systems fast enough to keep up with the processing speed. |
| Memory-intensive | Must hold massive amounts of information in active memory simultaneously. | Assembling complete Genome sequences from fragments, running city-scale simulations, analyzing entire datasets in memory for instant queries, or processing real-time data from thousands of sensors. | Computers with exceptionally large memory capacity (often 1-4 terabytes), configurations that provide lots of memory per processor, and sometimes special settings to manage very large memory allocations. |
| I/O-intensive (data bottleneck) | Spends more time reading and writing data than actually computing results. | Processing satellite imagery covering entire continents, converting massive video libraries between formats, analyzing years of system logs to find patterns, or processing genetic data from large population studies. | Storage systems that can read and write data extremely fast, file systems designed to handle many computers accessing data simultaneously, and strategies to keep data close to where it's being processed. |

> [!IMPORTANT]
> On-premises HPC can be cost-effective for predictable, continuous workloads with existing infrastructure. Cloud HPC offers advantages for burst capacity, variable workloads, rapid provisioning, and access to current hardware. Key factors include pay-as-you-go pricing, on-demand scalability, geographic distribution, and faster time-to-solution. Many organizations adopt a hybrid approach—using on-premises for steady-state workloads and cloud for bursting. (**Placeholder for this converted NOTE is TBD!!!**)

## How to use this guidance

✔ **Start with Design methodology**, which outlines the rationale and recurring themes across technical and operational areas. This systematic approach helps to define requirements and design strategies. Revisit this methodology when you face uncertain choices to stay aligned with the workload's overall goals.

✔ **Proceed to Design principles** to see how the design methodology aligns with the core Well-Architected Framework pillars. Consider your growth trajectory and scaling needs. Evaluate the underlying principles for all pillars collectively, including the tradeoffs between performance, cost, security, and operational complexity.

✔ **Focus on the design areas** that have the biggest effect on your solution. Each area includes considerations and recommendations to guide you through the design decisions specific to HPC workloads.

✔ **Use the Assessment Review Tool** to evaluate the readiness of your optimized HPC workload in production and identify areas for improvement.

> [!TIP]
> Every architectural decision involves a range of considerations and a set of acknowledged compromises that balance different aspects of the framework. These tradeoffs are indicated throughout the guidance with specific examples like cost versus performance, security versus usability, or simplicity versus flexibility.


## Next steps

- Review the Design methodology for HPC workloads to understand the systematic approach to HPC architecture
- Explore Design principles for HPC workloads to see how Well-Architected Framework pillars apply to HPC scenarios
- Deep dive into specific design areas most relevant to your workload requirements
- Complete the HPC workload assessment to evaluate your architecture and identify improvement opportunities

## Related resources

- [Azure HPC documentation](/azure/architecture/topics/high-performance-computing)
- [Azure CycleCloud](/azure/cyclecloud/)
- [Azure Batch](/azure/batch/)
- [Azure HPC VM sizes](/azure/virtual-machines/sizes-hpc)
- [Azure high-performance storage options](/azure/architecture/guide/storage/storage-start-here)
