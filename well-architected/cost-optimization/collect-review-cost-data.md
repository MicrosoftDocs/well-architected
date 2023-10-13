---
title: Recommendations for collecting and reviewing cost data
description: Learn how to collect and review cost data for a workload.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for collecting and reviewing cost data

**Applies to: CO 03** 

This guide describes the recommendations for collecting and reviewing cost data for your workload. Gather cost data to paint a holistic picture of your workload and ensure spending is optimized. Data collection includes all indicators of cost optimization, like billing data, resource utilization, and usage patterns.

Collected data allows you to understand the cost of architecture decisions and business drivers like costs per user or unit. This data gives you a clearer understanding of where money goes and how to optimize spending. Failure to collect and review cost data can lead to budget overruns, no baseline for spending, and a lack of understanding of the financial health of your cloud workloads.

**Definitions**

|Term  |Definition  |
|---------|---------|
|Resource utilization     | The amount of resource capacity a workload uses.        |
|Showback     |  An accounting model in which you show departments the cost of their workload usage, and you don't receive payment from them.       |
|Chargeback     |  An accounting model in which you charge departments for their workload usage and receive payments from them.       |
|Billing boundary     |   The scope of what a bill includes.      |

## Key design strategies

Data collection is essential for identifying cost-saving opportunities, accounting (*showback* and *chargeback*), and for efficient resource usage. You must prioritize the collection and review of cost data from all relevant sources. You should centralize the collected data for streamlined analysis and regular review, assign resource owners, and automate alerts where possible.

### Collect cost data

Effective cost management of cloud workloads requires a comprehensive grasp of associated expenses, from computing to network usage. Data collected provides a granular view of where and how resources are being consumed. It allows you to identify inefficiencies, make informed decisions about resource allocation, and ultimately optimize costs to ensure you're getting the best value for your investment.

**Enable data collection**. Data collection should include all sources of workload cost, such as compute, storage, network usage, and any other services or features the workload uses. The data should include invoiced and metered data. Invoiced data is *real*, which means it reflects actual expenses that have been billed. Metered data is a predictive form of data based on the billing plans for services. While still valuable, daily slices of metered data are considered *good estimates* rather than precise figures. Recognizing the distinctions between invoiced and metered data in these components can provide more accuracy in financial planning and analysis.

**Use all available collection methods**. To collect cost data, use all available tools and methods at your disposal like service provider's cost monitoring and utilities to monitor workload expenses. While these tools typically offer detailed insights into cost breakdowns, usage trends, and optimization suggestions, they might not capture everything. Understand their default capabilities, like data dictionaries and taxonomies.

Design custom views if they're required for your specific workload. Beyond native tools, if your service provider offers APIs, tap into them to programmatically retrieve cost data. APIs facilitate automated cost reporting and seamlessly integrate with your existing management systems. Remember, the goal is to gather cost details from every possible source. Whether that means pulling data via an API, manually entering costs, or syncing with your financial systems, it's vital to ensure a centralized and comprehensive cost overview.

**Centralize cost data**. Centralized cost data allows for easier management and analysis of that data. It ensures you have a unified view, through a common data schema, of all workload costs and enables better cost optimization strategies. You need to combine usage data, and the data should flow into a central analytical sink. You can use a cost management tool provided by your cloud provider or integrate the data with third-party cost management solutions. The goal is to have a low-cost solution that's easily accessible by authorized stakeholders and provides robust data analysis capabilities.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Retaining cost data for longer periods enables historical analysis and trend identification. However, storing data can be costly. To minimize cost, store older data as aggregated data points without the granularity of newer data. Also, determine the best retention period based on your analysis needs.

### Group data

Grouping data allows you to gain better insights to manage costs effectively. You can break down costs based on different dimensions, such as departments or projects, allowing you to accurately allocate costs to the respective stakeholders. This promotes transparency, accountability, and cost awareness.

 Group cost data into meaningful categories such as resources, services, environments, regions, departments, projects, or teams. For example, understanding the cost breakdown at the resource and service level can help you make informed decisions about resource allocation, scaling, or even decommissioning. Grouping cost data by environment, such as production, disaster recovery, or quality assurance, can help you identify cost discrepancies and optimize resource usage based on the specific needs of each environment. Group data by:

