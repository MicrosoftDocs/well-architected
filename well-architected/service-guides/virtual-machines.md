---
title: Azure Well-Architected Framework perspective on Virtual Machines and scale sets
description: See Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure Virtual Machines.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 02/20/2024
ms.topic: conceptual
products:
  - azure-virtual-machines
categories:
  - compute
---

# Azure Well-Architected Framework perspective on Virtual Machines and scale sets

Azure Virtual Machines is a type of compute service that you can use to create and run virtual machines (VMs) on the Azure platform. It offers flexibility in different SKUs, operating systems, and configurations with various billing models.

This article assumes that as an architect you've reviewed the [compute decision tree](/azure/architecture/guide/technology-choices/compute-decision-tree) and chose Virtual Machines as the compute service for your workload. 
The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](../pillars.md).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope. 
>
> Also included are *recommendations* on the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations available for Virtual Machines and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environments. 
>
> Foundational architecture that demonstrates the key recommendations: [Virtual Machines baseline architecture](/azure/architecture/virtual-machines/baseline).

### Technology scope

This review focuses on the interrelated decisions for the following Azure resources:  

- Virtual Machines
- Azure Virtual Machine Scale Sets
- Disks 
    
  Disks are a critical dependency for VM-based architectures. For more information, see [Disks and optimization](/azure/well-architected/service-guides/azure-disks-cost-optimization).

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Design checklist

