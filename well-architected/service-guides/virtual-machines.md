---
title: Azure Well-Architected Framework - Virtual Machines and scale sets
description: Design considerations and recommendations about Azure virtual machines.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 01/13/2024
ms.topic: conceptual
products:
  - azure-virtual-machines
categories:
  - compute
---

# Azure Well-Architected Framework perspective on Virtual Machines and scale sets

Azure Virtual Machines is a type of compute service that allows you to create and run virtual machines on the Azure platform. There's flexibility in choosing from different SKUs, operating systems, and configurations with various billing models.

This article assumes that as an architect, you've reviewed the [**compute decision tree**](/azure/architecture/guide/technology-choices/compute-decision-tree) and chosen Virtual Machines as the compute for your workload. The guidance in this article provides architectural recommendations are mapped to the principles of the [**Azure Well-Architected Framework pillars**](../pillars.md).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a _design checklist_ that presents architectural areas of concern along with design strategies localized to the technology scope. 
>
> Also included are _recommendations_ on the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations available for Azure Virtual Machines and their dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environments. 
>
> Foundational architecture that demostrates the key recommendations: [Azure Virtual Machine baseline architecture](/azure/architecture/virtual-machines/baseline).


##### Technology scope

This review focuses on the interrelated decisions for these Azure resources.  

- Azure Virtual Machines (VMs)
- Azure Virtual Machine Scale Sets (scale sets)
- Disks are a critical dependency for virtual machine-based architectures. These considerations are given in [Azure Well-Architected Framework review - Disks](./azure-disks-cost-optimization.md).

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

##### Design checklist