- *Usage and component data*: Collect detailed information about the usage and cost of each component in your workload. You can analyze costs from different angles and gain insights into the cost breakdown by capturing this data.

- *Different dimensions*: Break down your daily expenses by technical dimensions (for example, resource types or service categories), resource organization dimensions (for example, departments or teams), and business model dimensions (for example, projects or cost centers). This breakdown allows you to analyze costs based on the dimensions that matter the most to your specific problem or scenario.

- *Metadata*: Metadata can be used to group data and help generate meaningful cost reports. It enables you to identify areas of high cost and implement accountability measures or cost optimization strategies at the department or project level. Using metadata, you can design a mechanism to group costs in a way that reflects your application's core business model. For example, tagging resources with tenant identifiers instead of shared resources in a multitenant solution. The ability to pivot cost data based on your application's pricing model can deliver key insights.

### Generate cost reports

After collecting cost data, you need to use it to generate cost reports. Cost reports provide visibility into spending and facilitate the analysis of your workload spending patterns. You can identify areas where cost optimization is needed and make informed decisions to optimize your spending. Cost reports enable you to allocate costs to different teams, departments, or projects. This allocation helps in understanding distribution and facilitates accurate chargeback or showback.

**Address common scenarios**. When generating cost reports for workload costs, you want to be able to address common cost concerns. Gathering data in common concern areas helps ensure that the necessary data sets, such as costs, metrics, and usage, are interpreted cooperatively. Common concern areas include:

- *Granular costs*: Cost reports should provide information on the amount allocated per user and the cost per device.

- *Resource utilization*: Cost reports should help assess if current resources are fully utilized and identify potential savings.

- *Alternative solutions*: Cost reports should compare the costs and potential savings of transitioning to a new solution. They should also evaluate the feasibility of switching to a dynamic solution.

- *Return on investment*: Cost reports should help determine what percentage of revenue goes into system operation. If the system doesn't boost revenue, other ROI metrics should be measured.

- *Spending patterns*: Cost reports should analyze spending patterns to identify trends and patterns in costs over time. Spending patterns help in making informed decisions about cost optimization and budget planning.

**Align to accounting standards**. Cost reports should accommodate your internal accounting standards. Common systems are showback and chargeback. Showback is about visibility, and chargeback is about accountability.

- *Showback* refers to providing cost visibility throughout an organization without charging individual teams or departments for their cloud costs. You can use cost reports to generate showback statements that showcase the costs incurred by each team or department. For example, the marketing team utilized $15,000, while the engineering department incurred costs of $25,000 for a combined workload expenditure of $40,000. Showback provides each department with a breakdown of costs, allowing each team to review and optimize their resource consumption. These reports provide transparency and enable stakeholders to understand their usage and associated costs.

- *Chargeback* involves billing internal teams or departments for their respective cloud costs based on their actual usage. Chargeback is dual-faceted. You can charge others and others can charge you based on resource consumption and services rendered. For example, your workload uses centralized security services. For this month, the security team billed you $10,000 for their services. But you charged the sales and marketing departments $7,000 and $8,000, respectively, for using your workload. All chargeback transactions, both credits and debits, are integrated into your centralized cost data sink. Chargeback ensures every expense is accounted for and incorporated into your organization's financial management, offering a holistic view and promoting optimization of interdepartmental costs.

**Provide comprehensive reports**. Cost reports should include the cost of cloud services and vendors. The report should include costs incurred (invoiced), prepaid costs (amortized), trends, forecasts, credits, and cost variance. In both showback and chargeback systems, cost reports should include the following elements:

- *Incurred costs*: Incurred costs refer to the actual costs that have been accrued based on metered usage. These costs are calculated based on the consumption of resources or services within a specific billing period.

- *Prepaid costs*: Prepaid costs are expenses that have been paid in advance and are spread out over a specific period of time. These costs are typically amortized or allocated evenly over the duration of the prepaid period.

