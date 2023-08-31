---
title: Recommendations for collecting and reviewing cost data
description: Learn how to collect and review cost data for a workload.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for collecting and reviewing cost data

**Applies to: CO** \## 

This guide provides recommendations for collecting and reviewing cost data for a workload. Optimizing workload spend requires gathering data to paint a holistic picture of the workload\'s cost optimization. Data collection should include all indicators of cost optimization, such as billing data, resource utilization, and usage patterns. The data should allow you to understand the cost of architecture decisions and the cost of business drivers, such as costs per user or unit. This data gives you a clearer understanding of where money goes and how to optimize spend. Failure to collect and review cost data can lead to budget overruns, no baseline to optimize spend against, and a lack of understanding of the financial health of your cloud workloads.

## Definitions

|Term  |Definition  |
|---------|---------|
|Resource utilization     | How much resource capacity a workload uses.        |
|Showback     |  Reporting the cost of a workload to those who used it during the billing period.       |
|Chargeback     |  Billing for the cost of a workload to those who used it during the billing period.       |
|Billing boundary     |   The scope of what a bill includes.      |

## Key strategies

You must prioritize the collection and review of cost data from all relevant sources. You should centralize the collected cost data for streamlined analysis and conducting regular reviews. Data collection is essential for identifying cost-saving opportunities, accounting (showback and chargeback), and fostering responsibility for efficient resource usage.

### Collect cost data

Effective cost management of cloud workloads requires a comprehensive grasp of associated expenses, from compute to network usage. This data provides a granular view of where and how resources are being consumed. It allows you to identify inefficiencies, make informed decisions about resource allocation, and ultimately optimize costs to ensure you're getting the best value for your investment.

**Enable data collection.** This collection should include data on all sources of workload cost, such as compute, storage, network usage, and any additional services or features the workload uses. The data should include invoiced and metered data. Invoiced data is \"real.\" It reflects actual expenses that have been billed. Daily slices of this data are factual representations of the costs incurred. Metered data is a predictive or interpreted form of data based on the billing plans for services. While still valuable, daily slices are considered \"good estimates\" rather than precise figures. Recognizing the distinctions between invoiced and metered data in these components can provide more accuracy in financial planning and analysis.

**Use all available collection methods.** To collect cost data, leverage all available tools and methods. Use service provider\'s cost monitoring and utilities to monitor workload expenses. While these tools typically offer detailed insights into cost breakdowns, usage trends, and optimization suggestions, they might not capture everything out of the box. Understand their default capabilities, like data dictionaries and taxonomies.

Design custom views if they\'re required for your specific workload. Beyond these native tools, if your service provider offers APIs, tap into them to programmatically retrieve cost data. This not only facilitates automated cost reporting but also seamlessly integrates with your existing management systems. Remember, our goal is to gather cost details from every possible source. Whether that means pulling data via an API, manually entering costs, or syncing with your financial systems, it\'s vital to ensure a centralized and comprehensive cost overview.

**Centralize cost data.** Centralizing cost data allows for easier management and analysis of cost data. It ensures you have a unified view, through a common data schema, of all workload costs and enables better cost optimization strategies. You need to aggregate usage data, and the data should flow into a central analytical sink.

You can use a cost management tool provided by your cloud provider or integrate the data with third-party cost management solutions. The goal is to have a low-cost solution that is easily accessible by authorized stakeholders and provides robust data analysis capabilities.

*Tradeoff*. Retaining cost data for longer periods enables historical analysis and trend identification. However, storing cost data storage costs. To minimize cost, store older cost data as aggregated data points without the granularity of newer data. Also, determine the best retention period based on your analysis needs.

**Be able to group data.** Grouping cost data by dimensions such as resource type, environment, and other workload-relevant factors is a common practice in cost optimization. It involves categorizing cost data based on various attributes to gain better insights and manage costs effectively. Cost reports play a crucial role in cost allocation. By breaking down costs based on different dimensions, such as departments or projects, you can accurately allocate costs to the respective stakeholders. This promotes transparency, accountability, and cost awareness within the organization.

