---
title: Architecture strategies for collecting and reviewing cost data
description: Learn how to collect and review cost data for a workload.
author: stephen-sumner
ms.author: ssumner
ms.date: 03/06/2026
ms.topic: concept-article
contributors: 
    - arclares
---

# Architecture strategies for collecting and reviewing cost data

**Applies to this Azure Well-Architected Framework Cost Optimization checklist recommendation:** 

|**CO:03**| Collect and review cost data. Data collection should capture daily costs. In cost reports, include incurred costs (metered), prepaid costs (amortized), trends, and forecasts. Stakeholders should regularly review spending against the budget and cost model. Automate alerts to trigger notifications at key thresholds and detect anomalies to indicate deviations from trend baselines.| 
|---|---| 

Collect cost data to build a holistic picture of your workload spending. Data collection covers all cost indicators — billing data, usage patterns, and spending trends.

Cost data reveals the true cost of architecture decisions and business drivers like cost per user. Without it, you risk budget overruns, missing baselines, and poor visibility into your workload's financial health.

**Definitions**

|Term  |Definition  |
|---------|---------|
|Actual costs     | A cost metric that shows purchases as they appear on your bill. For example, a one-year reservation purchased for $1,200 in January shows the full $1,200 in January and zero reservation cost in subsequent months. If you group actual costs by VM, a VM that received a reservation benefit shows zero cost for that month.      |
|Amortized costs     | A cost metric that splits a reservation or savings plan purchase across the commitment term. For example, the same $1,200 reservation shows a proportional cost each month based on the number of days. Costs are attributed to the VMs that received the benefit. Unused reservation or savings plan costs aren't attributed to any specific resource or subscription.      |
|Billing boundary     |   The scope of what a bill includes.      |
|Chargeback     |  An accounting model in which you charge departments for their workload usage and receive payments from them.       |
|Commitment-based discounts     | Reduced rates earned by committing to a specific amount of usage or spending over a period of time. Examples include Azure reservations, Azure savings plans, and prepaid software subscriptions.      |
|FOCUS     | The FinOps Open Cost and Usage Specification, a provider-agnostic schema for cost and usage data. Azure Cost Management supports FOCUS-formatted exports.      |
|Showback     |  An accounting model in which you show departments the cost of their workload usage, and you don't receive payment from them.       |


Data collection is essential for identifying cost-saving opportunities, accounting (*showback* and *chargeback*), and for efficient resource usage. You must prioritize the collection and review of cost data from all relevant sources. You should centralize the collected data for streamlined analysis and regular review, assign resource owners, and automate alerts where possible.

## Collect cost data

**Enable data collection**. Data collection should include all sources of workload cost, such as compute, storage, network usage, and any other services or features the workload uses. The data should include both **actual costs** and **amortized costs**. Actual costs reflect charges as billed — what appears on your invoice. Amortized costs distribute commitment-based charges (such as reservations and savings plans) evenly across their term, providing a normalized daily cost view. Actual cost data typically has a 24–72 hour ingestion delay. Collecting both views lets you reconcile invoices while also understanding the effective cost of committed resources.

**Use all available collection methods**. To collect cost data, use all available tools and methods at your disposal:

- Use your cloud provider's native cost monitoring tools for detailed cost breakdowns, usage trends, and optimization suggestions.
- Design custom views for workload-specific needs when default capabilities don't capture everything.
- Use APIs to programmatically retrieve cost data and integrate with existing management systems for automated reporting.
- Pull from all sources — APIs, manual entry, and financial system syncs — to build a centralized and comprehensive cost overview.

**Centralize cost data**. Use **Azure Cost Management** as your primary cost data platform. For long-term storage and advanced analytics, configure **Cost Management exports** to write cost data — preferably in [FOCUS](/cloud-computing/finops/focus/what-is-focus) format — to an **Azure Data Lake Storage Gen2** account on a daily or monthly schedule. Enable file compression (Parquet or CSV with gzip) on exports to reduce storage costs. Engage your organization's **central cloud team**, **Cloud Center of Excellence (CCoE)**, or **centralized FinOps team** to manage the centralized data platform and establish governance standards using the [Cloud Adoption Framework](/azure/cloud-adoption-framework/).

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Retaining cost data for longer periods enables historical analysis and trend identification. However, storing data can be costly. Azure Cost Management retains cost data for 13 months. To preserve historical data beyond that window, schedule Cost Management exports to a storage account. Use lifecycle management policies on the storage account to move older data to cool or archive tiers.

