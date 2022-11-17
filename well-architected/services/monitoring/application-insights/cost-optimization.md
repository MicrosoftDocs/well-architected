---
title: Cost optimization and Application Insights
description: Focuses on the Application Insights service used in the Monitoring solution to provide best-practice, design, and configuration recommendations related to Cost optimization.
author: v-stacywray
ms.author: martinek
ms.date: 01/31/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - application-insights
categories:
  - monitoring
  - management-and-governance
---

# Cost optimization and Application Insights

Application Insights is a feature of [Azure Monitor](/azure/azure-monitor/overview). This feature provides extensible application performance management (APM) and monitoring for live web apps.

Key features include:

- Supports a wide variety of platforms, including .NET, Node.js, Java, and Python.
- Works for apps hosted on-premises, hybrid, or on any public cloud.
- Integrates with DevOps processes.
- Has connection points to many development tools.
- Can monitor and analyze customer data from mobile apps by integrating with Visual Studio App Center.

For more information, reference [Application Insights overview](/azure/azure-monitor/app/app-insights-overview).

## Design considerations

Application Insights includes the following design considerations for cost optimization:

- Consider using sampling to reduce the amount of data that's sent:

  Sampling is a feature in Application Insights. It's a recommended way to reduce data traffic, data, and storage costs. Refer to [Sampling in Application Insights](/azure/azure-monitor/app/sampling).

- Consider turning off collection for unneeded modules:

  On configuration files, you can enable or disable data modules and initializers for tracking data from your applications. Refer to [Application Insights for web pages](/azure/azure-monitor/app/javascript#configuration).

- Consider limiting Asynchronous JavaScript and XML (AJAX) call tracing:

  AJAX calls can be limited to reduce costs. Refer to [Application Insights for web pages](/azure/azure-monitor/app/javascript#configuration), which explains the fields and its configurations.

## Checklist

**Have you configured Application Insights with cost optimization in mind?**

> [!div class="checklist"]
> - Evaluate usage of daily cap to limit the daily ingestion for your workspace.
> - Use sampling in Azure Application Insights to reduce data traffic, data costs, and storage costs, while preserving a statistically correct analysis of application data.

## Configuration recommendations

Consider the following recommendations for cost optimization when configuring Application Insights:

|Recommendation|Description|
|--------------|-----------|
|Evaluate daily cap usage to limit the daily ingestion for your workspace.|Daily cap is used to manage an unexpected increase in data volume. Use daily cap when you want to limit unplanned charges for your workspace. Use care with this configuration as it can cause some data to be unwritten on Log Analytics workspace if the daily cap is reached. This configuration can impact services whose functionality may depend on the availability of up-to-date data in the workspace. Refer to [Set the Daily Cap](/azure/azure-monitor/app/pricing#set-the-daily-cap) about how to set the daily cap in Application Insights. *Note: If you have a workspace-based Application Insights, use the daily cap in workspace to limit ingestion and costs instead of using the cap in Application Insights.*|

## Next step

> [!div class="nextstepaction"]
> [Operational excellence and Application Insights](operational-excellence.md)
