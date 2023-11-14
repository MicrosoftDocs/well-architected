---
title: Security and Application Insights
description: Focuses on the Application Insights service used in the Monitoring solution to provide best-practice and configuration recommendations related to Security.
author: martinekuan
ms.author: martinek
ms.date: 01/31/2022
ms.topic: conceptual
products:
  - azure-application-insights
categories:
  - monitoring
  - management-and-governance
---

# Security and Application Insights

Application Insights is a feature of [Azure Monitor](/azure/azure-monitor/overview). This feature provides extensible application performance management (APM) and monitoring for live web apps.

Key features include:

- Supports a wide variety of platforms, including .NET, Node.js, Java, and Python.
- Works for apps hosted on-premises, hybrid, or on any public cloud.
- Integrates with DevOps processes.
- Has connection points to many development tools.
- Can monitor and analyze customer data from mobile apps by integrating with Visual Studio App Center.

For more information, reference [Application Insights overview](/azure/azure-monitor/app/app-insights-overview).

## Checklist

**Have you configured Application Insights with security in mind?**

> [!div class="checklist"]
> - Review instances where customer data is captured in your application.

## Configuration recommendations

Consider the following security recommendation when configuring Application Insights:

|Recommendation|Description|
|--------------|-----------|
|Review instances where customer data is captured in your application.|We don't recommend collecting customer data in Application Insights, although it can be unavoidable. It's up to you and your company to determine the strategy you'll use to handle your private data.|

## Next step

> [!div class="nextstepaction"]
> [Cost optimization and Application Insights](cost-optimization.md)