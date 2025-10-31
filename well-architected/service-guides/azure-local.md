---
title: Architecture Best Practices for Azure Local
description: Learn how to deploy Azure Local so that you can create and manage Windows and Linux VMs, Kubernetes clusters, and other Azure Arc-enabled services.
author: neilbird
ms.author: nebird
ms.topic: concept-article
ms.date: 07/18/2025
ms.service: azure-waf
ms.subservice: waf-service-guide
products: 
    - azure-local
categories: 
    - hybrid
---

# Architecture best practices for Azure Local

Azure Local extends Azure to customer-owned infrastructure using Azure Arc, enabling modern and traditional applications to run consistently across distributed or edge locations. The solution provides a unified management experience using Azure and supports a wide range of validated hardware from trusted Microsoft partners. You can use Azure Local and Azure Arc capabilities to keep business systems and application data on-premises to address data sovereignty, regulation and compliance, and latency requirements.

This article assumes you have an understanding of hybrid systems and have working knowledge of Azure Local. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](../pillars.md).

### Technology scope

This review focuses on the interrelated decisions for the following Azure resources:

- Azure Local (_platform_), 2311 and later
- Azure Local VMs (_workload_)

> [!NOTE]
>
> This article covers the preceding scope and provides checklists and recommendations that are organized by **platform architecture** and **workload architecture**. Platform concerns are the responsibility of the platform administrators. Workload concerns are the responsibility of the workload operator and application developers. These roles and responsibilities are distinct, but depending on your organization structure they could be owned by one team, or separate teams and individuals. Keep that distinction in mind when you apply the guidance.

This guidance doesn't focus on specific resource types that you can deploy on Azure Local, such as [Azure Local VMs](/azure/azure-local/manage/create-arc-virtual-machines), [Azure Kubernetes Service (AKS)](/azure/aks/hybrid/cluster-architecture), and [Azure Virtual Desktop](/azure/virtual-desktop/azure-stack-hci-overview). When you deploy these resource types on Azure Local, refer to the respective workload guidance to design solutions that meet your business requirements.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [**Reliability design principles**](../resiliency/principles.md) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

In hybrid cloud deployments, the goal is to reduce the effects of one component failure. Use these design checklists and configuration suggestions to lessen the impact of a component failure for workloads that you deploy on Azure Local.

