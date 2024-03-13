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

Understanding the Well-Architected Framework pillars can help produce a high-quality, stable, and efficient hybrid cloud architecture pattern. We recommend that you review your workload by using the [Azure Well-Architected Framework Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment. Consider reviewing reference architectures that reflect these considerations in their design. We recommend starting with the [baseline architecture for an Azure Stack HCI cluster](/azure/architecture/hybrid/azure-stack-robo) and [Azure Stack HCI network deployment patterns](/azure-stack/hci/plan/choose-network-pattern). Also review the [Prepare your environment for a hybrid and multi-cloud scenario](/azure/cloud-adoption-framework/scenarios/hybrid/ready), which provides an architectural approach and references to prepare landing zone subscriptions for a scalable deployment and implementation of Azure Stack HCI.

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a design checklist that presents architectural areas of concern along with design strategies localized to the scope of technology.
>
> Also included are _recommendations_ on the technological capabilities that can help materialize those strategies. The recommendations do not represent an exhaustive list of all configurations available for Azure Stack HCI clusters or workloads. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build proof-of-concepts or optimize existing environments.
>
> Foundational architecture that demonstrates the key recommendations: [Azure Stack HCI baseline architecture - TO DO: UPDATE HCI DIAGRAM in ARCHITECTURE CENTRE](/azure/architecture/hybrid/azure-stack-robo).

#### Technology scope

This review article focuses on the interrelated decisions for these Azure resources:

- Azure Stack HCI (clusters)
- Virtual Machines (workload)

This guidance does not aim to address all workload resource types, as these can have specific architecture aspects and suggestions, such as Azure Arc-enabled Kubernetes or Azure Virtual Desktop (AVD).

## Reliability

In hybrid cloud deployments, we recognize that failures are inevitable, instead of aiming to avoid every possible failure, the goal is to reduce the effects of one component failing. Use these design checklists and configuration suggestions to lessen the impact of a component failure for workloads deployed on Azure Stack HCI.

When discussing reliability for Azure Stack HCI, it is important to distinguish between _cluster reliability_ and _workload reliability_. Cluster reliability is the responsibility of cluster or platform administrators, while workload reliability has a dependency on the platform, it is the responsibility of the application owners and/or developers to design the application(s) that can deliver the defined reliability targets. These roles and responsibilities can be owned by separate teams or individuals, or belong to a single team, either way the recommendations and considerations below can be used to increase the reliability of workloads deployed on Azure Stack HCI.

The  **design checklist** and list of **recommendations** below indicate if each consideration is relevant for cluster architecture, workload architecture, or both.

#### Design checklist

> [!div class="checklist"]
>
> - **Cluster nodes architecture:** Use the [Azure Stack HCI Solution Catalog](https://aka.ms/azurestackhcicatalog/#/catalog) to gain knowledge and understanding of latest hardware OEM innovations for Azure Stack HCI cluster deployments, consider using Premium Solutions to benefit from additional integration and turn-key deployment capabilities.
>
> - **Cluster storage architecture:** Select appropriate physical disk type(s) for the cluster nodes (_NVMe, SSD, HDD..etc_) and [fault tolerance / storage efficiency options](/azure-stack/hci/concepts/fault-tolerance) within Storage Spaces Direct (S2D) based on your workload requirements. These decisions influence the performance, capacity and resiliency capabilities when [creating S2D virtual disks / volumes](/azure-stack/hci/concepts/plan-volumes), for example a 3-way mirror can increase reliability and performance. Use the [S2D Capacity Calculator Tool](https://aka.ms/s2dcalc) to help plan storage using your workload capacity requirements.
>
> - **Cluster nodes architecture:** Use the [Azure Stack HCI Sizer Tool](https://aka.ms/azurestackhcicatalog/#/sizer) during the cluster design (_pre-deployment_) phase. It is important to size Azure Stack HCI clusters appropriately using the workload capacity, performance, and resiliency requirements as inputs. This will determine the maximum number of nodes that can be offline simultaneously, such as for planned (_maintenance_) or unplanned (_power / hardware failure_) events. As a minimum requirement, plan to "reserve 1 x physical nodes (N+1) worth of capacity" across the cluster, or N+2 nodes for business or mission-critical use cases, to ensure cluster nodes can be "drained" to perform updates.
>
> - **Cluster architecture:** Use the [Environmental Checker tool in standalone mode](/azure-stack/hci/manage/use-environment-checker) to assess the readiness of the target environment prior to deploying an Azure Stack HCI solution. This will validate the required connectivity, hardware, Active Directory, network, and Arc integration prerequisites are configured correctly.
>
> - **Cluster network architecture:**  Ensure clusters are implemented using one of the [validated network topologies](/azure-stack/hci/deploy/deployment-introduction#validated-network-topologies), selecting the most appropriate design based on your requirements. Multi-node switched deployments provide increased fault tolerance when [deployed with two Top of Rack (ToR) switches per HCI cluster](/azure-stack/hci/concepts/host-network-requirements#storage-traffic-models) as network adapters for management, compute and storage networks are connected to two ToR switches, to provide resiliency during switch servicing operations.
>
> - **Workload architecture high availability:** From a conceptual perspective, consider an Azure Stack HCI cluster [a logical "_availability zone_"](/azure/well-architected/reliability/regions-availability-zones) to host and run workloads. For business or mission-critical use cases, [to provide redundancy deploy multiple instances of your workloads across two or more separate Azure Stack HCI clusters](/azure/well-architected/reliability/redundancy#key-design-strategies). Use application high availability patterns and practices, such as active / passive with synchronous or a-synchronous data replication and external network load balancers to direct user requests across multiple HCI clusters, ideally deployed in separate datacenter locations.
>
> - **Workload architecture disaster recovery:** For business or mission-critical use cases implement a [structured, tested and documented disaster recovery (DR) capability](/azure/well-architected/reliability/disaster-recovery) configured based on your workload RPO and RTO targets. This provides business continuity in the event of an unplanned outage or disaster, such as failure of a single datacenter. It is possible to use Azure Site Recovery to protect virtual machines running on Azure Stack HCI. Review [Protect VM workloads with Azure Site Recovery on Azure Stack HCI (preview)](/azure-stack/hci/manage/azure-site-recovery) for more information.
>
> - **Workload architecture backup:** Ensure workload backups are configured and tested regularly based on your workload data recovery / retention requirements, including the file and folder level restore capabilities. [Azure Backup can be used to enable host and/or guest level backups of Azure Stack HCI](/azure/backup/back-up-azure-stack-hyperconverged-infrastructure-virtual-machines), or review data protection solutions available from Backup ISV partners.
>
> - **Cluster + workload architecture:** Reliability and resiliency design requirements are application and workload type specific, for example [Arc Virtual Machines (VMs)](/azure-stack/hci/manage/create-arc-virtual-machines), [AKS enabled by Arc](/azure/aks/hybrid/scale-requirements), and [Azure Virtual Desktop (AVD)](/azure/virtual-desktop/azure-stack-hci-overview), when deploying these resource types on Azure Stack HCI, refer to the respective workload guidance to design solutions to meet your workload reliability targets.

### Recommendations

Explore the following table of recommendations to optimize your Azure Stack HCI configuration for Reliability.

| Recommendation | Benefit |
|--------|----|
|Reserve the equivalent of **[one capacity disk worth of space per node](/azure-stack/hci/concepts/plan-volumes#reserve-capacity)**, (_up to four nodes_) within the S2D storage pool.| Reserving capacity, by leaving unallocated space in the storage pool, enables S2D to repair "in-place" when a physical disk fails, which improves data resiliency and performance in the event of a physical disk failure.|
|Ensure all physical nodes have network access to the list of **[required outbound https endpoints](/azure-stack/hci/concepts/firewall-requirements)** for Azure Stack HCI and Azure Arc.| To be able to reliably manage, monitor and operate Azure Stack HCI clusters and/or workload resources, the required outbound network endpoints must be accessible. Temporary interruption will not impact the running status of the workload, but may effect manageability.|
|When creating Volumes (_virtual disks_) in Storage Spaces Direct, select the appropriate **[resiliency types](/azure-stack/hci/concepts/plan-volumes#choosing-the-resiliency-type)** to maximize workload resiliency and performance.|For clusters with three or more nodes, consider using a 3-way mirror to provide the highest resiliency and performance capabilities. The use of mirrored volumes is recommended for business or mission-critical workloads.|
|Consider implementing **[workload anti-affinity rules](/azure-stack/hci/manage/vm-affinity#anti-affinity-rule-examples)** to ensure the VMs hosting multiple instances of the same service, run on separate physical hosts.|Make all things redundant: For business or mission-critical workloads, deploy multiple instances of your applications or services using multiple virtual machines and/or kubernetes replica sets / pods. This increases resiliency in the event of an unplanned outage of a single physical node.|

For more suggestions, see [Principles of the reliability pillar](/azure/well-architected/resiliency/principles).

## Security

AKS Hybird - section below needs complete review....

Security is one of the most important aspects of any architecture. To explore how Azure Stack HCI controls can increase the security posture of your application workloads, we recommend reviewing the [Security design principles](../security/principles.md). If your Azure Stack HCI clusters will host sensitive workload that comply with the regulatory requirements of the Payment Card Industry Data Security Standard (PCI-DSS 3.2.1), review [Azure Stack HCI security considerations](/azure-stack/hci/concepts/security).

To learn about DoD Impact Level 5 (IL5) support and requirements with Azure Stack HCI, review [Azure Government IL5 isolation requirements](/azure/azure-government/documentation-government-impact-level-5#azure-hybrid-workloads-service).

When discussing security of Azure Stack HCI, it is important to distinguish between _cluster security_ and _workload security_. Cluster security is the responsibility of the platform or cluster administrators, while workload security is the responsibility of the workload admins and application developers. Azure Stack HCI has considerations and recommendations for both roles.

The **design checklist** and list of **recommendations** below indicate if each consideration is relevant for cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]
>
> - **Cluster security controls:** During the cloud based deployment of Azure Stack HCI, it is recommended to select and use the [Default Security Features and Settings](/azure-stack/hci/concepts/security-features), which enables more than 300 OS security settings, Windows Defender Application Control, Credential Guard, BitLocker encryption for the OS and data volumes and Signing for external SMB traffic that provide the highest level of security. These settings can be customized based on your organizational security requirements.
>
> - **Cluster security controls:** Use [Microsoft Defender for Cloud](/azure-stack/hci/manage/manage-security-with-defender-for-cloud) to audit and remediate your security posture of Azure Stack HCI clusters, nodes and workloads, providing a single pane of glass experience to help manage security posture.
>
> - **Cluster security controls:** Use Microsoft Defender for Servers deployed in AKS Hybrid with [Azure Sentinel](/azure/sentinel/overview) to detect and quickly respond to threats across your clusters and workloads.
>
> - **Cluster security controls:** Configure Entra ID user account membership of the [built-in roles based access control (RBAC) roles](/azure-stack/hci/manage/assign-vm-rbac-roles) to control access to VMs and VM resources on your Azure Stack HCI, such as HCI Administrator, VM Contributor and VM Reader.
>
> - **Workload security controls:** **NEEDS REVIEW**: Use VLAN based network-isolation to deploy workloads (VMs) that need to be isolated from each other across the different VLAN networks. Ensure each VLAN is configured and reachable from the management network for the hosts to communicate with the VLAN networks through the ToR (Top-of-Rack) switches or gateways.
>
> - **Workload security controls:** Use Trusted launch for virtual machines that protect against persistent and standard attacks on Gen 2 virtual machines with configurable features like secure boot and virtual Trusted Platform Module (vTPM).
>
> - **Workload security controls:** Use Azure Policy to re-use the built-in policies such as security baselines for Windows and Linux workloads, or create new custom policies for auditing security settings and assessing the compliance state of the target workloads deployed on Azure Stack HCI.

### Recommendations

Explore the following table of recommendations to optimize your Azure Stack HCI configuration for security.

|Recommendation|Benefit|
|----------------------------------|-----------|
|Use the **[Security baseline and drift controls](/azure-stack/hci/manage/manage-secure-baseline)** settings to apply and maintain security settings on cluster nodes.|This helps to protect against changes / drift, by automatically refreshing security settings every 90 minutes to enforce the intended security posture of Azure Stack HCI.|
|Use **[Manage Windows Defender Application Control](/azure-stack/hci/manage/manage-wdac)** in Azure Stack HCI|Windows Defender Application Control (WDAC) reduces the attack surface of Azure Stack HCI, use Azure Portal or PowerShell to view policy settings and control policy modes. WDAC policies help to control which drivers and apps are allowed to run on your system. |
|**[Enable Volume encryption using BitLocker](/azure-stack/hci/concepts/security-features#volume-encryption-via-bitlocker)** for data encryption-at-rest protection|BitLocker protects OS and data volumes, encrypting the cluster shared volumes (CSVs) created on the Azure Stack HCI using XTS-AES 256-bit encryption. This is a recommended default setting, enabled during cluster deployment for all the data volumes.|
|Export **[BitLocker Recovery Keys](/azure-stack/hci/manage/manage-bitlocker#get-bitlocker-recovery-keys)** to store them in a secure location, external from the cluster.|BitLocker keys may be required during specific troubleshooting or recovery actions, it is recommended to export, save and backup encrypts keys for OS and data volumes from each cluster using the 'Get-AsRecoveryKeyInfo' PowerShell cmdlet.|
|Based on your organizational requirements, consider periodically, **[changing the password associated with the deployment user identity for Azure Stack HCI](/azure-stack/hci/manage/manage-secrets-rotation)**.|The PowerShell cmdlet 'Set-azurestacklcmuserpassword' provides the ability to rotate AzureStackLCMUserCredential domain administrator credential secrets, which is the user identity that connects to cluster nodes for lifecycle management (LCM).|
|Configure **[Syslog forwarding of security events](/azure-stack/hci/concepts/security-features#syslog-forwarding-of-security-events)** to increase the security monitoring and alerting posture.|Forwarding security event data using syslog forwarding agents installed on the nodes by default, provides alerting and reporting capabilities through integration with customer-managed security information & event management (SIEM) solution.|
|Review **[Overview of Server Message Block signing](/troubleshoot/windows-server/networking/overview-server-message-block-signing)** that can be used to enhance Data-in transit protection.|SMB signing allows you to digitally sign SMB traffic between an Azure Stack HCI system and other systems. Configuring signing for external SMB traffic between the Azure Stack HCI system and others, helps to prevent relay attacks.|
|Review the **[SMB Encryption](/windows-server/storage/file-server/smb-security#smb-encryption)** setting that can be used to enhance Data-in transit protection.| "SMB Encryption for in-cluster traffic" setting encrypts traffic between servers in the cluster on your storage network.|

For more information on the security features introduced in 23H2, see [Review Security Features](/azure-stack/hci/concepts/security-features).

#### Cluster security

Enabling Microsoft Defender for Cloud to a subscription registered with hybrid resources such as Azure Stack HCI and Azure Arc based virtual machines running on Azure Stack HCI are checked for its security compliance (Foundational CSPM, Cloud Security Posture Management) using the Security benchmark policies (Microsoft cloud security benchmark) configured in Azure policy. Additionally, enabling Defender for Servers plan at a subscription level helps to assess the security at workload level (CWP, Cloud Workload Protection) such as Azure Arc enabled virtual machines running on Azure Stack HCI.  

Here are some built-in policies that are checked on the Azure Stack HCI through the Security benchmark, Update Manager and Guest Configuration:

- Machines should be configured to periodically check for missing system updates
- Machines should have a vulnerability assessment solution
- Vulnerabilities in security configuration on your Windows machines should be remediated (powered by Guest Configuration)
- Windows Defender Exploit Guard should be enabled on machines

#### Workload security

Here are some built-in policies that are checked on the Azure Arc based virtual machines running on Azure Stack HCI through the Security benchmark, Update Manager and Guest Configuration:

- Log Analytics agent should be installed on Windows-based Azure Arc-enabled machines.
- Machines should be configured to periodically check for missing system update.s
- Machines should have a vulnerability assessment solution.
- Windows Defender Exploit Guard should be enabled on machines.
- Windows servers should be configured to use secure communication protocols.

Here are some additional built-in policies you may want to enable on your Azure Stack HCI and Azure Arc based virtual machines running on Azure Stack HCI:

- [Preview]: Enable Extended Security Updates (ESUs) license to keep Windows 2012 machines protected after their support lifecycle has ended.

You can find these policies in the Azure portal under Policy > Definitions > Category: Azure Arc.

[Preview]: Configure prerequisites to enable Guest Attestation on Trusted Launch enabled VMs

You can find these policies in the Azure portal under Policy > Definitions > Category: Trusted Launch.

## Cost optimization

Cost optimization is about understanding your different configuration options and recommended best practices to reduce unnecessary expenses and improve operational efficiencies. Before you follow the guidance in this article, we recommend you review the following resources:

- [Cost optimization design principles](../cost-optimization/principles.md).

- Azure Stack HCI is priced on a per physical core basis, for on-premises clusters. If you are a Windows Server Datacenter customer with active Software Assurance (SA), you may choose to exchange core licenses to activate Azure Hybrid Benefit, which waives the Azure Stack HCI per core costs and Windows Server subscription. Review [Azure Hybrid Benefit](/windows-server/get-started/azure-hybrid-benefit) for more information.

When discussing cost optimization for Azure Stack HCI, The main cost aspect and element is purchasing the hardware for the cluster(s). After the cluster is successfully provisioned, it is important to differentiate between the _cost of licensing the cluster_, and the _cost of the workload and integrated cloud services_. Cluster resources and costs are managed by the cluster or platform administrators, while workload resources and any additional cloud services costs are managed by the application owners or developers. Azure Stack HCI has considerations and recommendations for both roles.

For cluster cost optimization, go to the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) and select **Azure Stack HCI** from the available products. You can test different configuration and payment plans in the calculator.

The **design checklist** and list of **recommendations** below indicate if each consideration is relevant for cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]
>
> - **Cluster architecture:** Use [Azure Stack Pricing](https://azure.microsoft.com/pricing/details/azure-stack/hci/) page in exploring the pricing options available to purchase Azure Stack HCI for on-premises servers. The flexible per core subscription model enables customers to optimize cost based on your needs.
>
> - **Cluster architecture:** Use Azure Stack HCI free trial offer for the first 60 days after registration for PoCs and validation purposes.
>
> - **Cluster architecture:** Turn on Azure Benefits on Azure Stack HCI to use some of the Azure exclusive workloads such as Windows Server Datacenter (Azure Edition), Extended Security Update (ESUs), Azure Policy Guest Configuration and Azure Virtual Desktop with no extra licensing cost. Review [Azure Benefits on Azure Stack HCI](/azure-stack/hci/manage/azure-benefits?tabs=wac) for more information.
>
> - **Workload architecture:** This document  [Cost governance for Azure Arc-enabled Kubernetes](/azure/cloud-adoption-framework/scenarios/hybrid/arc-enabled-kubernetes/eslz-arc-kubernetes-cost-governance) provides cost governance considerations and recommendations for you to keep in mind while using Azure Arc-enabled Kubernetes.
>
> - **Workload architecture:** [Design review checklist for Cost Optimizationing](/azure/well-architected/cost-optimization/checklist) presents a set of recommendations about cost optimization for your workload to help you achieve a high return on investment (ROI) based on the business value that your workload delivers. Cost optimization balances actual costs versus perceived value, team efficiency, focus, and effort, while meeting the defined functional and nonfunctional requirements of your workload.

### Recommendations

Explore the following table of recommendations to optimize your Azure Stack HCI configuration for cost.

| Recommendation | Benefit |
|----------------------------------|-----------|
|Use the **[Azure Hybrid Benefit for Azure Stack HCI](/azure-stack/hci/concepts/azure-hybrid-benefit-hci)** if you have Windows Server Datacenter Licenses with Software Assurance (SA).|With Azure Hybrid Benefit for Azure Stack HCI, you can maximize the value of your on-premises licenses and modernize your existing infrastructure to Azure Stack HCI at no additional cost.|
|Review the **[License Windows Server VMs on Azure Stack HCI](/azure-stack/hci/manage/vm-activate?tabs=azure-portal#windows-server-subscription)** to choose either Windows Server Subscription add-on or Bring your own license (BYOL) to license and activate the Windows Server virtual machines (VMs) for using them on Azure Stack HCI.| While, you can use any existing Windows Server licenses and activation methods that you already have, optionally, you can enable "Windows Server Subscription add-on" available for Azure Stack HCI only to subscribe Windows Server guest licenses through Azure which is charged for the total number of physical cores in the cluster.|
|Use the **[Azure verification for VMs](/azure-stack/hci/deploy/azure-verification?tabs=wac)** benefits extended to Azure Stack HCI that makes it possible for supported Azure-exclusive workloads to work outside of the cloud.|This benefit is enabled by default on Azure Stack HCI 23H2 or later. It helps to provide guarantees for the VMs to operate in other Azure environments and allows the workloads to benefit from offers that are available only in Azure, such as Extended Security Updates (ESU).|

For more suggestions, see [Principles of the cost optimization pillar](../cost-optimization/index.yml).

### Cloud efficiency

Making workloads more [sustainable and cloud efficient](../sustainability/sustainability-get-started.md), requires combining efforts around **cost optimization**, **reducing carbon emissions**, and **optimizing energy consumption**. Optimizing the application's cost is the initial step in making workloads more sustainable.

Learn how to build sustainable and efficient solutions using Azure Stack HCI, by completing [The Principles of Sustainable Software Engineering](/training/modules/sustainable-software-engineering-overview/) training module.

## Operational excellence

Monitoring and diagnostics are crucial. Not only can you measure performance statistics, but also use metrics troubleshoot and remediate issues quickly. We recommend reviewing the [Operational excellence design principles](/azure/well-architected/devops/principles) and how to [collect logs](/azure-stack/hci/manage/collect-logs) to help troubleshoot issues with Azure Stack HCI.

When discussing operational excellence with Azure Stack HCI, it's important to distinguish between _cluster operational excellence_ and _workload operational excellence_. Cluster operational practices are the responsibility of the cluster administrators, while workload operational practices are the responsibility of the workload admins and application developers. Azure Stack HCI has considerations and recommendations for both roles.

The **design checklist** and list of **recommendations** below indicate if each consideration is relevant for cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]
>
> - **Cluster configuration:** [Enable Azure Monitor integration with Azure Stack HCI](/azure-stack/hci/concepts/monitoring-overview) to enhance monitoring and alerting capabilities using Azure. This provides near real-time monitoring and there is no additional cost of using this feature.
>
> - **Cluster configuration:** [Enable Health Alerts in Azure Monitor for Azure Stack HCI](/azure-stack/hci/manage/health-alerts-via-azure-monitor-alerts) and configure the alert processing rules based on your organizational requirements. Additionally, you can [Set up log alerts for Azure Stack HCI](/azure-stack/hci/manage/setup-hci-system-alerts) that are used to perform advanced logic operations on your log data and [Set up metric alerts for Azure Stack HCI](/azure-stack/hci/manage/setup-metric-alerts) that are used to evaluate metrics of your Azure Stack HCI system at regular intervals.
>
> - **Cluster configuration:** Turn on [Azure Stack HCI Insights](/azure-stack/hci/manage/monitor-hci-single-23h2) for detailed monitoring and logs through Azure Monitor Log Analytics. Insights provides  access to default workbooks with basic metrics, along with specialized workbooks created for monitoring key features of Azure Stack HCI.
>
> - **Cluster configuration:** Observability in Azure Stack HCI is enabled by default at the time of installation and is important because it enables the collection of telemetry and diagnostic information from the system automatically using the TelemetryAndDiagnostics extension that is installed in all cluster nodes. Review [Azure Stack HCI observability](/azure-stack/hci/concepts/observability) for more information.
>
> - **Cluster configuration:** For optimizing the storage space in cluster storage volume (CSV), [Monitor ReFS deduplication and compression](/azure-stack/hci/manage/monitor-features?branch=pr-en-us-14632#monitor-refs-deduplication-and-compression) which is more suitable for general-purpose workload types such as file servers (GPFS) and backup targets.
>
> - **Cluster configuration:** For customers using DELL APEX cloud platform, you can enable [Monitor Dell APEX cloud platform](/azure-stack/hci/manage/monitor-features?branch=pr-en-us-14632#monitor-dell-apex-cloud-platform) to visualize hardware events in real-time, resulting in accelerated issue detection and resolution using the Azure Workbook from Dell. You simply need to enable Event Monitoring for Dell APEX Cloud Platform for Microsoft Azure feature.
>
> - **Workload configuration:** Ensure you have enabled monitoring and alerting for workloads deployed on Azure Stack HCI clusters. Consider using [Azure Monitor with a centralized Log Analytics workspace](/azure/cloud-adoption-framework/ready/landing-zone/design-area/management-workloads#workload-management-and-monitoring-recommendations) to gain insights from logs, metrics and to enable alerting capabilities for your workload, or use an alternative ISV monitoring solution based on your organization's requirements.
>
> - **Workload configuration** To enable workload monitoring, it is recommended to use the [Azure Arc for Servers - Azure Monitor extension](/azure/azure-arc/servers/manage-vm-extensions#extensions) for VM workload, or the [Azure Monitor Container Insights Extension for Azure Arc-enabled Kubernetes clusters](/azure/azure-arc/kubernetes/extensions-release#azure-monitor-container-insights) for kubernetes clusters.

### Recommendations

Explore the following table of recommendations to optimize your Azure Stack HCI configuration for operations.

| Recommendation | Benefit |
|--------|----|
|Configure **[Monitor Azure Stack HCI features with Insights](/azure-stack/hci/manage/monitor-features)**  to monitor either a single Azure Stack HCI cluster or multiple clusters simultaneously.|Azure Stack HCI insights provides customizable monitoring of cluster health, availability, and performance on one or more clusters. Cost will be based on the amount of data ingested and your data retention settings in Log Analytics.|
|Use **[Monitor Azure Stack HCI with Azure Monitor Metrics](/azure-stack/hci/manage/monitor-cluster-with-metrics)** which are enabled by default to collect comprehensive list of metrics for compute, storage, and network resources in Azure Stack HCI.| Key benefits of using Metrics for Azure Stack HCI are no additional cost, near real-time insights, creating own graphs and customizing through aggregation and filter functionality and configuring custom alert rules.|
|Review and configure **[Respond to Azure Stack HCI health alerts with Azure Monitor alerts](/azure-stack/hci/manage/health-alerts-via-azure-monitor-alerts)** which enhances the health alerts (built-in) capability of Azure Stack HCI.|The integration of Azure Monitor alerts with Azure Stack HCI offers several key benefits such as no additional cost, near real-time monitoring and customizing alerts to notify the right team or admin for remediation.|
|Review **[About Updates in Azure Stack HCI](/azure-stack/hci/update/whats-the-lifecycle-manager-23h2)** in release 23H2 and above which provides a flexible foundation for integrating and managing various aspects of the Azure Stack HCI solution in one place.|The update orchestrator is installed during initial cluster deployment, this automates the management of OS, core agents and services, including solution extensions.|

For more suggestions, see [Principles of the operational excellence pillar](/azure/well-architected/devops/principles).

## Performance efficiency

Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. We recommend you review the [Performance efficiency principles](/azure/well-architected/scalability/principles).

When discussing performance with Azure Stack HCI, it's important to distinguish between _cluster performance_ and _workload performance_. Cluster design and performance capabilities are the responsibility of the cluster administrators and the hardware OEM partner, while workload performance is the responsibility of the workload admins and application developers. Azure Stack HCI has considerations and recommendations for both roles.

The **design checklist** and list of **recommendations** below indicate if each consideration is relevant for cluster architecture, workload architecture, or both.

### Design checklist

As you make design choices for Azure Stack HCI, review the [Performance efficiency principles](/azure/well-architected/scalability/principles).

> [!div class="checklist"]
>
> - **Cluster architectures:** Use the Azure Stack HCI certified / validated hardware from OEM partner offerings, consider using the Premium Solution Builders available in the [Azure Stack HCI Catalog](https://aka.ms/azurestackhcicatalog#catalog) to optimize the performance of Azure Stack HCI system.
>
> - **Cluster architectures:** Use all-flash (NVMe or SSD) based solutions for workloads that have high performance / low latency requirements. This includes but not limited to, highly transactional database technologies, production Arc enabled Kubernetes clusters or any mission or business critical workloads with low latency or high performance / through-put storage requirements.
>
> - **Cluster architecture:** Baseline cluster storage performance prior to deploying production workloads. One option is to use VMFleet, which is a tool that can be used to generate load and measure the performance of a storage subsystem. Review [Using VMFleet for measuring storage subsystem performance](https://github.com/microsoft/diskspd/wiki/VMFleet) for more information.
>
> - **Cluster architecture:** Enable monitoring for ReFS Deduplication and Compression feature after enabling Insights for the Azure Stack HCI cluster. This feature helps monitoring ReFS deduplication and compression savings, performance impact and jobs. Review [Monitor ReFS deduplication and compression](/azure-stack/hci/manage/monitor-features#monitor-refs-deduplication-and-compression) for more information.

### Recommendations

Explore the following table of recommendations to optimize your Azure Stack HCI configuration for performance.

| Recommendation | Benefit |
|--------|----|
|Configure **[Monitor Azure Stack HCI features with Insights](/azure-stack/hci/manage/monitor-features)**  to monitor either a single Azure Stack HCI cluster or multiple clusters simultaneously.|Azure Stack HCI insights provides customizable monitoring of cluster health, availability, and performance on one or more clusters. Cost will be based on the amount of data ingested and your data retention settings.|
|Choose **[use an all-flash based storage](/azure-stack/hci/concepts/cache#all-flash-deployment-possibilities)** hardware configuration for running performance intensive workloads on Azure Stack HCI. | All-flash deployments to maximize storage performance. All-NVMe or all-SSD configurations (_especially at very small scale_) improves storage efficiency and maximizes performance, as no drives are used as a cache tier.|
|When **[creating S2D virtual disks, use mirroring](/azure-stack/hci/concepts/plan-volumes#when-performance-matters-most)** for performance intensive workloads.| Workloads that have strict latency requirements or that need high throughput with a mix of random read/write IOPs, such as SQL Server databases or performance-sensitive Hyper-V virtual machines. Deploy the workload VHDs on Volumes that use mirroring to maximize performance. Mirroring is faster than any other resiliency type.|
|Consider using **[DiskSpd and/or VMFleet to test workload storage performance](/azure-stack/hci/manage/diskspd-overview)** capabilities of the cluster.|Establish a baseline for cluster performance, prior to deploying production workloads.  DiskSpd allows administrators to test the storage performance of the cluster, using various command line parameters. At its core, DiskSpd simply issues read and write operations and outputs performance metrics, such as latency, throughput, and IOPs.|
|**[Monitor cluster performance with the Health Service](/azure-stack/hci/manage/health-service-overview)**, using PowerShell for alerting, cluster performance history and more.| The Health Service, improves the day-to-day monitoring and operational experience for clusters running Storage Spaces Direct. The Health Service is enabled by default with Storage Spaces Direct. No additional action is required to set it up or start it. Get live performance and capacity information from your Storage Spaces Direct cluster.|

For more suggestions, see [Principles of the performance efficiency pillar](/azure/well-architected/scalability/principles).

## Azure Policies

Azure Policy offers various built-in policy definitions that apply to both the Azure Stack HCI and Azure Arc enabled virtual machines to monitor their compliance state and enhance the security of those resources using Azure Policy and Microsoft Defender for Cloud.

To start with, review the list of built-in policies available for Azure Stack HCI, such as:  

- [Preview]: Host and VM networking should be protected on Azure Stack HCI systems.
- [Preview]: Azure Stack HCI systems should have encrypted volumes.
- [Preview]: Azure Stack HCI servers should have consistently enforced application control policies.
- [Preview]: Azure Stack HCI servers should meet Secured-core requirements.

You can find these policies in the Azure portal under Policy > Definitions > Category: Stack HCI.

For the above built-in-policies, Microsoft Defender for Cloud has added [New Recommendations for Azure Stack HCI](/azure/defender-for-cloud/upcoming-changes#four-new-recommendations-for-azure-stack-hci-resource-type) to show its compliance state.

Here are few built-in policies under Security Center category that are evaluated by enabling Defender for Cloud to a subscription that is registered with hybrid resources such as Azure Stack HCI and Azure Arc VMs:

- [Preview]: Azure Security agent should be installed on your Windows Arc machines.
- [Preview]: Azure Security agent should be installed on your Linux Arc machines.
- [Preview]: ChangeTracking extension should be installed on your Linux Arc machine.

For full details, see [Built-in policies under Security Center](/azure/governance/policy/samples/built-in-policies#security-center).

Additionally, you can review some of the built-in policies available under Azure Arc and see if it could be applied for the Arc VMs deployed on Azure Stack HCI. For details, see [built-in policy definitions for Azure Arc enabled workloads](/azure/governance/policy/samples/built-in-policies#azure-arc).

- [Preview]: Deny Extended Security Updates (ESUs) license creation or modification.
- [Preview]: Enable Extended Security Updates (ESUs) license to keep Windows 2012 machines protected after their support lifecycle has ended.

In addition to the built-in policies and definitions, custom policies can be created for both the Azure Stack HCI resources and Azure Arc VMs deployed on HCI cluster. For example, below are the items for which custom policies can be created:

- Audit if Azure Stack HCI hosts are registered with Azure.
- Audit if Azure Stack HCI hosts are running the latest version of the operating system.
- Audit if Azure Stack HCI hosts have the required hardware components.
- Audit if Azure Stack HCI hosts have the required network configuration.
- Audit if Azure Stack HCI hosts have the required Azure services enabled.
- Audit if Azure Stack HCI hosts have the required security settings.
- Audit if Azure Stack HCI hosts have the required extensions installed.
- Audit if Azure Stack HCI hosts have the required Kubernetes clusters deployed.
- Audit if Azure Stack HCI hosts have the required Azure Kubernetes Service (AKS) integration enabled.

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your Virtual Machines.

- [Reliability](/azure/advisor/advisor-high-availability-recommendations)
- [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations)
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations)
- [Operational excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations)

## Additional resources

### Azure Architecture Center guidance

Many organizations need a hybrid approach to analytics, automation, and services because their data is hosted both on premises and in the cloud. Organizations often extend on-premises data solutions to the cloud. To connect environments, organizations start by choosing a hybrid network architecture. Review [Hybrid + Multi-cloud Azure Architecture Center Guide](/azure/architecture/hybrid/hybrid-start-here) for more information on choosing the right architecture for the hybrid and multi-cloud environments.

### Cloud Adoption Framework guidance

The Cloud Adoption Framework [Ready methodology](/azure/cloud-adoption-framework/ready/) guides customers as they prepare their environment for cloud adoption. The methodology includes technical accelerators like Azure landing zones, which are the building blocks of any Azure cloud adoption environment. Review the below documents for more details on preparing your environment for hybrid cloud.

- [Prepare your environment for a hybrid and multi-cloud scenarios](/azure/cloud-adoption-framework/scenarios/hybrid/ready)
- [Introduction to Azure Arc landing zone accelerator for hybrid and multi-cloud](/azure/cloud-adoption-framework/scenarios/hybrid/enterprise-scale-landing-zone)

## Next steps

- For deploying an Azure Stack HCI cluster using Azure portal or ARM template: [About Azure Stack HCI, version 23H2 deployment](/azure-stack/hci/deploy/deployment-introduction)