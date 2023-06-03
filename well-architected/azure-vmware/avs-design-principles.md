---
title: Azure VMware Solution (AVS) cost optimization recommendations
description: TBD.
author: Mahesh-MSFT
ms.author: maksh
ms.date: 05/15/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-vmware
---

# Azure VMware (AVS) design principles

VMware workloads must be based on the key design principles of the Well-Architected Framework, which serve as a compass across the key design areas. We highly recommend that you familiarize yourselves with these principles to better understand their impact and the trade-offs associated with non-adherence.

## Reliability

## Security

## Cost Optimization

|Design principle|Considerations|
|---|---|
|Develop a cost model.|Cost modeling is the exercise of identifying overall workload solution components and associated costs.  </br>▪ [Azure Migrate for Azure VMware Solution](/azure/migrate/how-to-create-azure-vmware-solution-assessment) to estimate costs. Using As on-premises assessment may carry forward inefficient cost planning from on-premise environment to the AVS private cloud. </br>▪ [Azure Reservations](https://learn.microsoft.com/azure/azure-vmware/reserved-instance) and [Azure Hybrid Benefits](https://azure.microsoft.com/pricing/hybrid-benefit/) to take advantage of discounts. </br>▪ Determine whether shared centralized services can be used for [AVS private cloud](/azure/azure-vmware/plan-private-cloud-deployment). </br>▪ Use this [deployment checklist](/azure/azure-vmware/plan-private-cloud-deployment) to determine number of hosts/clusters and the associated costs.</br>▪ [Azure Cost Management](/azure/cost-management-billing/cost-management-billing-overview) guidance can help identify cost optimization opportunities before, during, and after deploying AVS private cloud. </br>▪ Factor additional costs for any [scaling out](https://techcommunity.microsoft.com/t5/azure-migration-and/azure-vmware-solution-auto-scale/ba-p/3690186) operations.|
|Monitor cost to track the workload spend.|To decide appropriate method to break out costs for reporting.</br>▪ Use [Azure Billing](/azure/cost-management-billing/costs/reporting-get-started) to analyze, report and generate invoices. Understand that [taxes, credits, payment methods](/azure/cost-management-billing/costs/reporting-get-started#invoices-and-credits) influence final invoice. </br>▪ For any additional customization needed to correlate costs with usage of specific Azure service, see [Managing usage and costs with Azure Monitor Logs](/azure/well-architected/services/monitoring/log-analytics/cost-optimization).</br>▪  Configure appropriate [data retention and archiving policy](/azure/azure-monitor/logs/data-retention-archive) for storing logs in Log Analytics workspace to reduce associated costs .|
|Have cost governance procedures.|Proactive measures will keep costs at a specified threshold and respond if/when those thresholds are crossed.</br>▪ [Azure Budget](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) to set a predefined cost.</br>▪ [Action Groups](/azure/cost-management-billing/costs/tutorial-acm-create-budgets#trigger-an-action-group) to decide who will receive email alerts for actual and/or forecasted costs.</br>▪ [Anomaly Detection](/azure/cost-management-billing/understand/analyze-unexpected-charges) to detect and respond to unexpected costs and usage patterns.|
|Compare the cost of using AVS to running the workload using Azure native services.|▪ Compare costs for [app modernization](https://azure.microsoft.com/resources/cloud-computing-dictionary/what-is-application-modernization) of certain types of workloads that are deployed with Azure native services.</br>▪ Evaluate costs when adding nodes in AVS. See [When to migrate and when to modernize](https://techcommunity.microsoft.com/t5/azure-migration-and/moving-to-the-cloud-your-guide-on-when-to-migrate-and-when-to/ba-p/3594144) to make a informed decision.</br>▪ Identify any storage or backup data using large capacity from vSAN datastore of AVS. Use [Azure NetApp Files](/azure/azure-vmware/netapp-files-with-azure-vmware-solution) as a vSAN datastore to reduce costs associated with storage.</br>▪ Estimate costs associated with outbound traffic from AVS to on-premises environment over [ExpressRoute circuit](/azure/expressroute/). On-premise environments are connected with AVS private cloud using ExpressRoute GlobalReach which also has costs associated with outbound data transfer.|
|Rationalize cost.|Understand holistic costs besides owning AVS. There are other services such as backup, high availability, automation,. which are essential for running workloads AVS. </br>▪ Use Azure Automation to [auto-scale Azure VMware Solution private cloud](https://techcommunity.microsoft.com/t5/azure-migration-and/azure-vmware-solution-auto-scale/ba-p/3690186#:~:text=This%20Auto%2DScale%20function%20allows,%2C%20memory%2C%20and%20storage%20resources.) and reduce costs associated with management overhead and support operations.</br>▪ Define RPO and RTO for workloads running in AVS. You can control costs associated with [backup](/azure/azure-vmware/ecosystem-back-up-vms) and [disaster recovery (DR)](/azure/azure-vmware/ecosystem-disaster-recovery-vms) related tools. Understand if there are any additional costs associated with support from third-party vendors.</br>▪ There might be additional cost for operationalizing DR site because there are  additional services and tools with licensing costs. Have clear expectations around [SLA and costs](/azure/azure-vmware/disaster-recovery-using-vmware-site-recovery-manager) associated during DR event.</br>▪ Classify workloads on AVS from their Service Level Agreement (SLA) point of view. Such classification helps to control costs associated with implementing different types of solutions for [high availability](https://techcommunity.microsoft.com/t5/azure-migration-and/azure-vmware-solution-availability-design-considerations/ba-p/3682915).


## Operational Excellence


## Performance Efficiency


## Next step

Start by reviewing the design considerations for Azure infrastructure needed to support the workload.

> [!div class="nextstepaction"]
> [Infrastructure](avs-infrastructure.md)
