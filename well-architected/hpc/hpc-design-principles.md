---
title: Design Principles for HPC Workloads on Azure
description: Review the design principles of the Azure Well-Architected Framework. Learn how to apply these principles to an HPC workload.
author: padmalathas
ms.author: prwilk
ms.date: 03/06/2026
ms.topic: concept-article
ms.update-cycle: 180-days  
---

# Design Principles for HPC Workloads

The HPC design methodology is underpinned by five key design principles derived from the Well-Architected Framework (WAF) pillars. These principles serve as a compass for subsequent design decisions across critical design areas. We highly recommend that you familiarize yourself with these principles to better understand their impact and the trade-offs associated with non-adherence. The following tables present configuration recommendations organized by WAF pillars, helping you build HPC environments that are reliable, secure, cost-effective, operationally excellent, and performant.

> [!NOTE]
> There are obvious cost trade-offs associated with maximizing performance and reliability. Design decisions should be driven by business requirements and acceptable trade-offs between performance, reliability, and cost.

## Reliability

Building reliable HPC environments on Azure requires planning for failures at multiple levels from individual compute nodes to entire regions. The ephemeral nature of cloud resources and the long-running characteristics of HPC jobs demand deliberate strategies for state preservation and recovery.

### Considerations

- **Job state preservation.** Long-running HPC jobs are vulnerable to infrastructure failures, spot evictions, and maintenance events. Without systematic checkpointing, any interruption forces complete job restarts, wasting compute hours and delaying results.
- **Node failure tolerance.** Individual VM failures are expected events in large-scale deployments. The scheduling and orchestration layer must detect failures promptly and either reassign work to healthy nodes or preserve state for recovery.
- **Data protection strategy.** HPC workflows generate data with varying durability requirements from disposable scratch files to irreplaceable simulation results. Protection mechanisms should match data value without imposing unnecessary overhead on transient working data.
- **Cross-region continuity.** Critical workloads may require the ability to resume computation in an alternate Azure region following regional outages. This capability requires advance planning for data replication and environment reproducibility.

### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| Implement application-level checkpointing to [Azure Blob Storage](https://learn.microsoft.com/en-us/azure/storage/blobs/) or [Azure Managed Lustre](https://learn.microsoft.com/en-us/azure/azure-managed-lustre/), with checkpoint frequency aligned to RPO targets and job criticality. | Protects long-running jobs from complete restart after failures. Jobs resume from last checkpoint rather than beginning, saving compute time and cost. |
| Use [Azure CycleCloud](https://learn.microsoft.com/en-us/azure/cyclecloud/) or [Azure Batch](https://learn.microsoft.com/en-us/azure/batch/) automatic retry policies to handle transient node failures and reschedule failed tasks without manual intervention. | Maintains job progress during temporary infrastructure issues. Automatic rescheduling eliminates manual monitoring and reduces time to completion. |
| Replicate critical datasets and final results to [geo-redundant storage (GRS)](https://learn.microsoft.com/en-us/azure/storage/common/storage-redundancy#geo-redundant-storage) or a paired Azure region, while using locally redundant storage for high-performance scratch data. | Balances data protection with performance needs. Critical data survives regional failures while temporary scratch data maintains high throughput during computation. |
| Maintain infrastructure-as-code definitions using [Bicep](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/) or [Terraform](https://learn.microsoft.com/en-us/azure/developer/terraform/) in Azure DevOps or GitHub to enable rapid cluster recreation in alternate regions when disaster recovery is required. | Enables quick recovery after catastrophic failures. Clusters can be rebuilt in new regions within hours instead of days of manual reconfiguration. |

## Security

HPC environments often process sensitive data proprietary research, regulated information, or competitive intellectual property while requiring broad access for legitimate users. Security architecture must protect assets without creating barriers that impede computational workflows.

### Considerations

- **Network boundary enforcement.** HPC clusters should operate within defined network perimeters that restrict unauthorized access while permitting necessary communication between compute nodes, storage systems, and job submission endpoints.
- **Identity and access governance.** Users require varying levels of access to HPC resources from basic job submission to administrative cluster management. Permissions should follow least-privilege principles and integrate with organizational identity systems.
- **Regulatory compliance.** Organizations processing data subject to regulatory requirements (HIPAA, ITAR, FedRAMP) must implement specific controls and demonstrate compliance through audit trails and documentation.
- **Workload isolation.** Multi-tenant HPC environments or clusters processing sensitive data require mechanisms to prevent unauthorized access between users or jobs, both at the compute layer and within shared storage systems.
- **Data protection controls.** Sensitive data requires encryption during storage and transmission. Access to protected datasets must be restricted to authorized users and jobs through enforced policies.

### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| Deploy HPC clusters within isolated [virtual networks](https://learn.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview) using [Network Security Groups](https://learn.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview) to restrict inbound access and control communication flows between subnets. | Prevents unauthorized access to computational resources and sensitive data. Network isolation limits attack surface and contains potential security breaches. |
| Integrate Azure CycleCloud and Azure Batch with [Microsoft Entra ID](https://learn.microsoft.com/en-us/entra/identity/) for authentication, implement [Azure RBAC](https://learn.microsoft.com/en-us/azure/role-based-access-control/) to enforce least-privilege access across HPC resources. | Ensures only authorized users access HPC resources. Role-based permissions prevent accidental or malicious misuse of expensive compute infrastructure. |
| Use [Azure Private Link](https://learn.microsoft.com/en-us/azure/private-link/) to access storage and management services without exposing traffic to public networks; configure [storage firewalls](https://learn.microsoft.com/en-us/azure/storage/common/storage-network-security) to restrict access to authorized virtual networks. | Eliminates data exposure to public internet during transfers. Private connectivity prevents data exfiltration and man-in-the-middle attacks. |
| Enable [encryption at rest](https://learn.microsoft.com/en-us/azure/storage/common/storage-service-encryption) for all Azure storage services; use [Azure Key Vault](https://learn.microsoft.com/en-us/azure/key-vault/) to manage encryption keys and secrets; enforce encryption in transit for all data movement. | Protects data if storage media is compromised or traffic is intercepted. Centralized key management ensures consistent security policy enforcement. |


## Cost Optimization

Cloud-based HPC offers the flexibility to scale resources with demand, but without deliberate governance, costs can escalate rapidly. Optimization requires continuous alignment between provisioned resources and actual workload requirements.

### Considerations

- **Storage cost management.** HPC workflows generate large data volumes with varying access patterns. High-performance storage tiers are expensive; retaining cold data on premium storage wastes budget without providing performance benefits.
- **Compute right-sizing.** Over-provisioned VMs waste money on unused capacity; under-provisioned VMs extend job runtimes and may cost more due to prolonged resource consumption. Accurate workload profiling enables appropriate sizing decisions.
- **Elastic scaling alignment.** Static cluster provisioning—whether too large or too small misaligns costs with value delivery. Infrastructure should expand when jobs are queued and contract when demand subsides.
- **Interruptible workload economics.** Many HPC jobs can tolerate interruption if they implement checkpointing. These workloads are candidates for significantly discounted Spot VMs, but require architectural changes to handle evictions gracefully.
- **Operational overhead accounting.** The total cost of HPC includes not just infrastructure but also the labor required to manage it. Managed services may have higher direct costs but lower total cost when operational effort is included.

### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| Implement [Azure Blob Storage lifecycle management](https://learn.microsoft.com/en-us/azure/storage/blobs/lifecycle-management-overview) policies to automatically transition aging data from hot to cool or archive tiers based on last access time. | Reduces storage costs without manual intervention. Older data automatically moves to cheaper tiers while maintaining accessibility for compliance. |
| Use Azure CycleCloud or [Azure Batch analytics](https://learn.microsoft.com/en-us/azure/batch/batch-analytics) to profile actual resource consumption, then select VM sizes that match observed CPU, memory, and I/O requirements. | Prevents paying for unused capacity. Right-sized VMs deliver required performance at lower cost than over-provisioned alternatives. |
| Configure [Azure CycleCloud autoscaling](https://learn.microsoft.com/en-us/azure/cyclecloud/concepts/autoscale) to provision compute nodes in response to scheduler queue depth and deallocate idle nodes after configurable timeout periods. | Eliminates spending on idle resources. Automatic scaling matches capacity to demand, reducing costs during low-utilization periods. |
| Deploy [Azure Spot VMs](https://learn.microsoft.com/en-us/azure/virtual-machines/spot-vms) for fault-tolerant workloads with checkpointing enabled; configure Batch pools or CycleCloud clusters to use Spot instances with automatic failover to standard VMs when needed. | Achieves significant compute cost savings. Spot pricing can be 70-90% cheaper than standard VMs for workloads that tolerate interruptions. |

## Operational Excellence

Operational maturity in HPC environments requires balancing user autonomy with system reliability. Researchers and engineers expect familiar tools and workflows, while operations teams need visibility, automation, and reproducibility to manage complex infrastructure at scale.

### Considerations

- **Infrastructure reproducibility.** Manual cluster provisioning introduces configuration inconsistencies and delays. HPC environments should be deployable through automated, version-controlled processes that produce identical results across deployments.
- **Scheduler integration.** HPC users have established workflows built around specific job schedulers. Requiring users to adopt unfamiliar submission methods creates friction and delays adoption of cloud-based HPC resources.
- **Workflow automation.** End-to-end HPC pipelines involve multiple stages data staging, preprocessing, computation, post-processing, and results delivery. Manual orchestration of these stages introduces errors and limits throughput.
- **Observability and diagnostics.** Large-scale HPC jobs can fail in subtle ways that are difficult to diagnose without comprehensive telemetry. Operators need visibility into job progress, resource utilization, and system health to troubleshoot issues effectively.
- **Change management discipline.** Updates to HPC software stacks, scheduler configurations, or infrastructure definitions can introduce regressions that cause job failures. Changes should be tested and deployed through controlled processes.

### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| Define HPC cluster infrastructure using Bicep or Terraform templates with [Azure Resource Manager](https://learn.microsoft.com/en-us/azure/azure-resource-manager/), enabling consistent deployments and version-controlled infrastructure changes. | Ensures reproducible environments across deployments. Infrastructure as code eliminates configuration drift and enables rapid disaster recovery. |
| Deploy Azure CycleCloud with native support for [Slurm](https://learn.microsoft.com/en-us/azure/cyclecloud/how-to/run-slurm-clusters), PBS Pro, Grid Engine, or other schedulers that align with existing user workflows and submission scripts. | Preserves user productivity during cloud migration. Familiar schedulers eliminate retraining and allow existing job scripts to run unchanged. |
| Implement [Azure Batch for managed job scheduling](https://learn.microsoft.com/en-us/azure/batch/batch-job-schedule) with built-in task dependencies, or integrate CycleCloud clusters with workflow orchestration tools for complex multi-stage pipelines. | Automates multi-step computational workflows. Dependencies execute in correct order without manual coordination between pipeline stages. |
| Configure [Azure Monitor](https://learn.microsoft.com/en-us/azure/azure-monitor/) to collect cluster metrics, job telemetry, and diagnostic logs; use [Log Analytics workspaces](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/log-analytics-overview) and workbooks to create operational dashboards for HPC environments. | Provides visibility into cluster health and job progress. Dashboards enable proactive problem identification before issues impact users. |


## Performance Efficiency

HPC workloads demand maximum computational throughput, making hardware selection and system architecture critical to achieving performance objectives. Azure provides purpose-built HPC infrastructure, but realizing its potential requires deliberate alignment between workload characteristics and resource configuration.

### Considerations

- **Workload-hardware alignment.** HPC applications exhibit diverse resource profiles CPU bound simulations, memory-intensive analyses, GPU accelerated computations, and I/O-dominated workflows each require different VM families to achieve optimal performance.
- **Interconnect performance.** Tightly coupled parallel applications that exchange data frequently between nodes are highly sensitive to network latency and bandwidth. Standard ethernet networking introduces bottlenecks that can negate the benefits of additional compute nodes.
- **Parallel storage throughput.** When hundreds of compute nodes access shared datasets simultaneously, storage systems become the primary performance constraint. Traditional file servers cannot sustain the aggregate throughput that large-scale HPC jobs demand.
- **Cluster topology optimization.** Physical proximity between communicating VMs directly impacts MPI performance. Distributed placement across datacenter infrastructure introduces latency that degrades tightly coupled workload performance.
- **Environment consistency.** Performance variability across nodes caused by software version differences, library mismatches, or configuration drift produces unpredictable job behavior and complicates performance optimization efforts.

### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| Select [HBv4](https://learn.microsoft.com/en-us/azure/virtual-machines/hbv4-series) or [HBv5 series VMs](https://learn.microsoft.com/en-us/azure/virtual-machines/hbv5-series) for CPU-intensive workloads requiring high memory bandwidth; choose [NDv5 series](https://learn.microsoft.com/en-us/azure/virtual-machines/ndv5-series) for GPU-accelerated computations; use memory-optimized series for data-intensive analytics. | Matches hardware capabilities to application requirements. Appropriate VM selection delivers maximum performance per dollar spent. |
| Deploy [InfiniBand-enabled VM series](https://learn.microsoft.com/en-us/azure/virtual-machines/workloads/hpc/enable-infiniband) (HB, HC, ND) for MPI workloads requiring low-latency, high-bandwidth node-to-node communication. | Minimizes communication overhead in parallel applications. Low-latency networking enables efficient scaling to hundreds or thousands of nodes. |
| Implement [Azure Managed Lustre](https://learn.microsoft.com/en-us/azure/azure-managed-lustre/amlfs-overview) for scratch storage requiring high aggregate throughput, or [Azure NetApp Files](https://learn.microsoft.com/en-us/azure/azure-netapp-files/) for workloads needing shared POSIX-compliant file access with consistent performance. | Prevents storage from becoming the bottleneck. Parallel file systems sustain concurrent access from many nodes without performance degradation. |
| Configure [Proximity Placement Groups](https://learn.microsoft.com/en-us/azure/virtual-machines/co-location) to allocate compute nodes within the same datacenter network segment, minimizing latency for tightly coupled parallel applications. | Reduces network distance between communicating nodes. Physical proximity improves message passing performance in communication-intensive workloads. |

## Next Steps

Design areas are interconnected and changes in one area may impact others. Start with the area most critical to your workload, then review how choices create trade-offs across the architecture.