- *Trends*: Analyzing cost trends involves examining the historical data to identify patterns and changes in spending over time. This analysis helps you understand how costs have fluctuated and identify any underlying factors.

- *Forecasts*: Cost forecasts predict future spending based on historical data and trends, allowing you to estimate future costs and plan accordingly. Forecasts can be generated using various techniques such as machine learning algorithms.

- *Credits*: Service providers often provide credits (free utilization) on services. Cost reports should include credit balances and usage to properly understand spending needs.

- *Cost variance*: Cost variance in a cost report refers to the difference between the actual costs incurred and the expected or budgeted costs. It helps you identify deviations from the planned costs and understand the reasons behind them.

### Assign resource owners

Each cost item should have a directly responsible individual (DRI) as the *resource owner*. Assigning a resource owner to each cost item ensures clear accountability for the associated costs. It helps identify who is responsible for managing and optimizing the usage and cost of specific resources or services. Resource owners are important for:

- **Cost allocation**: Having a resource owner assigned to each cost item enables accurate cost allocation. Resource owners ensure cost attribution to the appropriate teams, departments, or projects, facilitating financial transparency and budget management.

- **Communication**: Assigning resource owners promotes effective communication and collaboration within a workload team and organization. It facilitates discussions about cost management, encourages sharing of best practices, and enables resource owners to work together to optimize costs collectively.

- **Decision-making**: Resource owners play a crucial role in decision-making related to resource provisioning, scaling, and optimization. They have the necessary insights and ownership to make informed decisions that align with business objectives and cost optimization goals. Resource owners can actively monitor and analyze the costs associated with their resources. They can identify cost-saving opportunities, optimize resource usage, and make decisions to control and reduce costs.

### Review cost data

Regularly review spending against the budget and cost model with stakeholders. Regular reviews help in identifying cost trends, outliers, and areas for optimization. It's important to involve stakeholders such as finance teams, operations teams, and decision-makers in these reviews to drive cost optimization initiatives. Reviews ensure that costs are aligned with expectations and allow for adjustments if necessary. Monitor changes in usage patterns, adjust resource allocations as needed, and implement cost-saving measures based on ongoing analysis of cost data.

#### Analyze cost data

Review the cost data collected from your workload to gain insights into your spending patterns. Reviews can include analyzing resource utilization, identifying cost drivers, and understanding the distribution of costs across different components of your workload. You should also notice increases and decreases in costs, for example, in compute usage and network transfer costs. Look for areas where you can optimize costs without sacrificing performance or functionality. For example, identify underutilized resources, rightsizing instances, or cost-saving features provided by your cloud provider.

#### Review architectural choices

When examining the architectural decisions of your workload, it's essential to focus on cost implications. Utilizing alternative patterns or cloud-native offerings can lead to significant cost savings. Opting for Platform as a Service (PaaS) or Software as a Service (SaaS) over Infrastructure as a Service (IaaS) can be more economical. With PaaS, not only are infrastructure expenses part of the service's pricing, but the platform also simplifies the provisioning and management of these resources under a unified cost. For instance, deploying a lower tier virtual machine as a jump box might introduce additional costs for storage, server management, and public IP configuration. In contrast, PaaS handles these complexities, offering a consolidated cost that often encompasses enhanced security.

### Automate alerts

Implementing automated alerts can trigger budget notifications at key thresholds. These alerts can be set up to notify stakeholders and DRIs when costs exceed predefined limits or when there are significant deviations from expected spending patterns. Budget alerts and forecast alerts are two different types of cost alerts used for automating cost alerts.

**Use budget alerts.** Budget alerts allow you to set a budget amount and define thresholds for actual costs. When the actual costs exceed the specified thresholds, budget alerts are triggered. These alerts help you monitor your spending and notify you when you're approaching or exceeding your budget. Budget alerts are based on the actual costs you've accrued. Workload spend tends to vary. You should, at minimum, set alerts for the target budget at the anticipated costs (100 percent), ideal spend (90 percent), and less than ideal spend (110 percent).
  