## Group and categorize cost data

Grouping data allows you to gain better insights to manage costs effectively. Break down costs based on different dimensions, such as departments or projects, to accurately allocate costs to the respective stakeholders. Grouping data promotes transparency, accountability, and cost awareness.

Group cost data into meaningful categories such as resources, services, environments, regions, departments, projects, or teams. For example, understanding the cost breakdown at the resource and service level helps you make informed decisions about resource allocation, scaling, or decommissioning. When you group cost data by environment, such as production, disaster recovery, or quality assurance, it helps you identify cost discrepancies and optimize resource usage based on the specific needs of each environment. When you group workload data, consider the following recommendations:

- *Collect usage and component data.* Collect detailed information about the usage and cost of each component in your workload. Analyze costs from different angles and gain insights into the cost breakdown by capturing this data.

- *See different dimensions.* Break down your daily expenses by technical dimensions (for example, resource types or service categories), resource organization dimensions (for example, departments or teams), and business model dimensions (for example, projects or cost centers).

- *Apply metadata.* Use metadata to group data and generate meaningful cost reports. Metadata enables you to identify areas of high cost and implement accountability measures or cost optimization strategies at the department or project level. Design a mechanism to group costs in a way that reflects your application's core business model, such as tagging resources with tenant identifiers in a multitenant solution.


Enable **tag inheritance** in Cost Management to automatically apply subscription and resource group tags to child resources that lack them. Tag inheritance helps fill gaps without requiring every resource to be individually tagged. For shared resources that can't be directly tagged to a single owner, use **cost allocation rules** in Cost Management to redistribute costs based on custom logic.

## Generate cost reports

After collecting cost data, use it to generate cost reports. Cost reports provide visibility into spending and facilitate the analysis of your workload spending patterns. Identify areas where cost optimization is needed and make informed decisions to optimize your spending. Cost reports enable you to allocate costs to different teams, departments, or projects. This allocation helps in understanding cost distribution and facilitates accurate chargeback or showback.

**Address common scenarios**. When generating cost reports, address common cost concerns. Gathering data in common concern areas helps ensure that the necessary data sets — costs, metrics, and usage — are interpreted cooperatively. Common concern areas include:

- *Granular costs*: Report the amount allocated per department and the cost per project.

- *Return on investment*: Determine what percentage of revenue goes into system operation. If the system doesn't boost revenue, measure other ROI metrics.

- *Spending patterns*: Analyze spending patterns to identify trends and patterns in costs over time. Use spending patterns to make informed decisions about cost optimization and budget planning.

**Align to accounting standards**. Cost reports should accommodate your internal accounting standards. Common systems are showback and chargeback. Showback is about visibility, and chargeback is about accountability.

- *Showback*: Use cost reports to generate showback statements. For example, the marketing team utilized $15,000, while the engineering department incurred costs of $25,000 for a combined workload expenditure of $40,000. Showback provides each department with a breakdown of costs, allowing each team to review and optimize their resource consumption.

- *Chargeback*: Chargeback is dual-faceted — you charge others and others charge you based on resource consumption and services rendered. For example, your workload uses centralized security services. The security team billed you $10,000 for their services. But you charged the sales and marketing departments $7,000 and $8,000, respectively, for using your workload. All chargeback transactions, both credits and debits, are integrated into your centralized cost data sink.

**Provide comprehensive reports**. Cost reports should include the cost of cloud services and vendors. The report should include **actual costs**, **amortized costs**, trends, forecasts, credits, and cost variance. Refer to the definitions table for actual and commitment-based cost metrics. In both showback and chargeback systems, cost reports should also include:

- *Trends*: Examine historical data to identify patterns and changes in spending over time. This analysis helps you understand how costs fluctuate and identify underlying factors.

- *Forecasts*: Predict future spending based on historical data and trends, allowing you to estimate future costs and plan accordingly. Forecasts can be generated using various techniques such as machine learning algorithms.

- *Cost variance*: Compare the actual costs incurred against the expected or budgeted costs. Cost variance helps you identify deviations from the plan and understand the reasons behind them.

## Assign resource owners

Each cost item should have a directly responsible individual (DRI) as the *resource owner*. Assigning a resource owner to each cost item ensures clear accountability for the associated costs. It helps identify who is responsible for managing and optimizing the usage and cost of specific resources or services. Resource owners are important for:

- *Cost allocation*: Having a resource owner assigned to each cost item enables accurate cost allocation. Resource owners ensure cost attribution to the appropriate teams, departments, or projects, facilitating financial transparency and budget management.

