---
title: Recommendations for cost modeling
description: Learn how to predict the combined costs of services and their associated expenses for a workload.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
ms.custom:
  - guide
categories:
  - management-and-governance
---
# Recommendations for cost modeling:

**Applies to: CO**

This guide describes the best practices for creating a cost model for a workload. A cost model is an estimate. The estimate focuses on predicting the combined costs of services and their associated expenses for a workload. Building a cost model is vital for forecasting expenses and budget planning. The model also aids in scenario analysis, allowing users to assess the cost implications of potential workload changes. Without a cost model, organizations risk unforeseen expenses, budget overruns, and missed opportunities for optimization.

## Definitions

| Term | Definition |
|------|------------|
| Cost model | The estimated cost of a workload that captures all the dimensions of billing including operations. |
| Cost meter | A tracking mechanism used to measure the usage of resources over time, emitting usage records that are used to calculate the bill for each resource based on its associated meter, such as compute hours, data transfer, input-output operations, and other. |
| Chargeback | An accounting model where you charge departments for their workload usage and receiving payments from them. |
| Showback | An accounting model where you show departments the costs of their workload usage and do not receive payment from them. |

## Key strategies 

A cost model provides a projection of workload cost, derived from existing data. It\'s important to note that this model is not about gaining visibility into expenses or controlling them. Instead, it\'s centered on forecasting the predicted spend considering all available known factors. The objective is not to find the least expensive solution but to determine the best solution for the workload. The best solution is one that aligns spending to workload priorities. A cost model enables you to establish a workload budget, ensure alignment with this budget, and allocate funds for cloud resources.

### Understand costs and savings options

Cloud service providers offer different ways to save money, and you need to know them all. Learn about the various ways to optimize costs and build your cost model around them. Common ways to save include using different billing models, making customer agreements, selecting services, and purchasing licenses.

**Understand workload needs.** Determine the short and long-term cost objectives of your workload. By analyzing workload characteristics, you can determine which available discounts are most suitable for your specific workload. For example, if your workload has predictable usage patterns, you might consider utilizing compute reservations or prepaid models to optimize costs.

Analyze the key components of your workload, including essential resources such as servers, databases, networks, and licenses. Dive deeper into understanding the workload\'s stability, predictability, and its sensitivity to external factors like downtime and degraded performance. Next, assess the specific requirements of your workload. This includes performance, scalability, observability, backup, and disaster recovery needs. These elements play a direct role in determining the efficiency and effectiveness of your workload.

It\'s crucial not to overlook the need for supporting services. Services that support observability, security, and governance not only incur costs but also play a pivotal role in the operation of your workload. Observability solutions, such as monitoring tools and logging mechanisms, offer insights into workload usage and performance. Robust security measures, ranging from encryption and access controls to regular security audits, safeguard your workload and ensure compliance with various regulations. Governance practices and policies are crucial for compliance and efficient resource utilization. Incorporate the expenses related to implementing these practices into your budget.

By ensuring that these often-overlooked factors are incorporated early in the budgeting phase, your cost modeling strategy will be both comprehensive and effective, helping prevent unexpected costs in the future.

**Understand the different billing models.** A billing model refers to the different ways cloud service providers charge for their services, such as pay-as-you-go, prepaid, compute reservations, and spot pricing. Understanding the available billing models helps you identify the most suitable and cost-effective option based on its specific requirements and usage patterns. Each billing model has its own advantages and disadvantages in terms of cost structure and flexibility. For example, pay-as-you-go may provide flexibility but could be more expensive in the long run compared to prepaid or reserved instances.

**Understand** **customer agreements.** Cloud service providers offer customer or enterprise agreements for customers. Agreements with cloud service providers can offer unknown and unused discounts through available programs. Some benefits allow you to bring your existing licenses and save money. These cost optimization strategies can help you maximize the value of your resources and reduce your overall expenses.

**Estimate the cost of licenses***.* Licenses can add considerable costs to a workload. Getting estimates for all licenses is essential to building an accurate cost model. Contacting the software provider and software resellers can help you to find the best deal. Existing customers should use existing licensing benefits and discounts.

