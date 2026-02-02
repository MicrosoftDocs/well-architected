---
title: Design Principles for HPC Workloads on Azure
description: Review the design principles of the Azure Well-Architected Framework. Learn how to apply these principles to an HPC workload.
author: padmalathas
ms.author: prwilk
ms.date: 02/06/2026
ms.topic: concept-article
ms.update-cycle: 180-days  
---

# Design Principles for HPC Workloads

The HPC design methodology is underpinned by five key design principles derived from the Well-Architected Framework pillars. These principles serve as a compass for subsequent design decisions across critical design areas. We highly recommend that you familiarize yourself with these principles to better understand their impact and the trade-offs associated with non-adherence.

> [!NOTE]
> There are obvious cost trade-offs associated with maximizing performance and reliability. Design decisions should be driven by business requirements and acceptable trade-offs between performance, reliability, and cost.

## Reliability

Building reliable HPC environments on Azure requires planning for failures at multiple levels—from individual compute nodes to entire regions. The ephemeral nature of cloud resources and the long-running characteristics of HPC jobs demand deliberate strategies for state preservation and recovery.

### Considerations

- **Job state preservation.** Long-running HPC jobs are vulnerable to infrastructure failures, spot evictions, and maintenance events. Without systematic checkpointing, any interruption forces complete job restarts, wasting compute hours and delaying results.

- **Node failure tolerance.** Individual VM failures are expected events in large-scale deployments. The scheduling and orchestration layer must detect failures promptly and either reassign work to healthy nodes or preserve state for recovery.

- **Data protection strategy.** HPC workflows generate data with varying durability requirements—from disposable scratch files to irreplaceable simulation results. Protection mechanisms should match data value without imposing unnecessary overhead on transient working data.

- **Cross-region continuity.** Critical workloads may require the ability to resume computation in an alternate Azure region following regional outages. This capability requires advance planning for data replication and environment reproducibility.

### Recommendations


**OLD**

| Azure Service/Feature | Recommendation | 
|-----------------------|----------------|
| Azure Blob Storage, Azure Managed Lustre | Implement application-level checkpointing to Azure Blob Storage or Azure Managed Lustre, with checkpoint frequency aligned to RPO targets and job criticality. | 
| Azure CycleCloud, Azure Batch | Use Azure CycleCloud or Azure Batch automatic retry policies to handle transient node failures and reschedule failed tasks without manual intervention. | 
| Azure Batch, Azure CycleCloud | Configure Azure Batch task retry policies and CycleCloud node health monitoring to automatically detect and respond to compute node failures during job execution. | 
| Azure Blob Storage (GRS/LRS), Azure NetApp Files | Replicate critical datasets and final results to geo-redundant storage (GRS) or a paired Azure region, while using locally redundant storage for high-performance scratch data. | 
| Azure Resource Manager, Bicep, Terraform | Maintain infrastructure-as-code definitions in Azure DevOps or GitHub to enable rapid cluster recreation in alternate regions when disaster recovery is required. | 

**NEW**

| Consideration | Azure Interpretation | Why (General HPC Rationale) |
|---------------|---------------------|----------------------------|
| **Backup and disaster recovery** | Azure Backup & Site Recovery for RPO/RTO objectives | Long-running HPC jobs and valuable research data need protection. Regular backups guard against data loss, while disaster recovery plans with checkpoint files enable quick resumption in another region after major failures. |
| **Zero-downtime updates** | Blue/green deployments via App Service slots and AKS strategies | Job submission portals and result databases need updates without disrupting researchers. Running parallel environments (blue/green) allows seamless switchovers so users never lose access during upgrades. |




## Performance Efficiency

HPC workloads demand maximum computational throughput, making hardware selection and system architecture critical to achieving performance objectives. Azure provides purpose-built HPC infrastructure, but realizing its potential requires deliberate alignment between workload characteristics and resource configuration.

### Considerations

- **Workload-hardware alignment.** HPC applications exhibit diverse resource profiles—CPU-bound simulations, memory-intensive analyses, GPU-accelerated computations, and I/O-dominated workflows each require different VM families to achieve optimal performance.

- **Interconnect performance.** Tightly coupled parallel applications that exchange data frequently between nodes are highly sensitive to network latency and bandwidth. Standard ethernet networking introduces bottlenecks that can negate the benefits of additional compute nodes.

- **Parallel storage throughput.** When hundreds of compute nodes access shared datasets simultaneously, storage systems become the primary performance constraint. Traditional file servers cannot sustain the aggregate throughput that large-scale HPC jobs demand.

- **Cluster topology optimization.** Physical proximity between communicating VMs directly impacts MPI performance. Distributed placement across datacenter infrastructure introduces latency that degrades tightly coupled workload performance.

