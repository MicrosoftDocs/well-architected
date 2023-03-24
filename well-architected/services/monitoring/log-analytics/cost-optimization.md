---
title: Cost optimization and Log Analytics
description: Focuses on the Log Analytics service used in the Monitoring solution to provide best-practice, design considerations, and configuration recommendations related to Cost optimization.
author: martinekuan
ms.author: martinek
ms.date: 01/31/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - log-analytics
  - azure-monitor
categories:
  - monitoring
  - management-and-governance
---

# Cost optimization and Log Analytics

Log Analytics is a tool in the Azure portal used to edit and run log queries with data in Azure Monitor Logs. You can write a query that returns a set of records. Then, use features of Log Analytics to sort, filter, and analyze them. Alternately, write a more advanced query to do statistical analysis. Visualize the results in a chart to identify a particular trend.

For more information, reference [Overview of Log Analytics in Azure Monitor](/azure/azure-monitor/logs/log-analytics-overview).

To understand how Log Analytics supports cost optimization, reference [Manage usage and costs with Azure Monitor Logs](/azure/azure-monitor/logs/manage-cost-storage). This article includes:

- [Estimating the costs to manage your environment](/azure/azure-monitor/logs/manage-cost-storage#estimating-the-costs-to-manage-your-environment)
- [Viewing Log Analytics usage on your Azure bill](/azure/azure-monitor/logs/manage-cost-storage#viewing-log-analytics-usage-on-your-azure-bill)
- [Understand your usage and optimizing your pricing tier](/azure/azure-monitor/logs/manage-cost-storage#understand-your-usage-and-optimizing-your-pricing-tier)

## Design considerations

Log Analytics workspace includes the following design considerations:

- Consider how long to retain data on Log Analytics:

  Data ingested into Log Analytics workspace can be retained at no additional charge up to the first `31` days. Consider general aspects to configure the [Log Analytics workspace level default retention](/azure/azure-monitor/logs/manage-cost-storage#workspace-level-default-retention). Consider specific needs to configure data [retention by data type](/azure/azure-monitor/logs/manage-cost-storage#retention-by-data-type) that can be as low as four days. Example: Usually, performance data doesn't need to be retained longer, instead, security logs may need to be retained longer.

- Consider exporting data for long-term retention or auditing purposes:

  Data retained for audit purposes may be exported to a cheaper storage type. Refer to [Log Analytics workspace data export in Azure Monitor (preview)](/azure/azure-monitor/logs/logs-data-export?tabs=portal).

## Checklist

**Have you configured Log Analytics with cost optimization in mind?**

> [!div class="checklist"]
> - Consider adoption of the Commitment Tiers pricing model to the Log Analytics workspace.
> - Evaluate usage of daily cap to limit the daily ingestion for your workspace.
> - Understand Log Analytics workspace usage.
> - Evaluate possible data ingestion volume reducing.

## Configuration recommendations

Consider the following recommendation for cost optimization when configuring Log Analytics:

|Recommendation|Description|
|--------------|-----------|
|Consider adoption of the Commitment Tiers pricing model to the Log Analytics workspace.|The usage of Commitment Tiers enables saving as much as `30%` compared to Pay-As-You-Go pricing. Commitment Tiers starts at `100 GB/day` and any usage above the reservation level is billed at the Pay-As-You-Go rate. Refer to [Changing pricing tier](/azure/azure-monitor/logs/manage-cost-storage#changing-pricing-tier) about how to change the pricing tier to Capacity Reservations. Use the [Log Analytics usage and estimated cost](/azure/azure-monitor/logs/manage-cost-storage#understand-your-usage-and-estimate-costs) page to analyze data usage and calculate possible Commitment Tiers. *Note: Azure Defender (Security Center) billing includes `500 MB/node/day` allocation against the [security data types](/azure/azure-monitor/reference/tables/tables-category#security). Take it into consideration when calculating Commitment Tiers.*|
|Evaluate daily cap usage to limit the daily ingestion for your workspace.|Daily cap is used to manage an unexpected increase in data volume. Use daily cap when you want to limit unplanned charges for your workspace. Use care with this configuration as it can cause some data to be unwritten on Log Analytics workspace if the daily cap is reached. This configuration can impact services whose functionality may depend on the availability of up-to-date data in the workspace. Refer to [Set the Daily Cap](/azure/azure-monitor/app/pricing#set-the-daily-cap) about how to set the daily cap in Application Insights. *Note: If you have a workspace-based Application Insights, use daily cap in workspace to limit ingestion and costs instead of using the cap in Application Insights.*|
|Understand Log Analytics workspace usage.|When Log Analytics workspace usage is higher than expected, consider the [troubleshooting](/azure/azure-monitor/logs/manage-cost-storage#troubleshooting-why-usage-is-higher-than-expected) guide and the [Understanding ingested data volume](/azure/azure-monitor/logs/manage-cost-storage#understanding-ingested-data-volume) guide to understand the unexpected behavior.|
|Evaluate possible data ingestion volume reducing.|Refer to [Tips for reducing data volume](/azure/azure-monitor/logs/manage-cost-storage#tips-for-reducing-data-volume) documentation to help configure data ingestion properly.|

## Next step

> [!div class="nextstepaction"]
> [Security and Application Insights](../../monitoring/application-insights/security.md)