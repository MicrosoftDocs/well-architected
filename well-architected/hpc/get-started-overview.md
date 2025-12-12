---
title: HPC workloads on Azure
description: Learn about architectural considerations, including common challenges and key design areas, for building and operating HPC workloads on Azure.
author: prwilk
ms.author: padmalathas
ms.date: 12/12/2025
ms.topic: concept-article
ms.update-cycle: 180-days  
---

# HPC workloads on Azure

This article addresses architectural challenges of designing high-performance computing (HPC) workloads. It focuses on design strategies for all the characteristics of HPC workloads. The recommendations are based on Azure Well-Architected Framework principles and include insights from successful Azure HPC implementations on Azure.

These articles are meant for workload owners and technical stakeholders like architects, HPC administrators, development leads, and IT leaders. Specialized HPC roles, such as computational scientists, researchers, and performance engineers, should also be aware of this guidance because collaboration across various roles and teams is a key aspect.

> [!NOTE]
> Azure offers various services and infrastructure that are core to any HPC workload or build around. Depending on your business needs, you can choose between fully managed platform as a service (PaaS) solutions, infrastructure as a service (IaaS) solutions with HPC-optimized resources, or hybrid approaches.
>
> Specific Azure services and their capabilities are not covered here. We recommend that you refer to the respective product documentation for that information.

For example, engineers testing how a new aircraft design handles wind resistance need to run thousands of calculations across the entire structure. Researchers studying how proteins fold to develop new medicines process enormous datasets that require specialized computing power. Financial analysts running market simulations explore millions of scenarios to understand risk. Weather forecasters process global atmospheric data to predict storms days in advance.

These scenarios share common needs: they require significant processing capacity, handle large volumes of data, often need specialized hardware like graphics processors, and benefit from distributing work across many machines. If your workload takes too long on regular computers, requires specialized processing capabilities, or involves running the same analysis across many variations, this guidance can help you design an effective solution.

This guidance is designed for workloads that need more computational power than standard applications can provide. Consider using HPC when your work involves processing massive amounts of data, running complex calculations that take hours or days, or solving problems that require many computers working together simultaneously.

## What is an HPC workload?

In the preceeding example shown , workload must be capable of handling computationally intensiveworklods. It requires significant processing power, memory, storage, or network bandwidth beyond what standard computing resources can efficiently provide. It focuses on delivering high throughput, low latency, parallel processing capabilities, and optimizing for scientific, engineering, or data-intensive computing scenarios.

In the preceding example shown, the workload must be capable of handling computationally intensive tasks. It requires significant processing power, memory, storage, or network bandwidth beyond what standard computing resources can efficiently provide. HPC workloads differ from traditional cloud workloads in ways that create both opportunities and challenges.

**Opportunities:**
- **Massive computational scale**: Process complex problems in hours or days that would take months or years on standard systems, accelerating research and innovation timelines
- **Parallel efficiency**: Distribute work across hundreds or thousands of compute resources simultaneously, enabling breakthrough discoveries in science and engineering
- **Specialized performance**: Leverage purpose-built hardware like GPUs and high-speed interconnects to achieve performance levels impossible with general-purpose infrastructure
- **Flexible resource allocation**: Scale resources dynamically to match workload demands, paying only for capacity when needed rather than maintaining idle infrastructure


## Before you begin your design strategies

> [!NOTE]
> This guidance focuses specifically on HPC workloads requiring specialized compute infrastructure and parallel processing.
> - AI/ML workloads: See the [AI workload guidance](/azure/well-architected/ai/get-started) for model training, inference, and MLOps patterns
> - Big Data analytics: Data lake and analytics platform patterns are covered separately in Data analytics guidance
> - Standard web applications: Traditional cloud-native patterns are covered in the [Well-Architected Framework guidance](/azure/well-architected/pillars)

### Familiarize yourself with the broad HPC workload categories

### Workloads that need constant communication between computers

These applications require all computers to constantly talk to each other while solving a problem. Think of it like an orchestra where every musician must stay perfectly synchronized—if one section slows down, the entire performance suffers.