You should be able to group cost data by resources, service, environments, region, departments, projects, teams and any other meaningful category. For example, understanding the cost breakdown at the resource and service level can help you make informed decisions about resource allocation, scaling, or even decommissioning. Grouping cost data by environment, such as production, disaster recovery, quality assurance, helps you identify cost discrepancies and optimize resource usage based on the specific needs of each environment.

-   Usage and component data: Collect detailed information about the usage and cost of each component in your workload. This includes tracking the usage of resources, services, and applications. By capturing this data, you can analyze costs from different angles and gain insights into the cost breakdown.

-   Different dimensions: Aim to have a daily spend breakdown by technical dimensions (e.g., resource types, service categories), resource organization dimensions (e.g., departments, teams), and business model dimensions (e.g., projects, cost centers). This breakdown allows you to analyze costs based on the dimensions that matter the most to your specific problem or scenario.

-   Metadata: Metadata, such as tags, can be used to group the cost data and help generate meaningful cost reports. This helps you analyze costs from a business perspective and allocate costs to the respective stakeholders. It enables you to identify areas of high cost and implement accountability measures or cost optimization strategies at the department or project level.

If relevant, design a mechanism to group costs in a way that reflects your application's core business model. For example, tagging resources with tenant identifiers vs shared resources in a multi-tenant solution. Being able to pivot cost data to see spend based on your application's pricing model can deliver key insights.

### Generate cost reports

After collecting cost data, you need to use it and generate cost reports. Cost reports provide visibility into the spending and analyze the spending patterns and trends of your workload. You can identify areas where cost optimization is needed and make informed decisions to optimize your spending. Cost reports enable you to allocate costs to different teams, departments, or projects. This helps in understanding the cost distribution and facilitates accurate chargeback or showback.

**Address common scenarios.** When generating cost reports for workload costs, you want to be able to address common cost concerns. This helps ensure that the necessary data sets, such as costs, metrics, and usage, are gathered synergistically.

-   Granular costs: Cost reports should provide information on the amount allocated per user and the cost per device.

-   Resources utilization: Cost reports should help assess if current resources are fully utilized and identify potential savings from scaling down.

-   Alternative solutions: Cost reports should compare the costs of transitioning to a new solution with the potential savings. They should also evaluate the feasibility of switching to a dynamic solution.

-   Return on investment: Cost reports should help determine what percentage of revenue goes into system operation. If the system doesn\'t boost revenue, other ROI metrics should be measured.

-   Identify spending patterns: Cost reports should analyze spending patterns to identify trends and patterns in costs over time. This can help in making informed decisions about cost optimization and budget planning .

**Align to accounting standards.** The cost reports should accommodate your internal accounting standards. Common systems are showback and chargeback. Showback is about visibility, and chargeback is about accountability.

*Showback* refers to providing cost visibility throughout an organization without charging individual teams or departments for their cloud costs. You can use cost reports to generate showback statements that showcase the costs incurred by each team or department. For example, the Marketing team utilized workload resources amounting to \$15,000, while the Engineering department incurred costs of \$25,000. This represents a combined workload expenditure of \$40,000. Showback provides each department with a breakdown of costs, allowing each team to review and optimize their resource consumption. These reports provide transparency and enable stakeholders to understand their usage and associated costs.

*Chargeback* involves billing internal teams or departments for their respective cloud costs based on their actual usage. Chargeback is dual-faceted. You can charge others and others can charge you based on resource consumption and services rendered.

For example, your workload uses centralized security services. For this month, the security team billed you \$10,000 for their services. But you charged the Sales and Marketing departments \$7,000 and \$8,000, for using your workload.

All these chargeback transactions, both credits and debits, are integrated into your centralized cost data sink. This system ensures every expense is tracked, accounted for, and incorporated into your organization\'s broader financial management, offering a holistic view, and facilitating optimization of inter-departmental costs.