- *Communication*: Assigning resource owners promotes effective communication and collaboration within a workload team and organization. It facilitates discussions about cost management, encourages sharing of best practices, and enables resource owners to work together to optimize costs collectively.

- *Decision-making*: Resource owners play a crucial role in decision-making related to resource provisioning, scaling, and optimization. They have the necessary insights and ownership to make informed decisions that align with business objectives and cost optimization goals.

## Review cost data

Regularly review spending against the budget and cost model with stakeholders. Regular reviews help in identifying cost trends, outliers, and areas for optimization. It's important to involve stakeholders such as finance teams, operations teams, and decision-makers in these reviews to drive cost optimization initiatives. Reviews ensure that costs are aligned with expectations and allow for adjustments if necessary. Monitor changes in usage patterns, adjust resource allocations as needed, and implement cost-saving measures based on ongoing analysis of cost data.

### Review architectural choices

Examine your workload's architectural decisions for cost implications. When reviewing architecture design choices, compare the **total cost of ownership (TCO)** of competing approaches — not just the unit price. Factor in management overhead, licensing, scaling behavior, and commitment-based discount eligibility. Managed services (PaaS/SaaS) often reduce operational labor costs but may have higher unit prices. IaaS gives pricing flexibility but shifts operational costs to your team. Use the Azure Pricing Calculator and cost modeling from [CO:02](cost-model.md) to quantify tradeoffs before deciding.

Engage your organization's **central cloud team**, **Cloud Center of Excellence (CCoE)**, or **centralized FinOps team** when reviewing major architectural decisions. A mature FinOps practice provides benchmarking data, rate optimization insights, and commitment-based discount strategies that inform architecture tradeoffs. The [FinOps Framework management guidance](/cloud-computing/finops/framework/manage/manage-finops) covers establishing and scaling a centralized FinOps function. The [Cloud Adoption Framework](/azure/cloud-adoption-framework/) provides cost governance guidance that complements workload-level reviews with organizational policies, landing zone configurations, and shared service cost allocation.

## Automate cost alerts

Implementing automated alerts can trigger budget notifications at key thresholds. These alerts can be set up to notify stakeholders and DRIs when costs exceed predefined limits or when there are significant deviations from expected spending patterns. Budget alerts and forecast alerts are two different types of cost alerts used for automating cost alerts.

**Use budget alerts.** Budget alerts allow you to set a budget amount and define thresholds for actual costs. When the actual costs exceed the specified thresholds, budget alerts are triggered. These alerts help you monitor your spending and notify you when you're approaching or exceeding your budget. Budget alerts are based on the actual costs you accrued. Workload spend tends to vary. You should, at minimum, set alerts for the target budget at the anticipated costs (100 percent), ideal spend (90 percent), and less than ideal spend (110 percent).
  
**Use forecast alerts.** Forecast alerts provide advanced notification when your spending trends are likely to exceed your budget. These alerts are generated based on forecasted cost predictions. When the forecasted cost exceeds the set threshold, forecast alerts are triggered. Forecast alerts help you anticipate potential cost overruns so you can take proactive measures to control your spending. You should set the forecast alert at 110 percent of the target budget.

**Use anomaly detection.** Anomaly detection helps identify unexpected or abnormal patterns in cost data, allowing you to detect and address cost anomalies promptly. Utilize anomaly detection techniques to identify deviations from trend baselines, such as unexpected spikes or drops in costs, and take appropriate action. You should tune anomaly detection to catch fluctuations that your spending guardrails can't or intermittently don't prevent.

Based on the analysis of cost anomalies, determine the necessary actions to address the situation. Action plans might involve optimizing resource utilization, resizing virtual machines, implementing Azure Policy controls, or adjusting budgets. It's important to align cost control measures with business values and obtain the necessary approvals for budget adjustments.

Implement automated processes to identify and address cost variances in real-time. Options include automatically scaling resources, automating shutdowns, or establishing workflows for investigation and mitigation of cost anomalies. Establish key performance indicators (KPIs) to measure the accuracy of cost forecasts, cost versus budget, the number of unexpected anomalies, and the time to react to forecast alerts. Regularly review forecasts, track variance, and ensure alignment with budget expectations.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Automating the collection and review of cost data can save time and effort. However, relying solely on automation might overlook certain cost optimization opportunities that require manual review and analysis. Finding the right balance between automation and manual review is crucial.

## Azure facilitation