- **Environment consistency.** Performance variability across nodes—caused by software version differences, library mismatches, or configuration drift—produces unpredictable job behavior and complicates performance optimization efforts.

### Recommendations

**OLD**

| Azure Service/Feature | Recommendation | 
| ----------------------| ---------------|
| Azure HBv4/HBv5 VMs, Azure NDv5 VMs | Select HBv4 or HBv5 series VMs for CPU-intensive workloads requiring high memory bandwidth; choose NDv5 series for GPU-accelerated computations; use memory-optimized series for data-intensive analytics. | 
| Azure InfiniBand networking | Deploy InfiniBand-enabled VM series (HB, HC, ND) for MPI workloads requiring low-latency, high-bandwidth node-to-node communication. | 
| Azure Managed Lustre, Azure NetApp Files | Implement Azure Managed Lustre for scratch storage requiring high aggregate throughput, or Azure NetApp Files for workloads needing shared POSIX-compliant file access with consistent performance. | 
| Azure Proximity Placement Groups | Configure Proximity Placement Groups to colocate compute nodes within the same datacenter network segment, minimizing latency for tightly coupled parallel applications. | 
| Azure Compute Gallery, Azure Image Builder | Build and maintain standardized VM images with pre-installed HPC libraries, MPI implementations, and application software using Azure Compute Gallery for consistent deployment across all nodes. | 


**NEW**

| Consideration | Azure Interpretation | Why (General HPC Rationale) |
|---------------|---------------------|----------------------------|
| **Tightly coupled workloads** | InfiniBand-enabled VMs (HBv3, HC-series) with MPI libraries and CycleCloud orchestration | MPI applications exchange data constantly between nodes. Without ultra-low latency networks like InfiniBand, jobs spend most time waiting on messages instead of computing. Fast interconnects plus proper orchestration enable efficient large-scale simulations. |
| **Physical proximity** | Proximity Placement Groups (PPGs) place VMs close together | Physical distance adds network hops and latency. Grouping communicating nodes on the same rack mimics on-premises cluster locality, dramatically improving performance for communication-intensive workloads. |
| **Compute resource selection** | VM series categorized by capability (H-series for HPC, memory-optimized, GPU-enabled) | Different applications have different bottlenecks. Memory-heavy simulations need high-memory nodes, while compute-bound workloads benefit from high-core-count CPUs. Understanding vCPU = logical core helps optimize resource usage and performance tuning. |
| **Virtualization overhead** | Customized Hyper-V hypervisor optimized for HPC performance | HPC applications traditionally run on bare-metal for maximum speed. Modern hypervisors use CPU pinning, huge pages, and SR-IOV to deliver near-physical performance while maintaining cloud flexibility and security. |
| **High-performance storage** | Premium SSD, Ultra Disk, and Azure NetApp Files for high IOPS | I/O-intensive workloads need fast local storage for temporary data and high-throughput shared storage for results. Proper storage selection prevents CPUs from sitting idle waiting on slow disks. |
| **Parallel file systems** | Azure NetApp Files and Lustre via HPC Cache | Hundreds of nodes simultaneously reading/writing shared files require distributed storage. Parallel filesystems prevent I/O bottlenecks when many nodes need concurrent data access. |
| **Intelligent data caching** | Azure HPC Cache with lazy-loading for Blob Storage datasets | Datasets can be petabytes large but jobs often need only subsets. Caching pulls data on-demand and keeps it local, delivering fast access without copying entire archives upfront. |
| **Pre-configured environments** | VM images via Azure Marketplace and Shared Image Gallery | Setting up software environments (compilers, libraries, MPI) on each node is time-consuming and error-prone. Pre-built images ensure all nodes have identical configurations for reproducible results and immediate productivity. |
| **Workload-optimized sizing** | Azure HPC Calculator helps determine optimal memory-to-core ratios | Matching hardware to workload requirements avoids over-provisioning expensive resources or under-provisioning and hitting memory limits. Balanced configurations run efficiently without wasting budget. |
| **Domain-specific applications** | Support for CAE tools (ANSYS, OpenFOAM) on optimized VMs | Engineering simulations parallelize naturally across processors and run for hours or days. HPC infrastructure provides the capacity and environment needed for faster design iteration and higher-fidelity models. |

## Operational Excellence

Operational maturity in HPC environments requires balancing user autonomy with system reliability. Researchers and engineers expect familiar tools and workflows, while operations teams need visibility, automation, and reproducibility to manage complex infrastructure at scale.

### Considerations

- **Infrastructure reproducibility.** Manual cluster provisioning introduces configuration inconsistencies and delays. HPC environments should be deployable through automated, version-controlled processes that produce identical results across deployments.

- **Scheduler integration.** HPC users have established workflows built around specific job schedulers. Requiring users to adopt unfamiliar submission methods creates friction and delays adoption of cloud-based HPC resources.