Start your design strategy based on the [**design review checklist for Reliability**](../reliability/checklist.md) and determine its relevance to your business requirements while keeping in mind the SKUs and features of VMs and their dependencies. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review Azure VM quotas and limits** that might pose design restrictions. VMs have specific limits and quotas, which vary based on the type of VM, the region, and other factors. There might be subscription restrictions, such as number of VMs per subscription, the number of cores per VM, and more. If your subscription is shared with other workloads, your consumption might be reduced.
>      Check limits on [VMs](/azure/azure-resource-manager/management/azure-subscription-service-limits#virtual-machine-scale-sets-limits), [Virtual Machine Scale Sets](/azure/azure-resource-manager/management/azure-subscription-service-limits#virtual-machine-scale-sets-limits), [managed disks](/azure/azure-resource-manager/management/azure-subscription-service-limits#managed-virtual-machine-disks), and other components of the workload.
>
> - **Do failure mode analysis** to minimize points of failure, by focusing on VM interactions with the network and storage components. Opt for design choices such as ephemeral OS disks to localize disk access, avoiding network hops. Adding a load balancer can enhance self-preservation by distributing network traffic across multiple VMs, improving availability and reliability.
>
> - **Calculate your composite Service Level Objectives (SLOs) based on Azure Service Level Agreements (SLAs)**. Ensure that your SLO isn't higher than the [Azure SLAs](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) to avoid unrealistic expectations and potential issues.
>
>     Be aware of the _complexities introduced by dependencies_. For example, some dependencies like virtual network and NICs, don't have their own SLAs. Other dependencies, such as an associated data disk has SLAs that are integrated with VM SLAs. All those variations should be considered as they can impact VM performance and reliability.
>
>     _Factor in the critical dependencies_ of VMs on various components such as disks and networking components. Understanding these relationships is important for determining the critical flows that might impact reliability.
>     _Create state isolation_. Workload data should be on a separate data disk to prevent interference with the OS disk. If a VM fails, a new OS disk can be spun up with the same data disk, ensuring resilience and fault isolation. See, [Ephemeral OS disks](/azure/virtual-machines/ephemeral-os-disks).
>
> - **Make VMs and dependencies redundant across zones**. If a VM fails, the _workload should continue to function due to resiliency_. Include dependencies in your redundancy choices. For example, use built-in redundancy options available with disks, use zone redundant IPs, ensuring data availability and high uptime.
>
> - **Be ready to scale out** to prevent service level degradation and avoid failures. [Azure Virtual Machine Scale Sets](/azure/virtual-machines/flexible-virtual-machine-scale-sets) have scaling capabilities that can spin up new instances as required and distribute load across multiple VMs and availability zone.
>
> - **Explore the auto recovery options**. Azure supports health degradation monitoring and auto repair features to support self-healing for VMs. This could involve using Azure Site Recovery, having a passive standby to failover to, or redeploying from Infrastructure as Code (IAC). The chosen method should align with business requirements and organizational operations. For information, see [VM service disruptions](/azure/virtual-machines/overview#service-disruptions).
>
> - **Right-size the VMs and their dependencies**. Understand your VM's expected work to ensure it's not undersized and can handle maximum load. Have extra capacity to mitigate failures.
>
> - **Create a comprehensive disaster recovery plan**. Disaster preparedness involves creating a comprehensive plan and deciding on a technology for recovery.
>
>     Dependencies and stateful components, such as attached storage, can complicate recovery. If disks go down, it can impact the functioning of the VMs. Include a clear process for these dependencies in your recovery plans.
>
> - **Run operations with rigor**. Reliability design choices must be supported by effective operations based on the principles of _monitoring, resiliency testing in production, automated application VM patches and upgrades, and consistency of deployments_ with configuration as code. For more information, see [Operational Excellence](#operational-excellence).

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|(Scale set) **Use Azure Virtual Machine Scale Sets in [Flexible orchestration mode](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes#scale-sets-with-flexible-orchestration)** to deploy VMs. | You'll be able to future proof your application for scaling and take advantage of the high availability guarantees that spreads VMs across fault domains in a region an availability zone.|
|(VMs) **Implement heath endpoints** on VMs that emit instance health status. <br> (Scale set) **[Enable automatic repairs](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-instance-repairs)** on the scale set by specifying the preferred _repair action_. <br> Consider setting a time frame during which automatic repairs are paused if state the virtual machine is changed. This strategy can prevent inadvertent or premature repair operations.|Availability is maintained even if an instance is deemed unhealthy. Automatic repairs initiate recovery by replacing the faulty instance.|
|(Scale set) **[Enable overprovisioning](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning)** on scale sets.|Overprovisioning can reduce deployment times with the cost benefit because the extra VMs aren't billed. |
|(Scale set) Allow Flexible orchestration to **[max spread the VM instances](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes#what-has-changed-with-flexible-orchestration-mode)** fault domains.| This option gives the ability to isolate fault domains. During maintenance periods, when one fault domain is updated, VM instances are available in the other fault domains.|
|(Scale set) **[Deploy across availability zones](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#design-considerations-for-availability-zones)** on scale sets.<br> Zone balance the instances to make sure they're equally spread across zones.| The VM instances are provisioned in physically separate locations within each Azure region that are tolerant to local failures. <br> Keep in mind that there might be uneven number of instances across zones, depending on resource availability. Zone balancing supports availability by making sure if one zone is down, other zones have sufficient instances.|
|(VMs) Take advantage of the **[capacity reservations feature](/azure/virtual-machines/capacity-reservation-overview)**. |The capacity is reserved for your use and is available within the scope of applicable Service Level Agreements (SLAs). They can be deleted when no longer needed and billing is consumption based.|

>[!TIP]
>For more information on Reliability guidance for VMs, see [Reliability in Azure Virtual Machines - Azure Virtual Machines](/azure/reliability/reliability-virtual-machines).

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying the approaches to the technical design around Azure Virtual Machines.

##### Design checklist

Start your design strategy based on the [**design review checklist for Security**](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review the security baselines** for [Linux](/security/benchmark/azure/baselines/virtual-machines-linux-security-baseline), [Windows](/security/benchmark/azure/baselines/virtual-machines-windows-security-baseline) VMs and also [scale set baseline](/security/benchmark/azure/baselines/virtual-machine-scale-sets-security-baseline).
>
>     As part of your baseline technology choices, _evaluate VM SKUs that have security features_.
>
> - **Ensure timely and automated security patching and upgrades**. Make sure updates are automatically rolled out and validated with a well-defined process in place. Use a solution like [Azure Automation](/azure/automation/update-management/overview) to manage operating system updates and maintain security compliance with critical updates.
>
> - **Identify the VMs that hold state**. Make sure that _data is classified_ according to organization-provided sensitivity labels and protected through _appropriate levels of encryption_ (at rest and in transit) and other security controls. If you have high sensitivity requirements, consider using double encryption, Azure Confidential Compute to protect data in use, and other high security controls.
>
> - **Provide segmentation** to the VMs and scale sets by setting network boundaries, access controls, and also place VMs in resource groups that share the same lifecycle.
>
> - **Apply access controls on identities** trying to reach the VMs and also VMs reaching other resources. _Use Microsoft Entra ID_ for authentication and authorization needs making sure strong passwords, multi-factor authentication, and role-based access control (RBAC) are in place for your VMs (and its dependencies such as secrets) to permit allowed identities to only perform operations expected of their roles.
>
>     Restrict resource access based on conditions using Azure Entra ID Conditional Access. _Define the conditional policies_ based on duration and the minimum set of permissions.
>
> - **Use network controls to restrict ingress and egress traffic**. Place VMs and scale sets in Azure Virtual Network for isolation and put network security groups to filter traffic. Protect against distributed denial of service (DDoS) attacks and _use load balancers and firewalls rules_ to protect against malicious traffic and data exfiltration attacks.
>
>     Use [Azure Bastion](/azure/bastion/bastion-overview) provides _secure connectivity to the VMs for operational access_.
>
>     Communication to and from the VMs to PaaS services should be over _private endpoints_.
>
> - **Reduce the attack surface** by hardening OS images and removing unused components. _Use smaller images_ and remove binaries that are not required to run the workload. Additionally, _tighten VM configurations_ by removing default accounts, ports, and other settings that aren't needed.
>
> - **Protect secrets** such as certificates needed for data in transit authentication. Consider using the Azure Key Vault extension ([Windows](/azure/virtual-machines/extensions/key-vault-windows), [Linux](/azure/virtual-machines/extensions/key-vault-linux)) that provides _automatic refresh of certificates_ stored in an Key Vault. When it detects a change in the certificates, the extension retrieves and installs the corresponding certificates.
>
> - **Threat detection**. Monitor VMs for threats and misconfigurations. Use [Defender for Servers](/azure/defender-for-cloud/tutorial-enable-servers-plan) to capture VM and OS changes, and maintain an audit trail of access, new accounts, and changes in permissions.
>
> - **Threat prevention**. Protect against malware attacks and malicious actors by implementing security controls such as firewalls, anti-virus software, and intrusion detection systems.

##### Recommendations

| Recommendation | Benefit |
|--------|----|
|(Scale set) [**Assign managed identity** to scale sets](/entra/identity/managed-identities-azure-resources/qs-configure-cli-windows-vmss). All VMs in the scale set get the same identity through the specified VM profile. <br><br> (VMs) You can also [assign managed identity to individual VMs](/entra/identity/managed-identities-azure-resources/qs-configure-template-windows-vm) during creation and then add it to a scale set, if needed.| When VMs communicate with other resources, they cross a trust boundary. Scale sets and VMs should authenticate their identity before communication is allowed. With managed identities that authentication handled by Microsoft Entra ID.|
|(Scale set) **Choose VM SKUs with security features**. <br>For example, [some SKUs support for BitLocker encryption](/azure/virtual-machines/windows/disk-encryption-overview#supported-vms-and-operating-systems), Confidential Compute, and so on.|Azure-provided features are based on signals captured across numerous tenants and can protect resources better than custom controls. You can also use policies to enforce those controls.|
|(VMs, Scale set) **Apply organization-recommended tags** in the provisioned resources.|[Tagging](/azure/azure-resource-manager/management/tag-resources) is a common way to segment and organize resources and can be crucial during incident management. For more information, see [Purpose of naming and tagging](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#purpose-of-naming-and-tagging). |
|(VMs, Scale set) **Set a security profile** with security features you want to enable in the VM configuration. <br> For example, you can specify [encryption at host](/azure/virtual-machines/disks-enable-host-based-encryption-portal) in the profile, data stored on the VM host is encrypted at rest and flows are encrypted to the Storage service.|The features provided in the security profile are automatically enabled at creation. <br> For more information, see [Azure security baseline for Virtual Machine Scale Sets](/security/benchmark/azure/baselines/virtual-machine-scale-sets-security-baseline#security-profile).|
|(VMs) **Choose secure networking options** for your VM's network profile. <br><br> Don't directly associate public IPs to your VMs and don't enable IP forwarding. <br><br> Ensure all Virtual Network Interfaces have an NSG associated.|The networking profile allows you set segmentation controls. <br> Attackers scan public IPs, making VMs vulnerable to threats.|
|(VMs) **Choose secure storage options** for your VM's storage profile. <br><br> Enable disk encryption and data at rest encryption by default. Disable public network access to the VM disks.|Disabling public helps to prevent unauthorized access to your data and resources. |
|(Scale set) **Include extensions in your VMs** that protect against threats. <br> For example, <br> - Azure Key Vault extension ([Windows](/azure/virtual-machines/extensions/key-vault-windows), [Linux](/azure/virtual-machines/extensions/key-vault-linux)) <br> - [Microsoft Entra ID authentication](/entra/identity/devices/howto-vm-sign-in-azure-ad-linux) <br> - [Microsoft Antimalware Extension](/azure/virtual-machines/extensions/iaas-antimalware-windows) <br> - Azure Disk Encryption extension ([Window](/azure/virtual-machines/windows/disk-encryption-overview), [Linux](/azure/virtual-machines/linux/disk-encryption-overview)). | The extensions are used to bootstrap the VMs with the right software that protect access to and from the VMs. <br> Microsoft-provided extensions are updated frequently to keep up with the evolving security standards.|



## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making the tradeoffs necessary in the technical design related to Azure Virtual Machines and the environment in which they run.

##### Design checklist

Start your design strategy based on the [**design review checklist for Cost Optimization**](../cost-optimization/checklist.md) for investments and fine tune the design so that the workload is aligned with the budget allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Estimate realistic costs**. Use the [pricing calculator](https://azure.microsoft.com/pricing/calculator/#virtual-machines) to estimate the costs of your Azure VMs. Identify the best VM for your workloads using the virtual machines selector. For more information, see [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)and [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) pricing.
>
> - **Cost guardrails**. Use governance policies to restrict resource types, configurations, and locations. Additionally, use role-based access control to block actions that can lead to overspending.
>
> - **Choose the right resources**. You selection of VM plan sizes and SKUs have a direct impact on the overall cost. Choose VMs based on workload characteristics. Is the workload CPU intensive or does it run interruptable processes? Each SKU has associated disk options that impacts the overall cost.
>
> - **Choose the right capabilities for dependent resources**. Save on backup storage costs for the vault-standard tier using Azure Backup Storage reserved capacity. It offers a discount when you commit to a reservation for either one year or three years.
>
>     Archive tier in Azure storage is an offline tier, which is optimized for storing blob data that is rarely accessed. The archive tier offers the lowest storage costs, but higher data retrieval costs and latency compared to the online tiers (hot and cool).
>
>    Consider using [Zone to Zone disaster recovery](/azure/site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery) for virtual machines to recover from site failure while reducing the complexity of availability using zone-redundant services. There can be cost benefits from reduced operational complexity.
>
> - **Choose the right billing model**. Evaluate whether commitment-based models for compute optimize costs for based on the business requirements of workload. Consider these Azure options:
>   - **[Azure Reservations](/azure/virtual-machines/prepay-reserved-vm-instances)**: Prepay for predictable workloads and save over consumption-based pricing.
>       >[!IMPORTANT]
>       >
>       > Purchasing reserved instances is a way to reduce Azure costs for workloads with stable usage. Make sure you manage usage. If usage is too low, then you're paying for resources that aren't used. Keep reserved instances simple and keep management overhead low to prevent increasing cost.
>   - **[Azure Savings Plan](/azure/cost-management-billing/savings-plan/savings-plan-compute-overview)**: If you commit to spend a fixed hourly amount on compute services for one or three years, this plan can reduce costs.
>   - **[Azure Hybrid Benefit](/azure/cost-management-billing/scope-level/)**: Save when you migrate your on-premises VMs to Azure.
> - **Monitor usage**. Continuously monitor usage patterns and detect unused or underutilized VMs. For those instances, shut down VM instances when not in use. Monitoring is a key approach of Operational Excellence. For more information, see the recommendations given in [Operational Excellence](#operational-excellence).
>
> - **Look for ways to optimize**. Some strategies include choosing the most cost-effective approach between increasing resources in an existing system (scale up) or adding more instances of that system (scale out). Consider offloading demand by distributing demand to other resources, or you can reduce demand by implementing priority queues, gateway offloading, buffering, and rate limiting. For more information, see the recommendations given in [Performance Efficiency](#performance-efficiency).

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|(VMs, Scale set) **Choose the right VM plan size and SKU**. Identify the best [VM sizes](/azure/virtual-machines/sizes) for your workload. <br> Identify the best VM for your workloads with the virtual machines selector. See [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) and [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) pricing. <br><br> For workloads that can tolerate a certain amount of interruptibility, such as highly parallel batch processing jobs, consider [Spot VMs](/azure/virtual-machines/spot-vms). They're also well suited for experimenting, developing, and testing large-scale solutions.  | SKUs are priced according to the offered capabilities. If you don't need advanced capabilities, avoid overspending on expensive SKUs. <br><br> Spot VMs take advantage of the surplus capacity in Azure at a lower cost.|
|(VMs, Scale set) **Evaluate the [disk options](/azure/virtual-machines/disks-types)** associated with VMS SKUs. <br> Determine your performance needs keeping in mind the storage capacity needs, accounting for fluctuating workload patterns. <br> For example, Premium SSD v2 allows you to granularly adjust your performance independent of the disk’s size.|Some higher performance disk types offer extra cost optimization features and strategies <br> The Premium SSD v2 adjustment capability can reduce costs because you get high performance without overprovisioning, which could otherwise lead to underutilized resources.|
|(Scale set) **Mix regular VMs with Spot VMs**. <br> Flexible orchestration allows you to [**distribute Spot VMs**](/azure/virtual-machine-scale-sets/spot-priority-mix) based on a specified percentage. |Reduce compute infrastructure costs by applying the deep discounts of Spot VMs.|
|(Scale set) **Reduce the number of VM instances when demand decreases**. <br>[**Set a scale-in policy**](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-scale-in-policy) based on a criteria. <br> <br> Stop VMs during off-hours. You can use the [**Azure Automation Start/Stop feature**](/azure/automation/automation-solution-vm-management) and configure according to your business needs.| Scaling-in or stopping resources when not in use reduces the number of virtual machines running in the scale set, saving costs. <br> The Start/Stop feature is a suitable low-cost automation option.|
|(VMs, Scale set) **Take advantage of license mobility with Azure Hybrid Benefit (AHB)**. VMs have a licensing option that allows you to bring your own on-premises [Windows Server OS licenses](/azure/virtual-machines/windows/hybrid-use-benefit-licensing) to Azure. <br> You can utilize certain Linux subscriptions to Azure through AHB.|You can maximize your on-premises licenses while getting the benefits of the cloud.|


## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals towards the operational requirements of the workload.

##### Design checklist

Start your design strategy based on the [**design review checklist for Operational Excellence**](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure VMs and scale sets.

> [!div class="checklist"]
>
> - **Monitor the VM instances**. Collect logs and metrics from VM instances to monitor resource usage and measure the health of the instances. Common metrics include CPU usage, number of requests, I/O latency, and [others](/azure/azure-monitor/vm/vminsights-log-query). Set up Azure Monitor [alerts](/azure/virtual-network/monitor-virtual-network#alerts) to get notified about issues and for detecting configuration changes in your environment.
>
> - **Monitor health of the VMs and the dependencies**. 
>    -  Deploy monitoring components to collect logs and metrics to get a comprehensive view of your VMs, guest OS, boot data. Azure Virtual Machine Scale Sets roll up telemetry, allowing you to view health metrics at an individual VM level or as an aggregate. This data can be viewed per VM or aggregated across multiple VMs through Azure Monitor. See [Recommendations on monitoring agents](#recommendations-3).
>     - Take advantage of networking components that check the health status of VMs. For example, Azure Load Balancer pings VMs to detect unhealthy VMs and reroute traffic accordingly. 
>     - Set up Azure Monitor alert rules.  Determine important conditions in your monitoring data to identify and address issues before the system is impacted. 
>
> - **Create a maintenance plan**. that includes regular system patching is a part of routine operations. Include emergencies process that allows for immediate application of patches. You can have custom process to manage the patching and, or partially delegate it to Azure.
>    Azure provides features for individual [VM maintenance](/azure/virtual-machines/maintenance-configurations). Also, you can set up  maintenance windows to minimize disruptions during updates. During platform updates, fault domain considerations are key for resilience. If one instance per zone is needed across two zones, four instances are required in each zone. If spread across two zones, at least six instances are needed.
>
> - **Automate processes for bootstrapping, running scripts, and configuring VMs**. You can use extensions or automate through custom scripts. Here are some recommended options:
>   - [Azure Key Vault virtual machine (VM) extension](/azure/virtual-machines/extensions/key-vault-windows) that provide automatic refresh of certificates stored in an Azure Key Vault.
>
>   - [Azure Custom Script Extension](/azure/virtual-machines/extensions/custom-script-linux) (Windows and Linux) that downloads and runs scripts on Azure virtual machines (VMs). Use this extension for post-deployment configuration, software installation, or any other configuration or management task.
>   - Use cloud-init to setup the startup environment for Linux-based VMs.
>
> - **Have processes for installing automatic updates**. Consider using [Automatic VM Guest patching](/azure/virtual-machines/automatic-vm-guest-patching) for a timely rollout of critical and security patches. Use [Update Management in Azure Automation](/azure/automation/update-management/overview) to manage OS updates for your Windows and Linux virtual machines in Azure.
>
> - **Build a test environment** that closely matches your production environment to test updates and changes before deploying to production. Have processes in place to test the security updates, performance baselines, and reliability faults. Take advantage of Azure Chaos Studio fault libraries to inject and simulate error conditions. For more information, see [Azure Chaos Studio fault and action library](/azure/chaos-studio/chaos-studio-fault-library).
>
> - **Manage your quota**. Plan what level of quota will be required and review that level regularly as the workload evolves and grows and [request changes early](/azure/azure-portal/supportability/per-vm-quota-requests).

##### Recommendations

| Recommendation | Benefit |
|--------|----|
|(Scale set) **Azure Virtual Machine Scale Sets in [Flexible orchestration mode](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes#scale-sets-with-flexible-orchestration)** can help simplify the deployment and management of your workload. For example, with automatic repairs, self-healing is done with ease of management.| Flex orchestration can manage VM instances at scale. Handing individual VMs adds operational overhead. <br><br> For example, when deleting VM instances, the associated disks and NICs are also automatically deleted. VM instances are spread across multiple fault domains so that update operations don't cause disruption in service.|
|(Scale set) **Keep your VMs up-to-date** by setting an [**upgrade policy**](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-policy). Rolling upgrades are recommended, however, if you need granular control, opt for manual upgrades. <br><br> For Flex orchestration, you can use [**Update management in Azure Automation**](/azure/automation/update-management/overview).|Primary reason for upgrades is security. Security assurances for the instances shouldn't decay with time. <br><br> Rolling upgrades are done in batches, making sure all instances aren't down at the same time.|
|(VMs, Scale set) Automatically deploy VM applications from the [Azure Compute Gallery](/azure/virtual-machines/azure-compute-gallery) by defining the applications in the [**profile**](/azure/templates/microsoft.compute/virtualmachinescalesets?pivots=deployment-language-bicep#applicationprofile). |The VMs in the scale set are created and preinstalled with the specified apps, leading to easy of management.|
|[**Install pre-built software components as extensions**](/azure/virtual-machines/extensions/overview) as part of bootstrapping. <br> Azure supports many extensions that can be used to configure, monitor, secure, and provide utility applications for your virtual machines. <br><br>  [**Enable automatic upgrades**](/azure/virtual-machines/automatic-extension-upgrade)  on extensions. |Extensions can help simplify the software installation at scale without having to manually install, configure, or upgrade it on each VM.|
|(VMs, Scaleset) **Monitor and measure health** of the VM instances. <br><br> Enable [**VM insights**](/azure/azure-monitor/vm/vminsights-overview) to monitor health and performance, and view trends from the collected data.<br><br>Deploy the [**Azure Monitor Agent (AMA)**](/azure/azure-monitor/agents/agents-overview) extension to your VMs to collect monitoring data from the guest OS, with OS-specific [**Data Collection Rules (DCR)**](/azure/azure-monitor/agents/data-collection-rule-azure-monitor-agent). <br><br> Use [**boot diagnostics**](/azure/virtual-machines/boot-diagnostics) to get information as VMs boot. It also provides diagnosis of boot failures.|Monitoring data is at the core of any incident resolution. A comprehensive monitoring stack provides information about how the VMs are performing and their health. By continuously monitoring the instances, you can be ready or prevent failures occurring from performance overload, reliability issues, and other failures.|


## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [**design review checklist for Performance Efficiency**](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for Azure VMs and scale sets.

> [!div class="checklist"]
>
> - **Define performance targets**. Identify VM metrics to track and measure against performance indicators as response time, CPU utilization, and memory utilization, as well as workload metrics such as transactions per second, concurrent users, and availability and health.
>
> - **Factor in the performance profile of VMs, scale sets, and disk configuration in your capacity planning**. Each SKU has a different profile of memory and CPU and behaves differently depending on the type of workload. Conduct pilots and proof of concepts to understand performance behavior under the specific workload.
>
> - **VM performance tuning**. Take advantage of VMs performance optimization and enhancing features as required by the workload. For example, locally attached NVMe for high performance use cases, accelerated networking, and Premium SSD v2 for  better performance and scalability.
>
> - **Take into account the dependent services**. Workload dependencies that interact with the VMs can impact performance. For example, caching, network traffic, and CDN. Also consider  geographical distribution (zones, regions), which can add latency.
>
> - **Collect performance data**. Follow the [Operational Excellence best practices](#operational-excellence) for monitoring and deploy the appropriate extensions to view metrics that track against performance indicators.

##### Recommendations

| Recommendation | Benefit |
|--------|----|
|(VMs, Scale set) [**Choose SKUs for VMs**](/azure/virtual-machines/sizes) that align with your capacity planning. <br><br>Have a good understanding of your workload requirements, including the number of cores, memory, storage, and network bandwidth so that you can filter out unsuitable SKUs.|Right sizing your VMs is a fundamental decision that can have a significant impact on the performance of your workload. Without the right set of VMs, you can experience performance issues and accrue unnecessary costs.|
|(VMs, Scale set)  Deploy VMs in [**proximity placement groups**](/azure/virtual-machine-scale-sets/proximity-placement-groups). | Proximity placement groups reduce the physical distance between Azure compute resources, which can improve performance and reduce network latency between stand-alone VMs, VMs in multiple availability sets, or multiple scale sets. |
|(VMs, Scale set)  Set the [**storage profile**](/azure/virtual-machines/disks-types) by analyzing disk performance of existing workloads and the chosen VM SKU. <br><br> Use Azure [premium SSDs](/azure/virtual-machines/disks-performance-tiers) for production VMs. Adjust the performance of disks with Premium SSD v2. <br><br>Use locally attached NVMe devices.|Premium SSDs deliver high-performance and low-latency disk support VMs with IO-intensive workloads. <br> Premium SSD v2 doesn't require disk resizing enabling high performance without excessive over-provisioning and minimizing the cost of unused capacity. <br><br> When available on VM SKUs, locally attached NVMe or similar devices can offer high performance, especially for use cases requiring high IOPS and low latency.|
| (VMs) Consider enabling [Accelerated networking](/azure/virtual-network/accelerated-networking-overview).| It enables single root I/O virtualization (SR-IOV) to a VM, greatly improving its networking performance. |
|(VMs, Scale set) [**Set autoscale rules**](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-portal) to increase or decrease the number of VM instances in your scale set based on demand.| If your application demand increases, the load on the VM instances in your scale set increases. If this increased load is consistent, autoscale rules ensure that you have enough resources to meet the demand. |


## Azure policies

Azure provides an extensive set of built-in policies related to Azure VMs and the dependencies. Some of the preceding recommendations can be audited through Azure Policies. For example, you can check if:

- Encryption at host is enabled.
- Anti-malware extensions are deployed and enabled for automatic updates on Windows server VMs.
- Automatic OS image patching on scale sets is enabled
- Only approved virtual machine extensions are installed.
- Azure Monitor Agent and the Dependency agents are enabled on new virtual machines in your Azure environment.  
- Only the allowed VM SKUs are deployed to limit sizes according to cost constraints.


For comprehensive governance, review the [Azure Policy built-in definitions for Azure Virtual Machines](/azure/virtual-machines/policy-reference) and other policies that might impact the security of the compute layer.

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your Virtual Machines.

- [Reliability](/azure/advisor/advisor-high-availability-recommendations)
- [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations)
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations)
- [Operational excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations)


## Next steps

Consider these articles as resources that demonstrate the  recommendations highlighted in this article.

- Use these reference architectures as examples of how these recommendations can be applied to a workload.
  - Single VM architectures: [Linux VM](/azure/architecture/reference-architectures/n-tier/linux-vm#architecture) and [Windows VM](/azure/architecture/reference-architectures/n-tier/windows-vm#architecture).
  - Foundational architecture that focuses on infrastructure recommendations: [Azure virtual machine baseline architecture](/azure/architecture/virtual-machines/baseline).
- Build implementation expertise using product documentation: 
  - [Azure Virtual Machines](/azure/virtual-machines/)
  - [Azure Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets/overview)
