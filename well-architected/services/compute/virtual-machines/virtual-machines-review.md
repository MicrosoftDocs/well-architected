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

## Prerequisites

- Understanding the Well-Architected Framework pillars can help produce a high quality, stable, and efficient cloud architecture. We recommend that you review your workload using the [Microsoft Azure Well-Architected Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.

- Use a reference architecture to review the considerations based on the guidance provided in this article. We recommend, you start with [Run a Linux VM on Azure](/azure/architecture/reference-architectures/n-tier/linux-vm).


## Reliability
As you make design choices for virtual machines, review the [design principles](/azure/architecture/framework/resiliency/principles) for adding reliability to the architecture. 

### Design checklist
> [!div class="checklist"]
> - When defining test availability and recovery targets, review [SLAs for virtual machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/). Singleton workloads should use Premium Managed Disks to enhance resiliency and obtain a 99.9% SLA as well as dedicated performance characteristics. Non-Singleton workloads should consider two or more replica instances with Managed disks (Standard or Premium) that are deployed within an [Availability Set](/azure/virtual-machines/manage-availability) to obtain a `99.95%` SLA or across [Availability Zones](/azure/availability-zones/az-overview#availability-zones) to obtain a `99.99%` SLA.
> - Where appropriate, virtual machines should be deployed across Availability Zones to maximize resilience within a specific Azure region.    
> - Provide resiliency to failures by using Managed Disks should for all virtual machine OS and data disks to ensure resiliency across underlying storage stamps within a data center. 
> - Allow for reliability in scalability and performance.  To ensure application scalability while navigating within disk sizing thresholds, it's highly recommended that applications be installed on data disks rather than on the OS disk. 
> - Monitor and measure health by using virtual machine Resource Health alerts and enable diagnostic logging for all virtual machines.

### Recommendations
Explore the following table of recommendations to optimize your Virtual Machine configuration for service reliability:

|Recommendation|Benefit|
|------------------------------|-----------|
|Use [Azure Metadata Service](/azure/virtual-machines/windows/scheduled-events) Scheduled Events to proactively respond to maintenance events.|Your application will have time to prepare for upcoming maintenance events and limit disruption.|
|Set virtual machine [Resource Health Alerts](/azure/service-health/resource-health-alert-arm-template-guide) on health events with an appropriate threshold for unavailability.|Key stakeholders are notified when relevant health events occur. The threshold value will minimize signal to noise ratios so that transient faults don't generate an alert. For example, configuring a virtual machine alert with an unavailability threshold of one minute before an alert is triggered.|
|Where appropriate, virtual machines should be deployed across Availability Zones to maximize resilience within a specific Azure region.|Within a region, Availability Zones offer unique physical locations within an Azure region, with one or more datacenters equipped with independent power, cooling, and networking. See [Datacenter Fault Tolerance](/azure/virtual-machines/availability#use-availability-zones-to-protect-from-datacenter-level-failures) and [High availability and disaster recovery for IaaS apps](/azure/architecture/example-scenario/infrastructure/iaas-high-availability-disaster-recovery).|
|Consider using proximity placement groups (PPGs) with Availability Zones (AZ) to have redundant in-zone VMs.|It's not possible to create an Availability Set (AS) inside an Availability Zone (AZ) and it's also not possible to control the distribution of VMs within a single availability zone across different fault domains (FD), and update domains (UD). All VMs within a single availability zone might share a common power source and network switch, and can all be rebooted, or affected, by an outage or maintenance task at the same time. If you create VMs across different AZs, your VMs are effectively distributed across different FDs and UDs. If you want to achieve redundant in-zone VMs and cross-zone VMs, you should place the in-zone VMs in proximity placement groups within availability sets to ensure they won't all be rebooted at once. Go to [Combine ASs and AZs with PPGs](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios#combine-availability-sets-and-availability-zones-with-proximity-placement-groups) for detailed instructions.
|Enable diagnostic logging for all virtual machines to ensure you route health metrics, boot diagnostics, and infrastructure logs to Log Analytics or an alternative log aggregation technology.|Platform logs provide detailed diagnostic and auditing information for Azure resources, and the Azure platform they depend on. Reference [Overview of Azure platform logs](/azure/azure-monitor/essentials/platform-logs-overview) for more information.|
|Enable [Azure Backup Soft Delete](/azure/backup/backup-azure-security-feature-cloud) for the Recovery Services vault to protect against accidental or malicious deletion of backup data, ensuring the ability to recover.|Even if a malicious actor deletes a backup (or backup data is accidentally deleted), the backup data is kept for `14` more days, allowing the recovery of that backup item with no data loss. The extra `14` days of retention for backup data in the soft delete state don't incur any cost to you.|

Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the recommentations [here](#azure-advisor-recommendations).


### Policy definitions

- Azure policy definition is to *audit standalone single instance VMs that aren't protected by an SLA*. It will flag an audit event for all Virtual Machine instances that aren't deployed within an Availability Set, across Availability Zones, and aren't using Premium Storage for both OS and Data disks. It also encompasses both Virtual Machine and Virtual Machine Scale Set resources. To view all VM instances that belong to this category, run the query described in [Related resources](#additional-resources).
- To identify resiliency risks to existing compute resources and support continuous compliance for new resources within a customer tenant, it's recommended you use Azure Policy and Azure Resource Graph to Audit the use of non-resilient deployment configurations.
- Azure policy definition is to audit Availability Sets containing single instance VMs that aren't protected by an SLA. It will flag an audit event for all Availability Sets that don't contain multiple instances.

All built-in policy definitions are listed in [Built-in policies - Compute](/azure/governance/policy/samples/built-in-policies#compute).

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
> - Review SKUs that could benefit from Reserved Instances for one year, three years, or more.

### Recommendations

Explore the following table of recommendations to optimize your Virtual Machine configuration for service cost:

|Recommendation|Benefit|
|------------------------------|-----------|
|Use the Start and Stop VMs during off-hours feature of virtual machines to minimize waste.| Configuring start and stop times will shut down instances that aren't in use. The feature is suitable as a low-cost automation option. |
|Use Spot VMs when appropriate.|Spot VMs are ideal for workloads that can be interrupted, such as highly parallel batch processing jobs. These VMs take advantage of the surplus capacity in Azure at a lower cost. They're also well suited for experimenting, developing, and testing large-scale solutions.|
|Consider using Burstable (B) series VM sizes for VMs that are idle most of the time and have high usage for a certain period of time.|The B-series VMs are ideal for workloads that don't need the full performance of the CPU continuously such as web servers, proof of concepts, small databases, and development build environments.|
|Use [Zone to Zone disaster recovery](/azure/site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery) for virtual machines.| Replicate, failover, and failback your business-critical virtual machines within the same region with zones. Ideal for those customers that have complicated networking infrastructure and want to avoid the cost, and complexity of recreating infrastructure in a secondary region. For more information about regions, reference [Products available by region](https://azure.microsoft.com/global-infrastructure/services/).|
|Review SKUs that could benefit from Reserved Instances for one year, three years, or more.|Purchasing reserved instances is a way to reduce Azure costs for workloads with stable usage. Make sure you manage usage. If usage is too low, then you're paying for resources that aren't used. Keep RI instances simple and keep management overhead low to prevent increasing cost.|

Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the recommentations [here](#azure-advisor-recommendations).

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

The following query expands on the identification of standalone instances by *identifying any Availability Sets containing single instance VMs*, which are exposed to the same risks as standalone single instances outside of an Availability Set:

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