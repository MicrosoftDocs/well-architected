---
title: Azure Well-Architected Framework review for Azure Stack Hub
description: Provides a template for a Well-Architected Framework (WAF) article that is specific to Azure Stack Hub.
author: neilbird
ms.author: nebird
ms.topic: conceptual
ms.custom:
products: azure-stack-hub
---

# Azure Well-Architected Framework review - Azure Stack Hub

This article provides architectural best practices for Azure Stack Hub. The guidance is based on the five pillars of architecture excellence:

- Reliability
- Security
- Cost optimization
- Operational excellence
- Performance efficiency

We assume that you understand system design principles, have working knowledge of Azure Stack Hub, and are well versed with its features. For more information, see [Azure Stack Hub](/azure-stack/operator/azure-stack-overview).

## Prerequisites

Understanding the Well-Architected Framework pillars can help produce a high-quality, stable, and efficient cloud architecture. We recommend that you review your workload by using the [Azure Well-Architected Framework Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.

For context, consider reviewing a reference architecture that reflects these considerations in its design. We recommend that you start with the [baseline architecture for an Azure Stack Hub cluster](/azure/architecture/hybrid/azure-stack-robo) and [Azure Stack Hub network deployment patterns](/azure-stack/operator/azure-stack-network). Also review the [Prepare your environment for a hybrid and multi-cloud scenario](/azure/cloud-adoption-framework/scenarios/hybrid/ready), which provides an architectural approach and reference implementation to prepare landing zone subscriptions for a scalable Azure Stack Hub cluster.

## Reliability

In the cloud, we acknowledge that failures happen. Instead of trying to prevent failures altogether, the goal is to minimize failures and the effects of a single failing component. This section is focused on providing information to help minimize the impact of failures.

Azure Stack Hub is a [hyper-converged infrastructure platform](\azure-stack\operator\azure-stack-capacity-planning-overview#hyperconvergence-and-the-scale-unit) that provides a level of resiliency and [fault tolerance](\azure-stack\operator\azure-stack-storage-infrastructure-overview#fault-tolerance-and-mirroring) using co-engineered hardware and software technologies. These technologies provide availability and scalability of resources within an Azure Stack Hub cluster, also referred to as a *scale unit*.

Azure Stack Hub uses the same operational model as Azure, however instead of Microsoft employees managing the underlying platform, your engineers perform the role and responsibilities of the Cloud Operator persona. For more information, see [How is Azure Stack Hub managed?](\azure-stack\operator\azure-stack-overview#how-is-azure-stack-hub-managed).

Reliability in the context of Azure Stack Hub, is a **shared responsibility** between the [user](\azure-stack\user) and [cloud operator](\azure-stack\operator) personas. Users design, deploy and operate ARM resources that host applications and services on one or more Azure Stack Hub scale units. Cloud operators are responsible for the management, monitoring, capacity management and update operations provided by the Azure Stack Hub scale unit(s).

### Reliability for users

Users designing and deploying applications or business systems on Azure Stack Hub should apply the general architecture design principles and guidance used for Azure<sup>*</sup>. One of the first design requirements is to define and document the applications' availability and recovery targets. These targets include concepts such as the recovery time objective (RTO) and recovery point objective (RPO). RTO is the *maximum acceptable time that an application is unavailable after an incident*. RPO defines the *maximum duration of data loss that's acceptable during a disaster.*

Once the availability and recovery targets have been defined, the application architecture can be designed to meet the requirements. To achieve low RTO and RPO times, industry standard architecture patterns should be leveraged. These patterns include practices such as clustering, asynchronous or synchronous database operations, file replication, and implementing automated or manual failover capabilities within the application to meet the high availability (HA) and disaster recovery (DR) requirements. There are several example reference architectures and solution deployment guides available in the [Hybrid section of Azure Architecture Center](/azure/architecture/hybrid/hybrid-start-here). The reference architectures include diagrams and step by step guides that can be used to deploy the solutions on Azure Stack Hub, examples below:

- [Deploy a SQL Server Availability Groups across two Azure Stack Hub environments](/azure/architecture/hybrid/deployments/solution-deployment-guide-sql-ha)
- [Deploy a highly available MongoDB solution across two Azure Stack Hub environments](/azure/architecture/hybrid/deployments/solution-deployment-guide-mongodb-ha)
- [Deploy a high availability Kubernetes cluster on Azure Stack Hub](/azure/architecture/hybrid/deployments/solution-deployment-guide-highly-available-kubernetes)

Users designing workloads to run on Azure Stack Hub should aim to [make all things redundant](\azure\architecture\guide\design-principles\redundancy) in their application architecture design. For any line-of-business (LOB) applications that expose end points for services, the endpoints should be deployed using a load balancer to allow for [multiple instances of the service(s) to be provisioned on multiple virtual machines (VMs)](\azure\architecture\reference-architectures\n-tier\n-tier-sql-server). Each virtual machine that hosts an instance of a service should be added to an [Availability Set to provide high availability using fault domains](\azure-stack\user\azure-stack-vm-considerations#high-availability).

<sub>* Where there is consistency in the resources and resiliency capabilities available for both Azure and Azure Stack Hub.</sub>

### Reliability for cloud operators

Cloud operators are responsible for the day-to-day operational tasks that ensure effective management and operation of an Azure Stack Hub scale unit. Many of these tasks are related to underlying platform and therefore can affect reliability, including:

- Infrastructure health and availability management:
  - Hardware components of the physical nodes, such as their motherboards, CPUs, memory DIMMs, disks, and networking connectivity to the Top of Rack (ToR) switches.
  - Region health for resource provider (RP) services:
- Capacity management for the storage, network, and compute resource providers
  - Defining and applying quotas to ensure users can effectively use the system within the defined requirements.
- Disaster recovery for the platform
  - Infrastructure backup

To understand how Azure Stack Hub supports reliability for your application workloads, reference the following articles:

- [Capacity planning for Azure Stack Hub overview](/azure-stack/operator/azure-stack-capacity-planning-overview)
- [Storage Spaces Direct cache and capacity tiers](/azure-stack/operator/azure-stack-capacity-planning-storage#storage-spaces-direct-cache-and-capacity-tiers)
- [Datacenter integration planning considerations for Azure Stack Hub integrated systems](/azure-stack/operator/azure-stack-datacenter-integration)

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each consideration is applicable to cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]

> - **Cluster architecture (Server):** Reserve additional hosts for emergency scenarios (e.g. maintenance, one node is down etc.). The recommendation is to have N + 1 nodes for the cluster.
> - **Cluster architecture (Storage):** Estimate the number of failures that can occur simultaneously without compromising availability and data. The recommendation is to have 2 additional disks to sustain disk failures.
> - **Cluster architecture (Storage):** Choose the right resiliency preference (Performance, Capacity) for storage based on the workload. This choice decides what type of mirroring will be used for the CSV filesystem.
> - **Cluster architecture (Host Network):** To ensure fault tolerance, it is recommended to have two or more network adapters per node which are symmetric (of the same make, model, speed, and configuration) across all nodes.This is because having multiple network adapters provides redundancy and ensures that the cluster remains operational even if one of the network adapters fails.
> - **Cluster architecture (Physical Switch Network):** To ensure resiliency, it is recommended to have two TOR  switches and ensure the network adapters for management, compute, storage are connected to the TOR switches in fault tolerant mode.
> - **Workload architecture:** The reliability design of the workloads varies depending on the type of workloads (VM, AKS, AVD, etc.,) that is deployed on Azure Stack Hub. Refer to the respective workload specific guidance in designing the reliability for each of the workloads.
> - **Workload architecture:** For mission critical applications users need to consider implementing with primary and secondary locations. With primary and secondary locations, users can deploy applications in an active/passive configuration across two environments. For less critical workloads, it might be acceptable to use unused capacity in the secondary location to perform on-demand restoration of applications from backup. 

### Azure Stack Hub configuration recommendations

Explore the following table of recommendations to optimize your Azure Stack Hub configuration for Reliability.

| Recommendation | Benefit |
|--------|----|
|**Cluster and workload architectures:** Control pod scheduling using node selectors and affinity.|Allows the hybrid workloads scheduler to logically isolate workloads by hardware in the node. Unlike pods without a matching node selector can be scheduled on labeled nodes, which allows unused resources on the nodes to consume, but gives priority to pods that define the matching node selector. Use node affinity for more flexibility, which allows you to define what happens if the pod can't be matched with a node.|
|**Cluster architecture:** Ensure proper selection of network plugin based on network requirements and cluster sizing.|Azure CNI is required for specific scenarios, for example, Windows-based node pools, specific networking requirements and hybrid workloads Network Policies. Reference for more information.

For more suggestions, see [Principles of the reliability pillar](/azure/well-architected/resiliency/principles).

## Security

AKS Engine for Kubernetes - section below needs complete review....

Security is one of the most important aspects of any architecture. To explore how Azure Stack Hub can bolster the security of your application workload, we recommend you review the [Security design principles](../security/principles.md). If your Azure Stack Hub cluster needs to be designed to run a sensitive workload that meets the regulatory requirements of the Payment Card Industry Data Security Standard (PCI-DSS 3.2.1), review [Azure Stack Hub regulated cluster for PCI-DSS 3.2.1](/azure/architecture/reference-architectures/containers/Azure Stack Hub-pci/Azure Stack Hub-pci-intro).

To learn about DoD Impact Level 5 (IL5) support and requirements with Azure Stack Hub, review [Azure Government IL5 isolation requirements](/azure/azure-government/documentation-government-impact-level-5#azure-hybrid workloads-service).

When discussing security with Azure Stack Hub, it's important to distinguish between *cluster security* and *workload security*. Cluster security is a shared responsibility between the cluster admin and their resource provider, while workload security is the domain of a developer. Azure Stack Hub has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]
>
> - **Cluster architecture:** Use [Managed Identities](/azure/Azure Stack Hub/use-managed-identity) to avoid managing and rotating service principles.
> - **Cluster architecture:** Use 
> - **Cluster architecture:** Use Microsoft Defender for containers deployed in AKS Hybrid with [Azure Sentinel](/azure/sentinel/overview) to detect and quickly respond to threats across your cluster and workloads running on them.
> - **Cluster architecture:** Deploy a 
> - **Workload architecture:** Use a 
> - **Workload architecture:** Ensure your CI/CID pipeline is hardened with container-aware scanning.

### Recommendations

Explore the following table of recommendations to optimize your Azure Stack Hub configuration for security.

|Recommendation|Benefit|
|----------------------------------|-----------|
|**Cluster architecture:** Use Microsoft Entra integration.|Using Microsoft Entra ID centralizes the identity management component. Any change in user account or group status is automatically updated in access to the Azure Stack Hub cluster. The developers and application owners of your hybrid workloads cluster need access to different resources.|
|**Cluster architecture:** Authenticate with Microsoft Entra ID to Azure Container Registry.|Azure Stack Hub and Microsoft Entra ID enables authentication with Azure Container Registry without the use of `imagePullSecrets` secrets. Review [Authenticate with Azure Container Registry from Azure Stack Hub](/azure/Azure Stack Hub/cluster-container-registry-integration?tabs=azure-cli) for more information.|
|**Cluster architecture:** Secure network traffic to your API server with [private Azure Stack Hub cluster](/azure/Azure Stack Hub/private-clusters).|By default, network traffic between your node pools and the API server travels the Microsoft backbone network; by using a private cluster, you can ensure network traffic to your API server remains on the private network only.|
|**Cluster architecture:** For non-private Azure Stack Hub clusters, use API server authorized IP ranges.|When using public clusters, you can still limit the traffic that can reach your clusters API server by using the authorized IP range feature. Include sources like the public IPs of your deployment build agents, operations management, and node pools' egress point (such as Azure Firewall).|
|**Cluster architecture:** Protect the API server with Microsoft Entra RBAC.|Securing access to the hybrid workloads API Server is one of the most important things you can do to secure your cluster. Integrate hybrid workloads role-based access control (RBAC) with Microsoft Entra ID to control access to the API server. [Disable local accounts](/azure/Azure Stack Hub/managed-aad#disable-local-accounts) to enforce all cluster access using Microsoft Entra ID-based identities.|

For more suggestions, see [Principles of the security pillar](/azure/well-architected/security/security-principles).

#### Cluster architecture

- Microsoft Defender for Cloud-based policies
- Authentication mode and configuration policies (Microsoft Entra ID, RBAC, disable local authentication)
- API Server network access policies, including private cluster

#### Cluster and workload architecture

- Workloads deployed in AKS Hybrid cluster pod security initiatives Linux-based workloads
- Include 

## Cost optimization

Cost optimization is about understanding your different configuration options and recommended best practices to reduce unnecessary expenses and improve operational efficiencies. Before you follow the guidance in this article, we recommend you review the following resources:

* [Cost optimization design principles](../cost-optimization/principles.md).
* How pricing and cost management work in Azure Stack Hub compared to 
* If you are running Azure Stack Hub on-premises or at the edge, [Azure Hybrid Benefit](/windows-server/get-started/azure-hybrid-benefit) can also be used to further reduce costs when running containerized applications in those scenarios.
* ESU for workloads

When discussing cost optimization with Azure Stack Hub, it's important to distinguish between *cost of cluster resources* and *cost of workload resources*. Cluster resources are a shared responsibility between the cluster admin and their resource provider, while workload resources are the domain of a developer. Azure Stack Hub has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations**, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

For cluster cost optimization, go to the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) and select **Azure Stack Hub** from the available products. You can test different configuration and payment plans in the calculator.

### Design checklist

> [!div class="checklist"]
> - **Cluster architecture:** Use appropriate VM SKU per node pool and reserved instances where long-term capacity is expected.
> - **Cluster and workload architectures:** Use appropriate managed disk tier and size.
> - **Cluster architecture:** Review performance metrics, starting with CPU, memory, storage, and network, to identify cost optimization opportunities by cluster, nodes, and namespace.
> - **Cluster architecture:** Use cluster autoscaler to scale in when workloads are less active.

### Recommendations

Explore the following table of recommendations to optimize your Azure Stack Hub configuration for cost.

| Recommendation | Benefit |
|----------------------------------|-----------|
|**Cluster and workload architectures:** Align SKU selection and managed disk size with workload requirements.|Matching your selection to your workload demands ensures you don't pay for unneeded resources.|
|**Cluster architecture:** Select the right virtual machine instance type. |Selecting the right virtual machine instance type is critical as it directly impacts the cost of running applications on Azure Stack Hub. Choosing a high-performance instance without proper utilization can lead to wasteful spending, while choosing a powerful instance can lead to performance issues and increased downtime. To determine the right virtual machine instance type, consider workload characteristics, resource requirements, and availability needs.|
|**Cluster architecture:** Select [virtual machines based on the Arm architecture](/azure/virtual-machines/dplsv5-dpldsv5-series). |Azure Stack Hub supports [creating ARM64 Ubuntu agent nodes](/azure/Azure Stack Hub/use-multiple-node-pools#add-an-arm64-node-pool), as well as a of mix Intel and ARM architecture nodes within a cluster that can bring better performance at a lower cost.|

For more suggestions, see [Principles of the cost optimization pillar](../cost-optimization/index.yml).

### Cloud efficiency

Making workloads more [sustainable and cloud efficient](../sustainability/sustainability-get-started.md), requires combining efforts around **cost optimization**, **reducing carbon emissions**, and **optimizing energy consumption**. Optimizing the application's cost is the initial step in making workloads more sustainable.

Learn how to build sustainable and efficient Azure Stack Hub workloads, in Sustainable software engineering principles in Azure Stack Hub.

## Operational excellence

Monitoring and diagnostics are crucial. Not only can you measure performance statistics, but also use metrics troubleshoot and remediate issues quickly. We recommend you review the [Operational excellence design principles](/azure/well-architected/devops/principles) and the [Day-2 operations guide](/azure/architecture/operator-guides/Azure Stack Hub/day-2-operations-guide).

When discussing operational excellence with Azure Stack Hub, it's important to distinguish between *cluster operational excellence* and *workload operational excellence*. Cluster operations are a shared responsibility between the cluster admin and their resource provider, while workload operations are the domain of a developer. Azure Stack Hub has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]
> - **Cluster architecture:** Use a template-based deployment using Bicep, Terraform, or others. Make sure that all deployments are repeatable, traceable, and stored in a source code repo.
> - **Cluster architecture:** Build an automated process to ensure your clusters are bootstrapped with the necessary cluster-wide configurations and deployments. This is often performed using GitOps.
> - **Workload architecture:** Use a repeatable and automated deployment processes for your workload within your software development lifecycle.

### Recommendations

Explore the following table of recommendations to optimize your Azure Stack Hub configuration for operations.

| Recommendation | Benefit |
|--------|----|
|**Cluster and workload architectures:** Review [Azure Stack Hub best practices](/azure/Azure Stack Hub/best-practices) documentation.|To build and run applications successfully in Azure Stack Hub, there are key considerations to understand and implement. These areas include multi-tenancy and scheduler features, cluster, and pod security, or business continuity and disaster recovery.|
|**Cluster and workload architectures:** Review [Azure Chaos Studio](/azure/chaos-studio/chaos-studio-tutorial-Azure Stack Hub-portal).| Azure Chaos Studio can help simulate faults and trigger disaster recovery situations.|

For more suggestions, see [Principles of the operational excellence pillar](/azure/well-architected/devops/principles).

Review: Azure Advisor also makes recommendations on a subset of the items listed in the policy section below, such unsupported Azure Stack Hub versions and unconfigured diagnostic settings. Likewise, it makes workload recommendations around the use of the default namespace.

There are a numerous number of policies, and key policies related to this pillar are summarized here. For a more detailed view, see [built-in policy definitions for hybrid workloads](/azure/governance/policy/samples/built-in-policies#hybrid workloads).

#### Cluster architecture

- Azure Stack Hub version restrictions - support policy
- Prevent command invoke

#### Cluster and workload architecture

- Namespace deployment restrictions AKS Hybrid

## Performance efficiency

Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. We recommend you review the [Performance efficiency principles](/azure/well-architected/scalability/principles).

When discussing performance with Azure Stack Hub, it's important to distinguish between *cluster performance* and *workload performance*. Cluster performance is a shared responsibility between the cluster admin and their resource provider, while workload performance is the domain of a developer. Azure Stack Hub has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

### Design checklist

As you make design choices for Azure Stack Hub, review the [Performance efficiency principles](/azure/well-architected/scalability/principles).

> [!div class="checklist"]
> - **Cluster and workload architectures:** Perform and iterate on a detailed capacity plan exercise that includes SKU, autoscale settings, IP addressing, and failover considerations.
> - **Cluster architecture:** Enable [cluster autoscaler](/azure/Azure Stack Hub/

### Recommendations

Explore the following table of recommendations to optimize your Azure Stack Hub configuration for performance.

| Recommendation | Benefit |
|--------|----|
|**Cluster and workload architectures:** Develop a detailed capacity plan and continually review and revise.|After formalizing your capacity plan, it should be frequently updated by continuously observing the resource utilization of the cluster.|
|**Cluster architecture:** Enable [cluster autoscaler](/azure/Azure Stack Hub/cluster-autoscaler) to automatically adjust the number of agent nodes in response to resource constraints.|The ability to automatically scale up or down the number of nodes in your Azure Stack Hub cluster lets you run an efficient, cost-effective cluster.|

For more suggestions, see [Principles of the performance efficiency pillar](/azure/well-architected/scalability/principles).

#### Cluster and workload architecture

- VM Sizes, resource limits

## Additional resources

### Azure Architecture Center guidance

### Cloud Adoption Framework guidance

- Azure Stack Hub Landing Zone Accelerator

## Next steps

- Deploy an Azure Stack Hub cluster using the Azure CLI Quickstart: Deploy an Azure Stack Hub cluster using the Azure CLI
