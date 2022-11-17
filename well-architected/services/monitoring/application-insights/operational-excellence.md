---
title: Operational excellence and Application Insights
description: Focuses on the Application Insights service used in the Monitoring solution to provide best-practice and configuration recommendations related to Operational excellence.
author: martinekuan
ms.author: martinek
ms.date: 02/01/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - application-insights
categories:
  - monitoring
  - management-and-governance
---

# Operational excellence and Application Insights

Application Insights is a feature of [Azure Monitor](/azure/azure-monitor/overview). This feature provides extensible application performance management (APM) and monitoring for live web apps.

Key features include:

- Supports a wide variety of platforms, including .NET, Node.js, Java, and Python.
- Works for apps hosted on-premises, hybrid, or on any public cloud.
- Integrates with DevOps processes.
- Has connection points to many development tools.
- Can monitor and analyze customer data from mobile apps by integrating with Visual Studio App Center.

For more information, reference [Application Insights overview](/azure/azure-monitor/app/app-insights-overview).

## Checklist

**Have you configured Application Insights with operational excellence in mind?**

> [!div class="checklist"]
> - Configure Application Insights to monitor the availability and responsiveness of your web application.
> - Be aware that Application Insights can be used to monitor deployed sites and services on-premises (or on an Azure Virtual Machine (VM)).
> - Evaluate Java codeless application monitoring for your Java-based application development stack.
> - Configure sampling in Application Insights.
> - Record custom events and metrics from sites and services in Application Insights.
> - Use Application Insights to ingest existing log traces from common libraries, such as `ILogger`, `Nlog`, and `log4Net`.
> - Become familiar with the Application Insights quotas and limits.
> - Review the need for custom analysis. Use Application Insights data with tools such as Azure Dashboards or Power BI.
> - Separate data across Application Insights resources.

## Configuration recommendations

Consider the following recommendations for operational excellence when configuring Application Insights:

|Recommendation|Description|
|--------------|-----------|
|Configure Application Insights to monitor the availability and responsiveness of your web application.|After you've deployed your application, you can set up recurring tests to monitor availability and responsiveness. Application Insights sends web requests to your application at regular intervals from points around the world. It can alert you if your application isn't responding or if it responds too slowly.|
|Evaluate Java codeless application monitoring for your Java-based application development stack.|Java codeless application monitoring is all about simplicity. There are no code changes. You can enable the Java agent through a couple of configuration changes. The Java agent works in any environment and allows you to monitor all your Java applications. No matter if you're running your Java apps on Virtual Machines, on-premises, in Azure Kubernetes Service (AKS), on Windows, or Linux, the Java `3.0` agent will monitor your app.|
|Configure sampling in Application Insights.|Ingestion sampling operates at the point where the data from your web servers, browsers, and devices reaches the Application Insights service endpoints. Although it doesn't reduce the data sent from your app, it does reduce the amount processed, retained, and charged by Application Insights. Use this type of sampling if your app often goes above its monthly quota. Use ingestion sampling if you don't have access to the Software Development Kit (SDK)-based types of sampling.|
|Record custom events and metrics from sites and services in Application Insights.|Use Application Insights to record domain-specific custom events and metrics from your site or service. For example: *number-of-active-baskets* or *product-lines-out-of-stock*.|
|Use Application Insights to ingest existing log traces from common libraries, such as `ILogger`, `Nlog`, and `log4Net`.|If you're already using a logging framework such as `ILogger`, `Nlog`, `log4Net`, or `System.Diagnostics.Trace`, we recommend sending your diagnostic tracing logs to Application Insights. For Python applications, send diagnostic tracing logs using `AzureLogHandler` in OpenCensus Python for Azure Monitor. You can explore and search these logs, which are merged with the other log files from your application. Merging the log files allows you to identify traces associated with each user request and correlate them with other events and exception reports.|
|Become familiar with the Application Insights quotas and limits.|This information can influence your sampling model and your strategy for separating Application Insights resources.|
|Review the need for custom analysis. Use Application Insights data with tools such as Azure Dashboards or Power BI.|There are several available options to analyze your Application Insights data. For example, you can create a dashboard in the Azure portal that includes tiles visualizing data from multiple Azure resources across different resource groups and subscriptions. Alternatively, you can use Power BI to analyze data combined with data from other sources and share insights.|
|Separate data across Application Insights resources.|It's important to consider when to share a single Application Insights resource and when to create a new one. For example, you should use a single resource for application components that you deploy together, a single Team develops, or that the same set of DevOps or ITOps users manages. You should use a separate resource for different environments.|

## Next step

> [!div class="nextstepaction"]
> [Operational excellence and Application Insights](operational-excellence.md)