It's important to distinguish between *platform reliability* and *workload reliability*. Workload reliability has a dependency on the platform. Application owners or developers must design applications that can deliver the defined reliability targets.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the performance of Azure Local. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - (Azure Local platform architecture and workload architecture) **Define workload reliability targets**.
>
>   - Set your [service-level objectives (SLOs) so that you can evaluate availability targets](../reliability/metrics.md). Calculate SLOs as a percentage, such as 99.9%, 99.95%, or 99.995%, that reflects workload uptime. Keep in mind that this calculation isn't based only on the platform metrics that the Azure Local instance or workload emits. To get a comprehensive target measurement, factor in nuanced factors that are quantified, such as expected downtime during releases, routine operations, supportability, or other workload-specific or organizational-specific factors.
>
>   - Microsoft-provided service-level agreements (SLAs) often influence SLO calculations. But Microsoft doesn't provide an SLA for the uptime and connectivity of Azure Local instances or the deployed workload, because Microsoft doesn't control the customer datacenter reliability (_such as power and cooling_) or the people and processes that administer the platform.
>
> - (Azure Local platform architecture) **Consider how performance and operations affect reliability**.
>
>   **Degraded performance of the instance** or its dependencies can make the Azure Local platform unavailable. For example:
>
>   - Without proper workload capacity planning, it's challenging to _rightsize Azure Local instances_ in the design phase, which is a requirement so that the workload can meet the desired reliability targets. Use the [Azure Local sizer tool](https://azurelocalsolutions.azure.microsoft.com/#/sizer) during instance design. Consider the "_N+1 minimum requirement for number of machines_" if you require highly available VMs. For business-critical or mission-critical workloads, consider using a "_N+2 number of machines_" for the instance size if resiliency is paramount.
>
>   - The reliability of the platform depends on how well the critical platform dependencies, such as physical disk types, perform. _You must choose the right disk types for your requirements_. For workloads that need low-latency and high-throughput storage, we recommend an all-flash (_NVMe/SSD only_) storage configuration. For general purpose compute, a hybrid storage (_NVMe or SSDs for cache and HDDs for capacity_) configuration might provide more storage space. But the tradeoff is that spinning disks have significantly lower performance if your workload exceeds the [cache working set](/azure/azure-local/concepts/cache#sizing-the-cache), and HDDs have a much lower _mean time between failure_ value compared to NVMe/SSDs. For more information, review the [physical disk drives section](/azure/architecture/hybrid/azure-local-baseline#physical-disk-drives) of the Azure Local baseline reference architecture.
>
>     [Performance Efficiency](#performance-efficiency) describes these examples in more detail.
>
>   **Improper Azure Local operations** can affect patching and upgrades, testing, and consistency of deployments. Here are some examples:
>
>   - If the Azure Local platform doesn't _evolve with the latest hardware original equipment manufacturer (OEM) firmware, drivers, and innovations_, the platform might not take advantage of the latest resiliency features. Apply hardware OEM driver and firmware updates regularly. For more information, see [Solution Builder Extension updates for Azure Local](/azure/azure-local/update/solution-builder-extension) or speak to your hardware OEM partner for how to obtain firmware and driver updates.
>
>   - You must test the target environment for connectivity, hardware, and identity and access management before your deployment. Otherwise, you might deploy the Azure Local solution to an unstable environment, which can create reliability problems. You can use the [environmental checker tool in standalone mode](/azure/azure-local/manage/use-environment-checker) to detect problems, even before the instance hardware is available.
>
>       For operational guidance, see [Operational Excellence](#operational-excellence).
>
> - (Azure Local platform architecture) **Provide fault tolerance to the instance and its infrastructure dependencies**.
>
>   - **Storage design choices**. For most deployments, the default option to "_automatically create workload and infrastructure volumes_" is sufficient. If you select the _advanced option: "create required infrastructure volumes only"_, configure the appropriate _volume fault tolerance within Storage Spaces Direct_ based on your workload requirements. These decisions influence the performance, capacity, and resiliency capabilities of the volumes. For example, a three-way mirror increases reliability and performance for instances with three or more machines. For more information, see [Fault tolerance for storage efficiency](/azure/azure-local/concepts/fault-tolerance) and [Create Storage Spaces Direct virtual disks and volumes](/azure/azure-local/concepts/plan-volumes).
>
>    - **Network architecture**. Use a _validated network topology_ to deploy Azure Local. Multi-machine instances, *with four or more physical machines*, require the "*storage switched*" design. Instances with two or three machines can optionally use the "*storage switchless*" design. Regardless of the instance size, we recommend that you use dual top of rack (ToR) switches for the management and compute intents (_north and south uplinks_) to provide increased fault tolerance. The dual ToR topology also provides resiliency during switch servicing (*firmware update*) operations. For more information, see [Validated network topologies](/azure/azure-local/deploy/deployment-introduction#validated-network-topologies).
>
> - (Workload architecture) **Build redundancy to provide resiliency**.
>
>   - Consider a workload that you deploy on a single Azure Local instance as a *locally redundant deployment*. The instance provides high availability at the platform level, but you must remember that you deploy the instance "_in a single rack_". Therefore, for business-critical or mission-critical use cases, we recommend that you deploy multiple instances of a workload or service across two or more separate Azure Local instances, ideally in separate physical locations.
>
>   - Use industry-standard high-availability patterns for workloads, for example a design that provides active/passive synchronous or asynchronous data replication [(_such as SQL Server Always On_)](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Another example is an external network load balancing (_NLB_) technology that can route user requests across the multiple workload instances that run on Azure Local instances that you deploy in separate physical locations. Consider using a partner external NLB device. Or evaluate the [load balancing options](/azure/architecture/guide/technology-choices/load-balancing-overview) that support traffic routing for hybrid and on-premises services, such as an Azure Application Gateway instance that uses Azure ExpressRoute or a VPN tunnel to connect to an on-premises service.
>
>       For more information, see [Deploy workloads instances across multiple Azure Local instances](../reliability/redundancy.md).
>
> - (Workload architecture) **Plan and test recoverability** based on your workload recovery point objective (RPO) and recovery time objective (RTO) targets.
>
>   Have _a well-documented disaster recovery plan_. Test the recovery steps regularly to ensure that your business continuity plans and processes are valid. Determine whether Azure Site Recovery is a viable choice for protecting VMs that run on Azure Local. For more information, see [Protect VM workloads with Azure Site Recovery on Azure Local (preview)](/azure/azure-local/manage/azure-site-recovery).
>
> - (Workload architecture) **Configure and regularly test workload backup and restore procedures**.
>
>   Business requirements for _data recovery and retention_ drive the strategy for workload backups. A comprehensive strategy includes considerations for _workload operating system (OS) and application persistent data_, with the ability to restore individual (_point-in-time_) file-level and folder-level data. Configure the backup retention policies based on your data recovery and compliance requirements, which determine the number and age of available data recovery points. Explore Azure Backup as an option to enable host-level or VM guest-level backups for Azure Local. Review data protection solutions from Backup independent software vendor partners where relevant. For more information, see [Azure Backup guidance and best practices](/azure/backup/guidance-best-practices) and [Azure Backup for Azure Local](/azure/backup/back-up-azure-stack-hyperconverged-infrastructure-virtual-machines).

### Configuration recommendations

| Recommendation | Benefit |
|--------|----|
|For business or mission-critical scenarios, [implement multi-region workload architectures, to allow maintenance windows when applying updates to reduce risk.](/azure/well-architected/reliability/highly-available-multi-region-design)|For business or mission-critical scenarios (_with very strict / low SLO targets_) consider implementing maintenance windows when applying updates. For example, perform a failover of business critical workloads between active / passive Azure Local instances, as part of your disaster recovery and business continuity (BC/DR) capability. Using a workload failover approach reduces operational risks when applying updates to Azure Local, given the resiliency of any failover cluster is temporarily reduced when physical nodes are drained and restarted (_one by one_) during servicing operations.|
|Reserve the equivalent of [**one capacity disk worth of space per machine**](/azure/azure-local/concepts/plan-volumes#reserve-capacity) within the Storage Spaces Direct storage pool.|If you choose to create workload volumes after you deploy an Azure Local instance (_Advanced option: "create required infrastructure volumes only"_), we recommend that you **leave 5% to 10% of the total pool capacity unallocated in the storage pool**. This reserved and unused, or free, capacity enables Storage Spaces Direct to repair "in-place" when a physical disk fails, which improves data resiliency and performance if a physical disk failure occurs.|
|Ensure that all physical machines have network access to the list of [**required outbound HTTPS endpoints**](/azure/azure-local/concepts/firewall-requirements) for Azure Local and Azure Arc.| To reliably manage, monitor, and operate Azure Local instances or workload resources, the required outbound network endpoints must have access, either directly or through a proxy server. A temporary interruption doesn't affect the running status of the workload but might affect manageability.|
|If you opt to create workload volumes (_virtual disks_) manually, use the most appropriate [**resiliency type**](/azure/azure-local/concepts/plan-volumes#choosing-the-resiliency-type) to maximize workload resiliency and performance. For any user volumes that you create manually after you deploy the instance, [create a storage path for the volumes in Azure](/azure/azure-local/manage/create-storage-path). The volume can store workload VM configuration files, VM virtual hard disks (VHDs), and VM images via the storage path.|For Azure Local instances with three or more machines, consider using a three-way mirror to provide the highest resiliency and performance capabilities. We recommend that you use mirrored volumes for business-critical or mission-critical workloads.|
|Consider implementing [**workload anti-affinity rules**](/azure/azure-local/manage/vm-affinity#anti-affinity-rule-examples) to ensure that the VMs that host multiple instances of the same service run on separate physical hosts. This concept is similar to "_availability sets_" in Azure.|Make all components redundant. For business-critical or mission-critical workloads, use multiple Azure Local VMs or Kubernetes replica sets or pods to deploy multiple instances of your applications or services. This approach increases resiliency if an unplanned outage of a single physical machine occurs.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [**Security design principles**](../security/security-principles.md) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure Local.

Azure Local is a secure-by-default product that has more than 300 security settings enabled during the cloud deployment process. Default security settings provide a consistent security baseline to ensure that devices start in a known good state. And you can use _drift protection controls_ to provide at-scale management.

Default security features in Azure Local include hardened OS security settings, Application Control, volume encryption via BitLocker, secret rotation, local built-in user accounts, and Microsoft Defender for Cloud. For more information, see [Review security features](/azure/azure-local/concepts/security-features).

### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md). Identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - (Azure Local platform architecture) **Review the security baselines**. [Azure Local and security standards](/azure/azure-local/assurance/azure-stack-security-standards) provide baseline guidance to strengthen the security posture of the platform and hosted workloads. If your workload needs to comply with specific regulatory compliance regulations, factor in the regulatory security standards, such as Payment Card Industry Data Security Standards and Federal Information Processing Standard 140.
>
>   [Azure Local platform-provided default settings](/azure/azure-local/concepts/security-features) enable security features, including identity controls, network filtering, and encryption. These settings form a good security baseline for a newly provisioned Azure Local instance. You can customize each setting based on your organizational security requirements.
>
>   Make sure that you [detect and protect against undesired security configuration drift](/azure/azure-local/manage/manage-secure-baseline).
>
> - (Azure Local platform architecture) **Detect, prevent, and respond to threats**. Continuously monitor the Azure Local environment and protect against existing and evolving threats.
>
>   We recommend that you enable Defender for Cloud on Azure Local. Enable the basic Defender for Cloud plan (_free tier_) by using Defender Cloud Security Posture Management to monitor and identify steps that you can take to secure your Azure Local platform, along with other Azure and Azure Arc resources.
>
>   To benefit from the enhanced security features, including security alerts for individual servers and Azure Local VMs, enable Microsoft Defender for Servers on your Azure Local instance machines and Azure Local VMs.
>
>   - Use [Defender for Cloud](/azure/azure-local/manage/manage-security-with-defender-for-cloud) to measure the security posture of Azure Local machines and workloads. Defender for Cloud provides a single pane of glass experience to help manage security compliance.
>   - Use [Defender for Servers](/azure/defender-for-cloud/tutorial-enable-servers-plan) to monitor the hosted VMs for threats and misconfigurations. You can also enable endpoint detection and response capabilities on Azure Local machines.
>
>   - Consider aggregating security and threat intelligence data from all sources into a centralized security information and event management (SIEM) solution, such as [Microsoft Sentinel](/azure/sentinel/overview).
>
> - (Azure Local platform architecture and workload architecture) **Create segmentation to contain the blast radius**. There are several strategies to attain segmentation.
>
>   - **Identity**. Keep roles and responsibilities for the platform and workload separate. Allow only authorized identities to carry out the specific operations that align with their designated roles. Azure Local platform administrators use both Azure and local domain credentials to do platform duties. Workload operators and application developers manage workload security. To simplify delegating permissions, use [Azure Local built-in role-based access control (RBAC)](/azure/azure-local/manage/assign-vm-rbac-roles) roles, such as 'Azure Local Administrator' for platform administrators and 'Azure Local VM Contributor' or 'Azure Local VM Reader' for workload operators. For more information about specific built-in role _actions_, see [Azure RBAC documentation for hybrid and multicloud roles](/azure/role-based-access-control/built-in-roles/hybrid-multicloud).
>
>   - **Network**. Isolate networks if needed. For example, you can provision multiple logical networks that use separate virtual local area networks (vLANs) and network address ranges. When you use this approach, ensure that the management network can reach each logical network and vLAN so that Azure Local machines can communicate with the vLAN networks through the ToR switches or gateways. This configuration is required for availability management of the workload, such as allowing infrastructure management agents to communicate with the workload guest OS.
>
>   - Review [Recommendations for building a segmentation strategy](/azure/well-architected/security/segmentation) for additional information.
>
> - (Azure Local platform architecture and workload architecture) **Use a trusted identity provider to control access**. We recommend Microsoft Entra ID for all authentication and authorization purposes. You can join a workload to an on-premises Windows Server Active Directory domain if required. Take advantage of features that support strong passwords, multifactor authentication, RBAC, and controls for the management of secrets.
>
> - (Azure Local platform architecture and workload architecture) **Isolate, filter, and block network traffic.** You might have a workload use case that requires virtual networks, microsegmentation via network security groups, network quality of service policies, or virtual appliance chaining so that you can bring in partner appliances for filtering. If you have such a workload, see [software-defined network considerations for network reference patterns](/azure/azure-local/plan/network-patterns-sdn-considerations) for a list of the supported features and capabilities that [Network Controller](/azure/azure-local/concepts/network-controller-overview) provides.
>
> - (Workload architecture) **Encrypt data to protect against tampering**. Encrypt data in transit, data at rest, and data in use.
>
>   - Data-at-rest encryption is enabled on data volumes that you create during deployment. These data volumes include both infrastructure volumes and workload volumes. For more information, see [Manage BitLocker encryption](/azure/azure-local/manage/manage-bitlocker).
>
>   - Use [trusted launch for Azure Local VMs](/azure/azure-local/manage/trusted-launch-vm-overview) to improve security of Gen 2 VMs by using OS features of modern operating systems, such as Secure Boot, which can use a virtual Trusted Platform Module.
>
> - **Operationalize secret management**. Based on your organizational requirements, change the credentials that are associated with the deployment user identity for Azure Local. For more information, see [Manage secrets rotation](/azure/azure-local/manage/manage-secrets-rotation).
>
> - (Azure Local platform architecture) **Enforce security controls**. Use Azure Policy to audit and enforce built-in policies, such as "_Application control policies should be consistently enforced_" or "_Encrypted volumes should be implemented_". You can use these Azure policies to audit security settings and assess the compliance status of Azure Local. For examples of the available policies, see [Azure policies](#azure-policies).
>
> - (Workload architecture) **Improve workload security posture with built-in policies**. To assess Azure Local VMs that run on Azure Local, you can apply built-in policies via the security benchmark, Azure Update Manager, or the Azure Policy guest configuration extension. You can use various policies to check the following conditions:
>
>   - Log Analytics agent installation
>   - Out-of-date system updates that need to be up to date with the latest security patches
>   - Vulnerability assessment and potential mitigations
>   - Use of secure communication protocols

### Configuration recommendations

|Recommendation|Benefit|
|----------------------------------|-----------|
|Use the [**security baseline and drift controls**](/azure/azure-local/manage/manage-secure-baseline) settings to apply and maintain security settings on instance machines.|These configurations help to protect against unwanted changes and drift because they automatically refresh security settings every 90 minutes to enforce the intended security posture of Azure Local.|
|Use [**Application Control**](/azure/azure-local/manage/manage-wdac) in Azure Local.|Application Control reduces the attack surface of Azure Local. Use the Azure portal or PowerShell to view policy settings and control policy modes. Application Control policies help to control which drivers and apps are allowed to run on your system. |
|[**Enable volume encryption via BitLocker**](/azure/azure-local/manage/manage-bitlocker#enable-disable-volume-encryption-with-bitlocker) for data encryption-at-rest protection.|BitLocker protects OS and data volumes by encrypting the instance shared volumes that are created on the Azure Local. BitLocker uses XTS-AES 256-bit encryption. We recommended that you keep the volume encryption default setting enabled during Azure Local cloud deployment for all data volumes.|
|Export [**BitLocker recovery keys**](/azure/azure-local/manage/manage-bitlocker#get-bitlocker-recovery-keys) to store them in a secure location that's external from the Azure Local instance.|You might need BitLocker keys during specific troubleshooting or recovery actions. We recommend that you export, save, and back up encrypt keys for OS and data volumes from each Azure Local instance via the 'Get-AsRecoveryKeyInfo' PowerShell cmdlet. Save the keys in a secure external location, such as Azure Key Vault.|
|Use a security information and event management (SIEM) solution to increase security monitoring and alerting capabilities. To do so, you can [**onboard Azure Arc-enabled servers (Azure Local platform machines) to Microsoft Sentinel**](/azure/azure-arc/servers/scenario-onboard-azure-sentinel). Alternatively, if you use a different SIEM solution, configure [**syslog forwarding of security events**](/azure/azure-local/concepts/security-features#syslog-forwarding-of-security-events) to the chosen solution.|Forward security event data by using Microsoft Sentinel or syslog forwarding to provide alerting and reporting capabilities through integration with a customer-managed SIEM solution.|
|Use [**Server Message Block (SMB) signing**](/troubleshoot/windows-server/networking/overview-server-message-block-signing) to enhance data-in-transit protection, which is enabled in the "default security settings."|Server Message Block (SMB) signing allows you to digitally sign SMB traffic between an Azure Local platform and systems external to the platform (north or south). Configure signing for external SMB traffic between the Azure Local platform and other systems to help prevent relay attacks.|
|Use the [**Server Message Block (SMB) encryption**](/windows-server/storage/file-server/smb-security#smb-encryption) setting to enhance data-in-transit protection, which is enabled in the "default security settings."|The Server Message Block (SMB) encryption for in-instance traffic setting controls the encryption of traffic between physical machines in the Azure Local instance (east or west) on your storage network.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure Local and its environment.

### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time. 

Azure Local incurs costs for hardware, software licensing, workloads, guest VMs (Windows Server or Linux) licensing, and other integrated cloud services, such as Azure Monitor and Defender for Cloud.

> [!div class="checklist"]
>
> - (Azure Local platform architecture and workload architecture) **Estimate realistic costs as part of cost modeling**. Use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) to select and configure services like Azure Local, Azure Arc, and AKS on Azure Local. Experiment with various configurations and payment options to model costs.
>
> - (Azure Local platform architecture and workload architecture) **Optimize the cost of Azure Local hardware**. Choose a hardware OEM partner that aligns with your business and commercial requirements. To explore the certified list of validated machines, integrated systems, and premier solutions, see [Azure Local solutions catalog](https://azurelocalsolutions.azure.microsoft.com/#/catalog). Communicate your workload characteristics, size, quantity, and performance with your hardware partner so that you can rightsize a cost-effective hardware solution for the Azure Local machine and instance size.
>
> - (Azure Local platform architecture) **Optimize your licensing costs**. Azure Local software is licensed and billed on a "_per physical CPU core_" basis. Use existing on-premises core licenses with [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/#why-azure-hybrid-benefit) to reduce licensing costs for Azure Local workloads, such as Azure Local VMs that run Windows Server, SQL Server, or AKS and Azure Arc-enabled Azure SQL Managed Instance. For more information, see [Azure Hybrid Benefit cost calculator](https://azure.microsoft.com/pricing/hybrid-benefit/#why-azure-hybrid-benefit).
>
> - (Azure Local platform architecture) **Save on environment costs**. Evaluate whether the following options can help optimize your resource usage.
>
>   - **Take advantage of discount programs that Microsoft offers**. Consider using Azure Hybrid Benefit to reduce the cost to run Azure Local and Windows Server workloads. For more information, see [Azure Hybrid Benefit for Azure Local](/azure/azure-local/concepts/azure-hybrid-benefit).
>
>   - **Explore promotional offers**. Take advantage of the Azure Local 60-day free trial after registration for initial proof of concepts or validations.
>
> - (Azure Local platform architecture) **Save on operational costs**.
>
>   - **Evaluate technology options for patching, updating, and other operations**. Update Manager is free for Azure Local instances and Azure Local VMs, for more information, see [Update Manager FAQ](/azure/update-manager/update-manager-faq#what-is-the-pricing-for-azure-update-manager) and [Update Manager pricing](https://azure.microsoft.com/pricing/details/azure-update-management-center).
>
>   - **Evaluate costs related to observability**. Set up alert rules and data collection rules (DCRs) to meet your monitoring and auditing needs. The amount of data that your workload ingests, processes, and retains directly influences costs. Optimize by using smart retention policies, limiting the number and frequency of alerts, and choosing the right storage tier for storing logs. For more information, see [Cost Optimization guidance for Log Analytics](../service-guides/azure-log-analytics.md#cost-optimization).
>
> - (Workload architecture) **Evaluate density over isolation**. Use AKS on Azure Local to improve density and simplify workload management so that you can enable containerized applications to scale across multiple datacenter or edge locations. Azure Kubernetes Service (AKS) on Azure Local is included as part of Azure Local pricing, for more information, see [AKS on Azure Local pricing](https://azure.microsoft.com/pricing/details/azure-stack/aks-hci/).

### Configuration recommendations

| Recommendation | Benefit |
|----------------------------------|-----------|
|Use [**Azure Hybrid Benefit for Azure Local**](/azure/azure-local/concepts/azure-hybrid-benefit) if you have Windows Server Datacenter licenses with Software Assurance.|With Azure Hybrid Benefit for Azure Local, you can maximize the value of your on-premises licenses and modernize your existing infrastructure to Azure Local at no additional cost.|
|Choose either the Windows Server subscription add-on or bring your own license to license and activate the Windows Server VMs and use them on Azure Local. For more information, see [**License Windows Server VMs on Azure Local**](/azure/azure-local/manage/vm-activate#windows-server-subscription).| While you can use any existing Windows Server licenses and activation methods available, optionally, you can enable "Windows Server subscription add-on" available for Azure Local only to subscribe Windows Server guest licenses through Azure which is charged for the total number of physical cores in the Azure Local instance.|
|Use the [**Azure verification for VMs**](/azure/azure-local/deploy/azure-verification) benefit extended to Azure Local so that supported Azure-exclusive workloads can work outside of the cloud.|This benefit is enabled by default on Azure Local 2311 or later. Use this benefit so that the VMs can operate in other Azure environments and workloads can benefit from offers that are available only in Azure, such as Extended Security Updates enabled by Azure Arc.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

Monitoring and diagnostics are crucial. You can use metrics to measure performance statistics and to troubleshoot and remediate problems quickly. For more information about how to troubleshoot problems, see [Operational Excellence design principles](../devops/principles.md) and [Collect diagnostic logs for Azure Local](/azure/azure-local/manage/collect-logs).

### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure Local.

> [!div class="checklist"]
>
> - (Azure Local platform architecture) **Increase supportability of Azure Local**. Observability is enabled by default at the time of deployment. These capabilities enhance the supportability of the platform. Telemetry and diagnostic information is shared securely from the platform by using the _AzureEdgeTelemetryAndDiagnostics_ extension, which is installed on all Azure Local machines by default. For more information, see [Azure Local observability](/azure/azure-local/concepts/observability).
>
> - (Azure Local platform architecture) **Use Azure services to reduce operational complexity and increase management scale**. Azure Local is integrated with Azure to enable services such as Update Manager for patching the platform and Azure Monitor for monitoring and alerting. You can use Azure Arc and Azure Policy to manage security configuration and compliance auditing. Implement Defender for Cloud to help manage cyber threats and vulnerability. Use Azure as the control plane for these operational processes and procedures to help reduce complexity, improve efficiencies of scale, and improve management consistency.
>
> - (Workload architecture) **Plan IP address network range requirements for workloads in advance**. Azure Local provides a platform to deploy and manage virtualized or containerized workloads. Also consider the IP address requirements for logical networks that your workload uses. Review these resources:
>
>    - [Azure Local (_platform_) network reference architecture and IP requirements](/azure/azure-local/plan/cloud-deployment-network-considerations#network-design-framework)
>
>    - Workloads deployed on Azure Local require [logical networks](/azure/azure-local/manage/create-logical-networks). For specific examples, see [Network requirements for AKS clusters](/azure/aks/hybrid/aks-hci-network-system-requirements#networking-for-aks-cluster-vms), [Logical networks for Azure Local VMs](/azure/azure-local/manage/create-logical-networks), and [Virtual Desktop with Azure Local](/azure/virtual-desktop/azure-stack-hci-overview).
>
> - (Workload configuration) **Enable monitoring and alerting for workloads that you deploy on Azure Local**. You can use [Azure Monitor for virtual machines](https://aka.ms/vmmonitoringdocs), or [VM Insights for Azure Local VMs](https://aka.ms/vminsightsdocs), or use [Container Insights and managed Prometheus AKS clusters](/azure/azure-monitor/containers/kubernetes-monitoring-enable).
>
>   Evaluate whether you should use a centralized Log Analytics workspace for your workload. For an example of a shared log sink (_data location_), see [Workload management and monitoring recommendations](/azure/cloud-adoption-framework/ready/landing-zone/design-area/management-workloads#workload-management-and-monitoring-recommendations).
>
> - (Azure Local platform architecture) **Use proper validation techniques for a safe deployment**. Use the [environmental checker tool in standalone mode](/azure/azure-local/manage/use-environment-checker) to assess the readiness of the target environment before you deploy an Azure Local solution. This tool validates the proper configuration of required connectivity, hardware, Windows Server Active Directory, networks, and Azure Arc integration prerequisites.
>
> - (Azure Local platform architecture) **Get current and stay current**. Use the [Azure Local solution catalog](https://azurelocalsolutions.azure.microsoft.com/#/catalog) to stay current with the latest hardware OEM innovations for Azure Local instance deployments. Consider using premium solutions to benefit from extra integration, turn-key deployment capabilities, and a simplified update experience.
>
>   Use Update Manager to update the platform and manage the OS, core agents, and services, including solution extensions. Stay current, and consider using the "Enable automatic upgrade" setting where possible for extensions.

### Configuration recommendations

| Recommendation | Benefit |
|--------|----|
|[**Enable Monitor Insights on Azure Local instances**](/azure/azure-local/concepts/monitoring-overview) to enhance monitoring and alerting by using native Azure capabilities. <br><br> Insights can monitor key Azure Local features by using the instance performance counters and event log channels that are collected by the data collection rule (DCR). <br><br>For certain hardware infrastructure, such as Dell APEX, you can visualize hardware events in real time.<br><br>For more information, see [Feature workbooks](/azure/azure-local/manage/monitor-features#monitor-dell-apex-cloud-platform).|Azure manages Insights, so it's always up to date, it's scalable across multiple instances, and it's highly customizable.<br><br>Insights provides access to default workbooks with basic metrics, along with specialized workbooks that are created for monitoring key features of Azure Local. This feature provides near real-time monitoring. You can create graphs and customized visualization by using aggregation and the filter functionality. You can also configure custom alert rules.<br><br>The cost of Insights is based on the quantity of data ingested and the data retention settings of the Log Analytics workspace. When you [enable Azure Local Insights](/azure/azure-local/manage/monitor-single-23h2#enable-insights), we **recommended** that you use the DCR created by the Insights creation experience. The prefix of the DCR name is `AzureStackHCI-`. It's configured to collect only the required data.|
|**Set up alerts**, and configure the alert processing rules based on your organizational requirements. Get notified of changes in health, metrics, logs, or other types of observability data. <br><br> - [Health alerts](/azure/azure-local/manage/health-alerts-via-azure-monitor-alerts)<br>- [Log alerts](/azure/azure-local/manage/setup-system-alerts) <br> - [Metric alerts](/azure/azure-local/manage/setup-metric-alerts)<br><br>For more information, see [Recommended rules for metric alerts](/azure/azure-local/manage/set-up-recommended-alert-rules). |Integrate Monitor alerts with Azure Local to get several key benefits at no extra cost. Get near real-time monitoring and customize alerts to notify the right team or admin for remediation. <br><br>You can collect a comprehensive list of metrics for compute, storage, and network resources in Azure Local. Perform advanced logic operations on your log data and evaluate metrics of your Azure Local instance at regular intervals.|
|Use the update feature to integrate and manage various aspects of the Azure Local solution in one place. For more information, see [**About updates in Azure Local**](/azure/azure-local/update/about-updates-23h2). |The update orchestrator is installed during the initial Azure Local instance deployment. This feature automates updates and management operations. To keep Azure Local in a supported state, make sure that you update your instances on a regular cadence to move to new baseline builds when they become available. This method provides new capabilities and improvements to the platform.<br><br>For more information about _release trains_, the cadence of updates, and the support window of each baseline build, see [Azure Local release information](/azure/azure-local/release-information-23h2#about-azure-stack-hci-version-23h2-releases).|
|To help with hands-on skilling, labs, training events, product demos, or proof-of-concept projects, consider using [Azure Arc Jumpstart](https://jumpstart.azure.com/azure_jumpstart_localbox). Rapidly deploy Azure Local without the need for physical hardware by using a VM on Azure to deploy the solution.|[LocalBox](https://jumpstart.azure.com/azure_jumpstart_localbox) supports virtualized deployments of Azure Local to enable rapid testing and evaluation of the latest capabilities of Azure edge products, such as native Azure Arc and AKS integration in a self-contained sandbox.<br><br>You can deploy this sandbox to an Azure subscription by using a VM that supports nested virtualization to emulate an Azure Local instance inside an Azure VM. Get Azure Local features like the new [cloud deployment feature](/azure/azure-local/deploy/deployment-azure-resource-manager-template) with minimal manual effort.<br><br>For more information, see [Microsoft Tech Community blog](https://techcommunity.microsoft.com/t5/azure-arc-blog/announcing-hcibox-support-for-azure-stack-hci-23h2/ba-p/4035596).|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key indicators for Azure Local.

> [!div class="checklist"]
>
> - (Azure Local platform architecture) **Use the Azure Local-validated hardware** or integrated systems from OEM partner offerings. Consider using the premium solution builders in the [Azure Local catalog](https://azurelocalsolutions.azure.microsoft.com/#/catalog) to optimize the performance of your Azure Local environment.
>
> - (Azure Local platform storage architecture) Choose the right [**physical disk types for the Azure Local machines**](/azure/azure-local/concepts/cache#drive-types-and-deployment-options) based on your workload performance and capacity requirements. For high-performance workloads that require low latency and high-throughput storage, consider using an all-flash (NVMe/SSD only) storage configuration. For general purpose compute or large storage capacity requirements, consider using hybrid storage (SSD or NVMe for cache tier and HDDs for capacity tier), which might provide increased storage capacity.
>
> - (Azure Local platform architecture) **Use the [Azure Local sizer tool](https://azurelocalsolutions.azure.microsoft.com/#/sizer) during the instance design (_pre-deployment_) phase**. Azure Local instances should be sized appropriately by using the workload capacity, performance, and resiliency requirements as inputs. The size determines the maximum number of physical machines that can be offline simultaneously (_cluster quorum_), such as any planned (_maintenance_) or unplanned (_power or hardware failure_) events. For more information, see [Cluster quorum overview](/azure/azure-local/concepts/quorum#cluster-quorum-overview).
>
> - (Azure Local platform architecture) **Use all-flash (NVMe or SSD) based solutions for workloads that have high-performance or low-latency requirements**. These workloads include but are not limited to highly transactional database technologies, production AKS clusters, or any mission-critical or business-critical workloads with low-latency or high-throughput storage requirements. Use all-flash deployments to maximize storage performance. All-NVMe or all-SSD configurations (_especially at a very small scale_) improve storage efficiency and maximize performance because no drives are used as a cache tier. For more information, see [All-flash-based storage](/azure/azure-local/concepts/cache#all-flash-deployment-possibilities).
>
> - (Azure Local platform architecture) **Establish a performance baseline for Azure Local instance storage** before you deploy production workloads. Configure [**Monitor Azure Local features with Insights**](/azure/azure-local/manage/monitor-features) to monitor the performance of a single Azure Local instance or multiple instances simultaneously.
>
> - (Azure Local platform architecture) **Consider using the Monitor for Resilient File System (ReFS) deduplication and compression feature** after you enable Insights for the Azure Local instance. Determine whether you should use this feature based on your workload storage usage and capacity requirements. This feature provides monitoring for ReFS deduplication and compression savings, performance impact, and jobs. For more information, see [Monitor ReFS deduplication and compression](/azure/azure-local/manage/monitor-features#monitor-refs-deduplication-and-compression).
>
>   As a minimum requirement, plan to reserve `1 x physical machines (N+1)` worth of capacity across the instance to ensure that instance machines can be drained when they perform updates via Update Management. Consider reserving `2 physical machines (N+2)` machines work of capacity for business-critical or mission-critical use cases.
>

### Configuration recommendations

| Recommendation | Benefit |
|--------|----|
|If you select the advanced option to "_create infrastructure volumes only_" during Azure Local instance deployment, we recommend that you [**create the virtual disks by using mirroring**](/azure/azure-local/concepts/plan-volumes#when-performance-matters-most) when you create workload volumes for performance-intensive workloads.|This recommendation benefits workloads that have strict latency requirements or that need high throughput with a mix of random read and write input/output operations per second (IOPs), such as SQL Server databases, Kubernetes clusters, or other performance-sensitive VMs. Deploy the workload VHDs on volumes that use mirroring to maximize performance and resiliency. Mirroring is faster than any other resiliency type.|
|Consider using [**DiskSpd to test workload storage performance**](/azure/azure-local/manage/diskspd-overview) capabilities of the Azure Local instance. <br><br>You can also use VMFleet to generate load and measure the performance of a storage subsystem. Evaluate whether you should use [VMFleet for measuring storage subsystem performance](https://github.com/microsoft/diskspd/wiki/VMFleet).|Establish a baseline for Azure Local instance performance before you deploy production workloads. DiskSpd allows administrators to test the storage performance of the instance by using various command line parameters. The main function of DiskSpd is to issue read and write operations and output performance metrics, such as latency, throughput, and IOPs.|

## Tradeoffs

There are design tradeoffs with the approaches described in the pillar checklists. Here are some examples of advantages and drawbacks.

:::image type="icon" source="../_images/trade-off.svg"::: **Building redundancy increases costs**

- Understand your workload's requirements up front, such as the workload recovery time objective (RTO) and recovery point objective (RPO) targets and storage performance requirements (IOPs and throughput), when you design and procure the hardware for an Azure Local solution. To deploy highly available workloads, we recommend a minimum of a three-machine instance, which enables three-way mirroring for workload volumes and data. For the compute resources, ensure that you deploy a minimum of "N+1 number of physical machines", which _reserves the capacity of a "single machine worth of space" in the instance at all times_. For business-critical or mission-critical workloads, consider reserving "_N+2 machines worth of capacity_" to provide increased resiliency. For example, if two machines in the instance are offline, the workload can remain online. This approach provides increased resiliency for a scenario such as, if a machine running workload goes offline during a planned update procedure (_resulting in two machines being offline simultaneously_).

- For business-critical or mission-critical workloads, we recommend that you deploy two or more separate Azure Local instances and deploy multiple instances of your workload services across the separate instances. Use a workload design pattern that takes advantage of data replication and application load balancing technologies. For example, [SQL Server always-on availability groups](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) use synchronous or asynchronous database replication to achieve low RTO and RTO targets across separate instances in different datacenters.

- Consequently, an increase in workload resiliency and a decrease in RTO and RPO targets increases costs and requires well-architected applications and operational rigor.

:::image type="icon" source="../_images/trade-off.svg"::: **Providing scalability without effective workload planning increases costs**

- Incorrect instance sizing can lead to insufficient capacity or reduced return on investment (ROI) if the hardware is overprovisioned. Both scenarios affect costs.

- **Increased capacity equals higher costs**. During the Azure Local instance design phase, adequate planning is required to _rightsize the capabilities and number of instance machines_ based on workload capacity requirements. Therefore, you must understand the workload requirements (_vCPUs, memory, storage, and X number of VMs_) and allow for some extra _headroom_ in addition to projected growth. You can perform an add-machine gesture when you use a "_storage switched_" design. But it can take a long time to get more hardware after your deployment. And an add-note gesture is more complex than sizing the instance hardware and number of machines (_maximum 16 machines_) appropriately during the initial deployment.

- There are disadvantages if you overprovision the machine hardware specification and select the incorrect number of machines (_size of the instance_). For example, if the workload requirements are much smaller than the instance's overall capacity and the hardware is underused throughout the hardware warranty period, the ROI value might decrease.

## Azure policies 

Azure provides an extensive set of built-in policies related to Azure Local and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Host and VM networking should be protected.
- Encrypted volumes should be implemented.
- Application control policies should be consistently enforced.
- Secured-core requirements should be met.

Review the [Azure Local built-in policies](/azure/governance/policy/samples/built-in-policies#stack-hci). Defender for Cloud has [new recommendations](/azure/defender-for-cloud/upcoming-changes#four-new-recommendations-for-azure-stack-hci-resource-type) that show the compliance state for the built-in policies. For more information, see [Built-in policies for Azure Security Center](/azure/governance/policy/samples/built-in-policies#security-center).

If your workload runs on Azure Local VMs that you deploy on Azure Local, consider built-in policies, such as denying the creation or modification of Extended Security Updates licenses. For more information, see [Built-in policy definitions for Azure Arc-enabled workloads](/azure/governance/policy/samples/built-in-policies#azure-arc).

Consider creating custom policies to provide extra governance for both the Azure Local resources and Azure Local VMs that you deploy on an Azure Local instance. For example:

- Auditing Azure Local host registration with Azure
- Ensuring that hosts run the latest OS version
- Checking for required hardware components and network configurations
- Verifying the enablement of necessary Azure services and security settings
- Confirming the installation of required extensions
- Assessing the deployment of Kubernetes clusters and AKS integration

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [Azure Local baseline reference architecture](/azure/architecture/hybrid/azure-stack-hci-baseline).

## Next steps

- Consider the following articles in Azure Architecture Center as resources that demonstrate the recommendations highlighted in this article.
    -   Foundational architecture that demonstrates the key recommendations: [Azure Local baseline reference architecture](/azure/architecture/hybrid/azure-stack-hci-baseline).
    -   If your organization needs a hybrid approach, carefully select your design choices related to a hybrid network architecture. For more information, see [Hybrid architecture design](/azure/architecture/hybrid/hybrid-start-here).

- Build implementation expertise by using the following Azure Local product documentation:
  - [Azure Local overview](/azure/azure-local/overview)
  - [Azure Local network deployment considerations](/azure/azure-local/plan/cloud-deployment-network-considerations)
  - [About Azure Local deployment](/azure/azure-local/deploy/deployment-introduction)

- Review Cloud Adoption Framework guidance:

    The Cloud Adoption Framework [Ready methodology](/azure/cloud-adoption-framework/ready/) guides customers when they prepare their environment for cloud adoption. The methodology includes technical accelerators like Azure landing zones, which are the building blocks of any Azure cloud adoption environment. Review the following articles for more details about how to prepare your environment for a hybrid cloud.

    - [Prepare your environment for hybrid and multicloud scenarios](/azure/cloud-adoption-framework/scenarios/hybrid/ready)
    - [Introduction to Azure Arc landing zone accelerator for hybrid and multicloud](/azure/cloud-adoption-framework/scenarios/hybrid/enterprise-scale-landing-zone)
