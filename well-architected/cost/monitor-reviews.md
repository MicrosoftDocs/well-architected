---
title: Conduct cost reviews
description: Learn about cost monitoring and cost review to apply proactive and reactive approaches to help create cost controls.
author: PageWriter-MSFT
ms.author: martinek
ms.date: 04/05/2023
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom:
  - article
  - internal-intro
products:
  - azure
  - azure-cost-management
  - azure-monitor
---

# Conduct cost reviews

Cost monitoring tracks and reviews cloud costs to help establish budget controls and prevent misuse. It's important to adopt proactive and reactive review approaches for monitoring cost. Stakeholders should conduct cost reviews regularly and include reactive cost reviews. For example, when a budget limit causes an alert.

## Who should be included in a cost review?

Cost monitoring is a complex process that involves planning, cost estimation, budgeting, and cost control, which can include several different teams.

- **Financial stakeholders**. Need to understand cloud billing to derive business benefits using financial metrics to make effective decisions.
- **Technical team**. Provide insight on cloud cost metering and cloud architecture.
  - **Application owners and System administrator**. Monitor and back-up cloud systems.
  - **Business unit representatives** Need awareness of the decisions.

You can identify owners of systems or applications through resource tags.

## Plan and schedule cost reviews

We recommend scheduling cost reviews as part of the regular business reviews.

- Schedule a review during the billing period. This review creates awareness of the estimated pending billing. You can use [Azure Advisor](/azure/advisor/advisor-cost-recommendations), [Advisor Score](/azure/advisor/azure-advisor-score/), and [Azure Cost Management – cost analysis](/azure/cost-management-billing/costs/) to help with the reports.
- Schedule a review after the billing period. This review shows the actual cost with activity for that month. Use [Balance APIs](/azure/cost-management-billing/manage/consumption-api-overview#balances-api) to generate monthly reports. The APIs can query data that gets information on balances, new purchases, Azure Marketplace service charges, adjustments, and overage charges.
- Schedule a review when you receive a [budget alert](/azure/cost-management/cost-mgt-alerts-monitor-usage-spending) or Azure Advisor recommendation.

Web Direct (pay-as-you-go) and Cloud Solution Provider (CSP) billing occurs monthly, while Enterprise Agreement (EA) billing occurs annually. But ensure you do a cost review regardless of the billing cycle.
