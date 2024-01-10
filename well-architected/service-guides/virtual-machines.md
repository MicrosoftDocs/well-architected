---
title: Azure Well-Architected Framework review - Virtual Machines
description: Design considerations and recommendations about Azure virtual machines.
author: ericd-mst-github
ms.author: erd
ms.date: 06/29/2023
ms.topic: conceptual
products:
  - azure-virtual-machines
categories:
  - compute
  - management-and-governance
---

# Azure Well-Architected Framework review - Virtual Machines and scale sets

Azure Virtual Machines (VMs) is a type of compute service that allows you to create and run virtual machines on the Azure platform. There's flexibility in choosing from different SKUs, operating systems, and configurations with various billing models.

This article provides architectural recommendations for making informed decisions when using various features to manage VMs, such as scaling, backup, monitoring, security, and more. The guidance is based on the [**Azure Well-Architected Framework pillars**](../pillars.md).

**Technology scope**

Recommendations in this article focus on the interrelated decisions in these areas, which are typical for designs that use virtual machines on Azure.  

- Azure VMs
- Azure Virtual Machine Scale Sets
- Disks. For more information, see [Azure Well-Architected Framework review - Disks](./azure-disks-cost-optimization.md).

## Review resources

Consider these articles as resources that demonstrate the  recommendations highlighted in this article.

