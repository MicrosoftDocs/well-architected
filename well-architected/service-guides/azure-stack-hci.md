---
title: Azure Well-Architected Framework review for Azure Stack HCI
description: Provides a template for a Well-Architected Framework (WAF) article that is specific to Azure Stack HCI.
author: neilbird
ms.author: nebird
ms.topic: conceptual
ms.custom:
products: azure-stack-hci
---

# Azure Well-Architected Framework review - Azure Stack HCI

This article provides architectural best practices for Azure Stack HCI. The guidance is based on the five pillars of architecture excellence:

- Reliability
- Security
- Cost optimization
- Operational excellence
- Performance efficiency

We assume that you understand system design principles, have working knowledge of Azure Stack HCI, and are well versed with its features. For more information, see [Azure Stack HCI](/azure-stack/hci/overview).

## Prerequisites

Understanding the Well-Architected Framework pillars can help produce a high-quality, stable, and efficient cloud architecture. We recommend that you review your workload by using the [Azure Well-Architected Framework Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.

For context, consider reviewing a reference architecture that reflects these considerations in its design. We recommend that you start with the [baseline architecture for an Azure Stack HCI cluster](/azure/architecture/hybrid/azure-stack-robo) and [Azure Stack HCI network deployment patterns](/azure-stack/hci/plan/choose-network-pattern). Also review the [Prepare your environment for a hybrid and multi-cloud scenario](/azure/cloud-adoption-framework/scenarios/hybrid/ready), which provides an architectural approach and reference implementation to prepare landing zone subscriptions for a scalable Azure Stack HCI cluster.

## Reliability

Azure Stack HCI has considerations and recommendations for both of these roles. In the hybrid cloud, we acknowledge that failures happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component. Use the following information to minimize failed instances.
When discussing reliability with Azure Stack HCI, it's important to distinguish between *cluster reliability* and *workload reliability*. Cluster reliability is the responsibility of cluster admin and the resource provider, while workload reliability is the domain of a operator or developer. Azure Stack HCI has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each consideration is applicable to cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]
> - **Cluster architecture (Server):** Reserve additional hosts for emergency scenarios (e.g. maintenance, one node is down etc.). The recommendation is to have N + 1 nodes for the cluster.
> - **Cluster architecture (Storage):** Estimate the number of failures that can occur simultaneously without compromising availability and data. The recommendation is to have 2 additional disks to sustain disk failures.
> - **Cluster architecture (Storage):** Choose the right resiliency preference (Performance, Capacity) for storage based on the workload. This choice decides what type of mirroring will be used for the CSV filesystem.
> - **Cluster architecture (Host Network):** To ensure fault tolerance, it is recommended to have two or more network adapters per node which are symmetric (of the same make, model, speed, and configuration) across all nodes.This is because having multiple network adapters provides redundancy and ensures that the cluster remains operational even if one of the network adapters fails.
> - **Cluster architecture (Physical Switch Network):** To ensure resiliency, it is recommended to have two TOR  switches and ensure the network adapters for management, compute, storage are connected to the TOR switches in fault tolerant mode.
> - **Workload architecture:** The reliability design of the workloads varies depending on the type of workloads (VM, AKS, AVD, etc.,) that is deployed on Azure Stack HCI. Refer to the respective workload specific guidance in designing the reliability for each of the workloads.

### Azure Stack HCI configuration recommendations

Explore the following table of recommendations to optimize your Azure Stack HCI configuration for Reliability.

| Recommendation | Benefit |
|--------|----|
|**Cluster architecture (Server):** | Size the cluster with the expected maximum number of nodes that might go down during planned or unplanned maintenance cycles. As a best practice always account for 1 or 2 extra nodes that ensures the cluster is resilient and highly available for node failures both during the planned and unplanned maintenance cycles. For better performance and reliability, choose the recommended hardware from the [HCI Solution Catalog](https://azurestackhcisolutions.azure.microsoft.com/) and do the right sizing of the cluster using the [HCI Sizer Tool](https://azurestackhcisolutions.azure.microsoft.com/#/sizer)|
|**Cluster architecture (Server and Storage):** | Make sure that the physical server and storage hardware used to deploy an Azure Stack HCI cluster meets the requirements as listed in the [HCI deployment prerequisite](/azure-stack/hci/deploy/deployment-prerequisites#server-and-storage-requirements) and all the servers are of same make, model, manufacturer, have the same network adapters, and have the same number and type of storage drives.|
|**Cluster architecture (Storage):** | Ensure setting up a cluster witness (cloud or file share) for clusters with two, three or four nodes. In 23H2 and above, cloud witness on Azure using storage account is provisioned automatically as part of the cluster deployment based on the parameters chosen by the user in the Azure Portal or ARM template. |
|**Cluster architecture (Network):** |Choose the right network pattern with resilient infrastructure for TOR switches, Network Adapters, Switch vs. Switchless, Converged vs. Non-converged using the guidance [Azure Stack HCI deployment network reference patterns](/azure-stack/hci/plan/choose-network-pattern)|
|**Cluster architecture (Storage Volume):** | Ensure choosing the resiliency types: Volumes in Storage Spaces Direct provide resiliency to protect against hardware problems, such as drive or server failures, and to enable continuous availability throughout server maintenance, such as software updates. Which resiliency type to use depends on the needs of your workload. Here's a table that summarizes which workloads are a good fit for each resiliency type, as well as the performance and storage efficiency of each resiliency type. [Choosing the Resiliency Types](/azure-stack/hci/concepts/plan-volumes#choosing-the-resiliency-type)|

For more suggestions, see [Principles of the reliability pillar](/azure/well-architected/resiliency/principles).

### Azure Policy

Azure Stack HCI offers some built-in Azure Policies that apply to both the Azure resource like typical Azure Policies and, using the Azure Policy add-on for Azure Stack HCI, also within the cluster. There are a numerous number of policies, and key policies related to this pillar are summarized here. For a more detailed view, see [built-in policy definitions for Azure Stack HCI](/azure/governance/policy/samples/built-in-policies#azurestackhci).

#### Cluster and workload architecture

In addition to the built-in Azure Policy definitions, custom policies can be created for both the Azure Stack HCI resource and for the Azure Policy add-on for hybrid workloads. This allows you to add additional reliability constraints you'd like to enforce in your cluster and workload architecture.

## Security

AKS Hybird - section below needs complete review....

Security is one of the most important aspects of any architecture. To explore how Azure Stack HCI can bolster the security of your application workload, we recommend you review the [Security design principles](../security/principles.md). If your Azure Stack HCI cluster needs to be designed to run a sensitive workload that meets the regulatory requirements of the Payment Card Industry Data Security Standard (PCI-DSS 3.2.1), review [Azure Stack HCI regulated cluster for PCI-DSS 3.2.1](/azure/architecture/reference-architectures/containers/Azure Stack HCI-pci/Azure Stack HCI-pci-intro).

To learn about DoD Impact Level 5 (IL5) support and requirements with Azure Stack HCI, review [Azure Government IL5 isolation requirements](/azure/azure-government/documentation-government-impact-level-5#azure-hybrid workloads-service).

When discussing security with Azure Stack HCI, it's important to distinguish between *cluster security* and *workload security*. Cluster security is a shared responsibility between the cluster admin and their resource provider, while workload security is the domain of a developer. Azure Stack HCI has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]
>
> - **Cluster architecture:** Use [Managed Identities](/azure/Azure Stack HCI/use-managed-identity) to avoid managing and rotating service principles.
> - **Cluster architecture:** Use 
> - **Cluster architecture:** Use Microsoft Defender for containers deployed in AKS Hybrid with [Azure Sentinel](/azure/sentinel/overview) to detect and quickly respond to threats across your cluster and workloads running on them.
> - **Cluster architecture:** Deploy a 
> - **Workload architecture:** Use a 
> - **Workload architecture:** Ensure your CI/CID pipeline is hardened with container-aware scanning.

### Recommendations

Explore the following table of recommendations to optimize your Azure Stack HCI configuration for security.

|Recommendation|Benefit|
|----------------------------------|-----------|
|**Cluster architecture:** Use Microsoft Entra integration.|Using Microsoft Entra ID centralizes the identity management component. Any change in user account or group status is automatically updated in access to the Azure Stack HCI cluster. The developers and application owners of your hybrid workloads cluster need access to different resources.|
|**Cluster architecture:** Authenticate with Microsoft Entra ID to Azure Container Registry.|Azure Stack HCI and Microsoft Entra ID enables authentication with Azure Container Registry without the use of `imagePullSecrets` secrets. Review [Authenticate with Azure Container Registry from Azure Stack HCI](/azure/Azure Stack HCI/cluster-container-registry-integration?tabs=azure-cli) for more information.|
|**Cluster architecture:** Secure network traffic to your API server with [private Azure Stack HCI cluster](/azure/Azure Stack HCI/private-clusters).|By default, network traffic between your node pools and the API server travels the Microsoft backbone network; by using a private cluster, you can ensure network traffic to your API server remains on the private network only.|
|**Cluster architecture:** For non-private Azure Stack HCI clusters, use API server authorized IP ranges.|When using public clusters, you can still limit the traffic that can reach your clusters API server by using the authorized IP range feature. Include sources like the public IPs of your deployment build agents, operations management, and node pools' egress point (such as Azure Firewall).|
|**Cluster architecture:** Protect the API server with Microsoft Entra RBAC.|Securing access to the hybrid workloads API Server is one of the most important things you can do to secure your cluster. Integrate hybrid workloads role-based access control (RBAC) with Microsoft Entra ID to control access to the API server. [Disable local accounts](/azure/Azure Stack HCI/managed-aad#disable-local-accounts) to enforce all cluster access using Microsoft Entra ID-based identities.|

For more suggestions, see [Principles of the security pillar](/azure/well-architected/security/security-principles).

## Needs review:
 Azure Advisor helps ensure and improve Azure Stack HCI. It makes recommendations on a subset of the items listed in the policy section below, such as clusters without RBAC configured, missing Microsoft Defender configuration, unrestricted network access to the API Server. Likewise, it makes workload recommendations for some of the pod security initiative items. Review the [recommendations](/azure/advisor/advisor-security-recommendations).

### Policy definitions

Azure Policy offers various built-in policy definitions that apply to both the Azure resource and Azure Stack HCI like standard policy definitions, 

There are a numerous number of policies, and key policies related to this pillar are summarized here. For a more detailed view, see [built-in policy definitions for hybrid workloads](/azure/governance/policy/samples/built-in-policies#hybrid workloads).

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
* How pricing and cost management work in Azure Stack HCI compared to 
* If you are running Azure Stack HCI on-premises or at the edge, [Azure Hybrid Benefit](/windows-server/get-started/azure-hybrid-benefit) can also be used to further reduce costs when running containerized applications in those scenarios.
* ESU for workloads

When discussing cost optimization with Azure Stack HCI, it's important to distinguish between *cost of cluster resources* and *cost of workload resources*. Cluster resources are a shared responsibility between the cluster admin and their resource provider, while workload resources are the domain of a developer. Azure Stack HCI has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations**, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

For cluster cost optimization, go to the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) and select **Azure Stack HCI** from the available products. You can test different configuration and payment plans in the calculator.

### Design checklist

> [!div class="checklist"]
> - **Cluster architecture:** Use appropriate VM SKU per node pool and reserved instances where long-term capacity is expected.
> - **Cluster and workload architectures:** Use appropriate managed disk tier and size.
> - **Cluster architecture:** Review performance metrics, starting with CPU, memory, storage, and network, to identify cost optimization opportunities by cluster, nodes, and namespace.
> - **Cluster architecture:** Use cluster autoscaler to scale in when workloads are less active.

### Recommendations

Explore the following table of recommendations to optimize your Azure Stack HCI configuration for cost.

| Recommendation | Benefit |
|----------------------------------|-----------|
|**Cluster and workload architectures:** Align SKU selection and managed disk size with workload requirements.|Matching your selection to your workload demands ensures you don't pay for unneeded resources.|
|**Cluster architecture:** Select the right virtual machine instance type. |Selecting the right virtual machine instance type is critical as it directly impacts the cost of running applications on Azure Stack HCI. Choosing a high-performance instance without proper utilization can lead to wasteful spending, while choosing a powerful instance can lead to performance issues and increased downtime. To determine the right virtual machine instance type, consider workload characteristics, resource requirements, and availability needs.|
|**Cluster architecture:** Select [virtual machines based on the Arm architecture](/azure/virtual-machines/dplsv5-dpldsv5-series). |Azure Stack HCI supports [creating ARM64 Ubuntu agent nodes](/azure/Azure Stack HCI/use-multiple-node-pools#add-an-arm64-node-pool), as well as a of mix Intel and ARM architecture nodes within a cluster that can bring better performance at a lower cost.|

For more suggestions, see [Principles of the cost optimization pillar](../cost-optimization/index.yml).

### Policy definitions

While there are no built-in policies that are related to cost optimization, custom policies can be created for both the Azure Stack HCI resource and for the Azure Policy add-on for hybrid workloads. This allows you to add additional cost optimization constraints you'd like to enforce in your cluster and workload architecture.

### Cloud efficiency

Making workloads more [sustainable and cloud efficient](../sustainability/sustainability-get-started.md), requires combining efforts around **cost optimization**, **reducing carbon emissions**, and **optimizing energy consumption**. Optimizing the application's cost is the initial step in making workloads more sustainable.

Learn how to build sustainable and efficient Azure Stack HCI workloads, in Sustainable software engineering principles in Azure Stack HCI.

## Operational excellence

Monitoring and diagnostics are crucial. Not only can you measure performance statistics, but also use metrics troubleshoot and remediate issues quickly. We recommend you review the [Operational excellence design principles](/azure/well-architected/devops/principles) and the [Day-2 operations guide](/azure/architecture/operator-guides/Azure Stack HCI/day-2-operations-guide).

When discussing operational excellence with Azure Stack HCI, it's important to distinguish between *cluster operational excellence* and *workload operational excellence*. Cluster operations are a shared responsibility between the cluster admin and their resource provider, while workload operations are the domain of a developer. Azure Stack HCI has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]
> - **Cluster architecture:** Use a template-based deployment using Bicep, Terraform, or others. Make sure that all deployments are repeatable, traceable, and stored in a source code repo.
> - **Cluster architecture:** Build an automated process to ensure your clusters are bootstrapped with the necessary cluster-wide configurations and deployments. This is often performed using GitOps.
> - **Workload architecture:** Use a repeatable and automated deployment processes for your workload within your software development lifecycle.

### Recommendations

Explore the following table of recommendations to optimize your Azure Stack HCI configuration for operations.

| Recommendation | Benefit |
|--------|----|
|**Cluster and workload architectures:** Review [Azure Stack HCI best practices](/azure/Azure Stack HCI/best-practices) documentation.|To build and run applications successfully in Azure Stack HCI, there are key considerations to understand and implement. These areas include multi-tenancy and scheduler features, cluster, and pod security, or business continuity and disaster recovery.|
|**Cluster and workload architectures:** Review [Azure Chaos Studio](/azure/chaos-studio/chaos-studio-tutorial-Azure Stack HCI-portal).| Azure Chaos Studio can help simulate faults and trigger disaster recovery situations.|

For more suggestions, see [Principles of the operational excellence pillar](/azure/well-architected/devops/principles).

Review: Azure Advisor also makes recommendations on a subset of the items listed in the policy section below, such unsupported Azure Stack HCI versions and unconfigured diagnostic settings. Likewise, it makes workload recommendations around the use of the default namespace.

### Policy definitions

Review: Azure Policy offers various built-in policy definitions that apply to both the Azure resource and Azure Stack HCI like standard policy definitions, and using the Azure Policy add-on for hybrid workloads, also within the cluster. Many of the Azure resource policies come in both *Audit/Deny*, but also in a *Deploy If Not Exists* variant.

There are a numerous number of policies, and key policies related to this pillar are summarized here. For a more detailed view, see [built-in policy definitions for hybrid workloads](/azure/governance/policy/samples/built-in-policies#hybrid workloads).

#### Cluster architecture

- Azure Policy add-on for hybrid workloads
- GitOps configuration policies
- Diagnostics settings policies
- Azure Stack HCI version restrictions - support policy
- Prevent command invoke

#### Cluster and workload architecture

- Namespace deployment restrictions AKS Hybrid

In addition to the built-in policies, custom policies can be created for both the Azure Stack HCI resource and for the Azure Policy add-on for hybrid workloads. This allows you to add additional security constraints you'd like to enforce in your cluster and workload architecture.

## Performance efficiency

Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. We recommend you review the [Performance efficiency principles](/azure/well-architected/scalability/principles).

When discussing performance with Azure Stack HCI, it's important to distinguish between *cluster performance* and *workload performance*. Cluster performance is a shared responsibility between the cluster admin and their resource provider, while workload performance is the domain of a developer. Azure Stack HCI has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

### Design checklist

As you make design choices for Azure Stack HCI, review the [Performance efficiency principles](/azure/well-architected/scalability/principles).

> [!div class="checklist"]
> - **Cluster and workload architectures:** Perform and iterate on a detailed capacity plan exercise that includes SKU, autoscale settings, IP addressing, and failover considerations.
> - **Cluster architecture:** Enable [cluster autoscaler](/azure/Azure Stack HCI/

### Recommendations

Explore the following table of recommendations to optimize your Azure Stack HCI configuration for performance.

| Recommendation | Benefit |
|--------|----|
|**Cluster and workload architectures:** Develop a detailed capacity plan and continually review and revise.|After formalizing your capacity plan, it should be frequently updated by continuously observing the resource utilization of the cluster.|
|**Cluster architecture:** Enable [cluster autoscaler](/azure/Azure Stack HCI/cluster-autoscaler) to automatically adjust the number of agent nodes in response to resource constraints.|The ability to automatically scale up or down the number of nodes in your Azure Stack HCI cluster lets you run an efficient, cost-effective cluster.|

For more suggestions, see [Principles of the performance efficiency pillar](/azure/well-architected/scalability/principles).

### Policy definitions

Review: Azure Policy offers various built-in policy definitions that apply to both the Azure resource and Azure Stack HCI like standard policy definitions, and using the Azure Policy add-on for hybrid workloads, also within the cluster. Many of the Azure resource policies come in both *Audit/Deny*, but also in a *Deploy If Not Exists* variant.

Review: There are a numerous number of policies, and key policies related to this pillar are summarized here. For a more detailed view, see [built-in policy definitions for hybrid workloads](/azure/governance/policy/samples/built-in-policies#hybrid workloads).

#### Cluster and workload architecture

- CPU and memory resource limits

In addition to the built-in policies, custom policies can be created for both the Azure Stack HCI resource and for the Azure Policy add-on for hybrid workloads. This allows you to add additional security constraints you'd like to enforce in your cluster and workload architecture.

## Additional resources

### Azure Architecture Center guidance

### Cloud Adoption Framework guidance

- [Azure Stack HCI Landing Zone Accelerator](/azure/cloud-adoption-framework/scenarios/hybrid/enterprise-scale-landing-zone/)

## Next steps

- Deploy an Azure Stack HCI cluster using the Azure CLI Quickstart: Deploy an Azure Stack HCI cluster using the Azure CLI