**Estimate** **service costs.** Cloud service providers have a range of services to support your workload. The services you choose should help you meet your short- and long-term cost objectives. For example, you might want to move an on-premises workload to the cloud with minimal changes. You need to pick a cloud service that supports your workload goals while also providing the greatest return on investment. Use the cost calculators provided by the cloud platform to estimate the cost of the resources in the workload. These tools help you understand the different cost meters in a resource and the cost implications of different billing models.

Your cost modeling should consider the cost advantages and disadvantages of each service. Service level objectives and platform features have cost implications. For example, downtime might cost your organization a lot of money. Spending more to reach a higher service level objective can generate revenue by avoiding downtime and increasing customer satisfaction. Using built-in features is often more cost efficient than building custom features you need to develop and maintain.

**Estimate resiliency costs.** Estimating the costs of resiliency involves considering multiple factors such as infrastructure, maintenance, data replication and storage, disaster recovery, and performance impact . It is important to consider the specific requirements and goals of your application or system, including the level of resiliency needed, desired service level objectives (SLOs), and availability goals for each dependency on the critical path. The costs can vary based on the chosen cloud services and technologies.

You may also need to consider costs associated with data replication, storage, and bandwidth usage, as well as implementing disaster recovery measures . Maintenance costs, including ongoing operational expenses for monitoring, testing, and regular maintenance of the resilient infrastructure, should also be factored in.

**Estimate operational costs.** To estimate the cost of workload maintenance, you need to consider ongoing operational expenses associated with monitoring, testing, and regular maintenance of the infrastructure. These costs can include:

-   Monitoring: Monitoring the performance and health of the infrastructure using monitoring tools and services helps track system metrics, detect issues, and ensure availability ^1^ .

-   Testing: Regular testing activities, such as load testing, security testing, and performance testing, are essential for maintaining the integrity of your workload. This includes the resources and tools required for testing the system\'s resilience, scalability, and security .

-   Regular Maintenance: Regular maintenance tasks such as applying software patches, updates, and security fixes are necessary to keep the system up to date. Additionally, routine tasks like data backup, system optimization, and configuration management contribute to the ongoing maintenance costs.

### Develop the cost model

The cost model is an estimate of all costs associated with the workload. These costs include infrastructure costs, software licenses, personnel costs, maintenance and support costs, and any other cost components specific to your operations.

**Align estimates to cost drivers.** For each cost category (services, license, etc.), determine the factors that drive the costs. Cost drivers are factors such as usage volume, number of users, storage capacity, or any other metric that directly affects the cost in that category. Assign quantitative values to the identified cost drivers. This can involve estimating usage volumes, determining the number of users or resources, or any other method that allows you to quantify the impact of each cost driver. Based on the cost categories and drivers, establish mathematical models or formulas that relate the cost drivers to the associated costs. This can involve simple linear relationships or more complex calculations depending on the nature of the cost category.

**Associate costs to business metrics.** The cost model should associate costs with the relevant business metrics, such as the cost of each user or the cost of each unit sold. By linking costs directly to business metrics, organizations can gain visibility into the financial impact of different activities. For example, if you expect the number of users to grow, then you can estimate how much it will cost to support those users. This visibility is what many business would like the cost model to provide. It requires making the cost model adaptable and adds complexity to cost modeling. Ultimately, an adaptable cost model support a scenario analysis that allows you to estimate the cost of workload or business changes.

Determining the cost of a user a couple of approaches. A simple method is to get a rough estimate is to divide the total cost of the workload by the number of users. This provides an estimate of how much it costs to support each individual user within the workload. To determine the cost of a user more accurately, you need to consider the specific resources and services utilized by each user. For example, if the workload includes cloud services such as virtual machines, storage, networking, software licenses, data transfer, or any user-specific customizations or configurations.

**Publish** **the cost model.** Publish the cost model by documenting the cost categories, drivers, and the mathematical relationships used to calculate the costs. This documentation should be comprehensive and easily understood by relevant stakeholders. Ensure the cost model is accessible to all relevant stakeholders. Publish the cost model in a format or on a platform that allows for seamless data exchange and enables efficient collaboration between stakeholders.

### Negotiate a budget

