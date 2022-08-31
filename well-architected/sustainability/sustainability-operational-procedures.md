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

The discipline of green software and its implementation within the cloud efficiency patterns is quite recent and there are no specific and universal standards that have been agreed upon yet. While our fellows at the green software foundation work on creating and standardizing ways of making green software, it is still important that everyone considers this aspect in their daily work, and that when designing, planning and deploying Azure workloads, we consider the best practices that are already available and prepare our environment to be able to incorporate any new standards when ready. This document will guide you through setting up an environment for measuring and continuously improving the cost+carbon efficiency of your Azure workloads.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected sustainable workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a sustainable workload?](sustainability-get-started.md#what-is-a-sustainable-workload)

## Measure and track carbon impact

### Define emission targets

TBD

### Identify the metrics and set improvement goals.

TBD

### Conduct a sustainability review

TBD

### Cost optimization as a proxy

TBD

### Record current and future environmental impact using the Emissions Impact Dashboard

An essential aspect of working toward any sustainability goal is tracking and quantifying the progress. If you can't track and measure the impact, you will never be sure if the efforts are worthwhile.

**Recommendation:**

- Use the [Emissions Impact Dashboard](https://www.microsoft.com/sustainability/emissions-impact-dashboard) to record current and future environmental impact.
  - Identify and track metrics to quantify the achievement of technical, business, and sustainability outcomes.
  - Rely on tooling to help measure the impact, and record any changes made to your workload.

### Define emission targets when designing workloads

TBD

**Recommendation:**

- _Track the progress with [Service Level Objectives](/azure/cloud-adoption-framework/manage/monitor/service-level-objectives) (SLO) or [Service Level Agreements](/azure/architecture/framework/resiliency/business-metrics) (SLA), or other performance metrics._ (Measuring sustainability)

### Consider defining policies for minimum emission standards of any additions or changes made to workloads

TBD (Climate commitments)

### Identify the metrics and set improvement goals with a sustainability perspective

TBD (Measuring sustainability)

### Use cost optimization as a proxy for sustainability

Leverage cost insights to better understand and measure usage. Reducing unnecessary spending also lowers the number of excessive emissions from deployed workloads.

**Recommendation:**

- Review the concept of [using a proxy solution to measure emissions](/azure/architecture/framework/sustainability/sustainability-design-methodology#use-a-proxy-solution-to-measure-emissions).
- Leverage the guidance in the Azure Well-Architected Framework [Cost Optimization pillar](/azure/architecture/framework/cost).

## Community and knowledge sharing

Teams need to be constantly aware of new advancements in sustainability, so they leverage these learnings when implementing workloads.

### Make time for your team to learn about advancements in sustainable operations

TBD Climate commitments

### Allow team members to share their workload and company specific best practices for sustainable operations

TBD Carbon efficiency

### Create a sustainability community

TBD (Climate commitments)

### Create incentives for improving the environmental sustainability of a workload

TBD Climate commitments

## Next step

Review the design considerations for networking and connectivity.

> [!div class="nextstepaction"]
> [Networking and connectivity](sustainability-networking.md)
