---
title: Operational procedure considerations for sustainable workloads on Azure
description: This design area explores operational procedures for sustainable workloads on Azure.
author: Zimmergren
ms.author: tozimmergren
ms.topic: conceptual
ms.date: 07/27/2022
ms.service: architecture-center
ms.subservice: well-architected
categories: 
  - operations
products: Azure
ms.custom:
  - sustainability
---

# Operational procedure considerations for sustainable workloads on Azure

The discipline of green software and its implementation within cloud efficiency patterns is relatively recent, and no specific and universal standards have been agreed upon yet.

The [Green Software Foundation](https://greensoftware.foundation) works on creating and standardizing ways of making green software. However, it's vital that everyone considers this aspect in their daily work and that when designing, planning, and deploying Azure workloads, we consider the best practices that are already available and prepare our environment to incorporate new standards when ready.

This document will guide you through setting up an environment for measuring and continuously improving your Azure workloads' cost and carbon efficiency.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected sustainable workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a sustainable workload?](sustainability-get-started.md#what-is-a-sustainable-workload)

## Measure and track carbon impact

To optimize or improve something, we first must decide what we want to change and how to measure it.
In this section, you'll learn about best practices and guidelines to measure and track the sustainability impact of your workloads.  

### The Emissions Impact Dashboard

An essential aspect of working toward any sustainability goal is tracking and quantifying progress. If you can't track and measure the impact, you'll never be sure if the efforts are worthwhile. The Emissions Impact Dashboard is a Power BI dashboard that will give you a measure of the carbon impact of all your services and resource groups in your Azure subscription(s).

The Emissions Impact Dashboard produce insights in various forms, and allows for a wide range of reporting capabilities:

- Series of visual representations in the dashboard itself.
- Snapshot export to Excel, Power Point and PDF.
- [Continuous export to Microsoft Sustainability Manager](https://docs.microsoft.com/industry/sustainability/import-data-connectors#emissions-impact-dashboard-connector) and Dataverse.

**Recommendation:**

- Use the [Emissions Impact Dashboard](https://appsource.microsoft.com/product/power-bi/coi-sustainability.emissions_impact_dashboard) to record current and future environmental impact.
- Identify and track metrics to quantify the achievement of technical, business, and sustainability outcomes.
- Rely on tooling to help measure the impact, and record any changes made to your workload.
- Learn more about the Sustainability and Dataverse API access in the Microsoft Learn module [Access Microsoft Sustainability Manager data](https://docs.microsoft.com/learn/modules/access-cloud-sustainability/).

### Define emissions target

We've analyzed in previous paragraphs how the Software Carbon Intensity (SCI) is the score you're looking for to measure the carbon impact of your application(s) by adding the scalability and cost metrics to any carbon emissions measurement.

If you aren't using the Emissions Impact Dashboard, there are still ways of building carbon proxies that allow you to measure your application's impact on emissions.

It can be a challenge to build carbon proxies for existing applications. Therefore, we recommend planning for efficiency targets during the design phase of every workload. When adding new workloads to Azure, you should consider planning for costs and emissions that will add to your existing footprint. The main goal should always be not to emit carbon, so ideally, you should immediately find an optimization pattern to make up for the new emissions.

The next step is to define your target emissions, either for a single application or for your entire set of cloud workloads. The target can also include cost constraints, making it even easier to build upon since shrinking costs will give you some budget to optimize emissions. Once you know your target, the cloud efficiency continuous optimization process can start.

**Recommendations:**

- Calculate your new workload's minimum cost and carbon emissions (where applicable).
- Track progress with Service Level Objectives (SLO), Service Level Agreements (SLA), or other performance metrics.
- Provide optimization patterns to accommodate the new application to your overall cloud efficiency score.

### Identify the metrics and set improvement goals

### Cost optimization as a proxy

### Defining policies

### Use resources "cum grano salis"

## Community and knowledge sharing

### Create a sustainability community

### Plan for learning

### Share best practices across teams

### Plan for incentives

## Next step

Review the design considerations for networking and connectivity.

> [!div class="nextstepaction"]
> [Networking and connectivity](sustainability-networking.md)