- **Workflow automation.** End-to-end HPC pipelines involve multiple stages—data staging, preprocessing, computation, post-processing, and results delivery. Manual orchestration of these stages introduces errors and limits throughput.

- **Observability and diagnostics.** Large-scale HPC jobs can fail in subtle ways that are difficult to diagnose without comprehensive telemetry. Operators need visibility into job progress, resource utilization, and system health to troubleshoot issues effectively.

- **Change management discipline.** Updates to HPC software stacks, scheduler configurations, or infrastructure definitions can introduce regressions that cause job failures. Changes should be tested and deployed through controlled processes.

### Recommendations

**OLD**

| Azure Service/Feature | Recommendation | 
| ----------------------| ---------------|
| Azure Resource Manager, Bicep, Terraform | Define HPC cluster infrastructure using Bicep or Terraform templates, enabling consistent deployments and version-controlled infrastructure changes. | 
| Azure CycleCloud | Deploy Azure CycleCloud with native support for Slurm, PBS Pro, Grid Engine, or other schedulers that align with existing user workflows and submission scripts. | 
| Azure Batch, Azure CycleCloud | Implement Azure Batch for managed job scheduling with built-in task dependencies, or integrate CycleCloud clusters with workflow orchestration tools for complex multi-stage pipelines. | 
| Azure Monitor, Log Analytics | Configure Azure Monitor to collect cluster metrics, job telemetry, and diagnostic logs; use Log Analytics workspaces and workbooks to create operational dashboards for HPC environments. | 
| Azure DevOps, GitHub Actions | Use Azure DevOps or GitHub Actions pipelines to automate testing and deployment of infrastructure changes, application updates, and scheduler configuration modifications. | 

**NEW**

| Consideration | Azure Interpretation | Why (General HPC Rationale) |
|---------------|---------------------|----------------------------|
| **Job terminology** | Azure uses "workloads" and "jobs" interchangeably in documentation and tools | Jobs are individual tasks submitted to the cluster, while workloads refer broadly to computational load. Dual terminology aligns traditional HPC usage with modern cloud language so users find their tasks handled familiarly. |
| **High-throughput computing** | Azure Batch for running large volumes of independent tasks | Running thousands of small independent jobs (Monte Carlo simulations, rendering) needs automated queuing and dispatch. Schedulers maximize throughput by executing tasks as resources become available at massive scale. |
| **Job orchestration** | Azure Batch, CycleCloud, and Slurm integration for scheduling | Automated schedulers queue work, allocate resources based on policies, and maximize cluster utilization. Users choose familiar tools (Slurm) or cloud-native approaches (Kubernetes) that fit their workflows. |
| **Environment automation** | CycleCloud with custom recipes and configuration management | Clusters require specific software stacks on every node. Automating installation and configuration eliminates human error and enables rapid deployment of correctly-configured hundred-node clusters. |
| **Traditional scheduler support** | CycleCloud with native Slurm integration for familiar job scheduling | Many organizations have built workflows around Slurm over years. Supporting it means users bring environments as-is without rewriting job scripts, preserving tuning and scheduling policies. |


## Security

HPC environments often process sensitive data—proprietary research, regulated information, or competitive intellectual property—while requiring broad access for legitimate users. Security architecture must protect assets without creating barriers that impede computational workflows.

### Considerations

- **Network boundary enforcement.** HPC clusters should operate within defined network perimeters that restrict unauthorized access while permitting necessary communication between compute nodes, storage systems, and job submission endpoints.

- **Identity and access governance.** Users require varying levels of access to HPC resources—from basic job submission to administrative cluster management. Permissions should follow least-privilege principles and integrate with organizational identity systems.

- **Regulatory compliance.** Organizations processing data subject to regulatory requirements (HIPAA, ITAR, FedRAMP) must implement specific controls and demonstrate compliance through audit trails and documentation.

- **Workload isolation.** Multi-tenant HPC environments or clusters processing sensitive data require mechanisms to prevent unauthorized access between users or jobs, both at the compute layer and within shared storage systems.

- **Data protection controls.** Sensitive data requires encryption during storage and transmission. Access to protected datasets must be restricted to authorized users and jobs through enforced policies.

### Recommendations

**OLD**

| Azure Service/Feature | Recommendation | 
| ----------------------| ---------------|
| Azure Virtual Network, Network Security Groups | Deploy HPC clusters within isolated virtual networks using Network Security Groups to restrict inbound access and control communication flows between subnets. | 
| Microsoft Entra ID, Azure RBAC | Integrate Azure CycleCloud and Azure Batch with Microsoft Entra ID for authentication; implement Azure RBAC to enforce least-privilege access across HPC resources. | 
| Azure Confidential Computing, Microsoft Defender for Cloud | Deploy Azure confidential computing VMs for sensitive workloads, and use Microsoft Defender for Cloud to monitor compliance posture against regulatory frameworks. | 
| Azure Private Link, Storage Firewalls | Use Azure Private Link to access storage and management services without exposing traffic to public networks; configure storage firewalls to restrict access to authorized virtual networks. | 
| Azure Key Vault, Storage Service Encryption | Enable encryption at rest for all Azure storage services; use Azure Key Vault to manage encryption keys and secrets; enforce encryption in transit for all data movement. | 