Start your design strategy based on the [**design review checklist for Reliability**](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the SKUs and features of VMs and their dependencies. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review Virtual Machines quotas and limits** that might pose design restrictions. VMs have specific limits and quotas, which vary based on the type of VM or the region. There might be subscription restrictions, such as the number of VMs per subscription or the number of cores per VM. If other workloads share your subscription, then your ability to consume data might be reduced.
>      Check limits on [VMs](/azure/azure-resource-manager/management/azure-subscription-service-limits#virtual-machine-scale-sets-limits), [virtual machine scale sets](/azure/azure-resource-manager/management/azure-subscription-service-limits#virtual-machine-scale-sets-limits), and [managed disks](/azure/azure-resource-manager/management/azure-subscription-service-limits#managed-virtual-machine-disks).
>
> - **Conduct a failure mode analysis** to minimize points of failure by analyzing VM interactions with the network and storage components. Choose configurations like ephemeral operating system (OS) disks to localize disk access and avoid network hops. Add a load balancer to enhance self-preservation by distributing network traffic across multiple VMs, which improves availability and reliability.
>
> - **Calculate your composite service-level objectives (SLOs) based on Azure service-level agreements (SLAs)**. Ensure that your SLO isn't higher than the [Azure SLAs](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) to avoid unrealistic expectations and potential issues.
>
>     Be aware of the complexities that dependencies introduce. For example, some dependencies, like virtual networks and network interface cards (NICs), don't have their own SLAs. Other dependencies, such as an associated data disk, have SLAs that integrate with VM SLAs. You should consider these variations because they can affect VM performance and reliability.
>
>     Factor in the critical dependencies of VMs on components like disks and networking components. If you understand these relationships, then you can determine the critical flows that affect reliability.
>
> - **Create state isolation**. Workload data should be on a separate data disk to prevent interference with the OS disk. If a VM fails, you can create a new OS disk with the same data disk, which ensures resilience and fault isolation. For more information, see [Ephemeral OS disks](/azure/virtual-machines/ephemeral-os-disks).
>
> - **Make VMs and their dependencies redundant across zones**. If a VM fails, the workload should continue to function because of redundancy. Include dependencies in your redundancy choices. For example, use the built-in redundancy options that are available with disks. Use zone-redundant IPs to ensure data availability and high uptime.
>
> - **Be ready to scale up and scale out** to prevent service level degradation and to avoid failures. [Virtual Machine Scale Sets](/azure/virtual-machines/flexible-virtual-machine-scale-sets) have autoscale capabilities that create new instances as required and distribute the load across multiple VMs and availability zones.
>
> - **Explore the automatic recovery options**. Azure supports health degradation monitoring and self-healing features for VMs. For example, scale sets provide [automatic instance repairs](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-instance-repairs). In more advanced scenarios, self-healing involves using Azure Site Recovery, having a passive standby to fail over to, or redeploying from infrastructure as code (IaC). The method that you choose should align with the business requirements and your organizational operations. For more information, see [VM service disruptions](/azure/virtual-machines/overview#service-disruptions).
>
> - **Rightsize the VMs and their dependencies**. Understand your VM's expected work to ensure it's not undersized and can handle the maximum load. Have extra capacity to mitigate failures.
>
> - **Create a comprehensive disaster recovery plan**. Disaster preparedness involves creating a comprehensive plan and deciding on a technology for recovery.
>
>     Dependencies and stateful components, such as attached storage, can complicate recovery. If disks go down, then that failure affects the VM's functioning. Include a clear process for these dependencies in your recovery plans.
>
> - **Run operations with rigor**. Reliability design choices must be supported by effective operations based on the principles of monitoring, resiliency testing in production, automated application VM patches and upgrades, and consistency of deployments. For operational guidance, see [Operational Excellence](#operational-excellence).

### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|(Scale set) **Use Virtual Machine Scale Sets in [Flexible orchestration mode](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes#scale-sets-with-flexible-orchestration)** to deploy VMs. | Future-proof your application for scaling and take advantage of the high availability guarantees that spread VMs across fault domains in a region or an availability zone.|
|(VMs) **Implement heath endpoints** that emit instance health statuses on VMs. <br><br> (Scale set) **[Enable automatic repairs](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-instance-repairs)** on the scale set by specifying the preferred repair action. <br> Consider setting a time frame during which automatic repairs pause if the VM's state changes. |Maintain availability even if an instance is deemed unhealthy. Automatic repairs initiate recovery by replacing the faulty instance. <br><br>Setting a time window can prevent inadvertent or premature repair operations.|
|(Scale set) **[Enable overprovisioning](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning)** on scale sets.|Overprovisioning reduces deployment times and has a cost benefit because the extra VMs aren't billed. |
|(Scale set) Allow Flexible orchestration to **[spread the VM instances](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes#what-has-changed-with-flexible-orchestration-mode)** across as many fault domains as possible.| This option isolates fault domains. During maintenance periods, when one fault domain is updated, VM instances are available in the other fault domains.|
|(Scale set) **[Deploy across availability zones](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#design-considerations-for-availability-zones)** on scale sets. Set up at least two instances in each zone.<br> [**Zone balancing**](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#zone-balancing) equally spreads the instances across zones.| The VM instances are provisioned in physically separate locations within each Azure region that are tolerant to local failures. <br> Keep in mind that, depending on resource availability, there might be an uneven number of instances across zones. Zone balancing supports availability by making sure that, if one zone is down, the other zones have sufficient instances. <br> Two instances in each zone provide a buffer during upgrades.|
|(VMs) Take advantage of the **[capacity reservations feature](/azure/virtual-machines/capacity-reservation-overview)**. |Capacity is reserved for your use and is available within the scope of the applicable SLAs. You can delete capacity reservations when you no longer need them, and billing is consumption based.|


> [!TIP]
> For more information on Reliability for VMs, see [Reliability in Virtual Machines](/azure/reliability/reliability-virtual-machines).

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Virtual Machines.

### Design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md). Identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review the security baselines** for [Linux](/security/benchmark/azure/baselines/virtual-machines-linux-security-baseline) and [Windows](/security/benchmark/azure/baselines/virtual-machines-windows-security-baseline) VMs and [Virtual Machine Scale Sets](/security/benchmark/azure/baselines/virtual-machine-scale-sets-security-baseline).
>
>     As part of your baseline technology choices, consider the security features of the VM SKUs that support your workload.
>
> - **Ensure timely and automated security patching and upgrades**. Make sure updates are automatically rolled out and validated by using a well-defined process. Use a solution like [Azure Automation](/azure/automation/update-management/overview) to manage OS updates and maintain security compliance by making critical updates.
>
> - **Identify the VMs that hold state**. Make sure that data is classified according to the sensitivity labels that your organization provided. Protect data by using security controls like appropriate levels of at-rest and in-transit encryption. If you have high sensitivity requirements, consider using high-security controls like double encryption and Azure confidential computing to protect data-in-use.
>
> - **Provide segmentation** to the VMs and scale sets by setting network boundaries and access controls. Place VMs in resource groups that share the same lifecycle.
>
> - **Apply access controls to the identities** that try to reach the VMs and also to the VMs that reach other resources. Use Microsoft Entra ID for authentication and authorization needs. Put strong passwords, multifactor authentication, and role-based access control (RBAC) in place for your VMs and their dependencies, like secrets, to permit allowed identities to perform only the operations that are expected of their roles.
>
>     Restrict resource access based on conditions by using Microsoft Entra Conditional Access. Define the conditional policies based on duration and the minimum set of required permissions.
>
> - **Use network controls to restrict ingress and egress traffic**. Isolate VMs and scale sets in Azure Virtual Network and define network security groups to filter traffic. Protect against distributed denial of service (DDoS) attacks. Use load balancers and firewall rules to protect against malicious traffic and data exfiltration attacks.
>
>     Use [Azure Bastion](/azure/bastion/bastion-overview) to provide secure connectivity to the VMs for operational access.
>
>     Communication to and from the VMs to platform as a service (PaaS) solutions should be over private endpoints.
>
> - **Reduce the attack surface** by hardening OS images and removing unused components. Use smaller images and remove binaries that aren't required to run the workload. Tighten the VM configurations by removing features, like default accounts and ports, that you don't need.
>
> - **Protect secrets** such as the certificates that you need to protect data in transit. Consider using the Azure Key Vault extension for [Windows](/azure/virtual-machines/extensions/key-vault-windows) or [Linux](/azure/virtual-machines/extensions/key-vault-linux) that automatically refreshes the certificates stored in a key vault. When it detects a change in the certificates, the extension retrieves and installs the corresponding certificates.
>
> - **Threat detection**. Monitor VMs for threats and misconfigurations. Use [Defender for Servers](/azure/defender-for-cloud/tutorial-enable-servers-plan) to capture VM and OS changes, and maintain an audit trail of access, new accounts, and changes in permissions.
>
> - **Threat prevention**. Protect against malware attacks and malicious actors by implementing security controls like firewalls, antivirus software, and intrusion detection systems. Determine if a [Trusted Execution Environment (TEE)](/azure/confidential-computing/trusted-execution-environment) is required.

### Recommendations

| Recommendation | Benefit |
|--------|----|
|(Scale set) [**Assign a managed identity** to scale sets](/entra/identity/managed-identities-azure-resources/qs-configure-cli-windows-vmss). All VMs in the scale set get the same identity through the specified VM profile. <br><br> (VMs) You can also [assign a managed identity to individual VMs](/entra/identity/managed-identities-azure-resources/qs-configure-template-windows-vm) when you create them and then add it to a scale set if needed.| When VMs communicate with other resources, they cross a trust boundary. Scale sets and VMs should authenticate their identity before communication is allowed. Microsoft Entra ID handles that authentication by using managed identities.|
|(Scale set) **Choose VM SKUs with security features**. <br>For example, [some SKUs support BitLocker encryption](/azure/virtual-machines/windows/disk-encryption-overview#supported-vms-and-operating-systems), and [confidential computing](/azure/confidential-computing/overview) provides encryption of data-in-use. <br> Review the features to understand the limitations. |Azure-provided features are based on signals that are captured across many tenants and can protect resources better than custom controls. You can also use policies to enforce those controls.|
|(VMs, scale set) **Apply organization-recommended tags** in the provisioned resources.|[Tagging](/azure/azure-resource-manager/management/tag-resources) is a common way to segment and organize resources and can be crucial during incident management. For more information, see [Purpose of naming and tagging](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#purpose-of-naming-and-tagging). |
|(VMs, scale set) **Set a security profile** with the security features that you want to enable in the VM configuration. <br> For example, when you specify [encryption at host](/azure/virtual-machines/disks-enable-host-based-encryption-portal) in the profile, the data that's stored on the VM host is encrypted at rest and flows are encrypted to the storage service.|The features in the security profile are automatically enabled when the VM is created. <br> For more information, see [Azure security baseline for Virtual Machine Scale Sets](/security/benchmark/azure/baselines/virtual-machine-scale-sets-security-baseline#security-profile).|
|(VMs) **Choose secure networking options** for your VM's network profile. <br><br> Don't directly associate public IP addresses to your VMs and don't enable IP forwarding. <br><br> Ensure that all virtual network interfaces have an associated network security group.|You can set segmentation controls in the networking profile. <br> Attackers scan public IP addresses, which makes VMs vulnerable to threats.|
|(VMs) **Choose secure storage options** for your VM's storage profile. <br><br> [Enable disk encryption](/azure/security/fundamentals/azure-disk-encryption-vms-vmss) and data-at-rest encryption by default. Disable public network access to the VM disks.|Disabling public network access helps prevent unauthorized access to your data and resources. |
|(VMs, scale set) **Include extensions in your VMs** that protect against threats. <br> For example, <br> - Key Vault extension for [Windows](/azure/virtual-machines/extensions/key-vault-windows) and [Linux](/azure/virtual-machines/extensions/key-vault-linux) <br> - [Microsoft Entra ID authentication](/entra/identity/devices/howto-vm-sign-in-azure-ad-linux) <br> - [Microsoft Antimalware for Azure Cloud Services and Virtual Machines](/azure/virtual-machines/extensions/iaas-antimalware-windows) <br> - Azure Disk Encryption extension for [Windows](/azure/virtual-machines/windows/disk-encryption-overview) and [Linux](/azure/virtual-machines/linux/disk-encryption-overview). | The extensions are used to bootstrap the VMs with the right software that protects access to and from the VMs. <br> Microsoft-provided extensions are updated frequently to keep up with the evolving security standards.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Virtual Machines and its environment.

### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Estimate realistic costs**. Use the [pricing calculator](https://azure.microsoft.com/pricing/calculator/#virtual-machines) to estimate the costs of your VMs. Identify the best VM for your workload by using the VM selector. For more information, see [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) and [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) pricing.
>
> - **Implement cost guardrails**. Use governance policies to restrict resource types, configurations, and locations. Use RBAC to block actions that can lead to overspending.
>
> - **Choose the right resources**. Your selection of VM plan sizes and SKUs directly affect the overall cost. Choose VMs based on workload characteristics. Is the workload CPU intensive or does it run interruptible processes? Each SKU has associated disk options that affect the overall cost.
>
> - **Choose the right capabilities for dependent resources**. Save on backup storage costs for the vault-standard tier by using Azure Backup storage with reserved capacity. It offers a discount when you commit to a reservation for either one year or three years.
>
>     The archive tier in Azure Storage is an offline tier that's optimized for storing blob data that's rarely accessed. The archive tier offers the lowest storage costs but higher data retrieval costs and latency compared to the hot and cool online tiers.
>
>    Consider using [zone to zone disaster recovery](/azure/site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery) for VMs to recover from site failure while reducing the complexity of availability by using zone-redundant services. There can be cost benefits from reduced operational complexity.
>
> - **Choose the right billing model**. Evaluate whether commitment-based models for computing optimize costs based on the business requirements of workload. Consider these Azure options:
>   - **[Azure reservations](/azure/virtual-machines/prepay-reserved-vm-instances)**: Prepay for predictable workloads to reduce costs compared to consumption-based pricing.
>       >[!IMPORTANT]
>       >
>       > Purchase reserved instances to reduce Azure costs for workloads that have stable usage. Manage usage to make sure that you're not paying for more resources than you're using. Keep reserved instances simple and keep management overhead low to reduce costs.
>   - **[Savings plan](/azure/cost-management-billing/savings-plan/savings-plan-compute-overview)**: If you commit to spend a fixed hourly amount on compute services for one or three years, then this plan can reduce costs.
>   - **[Azure Hybrid Benefit](/azure/cost-management-billing/scope-level/)**: Save when you migrate your on-premises VMs to Azure.
> - **Monitor usage**. Continuously monitor usage patterns and detect unused or underutilized VMs. For those instances, shut down VM instances when they're not in use. Monitoring is a key approach of Operational Excellence. For more information, see the recommendations in [Operational Excellence](#operational-excellence).
>
> - **Look for ways to optimize**. Some strategies include choosing the most cost-effective approach between increasing resources in an existing system, or scaling up, and adding more instances of that system, or scaling out. You can offload demand by distributing it to other resources, or you can reduce demand by implementing priority queues, gateway offloading, buffering, and rate limiting. For more information, see the recommendations in [Performance Efficiency](#performance-efficiency).

### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|(VMs, scale set) **Choose the right VM plan size and SKU**. Identify the best [VM sizes](/azure/virtual-machines/sizes) for your workload. <br> Use the VM selector to identify the best VM for your workload. See [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) and [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) pricing. <br><br> For workloads like highly parallel batch processing jobs that can tolerate some interruptions, consider using [Azure Spot Virtual Machines](/azure/virtual-machines/spot-vms). Spot virtual machines are good for experimenting, developing, and testing large-scale solutions.  | SKUs are priced according to the capabilities that they offer. If you don't need advanced capabilities, don't overspend on SKUs. <br><br> Spot virtual machines take advantage of the surplus capacity in Azure at a lower cost.|
|(VMs, scale set) **Evaluate the [disk options](/azure/virtual-machines/disks-types)** that are associated with your VM's SKUs. <br> Determine your performance needs while keeping in mind your storage capacity needs and accounting for fluctuating workload patterns. <br> For example, the Azure Premium SSD v2 disk allows you to granularly adjust your performance independent of the disk's size.|Some high-performance disk types offer extra cost optimization features and strategies. <br> The Premium SSD v2 disk's adjustment capability can reduce costs because it provides high performance without overprovisioning, which could otherwise lead to underutilized resources.|
|(Scale set) **Mix regular VMs with spot virtual machines**. <br> Flexible orchestration lets you [**distribute spot virtual machines**](/azure/virtual-machine-scale-sets/spot-priority-mix) based on a specified percentage. |Reduce compute infrastructure costs by applying the deep discounts of spot virtual machines.|
|(Scale set) **Reduce the number of VM instances when demand decreases**. <br>[**Set a scale-in policy**](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-scale-in-policy) based on criteria. <br> <br> Stop VMs during off-hours. You can use the [**Azure Automation Start/Stop feature**](/azure/automation/automation-solution-vm-management) and configure it according to your business needs.| Scaling in or stopping resources when they're not in use reduces the number of VMs running in the scale set, which saves costs. <br> The Start/Stop feature is a low-cost automation option.|
|(VMs, scale set) **Take advantage of license mobility by using Azure Hybrid Benefit**. VMs have a licensing option that allows you to bring your own on-premises [Windows Server OS licenses](/azure/virtual-machines/windows/hybrid-use-benefit-licensing) to Azure. <br> Azure Hybrid Benefit also lets you bring certain Linux subscriptions to Azure.|You can maximize your on-premises licenses while getting the benefits of the cloud.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Virtual Machines and scale sets.

> [!div class="checklist"]
>
> - **Monitor the VM instances**. Collect logs and metrics from VM instances to monitor resource usage and measure the health of the instances. Some common [metrics](/azure/azure-monitor/vm/vminsights-log-query) include CPU usage, number of requests, and input/output (I/O) latency. Set up Azure Monitor [alerts](/azure/virtual-network/monitor-virtual-network#alerts) to be notified about issues and to detect configuration changes in your environment.
>
> - **Monitor the health of the VMs and their dependencies**. 
>    -  Deploy monitoring components to collect logs and metrics that give a comprehensive view of your VMs, guest OS, and boot diagnostics data. Virtual Machine Scale Sets roll up telemetry, which allows you to view health metrics at an individual VM level or as an aggregate. Use Azure Monitor to view this data per VM or aggregated across multiple VMs. For more information, see [Recommendations on monitoring agents](#recommendations-3).
>     - Take advantage of networking components that check the health status of VMs. For example, Azure Load Balancer pings VMs to detect unhealthy VMs and reroute traffic accordingly. 
>     - Set up Azure Monitor alert rules. Determine important conditions in your monitoring data to identify and address issues before they affect the system. 
>
> - **Create a maintenance plan** that includes regular system patching as a part of routine operations. Include emergency processes that allow for immediate patch application. You can have custom processes to manage patching or partially delegate the task to Azure.
>    Azure provides features for individual [VM maintenance](/azure/virtual-machines/maintenance-configurations). You can set up maintenance windows to minimize disruptions during updates. During platform updates, fault domain considerations are key for resilience. We recommend that you deploy at least two instances in a zone. Two VMs per zone guarantees a minimum of one VM in each zone because only one fault domain in a zone is updated at a time. So, for three zones, provision at least six instances.
>
> - **Automate processes for bootstrapping, running scripts, and configuring VMs**. You can automate processes by using extensions or custom scripts. We recommend the following options:
>   - The [Key Vault VM extension](/azure/virtual-machines/extensions/key-vault-windows) automatically refreshes certificates that are stored in a key vault.
>
>   - The [Azure Custom Script Extension](/azure/virtual-machines/extensions/custom-script-linux) for Windows and Linux downloads and runs scripts on Virtual Machines. Use this extension for post-deployment configuration, software installation, or any other configuration or management task.
>   - Use cloud-init to set up the startup environment for Linux-based VMs.
>
> - **Have processes for installing automatic updates**. Consider using [Automatic VM guest patching](/azure/virtual-machines/automatic-vm-guest-patching) for a timely rollout of critical patches and security patches. Use [Update Management in Azure Automation](/azure/automation/update-management/overview) to manage OS updates for your Windows and Linux VMs in Azure.
>
> - **Build a test environment** that closely matches your production environment to test updates and changes before you deploy them to production. Have processes in place to test the security updates, performance baselines, and reliability faults. Take advantage of Azure Chaos Studio fault libraries to inject and simulate error conditions. For more information, see [Azure Chaos Studio fault and action library](/azure/chaos-studio/chaos-studio-fault-library).
>
> - **Manage your quota**. Plan what level of quota your workload requires and review that level regularly as the workload evolves. If you need to increase or decrease your quota, [request those changes early](/azure/azure-portal/supportability/per-vm-quota-requests).

### Recommendations

| Recommendation | Benefit |
|--------|----|
|(Scale set) **Virtual Machine Scale Sets in [Flexible orchestration mode](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes#scale-sets-with-flexible-orchestration)** can help simplify the deployment and management of your workload. For example, you can easily manage self-healing by using automatic repairs.| Flexible orchestration can manage VM instances at scale. Handing individual VMs adds operational overhead. <br><br> For example, when you delete VM instances, the associated disks and NICs are also automatically deleted. VM instances are spread across multiple fault domains so that update operations don't disrupt service.|
|(Scale set) **Keep your VMs up to date** by setting an [**upgrade policy**](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-policy). We recommend rolling upgrades. However, if you need granular control, choose to upgrade manually. <br><br> For Flexible orchestration, you can use [**Update management in Azure Automation**](/azure/automation/update-management/overview).|Security is the primary reason for upgrades. Security assurances for the instances shouldn't decay over time. <br><br> Rolling upgrades are done in batches, which ensures all instances aren't down at the same time.|
|(VMs, scale set) Automatically deploy VM applications from the [Azure Compute Gallery](/azure/virtual-machines/azure-compute-gallery) by defining the applications in the [**profile**](/azure/templates/microsoft.compute/virtualmachinescalesets?pivots=deployment-language-bicep#applicationprofile). |The VMs in the scale set are created and the specified apps are preinstalled, which makes management easier.|
|[**Install prebuilt software components as extensions**](/azure/virtual-machines/extensions/overview) as part of bootstrapping. <br> Azure supports many extensions that can be used to configure, monitor, secure, and provide utility applications for your VMs. <br><br>  [**Enable automatic upgrades**](/azure/virtual-machines/automatic-extension-upgrade) on extensions. |Extensions can help simplify the software installation at scale without you having to manually install, configure, or upgrade it on each VM.|
|(VMs, scale set) **Monitor and measure the health** of the VM instances. <br><br> Deploy the [**Monitor agent**](/azure/azure-monitor/agents/agents-overview) extension to your VMs to collect monitoring data from the guest OS with OS-specific [**data collection rules**](/azure/azure-monitor/agents/data-collection-rule-azure-monitor-agent). <br><br> Enable [**VM insights**](/azure/azure-monitor/vm/vminsights-overview) to monitor health and performance and to view trends from the collected data.<br><br> Use [**boot diagnostics**](/azure/virtual-machines/boot-diagnostics) to get information as VMs boot. Boot diagnostics also diagnose boot failures.|Monitoring data is at the core of incident resolution. A comprehensive monitoring stack provides information about how the VMs are performing and their health. By continuously monitoring the instances, you can be ready for or prevent failures like performance overload and reliability issues.|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Virtual Machines and scale sets.

> [!div class="checklist"]
>
> - **Define performance targets**. Identify VM metrics to track and measure against performance indicators as response time, CPU utilization, and memory utilization, as well as workload metrics such as transactions per second, concurrent users, and availability and health.
>
> - **Factor in the performance profile of VMs, scale sets, and disk configuration in your capacity planning**. Each SKU has a different profile of memory and CPU and behaves differently depending on the type of workload. Conduct pilots and proofs of concept to understand performance behavior under the specific workload.
>
> - **VM performance tuning**. Take advantage of performance optimization and enhancing features as required by the workload. For example, use locally attached Non-Volatile Memory Express (NVMe) for high performance use cases and accelerated networking, and use Premium SSD v2 for better performance and scalability.
>
> - **Take the dependent services into account**. Workload dependencies, like caching, network traffic, and content delivery networks, that interact with the VMs can affect performance. Also, consider geographical distribution, like zones and regions, which can add latency.
>
> - **Collect performance data**. Follow the [Operational Excellence best practices](#operational-excellence) for monitoring and deploy the appropriate extensions to view metrics that track against performance indicators.
>
> - **Proximity placement groups**. Use [proximity placement groups](/azure/virtual-machine-scale-sets/proximity-placement-groups) in workloads where low latency is required to ensure that VMs are physically located close to each other.

### Recommendations

| Recommendation | Benefit |
|--------|----|
|(VMs, scale set) [**Choose SKUs for VMs**](/azure/virtual-machines/sizes) that align with your capacity planning. <br><br>Have a good understanding of your workload requirements, including the number of cores, memory, storage, and network bandwidth so that you can filter out unsuitable SKUs.|Rightsizing your VMs is a fundamental decision that significantly affects the performance of your workload. Without the right set of VMs, you might experience performance issues and accrue unnecessary costs.|
|(VMs, scale set) Deploy latency-sensitive workload VMs in [**proximity placement groups**](/azure/virtual-machine-scale-sets/proximity-placement-groups). | Proximity placement groups reduce the physical distance between Azure compute resources, which can improve performance and reduce network latency between stand-alone VMs, VMs in multiple availability sets, or VMs in multiple scale sets. |
|(VMs, scale set)  Set the [**storage profile**](/azure/virtual-machines/disks-types) by analyzing the disk performance of existing workloads and the VM SKU. <br><br> Use [Premium SSDs](/azure/virtual-machines/disks-performance-tiers) for production VMs. Adjust the performance of disks with Premium SSD v2. <br><br>Use locally attached NVMe devices.|Premium SSDs deliver high-performance and low-latency disk support VMs with I/O-intensive workloads. <br> Premium SSD v2 doesn't require disk resizing, which enables high performance without excessive over-provisioning and minimizes the cost of unused capacity. <br><br> When available on VM SKUs, locally attached NVMe or similar devices can offer high performance, especially for use cases that require high input/output operations per second (IOPS) and low latency.|
| (VMs) Consider enabling [**accelerated networking**](/azure/virtual-network/accelerated-networking-overview).| It enables single root I/O virtualization (SR-IOV) to a VM, which greatly improves its networking performance. |
|(VMs, scale set) [**Set autoscale rules**](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-portal) to increase or decrease the number of VM instances in your scale set based on demand.| If your application demand increases, the load on the VM instances in your scale set increases. Autoscale rules ensure that you have enough resources to meet the demand. |

## Azure policies

Azure provides an extensive set of built-in policies related to Virtual Machines and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Encryption at host is enabled.
- Anti-malware extensions are deployed and enabled for automatic updates on VMs that run Windows Server.
- Automatic OS image patching on scale sets is enabled.
- Only approved VM extensions are installed.
- The Monitor agent and the dependency agents are enabled on new VMs in your Azure environment.  
- Only the allowed VM SKUs are deployed to limit sizes according to cost constraints.
- Private endpoints are used to access disk resources.
- Vulnerability detection is enabled. There are specialized rules for Windows machines. For example, you can schedule Windows Defender to scan every day.

For comprehensive governance, review the [Azure Policy built-in definitions for Virtual Machines](/azure/virtual-machines/policy-reference) and other policies that might affect the security of the compute layer.

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of Virtual Machines.

- [Reliability](/azure/advisor/advisor-high-availability-recommendations)
- [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations)
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations)
- [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations)


## Next steps

Consider the following articles as resources that demonstrate the recommendations highlighted in this article.

- Use the following reference architectures as examples of how you can apply this article's guidance to a workload:
  - Single VM architectures: [Linux VM](/azure/architecture/reference-architectures/n-tier/linux-vm#architecture) and [Windows VM](/azure/architecture/reference-architectures/n-tier/windows-vm#architecture)
  - Foundational architecture that focuses on infrastructure recommendations: [Virtual Machines baseline architecture](/azure/architecture/virtual-machines/baseline)
- Build implementation expertise by using the following product documentation: 
  - [Virtual Machines](/azure/virtual-machines/)
  - [Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets/overview)
