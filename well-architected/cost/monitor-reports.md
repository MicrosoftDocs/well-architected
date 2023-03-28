---
title: Generate cost reports
description: Learn about and explore ways to gather cost data for reporting purposes by using Azure cost tools, consumption APIs, and custom scripts. You can then analyze and visualize the data as well.
author: PageWriter-MSFT
ms.author: martinek
ms.date: 03/28/2023
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - azure
  - azure-cost-management
  - azure-monitor
ms.custom:
  - article
  - internal-intro
---

# Generate cost reports

To monitor the cost of the workload, use Azure cost tools or custom reports. You can scope reports to business units, applications, IT infrastructure shared services, and so on. Make sure that the information is consistently shared with the stakeholders.

## Azure cost tools

Azure provides the following cost tools that help track cloud spending and offer recommendations.

- [Azure Advisor](/azure/advisor/advisor-cost-recommendations)
- [Advisor Score](/azure/advisor/azure-advisor-score)
- [Azure Cost Management](/azure/cost-management-billing/costs/)

### Cost analysis

**Cost analysis** is a tool in Azure Cost Management that lets you view aggregated costs over a period of time. This view helps you understand your spending trends.

You can view costs at different scopes, such as for a resource group or specific resource tags. Cost analysis provides built-in charts and custom views. You can also download the cost data in CSV format to analyze with other tools.

For more information, see: [Quickstart: Explore and analyze costs with cost analysis](/azure/cost-management/quick-acm-cost-analysis).

> [!NOTE]
> There are many ways of purchasing Azure services. Not all services are supported by Azure cost management. For example, you obtain detailed billing information for services purchased through a Cloud Solution Provider (CSP) directly from the CSP. For more information about the supported cost data, see [Understand cost management data](/azure/cost-management/quick-acm-cost-analysis).

### Advisor recommendations

Azure Advisor recommendations for cost highlight any over-provisioned services and ways to lower costs. For example, it can show virtual machines that should be resized to a lower SKU, un-provisioned ExpressRoute circuits, and idle virtual network gateways.

For more information, see [Advisor cost management recommendations](/azure/advisor/advisor-cost-recommendations).

## Consumption APIs

Granular and custom reports help track cost over time. Azure provides a set of consumption APIs to generate such reports. These APIs let you query and create various cost data. Data includes usage data for Azure services and third-party services through Azure Marketplace, balances, budgets, recommendations on reserved instances, and others. You can configure Azure role-based access control (Azure RBAC) policies to let only a certain set of users or applications access the data.

For example, you want to determine the cost of all resources used in your workload for a given period. One way of getting this data is by querying usage meters and the rate of those meters. You also need to know the billing period of the usage. By combining these APIs, you can estimate the consumption cost.

The Azure consumption APIs include:

- [Billing account API](/rest/api/billing/2019-10-01-preview/billingaccounts): To get your billing account to manage your invoices, payments, and track costs.
- [Billing periods API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): To get billing periods that have consumption data.
- [Usage detail API](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): To get the breakdown of consumed quantities and estimated charges.
- [Marketplace store charge API](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): To get usage-based marketplace charges for third-party services.
- [Price sheet API](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): To get the applicable rate for each meter.

You can import the results of the APIs into analysis tools.

> [!NOTE]
> Consumption APIs are supported for enterprise enrollments and web direct subscriptions (with exceptions). Check [Consumption APIs](/rest/api/consumption/) for updates to determine support for other types of Azure subscriptions.

For more information about common cost scenarios, see [Billing automation scenarios](/azure/cost-management-billing/manage/cost-management-automation-scenarios).

## Custom scripts

Use Azure APIs to schedule custom scripts that identify orphaned or empty resources. For example, unattached managed disks, load balancers, application gateways, or Azure SQL Servers with no databases. These resources incur a flat monthly charge while unused. Other resources might be stale, for example, VM diagnostics data in blob or table storage. To determine if you should delete the item, check its last use and modification timestamps.

## Analyze and visualize

Start with the usage details in the invoice. Review that information against relevant business data and events. If there are anomalies, evaluate the significant changes in business or applications that might have contributed those changes.

Power BI Desktop has a connector that lets you connect billing data from Azure Cost Management. You can create custom dashboards and reports, ask questions of your data, and publish and share your work.

> [!NOTE]
> Sharing requires Power BI Premium licenses.

For more information, see [Create visuals and reports with the Azure Cost Management connector in Power BI Desktop](/power-bi/desktop-connect-azure-cost-management).

You can also use the [Cost Management App](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp). The app uses the Azure Cost Management Template app for Power BI. You can import and analyze usage data and incurred cost within Power BI.
