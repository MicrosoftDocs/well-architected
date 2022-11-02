---
title: SAP workload operational excellence
description: SAP workload best practices for operational excellence
author: stephen-sumner
ms.author: ssumner
ms.date: 11/11/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom: SAP
---

# SAP workload operational excellence

Operational excellence about creating efficient processes that support an SAP workload. Operations will be the longest phase of the SAP workload lifecycle, and teams must be equipped with operational best practices to manage the day-today changes. A failure in operations will affect the success of an organization. It’s critical to tailor your operations to support an SAP workload in operations. For more information, see [WAF operational excellence] ( /azure/architecture/framework/devops/).

## Create standard operating procedure

Standard operating procedures (SOPs) are documented processes for managing a workload. Each SAP workload should have SOPs to govern operations and drive consistency. Without these procedures, operations teams can drift from management best practices.

![The relationship between WAF guidance and an SAP workload](./images/azsap-health-check_highres.png)

Microsoft has made this process easy for an SAP workload. We have three SAP assessments that help you at different stages in the cloud. The Well-Architected Review for SAP on Azure assessment compares your operations against industry best practices. For more information, see [WAF Assessments](https://learn.microsoft.com/assessments).

## Monitor the workload

Monitoring is the process to collect, analyze, and act on the telemetry gathered from an SAP workload. Monitoring provides insights of the health of SAP systems to compare with an expected baseline. The health, security, and reliability of an SAP workload is unknown without monitoring. Monitoring and diagnostics give insight into a system so that you know when and where failures occur.

A best practice is to use a common and consistent logging schema that lets you correlate events across systems. The monitoring and diagnostics process has several distinct phases:

- ***Instrumentation*** - Generating the raw data from application logs, web server logs, the diagnostics built into the Azure platform, and other sources
- ***Collection and storage*** - Consolidating the data into one place
- ***Analysis and diagnosis*** - Troubleshooting issues and seeing the overall health
- ***Visualization and alerts*** - Using telemetry data to spot trends or alert your operations team

## Automate workload infrastructure

You can use infrastructure as code (IaC) to automate SAP workload deployments with minimal human intervention and build a scalable and consistent SAP Estate on Azure. The manual process of creating the required SAP-workload resources is slow and allows for errors. The table below outlines the benefits of automated SAP deployment.

| Benefit domain | Automate deployment benefits | Manual deployment disadvantages |
| --- | --- | --- |
| Knowledge | Works immediately after some initial preparation time. Requires little domain knowledge.| Requires specialized knowledge in many domains outside of SAP.|
| Time | Consumes predictable time from 30 minutes to a couple of hours.| Can take much more time depending on the size of the SAP landscape, depending on the size of the SAP landscape.|
| Cost | Makes automated deployments cheap due to less time spent.| Expensive due to more time spent.|
| Testing | Provides templates that include test instrumentation during deployment and migration.| Allows for limited testing. Requires more work to inject tests in the process. |
| Scaling | Allows you to easily scale up, down, and out. Provides new deployment templates.| Takes more time to scale and customize the environment.|
| Standardization | Applies your defined standards with each deployment.| Sometimes leads to unwanted variations in design.|

Microsoft has a repository of SAP deployment templates that you should use. These templates support SAP HANA and NetWeaver with any database on any SAP-supported operating systems. For more information, see:

- [SAP deployment automation framework](/azure/virtual-machines/workloads/sap/automation-deployment-framework)
- [SAP automate repository](https://github.com/Azure/sap-automation)

## Next Step

>[!div class="nextstepaction"]
>[Overview](./overview.md)

>[!div class="nextstepaction"]
>[Reliability](./reliability.md)

>[!div class="nextstepaction"]
>[Security](./security.md)

>[!div class="nextstepaction"]
>[Cost Optimization](./cost-optimization.md)

>[!div class="nextstepaction"]
>[Performance Efficiency](./performance-efficiency.md)
