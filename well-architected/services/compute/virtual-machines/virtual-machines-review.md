---
title: Azure Well-Architected Framework review - Virtual Machines
description: Design considerations and recommendations about Azure virtual machines.
author: cynthn
ms.author: cynthn
ms.date: 04/25/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - virtual-machines
categories:
  - compute
  - management-and-governance
---

# Azure Well-Architected Framework review - Virtual Machines

[Virtual Machines](/azure/virtual-machines/) is an on-demand, scalable computing resource that gives you the flexibility of virtualization without having to buy and maintain physical hardware to run it. The intent of this article is to provide guidance about this resource based on the pillars of architecture excellence: Reliability, and Cost Optimization. If you are provisioning virtual machines in your design, consider the design principles and recommendations described in this article. 

Policy: /azure/virtual-machines/security-controls-policy
Built-in policies: /azure/governance/policy/samples/built-in-policies#compute
Policy reference: /azure/virtual-machines/policy-reference


## Prerequisites

- Understanding the Well-Architected Framework pillars can help produce a high quality, stable, and efficient cloud architecture. We recommend that you review your workload using the [Microsoft Azure Well-Architected Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.

- Use a reference architecture to review the considerations based on the guidance provided in this article. We recommend, you start with [Run a Linux VM on Azure](/azure/architecture/reference-architectures/n-tier/linux-vm).


## Reliability
As you make design choices for virtual machines, review the [design principles](/azure/architecture/framework/resiliency/principles) for adding reliability to the architecture. 

### Design checklist
> [!div class="checklist"]
> - Review the [SLAs for virtual machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/).
> - VMs should be deployed in Flexible scale sets. 
> - Deployed VMs across [Availability Zones](/azure-docs-pr/articles/virtual-machines/create-portal-availability-zone) .
> - Install applications on data disks.
> - Monitor and measure health.
> - Use [maintenance control](/azure/virtual-machines/maintenance-control) to manage system restarts.

### Recommendations
Explore the following table of recommendations to optimize your Virtual Machine configuration for service reliability:

|Recommendation|Benefit|
|------------------------------|-----------|
| Review [SLAs for virtual machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/). | When defining test availability and recovery targets, make sure you have a good understanding of the SLAs offered for VMs.|
| Deploy using Flexible scale sets. | Even single instance VMs should be deployed into a Flexible scale-set to future-proof your application for scaling and availability. |
| Deploy across availability zones | Virtual machines should be deployed across [Availability Zones](/azure-docs-pr/articles/virtual-machines/create-portal-availability-zone). Azure availability zones are physically separate locations within each Azure region that are tolerant to local failures.
| Install applications on data disks. | Having your data separate from your OS disk makes it faster to replace troubled VMs without affecting your application deployment.| 
| Monitor and measure health | >> We have a dozen tools - what should we recommend here? |
| Use maintenance control | Control when VM maintenance occurs using [Maintenance Control](/azure/virtual-machines/maintenance-control).|


Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the [Azure Advisor](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/HighAvailability) recommendations.


### Policy definitions

- >> We need something that won't flag for AV sets - not sure if this one still does and Azure policy definition is to *audit standalone single instance VMs that aren't protected by an SLA*. It will flag an audit event for all Virtual Machines that aren't deployed within an Availability Set, across Availability Zones, and aren't using Premium Storage for both OS and Data disks. It also encompasses both Virtual Machine and Virtual Machine Scale Set resources. To view all VM instances that belong to this category, run the query described in [Related resources](#additional-resources).
- To identify resiliency risks to existing compute resources and support continuous compliance for new resources within a customer tenant, it's recommended you use Azure Policy and Azure Resource Graph to Audit the use of non-resilient deployment configurations.
- Azure policy definition is to audit Availability Sets containing single instance VMs that aren't protected by an SLA. It will flag an audit event for all Availability Sets that don't contain multiple instances.

All built-in policy definitions are listed in [Built-in policies - Compute](/azure/governance/policy/samples/built-in-policies#compute).


## Security

This article provides an overview of the core Azure security features that can be used with virtual machines.

>> This has the existing security recommendations, do we just repeat them here? /azure/security/fundamentals/iaas
[H2 section introduction here.]


Concepts:
•	Authentication and access control
•	Role-Based Access Control
•	Managed Identities
•	Encryption
•	Network Security
•	Application Security
•	Secure Key and Secret Storage
Security Best Practices for IaaS workloads:
https://docs.microsoft.com/azure/security/fundamentals/iaas
Separate Excel file has Azure Advisor recommendations (two tabs, there are many)


### Design checklist


As you make design choices for \<product>, review the \[design principles](\<design principles link>) for \<pillar>.

> [!div class="checklist"]
> - Authentication and access control
> - Protect against malware
> - Managed Identities
> - Encryption
> - Secure key and secret storage


### Recommendations


Explore the following table of recommendations to optimize your \<product> configuration for \<pillar>.

Linux secuiryt baseline - /security/benchmark/azure/baselines/virtual-machines-linux-security-baseline
Windows security baseline - /security/benchmark/azure/baselines/virtual-machines-windows-security-baseline

| Recommendation | Benefit |
|--------|----|
| Authentication and access control | [Control VM access and secure privileged access](/azure/security/fundamentals/iaas#protect-vms-by-using-authentication-and-access-control). Ensure that only authorized users can set up and access VMs. |
| Protect against malware | Install [antimalware protection](/azure/security/fundamentals/iaas#protect-against-malware) to help identify and remove viruses. |
| Manage updates | Use a solution like [Azure Automation](/azure/automation/update-management/overview) to manage operating system updates. |
| Monitor for security | To monitor the security posture of your Windows and Linux VMs, use [Microsoft Defender for Cloud](/azure/defender-for-cloud/defender-for-cloud-introduction). |
| Use encryption | Use [Azure Disk Encryption](/azure/security/fundamentals/azure-disk-encryption-vms-vmss) to protect your data. |

For more suggestions, see [Principles of the security pillar](/azure/architecture/framework/security/security-principles).

Azure Advisor helps you ensure and improve security. Review the [recommendations](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/Security).

### Policy definitions


| Message | Description |
|--|--|
| >> Pretty sure these are all for the Azure Compute Gallery feature - VM applications - which is still in Preview and probably not the best to reference: |
| >> Current VM Application Version {name} was deprecated at {date}. | Attempt to deploy a VM Application version that has already been deprecated. |
| >> Current VM Application Version {name} supports OS {OS}, while current OSDisk's OS is {OS}. | Attempt to deploy a Linux application to Windows or vice versa. |
| >> The maximum number of VM applications (max=5, current={count}) has been exceeded. Use fewer applications and retry the request. | We currently only support five VM applications per VM or VMSS. |
| >> More than one VMApplication was specified with the same packageReferenceId. | An application was specified more than once. |
| >> Subscription not authorized to access this image. | The subscription does not have access to this application version. |
| >> Storage account in the arguments does not exist. | There are no applications for this subscription. |
| >> The platform image {image} is not available. Verify that all fields in the storage profile are correct. For more details about storage profile information, please refer to https://aka.ms/storageprofile. | The application does not exist. |
| >>> Are we going to recommend Azure Compute Gallery for image creation and storage? I don't see it anywhere else: |
| >>> The gallery image {image} is not available in {region} region. Please contact image owner to replicate to this region, or change your requested region. | The gallery application version exists, but it was not replicated to this region. |

All built-in policy definitions related to Azure Virtual Machines are listed in \[Built-in policies - \<category>]\(/azure/governance/policy/samples/built-in-policies#\<anchorlink>\).

## Cost Optimization

To estimate costs related to virtual machines, use these tools.

- Identify the best VM for your workloads with the virtual machines selector. See [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) and [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) pricing.
- Use this [pricing calculator](https://azure.microsoft.com/pricing/calculator/#virtual-machines) to configure and estimate the costs of your Azure VMs.

To optimize costs, review the [design principles](/azure/architecture/framework/cost/principles).

### Design considerations
> [!div class="checklist"]
> - Shut down VM instances which aren't in use.
> - Use Spot VMs when appropriate.
> - Consider using Burstable (B) series VM sizes for VMs that are idle most of the time and have high usage for a certain period of time.
> - Use [Zone to Zone disaster recovery](/azure/site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery) for virtual machines.
> - Prepay for Reserved Instances for one year, three years, or more.
> - Hybrid benefit licensing

### Recommendations

Explore the following table of recommendations to optimize your Virtual Machine configuration for service cost:

|Recommendation|Benefit|
|------------------------------|-----------|
| Stop VMs during off-hours | Configuring start and stop times will shut down instances that aren't in use. The feature is suitable as a low-cost automation option. |
| Use Spot VMs when appropriate.|Spot VMs are ideal for workloads that can be interrupted, such as highly parallel batch processing jobs. These VMs take advantage of the surplus capacity in Azure at a lower cost. They're also well suited for experimenting, developing, and testing large-scale solutions.|
|Consider using Burstable (B) series VM sizes for VMs that are idle |The B-series VMs are ideal for workloads that don't need the full performance of the CPU continuously such as web servers, proof of concepts, small databases, and development build environments.|
|Use [Zone to Zone disaster recovery](/azure/site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery) | Replicate, failover, and fail-back your business-critical virtual machines within the same region with zones. Ideal for those customers that have complicated networking infrastructure and want to avoid the cost, and complexity of recreating infrastructure in a secondary region. |
|Prepay for added cost savings | Purchasing [reserved instances](/virtual-machines/prepay-reserved-vm-instances) is a way to reduce Azure costs for workloads with stable usage. Make sure you manage usage. If usage is too low, then you're paying for resources that aren't used. Keep reserved instances simple and keep management overhead low to prevent increasing cost.|
| Use existing licensing through the hybrid benefit licensing program | Hybrid benefit licensing is available for both [Linux](/azure/virtual-machines/linux/azure-hybrid-benefit-linux) and [Windows](/azure/virtual-machines/windows/hybrid-use-benefit-licensing)|

 Azure Advisor helps you ensure and improve cost optimization. Review the [recommendations](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/Cost).

## Operational excellence


[H2 section introduction here.]

### Design checklist


As you make design choices for \<product>, review the \[design principles](\<design principles link>) for \<pillar>.

> [!div class="checklist"]
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>

### Recommendations


Explore the following table of recommendations to optimize your \<product> configuration for \<pillar>.

| Recommendation | Benefit |
|--------|----|
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |

For more suggestions, see [Principles of the operational excellence pillar](/azure/architecture/framework/devops/principles).

Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the [recommendations](../../contribute-how-to-write-waf-for-azure-offerings.md).

### Policy definitions


- \<Policy>
- \<Policy>
- \<Policy>

All built-in policy definitions related to Azure Virtual Machines are listed in \[Built-in policies - \<category>]\(/azure/governance/policy/samples/built-in-policies#\<anchorlink>\).


## Performance efficiency

Performance efficiency is matching the resources that are available to an application with the demand that it's receiving. Performance efficiency includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing your application code for peak performance.
Overview of the principles of Performance Efficiency:
/azure/architecture/framework/scalability/overview

Learn Modules about Performance Efficiency:
One page:
learn/modules/azure-well-architected-introduction/5-performance-efficiency

Similar to above link but much more exhaustive:
/learn/modules/azure-well-architected-performance-efficiency/

Concepts:
•	Scaling up (bigger instances)
•	Scaling out (more instances)
•	Autoscaling
•	Optimizing Network
•	Optimizing Storage
•	Utilizing Caching
•	Identifying Performance Bottlenecks

Performance Efficiency Pattern:
/azure/architecture/framework/scalability/performance-efficiency-patterns
Performance Efficiency Checklist:
/azure/architecture/framework/scalability/performance-efficiency
Virtual Machine Scale Sets:
/azure/virtual-machine-scale-sets/overview
Azure Advisor:
/azure/advisor/advisor-performance-recommendations
Relevant items:
•	Reduce DNS time-to-live on your Traffic Manager profile to fail over to healthy endpoints faster
•	Use managed disks to prevent disk I/O throttling
•	Improve the performance and reliability of virtual machine disks by using Premium Storage



### Design checklist


As you make design choices for \<product>, review the \[design principles](\<design principles link>) for \<pillar>.

> [!div class="checklist"]
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>

### Recommendations


Explore the following table of recommendations to optimize your \<product> configuration for \<pillar>.

| Recommendation | Benefit |
|--------|----|
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |

For more suggestions, see [Principles of the performance efficiency pillar](/azure/architecture/framework/scalability/principles).

Azure Advisor helps you ensure and improve performance. Review the [recommendations](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/Performance).

### Policy definitions

- Audit VMs that do not use managed disks - This policy audits VMs that do not use managed disks

All built-in policy definitions related to Azure Virtual Machines are listed in \[Built-in policies - \<category>]\(/azure/governance/policy/samples/built-in-policies#\<anchorlink>\).

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your Virtual Machines.

### Reliability
- [Use managed disks to improve data reliability](/azure/advisor/advisor-high-availability-recommendations#use-managed-disks-to-improve-data-reliability)
- [Protect your virtual machine data from accidental deletion](/azure/advisor/advisor-high-availability-recommendations#protect-your-virtual-machine-data-from-accidental-deletion)

### Cost Optimization
- [Optimize virtual machine spend by resizing or shutting down underutilized instances](/azure/advisor/advisor-cost-recommendations#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Buy reserved virtual machine instances to save money over pay-as-you-go costs](/azure/advisor/advisor-cost-recommendations#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)|


## Additional resources
Here are other resources to help you query for unhealthy instances.

### Query to identify unprotected resources 
Use the following query to *identify standalone single instance VMs that aren't protected by a minimum SLA of at least `99.5%`*. The query will return all VM instances that aren't deployed within an Availability Set, across Availability Zones, and aren't using either Standard SSD or Premium SSD for both OS and Data disks. This query can be altered easily to identify all single instance VMs, including those using Premium Storage, which are protected by a minimum SLA of at least `99.5%`. Remove the trailing `where` condition:

```sql
Resources
| where
    type =~ 'Microsoft.Compute/virtualMachines'
        and isnull(properties.availabilitySet.id)
    or type =~ 'Microsoft.Compute/virtualMachineScaleSets'
        and sku.capacity <= 1
        or properties.platformFaultDomainCount <= 1
| where 
    tags != '{"Skip":""}'
| where 
    isnull(zones)
| where
    properties.storageProfile.osDisk.managedDisk.storageAccountType !in ('Premium_LRS'
    or properties.storageProfile.dataDisks.managedDisk.storageAccountType != 'Premium_LRS'
        and array_length(properties.storageProfile.dataDisks) != 0
```

>> This is old, we probably shouldn't query for AV Sets? >> The following query expands on the identification of standalone instances by *identifying any Availability Sets containing single instance VMs*, which are exposed to the same risks as standalone single instances outside of an Availability Set:

```sql
Resources
| where 
    type =~ 'Microsoft.Compute/availabilitySets'
| where 
    tags != '{"Skip":""}'
| where 
    array_length(properties.virtualMachines) <= 1
| where
    properties.platformFaultDomainCount <= 1
```

### Cost analysis
Planned versus actual spending can be managed through [Azure Cost Management + Billing](/azure/cost-management-billing/costs/quick-acm-cost-analysis).
There are several options for grouping resources by billing unit. 


## Next steps
Use the recommendations as you provision virtual machines for your solution.
- [Quickstart: Create a Linux virtual machine in the Azure portal](/azure/virtual-machines/linux/quick-create-portal)
- [Quickstart: Create a Windows virtual machine in the Azure portal](/azure/virtual-machines/Windows/quick-create-portal)

- Learn module: [Introduction to Azure virtual machines](/learn/modules/intro-to-azure-virtual-machines/)

- Review the Virtual Machine recommendations provided by [Azure Advisor](/azure/advisor/).

- Review the built-in definitions provided by Azure Policy that apply to Virtual Machines. They are listed in the [Built-in policies - Compute](/azure/governance/policy/samples/built-in-policies#compute).