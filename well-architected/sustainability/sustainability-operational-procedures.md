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

The Emissions Impact Dashboards produce insights in various forms, and allows for a wide range of reporting capabilities:

- Series of visual representations in the dashboard itself.
- Snapshot export to Excel, Power Point and PDF.
- [Continuous export to Microsoft Sustainability Manager](/industry/sustainability/import-data-connectors#emissions-impact-dashboard-connector) and Dataverse.

_Green Software Foundation alignment: [Measuring sustainability](sustainability-design-principles.md#measuring-sustainability)_

**Recommendation:**

- Use the [Emissions Impact Dashboard](https://appsource.microsoft.com/product/power-bi/coi-sustainability.emissions_impact_dashboard) to record current and future environmental impact.
- Identify and track metrics to quantify the achievement of technical, business, and sustainability outcomes.
- Rely on tooling to help measure the impact, and record any changes made to your workload.
- Learn more about the Sustainability and Dataverse API access in the Microsoft Learn module [Access Microsoft Sustainability Manager data](/learn/modules/access-cloud-sustainability/).

### Define emissions target

The [Software Carbon Intensity (SCI)](sustainability-design-methodology.md#measure-and-track-carbon-impact) is the score you're looking for to measure the carbon impact of your application(s) by adding the scalability and cost metrics to any carbon emissions measurement.

If you aren't using the Emissions Impact Dashboard, there are still ways of building carbon proxies that allow you to measure your application's impact on emissions.

It can be a challenge to build carbon proxies for existing applications. Therefore, we recommend planning for efficiency targets during the design phase of every workload. When adding new workloads to Azure, you should consider planning for costs and emissions that will add to your existing footprint. The main goal should always be not to emit carbon, so ideally, you should immediately find an optimization pattern to make up for the new emissions.

The next step is to define your target emissions, either for a single application or for your entire set of cloud workloads. The target can also include cost constraints, making it even easier to build upon since shrinking costs will give you some budget to optimize emissions. Once you know your target, the cloud efficiency continuous optimization process can start.

_Green Software Foundation alignment: [Measuring sustainability](sustainability-design-principles.md#measuring-sustainability)_

**Recommendations:**

- Calculate your new workload's minimum cost and carbon emissions (where applicable).
- Track progress with Service Level Objectives (SLO), Service Level Agreements (SLA), or other performance metrics.
- Provide optimization patterns to accommodate the new application to your overall cloud efficiency score.

### Identify the metrics and set improvement goals

Once you've defined your target, you'll need to identify a few metrics that you can measure to prove your changes had a positive effect on efficiency.

The metrics can, as an example, be derived from these categories:

- Application performance metrics.
- Cost optimization metrics.
- Carbon emissions metrics (or proxies).

_Green Software Foundation alignment: [Measuring sustainability](sustainability-design-principles.md#measuring-sustainability)_

**Recommendation:**

- Discuss with every application owner since the impact of optimizing can vary and might affect many users.
- Make sure that any plan that impacts performance is agreed upon and communicated clearly to the app users so that they know that a lower performance may be necessary for the greater good of fewer carbon emissions.
- If you've connected the Microsoft Emissions Impact Dashboard (EID) to your Microsoft Sustainability Manager (MSM) instance, you can use the [Goal Tracking feature in MSM](/industry/sustainability/reports-scorecards-goals) to define and track your goals by linking them to live data from EID.

### Cost optimization as a proxy

Sometimes the ease of deploying cloud resources makes us forget what is useful and what is simply a waste of resources, money, and carbon. The message here's that experiments in the cloud can sometimes be costly in terms of overall cloud efficiency, not purely cost, while bringing no innovation.

Use cloud resources wisely, considering any extra workload's carbon footprint.

When defining your SCI, you can use carbon proxies to compensate for the lack of specific standards and measurements. One of the safest and most potent proxies for carbon emissions are your application(s) cost. Reducing unnecessary spending lowers the number of excessive emissions from deployed workloads as you're using fewer cloud resources.

Linking cost performance metrics to carbon efficiency can be a sound strategy because you won't necessarily need to compromise on your defined workload Key Performance Indicators (KPI) by optimizing cost and reducing carbon emissions. However, you might decide that you're prepared to sacrifice a KPI towards your carbon goal, which can also be part of your strategy.

_Green Software Foundation alignment: [Measuring sustainability](sustainability-design-principles.md#measuring-sustainability)_

**Recommendation:**

- Review the concept of [using a proxy solution to measure emissions](sustainability-design-methodology.md#use-a-proxy-solution-to-measure-emissions).
- Leverage the guidance in the Azure Well-Architected Framework [Cost Optimization pillar](/azure/architecture/framework/cost/).

### Defining policies

Azure Policy is a powerful tool that can make some decisions for your cloud efficiency easier to implement. Consider defining one of more policies to keep your Azure virtual data center continuously optimized.

_Green Software Foundation alignment: [Climate commitments](sustainability-design-principles.md#climate-commitments)_

**Recommendation:**

- Incorporate and use the [cost policies](/azure/cloud-adoption-framework/govern/cost-management/policy-statements) available in the Cloud Adoption Framework.
- Leverage [built-in policies](http://azure/governance/policy/samples/built-in-policies) relevant to cost in Azure Policy, as they're technically closely tied to sustainability.
- Customize Azure Policy policies according to green software principles. For example, create a new [Azure Policy initiative](http://azure/governance/policy/overview#initiative-definition) for "Sustainability".
  - Consider this tradeoff: Enforcement of new policies must not impact any unplanned operational performance metric.

## Community and knowledge sharing

Teams needs to be constantly aware of new advancements in sustainability, so they leverage these learnings when implementing workloads.

Building a community around cloud efficiency and green software is a good starting point to foster cloud efficiency awareness and culture across your organization.

### Create a sustainability community

Creating a sustainability community doesn't have to be a tedious task. Start with a small team that will invest some time in learning the sustainability status and the relevant information on green software. This team can also join the [Green Software Foundation](https://greensoftware.foundation/) and be part of the teams that create rules, standards, and more.

The Core cloud Efficiency team will have to be up to date with all the innovative tools and principles that drive your Azure workload's cost and carbon footprint.

_Green Software Foundation alignment: [Climate commitments](sustainability-design-principles.md#climate-commitments)_

**Recommendation:**

- Define policies and targets, and communicate their efforts and goals with the rest of the organization.
- Learn more by reading [how do I start a sustainability community in my organization?](https://devblogs.microsoft.com/sustainable-software/how-do-i-start-a-sustainability-community/)

### Plan for learning

Make time for the core team to learn about advancements in sustainable operations. Meanwhile, ensure that your entire organization starts thinking about green software and how to contribute to the sustainability picture with their daily choices.

_Green Software Foundation alignment: [Climate commitments](sustainability-design-principles.md#climate-commitments)_

**Recommendation:**

Review these popular training and learning resources:

- Use the self-paced learning module to [Learn about The Principles of Sustainable Software Engineering](/learn/modules/sustainable-software-engineering-overview/).
- Use the self-paced learning path to [Get started with Microsoft Cloud for Sustainability](/learn/paths/get-started-sustainability/).
- Find more resources in the [Microsoft Sustainability Learning Center](https://www.microsoft.com/sustainability/learning-center).

### Share best practices across teams

Driving adoption of sustainability efforts requires input and work from across the organization.

_Green Software Foundation alignment: [Climate commitments](sustainability-design-principles.md#climate-commitments)_

**Recommendation:**

- Let team members share their workload and company-specific best practices for sustainable operations.
- Set up a shared repository of best practices and guidance that have been tested in your environment with tangible results.
- Consider frequent knowledge-sharing sessions or internal webinars for getting everyone up to speed.

### Plan for incentives

The quickest way of enforcing policies and creating the right culture is by setting incentives for improving the environmental sustainability of a workload by either putting sustainability as a core KPI or adding it to the overall efficiency of the applications.

Many software partners already include green software in their best practices. Therefore, ensure that your efficiency targets are defined and accepted when discussing the workload.

_Green Software Foundation alignment: [Climate commitments](sustainability-design-principles.md#climate-commitments)_

**Recommendations:**

- Promote carbon-aware applications. Reward application owners if the measured carbon footprint meets the KPI.
- Introduce gamification by creating a friendly culture of sustainability competition—track records to promote green workloads, SCI scoring, and any optimization or improvement on the score.
- Consider introducing loyalty programs, where participants get incentives when they can prove the cloud efficiency of their applications.
- Explore the opportunity to introduce badges like "Carbon Aware" and "Carbon Optimized".

## Next step

Review the design considerations for networking and connectivity.

> [!div class="nextstepaction"]
> [Networking and connectivity](sustainability-networking.md)
