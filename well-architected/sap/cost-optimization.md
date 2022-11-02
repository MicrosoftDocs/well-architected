---
title: SAP workload cost optimization
description: SAP workload best practices for cost optimization 
author: stephen-sumner
ms.author: ssumner
ms.date: 11/11/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom: SAP
---
# SAP workload cost optimization

Cost optimization allows you to reduce the Total Cost of Ownership (TCO) of an SAP workload and maximize its business value. Microsoft makes significant investments in the fast evolution of its hardware to provide more value for less. The frequent increase in Azure hardware capability provides regular opportunity for an SAP workload to optimize costs, eliminate waste, and improve technology. To align Azure and your SAP workload, we recommend creating a roadmap for each SAP workload that contains the objectives and motivations for the workload. Organizational objectives and investment priorities should drive cost optimization.

We recommend that you focus on your SAP workload’s infrastructure and SAP applications for cost optimization.

## Optimize workload compute and storage

Optimizing SAP-workload infrastructure can turn infrastructure cost into a business investment. Infrastructure is the foundation of cost optimization and should be a scaled process during the initial cloud adoption phases. However, cost optimization is an ongoing process that requires continuous monitoring. You should monitor the compute and storage costs of an SAP workload to ensure the dollars spent are helping you meet business goals.

For more information, see:

- [SAP on Azure](https://azure.microsoft.com/solutions/sap/#overview)
- [SAP NetWeaver](/azure/virtual-machines/workloads/sap/planning-guide)
- [SAP HANA install](/azure/virtual-machines/workloads/sap/hana-get-started)
- [SAP HANA configuration](/azure/virtual-machines/workloads/sap/hana-vm-operations)

**(1) Optimize workload compute** - Compute optimization refers to choosing the right virtual machine (VM) to run each SAP workload. VMs provide the compute power for the SAP application and have a direct effect on cost and performance. Azure VMs have six types and multiple sizes for each type. VM types are optimized to run different types of workloads, and VMs sizes determine the number of cores, memory, and throughput allocated to the VM. In general, compute cost optimization is achieved through planning, monitoring, and resizing VMs to meet the needs of the SAP workload throughout its lifecycle.

- ***Choose the right VM type*** - Azure has SAP-certified VMs that can host each SAP workload. Choosing wrong VM type forces you to choose larger VMs to get the performance need. This misfit increases costs without benefit. A smaller VM of the correct type can give you equal or better performance than a large instance of the wrong size.

    Azure offers a list of SAP-certified configurations to help you understand what VMs work well with your business needs. For more information, see [SAP certified infrastructure]( https://azure.microsoft.com/solutions/sap/azure-solutions/#certified-infrastructure).

    Memory-optimized VMs can meet the requirements of most SAP applications. SAP online analytical processing (OLAP) workloads and an online transactional processing (OLTP) workloads should almost exclusively use M-series VMs. OLTP workloads include SAP HANA, SAP Business Suite on HANA, and SAP S/4HANA. OLAP workloads include SAP BW on HANA BW/4HANA. SAP Business One on HANA also pairs with M-series VMs.

    SAP NetWeaver, Business All-in-One, Business Suite Software, and BusinessObjects BI have broad capability with VM types. They can run on VMs in the A, D, G, E, and M-series.

- ***Optimize throughout migration*** - Many SAP journeys start on-premises, so it’s important to plan for compute optimization throughout the migration of a workload. Make sure to follow best practices across every SAP migration. You can find process guidance in our [SAP documentation](/azure/cloud-adoption-framework/scenarios/sap/). You’ll need to customize your compute optimization for each SAP workload.

    *Pre-migration* – Pre-migration optimization ensures you have sufficient cloud resources provisioned to support the expected migration runtime of the SAP workload. You want to verify the Azure VM meets the technical requirements of the on-premises workload. Planning will shorten the migration time of a workload and minimize the time of migration will keep costs lower.

    *Post-migration* – Post-migration optimization focuses on the end-user experience. This step coincides with the hypercare period, a time of elevated customer service to make sure that the workload is performing. You should monitor the workload as users begin to interact with it. The performance metrics might indicate that you need to downsize the VM or switch to a different VM type.

- ***Optimize in operations*** - It’s important to optimize VMs in operations for the most cost-savings. The operations phase refers to the daily management of an SAP workload, and with it, the ability to predict workload compute needs. It’s important to see how user demand affects compute needs over time. The VM choice should change along with the SAP workload’s requirements. Azure Advisor is a valuable tool for optimizing VM usage. For more information, see [Azure Advisor cost optimization](/azure/advisor/advisor-cost-recommendations).

Here are a few other cost-saving recommendations:

- ***Enforce VM governance*** – VM governance enhances security and can lower cost and should be enforced at VM creation. Every VM deployment increases the attack surface of the SAP workload and its operational cost. VMs created outside of the governance process are often forgotten about and left running, creating an attack surface without managed protection and unneeded expense. Locking down the VM-creation process improves the security posture of the SAP workload and avoids increasing costs.
- ***Stop a non-critical SAP workload*** – Each SAP workload has different levels of criticality. Some workloads aren’t needed on nights and weekends such as a workload sandbox environment. Stopping VMs that support non-critical workload environment can reduce costs. Azure allows you to automate the "snooze" process for SAP VMs. For more information, see [automate VM start and stop](/azure/automation/automation-solution-vm-management).
- ***Use Reserved Instances*** - Any SAP workload that needs to run continuously should use reserved instances to keep down costs. For budget predictability, you can make an advanced purchase for one or three years in a specified region. For more information, see [Azure Reservations](/azure/cost-management-billing/reservations/save-compute-costs-reservations).
- ***Use the Azure Hybrid Benefit*** - Azure lets you use on-premises Software Assurance-enabled Windows Server and SQL Server licenses. The benefit applies to RedHat and SUSE Linux subscriptions. This step can generate significant savings for a hybrid SAP workload. For more information, see [hybrid licensing benefit](https://azure.microsoft.com/pricing/hybrid-benefit/#calculator).

**(2) Optimize workload storage** - Storage hosts active and backup data and affects the performance, availability, and recoverability of an SAP workload. Each workload will have its own storage requirements.

- ***Use reserved capacity storage type*** - There are several storage options available for customer to choose based on the workload requirement. Managed disks, blog storage, and backup storage can support an SAP workload in various combinations. Each of these options also comes with storage reservation options to lower costs for persistent data.

    For more information, see:

  - [Azure disk reserved capacity](/azure/virtual-machines/disks-reserved-capacity)
  - [Blob storage reserved capacity](/azure/storage/blobs/storage-blob-reserved-capacity?toc=%2Fazure%2Fcost-management-billing%2Freservations%2Ftoc.json)
  - [Azure Backup Storage reserved capacity](/azure/backup/backup-azure-reserved-pricing-optimize-cost)

- ***Use lifecycle management policies*** - Other than the capacity reservations, we also need to ensure the right retention period if set for the VM & database backup of an SAP workload. An SAP database backup can be large and add to the storage cost if not optimized. We recommend that you create a lifecycle policy that meets the RTO and RPO of the SAP workload. The policy should move into Premium, Standard, Cold, Archive storage based on its age and business requirements.

## Optimize the SAP application

Optimizing your SAP application can lower the total cost of ownership without reducing capabilities. The goal is to generate the maximum return on investment (ROI). Here’s a few ways to optimize an SAP application:

***(1) Identify application responsibility*** - Optimizing an SAP Application should be the responsibility of the customer business application team. Having responsibility for costs will help drive decisions that optimize costs over the lifecycle of the SAP workload.

***(2) Rationalize and rearchitect*** - You should consider rationalizing or rearchitecting the SAP application, especially during migrations. S4 HANA often replaces older SAP applications that can be added as a legacy system.

***(3) Minimize investment in legacy systems*** - Host a legacy SAP application on minimum-supported architecture to help reduce cost and make sure that system is still useable. A legacy application is slower and less performant. Any legacy systems that remain after rationalizing and rearchitecting should receive the minimum spend possible and be retired when appropriate. For more information, see [Azure Cost Management](/azure/cost-management-billing/costs/cost-mgt-best-practices).

## Next Step

>[!div class="nextstepaction"]
>[Overview](./overview.md)

>[!div class="nextstepaction"]
>[Reliability](./reliability.md)

>[!div class="nextstepaction"]
>[Security](./security.md)

>[!div class="nextstepaction"]
>[Operational Excellence](./operational-excellence.md)

>[!div class="nextstepaction"]
>[Performance Efficiency](./performance-efficiency.md)