**Use forecast alerts.** Forecast alerts provide advanced notification when your spending trends are likely to exceed your budget. These alerts are generated based on forecasted cost predictions. When the forecasted cost exceeds the set threshold, forecast alerts are triggered. Forecast alerts help you anticipate potential cost overruns so you can take proactive measures to control your spending. You should set the forecast alert at 110 percent of the target budget.

**Use anomaly detection.** Anomaly detection helps identify unexpected or abnormal patterns in cost data, allowing you to detect and address cost anomalies promptly. Utilize anomaly detection techniques to identify deviations from trend baselines, such as unexpected spikes or drops in costs, and take appropriate action. You should tune anomaly detection to catch fluctuations that your spending guardrails can't or intermittently don't prevent.

Based on the analysis of cost anomalies, determine the necessary actions to address the situation. Action plans might involve optimizing resource utilization, resizing virtual machines, implementing Azure Policy controls, or adjusting budgets. It's important to align cost control measures with business values and obtain the necessary approvals for budget adjustments.

Implement automated processes to identify and address cost variances in real-time. Options include automatically scaling resources, automating shutdowns, or establishing workflows for investigation and mitigation of cost anomalies. Establish key performance indicators (KPIs) to measure the accuracy of cost forecasts, cost versus budget, the number of unexpected anomalies, and the time to react to forecast alerts. Regularly review forecasts, track variance, and ensure alignment with budget expectations.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Automating the collection and review of cost data can save time and effort. However, relying solely on automation might overlook certain cost optimization opportunities that require manual review and analysis. Finding the right balance between automation and manual review is crucial.

## Azure facilitation

**Data collection**: Azure provides services like Microsoft Cost Management and Azure Advisor that help track and analyze your Azure spending and usage. These services capture the necessary data to calculate costs accurately. Use Azure tags to group costs to align with different business units, engineering environments, and cost departments. Tags provide the visibility needed for businesses to manage and allocate costs across different groups.

**Cost reports**: Cost Management offers customizable reports that provide insights into your incurred costs, prepaid costs, trends, and forecasts. These reports can be tailored to your specific requirements and provide a comprehensive view of your costs. Microsoft Power BI can help with collecting and reviewing cost data. Power BI provides a comprehensive solution for collecting, reviewing, and analyzing cost data. It enables you to gain insights, track trends, and optimize costs effectively. It integrates with Cost Management and allows you to import cost data into Power BI.

**Review architecture design choices**: Azure provides a wide range of PaaS resources. Here are some examples of when you might consider PaaS options:

|Task|Use|
|---|---|
|Host a web server| [Azure App Service](/azure/app-service/) instead of setting up IIS servers.|
|Indexing and querying heterogenous data|[Azure Cognitive Search](/azure/search/search-what-is-azure-search) instead of ElasticSearch.|
|Host a database server|Azure offers many SQL and no-SQL options such as Azure SQL Database and Azure Cosmos DB.|
|Secure access to virtual machine|[Azure Bastion](/azure/bastion) instead of virtual machines as jump boxes.|
|Network security|[Azure Firewall](/azure/firewall/) instead of virtual network appliances.|

**Automated alerts**: Cost Management enables you to set up automated alerts based on spending thresholds. These alerts can trigger notifications to stakeholders when costs exceed predefined limits or deviate from expected patterns. You should use Advisor cost alerts and create cost anomaly alerts with your subscription. This feature can highlight unexpected spikes or drops in costs, allowing for timely investigation and action.

## Related links

- [Group and filter options in cost analysis and budgets](/azure/cost-management-billing/costs/group-filter)
- [Monitor usage and spending with cost alerts in Cost Management](/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending)
- [Azure billing and cost management budget scenario](/azure/cost-management-billing/manage/cost-management-budget-scenario)
- [Group and allocate costs using tag inheritance](/azure/cost-management-billing/costs/enable-tag-inheritance).
- [Allocate Azure costs](/azure/cost-management-billing/costs/allocate-costs)

## Next steps

We recommend that you review the Cost Optimization checklist to explore other concepts.

> [!div class="nextstepaction"]
> [Cost Optimization checklist](checklist.md)
