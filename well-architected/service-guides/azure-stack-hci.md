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

In hybrid cloud deployments, we acknowledge that failures happen, instead of trying to prevent any and all failures, the goal is to minimize the effects of a single failing component. Use the following design checklists and configuration recommendations to minimize the impact of a component failure for workloads deployed on Azure Stack HCI.

When discussing reliability for Azure Stack HCI, it's important to distinguish between *cluster reliability* and *workload reliability*. Cluster reliability is the responsibility of cluster or platform administrators, while workload reliability has a dependency on the platform, it is the responsibility of the application owners and/or developers to design the application(s) that can deliver the defined reliability targets. These roles and responsibilities can be owned by separate teams or individuals, or belong to a single team, either way the recommendations and considerations below can be used to increase the reliability of workloads deployed on Azure Stack HCI.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each consideration is applicable to cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]
>
> - **Cluster nodes architecture:** Use the [Azure Stack HCI Solution Catalog](https://aka.ms/azurestackhcicatalog/#/catalog) to gain knowledge and understanding of latest hardware OEM innovations for Azure Stack HCI cluster deployments, consider using Premium Solutions to benefit from additional integration and turn-key deployment capabilities.
> - **Cluster nodes architecture:** Use the [Azure Stack HCI Sizer Tool](https://aka.ms/azurestackhcicatalog/#/sizer) during the cluster design (*pre-deployment*) phase. It is important to size Azure Stack HCI clusters appropriately using the workload capacity, performance and resiliency requirements as inputs. This will determine the maximum number of nodes that can be offline simultaneously, such as for planned (*maintenance*) or unplanned (*power / hardware failure*) events. As a minimum requirement, plan to "reserve 1 x physical nodes (N+1) worth of capacity" across the cluster, or N+2 nodes for business or mission-critical use cases, to ensure cluster nodes can be drained to perform updates.
> - **Cluster architecture:** Use the [Environmental Checker tool in standalone mode](/azure-stack/hci/manage/use-environment-checker) to assess the readiness of the target environment prior to deploying an Azure Stack HCI solution. This will validate the required connectivity, hardware, Active Directory, network and Arc integration prerequisites are configured correctly.
> - **Cluster storage architecture:** Select appropriate physical disk type(s) for the cluster nodes (*NVMe, SSD, HDD..etc*) and [fault tolerance / storage efficiency options](/azure-stack/hci/concepts/fault-tolerance) within Storage Spaces Direct (S2D) based on your workload requirements. These decisions influence the performance, capacity and resiliency capabilities when [creating S2D virtual disks / volumes](/azure-stack/hci/concepts/plan-volumes), for example a 3-way mirror can increase reliability and performance. Use the [S2D Capacity Calculator Tool](https://aka.ms/s2dcalc) to help plan storage using your workload capacity requirements.
> - **Cluster network architecture:** [Review the HCI network reference patterns](/azure-stack/hci/plan/network-patterns-overview) and select the most appropriate design based on your requirements. Multi-node switched deployments provide increased fault tolerance when [deployed with two Top of Rack (ToR) switches per HCI cluster](/azure-stack/hci/concepts/host-network-requirements#storage-traffic-models) as network adapters for management, compute and storage networks are connected to two ToR switches, to provide resiliency and minimal downtime during switch servicing operations.
> - **Workload architecture high availability:** From a conceptual perspective, consider an Azure Stack HCI cluster [a logical "*availability zone*"](/azure/well-architected/reliability/regions-availability-zones) to host and run workloads. For business or mission-critical use cases, [to provide redundancy deploy multiple instances of your workloads across two or more separate Azure Stack HCI clusters](/azure/well-architected/reliability/redundancy#key-design-strategies). Use application high availability patterns and practices, such as active / passive with synchronous or a-synchronous data replication and external network load balancers to direct user requests across multiple HCI clusters, ideally deployed in separate datacenter locations.
> - **Workload architecture disaster recovery:** For business or mission-critical use cases implement a [structured, tested and documented disaster recovery (DR) capability](/azure/well-architected/reliability/disaster-recovery) configured based on your workload RPO and RTO targets. This provides business continuity in the event of an unplanned outage or disaster, such as failure of a single datacenter. It is possible to use Azure Site Recovery to protect virtual machines running on Azure Stack HCI. Review [Protect VM workloads with Azure Site Recovery on Azure Stack HCI (preview)](/azure-stack/hci/manage/azure-site-recovery) for more information.
> - **Workload architecture backup:** Ensure workload backups are configured and tested regularly based on your workload data recovery / retention requirements, including the file and folder level restore capabilities. [Azure Backup can be used to enable host and/or guest level backups of Azure Stack HCI](/azure/backup/back-up-azure-stack-hyperconverged-infrastructure-virtual-machines), or review data protection solutions available from Backup ISV partners.
> - **Cluster + workload architecture:** Reliability and resiliency design requirements are application and workload type specific, for example [Arc Virtual Machines (VMs)](/azure-stack/hci/manage/create-arc-virtual-machines), [AKS enabled by Arc](/azure/aks/hybrid/scale-requirements), and [Azure Virtual Desktop (AVD)](/azure/virtual-desktop/azure-stack-hci-overview), when deploying these resource types on Azure Stack HCI, refer to the respective workload guidance to design solutions to meet your workload reliability targets.

### Azure Stack HCI configuration recommendations

Explore the following table of recommendations to optimize your Azure Stack HCI configuration for Reliability.

| Recommendation | Benefit |
|--------|----|
|**Cluster storage architecture:** |Reserve the equivalent of ["one capacity disk worth of space per node", (*up to four nodes*) within the S2D storage pool](/azure-stack/hci/concepts/plan-volumes#reserve-capacity), this unallocated space can be used to repair "in-place" when a physical disk fails, which improves data resilience and performance.|
|**Cluster architecture:** |Hardware used to deploy an Azure Stack HCI cluster must meet the requirements listed in [HCI deployment prerequisites](/azure-stack/hci/deploy/deployment-prerequisites#server-and-storage-requirements), all physical nodes  must be homogenous, same manufacturer, make, model and network adapters, and have a consistent number and type of storage devices.|
|**Cluster network architecture** |Ensure the cluster is implemented using one of the [Validated network topologies](/azure-stack/hci/deploy/deployment-introduction#validated-network-topologies) and that cluster nodes have network access to the list of [required outbound https endpoints](/azure-stack/hci/concepts/firewall-requirements) for Azure Stack HCI.|
|**Cluster architecture** |In 23H2 and later, a [cloud witness is provisioned automatically using Azure storage](/azure-stack/hci/manage/witness), this is part of the cloud based cluster deployment process control by using Azure Portal or an ARM template.|
|**Cluster network architecture:** |Choose the right network infrastructure pattern and resilience capabilities, such as ToR Switch vs. Switch-less, Network Adapters, Converged vs. Non-converged using the guidance available: [Azure Stack HCI deployment network reference patterns](/azure-stack/hci/plan/choose-network-pattern)|
|**Cluster storage architecture:** |When creating Volumes (*virtual disks*) in Storage Spaces Direct, select the appropriate [resiliency types](/azure-stack/hci/concepts/plan-volumes#choosing-the-resiliency-type) using the number of physical nodes in the cluster and the workload resiliency and performance requirements as inputs.|
|**Workload architecture:** |Make all things redundant: For business or mission-critical workloads, it is required to deploy multiple instances of your services and/or applications using multiple virtual machines. Consider implementing [workload anti-affinity rules](/azure-stack/hci/manage/vm-affinity#anti-affinity-rule-examples) to ensure VMs that host multiple instances of the same service run on separate physical hosts, this increases resiliency in the event of an unplanned outage of a single physical node.

For more suggestions, see [Principles of the reliability pillar](/azure/well-architected/resiliency/principles).

### Azure Policy

Azure Policy is a service that helps you enforce organizational standards and assess compliance at scale. You can use Azure Policy to create and assign policies to your Azure Stack HCI hosts and virtual machines, and monitor their compliance state from Azure portal. Azure Policy also integrates with Azure Arc, which enables you to manage your Azure Stack HCI resources as if they were Azure resources.

Azure Stack HCI offers some built-in Azure Policies that apply to both the Azure resource like typical Azure Policies and, using the Azure Policy add-on for Azure Stack HCI, also within the cluster.

[There are some built-in policies available for Azure Stack HCI](/azure/defender-for-cloud/upcoming-changes#four-new-recommendations-for-azure-stack-hci-resource-type), such as:

[Preview]: Host and VM networking should be protected on Azure Stack HCI systems
[Preview]: Azure Stack HCI systems should have encrypted volumes
[Preview]: Azure Stack HCI servers should have consistently enforced application control policies
[Preview]: Azure Stack HCI servers should meet Secured-core requirements

You can find these policies in the Azure portal under Policy > Definitions > Category: Stack HCI.

#### Cluster and workload architecture

In addition to the built-in Azure Policy definitions, custom policies can be created for both the Azure Stack HCI resource and for the Azure Policy add-on for hybrid workloads. This allows you to add additional reliability constraints you'd like to enforce in your cluster and workload architecture.

Audit if Azure Stack HCI hosts are registered with Azure
Audit if Azure Stack HCI hosts are running the latest version of the operating system
Audit if Azure Stack HCI hosts have the required hardware components
Audit if Azure Stack HCI hosts have the required network configuration
Audit if Azure Stack HCI hosts have the required Azure services enabled
Audit if Azure Stack HCI hosts have the required security settings
Audit if Azure Stack HCI hosts have the required extensions installed
Audit if Azure Stack HCI hosts have the required Kubernetes clusters deployed
Audit if Azure Stack HCI hosts have the required Azure Kubernetes Service (AKS) integration enabled

## Security

AKS Hybird - section below needs complete review....

Security is one of the most important aspects of any architecture. To explore how Azure Stack HCI controls can increase the security posture of your application workloads, we recommend reviewing the [Security design principles](../security/principles.md). If your Azure Stack HCI clusters will host sensitive workload that comply with the regulatory requirements of the Payment Card Industry Data Security Standard (PCI-DSS 3.2.1), review [Azure Stack HCI security considerations](/azure-stack/hci/concepts/security).

To learn about DoD Impact Level 5 (IL5) support and requirements with Azure Stack HCI, review [Azure Government IL5 isolation requirements](/azure/azure-government/documentation-government-impact-level-5#azure-hybrid-workloads-service).

When discussing security with Azure Stack HCI, it's important to distinguish between *cluster security* and *workload security*. Cluster security is a shared responsibility between the cluster administrator, while workload security is the responsibility of the VM Guest OS admins and application developers. Azure Stack HCI has considerations and recommendations for both of these roles.

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

### Azure Advisor
 Azure Advisor helps ensure and improve Azure Stack HCI. It makes recommendations on a subset of the items listed in the policy section below, such as clusters without RBAC configured, missing Microsoft Defender configuration, unrestricted network access to the API Server. Likewise, it makes workload recommendations for some of the pod security initiative items. Review the [recommendations](/azure/advisor/advisor-security-recommendations).

### Policy definitions

Azure Policy offers various built-in policy definitions that apply to both the Azure Stack HCI and Arc enabled virtual machines to enhance the security of those resources using Azure Policy and Microsoft Defender of Cloud. Microsoft Defender for Cloud is a cloud-native application protection platform (CNAPP) that is made up of security measures and practices that are designed to protect cloud-based applications from various cyber threats and vulnerabilities. Review [Microsoft Defender for Cloud](/azure/defender-for-cloud/defender-for-cloud-introduction) for more information.

Here are some built-in policies on security available for Azure Stack HCI, such as:

[Preview]: Azure Stack HCI systems should have encrypted volumes
[Preview]: Azure Stack HCI servers should have consistently enforced application control policies
[Preview]: Azure Stack HCI servers should meet Secured-core requirements

You can find these policies in the Azure portal under Policy > Definitions > Category: Stack HCI.

#### Cluster security

Enabling Microsoft Defender for Cloud to a subscription registered with hybrid resources such as Azure Stack HCI and Azure Arc based virtual machines running on Azure Stack HCI are checked for its security compliance (Foundational CSPM, Cloud Security Posture Management) using the Security benchmark policies (Microsoft cloud security benchmark) configured in Azure policy. Additionally, enabling Defender for Servers plan at a subscription level helps to assess the security at workload level (CWP, Cloud Workload Protection) such as Azure Arc enabled virtual machines running on Azure Stack HCI.  

## Cost optimization

Cost optimization is about understanding your different configuration options and recommended best practices to reduce unnecessary expenses and improve operational efficiencies. Before you follow the guidance in this article, we recommend you review the following resources:

- [Cost optimization design principles](../cost-optimization/principles.md).
- How pricing and cost management works in Azure Stack HCI compared to ???
- Azure Stack HCI is priced on a per physical core basis, for on-premises clusters. If you are a Windows Server Datacenter customer with active Software Assurance (SA), you may choose to exchange core licenses to activate Azure Hybrid Benefit, which waives the Azure Stack HCI per core costs and Windows Server subscription. Review [Azure Hybrid Benefit](/windows-server/get-started/azure-hybrid-benefit) for more information.

When discussing cost optimization with Azure Stack HCI, it's important to distinguish between *cost of cluster resources* and *cost of workload resources*. Cluster resources are a shared responsibility between the cluster admin and their resource provider, while workload resources are the domain of a developer. Azure Stack HCI has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations**, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

For cluster cost optimization, go to the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) and select **Azure Stack HCI** from the available products. You can test different configuration and payment plans in the calculator.

### Design checklist

> [!div class="checklist"]
>
> - **Cluster architecture:** Use [Azure Stack Pricing](https://azure.microsoft.com/pricing/details/azure-stack/hci/) page in exploring the pricing options available to purchase Azure Stack HCI for on-premises servers. The flexible per core subscription model enables customers to optimize cost based on your needs.
> - **Cluster architecture:** Use Azure Stack HCI free trial offer for the first 60 days after registration for PoCs and validation purposes.
> - **Cluster architectures:** Turn on Azure Benefits on Azure Stack HCI to use some of the Azure exclusive workloads such as Windows Server Datacenter (Azure Edition), Extended Security Update (ESUs), Azure Policy Guest Configuration and Azure Virtual Desktop with no extra licensing cost. Review [Azure Benefits on Azure Stack HCI](/azure-stack/hci/manage/azure-benefits?tabs=wac) for more information.

### Recommendations

Explore the following table of recommendations to optimize your Azure Stack HCI configuration for cost.

| Recommendation | Benefit |
|----------------------------------|-----------|
|**Cluster architectures:** Azure Hybrid Benefit |Activate Azure Hybrid Benefit in the Azure Portal for the Azure Stack HCI cluster resource under the Configuration if you have Windows Server Datacenter license with active Software Assurance and would like to exchange those licenses to get Azure Stack HCI and Windows Server Subscription at no additional cost. |
|**Cluster architecture:** Windows Server subscription add-on |For customers that do not have valid Azure Hybrid Benefit licenses, you can consider subscribing to Windows Server guest licenses through Azure. You can purchase this in the Azure portal for the Azure Stack HCI cluster resource under the Configuration. By enabling this in the Azure Stack HCI cluster, you will be charged for the total number of physical cores in your cluster. |
|**Cluster architecture:** Azure verification for VMs - Enabled by default. |Ensure Azure verification for VMs is On under the Configuration for the Azure Stack HCI cluster resource in the Azure Portal. This will allow using the workloads and services that are available only on Azure, such as Extended Security Updates and others. |

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
>
> - **Cluster architecture:** For optimizing the storage space in cluster storage volume (CSV), use ReFS deduplication and compression which is more suitable for general-purpose workload types such as file servers (GPFS) and backup targets.
> - **Cluster architecture:** Enable Azure Monitor integration with Azure Stack HCI that enhances the health alerting capabilities of Azure Stack HCI. It is new real-time monitoring and there is no additional cost in using this feature.
> - **Cluster architecture:** Turn on Azure Stack HCI insights for detailed monitoring and logs through Azure Monitor Log Analytics.
> - **Cluster architecture:** Turn on Azure Stack HCI insights for detailed monitoring and logs through Azure Monitor Log Analytics.
> - **Cluster architecture:** Observability in Azure Stack HCI is enabled by default at the time of installation and is important because it enables the collection of telemetry and diagnostic information from the system automatically through through the TelemetryAndDiagnostics extension installed in all cluster nodes. Review [Azure Stack HCI observability](/azure-stack/hci/concepts/observability) for more information.

### Recommendations

Explore the following table of recommendations to optimize your Azure Stack HCI configuration for operations.

| Recommendation | Benefit |
|--------|----|
|**Cluster architectures:** Configure Azure Monitor Integration for Azure Stack HCI alerts| Enabling this feature allows you to forward the health alerts generated within the on-premises Azure Stack HCI system automatically to Azure Monitor alerts which then can be linked to an automated incident management system for timely and efficient response. Review [Respond to Azure Stack HCI health alerts with Azure Monitor alerts (preview)] (/azure-stack/hci/manage/health-alerts-via-azure-monitor-alerts) for more information.|
|**Cluster architectures:** Configure Azure Stack HCI Insights.| Azure Stack HCI insights provides customizable monitoring of cluster health, availability, and performance on one or more clusters. Cost will be based on the amount of data ingested and your data retention settings. Review [Monitor Azure Stack HCI features with Insights](/azure-stack/hci/manage/monitor-features) for more information.|
|**Cluster architectures:** Use Out-of-the-box Update feature in Azure Stack HCI integrated into Azure Update Manager | The update approach in release 23H2 and above provides a flexible foundation for integrating and managing various aspects of the Azure Stack HCI solution in one place. The update orchestrator is installed initially, enabling a new deployment experience, including management of the OS, core agents and services, and solution extensions. Review [About update in Azure Stack HCI](/azure-stack/hci/update/whats-the-lifecycle-manager-23h2) for more information.|
|**Workload architectures:** Turn on Guest management in Virtual Machines | Guest management allows you to run governance, monitoring, update management and security features on your virtual machines deployed on Azure Stack HCI. Review [Monitor Azure Stack HCI features with Insights](/azure-stack/hci/manage/monitor-features) for more information.|

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
>
> - **Cluster architectures:** Use the certified and validated hardware and software from Microsoft and Solution Builders available in the [Azure Stack HCI Catalog](https://azurestackhcisolutions.azure.microsoft.com/#/catalog) to optimize the performance of Azure Stack HCI system.
> - **Cluster architectures:** Use Premier Solutions for Azure Stack HCI that delivers the highest level of integration and validation through deeper collaboration between Microsoft and Solution partners for better end-to-end experience of Azure Stack HCI system.
> - **Cluster architecture:** Use VMFleet to get the baseline storage performance prior to deploying the workloads. VMFleet is a tool that can be used to measure the performance of a storage subsystem, best to measure baseline performance prior to workload deployment. Review [Using VMFleet for measuring storage subsystem performance](https://github.com/microsoft/diskspd/wiki/VMFleet) for more information.
> - **Cluster architecture:** Enable monitoring for ReFS Deduplication and Compression feature after enabling Insights for the Azure Stack HCI cluster. This feature helps monitoring ReFS deduplication and compression savings, performance impact and jobs. Review [Monitor ReFS deduplication and compression](/azure-stack/hci/manage/monitor-features#monitor-refs-deduplication-and-compression) for more information.

### Recommendations

Explore the following table of recommendations to optimize your Azure Stack HCI configuration for performance.

| Recommendation | Benefit |
|--------|----|
|**Cluster architectures:** Develop a detailed capacity plan and continually review and revise.|After formalizing your capacity plan, it should be frequently updated by continuously observing the resource utilization of the cluster.|
|**Cluster architectures:** When performance matters most | Workloads that have strict latency requirements or that need lots of mixed random IOPS, such as SQL Server databases or performance-sensitive Hyper-V virtual machines, should run on volumes that use mirroring to maximize performance. Mirroring is faster than any other resiliency type. Review [Planning the Storage Volumes](/azure-stack/hci/concepts/plan-volumes#choosing-the-resiliency-type) for more information.|
|**Cluster performance:** Monitor cluster performance with Health Service | The Health Service, improves the day-to-day monitoring and operational experience for clusters running Storage Spaces Direct. The Health Service is enabled by default with Storage Spaces Direct. No additional action is required to set it up or start it. Get live performance and capacity information from your Storage Spaces Direct cluster. Review [Monitor cluster performance with the Health Service](/azure-stack/hci/manage/health-service-overview) for more information.|
|**Workload performance:** Use DISKSPD to test workload storage performance| DISKSPD is a tool that you can customize to create your own synthetic workloads, and test your application before deployment. DISKSPD can give you a glimpse into what your system is capable of before deployment. At its core, DISKSPD simply issues a bunch of read and write operations and helps getting the performance metrics, whether it be latency, throughput, or IOPS. Review [Use DISKSPD to test workload storage performance](/azure-stack/hci/manage/diskspd-overview) for more information.|

For more suggestions, see [Principles of the performance efficiency pillar](/azure/well-architected/scalability/principles).

### Policy definitions

Review: Azure Policy offers various built-in policy definitions that apply to both the Azure resource and Azure Stack HCI like standard policy definitions, and using the Azure Policy add-on for hybrid workloads, also within the cluster. Many of the Azure resource policies come in both *Audit/Deny*, but also in a *Deploy If Not Exists* variant.

Review: There are a numerous number of policies, and key policies related to this pillar are summarized here. For a more detailed view, see [built-in policy definitions for hybrid workloads](/azure/governance/policy/samples/built-in-policies#hybrid workloads).

#### Cluster and workload architecture

- CPU and memory resource limits

In addition to the built-in policies, custom policies can be created for both the Azure Stack HCI resource and for the Azure Policy add-on for hybrid workloads. This allows you to add additional security constraints you'd like to enforce in your cluster and workload architecture.

## Additional resources

### Azure Architecture Center guidance

Many organizations need a hybrid approach to analytics, automation, and services because their data is hosted both on-premises and in the cloud. Organizations often extend on-premises data solutions to the cloud. To connect environments, organizations start by choosing a hybrid network architecture. Review [Hybrid + Multicloud Azure Architecture Center Guide](/azure/architecture/hybrid/hybrid-start-here) for more information on choosing the right architecture for the hybrid and multicloud environments.

### Cloud Adoption Framework guidance

The Cloud Adoption Framework [Ready methodology](/azure/cloud-adoption-framework/ready/) guides customers as they prepare their environment for cloud adoption. The methodology includes technical accelerators like Azure landing zones, which are the building blocks of any Azure cloud adoption environment. Review the below documents for more details on preparing your environment for hybrid cloud.

- [Prepare your environment for a hybrid and multicloud scenario](/azure/cloud-adoption-framework/scenarios/hybrid/ready)
- [Introduction to Azure Arc landing zone accelerator for hybrid and multicloud](/azure/cloud-adoption-framework/scenarios/hybrid/enterprise-scale-landing-zone)

## Next steps

- For deploying an Azure Stack HCI cluster using Azure portal or ARM template: [About Azure Stack HCI, version 23H2 deployment](/azure-stack/hci/deploy/deployment-introduction)