- Use these reference architectures to see examples of these recommendations.
  - Single VM architectures: [Linux VM](/azure/architecture/reference-architectures/n-tier/linux-vm#architecture) and [Windows VM](/azure/architecture/reference-architectures/n-tier/windows-vm#architecture).
  - Foundational architecture that focuses on infrastructure recommendations: [Azure virtual machine baseline architecture](/azure/architecture/virtual-machines/baseline).
- Build implementation expertise using product documentation: [Azure Virtual Machines](/azure/virtual-machines/) and [Azure Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets/overview).

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

Read the [**Reliability design principles**](/azure/well-architected/resiliency/principles) to understand the approaches applied for individual components, system flows, and the system as a whole.

##### Design checklist

Start your design strategy based on the [**design review checklist for Reliability**](../reliability/checklist.md) and determine its relevance to your business requirements while keeping in mind the SKUs and features of VMs and their dependencies. Extend the strategy to include more approaches as your see fit for your solution.

> [!div class="checklist"]
>
> - **Evaluate the critical dependencies** of VMs on various components such as disks and networking components. Understanding these relationships is important for determining the critical flows that might impact reliability.
> - **Calculate your composite Service Level Objectives (SLOs) based on Azure Service Level Agreements (SLAs)**. Ensure that your SLO isn't higher than the [Azure SLAs](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) to avoid unrealistic expectations and potential issues.
>     Be aware of the complexities introduced by dependencies. For example, some dependencies like virtual network and NICs, don't have their own SLAs. Other dependencies, such as an associated data disk has SLAs that are integrated with VM SLAs. All those variations should be considered as they can impact VM performance and reliability.
> - **Review Azure VM quotas and limits** that might pose design restrictions. VMs have specific limits and quotas, which vary based on the type of VM, the region, and other factors. There might be subscription restrictions, such as number of VMs per subscription, the number of cores per VM, and more. If your subscription is shared with other workloads, your consumption might be reduced.
>      Check limits on [VMs](/azure/azure-resource-manager/management/azure-subscription-service-limits#virtual-machine-scale-sets-limits), [Virtual Machine Scale Sets](/azure/azure-resource-manager/management/azure-subscription-service-limits#virtual-machine-scale-sets-limits), [managed disks](/azure/azure-resource-manager/management/azure-subscription-service-limits#managed-virtual-machine-disks), and other components that you consider as dependencies.
> - **Make VMs and dependencies redundant across zones**. If a VM fails, the workload should continue to function due to resiliency. Include dependencies in your redundancy choices. For example, use built-in redundancy options available with disks, use zone redundant IPs, ensuring data availability and high uptime.
> - **Do failure mode analysis** to minimize points of failure, by focusing on VM interactions with the network and storage components. Opt for design choices such as ephemeral disks to localize the disk, avoiding network hops. Adding a load balancer can enhance self-preservation by distributing network traffic across multiple VMs, improving availability and reliability.
> - **Right-size the VMs and their dependencies**. Understand your VM's expected work to ensure it's not undersized and can handle maximum load. Overprovisioning is a way to provide extra capacity to mitigate failures.
> - **Be ready to scale out** when degradation is observed to mitigate chances of failure. [Azure Virtual Machine Scale Sets](/azure/virtual-machines/flexible-virtual-machine-scale-sets) have scaling capabilities that can spin up new instances as required and distribute load across multiple VMs and zone.
> - **Create a comprehensive disaster recovery plan**. Disaster preparedness involves creating a comprehensive plan and deciding on a technology for recovery. Explore how Azure supports health degradation monitoring and auto repair features to support self-healing for VMs. This could involve using Azure Site Recovery, having a passive standby to failover to, or redeploying from Infrastructure as Code (IAC). The chosen method should align with business requirements and organizational operations. For information, see [VM service disruptions](/azure/virtual-machines/overview#service-disruptions).
>
>     Dependencies and stateful components, such as attached storage, can complicate recovery. If disks go down, it can impact the functioning of the VMs. Include a clear process for these dependencies in your recovery plans.
> - **Create state isolation**. Workload data should be on a separate data disk to prevent interference with the OS disk. If a VM fails, a new OS disk can be spun up with the same data disk, ensuring resilience and fault isolation. See, [Ephemeral OS disks](/azure/virtual-machines/ephemeral-os-disks).
> - **Monitor health of the VMs and the dependencies**. Take advantage of networking components that check the health status of VMs. For example, Azure Load Balancer pings VMs to detect unhealthy VMs and reroute traffic accordingly.
>     Use monitoring agents to collect logs and metrics to get a comprehensive view of your VMs. Azure Virtual Machine Scale Sets roll up telemetry, allowing you to view health metrics at an individual VM level or as an aggregate.
> - **Conduct chaos experiments**. Azure Chaos Studio offers several fault libraries that target critical VM use cases and help you build resilient systems by intentionally injecting faults and observing the results.
> - **Create a maintenance plan**. that includes regular system patching is a part of routine operations. Include emergencies process that allows for immediate application of patches. You can have custom process to manage the patching and, or partially delegate it to Azure.
>    Azure provides features for individual [VM maintenance](/azure/virtual-machines/maintenance-configurations). Also, you can set up  maintenance windows to minimize disruptions during updates. During platform updates, fault domain considerations are key for resilience. If one instance per zone is needed across two zones, four instances are required in each zone. If spread across two zones, at least six instances are needed.

##### Recommendations

Use the design strategy to build your proof-of-concept or optimize your existing environment by evaluating these recommendations.

These recommendations aren't intended to be an exhaustive list of all configurations available for Azure Virtual Machines and their dependencies. Instead, they represent the key recommendations mapped to the preceding design perspectives.

|Recommendation|Benefit|
|------------------------------|-----------|
|**Scale set: Use Azure Virtual Machine Scale Sets in [Flexible orchestration mode](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes#scale-sets-with-flexible-orchestration)** to deploy VMs. | You'll be able to future proof your application for scaling and take advantage of the high availability guarantees that spreads VMs across fault domains in a region or within an availability zone.|
|**VMs: Implement heath endpoints** on VMs that emit instance health status. <br> **Scale set: [Enable automatic repairs](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-instance-repairs)** on the scale set by specifying the preferred _repair action_. <br> Consider setting a time frame during which automatic repairs are paused if state the virtual machine is changed. This strategy can prevent inadvertent or premature repair operations.|Availability is maintained even if an instance is deemed unhealthy. Automatic repairs initiate recovery by replacing the faulty instance.|
|**Scale set: [Enable overprovisioning](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning)** on scale sets.|Overprovisioning can reduce deployment times with the cost benefit because the extra VMs aren't billed. |
|**Scale set**: Allow Flexible orchestration to **max spread the VM instances** fault domains.| You'll be able to isolate fault domains. During maintenance periods, when one fault domain is updated, VM instances are available in the other fault domains.|
|**Scale set: [Deploy across availability zones](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#design-considerations-for-availability-zones)** on scale sets.<br> Zone balance the instances to make sure they are equally spread across zones.| The VM instances are provisioned in physically separate locations within each Azure region that are tolerant to local failures. <br> Keep in mind that there might be uneven number of instances across zones, depending on resource availability. Zone balancing supports availability by making sure if one zone is down, other zones have sufficient instances.|
|**VMs**: Take advantage of the **[capacity reservations feature](/azure/virtual-machines/capacity-reservation-overview)**. |The capacity is reserved for your use and is available within the scope of applicable Service Level Agreements (SLAs). They can be deleted when no longer needed and billing is consumption based.|

>[!TIP]
>For more information on Reliability guidance for VMs, see [Reliability in Azure Virtual Machines - Azure Virtual Machines](/azure/reliability/reliability-virtual-machines).

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

Read the [**Security design principles**](/azure/well-architected/security/security-principles) to understand the approaches for achieving those goals and apply them to Azure Virtual Machines and the environment them run in.

##### Design checklist

Start your design strategy based on the [**design review checklist for Security**](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as your see fit for your solution.

> [!div class="checklist"]
>
> - **Review the security baselines** for [Linux](/security/benchmark/azure/baselines/virtual-machines-linux-security-baseline), [Windows](/security/benchmark/azure/baselines/virtual-machines-windows-security-baseline) VMs and also [scale set baseline](/security/benchmark/azure/baselines/virtual-machine-scale-sets-security-baseline).
>
> - **Ensure timely and automated security patching and upgrades**. While Flexible orchrestration is recommended, automated OS upgrades are not yet in production. Make sure updates are rolled out and validated with a well-defined process in place.
> - **Identify the VMs that hold state**. Make sure that data is classified according to organization-provided sensitivity labels and protected through appropriate levels of encryption and other security controls. If you have high sensitivity requirements, consider using double encryption, Azure Confidential Compute to protect data in use, and other high security controls.
>
> - **Provide segmentation** to the VMs and scale sets by setting network boundaries, access controls, and also place VMs in resource groups that share the same lifecycle.
>
> - **Apply access controls on identities** trying to reach the VMs and also VMs reaching other resources. Use Microsoft Entra ID for authentication and authorization needs making sure strong passwords, multi-factor authentication, and role-based access control (RBAC) are in place for your VMs (and its dependencies such as secrets) to permit allowed identities to only perform operations expected of their roles.
>
> - **Use network controls to restrict ingress and egress traffic**. Place VMs and scale sets in Azure Virtual Network for isolation and put network security groups to filter traffic. Use load balancers and firewalls rules to protect against malicious traffic and data exfilteration attacks.
>
>     Use [Azure Bastion](/azure/bastion/bastion-overview) provides secure connectivity to the VMs for operational access.
>
>     Communication to and from the VMs to PaaS services should be over private endpoints.
>
> - **Reduce the attack surface** by hardening OS images and removing unused components. Use smaller images and remove binaries that are not required to run the workload. Additionally, tighten VM configuration by removing default accounts, ports, and other settings that aren't needed.
>
> - **Protect secrets** such as certificates needed for data in transit authentication. Consider using the [Azure Key Vault virtual machine extension (Windows and Linux)](/azure/virtual-machines/extensions/key-vault-windows) that provides automatic refresh of certificates stored in an Key Vault. When it detects a change in the certificates, the extension retrieves and installs the corresponding certificates.
>
> - **Threat detection**. Monitor VMs for threats and misconfigurations. Use Defender for Servers to capture VM and OS changes, and maintain an audit trail of access, new accounts, and changes in permissions.
>
> - **Threat prevention**. Protect against malware attacks and malicious actors by implementing security controls such as firewalls, anti-virus software, and intrusion detection systems.

### Recommendations

Explore the following table of recommendations to optimize your virtual machine configuration for security.

| Recommendation | Benefit |
|--------|----|
| Consider using Azure Bastion | Authentication and access control using [Azure Bastion](/azure/bastion/bastion-overview) provides secure and seamless RDP/SSH connectivity to your virtual machines directly from the Azure portal over TLS|
| Protect against malware | Install [antimalware protection](/azure/security/fundamentals/iaas#protect-against-malware) to help identify and remove viruses. |
| Manage updates | Use a solution like [Azure Automation](/azure/automation/update-management/overview) to manage operating system updates and maintain security compliance with critical updates. |
| Monitor for security | To monitor the security posture of your Windows and Linux VMs, use [Microsoft Defender for Cloud](/azure/defender-for-cloud/defender-for-cloud-introduction). |
| Use encryption | Use [Azure Disk Encryption](/azure/security/fundamentals/azure-disk-encryption-vms-vmss) to protect your data. |

For more suggestions, see [Principles of the security pillar](/azure/well-architected/security/security-principles).

Azure Advisor helps you ensure and improve security. Review the [security recommendations](/azure/advisor/advisor-security-recommendations).

### Policy definitions

- `Deploy default Microsoft IaaSAntimalware extension for Windows Server` -	This policy deploys a Microsoft IaaSAntimalware extension with a default configuration when a VM isn't configured with the antimalware extension.
- `Microsoft IaaSAntimalware extension should be deployed on Windows servers` - This policy audits any Windows server VM without Microsoft IaaSAntimalware extension deployed.
- `Only approved VM extensions should be installed` - This policy governs the virtual machine extensions that aren't approved.
- `Managed disks should be double encrypted with both platform-managed and customer-managed keys` - High security sensitive customers who are concerned of the risk associated with any particular encryption algorithm, implementation, or key being compromised can opt for additional layer of encryption using a different encryption algorithm/mode at the infrastructure layer using platform managed encryption keys. The disk encryption sets are required to use double encryption. Learn more at [https://aka.ms/disks-doubleEncryption](https://aka.ms/disks-doubleEncryption).
- `Managed disks should use a specific set of disk encryption sets for the customer-managed key encryption` - Requiring a specific set of disk encryption sets to be used with managed disks give you control over the keys used for encryption at rest. You're able to select the allowed encrypted sets and all others are rejected when attached to a disk. Learn more at [https://aka.ms/disks-cmk](https://aka.ms/disks-cmk).
- `Microsoft Antimalware for Azure should be configured to automatically update protection signatures` - This policy audits any Windows virtual machine not configured with automatic update of Microsoft Antimalware protection signatures.
- `OS and data disks should be encrypted with a customer-managed key` - Use customer-managed keys to manage the encryption at rest of the contents of your managed disks. By default, the data is encrypted at rest with platform-managed keys, but customer-managed keys are commonly required to meet regulatory compliance standards. Customer-managed keys enable the data to be encrypted with an Azure Key Vault key created and owned by you. You have full control and responsibility for the key lifecycle, including rotation and management. Learn more at [https://aka.ms/disks-cmk](https://aka.ms/disks-cmk).
- `Virtual machines and virtual machine scale sets should have encryption at host enabled` - Use encryption at host to get end-to-end encryption for your virtual machine and virtual machine scale set data. Encryption at host enables encryption at rest for your temporary disk and OS/data disk caches. Temporary and ephemeral OS disks are encrypted with platform-managed keys when encryption at host is enabled. OS/data disk caches are encrypted at rest with either customer-managed or platform-managed key, depending on the encryption type selected on the disk. Learn more at [https://aka.ms/vm-hbe](https://aka.ms/vm-hbe).
- `Require automatic OS image patching on Virtual Machine Scale Sets` - This policy enforces enabling automatic OS image patching on Virtual Machine Scale Sets to always keep virtual Machines secure by safely applying latest security patches every month.

All built-in policy definitions related to Azure Virtual Machines are listed in [Azure Policy built-in definitions for Azure Virtual Machines](/azure/virtual-machines/policy-reference).

## Cost optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

Read the [Cost Optimization design principles](../cost-optimization/principles.md) to understand the approaches for achieving those goals and understand the tradeoffs necessary in technical design choices related to Azure Virtual Machines and the environment them run in.

##### Design checklist

Start your design strategy based on the [**design review checklist for Cost Optimization**](../cost-optimization/checklist.md) for investments and fine tune the design so that the workload is aligned with the budget allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Estimate realistic costs**. Use the [pricing calculator](https://azure.microsoft.com/pricing/calculator/#virtual-machines) to estimate the costs of your Azure VMs. Identify the best VM for your workloads using the virtual machines selector. For more information, see [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)and [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) pricing.
>
> - **Cost guardrails**. Use governance policies to restrict resource types, configurations, and locations. Additionally, use role-based access control to block actions that can lead to overspending.
>
> - **Choose the right VM plan and SKU**. Make sure you select [VM sizes](/azure/virtual-machines/sizes) that serve the workload purposes. For interruptable processes, consider [Spot VMs](/azure/virtual-machines/spot-vms), which can lower costs. With Flexible orchestration, you can mix in spot VMs with regular VMs. Each SKU has associated disk options that impacts the overall cost.
>
> - **Choose the right capabilities for dependent resources**. Save on backup storage costs for the vault-standard tier using Azure Backup Storage reserved capacity. It offers a discount when you commit to a reservation for either one year or three years.
>
>     Archive tier in Azure storage is an offline tier, which is optimized for storing blob data that is rarely accessed. The archive tier offers the lowest storage costs, but higher data retrieval costs and latency compared to the online tiers (hot and cool).
>
>    Consider using [Zone to Zone disaster recovery](/azure/site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery) for virtual machines to recover from site failure while reducing the complexity of availability using zone-redundant services. There can be cost benefits from reduced operational complexity.
>
> - **Choose the right billing model**. Evaluate whether commitment-based models for compute, optimize costs for based on the business requirements of workload. Consider these Azure options:
>     - **[Azure Reservations](/azure/virtual-machines/prepay-reserved-vm-instances)**: Prepay for predictable workloads and save over consumption-based pricing.
>     - **[Azure Savings Plan](https://azure.microsoft.com/pricing/offers/savings-plan-compute/#benefits-and-features)**: If you commit to spend a fixed hourly amount on compute services for one or three years, this plan can reduce costs.
>     - **Azure Hybrid Benefit**: Save when you migrate your on-premises VMs to Azure.
>     - **License mobility**: Bring your own licenses to Azure and save on Windows Server and SQL Server.
> - **Monitor usage**. Continuously monitor usage patterns and detect unused or underutilized VMs. For those instances, shut down VM instances when not in use. Deploy [Azure Monitor Agent (AMA)](/azure/azure-monitor/agents/agents-overview) to collect monitoring data from the guest operating system. //TODO... add VMInsights? or remove AMA.
>
> - **Look for ways to optimize**. For example, use a Premium SSD v2 disk based on your workload patterns, and programmatically adjust its performance to account for either higher or lower demand. For other disk types, size your disks to achieve the desired performance without the need to provision instances over the needed capacity. Account for fluctuating workload patterns, and minimizing unused provisioned capacity.

##### Recommendations

Explore the following table of recommendations to optimize your Virtual Machine configuration for service cost:

|Recommendation|Benefit|
|------------------------------|-----------|
| Stop VMs during off-hours | Configuring start and stop times shut down instances that aren't in use. The feature is suitable as a low-cost automation option. |
| Use Spot VMs when appropriate.|Spot VMs are ideal for workloads that can be interrupted, such as highly parallel batch processing jobs. These VMs take advantage of the surplus capacity in Azure at a lower cost. They're also well suited for experimenting, developing, and testing large-scale solutions. Check out our [Azure Virtual Machine Spot Eviction](/azure/architecture/guide/spot/spot-eviction) guide to learn how to create a reliable interruptible workload in Azure.|
|Right-size your VMs |  Identify the best VM for your workloads with the virtual machines selector. See [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) and [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) pricing.|
|Configure Azure Bastion for operational access | [Azure Bastion](/azure/bastion/bastion-overview#pricing) is charged on a fix per-hour basis, and charges for outbound data transfers. |
|Utilize Premium SSD v2 effectively | Premium SSD v2 allows you to granularly adjust your performance independent of the disk's size. Combining this adjustment ability with an understanding workload patterns, offers an effective cost optimization strategy for IaaS infrastructure, enabling high performance without excessive over-provisioning and minimizing the cost of unused capacity. |
| Optimize with managed disks | Determine your performance needs in combination with your storage capacity needs, accounting for fluctuating workload patterns. Knowing your needs allows you to determine what disk type and disk size you need. Some higher performance disk types offer extra cost optimization features and strategies. |
|Prepay for added cost savings | Purchasing [reserved instances](/azure/virtual-machines/prepay-reserved-vm-instances) is a way to reduce Azure costs for workloads with stable usage. Make sure you manage usage. If usage is too low, then you're paying for resources that aren't used. Keep reserved instances simple and keep management overhead low to prevent increasing cost.|
| Use existing licensing through the hybrid benefit licensing program | Hybrid benefit licensing is available for both [Linux](/azure/virtual-machines/linux/azure-hybrid-benefit-linux) and [Windows](/azure/virtual-machines/windows/hybrid-use-benefit-licensing)|
| Deploy AMA | AMA supports Data Collection Rules (DCR) which allow filtering rules and data transformation to reduce overall data volume being uploaded, which lowers ingestion and storage costs.|

 Azure Advisor helps you ensure and improve cost optimization. Review the [cost recommendations](/azure/advisor/advisor-cost-recommendations).

### Policy definitions

Consider setting an `Allowed virtual machine SKU` policy to limit the sizes that can be used.

All built-in policy definitions related to Azure Virtual Machines are listed in [Azure Policy built-in definitions for Azure Virtual Machines](/azure/virtual-machines/policy-reference).

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

Read the [Operational Excellence design principles](../operational-excellence/principles.md) to understand the approaches for achieving those goals towards the operational requirements of the workload.

##### Design checklist

Start your design strategy based on the [**design review checklist for Operational Excellence**](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure VMs and scale sets.

> [!div class="checklist"]
>
> - **Monitor the VM instances**. Collect logs and metrics from VM instances to monitor resource usage and measure the health of the instances. Common metrics include CPU usage, number of requests, I/O latency, and [others](/azure/azure-monitor/vm/vminsights-log-query). Set up Azure Monitor [alerts](/azure/virtual-network/monitor-virtual-network#alerts) to get notified about issues and for detecting configuration changes in your environment.
>
> - **Automate processes for bootstrapping, running scripts, and configuring VMs**. You can use extensions or automate through custom scripts. Here are some recommended options:
>    - [Azure Key Vault virtual machine (VM) extension](/azure/virtual-machines/extensions/key-vault-windows) that provide automatic refresh of certificates stored in an Azure Key Vault.
>    - [Azure Custom Script Extension](/azure/virtual-machines/extensions/custom-script-linux) (Windows and Linux) that downloads and runs scripts on Azure virtual machines (VMs). Use this extension for post-deployment configuration, software installation, or any other configuration or management task.
>    - Use cloud-init to setup the startup environment for Linux-based VMs.
>
> - **Have processes for installing automatic updates**. Consider using [Automatic VM Guest patching](/azure/virtual-machines/automatic-vm-guest-patching) to keep your OS patched.
>
> - **Build a test environment** that closely matches your production environment to test updates and changes before deploying to production. Have processes in place to test the security updates, performance baselines, and reliability faults. Take advantage of Azure Chaos Studio fault libraries to inject and simulate error conditions. For more information, see [Azure Chaos Studio fault and action library](/azure/chaos-studio/chaos-studio-fault-library).


### Recommendations

| Recommendation | Benefit |
|--------|----|
| Monitor and measure health | In a production environment, it's important to [monitor](/azure/virtual-machines/monitor-vm) the health, and performance of your VMs.  |
| Setup Azure Monitor alert rules | Determine important conditions in your monitoring data to identify and address issues found in your system before customers are impacted. |
| Automate tasks | Building [automation](/azure/well-architected/devops/automation-tasks) reduces deviations from your plans and reduces that time it takes to manage your workload.  |
| Build a robust testing environment | Ideally, an organization has multiple environments in which to test deployments. These test environments should be similar enough to production that deployment and run time issues are detected before deployment to production. |
| Right-size your VMs | Choose the right [VM family](/azure/virtual-machines/sizes) for your workload. |
| Manage your quota | Plan what level of quota will be required and review that level regularly as the workload evolves and grows and [request changes early](/azure/azure-portal/supportability/per-vm-quota-requests)  |
|  Optimize with managed disks | Determine your performance needs in combination with your storage capacity needs, accounting for fluctuating workload patterns. Knowing your needs allows you to determine what disk type and disk size you need. Some higher performance disk types offer extra cost optimization features and strategies. |

For more suggestions, see [Principles of the operational excellence pillar](/azure/well-architected/devops/principles).

Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the [recommendations](/azure/advisor/advisor-reference-operational-excellence-recommendations).

### Policy definitions

- Consider setting an `Allowed virtual machine SKU` policy

All built-in policy definitions related to Azure Virtual Machines are listed in [Azure Policy built-in definitions for Azure Virtual Machines](/azure/virtual-machines/policy-reference).

## Performance efficiency

Performance efficiency is matching the resources that are available to an application with the demand that it's receiving. Performance efficiency includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing your application code for peak performance.

As you make design choices for your virtual machine deployment, review [Microsoft Azure Well-Architected Framework - Performance efficiency](/training/modules/azure-well-architected-performance-efficiency/) for performance and efficiency.

### Design checklist

> [!div class="checklist"]
>
> - Reduce latency by deploying VMs closer together in proximity placement groups.
> - Use premium SSD to improve the performance of your virtual machines, especially for I/O-intensive workloads.
> - Utilize Premium SSD v2 effectively as it is a newer version of premium SSD that offers even better performance and scalability.
> - Optimize with managed disks to improve scalability, availability, and performance, and simplify disk management tasks such as backup and restore.
> - Consider locally attached NVMe or similar devices for high-performance use cases such as big data analytics, machine learning, and high-performance computing.
> - Enable Accelerated Networking to improve network performance and latency.
> - Right size your VMs by choosing the appropriate VM size based on your workload requirements and monitor resource utilization to ensure optimal performance and cost.
> - Autoscale your Flexible scale sets to automatically increase or decrease the number of VM instances that run your application based on demand or a set schedule.

### Recommendations

Explore the following table of recommendations to optimize your virtual machine deployment configuration for performance and efficiency.

| Recommendation | Benefit |
|--------|----|
| Reduce latency | Consider deploying VMs in [Creating and using proximity placement groups using PowerShell](/azure/virtual-machines/co-location). |
| Convert disks from standard HDD to premium SSD | Azure [premium SSDs](/azure/virtual-machines/disks-performance-tiers) deliver high-performance and low-latency disk support for virtual machines (VMs) with input/output (IO)-intensive workloads. |
|Utilize Premium SSD v2 effectively | Premium SSD v2 allows you to granularly adjust your performance independent of the disk's size. Combining this adjustment ability with an understanding workload pattern, offers an effective cost optimization strategy for IaaS infrastructure, enabling high performance without excessive over-provisioning and minimizing the cost of unused capacity. |
| Optimize with managed disks |  Determine your performance needs in combination with your storage capacity needs, accounting for fluctuating workload patterns. Knowing your needs allows you to determine what disk type and disk size you need.|
| Use locally attached NVMe devices | When available on VM SKUs, locally attached NVMe or similar devices can offer high performance, especially for use cases requiring high IOPS and low latency. |
| Consider accelerated networking | [Accelerated networking](/azure/virtual-network/accelerated-networking-overview) enables single root I/O virtualization (SR-IOV) to a VM, greatly improving its networking performance. |
| Use autoscaling | Automatically increase or decrease the number of VM instances that run your application with [autoscaling](/azure/virtual-machine-scale-sets/scripts/cli-sample-enable-autoscale). |

For more suggestions, see [Principles of the performance efficiency pillar](/azure/well-architected/scalability/principles).

Azure Advisor helps you ensure and improve performance. Review the [recommendations](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/Performance).

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your Virtual Machines.

- [Reliability](/azure/advisor/advisor-high-availability-recommendations)
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations)
- [Operational excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations)

## Additional resources
Here are other resources to help you query for unhealthy instances.
### Cost analysis
Planned versus actual spending can be managed through [Azure Cost Management + Billing](/azure/cost-management-billing/costs/quick-acm-cost-analysis). There are several options for grouping resources by billing unit.
### Log Analytics
Collect logs and metrics from the Azure resources and Application Insights with [Azure Log Analytics](/azure/azure-monitor/logs/log-analytics-overview) to gain insights into the performance and health of your VMs. You can also use Log Analytics to query and analyze the data collected by Azure Monitor Logs.
### Application Insights
Use the [Application Insights](/azure/azure-monitor/app/app-insights-overview) extension alongside Azure Monitor to proactively understand how an application is performing and reactively review application execution data to determine the cause of an incident.

## Next steps
Use the recommendations as you provision virtual machines for your solution.

- Review the Learn module: [Introduction to Azure virtual machines](/training/modules/intro-to-azure-virtual-machines/).

- Review the Virtual Machine recommendations provided by [Azure Advisor](/azure/advisor/).

- Review the built-in definitions provided by Azure Policy that apply to Virtual Machines. All built-in policy definitions related to Azure Virtual Machines are listed in [Azure Policy built-in definitions for Azure Virtual Machines](/azure/virtual-machines/policy-reference).
