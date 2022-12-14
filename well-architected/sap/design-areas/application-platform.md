---
title: SAP workload application platform
description: SAP workload application platform
author: stephen-sumner
ms.author: ssumner
ms.date: 12/19/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom: SAP
---

# SAP workload application platform

***Hosting environment choices, application dependencies, frameworks, and libraries.***

## Platform compute costs

 Compute cost optimization is achieved through planning, monitoring, and resizing VMs throughout the SAP workload lifecycle. VMs provide the compute power for the SAP application and have a direct effect on cost and performance. We recommend monitoring the compute costs of an SAP workload to ensure the dollars spent are helping you meet organizational goals. Here are cost-optimization recommendations for SAP workload compute.

### Choose the right VM type

Azure has SAP-certified VMs for your workload. The wrong VM type will require larger sizes to get the performance need, increasing cost without benefit. A smaller VM of the correct type can give you equal or better performance than a large instance of the wrong type. Azure offers a list of SAP-certified configurations to help you understand what VMs work well with your business needs.

For more information, see [SAP certified infrastructure](https://azure.microsoft.com/solutions/sap/azure-solutions/#certified-infrastructure).

Memory-optimized VMs can meet the requirements of most SAP applications. An SAP online analytical processing (OLAP) workload and an online transactional processing (OLTP) workload should use M-series VMs. Examples of an OLTP workload include SAP HANA, SAP Business Suite on HANA, and SAP S/4HANA. Examples of OLAP workloads include SAP BW on HANA BW/4HANA. SAP Business One on HANA pairs with M-series VMs.

SAP NetWeaver, Business All-in-One, Business Suite Software, and BusinessObjects BI have broader alignment with different VM types. They can run on VMs in the D, G, E, and M-series.

### Optimize compute cost during migration

Many SAP journeys start on-premises, so it’s important to plan for compute optimization throughout the migration of a workload. Make sure to follow best practices across every SAP migration. You can find process guidance in our [CAF SAP documentation]( /azure/cloud-adoption-framework/scenarios/sap/). With the broader process guidance in place, you’ll still need to customize your compute optimization for each SAP workload. We want you to consider pre-migration and post-migration milestones.

***Optimize pre-migration***: Pre-migration optimization ensures you have sufficient cloud resources provisioned to support the expected migration runtime of the SAP workload. You need to verify that the Azure VM meets the technical requirements of the on-premises workload. Planning will shorten the migration time of a workload and minimize the time of migration will keep costs lower.

***Optimize post-migration***: Post-migration optimization focuses on the end-user experience. This step coincides with the hypercare period, a time of elevated customer service to make sure that the workload is performing. You should monitor the workload as users begin to interact with it. The performance metrics might indicate that you need to downsize the VM or switch to a different VM type.

### Optimize compute cost in operations

It’s important to optimize VMs in operations for the most cost-savings. By VM operations, we're referring to the daily management of an SAP workload. This phase of a workload brings the ability to predict the compute needs. It’s important to see how user demand affects compute needs over time. The VM choice should change along with the SAP workload’s requirements. Here are cost-saving recommendations for operations.

***Use Azure Advisor***: We recommend you use Azure Advisor to identify VM usage that needs optimization. For more information, see [Azure Advisor cost optimization](/azure/advisor/advisor-cost-recommendations).

***Enforce VM governance***: You should enforce VM governance at VM creation as a cost and security best practice. Every VM deployment increases the operational cost and attack surface of the SAP workload. VMs created outside of the governance process tend to create unneeded expense and have more vulnerabilities. We recommend using Azure Policy to enforce VM governance for your SAP workload. For more information, see [Azure Policy for SAP](/azure/cloud-adoption-framework/scenarios/sap/eslz-security-governance-and-compliance#use-azure-policy).

***Stop a non-critical SAP workload***: Each SAP workload has different levels of criticality. Some workloads aren’t needed on nights and weekends. A sandbox environment is a good example of low criticality. We recommend stopping VMs that support non-critical workload environment to reduce costs. You can automate this process for the SAP workload in Azure. For more information, see [automate VM start and stop](/azure/automation/automation-solution-vm-management).

***Use Reserved Instances***: Any SAP workload that needs to run continuously should use reserved instances to optimize cost. For budget predictability, you can make an advanced purchase for one or three years in a specified region. For more information, see [Azure Reservations](/azure/cost-management-billing/reservations/save-compute-costs-reservations).

***Use the Azure Hybrid Benefit***: Azure lets you use on-premises Software Assurance-enabled Windows Server and SQL Server licenses. The benefit applies to Red Hat and SUSE Linux subscriptions. This benefit can generate significant savings for a hybrid SAP workload. For more information, see [hybrid licensing benefit](https://azure.microsoft.com/pricing/hybrid-benefit/#calculator).

For more information, see:

- [SAP on Azure](https://azure.microsoft.com/solutions/sap/#overview)
- [SAP NetWeaver](/azure/virtual-machines/workloads/sap/planning-guide)
- [SAP HANA install](/azure/virtual-machines/workloads/sap/hana-get-started)
- [SAP HANA configuration](/azure/virtual-machines/workloads/sap/hana-vm-operations)

## Application server reliability

The goal of application server reliability is to have multiple application servers load balance traffic and failover when needed. Resiliency for the SAP application server layer can be achieved through redundancy. You can configure multiple dialog instances on different instances of Azure virtual machines with a minimum of two application servers. Here are application server resiliency recommendations.

***Use Availability Sets / Availability Zones***: An SAP application server can be deployed in an availability set or across availability zones. The decision you make needs to be based on workload requirements. We recommend you choose one method to improve resiliency, but we don’t recommend scale sets. For more information, see [availability zones for SAP](/azure/virtual-machines/workloads/sap/sap-ha-availability-zones).

***Use multiple application servers***: Using multiple smaller application servers instead of one larger application server is recommended. This setup avoids a single point of failure. It’s a best practice to configure SAP Logon Group (SMLG) and Batch Server Group (RZ12) for better load balancing between end-user & batch processing. For more information, see:

- [Azure Virtual Machines high availability for SAP NetWeaver](/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)
- [SAP HANA high availability for Azure virtual machines](/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [SAP workload configurations with Azure Availability Zones]( /azure/virtual-machines/workloads/sap/sap-ha-availability-zones)

## Compute performance efficiency

Compute is the core that powers an SAP application. Compute includes the hardware, number of cores, and memory. These features are foundational to organizations. If you don’t optimize your compute configuration, an SAP workload will be unable to meet spikes in user demand or stay withing predefined budgets. It’s important to know the demands on your workload and match those demands with the compute you use for your SAP workload. Here are some compute performance considerations.

**Conduct reference sizing for on-premises workload** - Reference sizing is the process of checking the configurations and resource utilization data of an SAP workload on-premises. Reference sizing data shows the current compute needs of the workload, and these needs should be matched in Azure. To find this information, use the SAP OS Collector. SAP OS Collector retrieves system utilization information that can be reported via SAP transaction OS07N and the EarlyWatch Alert. Any system performance and statistics gathering tools can collect similar information.

**Use SAP Quick Sizer for a new workload** - SAP Quick Sizer is a free web-based tool developed by SAP that translates business requirements into technical requirements. Use this tool when you build a new SAP workload to find the Azure VM with the correct network and storage throughput. For more information, see [SAP quick sizer]( https://service.sap.com/quicksizer).

## Next steps

> [!div class="nextstepaction"]
> [Application design](./application-design.md)

> [!div class="nextstepaction"]
> [Data platform](./data-platform.md)

> [!div class="nextstepaction"]
> [Networking and connectivity](./networking-and-connectivity.md)

> [!div class="nextstepaction"]
> [Operational procedures](./operational-procedures.md)

> [!div class="nextstepaction"]
> [Security](./security.md)