**Collecting and grouping cost data**: Use [Cost Management](/azure/cost-management-billing/costs/overview-cost-management) as the primary tool for tracking and analyzing Azure spending. Configure [Cost Management exports](/azure/cost-management-billing/costs/tutorial-improved-exports) to write cost data to an Azure Data Lake Storage Gen2 account on a daily schedule — use the **FOCUS** (FinOps Open Cost and Usage Specification) template for provider-agnostic, standardized cost data. Apply [Azure tags](/azure/azure-resource-manager/management/tag-resources) to resources and enable [tag inheritance](/azure/cost-management-billing/costs/enable-tag-inheritance) in Cost Management to fill tagging gaps. Use [cost allocation rules](/azure/cost-management-billing/costs/allocate-costs) to redistribute shared resource costs.

**Generating cost reports and dashboards**: Use [Cost analysis](/azure/cost-management-billing/costs/quick-acm-cost-analysis) for interactive exploration of actual and amortized costs with built-in views for resources, services, and tag dimensions. For advanced analytics, create a [Microsoft Fabric workspace for FinOps](/cloud-computing/finops/fabric/create-fabric-workspace-finops) by connecting a Fabric lakehouse to your ADLS Gen2 export storage via shortcuts, then build Power BI dashboards on the ingested cost data. This pipeline — **Cost Management exports → ADLS Gen2 → Fabric Lakehouse → Power BI** — scales to large datasets and enables custom FOCUS-based reporting. 

**Reviewing cost data**: Set [budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) at the subscription or resource group level with threshold-based alerts at 90%, 100%, and 110% of target spend. Use [anomaly detection](/azure/cost-management-billing/understand/analyze-unexpected-charges#identify-cost-anomalies) in Cost analysis to surface unexpected cost spikes. For programmatic access, use the [Cost Details API](/azure/cost-management-billing/automate/usage-details-best-practices) for raw cost data retrieval and automation.

**Reviewing architecture design choices**: Use the [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) to model costs for competing design approaches. Compare total cost of ownership across IaaS, PaaS, and SaaS options — include operational labor, licensing, and commitment-discount eligibility. The [Cloud Adoption Framework cost governance guidance](/azure/cloud-adoption-framework/govern/cost-management/) complements workload-level reviews with organizational policies.

**Automating alerts**: Use [budget alerts](/azure/cost-management-billing/costs/manage-automation#automate-alerts-and-actions-with-budgets) as action groups to trigger notifications, Azure Automation runbooks, or Logic Apps workflows when thresholds are breached. Use Cost analysis [anomaly detection](/azure/cost-management-billing/understand/analyze-unexpected-charges#identify-cost-anomalies) to view and respond to cost anomalies.

**Assessing FinOps maturity**: Use the [FinOps assessment](/assessments/60c02533-b280-4dec-ac5f-3f10cdd238b9/) to evaluate your organization's FinOps capabilities and identify areas for improvement across cost visibility, optimization, and governance.

**Optimize log analytics costs**: For high-traffic services generating significant log volumes, implement [ingestion-time transformation](/azure/azure-monitor/data-collection/data-collection-transformations) to selectively process and store relevant data. Configure filtering rules to retain critical events while reducing routine traffic logs in high-volume environments.

## Related links

- [CO:01 Create a culture of financial responsibility](create-culture-financial-responsibility.md)
- [CO:02 Cost model](cost-model.md)
- [CO:04 Set spending guardrails](set-spending-guardrails.md)
- [CO:05 Get the best rates](get-best-rates.md)
- [Group and filter options in cost analysis and budgets](/azure/cost-management-billing/costs/group-filter)
- [Monitor usage and spending with cost alerts in Cost Management](/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending)
- [Azure billing and cost management budget scenario](/azure/cost-management-billing/manage/cost-management-budget-scenario)
- [Group and allocate costs using tag inheritance](/azure/cost-management-billing/costs/enable-tag-inheritance)
- [Allocate Azure costs](/azure/cost-management-billing/costs/allocate-costs)
- [Create a Fabric workspace for FinOps](/cloud-computing/finops/fabric/create-fabric-workspace-finops)
- [FinOps hubs overview](/cloud-computing/finops/toolkit/hubs/finops-hubs-overview)
- [What is FOCUS?](/cloud-computing/finops/focus/what-is-focus)
- [Cloud Adoption Framework — Cost Management discipline](/azure/cloud-adoption-framework/govern/cost-management/)

## Cost Optimization checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Cost Optimization checklist](checklist.md)

<!-- Azure Update: 498568 - 2025-01-23 -->