**NEW**

| Consideration | Azure Interpretation | Why (General HPC Rationale) |
|---------------|---------------------|----------------------------|
| **Network isolation** | Network Security Groups (NSGs) control traffic at subnet and NIC level | HPC clusters need isolation from public internet except through controlled entry points. Firewall rules limiting traffic to essential communications (SSH, MPI) reduce attack surface and prevent unauthorized access. |
| **Access control** | Azure RBAC for fine-grained permissions | Multi-user environments need privilege separation—researchers submit jobs, operators manage infrastructure, admins configure systems. Role-based access prevents unauthorized actions and maintains data isolation between projects. |
| **Compliance management** | Azure Compliance & Security Center with certifications (HIPAA, GDPR, ITAR) | HPC often processes regulated data (genomics, financial models, proprietary research). Compliance tools enforce policies, highlight violations, and enable cutting-edge research on sensitive data without compromising legal obligations. |


## Cost Optimization

Cloud-based HPC offers the flexibility to scale resources with demand, but without deliberate governance, costs can escalate rapidly. Optimization requires continuous alignment between provisioned resources and actual workload requirements.

### Considerations

- **Storage cost management.** HPC workflows generate large data volumes with varying access patterns. High-performance storage tiers are expensive; retaining cold data on premium storage wastes budget without providing performance benefits.

- **Compute right-sizing.** Over-provisioned VMs waste money on unused capacity; under-provisioned VMs extend job runtimes and may cost more due to prolonged resource consumption. Accurate workload profiling enables appropriate sizing decisions.

- **Elastic scaling alignment.** Static cluster provisioning—whether too large or too small—misaligns costs with value delivery. Infrastructure should expand when jobs are queued and contract when demand subsides.

- **Interruptible workload economics.** Many HPC jobs can tolerate interruption if they implement checkpointing. These workloads are candidates for significantly discounted Spot VMs, but require architectural changes to handle evictions gracefully.

- **Operational overhead accounting.** The total cost of HPC includes not just infrastructure but also the labor required to manage it. Managed services may have higher direct costs but lower total cost when operational effort is included.

### Recommendations

**OLD**

| Azure Service/Feature | Recommendation | 
| ----------------------| ---------------|
| Azure Blob Storage Lifecycle Management | Implement Azure Blob Storage lifecycle management policies to automatically transition aging data from hot to cool or archive tiers based on last access time. | 
| Azure CycleCloud, Azure Batch, Azure Monitor | Use Azure CycleCloud or Azure Batch analytics to profile actual resource consumption, then select VM sizes that match observed CPU, memory, and I/O requirements. | 
| Azure CycleCloud Autoscaling | Configure Azure CycleCloud autoscaling to provision compute nodes in response to scheduler queue depth and deallocate idle nodes after configurable timeout periods. | 
| Azure Spot VMs, Azure Batch, Azure CycleCloud | Deploy Azure Spot VMs for fault-tolerant workloads with checkpointing enabled; configure Batch pools or CycleCloud clusters to use Spot instances with automatic failover to standard VMs when needed. | 
| Azure Batch, Azure CycleCloud | Evaluate Azure Batch (fully managed) versus Azure CycleCloud (self-managed schedulers) based on team expertise, customization requirements, and total cost including operational labor. | 
| Azure Cost Management, Resource Tags | Use Azure Cost Management to establish budgets, configure alerts, and analyze HPC spending patterns; implement resource tagging to attribute costs to specific projects or research groups. |

**NEW**

| Consideration | Azure Interpretation | Why (General HPC Rationale) |
|---------------|---------------------|----------------------------|
| **Tiered storage management** | Azure Blob Storage and Data Lake with lifecycle policies | HPC produces massive datasets—not all need expensive fast storage forever. Automated tiering keeps hot data on fast storage and migrates cold data to cheaper long-term storage, balancing performance and cost for petabyte-scale data. |
| **Right-sizing guidance** | Azure HPC Calculator for optimal resource configuration | Choosing appropriate hardware prevents over-provisioning expensive specialized resources or under-provisioning and hitting limits. Tools and guidelines help find the sweet spot that runs jobs efficiently without wasting budget. |
## Next Steps

Design areas are interconnected and changes in one area may impact others. Start with the area most critical to your workload, then review how choices create trade-offs across the architecture.
