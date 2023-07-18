---
title: Application considerations for Azure VMware Solution
description: Application considerations for Azure VMware Solution.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 7/18/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avs
---

# Application considerations for Azure VMware Solution

This section aims to build the operating model for the Azure VMware Solution, and the applications inside the Software-Defined Datacenter (SDDC). Standard operating procedures (SOPs) are documented processes for managing a workload. Each Azure VMware Solution workload should have SOPs to govern operations. Without SOPs, teams drift from management best practices, so we recommend a continuous cycle of assessment and health checks for your Azure VMware Solution workload.

## Align to business metrics
**Impact**: _Reliability_

Azure provides Service Level Agreements (SLAs) for all its services. The composite SLA of your workload should include [SLAs for Azure infrastructure](https://azure.microsoft.com/en-us/support/legal/sla/summary/) and SLAs defined for the application.   Being aware of [Azure VMware Solution Design Considerations](https://techcommunity.microsoft.com/t5/azure-migration-and/azure-vmware-solution-recoverability-design-considerations/ba-p/3746509) and using service Tiers in conjuction with application architectures designed for a specific SLAs will help you determine what, how, and where applications should be deployed.

##### Assessment Questions
- You understand how to use service level agreements to deliver the most appropriate and cost-effective levels of availability, security, performance, and reliability for each application?
- Availability targets such as Service Level Agreements (SLAs) for Azure VMware Solution application(s) are defined?

##### Recommendations

- Define Service Level Agreements (SLAs) for the application and its key use cases.
- Determine how much downtime is acceptable. Quantify that value as the Recovery time objective (RTO). 
- Determine the maximum duration of data loss that's acceptable during an outage. Quantify that value as the Recovery point objective (RPO).
- Document the business metrics in your strategy for backup and disaster recovery. 

## Choose a migration approach
**Impact**: _Performance Efficiency, Cost Optimization_

Common approaches for migrating or modernizing to the cloud are Rehost, Refactor, Rearchitect, and Rebuild. Each requires careful rationalization by evaluating the pros and cons. 
Your workload might be better suited for IaaS or PaaS services. Those services might be more cost-effective and performant than migrating to Azure VMware Solution.

The [modernization approach](https://azure.microsoft.com/en-us/resources/cloud-computing-dictionary/what-is-application-modernization), or updating current apps and data to a cloud-first model, can meet your business needs at reduced costs. Evaluate the choices for application and at the Azure infrastructure level.

Application: Choose modernization based on the purpose of the application, life expectancy, supportability, cost, and SLAs.

Infrastructure: Consider the cost of an Azure VMware Solution node against running applications in Azure native services. You can run as many workloads in Azure VMware Solution as possible in the static memory/storage/compute. However, porting applications to Azure native can be more cost-effective than instantiating another Azure VMware Solution node.

The [application assessment](https://learn.microsoft.com/en-us/azure/azure-netapp-files/faq-integration) results can help you understand where servers should be optimally placed.

Your workload might be better suited for IaaS or PaaS services. Those services might be more cost-effective and performant than migrating to Azure VMware Solution.

The modernization approach or updating current apps and data to a cloud-first model, can meet your business needs at reduced costs. Evaluate the choices for application and at the Azure infrastructure level. 
    
- **Application**: Choose modernization based on the purpose of the application, life expectancy, supportability, cost, and SLAs. 

- **Infrastructure**: Consider the cost of an Azure VMware Solution node against running applications in Azure native services. You can run as many workloads in Azure VMware Solution as you can fit in the static memory/storage/compute. However, porting applications to Azure native can be more cost-effective than instantiating another Azure VMware Solution node. 

##### Assessment questions

- You have developed a risk assessment guide and dentified the core scenarios are you protecting against?
- An application assessement has been completed and a dependency map has been created?

##### Recommendations

- **Use assessments** to get a complete picture of the impact on the application, infrastructure, and the adjustments needed in your business strategy. This exercise will help you  understand the workload, its dependencies, and system requirements. You will be better prepared for your migration wave planning.

    - [Strategic Migration Assessment and Readiness Tool (SMART)](/azure/cloud-adoption-framework/plan/smart-assessment)
    - [Tutorial: Assess VMware VMs for migration to Azure VMs](/azure/migrate/concepts-azure-vmware-solution-assessment-calculation)
    - [Application assessment](/azure/architecture/serverless-quest/application-assessment) 

- **Is Azure VMware Solution the right choice?** 

    The application assessment results can help you understand where servers should be optimally placed.

## Establish a security baseline 
**Impact**: _Security_

Secure workloads and validate compliance and regulatory requirements by using Microsoft Defender for Cloud and it's [cloud security policies]((https://learn.microsoft.com/azure/governance/policy/concepts/azure-security-benchmark-baseline)).  This tool provides recommendations on how you can secure your cloud solutions on Azure and compliance with those portions of regulatrory frameworks covered by the application platform. The secuirty baseline applies guidance from the Microsoft cloud security benchmarks and steps required for remediation.	Note that to enable Defender for Cloud, either ARC for VMware or ARC for servers is required.

[Configure Identity and Roles for AVS](https://learn.microsoft.com/en-us/azure/azure-vmware/concepts-identity), the external identity provider (using LDAPS) to enable administrative users to log into the vCenter, NSX-T and HCX appliances using their active directory credentials.  Then use [VMware priviliges in AVS](https://learn.microsoft.com/en-us/azure/azure-vmware/concepts-identity#view-the-vcenter-server-privileges) roles to assign roles to the users.  

Use [Azure Arc for VMware (where available)](https://learn.microsoft.com/en-us/azure/azure-arc/vmware-vsphere/overview) to deploy the AMA agent to virtual machines, otherwise use [Azure Arc for Servers (GA)](https://learn.microsoft.com/en-us/azure/azure-arc/servers/overview). The Microsoft cloud security benchmark provides recommendations on how you can secure your cloud solutions on Azure. This security baseline applies guidance from the Microsoft [cloud security benchmark](https://learn.microsoft.com/azure/governance/policy/concepts/azure-security-benchmark-baseline) version 1.0 through Azure Policy.  

The Microsoft cloud security benchmark recommends securing your cloud solutions on Azure. This security baseline applies controls defined by Microsoft cloud security benchmark version 1.0 to Azure Policy.

##### Assessment questions

- Security requirements are defined for this workload?
- Azure Arc for VMware or Azure Arc for server is used for deploying the Defender for Cloud (AMA) extension?

##### Recommendations

- Apply the recommendations given in the [**security baseline**](/security/benchmark/azure/baselines/azure-vmware-solution-security-baseline) to protect your workload. 

## Identify dependencies
**Impact**: _Reliability, Operational Excellence, Performance Efficiency_

Not all components of the solution are equally critical. Some components can take down the system while others might lead to degraded experience. Use the availability services to minimize outages of critical components.

Create a diagram that clearly shows the critical dependencies that might impact the overall availability of any application - the [Microsoft Assessment dependency map tool](https://learn.microsoft.com/en-us/azure/azure-monitor/vm/vminsights-maps) can be used.  

When deploying workloads into Azure VMware Solution, use the VMware [Affinity Rules](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.resmgmt.doc/GUID-FF28F29C-8B67-4EFF-A2EF-63B3537E6934.html) that are in place to keep VMs that run the same service (ie two servers running behind a load balancer) apart in event of host failures. This keeps the two servers running in Azure VMware Solution supporting the same application from being on the same Azure VMware Solution host and avoids moving VM's to a host where another copy is already running. This is also true for any VMs operating in a HA or clustered fashion within Azure VMware Solution. Note that an affinity rule specifies that the members of a selected virtual machine DRS group can or must run on the members of a specific host DRS group. An anti-affinity rule specifies that the members of a selected virtual machine DRS group cannot run on the members of a specific host DRS group.

When running HA solutions like [Windows Clustering in VMware Environments](https://learn.microsoft.com/en-us/azure/azure-vmware/configure-windows-server-failover-cluster) in your private cloud design, setting up a Windows cluster can help applications realized their SLA requirements. This is critially important especially for database (SQL) workloads running Azure VMware Solution - as there is no auto-failover capability in the Azure VMware Solution private cloud.

##### Assessment questions

- You have completed the Azure Migrate tool and reviewed the results?
- Are you using Microsoft Server Clustering in your private cloud design?

##### Recommendation

- **[Create a Microsoft Assessment dependency map](https://learn.microsoft.com/en-us/azure/azure-monitor/vm/vminsights-maps)** that identifies the critical dependencies. Maintain and check the accuracy of the map at a regular cadence. 
Work with application owners to determine the Azure VMware Solution configuration that will enable the application to operate with reduced functionality or degraded performance in the case of an outage.	[Appliction reliability](https://learn.microsoft.com/en-us/azure/well-architected/resiliency/app-design) requires resilience to respond to outages and to availability. The application should therefore be designed to operate even when impacted by regional, zonal, service or component failures across critical application scenarios and functionality.

## Scale the workload to handle load
**Impact**: _Performance Efficiency, Operational Excellence_

The environment should be able to expand and contract based on load. Handle those operations through automation. User input should be kept to a minimum to avoid typical errors caused by human in loop.
The amount of memory and CPU count are not configurable; you can configure oversubscription for CPU and you can alter the storage policy applied to the Azure VMware Solution datastores and you can add additional datastores use [Azure NetApp Files](https://learn.microsoft.com/en-us/azure/azure-netapp-files/faq-integration). 
To determine the level of oversubscription the environment can support, use the [Azure Migrate VMware Assessement Tool](https://learn.microsoft.com/en-us/azure/migrate/tutorial-assess-vmware-azure-vm). For storage,keep the 25% free space available as per the [Microsoft and VMware guidance](https://learn.microsoft.com/en-us/azure/azure-vmware/concepts-storage).

Deploy vSAN [Azure VMware Solution Storage Policies](https://learn.microsoft.com/en-us/azure/azure-vmware/configure-storage-policy), both thick and thin, that align with your service tier SLAs. The [Default Storage Policy](https://learn.microsoft.com/en-us/azure/azure-vmware/concepts-storage) in Azure VMware Solution is thick provisioned and the default mirror is based on RAID-1. You need to add additional nodes to get alternative (RAID-5, RAID-6) capabilities. The default storage policy in Azure VMware Solution is thick provisioning, change it to thin for higher VM density, and remember to track IO utilization.	For high I/O and large volume changes, use thick provisioned, for more static/lower I/O profiles, use thin provisioning. General guidance is use thin provisioned disks.	While the Default [Storage policies](https://learn.microsoft.com/en-us/azure/azure-vmware/concepts-storage) in Azure VMware Solution are redundant, if your machines require that data be copied to additional vSAN nodes, another policy should be created to ensure that the data meets your enhanced redundancy requirements.

[Azure Monitor Metrics in Azure VMware Solution](https://learn.microsoft.com/en-us/azure/azure-vmware/configure-alerts-for-azure-vmware-solution#work-with-metrics) is a feature of Azure Monitor that collects numeric data from monitored resources into a time-series database. Metrics are numerical values that are collected at regular intervals and describe some aspect of a system at a particular time. Monitor the logs surfaced by the Diagnostics service available in the Azure VMware Solution private cloud to watch for CPU, Memory and storage metrics.  

##### Assessment questions

- vSAN Storage Policies are in place that meet corporate standards, both RAID and FTT policies?
- You are using Azure native tools or are using 3rd party tools for management and monitoring that are Azure aware/supported in Azure?

##### Recommendations

- When sizing for an application, ensure that the virtual machine is sized to handle the largest workload in an Azure VMware Solution or physical hardware environment. You might be able to handle the load with vertical scaling. Migration is a good opportunity to review Azure capabilities such as scale sets and automated vertical scaling of virtual machine size.
- Define high and low threshold values by running performance tests. Observe Azure metrics for the percentage usage of cluster CPU, memory, and storage resources. 
- Set alerts on the threshold values and trigger your the auto-scale node event within in Azure VMware Solution private cloud. 
- Configure storage policies that meet the availability and performance requirements of you applications
- Monitor the CPU, memory and storage use (IO & Consumption) using the diagnostics metrics available for the Azure VMware Solution.

> [!NOTE]
> ![GitHub logo](../_images/github.svg)For an example implementation, see [Auto-Scale function](https://github.com/Azure/azure-vmware-solution/tree/main/avs-autoscale).

## Business Continuity and Disaster Recovery (BCDR)
**Impact**: _Performance Efficiency, Operational Excellence_

Use an Azure validated backup application to ensure you get support from Microsoft and the Vendor together should you ever encounter a need for support to restore a workload. Backups can be stored locally in Azure VMware Solution (not recommended), on Azure disk in the same region, or on Azure disk in a different region (preferred). Select the best option for you data based on the application's SLAs and cost. 

Use an Azure validated disaster recovery application to ensure you get support from Microsoft and the Vendor together should you ever encounter a disaster.	These tools are licensed separately. VMware SRM is a disaster recovery solution designed to minimize downtime of the virtual machines in an Azure VMware Solution environment if there was a disaster. SRM automates and orchestrates failover and failback, ensuring minimal downtime in a disaster. Built-in non-disruptive testing ensures your recovery time objectives are met. VMware SRM simplifies management through automation and ensures fast and highly predictable recovery times. Disaster recovery tools should allow customers to recover into Azure VMware Solution, from Azure VMware Solution to on-premises and between Azure VMware Solution instances, even across regions. As an alternative, Azure Site Recovery can be used to protect workloads running in Azure VMware Solution by replicating VM's to an Azure Native disaster recovery site.	

##### Assessment questions

- You have SLA’s established for your applications?
- SLAs include both an RTO and an RPO. Include these in a recovery run book that details the backup and disaster recovery steps?
- You are using an Azure validated backup application?

##### Recommendation

For backup use  [Microsoft Backup](https://learn.microsoft.com/en-us/azure/backup/backup-mabs-whats-new-mabs) (MABS) is available today, and Microsoft cloud native backup is expected by the end of CY23Q3.  There are also a number of 3rd party backup services,[AVS Backup Partners](https://learn.microsoft.com/en-us/azure/azure-vmware/ecosystem-back-up-vms), available.	

For disaster recovrey use [Azure Site Recovery](https://learn.microsoft.com/en-us/azure/site-recovery/site-recovery-overview), [vMWare Site Recovery Manager (SRM)](https://learn.microsoft.com/en-us/azure/azure-vmware/disaster-recovery-using-vmware-site-recovery-manager), J[etstream](https://learn.microsoft.com/en-us/azure/azure-vmware/deploy-disaster-recovery-using-jetstream), [Zerto](https://learn.microsoft.com/en-us/azure/azure-vmware/deploy-zerto-disaster-recovery) or a third party applications that have partnered with Microsoft. 

## Management
**Impact**: _Operational Excellence_

Arc-enabled Azure VMware Solution allows managing VMs, guest OS extensions, and guest management. [ARC with AVS](https://learn.microsoft.com/en-us/azure/azure-vmware/deploy-arc-for-azure-vmware-solution?tabs=windows) (preview) extends Azure governance and management capabilities to VMware vSphere infrastructure. With Azure Arc-enabled VMware vSphere, there is a consistent management experience across Azure and VMware vSphere infrastructure. Performance metrics and logs are sent to log analytics for analysis.	Operations are related to Create, Read, Update, and Delete (CRUD) virtual machines (VMs) in an Arc-enabled Azure VMware Solution private cloud are captured. Users can also enable guest management and install Azure extensions once the private cloud is Arc-enabled.	

Azure Arc for VMware enables the ability to perform various VMware virtual machine (VM) lifecycle operations directly from Azure, such as create, start/stop, resize, and delete. Azure Arc is also a key component for deploying the Defender extention onto Azure VMware Solution workload servers.

##### Assessment questions

- You are using  Azure Arc to capture logs and metrics from Azure VMware Solution workloads?
- You are using Azure Insights for monitoring the health of Azure native services?

##### Recommendations

- Use [Azure Arc for management](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/scenarios/hybrid/arc-enabled-servers/eslz-management-and-monitoring-arc-server) of workloads in Azure VMware Solution, regulatory compliance monitoring [Arc with the Defender for Cloud Extention](https://learn.microsoft.com/en-us/azure/defender-for-cloud/plan-defender-for-servers-agents)
