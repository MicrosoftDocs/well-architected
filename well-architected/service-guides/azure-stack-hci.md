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

We assume that you understand system design principles, have working knowledge of Azure Stack HCI, and are well versed with its features. For more information, see [Azure Stack HCI overview.](/azure-stack/hci/overview)

## Prerequisites

Understanding the Well-Architected Framework pillars can help produce a high-quality, stable, and efficient hybrid cloud architecture patterns. We recommend that you review your workload by using the [Azure Well-Architected Framework Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment. Consider reviewing reference architectures that reflect these considerations in their design. We recommend starting with the [baseline architecture for an Azure Stack HCI cluster](/azure/architecture/hybrid/azure-stack-robo) and [Azure Stack HCI network deployment patterns](/azure-stack/hci/plan/choose-network-pattern) patterns. Also review the [Prepare your environment for a hybrid and multi-cloud scenario](/azure/cloud-adoption-framework/scenarios/hybrid/ready), which provides an architectural approach and references to prepare landing zone subscriptions for a scalable deployment and implementation of Azure Stack HCI.

## Reliability

In hybrid cloud deployments, we acknowledge that failures can happen, instead of trying to prevent any and all failures, the goal is to minimize the effects of a single failing component. Use the following design checklists and configuration recommendations to minimize impact of a component failure for workloads deployed on Azure Stack HCI.

When discussing reliability for Azure Stack HCI, it's important to distinguish between *cluster reliability* and *workload reliability*. Cluster reliability is the responsibility of cluster or platform administrator, while workload reliability has a dependency on the platform, it is the responsibility of application owner and/or developer to design applications to meet reliability targets. Azure Stack HCI has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each consideration is applicable to cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]
>
> - **Cluster nodes architecture:** Use the [Azure Stack HCI Solution Catalog](https://aka.ms/azurestackhcicatalog/#/catalog) to gain knowledge and understanding of latest hardware OEM innovations for Azure Stack HCI cluster deployments, consider using the Premium Solutions offerings to benefit from additional integration and turn-key deployment capabilities.
> - **Cluster nodes architecture:** Size Azure Stack HCI clusters appropriately using the workload capacity, performance and resiliency requirements as inputs. This will determine the maximum number of nodes that can be offline simultaneously, such as for planned (*maintenance*) or unplanned (*power / hardware failure*) events. As a minimum requirement, plan to "reserve 1 x physical nodes (N+1) worth of capacity" across the cluster, or N+2 for business or mission-critical use cases, to ensure cluster nodes can be drained to perform updates. Use the [Azure Stack HCI Sizer Tool](https://aka.ms/azurestackhcicatalog/#/sizer) to help during the cluster design phase.
> - **Cluster storage architecture:** Select the appropriate physical disk type(s) for the cluster nodes (*NVMe, SSD, HDD..etc*) and [fault tolerance / storage efficiency options within Storage Spaces Direct (S2D) based on your workload requirements](/azure-stack/hci/concepts/fault-tolerance). These decisions will influence the performance, capacity and resiliency capabilities when [creating S2D virtual disks / volumes](/azure-stack/hci/concepts/plan-volumes), for example using a 3-way mirror will increase reliability and performance. The [S2D Capacity Calculator Tool](https://aka.ms/s2dcalc) can help when designing your capacity management model.
> - **Cluster network architecture:** To ensure fault tolerance, it is recommended to have two or more network adapters per node which are symmetric (of the same make, model, speed, and configuration) across all nodes.This is because having multiple network adapters provides redundancy and ensures that the cluster remains operational even if one of the network adapters fails.
> - **Cluster network architecture:** To ensure resiliency, it is recommended to have two TOR  switches and ensure the network adapters for management, compute, storage are connected to the TOR switches in fault tolerant mode.
> - **Workload architecture:** The reliability design of the workloads varies depending on the type of workloads (VM, AKS, AVD, etc.,) that is deployed on Azure Stack HCI. Refer to the respective workload specific guidance when designing the reliability of each workload type.
> - **Workload architecture:** Backup
> - **Workload architecture:** Disaster Recovery

### Azure Stack HCI configuration recommendations

Explore the following table of recommendations to optimize your Azure Stack HCI configuration for Reliability.

| Recommendation | Benefit |
|--------|----|
|**Cluster architecture:** |[The recommendation is to have 1 x disks "worth of space" unallocated in the storage pool](/azure-stack/hci/concepts/plan-volumes#reserve-capacity), per node (*up to eight nodes / disks max*), to sustain disk failures. |
|**Cluster architecture (Server and Storage):** | Make sure that the physical server and storage hardware used to deploy an Azure Stack HCI cluster meets the requirements as listed in the [HCI deployment prerequisite](/azure-stack/hci/deploy/deployment-prerequisites#server-and-storage-requirements) and all the servers are of same make, model, manufacturer, have the same network adapters, and have the same number and type of storage drives.|
|**Cluster architecture (Storage):** | Ensure setting up a cluster witness (cloud or file share) for clusters with two, three or four nodes. In 23H2 and above, cloud witness on Azure using storage account is provisioned automatically as part of the cluster deployment based on the parameters chosen by the user in the Azure Portal or ARM template. |
|**Cluster architecture (Network):** |Choose the right network pattern with resilient infrastructure for TOR switches, Network Adapters, Switch vs. Switchless, Converged vs. Non-converged using the guidance [Azure Stack HCI deployment network reference patterns](/azure-stack/hci/plan/choose-network-pattern)|
|**Cluster architecture (Storage Volume):** | Ensure choosing the resiliency types: Volumes in Storage Spaces Direct provide resiliency to protect against hardware problems, such as drive or server failures, and to enable continuous availability throughout server maintenance, such as software updates. Which resiliency type to use depends on the needs of your workload. Here's a table that summarizes which workloads are a good fit for each resiliency type, as well as the performance and storage efficiency of each resiliency type. [Choosing the Resiliency Types](/azure-stack/hci/concepts/plan-volumes#choosing-the-resiliency-type)|
|**Cluster architecture (Workload):** | Make all things redundant: For business/mission-critical workloads, it is required to deploy multiple instances of your services and/or applications using multiple virtual machines. Consider implementing [workload anti-affinity rules](/azure-stack/hci/manage/vm-affinity#anti-affinity-rule-examples) to ensure virtual machines hosting multiple instances of the same service run on separate physical hosts, to increase resiliency in the event of an unplanned outage of a single physical node.

For more suggestions, see [Principles of the reliability pillar](/azure/well-architected/resiliency/principles).

### Azure Policy

Azure Stack HCI offers some built-in Azure Policies that apply to both the Azure resource like typical Azure Policies and, using the Azure Policy add-on for Azure Stack HCI, also within the cluster. There are a numerous number of policies, and key policies related to this pillar are summarized here. For a more detailed view, see [built-in policy definitions for Azure Stack HCI](/azure/governance/policy/samples/built-in-policies#azurestackhci).

#### Cluster and workload architecture

In addition to the built-in Azure Policy definitions, custom policies can be created for both the Azure Stack HCI resource and for the Azure Policy add-on for hybrid workloads. This allows you to add additional reliability constraints you'd like to enforce in your cluster and workload architecture.

## Security

<<<<<<< HEAD
Security is one of the most important aspects of any architecture. To explore how Azure Stack HCI can bolster the security of your application workload, we recommend you review the [Security design principles](../security/principles.md).
=======
AKS Hybird - section below needs complete review....

Security is one of the most important aspects of any architecture. To explore how Azure Stack HCI can bolster the security of your application workload, we recommend you review the [Security design principles](../security/principles.md). If your Azure Stack HCI cluster needs to be designed to run a sensitive workload that meets the regulatory requirements of the Payment Card Industry Data Security Standard (PCI-DSS 3.2.1), review [Azure Stack HCI regulated cluster for PCI-DSS 3.2.1](/azure/architecture/reference-architectures/containers/AzureStackHCI-pci/AzureStackHCI-pci-intro).

To learn about DoD Impact Level 5 (IL5) support and requirements with Azure Stack HCI, review [Azure Government IL5 isolation requirements](/azure/azure-government/documentation-government-impact-level-5#azure-hybrid-workloads-service).
>>>>>>> 72770491 (HCI + Hub updates)

All new installations of release 23H2 and above starts with a secure-by-default strategy. The Azure Stack HCI servers are deployed with a tailored security baseline coupled with a security drift control mechanism and a set of well-known security features enabled by default.
  
When discussing security with Azure Stack HCI, it's important to distinguish between *cluster security* and *workload security*. Cluster security is a shared responsibility between the cluster admin and their resource provider, while workload security is the domain of a developer. Azure Stack HCI has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]
>
> - **Cluster architecture:** While deploying the Azure Stack HCI clusters, set the security level of your Azure Stack HCI system's infrastructure using the recommended security settings such as Security defaults, Windows Defender Credential Guard, BitLocker for the OS volume, BitLocker for data volume, Signing for external SMB traffic that provides the highest level of security, or customize these settings to match your security needs.
> - **Cluster architecture:** Use Microsoft Defender for Cloud to audit and remediate your security posture of Azure Stack HCI clusters, nodes and workloads together in one view.
> - **Cluster architecture:** Use Microsoft Defender for Servers deployed in AKS Hybrid with [Azure Sentinel](/azure/sentinel/overview) to detect and quickly respond to threats across your cluster and workloads running on them.
> - **Workload architecture:** Use VLAN based network-isolation to deploy workloads (VMs) that needs to be isolated from each other across the different VLAN networks. Ensure each VLAN is configured and reachable from the management network for the hosts to communicate with the VLAN networks through the ToR (Top-of-Rack) switches or gateways.  
> - **Workload architecture:** Use Trusted launch for virtual machines that protects against persistent and standard attacks on Gen 2 virtual machines with configurable features like secure boot and virtual Trusted Platform Module (vTPM).
> - **Workload architecture:** Use Azure Policy to re-use the builtin policies such as security baselines for Windows and Linux workloads or create new custom policies in auditing the security settings and assessing the compliance state of the target workloads running on Azure Stack HCI.

### Recommendations

Explore the following table of recommendations to optimize your Azure Stack HCI configuration for security.

|Recommendation|Benefit|
|----------------------------------|-----------|
|**Cluster architecture:** Use Secure by default|Using Secure by default maintains the security default on each server, helping to protect against changes or drifts that are automatically corrected to maintain the intended posture of the Azure Stack HCI system. Review [Security baseline and drift control](/azure-stack/hci/manage/manage-secure-baseline) for more information.|
|**Cluster architecture:** Windows Defender Credential Guard|Uses virtualization-based security to isolate secrets from credential-theft attacks. Unauthorized access to these secrets can lead to credential theft attacks like pass the hash and pass the ticket. Review [Credential Guard Overview](/windows/security/identity-protection/credential-guard/) for more information.|
|**Cluster architecture:** Windows Defender Application Control (WDAC) |Controls which drivers, applications and the code that are allowed to run directly on each server. Review [Managing Windows Defender Application Control](/azure-stack/hci/manage/manage-wdac) for more information. |
|**Cluster architecture:** BitLocker for the OS volume|Encrypts the OS volume on each server. Review [BitLocker Overview](/windows/security/operating-system-security/data-protection/bitlocker/) for more information. |
|**Cluster architecture:** BitLocker for data volumes|Encrypts cluster shared volumes (CSVs) created on the Azure Stack HCI system using XTS-AES 256-bit encryption. This is the recommended default setting which can be turned on during the cluster deployment in all the data volumes. Review [Volume encryption via BitLocker](/azure-stack/hci/concepts/security-features#volume-encryption-via-bitlocker) for more information.|
|**Cluster architecture:** Signing for external SMB traffic|Signs SMB traffic between this system and others to help prevent relay attacks. Review [Overview of Server Message Block signing](/troubleshoot/windows-server/networking/overview-server-message-block-signing) for more information. |
|**Cluster architecture:** SMB Encryption for in-cluster traffic (Optional)|Encrypts traffic between servers in the cluster (on your storage network). It is optional to turn on this based on the additional security needs for your environment. Review [SMB Encryption](/windows-server/storage/file-server/smb-security#smb-encryption) for more information. |

For more information on the security features introduced in 23H2, see [Review Security Features](/azure-stack/hci/concepts/security-features).

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
* Azure Stack HCI is priced on a per physical core basis on your on-premises servers. If you are a Windows Server Datacenter customer with active Software Assurance, you may also choose to exchange core licenses to activate Azure Hybrid Benefit, which waives the Azure Stack HCI host service fee and Windows Server subscription. Review [Azure Hybrid Benefit](/windows-server/get-started/azure-hybrid-benefit) for more information.

When discussing cost optimization with Azure Stack HCI, it's important to distinguish between *cost of cluster resources* and *cost of workload resources*. Cluster resources are a shared responsibility between the cluster admin and their resource provider, while workload resources are the domain of a developer. Azure Stack HCI has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations**, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

For cluster cost optimization, go to the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) and select **Azure Stack HCI** from the available products. You can test different configuration and payment plans in the calculator.

### Design checklist

> [!div class="checklist"]
> - **Cluster architecture:** Use [Azure Stack Pricing](/pricing/details/azure-stack/hci/) page in exploring the pricing options available to purchase Azure Stack HCI for on-premises servers.
> - **Cluster architecture:** Use Azure Stack HCI free trial offer for the first 60 days after registration for PoCs and validation purposes.
> - **Cluster architectures:** Turn on Azure Benefits on Azure Stack HCI to use some of the Azure exclusive workloads such as Windows Server Datacenter (Azure Edition), Extended Security Update (ESUs), Azure Policy Guest Configuration and Azure Virtual Desktop with no extra licensing cost. Review [Azure Benefits on Azure Stack HCI](/azure-stack/hci/manage/azure-benefits?tabs=wac) for more information.

### Recommendations

Explore the following table of recommendations to optimize your Azure Stack HCI configuration for cost.

| Recommendation | Benefit |
|----------------------------------|-----------|
|**Cluster architectures:** Azure Hybrid Benefit |Activate Azure Hybrid Benefit in the Azure Portal for the Azure Stack HCI cluster resource under the Configuration if you have Windows Server Datacenter license with active Software Assurance and would like to exchange those licenses to get Azure Stack HCI and Windows Server Subscription at no additional cost. |
|**Cluster architecture:** Windows Server subscription add-on |For customers that do not have valid Azure Hybrid Benefit licenses, you can consider subscribing to Windows Server guest licenses through Azure. You can purchase this in the Azure portal for the Azure Stack HCI cluster resource under the Configuration. By enabling this in the Azure Stack HCI cluster, you will be charged for the total number of physical cores in your cluster. |
|**Cluster architecture:** Azure verification for VMs - Enabled by default. |Ensure Azure verification for VMs is On under the Configuration for the Azure Stack HCI cluster resource in the Azure Portal. This will allow using the worklaods and services that are available only on Azure, such as Extended Security Updates and others. |

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
