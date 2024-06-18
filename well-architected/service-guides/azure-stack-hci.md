---
title: Azure Well-Architected Framework review for Azure Stack HCI
description: Provides a template for a Well-Architected Framework (WAF) article that is specific to Azure Stack HCI.
author: neilbird
ms.author: nebird
ms.topic: conceptual
ms.custom:
products: 
    - azure-stack-hci
categories: 
    - hybrid
---

# Azure Well-Architected Framework perspective on Azure Stack HCI

Azure Stack HCI is a hyperconverged infrastructure (HCI) platform that provides local storage, network and compute resources to support the creation and administration of Windows and Linux virtual machines (VMs), Kubernetes clusters for containerized workloads and additional services enabled through Azure Arc. The platform uses Azure for streamlined deployment and management, providing a unified and consistent management experience through Azure Arc. Azure Stack HCI and Azure Arc provide capabilities that enable business systems and application data to remain on-premises to address data sovereignty, regulation & compliance and latency requirements.

This article assumes you have an understanding of hybrid systems and have working knowledge of Azure Stack HCI. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](../pillars.md).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope.
>
> Also included are *recommendations* on the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations available for Azure Stack HCI platform or workloads. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environments.
>
> Azure architecture center guide that demonstrates the key recommendations: [Azure Stack HCI switchless storage for retail, manufacturing or remote office scenarios](/azure/architecture/hybrid/azure-stack-hci-switchless).

#### Technology scope

This review article focuses on the interrelated decisions for these Azure resources:

- Azure Stack HCI (_platform_), version 23H2 and later
- Azure Arc Virtual Machines (_workload_)

> [!NOTE]
>
> This article covers the preceding scope and organizes the checklists and recommendations by **platform architecture** and **workload architecture**. Platform concerns are the responsibility of the platform administrators, while the workload concerns are the responsibility of the workload operator and application developers. These roles and responsibilities are distinct and can be owned by separate teams or individuals. Keep that distinction in mind as you apply the guidance.

This guidance does not focus on specific resource types that can be deployed on Azure Stack HCI, such as [Azure Arc Virtual Machines (VMs)](/azure-stack/hci/manage/create-arc-virtual-machines), [Azure Kubernetes Service (AKS)](/azure/aks/hybrid/cluster-architecture), and [Azure Virtual Desktop (AVD)](/azure/virtual-desktop/azure-stack-hci-overview). When deploying these resource types on Azure Stack HCI, refer to the respective workload guidance to design solutions that meet your business requirements.

## Reliability

The purpose of the Reliability pillar is to provide continued service and functionality by **building resilience and an ability to recover quickly from failures**. The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy that can be applied to individual components, system flows, and the entire system.

In hybrid cloud deployments, the goal is to reduce the effects of one component failure. Use these design checklists and configuration suggestions to lessen the impact of a component failure for workloads deployed on Azure Stack HCI.

It's important to distinguish between *platform reliability* and *workload reliability*. Workload reliability has a dependency on the platform. It's the responsibility of the application owners and/or developers to design the application(s) that can deliver the defined reliability targets.

#### Design checklist