A cost model provides a foundation for negotiating a budget for the workload. The cost model is an estimate. A budget is a reality. Sometimes it takes negotiating to align the two. It's important everyone understands how the workload supports business objectives. Having the cost model presented as alignment to business objectives helps clarify the value of the workload.

**Share the cost model.** When you share the cost model, make sure the estimates are clear. Stakeholders should be able to see the cost distribution, cost variables, and optimization efforts.

**Modify cost model to fit budget.** Stakeholders might not agree to the proposed budget and offer a budget that's less than the cost model. It's important that stakeholders know how the budget affects the workload. You should create a second cost model that fits the budget with a buffer and explain any loss of functionality with the reduced budget.

The resulting budget should be realistic, but always remember to include a buffer for predicted usage changes over the budget period, which the cost model helps predict. A budget should also include a small and reasonable buffer for unplanned overages resulting from a mistake or from an unplanned business change.

**Set budget caps and quotas:** Define budget caps and quotas to limit spending and control costs. This ensures that you do not exceed the allocated budget for your workload. By setting budget caps and quotas, you can monitor and manage your spending effectively.

**Set budget alerts:** Implement alerts for cost management. Set up alerts to notify you when the spending reaches certain thresholds. This allows you to take immediate action and make necessary adjustments to stay within the budget. Monitoring usage and setting alerts can help identify trends, peak usage times, and opportunities for cost optimization.

### Budget reporting and allocation 

Budget reporting and allocation involves tracking and managing financial resources within an organization. This process helps in understanding how funds are being utilized, making informed decisions, and optimizing resource allocation. It's important that your budget reporting and allocation system account for cost drivers outside of the workload.\
\
When it comes to deferring workload costs to consumers and deferring costs to the workload provider, the approach may vary depending on the specific context and agreements between the parties involved. In some cases, the costs may be directly passed on to the consumers of the workload, while in others, the workload provider may bear the costs. The decision on how to allocate the costs and who should bear them can depend on factors such as the nature of the workload, the agreement between the parties, and the financial arrangements in place.\
\
Showback is a common practice in budget reporting where the costs of services or resources are shown to the relevant teams or individuals. It helps create awareness among teams regarding their resource consumption. Each team should be able to perform showback to understand the costs associated with their activities.\
\
Chargeback, on the other hand, goes a step further by not only showing the costs but also billing the costs back to the teams or departments based on their actual resource usage. This approach can help promote cost responsibility and encourage efficient resource utilization.

### Use and maintain the cost model

**Use the cost model.** A cost model isn\'t just an analytical tool. It\'s a decision-making aid. You should use the cost model for budgeting purposes, scenario analysis, and resource optimization.

-   *Budgeting:* Use the cost model for budgeting purposes. This includes budget forecasting, where you project future expenses to allocate funds effectively and avoid financial pitfalls. Additionally, regularly compare actual expenses against the budget and make adjustments if there are deviations.

-   *Scenario analysis:* The cost model is a powerful tool for scenario analysis, particularly for stakeholders like product owners. It helps in understanding the financial implications of business model changes, such as modifications to pricing, product offerings, or revenue streams. It also enables you to anticipate how changes in customer acquisition, retention, or churn rates might impact costs, allowing you to forecast increased expenses and plan for scaling.

-   *Resource optimization:* For the technical team, the cost model is particularly useful in optimizing cloud resources and understanding billing implications. This includes identifying areas where cloud resources are underutilized and making adjustments for significant cost savings. Additionally, the cost model can forecast the financial implications of scaling up resources in response to increased user traffic or processing needs. It also helps compare the costs associated with different billing models offered by cloud providers, allowing you to choose the most economical option.

**Maintain the cost model.** It's important to regularly update the model to reflect the latest data, business conditions, and any changes in the external environment. It is also crucial to engage stakeholders, including product owners and the technical team, in discussions around the cost model to ensure its relevance and alignment with different teams\' needs. Running simulations and reviewing the findings can inform informed decision-making. Lastly, educating all relevant team members on how to use the cost model fosters a culture of data-driven decision-making.

-   *Track resource usage:* Monitor the usage of resources in your workload. This is critical for adjusting cost models and identifying opportunities for cost optimization. Conduct utilization audits to identify underutilized resources and adjust cost estimates accordingly.

