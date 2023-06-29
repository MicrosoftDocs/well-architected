---
title: Set budgets and alerts
description: Learn about the Azure Cost Management alert feature. Use this feature to generate alerts when consumption reaches a threshold.
author: PageWriter-MSFT
ms.author: martinek
ms.date: 04/05/2023
ms.topic: conceptual
ms.custom:
  - article
  - internal-intro
---

# Set budgets and alerts

Azure Cost Management has an alert feature. Alerts are generated when consumption reaches a threshold.

Consider the metrics for each resource in the workload. For each metric, build alerts on baseline thresholds. Admins are notified when the workload is using the services at capacity. The admins can then tune the resources to target SKUs based on current load.

You can also set alerts on allowed budgets at the resource group or management groups scopes. By setting alerts on metrics and budgets, you can balance both cloud services performance and budget requirements.

Over time, you can optimize the workload to autoheal itself when it triggers alerts. For more information about using alerts, see [Use cost alerts to monitor usage and spending](/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending).

## Respond to alerts
When you receive an alert, check the current consumption data because alerts don't trigger in real time. There might be a delay between the alert and the current actual cost. Look for significant difference between cost values when the alert occurred and the current cost. Next, conduct a cost review to discuss the cost trend, possible causes, and any required action.

Determine short and long-term actions that justify business value. Can a temporary increase in the alert threshold be a feasible fix? Do you need to increase the longer-term budget? Approval of any budget increase is mandatory.

If unnecessary or expensive resources trigger the alert, implement other Azure Policy controls. You can also add budget automation to trigger resource scaling or shutdowns.

Once you've created your alerts, your can also proactively review your Azure Costs and the forecast for upcoming weeks and months. For more information about Azure Cost Analysis and how to create custom views for your application, see [Start using Cost analysis](/azure/cost-management-billing/costs/quick-acm-cost-analysis).

## Revise budgets

After you identify and analyze your spending patterns, you can set budget limits for applications or business units. You want to [assign access](/azure/cost-management/assign-access-acm-data) to view or manage each budget to the appropriate groups. Setting several alert thresholds for each budget can help track your burn down rate.

Azure Cost Management also has a tool that helps to identify cost anomalies and unexpected charges to your environment. To learn how to identify anomalies and unexpected changes in cost, see [Identify anomalies and unexpected changes in cost](/azure/cost-management-billing/understand/analyze-unexpected-charges)