> [!div class="checklist"]
>
> - (HCI platform and workload architecture) **Define workload reliability targets**.
>
>   - Set your [Service Level Objectives (SLOs) as a way to evaluate availability targets](/azure/well-architected/reliability/metrics). Calculate SLOs as a percentage, such as 99.9, 99.95, or 99.995, that reflects workload uptime. Keep in mind, that this calculation isn't just based on the platform metrics emitted by the HCI cluster or workload. To have a comprehensive target measurement, factor in nuanced factors that are quantified, such as expected downtime during releases, routine operations, supportability, or other workload / organizational specific factors.
>
>   - Microsoft-provided service level agreement (SLA) are often used as influencing factors when calculating SLOs. However, Microsoft doesn't provide a SLA for the uptime and connectivity of Azure Stack HCI clusters or the deployed workload, because Microsoft does not have control over the customer datacenter reliability (_such as power / cooling_), or the people and processes used to administer the platform.
>
> - (HCI platform architecture) **Consider how performance and operations impact reliability**.
>
>   **Degraded performance of the cluster** or its dependencies can cause the HCI platform to become unavailable. For example,
>
>   - Without proper workload capacity planning, it's challenging to _right-size Azure Stack HCI clusters_ in the design phase, this is required to enable workload to meet the desired reliability targets. Use the [Azure Stack HCI Sizer Tool](https://aka.ms/azurestackhcicatalog/#/sizer) during  cluster design. Consider the "_N+1 minimum requirement for number of nodes_" if highly available VMs are required, or for business or mission-critical workloads consider using a "_N+2 number of nodes_" for the cluster size if resiliency is paramount.
>
>   - The reliability of the platform depends on how well the critical platform dependencies, such as physical disk types, perform. _It is important to choose the right disk types for your requirements_. For workloads that need low latency and high-throughput storage, an all-flash (_NVMe/SSD only_) storage configuration is recommended. For general purpose compute, a hybrid storage (_NVMe or SSDs for cache, and HDDs for capacity_) configuration may offer more storage space, however the trade-off is that spinning disks have significantly lower performance if your workload exceeds the [cache working set](/azure-stack/hci/concepts/cache#sizing-the-cache), and HDDs have a much lower _mean time between failure_ (MTBF) value compared to NVMe/SSDs.
>
>       These examples are discussed in more detail in the [Performance Efficiency](#performance-efficiency) pillar.
>
>   **Improper Azure Stack HCI operations** can impact patching and upgrades, testing, and consistency of deployments. Here are some examples.
>
>   - If the HCI platform doesn't _evolve with the latest hardware OEM firmware, drivers and innovations_, the platform might not take advantage of the latest resiliency features. Review the [Azure Stack HCI Solution Catalog](https://aka.ms/azurestackhcicatalog/#/catalog) and apply hardware OEM driver and firmware updates regularly.
>
>   - The target environment must be tested for connectivity, hardware, identity and access management, prior to deployment. Otherwise, the Azure Stack HCI solution might be deployed to an unstable environment causing reliability issues. The [Environmental Checker tool in standalone mode](/azure-stack/hci/manage/use-environment-checker) can be used to detect issues, even prior to the cluster hardware being available.
>
>       For operational guidance, see [Operational Excellence](#operational-excellence).
>
> - (HCI platform architecture) **Provide fault tolerance to the cluster and its infrastructure dependencies**.
>
>   **Storage design choices**. For most deployments, the default option to "_automatically create workload and infrastructure volumes_" is sufficient. If you select the _Advanced option: "create required infrastructure volumes only"_, ensure to configure the appropriate _volume fault tolerance within Storage Spaces Direct_ (S2D) based on your workload requirements. These decisions influence the performance, capacity and resiliency capabilities of the volume(s). For example, using a three-way mirror increases reliability and performance for clusters with three or more nodes. For more information, see [Fault tolerance for storage efficiency](/azure-stack/hci/concepts/fault-tolerance) and [creating S2D virtual disks / volumes](/azure-stack/hci/concepts/plan-volumes).
>
>    **Network architecture**. Deploy Azure Stack HCI using a _validated network topology_. Multi-node clusters (*with 4 or more physical nodes*) require the "*storage switched*" design, whereas clusters with two or three node can optionally use the "*storage switchless*" design. Regardless of the cluster size, it is recommended to use dual top of rack (ToR) switches for the Management and Compute intents (_north/south uplinks_) to provide increased fault tolerance. The dual ToR topology also provides resiliency during switch servicing (*firmware update*) operations, for more information, see [Validated network topologies](/azure-stack/hci/deploy/deployment-introduction#validated-network-topologies).
>
> - (Workload architecture) **Be resilient by building redundancy**.
>
>   Consider workload deployed on a single Azure Stack HCI cluster as a *locally redundant deployment*, although the cluster provides high availability at the platform level, it is important to remember a cluster is "_deployed in a single rack_". Therefore, for business or mission-critical use cases, it is recommended to deploy multiple instances of a workload or service across two or more separate Azure Stack HCI clusters, ideally deployed in separate physical locations.
>
>   - Use industry standard high availability (HA) patterns for workloads_, for example a design that provides active/passive synchronous or asynchronous data replication [(_such as SQL Server Always On_)](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server), and an external network load balancing (_NLB_) technology that can route user requests across the multiple workload instances that run on HCI clusters deployed in separate physical locations. Consider using a third party external NLB device, or evaluate the [load balancing options](/azure/architecture/guide/technology-choices/load-balancing-overview) that support traffic routing for hybrid / on-premises services, such as an Azure Application Gateway connected to on-premises using Express Route or VPN Tunnel.
>
>       For more information, see [Deploy workloads instances across multiple Azure Stack HCI clusters](/azure/well-architected/reliability/redundancy#key-design-strategies).
>
> - (Workload architecture) **Plan and test recoverability** based on your workload RPO and RTO targets.
>
>   Have _a well-documented disaster recovery (DR) plan_, test the recovery steps regularly to validate your business continuity plans and processes are valid. Evaluate if Azure Site Recovery is a viable choice for protecting virtual machines running on Azure Stack HCI. Review the guidance given in [Protect VM workloads with Azure Site Recovery on Azure Stack HCI (preview)](/azure-stack/hci/manage/azure-site-recovery).
>
> - (Workload architecture) **Configure and regularly test workload backup and restore procedures**.
>
>   Business requirements for _data recovery and retention_ will drive the strategy for workload backups, a comprehensive strategy will include considerations for  _workload operating system and application persistent data_, with the ability to restore individual (_point-in-time_) file and folder level data. Configure the backup retention policies based on your data recovery and compliance requirements, which will determine the number (and age) of available data recovery points. Explore Azure Backup as an option to enable Host and/or VM Guest level backups for Azure Stack HCI, and review data protection solutions from Backup ISV partners where relevant. For more information review [Azure Backup guidance and best practices](/azure/backup/guidance-best-practices) and [Azure Backup for Azure Stack HCI](/azure/backup/back-up-azure-stack-hyperconverged-infrastructure-virtual-machines).

#### Recommendations

| Recommendation | Benefit |
|--------|----|
|Reserve the equivalent of **[one capacity disks worth of space per node](/azure-stack/hci/concepts/plan-volumes#reserve-capacity)**, within the S2D storage pool.|If you choose to create workload volumes post Azure Stack HCI cluster deployment (_Advanced option: "create required infrastructure volumes only"_), it is recommended to **leave 5% to 10% of the total pool capacity unallocated in the storage pool**_**. This reserved unused / free capacity enables Storage Spaces Direct (S2D) to repair "in-place" when a physical disk fails, which improves data resiliency and performance in the event of a physical disk failure.|
|Ensure all physical nodes have network access to the list of **[required outbound https endpoints](/azure-stack/hci/concepts/firewall-requirements)** for Azure Stack HCI and Azure Arc.| To be able to reliably manage, monitor and operate Azure Stack HCI clusters and/or workload resources, the required outbound network endpoints must be accessible, either directly or through proxy server. Temporary interruption will not impact the running status of the workload but might affect manageability.|
|If you opt to create workload volumes (_virtual disks_) manually, use the most appropriate **[resiliency type](/azure-stack/hci/concepts/plan-volumes#choosing-the-resiliency-type)** to maximize workload resiliency and performance. Additionally, for any user volumes created manually post cluster deployment, ensure to [create the storage path for the volume(s) in Azure](/azure-stack/hci/manage/create-storage-path) to enable the volume to used to store workload VM configuration files, VM VHDs and VM images.|For Azure Stack HCI clusters with three or more nodes, consider using a 3-way mirror to provide the highest resiliency and performance capabilities. The use of mirrored volumes is recommended for business or mission-critical workloads.|
|Consider implementing **[workload anti-affinity rules](/azure-stack/hci/manage/vm-affinity#anti-affinity-rule-examples)** to ensure the VMs hosting multiple instances of the same service, run on separate physical hosts, which is a similar concept to "_Availability Sets_" in Azure.|Make all things redundant: For business or mission-critical workloads, deploy multiple instances of your applications or services using multiple Azure Arc virtual machines and/or kubernetes replica sets / pods. This increases resiliency in the event of an unplanned outage of a single physical node.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

Azure Stack HCI is a secure-by-default product that has more than 300 security settings enabled during the cloud deployment process. Default security settings provide a consistent security baseline to ensure that devices start in a known good state, and _drift protection controls_ provide at-scale management using Azure.

Security features enabled by default in Azure Stack HCI include; hardened OS security settings, Windows Defender for Application Control (WDAC), volume encryption using BitLocker, secret rotation, local built-in user accounts, Microsoft Defender for Cloud, and more. For more information on these security features, see [Review Security Features](/azure-stack/hci/concepts/security-features).

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy to bolster the security of the workload and applications that are deployed on Azure Stack HCI.

#### Design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md). Identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - (HCI platform architecture) **Review the security baselines**. The [Azure Stack HCI and security standards](/azure-stack/hci/assurance/azure-stack-security-standards) provide baseline guidance to strengthen the security posture of the platform and hosted workloads. If your workload needs to comply with specific regulatory compliance regulations, factor in those regulatory security standards, such as Payment Card Industry (PCI) Data Security Standards (DSS), Federal Information Processing Standard (FIPS) 140, and so on.
>
>   [HCI platform-provided default settings](/azure-stack/hci/concepts/security-features) enable security features including identity controls, network filtering, encryption, and more. These settings form a good security baseline for a newly provisioned Azure Stack HCI cluster. They can be customized based on your organizational security requirements.
>
>   Make sure you [detect and protect against undesired security configuration drift](/azure-stack/hci/manage/manage-secure-baseline).
>
> - (HCI platform architecture) **Detect, prevent, and respond to threats**. Continuously monitor the HCI environment and protect against existing and evolving threats.
>
>   It is recommended to enable Microsoft Defender for Cloud on Azure Stack HCI. Enable the basic Defender for Cloud plan (_free tier_) using the Foundational Cloud Security Posture Management (CSPM) to monitor and identify steps you can take to secure your Azure Stack HCI platform, along with other Azure and Azure Arc resources.
>
>   To benefit from the enhanced security features including security alerts for individual servers and Arc VMs, enable Defender for Servers on your Azure Stack HCI cluster nodes and Azure Arc VMs.
>
>   - Use [Defender for Cloud](/azure-stack/hci/manage/manage-security-with-defender-for-cloud) to measure the security posture of Azure Stack HCI nodes and workloads, providing a single pane of glass experience to help manage security compliance.
>   - Use [Microsoft Defender for Servers](/azure/defender-for-cloud/tutorial-enable-servers-plan) to monitor the hosted virtual machines for threats and misconfigurations. There are options on Azure Stack HCI nodes to provide endpoint detection and response (EDR) capabilities.
>
>   - Consider aggregating security and threat intelligence data from all sources into a centralized security information and event management (SIEM) solution, such as [Azure Sentinel](/azure/sentinel/overview).
>
> - (HCI platform architecture, workload architecture) **Create segmentation to contain the blast radius**. There are several strategies to attain segmentation.
>
>   - **Identity**. Keep roles and responsibilities for the platform and workload separate. Only authorized identities should be allowed to carry out the specific operations aligned with their designated roles. The platform duties are done by HCI platform administrators using both Azure and local domain credentials, while workload security is the responsibility of the workload operators and application developers. Use [Azure Stack HCI built-in role-based access control (RBAC)](/azure-stack/hci/manage/assign-vm-rbac-roles) roles, such as 'Azure Stack HCI Administrator' for platform administrators, and 'Azure Stack HCI VM Contributor' or 'Azure Stack HCI VM Reader' for workload operators to simplify delegating permissions. For more detailed information on the specific _Actions_ the built-in role grant, see [Azure RBAC documentation for Hybrid + multicloud roles](/azure/role-based-access-control/built-in-roles/hybrid-multicloud).
>
>   - **Network**. If needed, isolate networks, such as provisioning separate virtual local area networks (vLANs). When using this approach, each vLAN should be reachable from the management network to ensure HCI cluster nodes can communicate with the vLAN networks through the ToR (Top-of-Rack) switches or gateways. This is required for availability management of the workload, such as "_keep-alive probes_" from clustering and/or integration services agent communication to the Guest OS from the hypervisor.
>
> - (Platform architecture, workload architecture) **Use a trusted Identity Provider (IdP) to control access**. Microsoft Entra ID is recommended for all authentication and authorization purposes. Workload can be joined to on-premises active directory (AD) domain if required. Take advantage of features that support strong passwords, multifactor authentication, role-based access control (RBAC), and controls for the management of secrets.
>
> - (Platform architecture, workload architecture) **Isolate, filter, block network traffic.** For workload use cases that require virtual networks, micro-segmentation using Network Security Groups (NSGs), network quality of service (QoS) policies, or virtual appliance chaining to allow you to bring in third-party appliances for filtering, [review software defined network (SDN) considerations for network reference patterns](/azure-stack/hci/plan/network-patterns-sdn-considerations) for a list of the supported features and capabilities provided by [Network Controller](/azure-stack/hci/concepts/network-controller-overview).
>
> - (Workload architecture) **Protect against tampering by encrypting data**. Encrypt data in transit, at rest, and in use.
>
>   - Data-at-rest encryption is enabled on data volumes created during deployment. These data volumes include both infrastructure volumes and workload volumes. Review [Manage BitLocker encryption](/azure-stack/hci/manage/manage-bitlocker) for additional information.
>
>   - Use [Trusted launch](/azure-stack/hci/manage/trusted-launch-vm-overview) Azure Arc virtual machines to improve security of Gen 2 virtual machines, by utilizing OS features of modern operating systems, such as Secure Boot that can use a virtual Trusted Platform Module (vTPM).
>
> - **Operationalize secret management**. Based on your organizational requirements, change the credentials associated with the deployment user identity for Azure Stack HCI. For more information, see [Manage secrets rotation](/azure-stack/hci/manage/manage-secrets-rotation).
>
> - (HCI platform architecture) **Enforce security controls** Use Azure Policy to audit and enforce built-in policies, such as "_Application control policies should be consistently enforced_" or "_Encrypted volumes should be implemented_". These Azure policies can be used for auditing security settings and assessing the compliance status Azure Stack HCI. Example of the available policies are described in the [Azure policies](#azure-policies) section.
>
> - (Workload architecture) **Improve workload security posture with built-in policies**. Built-in policies can be applied to assess Azure Arc virtual machines running on Azure Stack HCI through the Security benchmark, Update Manager, and Guest Configuration, the policies that can be used check these conditions are shown below:
>
>   - Log Analytics agent installation.
>   - System Update checks for missing system updates and keeping them up-to-date with the latest security patches.
>   - Vulnerability assessment and potential mitigations.
>   - Use of secure communication protocols.

#### Recommendations

|Recommendation|Benefit|
|----------------------------------|-----------|
|Use the **[Security baseline and drift controls](/azure-stack/hci/manage/manage-secure-baseline)** settings to apply and maintain security settings on cluster nodes.|This helps to protect against changes / drift, by automatically refreshing security settings every 90 minutes to enforce the intended security posture of Azure Stack HCI.|
|Use **[Manage Windows Defender Application Control](/azure-stack/hci/manage/manage-wdac)** in Azure Stack HCI|Windows Defender Application Control (WDAC) reduces the attack surface of Azure Stack HCI, use the Azure portal or PowerShell to view policy settings and control policy modes. WDAC policies help to control which drivers and apps are allowed to run on your system. |
|**[Enable volume encryption using BitLocker](/azure-stack/hci/concepts/security-features#volume-encryption-via-bitlocker)** for data encryption-at-rest protection|BitLocker protects OS and data volumes, encrypting the cluster shared volumes (CSVs) created on the Azure Stack HCI using XTS-AES 256-bit encryption. This is a recommended default setting, enabled during Azure Stack HCI cloud deployment for all the data volumes.|
|Export **[BitLocker Recovery Keys](/azure-stack/hci/manage/manage-bitlocker#get-bitlocker-recovery-keys)** to store them in a secure location, external from the Azure Stack HCI cluster.|BitLocker keys may be required during specific troubleshooting or recovery actions. It's recommended to export, save, and back up encrypt keys for OS and data volumes from each Azure Stack HCI cluster using the 'Get-AsRecoveryKeyInfo' PowerShell cmdlet. Save the keys in a secure external location such as Azure Key Vault.|
|It is recommended to use a SIEM solution to increase security monitoring and alerting capabilities, to achieve this it is possible to **[Onboard Azure Arc-enabled servers (HCI platform nodes) to Microsoft Sentinel](/azure/azure-arc/servers/scenario-onboard-azure-sentinel)**. Alternatively, if you are using another SIEM solution, configure **[Syslog forwarding of security events](/azure-stack/hci/concepts/security-features#syslog-forwarding-of-security-events)** to the chosen solution.|Forwarding security event data using Microsoft Sentinel or Syslog forwarding provides alerting and reporting capabilities through integration with customer-managed security information & event management (SIEM) solution.|
|Review **[Overview of Server Message Block signing](/troubleshoot/windows-server/networking/overview-server-message-block-signing)** that can be used to enhance Data-in transit protection, which is configured as enabled in the “Default Security Settings”.|SMB signing allows you to digitally sign SMB traffic between an Azure Stack HCI platform and systems external to the platform (North / South). Configuring signing for external SMB traffic between the Azure Stack HCI platform and other systems helps to prevent relay attacks.|
|Review the **[SMB Encryption](/windows-server/storage/file-server/smb-security#smb-encryption)** setting that can be used to enhance Data-in transit protection, which is configured as enabled in the “Default Security Settings”.|SMB Encryption for in-cluster traffic setting controls the encryption of traffic between physical nodes in the Azure Stack HCI cluster (East / West) on your storage network.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary.

#### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md). Azure Stack HCI incurs costs for hardware, software licensing, workloads, guest virtual machines (Windows Server or Linux) licensing, and other integrated cloud services, as Azure Monitor, Microsoft Defender for Cloud, and others. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - (HCI platform architecture, workload architecture) **Estimate realistic costs as part of cost modeling**. Use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) to select and configure services like Azure Stack HCI, Azure Arc, and Azure Kubernetes Service on Azure Stack HCI. Experiment with various configurations and payment options to model costs.
>
> - (HCI platform architecture, workload architecture) **Optimize the cost of Azure Stack HCI hardware**. Choose a hardware OEM partner that aligns with your business and commercial requirements. Review the [Azure Stack HCI Solutions Catalog](https://aka.ms/hci-catalog#catalog) to explore the list of validated nodes, integrated systems and Premier Solutions that are certified. Communicate your workload characteristics, size, quantity, and performance with your hardware partner, to enable you to right-size a cost-effective hardware solutions for the Azure Stack HCI nodes and cluster size.
>
> - (HCI platform architecture) **Optimize your licensing costs**. Azure Stack HCI software is licensed and billed using a "_per physical CPU core_" basis. Use existing on-premises core licenses with the [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/#why-azure-hybrid-benefit) to reduce licensing costs for Azure Stack HCI workloads, such as Azure Arc VMs running Windows Server, SQL Server, or Azure Kubernetes Service (AKS) and Azure Arc-enabled SQL Managed Instance (_MI_). For more information, see [Azure Hybrid Benefit - Hybrid Cost Calculator](https://azure.microsoft.com/pricing/hybrid-benefit/#why-azure-hybrid-benefit).
>
> - (HCI platform architecture) **Save on environment costs**. Evaluate if these options are right for you to optimize the utilization of resources.
>
>   - **Take advantage of discount programs offered by Microsoft**. Consider using Azure Hybrid Benefits to reduce costs for running Azure Stack HCI and Windows Server workloads. For more information, see [Azure Hybrid Benefits on Azure Stack HCI](/azure-stack/hci/concepts/azure-hybrid-benefit-hci).
>
>   - **Explore promotional offers**. Take advantage of Azure Stack HCI's 60-day free trial after registration for initial proof of concepts (PoCs) or validations.
>
> - (HCI platform architecture) **Save on operational costs**.
>
>   - **Evaluate technology options for patching, updating, and other operations**. Azure Update Manager is free for Azure Stack HCI clusters with Azure Benefits and Azure Arc VM management enabled. For details, see [Azure Update Manager FAQ](/azure/update-manager/update-manager-faq#is-azure-update-manager-chargeable-on-azure-stack-hci) and [related pricing](https://azure.microsoft.com/pricing/details/azure-update-management-center).
>
>   - **Evaluate costs related to observability**. Set up alert rules and data collection rules (DCRs) to meet your monitoring and auditing needs. The amount of data ingested, processed, and retained directly influences costs. Optimize by using smart retention policies, number and frequency of alerts, and choosing the right storage tier for storing logs. For more information, see [Cost Optimization guidance for Log Analytics](/azure/well-architected/service-guides/azure-log-analytics#cost-optimization).
>
> - (Workload architecture) **Evaluate density over isolation**. Using Azure Kubernetes Service (AKS) on Azure Stack HCI can improve density and simplify management of workloads, enabling containerize applications to scale across multiple datacenter or edge locations. For pricing details, see [Azure Kubernetes Service on Azure Stack HCI pricing](https://azure.microsoft.com/pricing/details/azure-stack/aks-hci/).

#### Recommendations

| Recommendation | Benefit |
|----------------------------------|-----------|
|Use the **[Azure Hybrid Benefit for Azure Stack HCI](/azure-stack/hci/concepts/azure-hybrid-benefit-hci)** if you have Windows Server Datacenter Licenses with Software Assurance (SA).|With Azure Hybrid Benefit for Azure Stack HCI, you can maximize the value of your on-premises licenses and modernize your existing infrastructure to Azure Stack HCI at no additional cost.|
|Review the **[License Windows Server VMs on Azure Stack HCI](/azure-stack/hci/manage/vm-activate?tabs=azure-portal#windows-server-subscription)** to choose either Windows Server Subscription add-on or Bring your own license (BYOL) to license and activate the Windows Server virtual machines (VMs) for using them on Azure Stack HCI.| While you can use any existing Windows Server licenses and activation methods available, optionally, you can enable "Windows Server Subscription add-on" available for Azure Stack HCI only to subscribe Windows Server guest licenses through Azure which is charged for the total number of physical cores in the Azure Stack HCI cluster.|
|Use the **[Azure verification for VMs](/azure-stack/hci/deploy/azure-verification?tabs=wac)** benefits extended to Azure Stack HCI that makes it possible for supported Azure-exclusive workloads to work outside of the cloud.|This benefit is enabled by default on Azure Stack HCI 23H2 or later. It helps to provide guarantees for the VMs to operate in other Azure environments and allows the workloads to benefit from offers that are available only in Azure, such as Extended Security Updates (ESU).|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

Monitoring and diagnostics are crucial. Not only can you measure performance statistics, but also use metrics troubleshoot and remediate issues quickly. We recommend reviewing the [Operational excellence design principles](/azure/well-architected/devops/principles) and how to [collect logs](/azure-stack/hci/manage/collect-logs) to help troubleshoot issues with Azure Stack HCI.

#### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployments.

> [!div class="checklist"]
>
> - (HCI platform architecture) **Increase supportability of Azure Stack HCI**. Observability is enabled by default at the time of deployment, these capabilities enhance the supportability of the platform. Telemetry and diagnostic information is shared securely from the platform using the _AzureEdgeTelemetryAndDiagnostics_ extension which is installed on all HCI cluster nodes by default. Review [Azure Stack HCI Observability](/azure-stack/hci/concepts/observability) documentation for more information.
>
> - (HCI platform architecture) **Use Azure services to reduce operational complexity and increase management scale**. Azure Stack HCI is integrated with Azure, this enables services such as Azure Update Manager for patching the platform, Azure Monitor for monitoring and alerting. Azure Arc together with Azure Policy for managing security configuration and compliance auditing, plus Defender for Cloud to help manage cyber threats and vulnerability management. Using Azure as the control plane for these operational processes and procedure help to reduce complexity, improves efficiencies of scale and consistency of management experience.
>
> - (Workload architecture) **Plan IP address network range requirements for workloads in advance**. Azure Stack HCI provides a platform to deploy and manage virtualized or containerized workloads. Also, consider the IP address requirements for logical networks used by your workload. Review these resources:
>
>    - [Azure Stack HCI (_platform_) network reference architecture and IP requirements](/azure-stack/hci/plan/cloud-deployment-network-considerations#network-design-framework)
>    - [Logical network(s)](/azure-stack/hci/manage/create-logical-networks) that will be used for your workloads, 
> For specific examples, see [network requirements for AKS clusters](/azure/aks/hybrid/aks-hci-network-system-requirements#networking-for-aks-cluster-vms), [logical networks for Arc VMs](/azure-stack/hci/manage/create-logical-networks), or [Azure Virtual Desktop (AVD)](/azure/virtual-desktop/azure-stack-hci-overview).
>
> - (Workload configuration) **Enable monitoring and alerting for workloads deployed on Azure Stack HCI clusters**. It's recommended that you use the [Azure Arc for Servers - Azure Monitor extension](/azure/azure-arc/servers/manage-vm-extensions#extensions) for VM workload, or the [Azure Monitor Container Insights Extension for Azure Kubernetes Service (AKS) clusters](/azure/azure-arc/kubernetes/extensions-release#azure-monitor-container-insights).
>
>   - Evaluate whether using a centralized Log Analytics workspace is appropriate for your workload. For an example of using a shared log sink, see [Azure Monitor with a centralized Log Analytics workspace](/azure/cloud-adoption-framework/ready/landing-zone/design-area/management-workloads#workload-management-and-monitoring-recommendations).
>
> - (HCI platform architecture) **Deploy safely by using proper validation techniques**. Use the [Environmental Checker tool in standalone mode](/azure-stack/hci/manage/use-environment-checker) to assess the readiness of the target environment prior to deploying an Azure Stack HCI solution. This will validate the required connectivity, hardware, Active Directory, network, and Arc integration prerequisites are configured correctly.
>
> - (HCI platform architecture) **Get current, stay current**. Use the [Azure Stack HCI Solution Catalog](https://aka.ms/azurestackhcicatalog/#/catalog) to stay current with the latest hardware OEM innovations for Azure Stack HCI cluster deployments. Consider using Premium Solutions to benefit from additional integration, turn-key deployment capabilities and simplified update experience.
>
>   - Azure Update Manager is used to update the platform, including the management of the operating system (OS), core agents and services, including solution extensions. It is important to stay current, consider using the "Enable automatic upgrade" setting where possible for extensions.

### Recommendations

| Recommendation | Benefit |
|--------|----|
|[**Enable Azure Monitor Insights on Azure Stack HCI clusters**](/azure-stack/hci/concepts/monitoring-overview) to enhance monitoring and alerting using native Azure capabilities. <br><br> Insights is capable of monitoring key Azure Stack HCI features using the cluster Performance counters and Event Log Channels that are collected by the data collection rule (DCR). <br><br>For certain hardware infrastructure, such as Dell APEX, you can visualize hardware events in real time.<br><br>For more information, see [Feature workbooks](/azure-stack/hci/manage/monitor-features#monitor-dell-apex-cloud-platform).|Insights is managed by Azure, so is always up to date, it is scalable across multiple clusters and is highly customizable.<br><br>Insights provides access to default workbooks with basic metrics, along with specialized workbooks created for monitoring key features of Azure Stack HCI. This provides near real-time monitoring and you can create graphs and customized visualization using aggregation and filter functionality and configuring custom alert rules.<br><br>The cost of the service is based on the quantity of data ingested and the data retention settings of the Log Analytics workspace. When [enabling Azure Stack HCI Insights](/azure-stack/hci/manage/monitor-hci-single-23h2#enable-insights) it is **strongly recommended** to use the DCR created by the Insights creation experience, this will be prefixed with `AzureStackHCI-` in the DCR name, and will be configured to collect only the required data.|
|**Set up alerts** and configure the alert processing rules based on your organizational requirements. Get notified of changes in health, metrics, logs, or other types of observability data. <br><br> - [Health Alerts](/azure-stack/hci/manage/health-alerts-via-azure-monitor-alerts)<br>- [Log alerts](/azure-stack/hci/manage/setup-hci-system-alerts) <br> - [Metric alerts](/azure-stack/hci/manage/setup-metric-alerts)<br>For more information, see [Recommended rules for metric alerts](/azure-stack/hci/manage/set-up-recommended-alert-rules). |The integration of Azure Monitor alerts with Azure Stack HCI offers several key benefits such as no additional cost, near real-time monitoring and customizing alerts to notify the right team or admin for remediation. <br><br>You can collect comprehensive list of metrics for compute, storage, and network resources in Azure Stack HCI. You can perform advanced logic operations on your log data and evaluate metrics of your Azure Stack HCI system at regular intervals.|
|Review **[About Updates in Azure Stack HCI](/azure-stack/hci/update/whats-the-lifecycle-manager-23h2)** in release 23H2 which provides a flexible foundation for integrating and managing various aspects of the Azure Stack HCI solution in one place.|The update orchestrator is installed during initial HCI cluster deployment. This component automates updates and management operations. To keep Azure Stack HCI in a supported state, make sure you update your cluster(s) on a regular cadence to move to new baseline builds as they become available, which will provide new capabilities and improvements to the platform.<br><br>To learn more about _release trains_, the cadence of updates, and the support window of each baseline build, review [Azure Stack HCI 23H2 release information](/azure-stack/hci/release-information-23h2#about-azure-stack-hci-version-23h2-releases).|
|To help with hands-on skilling, labs, training events, product demos, or proof-of-concept projects consider using [Jumpstart HCIBox](https://aka.ms/JumpstartHCIBox) which can be used to rapidly deploy Azure Stack HCI, which can be done without the requirement for physical hardware by deploying the solution using a VM on Azure.|[HCIBox](https://aka.ms/JumpstartHCIBox) supports Azure Stack HCI 23H2 to enable rapid testing and evaluation of the latest capabilities of Azure edge products, such as native Azure Arc and AKS integration in a self-contained sandbox.<br><br>This sandbox can be deployed easily to an Azure subscription, using a VM that supports nested virtualization to emulate an HCI cluster inside an Azure VM. This allows users to experience many Azure Stack HCI 23H2 features like the new [cloud deployment feature](/azure-stack/hci/deploy/deployment-azure-resource-manager-template) with minimal manual effort.<br><br>For more information on Jumpstart HCIBox review this [Microsoft Tech Community blog](https://techcommunity.microsoft.com/t5/azure-arc-blog/announcing-hcibox-support-for-azure-stack-hci-23h2/ba-p/4035596).|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

#### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key indicators for HCI platform performance and workload performance.

> [!div class="checklist"]
>
> - (HCI platform architecture) **Use the Azure Stack HCI-validated hardware** or integrated systems from OEM partner offerings. Consider using the Premium Solution Builders available in the [Azure Stack HCI Catalog](https://aka.ms/azurestackhcicatalog#catalog) to optimize the performance of Azure Stack HCI environment.
>
> - (HCI platform storage architecture) **Choose the right [physical disk type(s) for the HCI cluster nodes](/azure-stack/hci/concepts/cache#drive-types-and-deployment-options)** based on your workload performance and capacity requirements. For high-performance workloads that require low latency and high-throughput storage, consider using an all-flash (NVMe/SSD only) storage configuration. For general purpose compute and/or large storage capacity requirements, consider using hybrid storage (SSD or NVMe for cache tier and HDDs for capacity tier) which may provide increased storage capacity.
>
> - (HCI platform architecture) **Use the [Azure Stack HCI Sizer Tool](https://aka.ms/azurestackhcicatalog/#/sizer) during the cluster design (_pre-deployment_) phase**. Azure Stack HCI clusters should be sized appropriately by using the workload capacity, performance, and resiliency requirements as inputs. The size will determine the maximum number of physical nodes that can be offline simultaneously (_cluster quorum_), such as any planned (_maintenance_) or unplanned (_power / hardware failure_) events. For more information, see [Cluster quorum overview](/azure-stack/hci/concepts/quorum#cluster-quorum-overview).
>
> - (HCI platform architecture) **Use all-flash (NVMe or SSD) based solutions for workloads that have high performance or low latency requirements**. This includes but is not limited to highly transactional database technologies, production Azure Kubernetes Service (AKS) clusters or any mission or business critical workloads with low latency or high throughput storage requirements. All-flash deployments to maximize storage performance. All-NVMe or all-SSD configurations (_especially at very small scale_) improves storage efficiency and maximizes performance, as no drives are used as a cache tier. For more information, see [All-flash based storage](/azure-stack/hci/concepts/cache#all-flash-deployment-possibilities).
>
> - (HCI platform architecture) **Establish a performance baseline for HCI cluster storage** before deploying production workloads. Configure **[Monitor Azure Stack HCI features with Insights](/azure-stack/hci/manage/monitor-features)**  to monitor the performance of a single Azure Stack HCI cluster or multiple clusters simultaneously. 
>
> - (HCI platform architecture) **Consider using the Monitor for ReFS Deduplication and Compression feature** after enabling Insights for the Azure Stack HCI cluster. This consderation should be based on the workload storage usage and capacity requirements. This feature provides monitoring for ReFS deduplication and compression savings, performance impact, and jobs. For more information, see [Monitor ReFS deduplication and compression](/azure-stack/hci/manage/monitor-features#monitor-refs-deduplication-and-compression) for more information.
>
>   As a minimum requirement, plan to reserve `1 x physical nodes (N+1)` worth of capacity across the cluster to ensure cluster nodes can be drained when performing updates using Azure Update Management. Consider `N+2` nodes for business or mission-critical use cases.
>

### Recommendations

| Recommendation | Benefit |
|--------|----|
|If you selected the advanced option to "_create infrastructure volumes only_" during HCI cluster deployment, when creating workload volumes for performance intensive workloads it is recommended to **[create the virtual disks using Mirroring](/azure-stack/hci/concepts/plan-volumes#when-performance-matters-most)**.|Workloads that have strict latency requirements or that need high throughput with a mix of random read/write IOPs, such as SQL Server databases, Kubernetes clusters or other performance-sensitive virtual machines. Deploy the workload VHDs on volumes that use mirroring, to maximize performance and resiliency. Mirroring is faster than any other resiliency type.|
|Consider using **[DiskSpd to test workload storage performance](/azure-stack/hci/manage/diskspd-overview)** capabilities of the HCI cluster. <br><br>Another option is to use VMFleet that generates load and measures the performance of a storage subsystem. Evaluate if [using VMFleet for measuring storage subsystem performance](https://github.com/microsoft/diskspd/wiki/VMFleet) is the right option for you.|Establish a baseline for HCI cluster performance, prior to deploying production workloads.  DiskSpd allows administrators to test the storage performance of the cluster, using various command line parameters. At its core, DiskSpd simply issues read and write operations and outputs performance metrics, such as latency, throughput, and IOPs.|

## Tradeoffs

There are design tradeoffs with the approaches described in the pillar checklists. Here are some examples of advantages and drawbacks.

:::image type="icon" source="../_images/trade-off.svg"::: **Building redundancy increases costs**.

- It is essential to understand your workloads requirements up front, such as the workload RTO and RPO targets and storage performance (IOPs / Through-put) requirements when designing and procuring the hardware for an Azure Stack HCI solution. To deploy highly available workloads, a minimum of a 3-node cluster is recommended, which enables 3-way mirroring for workload volumes and data. For the Compute resources, ensure a minimum of N+1 number of physical nodes are deployed, (_reserving the capacity of a "single nodes worth of space" in the cluster at all times_), or N+2 worth of capacity, which provides additional resiliency, such that two nodes could be offline in a cluster, and the workload would be able to remain online, which caters for the unlikely event of _a node failing during a planned update procedure_.

- For business or mission-critical workloads, it is recommended to deploy two or more separate Azure Stack HCI clusters and deploy multiple instances of your workload services across the separate clusters. This requires workload design patterns that leverage data replication and application load balancing technologies, such as [SQL Server Always-On Availability Groups](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) using sync or a-sync database replication in order to achieve low RTO and RTO targets across separate clusters in different data centers.

- Consequently, increasing workload resiliency and achieving low RTO + RPO targets is more expensive, and requires well-architected applications and operational rigor.

:::image type="icon" source="../_images/trade-off.svg"::: **Providing scalability without effective workload planning, increases costs**.

- Incorrect cluster sizing can lead to insufficient capacity, or reduced ROI if the hardware is over provisioned, both scenarios impact costs.

- **Increased capacity, higher costs**. During the Azure Stack HCI cluster design phase, adequate planning is required to _right-size the capabilities and number of cluster nodes_ based on workload capacity requirements. Therefore, it is important to understand the workload requirements (_vCPUs, Memory and Storage X number of VMs_) and allow some additional _headroom_ in addition to projected growth. It is possible to perform Add-node gesture when using _storage switched_" design, however procuring additional hardware post-deployment can have a long lead time and is more complex than sizing the cluster hardware and number of nodes (_maximum 16 nodes_) appropriately during the initial deployment.

- There are disadvantages if you over provision the node hardware specification and select the incorrect number of nodes (_size of the cluster_). For example, if the workload requirements are much smaller than the clusters overall capacity, it could lead to a reduction in the return on investment (ROI) value, if the hardware is underutilized throughout the hardware warranty period.

## Azure policies

Azure Policy offers various built-in policy definitions that apply to both the Azure Stack HCI and Azure Arc virtual machines to monitor their compliance state and enhance the security of those resources using Azure Policy and Microsoft Defender for Cloud. Here are some key checks for Azure Stack HCI environment:

- Host and VM networking should be protected.
- Encrypted volumes should be implemented.
- Application control policies should be consistently enforced.
- Secured-core requirements should be met.

Review the [Azure Stack HCI built-in policies](/azure/governance/policy/samples/built-in-policies#stack-hci). Microsoft Defender for Cloud has [new recommendations](/azure/defender-for-cloud/upcoming-changes#four-new-recommendations-for-azure-stack-hci-resource-type) that shows the compliance state for the built-in policies. For more information, see [Built-in policies under Security Center](/azure/governance/policy/samples/built-in-policies#security-center).

If your workload runs on Arc VMs deployed on Azure Stack HCI, consider the built-in policies such as, denying the creation or modification of Extended Security Updates (ESUs) licenses. For more information, see [built-in policy definitions for Azure Arc enabled workloads](/azure/governance/policy/samples/built-in-policies#azure-arc).

Consider creating custom policies for additional governance that can be created for both the Azure Stack HCI resources and Azure Arc VMs deployed on HCI cluster. For example:

- Auditing Azure Stack HCI host registration with Azure.
- Ensuring hosts run the latest OS version.
- Checking for required hardware components and network configurations.
- Verifying the enablement of necessary Azure services and security settings.
- Confirming the installation of required extensions.
- Assessing the deployment of Kubernetes clusters and AKS integration.

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your Virtual Machines.

- [Reliability](/azure/advisor/advisor-high-availability-recommendations)
- [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations)
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations)
- [Operational excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations)

## Next steps

- Consider the following articles in Azure Architecture Center as resources that demonstrate the recommendations highlighted in this article.
    -   Foundational architecture that demonstrates the key recommendations:  [Azure Stack HCI switchless storage for retail, manufacturing or remote office scenarios](/azure/architecture/hybrid/azure-stack-hci-switchless).
    -   For organizations that need a hybrid approach, design choices around a hybrid network architecture are crucial. For more information, see [Hybrid architecture design](/azure/architecture/hybrid/hybrid-start-here).

- Build implementation expertise by using the following Azure Stack HCI product documentation: 
  - [Azure Stack HCI overview](/azure-stack/hci/overview)
  - [Azure Stack HCI network deployment considerations](/azure-stack/hci/plan/cloud-deployment-network-considerations)
  - [About Azure Stack HCI, version 23H2 deployment](/azure-stack/hci/deploy/deployment-introduction)

- Review Cloud Adoption Framework (CAF) guidance

    The Cloud Adoption Framework [Ready methodology](/azure/cloud-adoption-framework/ready/) guides customers as they prepare their environment for cloud adoption. The methodology includes technical accelerators like Azure landing zones, which are the building blocks of any Azure cloud adoption environment. Review the below documents for more details on preparing your environment for hybrid cloud.

    - [Prepare your environment for a hybrid and multicloud scenarios](/azure/cloud-adoption-framework/scenarios/hybrid/ready)
    - [Introduction to Azure Arc landing zone accelerator for hybrid and multicloud](/azure/cloud-adoption-framework/scenarios/hybrid/enterprise-scale-landing-zone)