-   *Generate and review forecasts***:** Forecasts should take the usage data and project the cost of the workload. Forecasts should be updated regularly and viewed often. You should investigate any forecasts that deviate significantly from the current cost model. When you find the issue, you should update the cost model accordingly. The definition of a significant deviation from the cost model is different for each workload. The deviation could be due to changes in workload usage patterns, resource requirements, pricing changes, or other factors that affect the cost estimation. These could cause you to exceed your budget and might require changes to the budget or workload design.

-   *Update the cost* *model:* Periodic review of the cost model helps ensure the workload receives the budget it needs. The review should use the metrics from the workload in production to inform budget reviews. Another driver for reviewing the cost model is the potential impact of services or technology changes. As services and technologies evolve, it may be necessary to make changes to the workload design to optimize costs or take advantage of new features. Regular review ensures that the cost model remains aligned with the changing landscape. You should also review the cost model before and after workload design changes.

You should update the cost model whenever you change cloud services. You should use the calculator for your cloud platform to estimate the cost of the cloud resources your workload needs to run. For new workloads, some of the cost variables, such as data transfer and storage amount, can be difficult to estimate. Using a business target can help generate estimates. For example, user base estimation can be helpful. Divide the daily revenue target (\$100,000) by an average purchase per visitor (\$100) to get an estimated number of daily visitors you need to support (1,000).

## Azure facilitation

**Azure pricing overview**: The Azure [pricing overview](https://azure.microsoft.com/en-us/pricing/#product-pricing) provides pricing information for all Azure services. It gives you a comprehensive view of the costs associated with different Azure services.

**Azure pricing calculator**: The Azure [pricing calculator](https://azure.microsoft.com/en-us/pricing/calculator/) is a helpful tool that allows you to estimate the hourly or monthly costs of your workload. By inputting the services you plan to use, you can get an estimate of the associated costs. This helps in planning and budgeting for your Azure usage.

**Total Cost of Ownership (TCO) calculator**: The [Total Cost of Ownership (TCO) Calculator](https://azure.microsoft.com/en-us/pricing/tco/calculator/) is designed to help you estimate the cost savings of migrating your workload to Azure. It takes into account various factors such as infrastructure costs, management costs, and labor costs to provide an estimate of the total cost of ownership. This helps you make informed decisions about the financial aspects of your Azure migration.

**Tag inheritance**: Azure allows you to use tag inheritance to [group and allocate costs.](https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/enable-tag-inheritance) Tags are metadata that you can assign to Azure resources, and with tag inheritance, you can track and manage costs for different teams or projects within your organization. This helps with cost allocation and analysis.

**Cost forecasts**: Azure automatically provides [cost forecasts](https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/quick-acm-cost-analysis#understand-your-forecast)., which enable you to plan and budget for your Azure usage. These forecasts help you understand the projected costs based on your current usage patterns, allowing you to make proactive decisions to optimize costs.

**Budget creation and management**: Azure allows you to [create and manage budgets](https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/tutorial-acm-create-budgets). Budgets help you proactively inform others about their spending, manage costs, and monitor spending over time. You can set budget thresholds, receive alerts, and track expenses to ensure cost control and optimization.

**Azure Hybrid Benefit.** Azure Hybrid Benefit is a program offered by cloud service providers like Azure that allows customers to bring their own licenses for certain software products to the cloud. By bringing your own license, you can take advantage of discounted pricing for using that software on the cloud platform . In the context of agreements, the Azure Hybrid Benefit can be part of the customer agreement between the cloud service provider and the customer. This agreement outlines the terms and conditions for utilizing the benefit and the eligible software products that can be covered under it . The benefit of bringing your own license is that it allows you to use your existing investments in software licenses and extend them to the cloud, resulting in potential cost savings. Instead of paying the full price for using the software on the cloud, you can benefit from the discounted pricing offered through the Azure Hybrid Benefit.

## Related links

-   [Measuring unit costs](https://learn.microsoft.com/en-us/azure/cost-management-billing/finops/capabilities-unit-costs)

-   [View and download Azure usage and charges](https://learn.microsoft.com/en-us/azure/cost-management-billing/understand/download-azure-daily-usage)