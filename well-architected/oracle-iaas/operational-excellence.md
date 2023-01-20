---
title: Oracle workload operational excellence
description: Oracle workload best practices for operational excellence
author: ckittel
ms.author: kegorman
ms.date: 12/29/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---
# Oracle workload operational excellence

Operational excellence is about creating efficient processes to support your Oracle workload. Operations will be the longest phase of the Oracle workload lifecycle, and teams must be equipped with operational best practices to manage the day-today tasks. Failure in operations will affect the other design areas and the overall success of the Oracle workload. It’s critical to tailor your operations to support an Oracle workload in operations. Below are recommendations to drive operational excellence.

## Monitor the workload

Monitoring and diagnostics are crucial for Oracle on Azure IaaS. As Oracle is a non-native solution running in Azure, Oracle Cloud Control offers the ability to monitor, manage and automate much of Oracle with little additional cost. Oracle Cloud Control comes with a Limited Use Enterprise Edition License, so the database repository does NOT require additional licensing, only the management packs are licensed to each target database. This ensures the same or similar tools that Oracle technologists have used on-premises are available in the cloud. We recommend you review the [Operational excellence design principles.](../devops/principles.md)

Oracle Cloud Control has text logs with a clear format that can be exported and loaded into [Azure Log Analytics](/azure/azure-monitor/logs/log-analytics-overview) to offer a single pane of glass when monitoring Oracle database information. Cloud Control can be used with management packs to add more functionality, including “simulating” a PaaS experience for IaaS solutions such as Oracle in Azure. When using the Cloud and Lifecycle Management Packs for Oracle Cloud Control, full cycle management can be added to the system- automating patching of both database and OS, along with cloning and deployments.

## Azure policy and operational excellence

Many built-in Azure Policy definitions come in Deploy if Not Exists (DINE) versions for the resources that commonly make up Oracle solutions on Azure. Infrastructure as code (IaC) is the gold standard for resource deployment in business-critical applications, such as Oracle workloads. DINE policies perform imperative operations on your infrastructure and happen outside of the declarative model of IaC. Consider their usage carefully, opting for IaC-based deployments of infrastructure and architecture decisions, leaving Azure Policy for governance, not workload deployment/configuration.

## Recommendations

Explore the following table of recommendations to optimize your ExpressRoute configuration for Operational excellence:

| Recommendation | Benefit |
| --- | --- |
| Use Perf Insights | Use [Perf Insights](/azure/virtual-machines/how-to-use-perfinsights) to monitor the overall cloud landscape.|
| Configure ExpressRoute connection monitoring between your on-premises and Azure network. | In case of failures or performance issues, it provides a first view into the network. |
| Consider Oracle Cloud Control for Oracle |Oracle is a non-native product and Oracle provides an infrastructure monitoring, management and automation tool that can be deployed on a VM with little to no cost to the customer, as it’s most likely already used on-premises. Deploy to the cloud to eliminate egress to an on-premises version and evolve to automate with: <br>- Monitoring templates <br>- Metric Extensions (self-service metrics) <br> - Corrective Actions <br> - OS and database patch automation <br/><br/> Oracle Cloud Control will “simulate” a PaaS like solution for IaaS when using it to manage, monitor and automate Oracle.|

## Next Step

>[!div class="nextstepaction"]
>[Performance Efficiency](./performance-efficiency.md)