**Common uses:** Simulating how air flows around an aircraft wing, predicting tomorrow's weather, testing how a car crumples in a crash, or modeling how molecules interact in drug development.

**What they need:** Extremely fast network connections between computers, keeping computers physically close together, and specialized high-performance hardware.

---

### Workloads that can work independently

These applications can divide work into separate pieces that don't need to coordinate with each other. Imagine a team of artists each painting different portraits—they work side by side but don't need to wait for each other or share brushes.

Use cases: Running thousands of "what-if" scenarios for investment portfolios, rendering frames for animated movies, analyzing DNA sequences, or testing different design variations for a product.

**What they need:** Standard network connections are usually fine, can use lower-cost computing that might occasionally get interrupted, and can easily add more computers when needed.

---
### Workloads that use graphics processors for speed

These applications use graphics processors (like those in gaming computers) because they're exceptionally good at doing millions of simple calculations at once. It's like having thousands of simple calculators working together instead of one very smart calculator.

Use cases:  Training artificial intelligence systems, processing seismic data to find oil deposits, running chemistry simulations, analyzing medical images, or creating engineering visualizations.

**What they need:** Computers with powerful graphics processors, specialized software that knows how to use these processors, and storage systems fast enough to keep up with the processing speed.

---

### Workloads that need enormous amounts of memory

These applications must hold massive amounts of information in active memory simultaneously, like trying to work on a giant jigsaw puzzle where all pieces need to be spread out on the table at once rather than stored in boxes.

Use cases:  Assembling complete genome sequences from fragments, running city-scale simulations, analyzing entire datasets in memory for instant queries, or processing real-time data from thousands of sensors.

**What they need:** Computers with exceptionally large memory capacity (often 1-4 terabytes), configurations that provide lots of memory per processor, and sometimes special settings to manage very large memory allocations.

---

### Workloads where moving data is the bottleneck

These applications spend more time reading and writing data than actually computing results. Think of it like a busy kitchen where chefs wait for ingredients to arrive rather than spending time cooking.

Use cases:  Processing satellite imagery covering entire continents, converting massive video libraries between formats, analyzing years of system logs to find patterns, or processing genetic data from large population studies.

**What they need:** Storage systems that can read and write data extremely fast, file systems designed to handle many computers accessing data simultaneously, and strategies to keep data close to where it's being processed.

### Evaluate your cloud versus on-premises options

If your workload has predictable, continuous compute requirements and you already have infrastructure, maintaining on-premises HPC might be cost-effective. But if you need burst capacity, rapid provisioning, access to the latest hardware, or have variable workloads, Azure HPC solutions offer significant advantages.

When you choose between cloud HPC and on-premises infrastructure, consider these factors:

| Aspect    | Description | Benefits  |
| :---------| :---------- | :-------- |
| **Infrastructure vs. operational expenditure**| Shift from upfront hardware costs to pay-as-you-go. | Improves cash flow, and reduces financial risk. |
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
| Network Performance | Frequent exchange of data during a job, creats network latency with the slow network connections.|
| Licensing Complexity | Software licenses add cost and require careful management.|
| Data Transfer Overhead | Moving large datasets is slow and resource-heavy, creating bottlenecks.|
| Storage Throughput | Parallel I/O needs high-performance file systems, standard storage falls short. |
| Resource Allocation | Efficient scheduling and job placement are complex and ongoing. |
| Application Scalability | Apps don’t always scale linearly, tuning and redesign may be needed. |
| Cost Predictability | Specialized hardware can drive costs beyond budget without controls. |
| Workforce Expertise | HPC demands rare skills in programming, networking, and optimization. |
| Job Reliability | Long jobs risk failures; checkpointing and fault tolerance add complexity. |
| Security vs. Performance | Strong security can slow workloads, balance compliance with speed. |


### Challenges 

- **Network Performance** : Frequent exchange of data during a job, creats network latency with the slow network connections.
| Licensing Complexity | Software licenses add cost and require careful management.|
| Data Transfer Overhead | Moving large datasets is slow and resource-heavy, creating bottlenecks.|
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
