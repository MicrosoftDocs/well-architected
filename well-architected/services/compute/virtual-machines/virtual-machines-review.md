---
title: Azure Well-Architected Framework review - Virtual Machines
description: Design considerations and recommendations about Azure virtual machines.
author: cynthn
ms.author: cynthn
ms.date: 04/27/2023
ms.topic: conceptual
products:
  - azure-virtual-machines
categories:
  - compute
  - management-and-governance
---

# Azure Well-Architected Framework review - Virtual Machines

[Virtual Machines](/azure/virtual-machines/) is an on-demand, scalable computing resource that gives you the flexibility of virtualization without having to buy and maintain physical hardware to run it. 

In this article, you learn architectural best practices for Azure Virtual Machines. The guidance is based on the five pillars of architectural excellence:

- Reliability
- Security
- Cost optimization
- Operational excellence
- Performance efficiency
## Prerequisites

- Understanding the Well-Architected Framework pillars can help produce a high quality, stable, and efficient cloud architecture. We recommend that you review your workload using the [Microsoft Azure Well-Architected Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.

- Use a reference architecture to review the considerations based on the guidance provided in this article. We recommend, you start with [Run a Linux VM on Azure](/azure/architecture/reference-architectures/n-tier/linux-vm).


## Reliability
As you make design choices for virtual machines, review the [design principles](/azure/well-architected/resiliency/principles) for adding reliability to the architecture.

### Design checklist
> [!div class="checklist"]
> - Review the [SLAs for virtual machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/).
> - VMs should be deployed in a scale set [using the Flexible orchestration mode](/azure/virtual-machines/flexible-virtual-machine-scale-sets).
> - Deployed VMs across [Availability Zones](/azure/virtual-machines/windows/quick-create-portal) .
> - Install applications on [data disks](/azure/virtual-machines/linux/add-disk).
> - Use [maintenance control](/azure/virtual-machines/maintenance-control).

### Recommendations
Explore the following table of recommendations to optimize your Virtual Machine configuration for service reliability:

|Recommendation|Benefit|
|------------------------------|-----------|
| Review SLAs for virtual machines. | When defining test availability and recovery targets, make sure you have a good understanding of the SLAs offered for VMs.|
| Deploy using Flexible scale sets. | Even single instance VMs should be deployed into a scale set using the Flexible orchestration mode to future-proof your application for scaling and availability. Flexible orchestration offers high availability guarantees (up to 1000 VMs) by spreading VMs across fault domains in a region or within an Availability Zone.|
| Deploy across availability zones | Azure availability zones are physically separate locations within each Azure region that are tolerant to local failures.
| Install applications on data disks. | Having your data on a separate disk from your OS disk makes it easier to recover from failures and to migrate workloads.
| Use maintenance control | Control when VM maintenance occurs to manage the timing of system restarts.|


Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the [Azure Advisor](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/HighAvailability) recommendations.


## Security

This article provides an overview of the core Azure security features that can be used with virtual machines.

As you make design choices for virtual machines, review the [security principles](/azure/well-architected/security/security-principles) and [Security best practices](/azure/security/fundamentals/iaas) for adding security to the architecture.


### Design checklist


As you make design choices for your virtual machine deployment, review the [design principles](/azure/well-architected/security/security-principles) for security.

> [!div class="checklist"]
> - Review the [Linux security baseline](/security/benchmark/azure/baselines/virtual-machines-linux-security-baseline)
> - Review the [Windows security baseline](/security/benchmark/azure/baselines/virtual-machines-windows-security-baseline)
> - Manage authentication and access control.
> - Protect against malware
> - Managed updates
> - Encryption

### Recommendations

Explore the following table of recommendations to optimize your virtual machine configuration for security.

| Recommendation | Benefit |
|--------|----|
| Consider using Azure Bastion | Authentication and access control using [Azure Bastion](/azure/bastion/bastion-overview) provides secure and seamless RDP/SSH connectivity to your virtual machines directly from the Azure portal over TLS|
| Protect against malware | Install [antimalware protection](/azure/security/fundamentals/iaas#protect-against-malware) to help identify and remove viruses. |
| Manage updates | Use a solution like [Azure Automation](/azure/automation/update-management/overview) to manage operating system updates. |
| Monitor for security | To monitor the security posture of your Windows and Linux VMs, use [Microsoft Defender for Cloud](/azure/defender-for-cloud/defender-for-cloud-introduction). |
| Use encryption | Use [Azure Disk Encryption](/azure/security/fundamentals/azure-disk-encryption-vms-vmss) to protect your data. |

For more suggestions, see [Principles of the security pillar](/azure/well-architected/security/security-principles).

Azure Advisor helps you ensure and improve security. Review the [recommendations](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/Security).

### Policy definitions


- `Deploy default Microsoft IaaSAntimalware extension for Windows Server` -	This policy deploys a Microsoft IaaSAntimalware extension with a default configuration when a VM is not configured with the antimalware extension.
- `Microsoft IaaSAntimalware extension should be deployed on Windows servers` - This policy audits any Windows server VM without Microsoft IaaSAntimalware extension deployed.
- `Only approved VM extensions should be installed` - This policy governs the virtual machine extensions that are not approved.
- `Managed disks should be double encrypted with both platform-managed and customer-managed keys` - High security sensitive customers who are concerned of the risk associated with any particular encryption algorithm, implementation, or key being compromised can opt for additional layer of encryption using a different encryption algorithm/mode at the infrastructure layer using platform managed encryption keys. The disk encryption sets are required to use double encryption. Learn more at [https://aka.ms/disks-doubleEncryption](https://aka.ms/disks-doubleEncryption).
- `Managed disks should use a specific set of disk encryption sets for the customer-managed key encryption` - Requiring a specific set of disk encryption sets to be used with managed disks give you control over the keys used for encryption at rest. You are able to select the allowed encrypted sets and all others are rejected when attached to a disk. Learn more at [https://aka.ms/disks-cmk](https://aka.ms/disks-cmk).
- `Microsoft Antimalware for Azure should be configured to automatically update protection signatures` - This policy audits any Windows virtual machine not configured with automatic update of Microsoft Antimalware protection signatures.
- `OS and data disks should be encrypted with a customer-managed key` - Use customer-managed keys to manage the encryption at rest of the contents of your managed disks. By default, the data is encrypted at rest with platform-managed keys, but customer-managed keys are commonly required to meet regulatory compliance standards. Customer-managed keys enable the data to be encrypted with an Azure Key Vault key created and owned by you. You have full control and responsibility for the key lifecycle, including rotation and management. Learn more at [https://aka.ms/disks-cmk](https://aka.ms/disks-cmk).
- `Virtual machines and virtual machine scale sets should have encryption at host enabled` - Use encryption at host to get end-to-end encryption for your virtual machine and virtual machine scale set data. Encryption at host enables encryption at rest for your temporary disk and OS/data disk caches. Temporary and ephemeral OS disks are encrypted with platform-managed keys when encryption at host is enabled. OS/data disk caches are encrypted at rest with either customer-managed or platform-managed key, depending on the encryption type selected on the disk. Learn more at [https://aka.ms/vm-hbe](https://aka.ms/vm-hbe).
- `Require automatic OS image patching on Virtual Machine Scale Sets` - This policy enforces enabling automatic OS image patching on Virtual Machine Scale Sets to always keep virtual Machines secure by safely applying latest security patches every month.


All built-in policy definitions related to Azure Virtual Machines are listed in [Azure Policy built-in definitions for Azure Virtual Machines](/azure/virtual-machines/policy-reference).

## Cost optimization

To optimize costs, review the [design principles](../../../cost-optimization/principles.md).

To estimate costs related to virtual machines, use these tools.

- Identify the best VM for your workloads with the virtual machines selector. For more information, see [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)and [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) pricing.
- Use this [pricing calculator](https://azure.microsoft.com/pricing/calculator/#virtual-machines) to configure and estimate the costs of your Azure VMs.

### Design considerations
> [!div class="checklist"]
> - Shut down VM instances which aren't in use.
> - Use [Spot VMs](/azure/virtual-machines/spot-vms) when appropriate.
> - Choose the right VM size for your workload.
> - Use [Zone to Zone disaster recovery](/azure/site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery) for virtual machines.
> - Prepay for [reserved instances](/azure/virtual-machines/prepay-reserved-vm-instances) or an [Azure savings plan for compute](https://azure.microsoft.com/pricing/offers/savings-plan-compute/#benefits-and-features) for significant savings.
> - Use hybrid benefit licensing

### Recommendations

Explore the following table of recommendations to optimize your Virtual Machine configuration for service cost:

|Recommendation|Benefit|
|------------------------------|-----------|
| Stop VMs during off-hours | Configuring start and stop times will shut down instances that aren't in use. The feature is suitable as a low-cost automation option. |
| Use Spot VMs when appropriate.|Spot VMs are ideal for workloads that can be interrupted, such as highly parallel batch processing jobs. These VMs take advantage of the surplus capacity in Azure at a lower cost. They're also well suited for experimenting, developing, and testing large-scale solutions. Check out our [Azure Virtual Machine Spot Eviction](/azure/architecture/guide/spot/spot-eviction) guide to learn how to create a reliable interruptible workload in Azure.|
|Right-size your VMs |  Identify the best VM for your workloads with the virtual machines selector. See [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) and [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) pricing.|
|Prepay for added cost savings | Purchasing [reserved instances](/azure/virtual-machines/prepay-reserved-vm-instances) is a way to reduce Azure costs for workloads with stable usage. Make sure you manage usage. If usage is too low, then you're paying for resources that aren't used. Keep reserved instances simple and keep management overhead low to prevent increasing cost. With an [Azure savings plan for compute](https://azure.microsoft.com/pricing/offers/savings-plan-compute/#benefits-and-features), you save money across select compute services globally. Commit to spend a fixed hourly amount for 1 or 3 years and unlock lower prices until you reach your hourly commitment.|

| Use existing licensing through the hybrid benefit licensing program | Hybrid benefit licensing is available for both [Linux](/azure/virtual-machines/linux/azure-hybrid-benefit-linux) and [Windows](/azure/virtual-machines/windows/hybrid-use-benefit-licensing)|

 Azure Advisor helps you ensure and improve cost optimization. Review the [recommendations](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/Cost).

### Policy definitions


- Consider setting an `Allowed virtual machine SKU` policy to limit the sizes that can be used.


All built-in policy definitions related to Azure Virtual Machines are listed in [Azure Policy built-in definitions for Azure Virtual Machines](/azure/virtual-machines/policy-reference).

## Operational excellence

To ensure operational excellence, review the [design principles](/azure/well-architected/devops/principles).

### Design checklist


> [!div class="checklist"]
> - [Monitor](/azure/virtual-machines/monitor-vm) and measure health.
> - [Automate](/azure/well-architected/devops/automation-tasks) tasks like provisioning and updating.
> - Build a robust testing environment.
> - Right size your VMs.
> - Manage your quota.


### Recommendations


| Recommendation | Benefit |
|--------|----|
| Monitor and measure health | In a production environment, it's important to [monitor](/azure/virtual-machines/monitor-vm) the health, and performance of your VMs.  |
| Automate tasks | Building [automation](/azure/well-architected/devops/automation-tasks) reduces deviations from your plans and reduces that time it takes to manage your workload.  |
| Build a robust testing environment | Ideally, an organization will have multiple environments in which to test deployments. These test environments should be similar enough to production that deployment and run time issues are detected before deployment to production. |
| Right-size your VMs | Choose the right [VM family](/azure/virtual-machines/sizes) for your workload. | 
| Manage your quota | Plan what level of quota will be required and review that level regularly as the workload evolves and grows and [request changes early](/azure/azure-portal/supportability/per-vm-quota-requests)  |


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
> - Reduce latency by deploying VMs closer together in proximity placement groups
> - Convert disks from standard HDD to premium SSD
> - Enable Accelerated Networking to improve network performance and latency
> - Autoscale your Flexible scale sets.


### Recommendations


Explore the following table of recommendations to optimize your virtual machine deployment configuration for performance and efficiency.

| Recommendation | Benefit |
|--------|----|
| Reduce latency | Consider deploying VMs in [Creating and using proximity placement groups using PowerShell](/azure/virtual-machines/co-location). |
| Convert disks from standard HDD to premium SSD | Azure [premium SSDs](/azure/virtual-machines/disks-performance-tiers) deliver high-performance and low-latency disk support for virtual machines (VMs) with input/output (IO)-intensive workloads. |
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

## Next steps
Use the recommendations as you provision virtual machines for your solution.

- Learn module: [Introduction to Azure virtual machines](/training/modules/intro-to-azure-virtual-machines/)

- Review the Virtual Machine recommendations provided by [Azure Advisor](/azure/advisor/).

- Review the built-in definitions provided by Azure Policy that apply to Virtual Machines. All built-in policy definitions related to Azure Virtual Machines are listed in [Azure Policy built-in definitions for Azure Virtual Machines](/azure/virtual-machines/policy-reference).