**Provide comprehensive reports.** Cost reports should include the costs of cloud services and vendors. The reports should include costs incurred (invoiced), pre-paid costs (amortized), trends, forecasts, credits, and cost variance. In both showback and chargeback systems, cost reports should include the following elements:

-   *Incurred costs (invoiced)*: Incurred costs refer to the actual costs that have been accrued based on metered usage. These costs are calculated based on the consumption of resources or services within a specific billing period.

-   *Pre-paid costs (amortized)*: Pre-paid costs are expenses that have been paid in advance and are spread out over a specific period of time. These costs are typically amortized or allocated evenly over the duration of the pre-paid period.

-   *Trends*: Analyzing cost trends involves examining the historical data to identify patterns and changes in spending over time. This analysis helps you understand how costs have fluctuated and identify any underlying factors or drivers.

-   *Forecasts*: Cost forecasts involve predicting future spending based on historical data and trends. This allows you to estimate future costs and plan accordingly. Forecasts can be generated using various techniques such as machine learning algorithms.

-   *Credits*: Service providers often provide credits (free utilization) on services. Cost reports should include credit balances and usage to properly understand spending needs.

-   *Cost variance*: Cost variance in a cost report refers to the difference between the actual costs incurred and the expected or budgeted costs. It helps to identify deviations from the planned costs and understand the reasons behind them.

### Assign resource owners

Each cost item should have a named directly responsible individual (DRI) as the "resource owner." Assigning a resource owner to each cost item ensures clear accountability for the associated costs. It helps identify who is responsible for managing and optimizing the usage and cost of specific resources or services.

-   *Cost allocation.* Having a resource owner assigned to each cost item enables accurate cost allocation. It allows cost attribution to the appropriate teams, departments, or projects, facilitating financial transparency and budget management.

-   *Communication.* Assigning resource owners promotes effective communication and collaboration within a workload team and their organization. It facilitates discussions about cost management, encourages sharing of best practices, and enables resource owners to work together to optimize costs collectively.

-   *Decision-making:* Resource owners play a crucial role in decision-making related to resource provisioning, scaling, and optimization. They have the necessary insights and ownership to make informed decisions that align with business objectives and cost optimization goals. Resource owners can actively monitor and analyze the costs associated with their resources. They can identify cost-saving opportunities, optimize resource usage, and make informed decisions to control and reduce costs.

### Review cost data

Regularly review spending against the budget and cost model with stakeholders. This helps in identifying cost trends, outliers, and areas for optimization. It is important to involve stakeholders such as finance teams, operations teams, and decision-makers in these reviews to drive cost optimization initiatives. Reviews ensure that costs are aligned with expectations and allow for adjustments if necessary. Monitor changes in usage patterns, adjust resource allocations as needed, and implement cost-saving measures based on ongoing analysis of cost data.

**Analyze cost data.** Review the cost data collected from your workload to gain insights into your spending patterns. Reviews can include analyzing resource utilization, identifying cost drivers, and understanding the distribution of costs across different components of your workload. You should also notice increases and decreases in costs, for example, in compute usage and network transfer costs.

Look for areas where you can optimize costs without sacrificing performance or functionality. This can involve identifying underutilized resources, rightsizing instances, or leveraging cost-saving features provided by your cloud provider.

**Automate alerts.** Implementing automated alerts can trigger spend notifications at key thresholds. These alerts can be set up to notify stakeholders and DRIs when costs exceed predefined limits or when there are significant deviations from expected spending patterns. You should set alerts against budgets and forecasts. Budget alerts and forecast alerts are two different types of cost alerts used for automating cost alerts.

-   *Budget alerts*: Budget alerts allow you to set a budget amount and define thresholds for actual costs. When the actual costs exceed the specified thresholds, budget alerts are triggered. These alerts help you monitor your spending and notify you when you are approaching or exceeding your budget. Budget alerts are based on the actual costs you\'ve accrued. You should, at a minimum, set alerts at 90%, 100%, and 110% of the target spend. Workload spend tends to vary. You should have a target budgets at the anticipated costs, ideal spend (90%), and less ideal spend (110%). For example, you anticipate a workload costing \$10,000 per month but should set for \$9,000 and \$11,000.

