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

The HPC design methodology is grounded in five design principles derived from the Azure Well‑Architected Framework (WAF) pillars. These principles guide architectural decisions across core HPC design areas such as compute, storage, networking, and job orchestration. Familiarity with these principles helps you understand their impact and the trade‑offs involved when they are not followed.

This article summarizes key considerations and recommendations, organized by WAF pillar, to help you design HPC environments on Azure that are reliable, secure, cost‑effective, operationally excellent, and high‑performing. 

These recommendations are most relevant for large‑scale, long‑running HPC workloads and may be adjusted for smaller or exploratory workloads.

Maximizing performance and reliability often increases cost. Design decisions should be driven by business requirements and by clearly defined trade‑offs between performance, reliability, and cost.

## Reliability

Building reliable HPC environments on Azure requires planning for failures at multiple levels from individual compute nodes to entire regions. The ephemeral nature of cloud resources and the long-running characteristics of HPC jobs demand deliberate strategies for state preservation and recovery.

### Considerations

- **Job state preservation:** Long‑running HPC jobs are vulnerable to infrastructure failures, spot evictions, and maintenance events. Without systematic checkpointing, interruptions can force full job restarts, wasting compute hours and delaying results.

- **Node failure tolerance:** Individual VM failures are expected in large‑scale deployments. The scheduling and orchestration layer must detect failures promptly and either reassign work to healthy nodes or preserve state for recovery.

- **Data protection strategy:** HPC workflows generate data with varying durability requirements, ranging from disposable scratch files to irreplaceable simulation results. Protection mechanisms should align with data value without imposing unnecessary overhead on transient working data.

- **Cross-region continuity:** Critical workloads may require the ability to resume computation in an alternate Azure region after a regional outage. This capability requires advance planning for data replication and environment reproducibility.

### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| Implement application-level checkpointing to [Azure Blob Storage](/azure/storage/blobs/) or [Azure Managed Lustre](/azure/azure-managed-lustre/). Align checkpoint frequency with RPO targets and job criticality. | Protects long-running jobs from complete restart after failures. Jobs resume from last checkpoint rather than beginning, saving compute time and cost. |
| Use Azure CycleCloud or Azure Batch automatic retry policies to handle transient node failures and reschedule failed tasks without manual intervention. | Maintains job progress during temporary infrastructure issues. Automatic rescheduling eliminates manual monitoring and reduces time to completion. |
| Replicate critical datasets and final results to [geo-redundant storage (GRS)](/azure/storage/common/storage-redundancy#geo-redundant-storage) or a paired Azure region. Use locally redundant storage for high-performance scratch data. | Balances data protection with performance needs. Critical data survives regional failures while temporary scratch data maintains high throughput during computation. |
| Maintain infrastructure-as-code definitions using [Bicep](/azure/azure-resource-manager/bicep/) or [Terraform](/azure/developer/terraform/) in Azure DevOps or GitHub to enable repeatable cluster deployment in alternate regions during disaster recovery. | Enables faster recovery after catastrophic failures by reducing manual reconfiguration and improving deployment consistency. |


## Security

HPC environments often process sensitive data, including proprietary research, regulated information, and competitive intellectual property, while requiring broad access for legitimate users. Security architecture must protect assets without creating barriers that impede computational workflows.

### Considerations

- **Network boundary enforcement:** Operate HPC clusters within defined network perimeters that restrict unauthorized access while permitting required communication between compute nodes, storage systems, and job submission endpoints.

- **Identity and access governance:** Users require varying levels of access to HPC resources, from basic job submission to administrative cluster management. Permissions should follow least‑privilege principles and integrate with organizational identity systems.

- **Regulatory compliance:** Organizations processing data subject to regulatory requirements such as HIPAA, ITAR, or FedRAMP must implement specific controls and maintain audit evidence to demonstrate compliance.

- **Workload isolation:** Multi-tenant HPC environments or clusters processing sensitive data require mechanisms to prevent unauthorized access between users or jobs, both at the compute layer and within shared storage systems.

- **Data protection controls:** Sensitive data requires encryption during storage and transmission. Access to protected datasets must be restricted to authorized users and jobs through enforced policies.

### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| Deploy HPC clusters within isolated virtual networks using network security groups | Reduces the attack surface by limiting network access to only required endpoints while allowing necessary node‑to‑node and storage communication. |
| Use Microsoft Entra ID for authentication, and integrate role [Azure RBAC](/azure/role-based-access-control/) | Enforces least-privilege access and provides centralized identity governance across HPC resources. |
| Use Secure administrative access using Azure Bastion | Prevents direct exposure of management ports and reduces the risk of unauthorized access. |
| Enable [encryption at rest](/azure/storage/common/storage-service-encryption) for all Azure storage services. Apply Azure policy to enforce security baselines. | Provides visibility into security posture, supports compliance audits, and enables rapid detection of misconfigurations or threats. Ensures consistent security controls across clusters and prevents configuration drift over time. |


## Cost Optimization

Cloud‑based HPC enables elastic scaling, but costs can grow rapidly without deliberate control. Maximizing business value requires balancing spend with performance and reliability requirements.

### Considerations

- **Storage cost management:** HPC workflows generate large data volumes with varied access patterns. Keeping cold data on high‑performance tiers increases costs without performance benefits.
  
- **Compute right-sizing:** Over‑provisioned VMs waste capacity, while under‑provisioned VMs extend job runtimes and increase total cost. Accurate workload profiling supports effective sizing.

- **Elastic scaling alignment:** Static cluster provisioning misaligns costs with workload demand. Infrastructure should scale with queued jobs and contract when demand subsides.

- **Interruptible workload economics:** Many HPC workloads tolerate interruption with checkpointing, making them suitable for discounted capacity.

- **Operational overhead accounting:** Total cost includes infrastructure and operational effort. Managed services can reduce administrative overhead even if unit costs are higher.

### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| Use Azure Blob Storage lifecycle management to transition aging data from hot to cool or archive tiers. | Reduces storage costs while maintaining data accessibility. |
| Analyze resource usage with Azure CycleCloud or Azure Batch analytics to select appropriate VM sizes. | Avoids unused capacity and balances performance with cost. |
| Configure [Azure CycleCloud autoscaling](/azure/cyclecloud/how-to/configure-autoscaling.md#tab=auto-scaling) to provision compute nodes based on scheduler queue depth and deallocate idle nodes. | Aligns compute capacity with demand and eliminates idle spend. |
| Deploy [Azure Spot VMs](/azure/virtual-machines/spot-vms) for fault-tolerant workloads with checkpointing, with fallback to standard VMs. | Lowers compute costs for interruption‑tolerant workloads. |

## Operational Excellence

Operational maturity in HPC environments requires balancing user autonomy with system reliability. Users expect familiar tools and workflows, while operations teams need automation, visibility, and repeatability to manage infrastructure at scale.

### Considerations

- **Infrastructure reproducibility:** Manual cluster provisioning introduces inconsistencies and delays. HPC environments should be deployed using automated, version‑controlled processes that produce consistent results across environments.

- **Scheduler integration:** HPC users rely on established job schedulers. Forcing new submission models increases friction and slows adoption of cloud-based HPC.

- **Workflow automation:** HPC pipelines often span data staging, preprocessing, computation, post-processing, and results delivery. Manual coordination increases error rates and limits throughput.

- **Observability and diagnostics:** Failures in large-scale HPC jobs can be difficult to diagnose without comprehensive telemetry. Operators need visibility into job status, resource utilization, and system health.

- **Change management discipline:** Updates to software stacks, schedulers, or infrastructure can introduce regressions. Changes should be validated and deployed through controlled, repeatable processes.

### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| Define HPC cluster infrastructure using Bicep or Terraform templates with [Azure Resource Manager](/azure/azure-resource-manager/) to manage deployments as code. | Ensures consistent, reproducible deployments and prevents configuration drift. Infrastructure as code also enables faster recovery scenarios. |
| Deploy Azure CycleCloud with native support for [Slurm](/azure/cyclecloud/qs-deploy-ccws.md#tab=how-to-deploy), PBS Pro, Grid Engine, or other schedulers that align with existing workflows. | Preserves user productivity by allowing existing job scripts and submission models to run unchanged. |
| Use [Azure Batch for managed job scheduling](/azure/batch/batch-job-schedule) with task dependencies, or integrate CycleCloud clusters with workflow orchestration tools for multi-stage pipelines. | Automates complex workflows and ensures pipeline stages execute in the correct order without manual coordination. |
| Configure [Azure Monitor](/azure/azure-monitor/) with diagnostic logs, metrics, and  [Log Analytics workspaces](/azure/azure-monitor/logs/log-analytics-overview) to build operational dashboards. | Improves visibility into cluster health and job progress, enabling faster troubleshooting and proactive operations. |


## Performance Efficiency

HPC workloads require maximum computational throughput, making hardware selection and architectural alignment critical. Azure provides purpose-built HPC infrastructure, but achieving optimal performance depends on matching workload characteristics to compute, networking, and storage configurations.

### Considerations

- **Workload-hardware alignment:** HPC applications vary widely in resource demands, including CPU-bound simulations, memory-intensive analytics, GPU-accelerated computations, and I/O-heavy workflows. Selecting inappropriate VM families limits achievable performance.

- **Interconnect performance:** Tightly coupled parallel workloads are highly sensitive to network latency and bandwidth. Standard Ethernet networking can introduce bottlenecks that offset the benefits of horizontal scaling.

- **Parallel storage throughput:** Large-scale HPC jobs generate concurrent access patterns that overwhelm traditional file servers. Storage systems must sustain high aggregate throughput across many nodes.

- **Cluster topology optimization:** Physical VM proximity directly affects MPI performance. Distributed placement increases latency and degrades tightly coupled workload efficiency.

- **Environment consistency:** Configuration drift, library mismatches, or software version inconsistencies across nodes introduce performance variability and complicate tuning and troubleshooting.

### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| Select [HBv4](/azure/virtual-machines/hbv4-series) or [HBv5 series VMs](/azure/virtual-machines/sizes/high-performance-compute/hbv5-series) for CPU-intensive workloads, [NDv5 series](/azure/virtual-machines/sizes/gpu-accelerated/ndh100v5-series) for GPU-accelerated workloads, and memory‑optimized VM families for data‑intensive analytics. | Aligns hardware capabilities with application requirements, maximizing performance efficiency and value. |
| Deploy [InfiniBand-enabled VM series](/azure/virtual-machines/workloads/hpc/enable-infiniband) (HB, HC, ND) for MPI workloads requiring low-latency, high-bandwidth node-to-node communication. | Minimizes communication overhead and enables efficient scaling for tightly coupled parallel workloads. |
| Use Azure Managed Lustre for high‑throughput scratch storage, or Azure NetApp Files for shared POSIX‑compliant file access with consistent performance. | Prevents storage bottlenecks by sustaining concurrent access from large numbers of compute nodes. |
| Configure [Proximity Placement Groups](/azure/virtual-machines/co-location) to place compute nodes within the same datacenter network segment. | Reduces inter‑node latency and improves message‑passing performance for communication‑intensive workloads. |


## Next Steps

Design areas are interconnected and changes in one area may impact others. Start with the area most critical to your workload, then review how choices create trade-offs across the architecture.
