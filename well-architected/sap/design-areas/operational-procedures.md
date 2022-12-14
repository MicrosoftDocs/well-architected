---
title: SAP workload operational excellence
description: SAP workload best practices for operational excellence
author: stephen-sumner
ms.author: ssumner
ms.date: 12/19/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom: SAP
---

# SAP workload operational excellence

(**NEED AN INTRO**)
## Use health checks and assessments

Standard operating procedures (SOPs) are documented processes for managing a workload. Each SAP workload should have SOPs to govern operations. Without SOPs, teams drift from management best practices, so we recommend a continuous cycle of assessment and health checks for your SAP workload.

**Run health checks.** We have four Azure SAP (AzSAP) health checks: (1) deployment checklist, (2) inventory checklist, (3) quality checks, and (4) Linux VM OS analyzer. The image below shows how they share a cycle with our Azure SAP assessments. For more information on the health checks, see [SAP quality checks](/azure/center-sap-solutions/get-quality-checks-insights).

:::image type="content" source="../images/azsap-health-check.png" alt-text="Diagram showing the Azure SAP assessments and health checks aligned to the customer journey." lightbox="../images/azsap-health-check.png" border="false":::
*Figure 1: The cycle of SAP health checks and assessments throughout th journey.*

**Conduct assessments.** We have three SAP assessments: (1) landing zone accelerator (LZA), (2) Azure SAP (AzSAP) deployment management assessment, and (3) the AzSAP Well-architected framework assessment. These assessments are designed for different stages in the SAP workload lifecycle.

The AzSAP Well-architected framework assessment is for operations. It compares your SAP operations against SAP workload best practices. The assessment encourages continuous improvement by building on each previous assessment.

:::image type="content" source="../images/laz-for-sap-platform-workload.png" alt-text="Diagram showing how the Well-Architected assessment builds a baseline and each iteration of the assessment builds on the previous one." lightbox="../images/laz-for-sap-platform-workload.png" border="false":::
*Figure 2: How the Well-architected assessment creates milestones and builds on these milestones over time.*

The initial assessment creates a baseline, and the next iteration of assessment uses the previous assessment as the starting point. It will maintain the selections from the last assessment to track and review the design principle. Because the assessment builds on itself, you can track improvements overtime. The assessment is designed for an existing SAP workload in Azure and can assess one or more of the WAF pillars.

We recommend using this SAP assessment to develop and realign the SOPs for your SAP workload. The assessment identifies areas of strength and weakness that allow you to build better SOPs. For more information, see [Azure Well-Architected Review](/assessments/).

## Monitor the workload

Monitoring is the process of collecting, analyzing, and acting on data gathered from an SAP workload. Monitoring provides insights of the health of the workload to compare with an expected baseline. It allows you to know when, where, and why failures occur.

A monitoring best practice is to use a common and consistent logging schema that lets you correlate events across systems. The monitoring and diagnostics process has several distinct phases:

- ***Instrumentation*** - Generating the raw data from application logs, web server logs, the diagnostics built into the Azure platform, and other sources.
- ***Collection and storage*** - Consolidating the data into one place.
- ***Analysis and diagnosis*** - Troubleshooting issues and seeing the overall health.
- ***Visualization and alerts*** - Using data to spot trends or alert your operations team.

We recommend using Azure Monitor for SAP solutions to drive these processes. Azure Monitor for SAP is an Azure-native monitoring product for SAP landscapes that run on Azure. Azure Monitor for SAP solutions uses specific parts of the Azure Monitor infrastructure to provide insights into the monitoring of SAP Netweaver, SAP HANA, SQL Server & Pacemaker High-Availability deployments on Azure. For more information, see [Azure Monitor for SAP Solutions](/azure/virtual-machines/workloads/sap/monitor-sap-on-azure).

## Automate workload infrastructure

You should use infrastructure as code (IaC) to automate SAP workload deployments with minimal human intervention and build a scalable and consistent SAP workload on Azure. The manual process of creating the required SAP workload resources is slow and allows for errors. Microsoft has a repository of SAP deployment templates that you should use. It’s called the SAP on Azure Deployment Automate Framework. The templates support SAP HANA and NetWeaver with any database on any SAP-supported operating systems. For more information, see:

- [SAP deployment automation framework](/azure/virtual-machines/workloads/sap/automation-deployment-framework)
- [SAP automate repository](https://github.com/Azure/sap-automation)
- [Azure Monitor for SAP solutions](/azure/virtual-machines/workloads/sap/monitor-sap-on-azure)

The table below outlines benefits of automated deployments with IaC.

| Benefit domain | Automate deployment benefits | Manual deployment disadvantages |
| --- | --- | --- |
| Knowledge | Works immediately after some initial preparation time. Requires little domain knowledge.| Requires specialized knowledge in many domains outside of SAP.|
| Time | Consumes predictable time from 30 minutes to a couple of hours.| Can take much more time depending on the size of the SAP landscape, depending on the size of the SAP landscape.|
| Cost | Makes automated deployments cheap due to less time spent.| Expensive due to more time spent.|
| Testing | Provides templates that include test instrumentation during deployment and migration.| Allows for limited testing. Requires more work to inject tests in the process. |
| Scaling | Allows you to easily scale up, down, and out. Provides new deployment templates.| Takes more time to scale and customize the environment.|
| Standardization | Applies your defined standards with each deployment.| Sometimes leads to unwanted variations in design.|

## Next steps

> [!div class="nextstepaction"]
> [Application design](./application-design.md)

> [!div class="nextstepaction"]
> [Application platform](./application-platform.md)

> [!div class="nextstepaction"]
> [Data platform](./data-platform.md)

> [!div class="nextstepaction"]
> [Networking and connectivity](./networking-and-connectivity.md)

> [!div class="nextstepaction"]
> [Security](./security.md)