-   *Forecast alerts*: Forecast alerts provide advanced notification when your spending trends are likely to exceed your budget. These alerts are generated based on forecasted cost predictions. When the forecasted cost projection exceeds the set threshold, forecast alerts are triggered. Forecast alerts help you anticipate potential cost overruns and take proactive measures to control your spending. You should set the forecast alert at 110% of the target budget.

*Risk.* Automating the collection and review of cost data can save time and effort. However, relying solely on automation may overlook certain cost optimization opportunities that require manual review and analysis. Finding the right balance between automation and manual review is crucial.

**Use** **anomaly detection.** Anomaly detection helps identify unexpected or abnormal patterns in cost data, allowing organizations to detect and address cost anomalies promptly. Utilize anomaly detection techniques to identify deviations from trend baselines. This can help you identify unexpected spikes or drops in costs and take appropriate actions. Anomaly detection catches cost changes that spending guardrails attempt to prevent. You should tune anomaly detection to catch fluctuations that your spending guardrails can't or intermittently don't prevent.

Based on the analysis of cost anomalies, determine the necessary actions to address the situation. This may involve optimizing resource utilization, resizing virtual machines, implementing Azure Policy controls, or adjusting budgets. It\'s important to align cost control measures with business value and obtain the necessary approvals for budget adjustments.

Implement automated processes to identify and address cost variances in real-time. This could include auto-scaling resources, automating shutdowns, or establishing workflows for investigation and mitigation of cost anomalies. Establish key performance indicators (KPIs) to measure the accuracy of cost forecasts, cost vs. budget, the number of unexpected anomalies, and the time to react to forecast alerts. Regularly review forecasts, track variance, and ensure alignment with budget expectations.

**Review architectural choices.** Assess the architectural choices made for your workload and evaluate their impact on cost. Consider whether there are alternative architectural patterns or cloud-native offerings that can provide cost savings, such as using platform-as-a-service (PaaS) or software-as-a-service (SaaS) solutions instead of infrastructure-as-a-service (IaaS).

## Azure facilitation

**Data collection.** Azure provides services like Azure Cost Management and Azure Advisor that help track and analyze your Azure spending and usage. These services capture the necessary data to calculate costs accurately.

Use Azure tags to group costs to align with different business units, engineering environments, and cost departments. Tags provide the visibility needed for businesses to manage and allocate costs across the different groups.

**Cost reports.** Azure Cost Management offers customizable reports that provide insights into your incurred costs, pre-paid costs, trends, and forecasts. These reports can be tailored to your specific requirements and provide a comprehensive view of your costs.

Power BI can help with collecting and reviewing cost data. Power BI provides a comprehensive solution for collecting, reviewing, and analyzing cost data. It enables you to gain insights, track trends, and optimize costs effectively. It integrates with Azure Cost Management and allows you to import cost data into Power BI.

**Automated alerts.** Azure Cost Management enables you to set up automated alerts based on spending thresholds. These alerts can trigger notifications to stakeholders when costs exceed predefined limits or deviate from expected patterns. You should use Azure Advisor cost alerts and create cost anomaly alerts per subscription. Azure Cost Management includes anomaly detection capabilities that help identify deviations from trend baselines. This feature can highlight unexpected spikes or drops in costs, allowing for timely investigation and action.

## Related links

[Group and filter options in Cost analysis and budgets - Microsoft Cost Management](/azure/cost-management-billing/costs/group-filter)

[Monitor usage and spending with cost alerts in Cost Management - Microsoft Cost Management](/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending)

[Azure billing and cost management budget scenario - Microsoft Cost Management](/azure/cost-management-billing/manage/cost-management-budget-scenario)

[Group and allocate costs using tag inheritance - Microsoft Cost Management](/azure/cost-management-billing/costs/enable-tag-inheritance)

[Allocate Azure costs - Microsoft Cost Management](/azure/cost-management-billing/costs/allocate-costs)
