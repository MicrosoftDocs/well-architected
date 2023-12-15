---
title: Recommendations for creating a cost model
description: Learn how to create an estimate that focuses on predicting the combined costs of services and their associated expenses.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
categories:
  - management-and-governance
---
# Recommendations for creating a cost model

**Applies to this Azure Well-Architected Framework Cost Optimization checklist recommendation:**

|[CO:02](checklist.md)|Create and maintain a cost model. A cost model should estimate the initial cost, run rates, and ongoing costs. Negotiate a budget that covers a cost model and has a buffer for unplanned spending.|
|---|---|

This guide describes the best practices for creating a cost model for your workload. A cost model is an estimate that predicts the combined costs of services and their associated expenses. It's foundational for expense forecasting and budget planning. A cost model provides scenario analysis, which allows you to assess the cost implications of potential workload changes. Without a cost model, you risk unforeseen expenses, budget overruns, and missed opportunities for cost optimization.

**Definitions**

| Term | Definition |
|------|------------|
| Chargeback | An accounting model in which you charge departments for their workload usage and receive payments from them. |
| Cost model | The estimated cost of a workload. It captures all the dimensions of billing, including operations. |
| Cost meter | A tracking mechanism that you use to measure the usage of resources over time. It tracks usage, such as compute hours, data transfer, and input-output operations. It emits the records that are used to calculate the bill for each resource based on its associated meter. |
| Showback | An accounting model in which you show departments the cost of their workload usage, and you don't receive payment from them. |

## Key design strategies

A cost model provides a projection of the workload cost based on existing data. The purpose of a cost model isn't to gain visibility into expenses or control them. The goal is to forecast the predicted expenses, considering all available known factors. From that prediction, you determine the best solution for your workload. The best solution aligns spending to workload priorities. A cost model enables you to establish a workload budget, ensure alignment with this budget, and allocate funds for cloud resources.

### Conduct workload assessment

Conducting a workload assessment involves systematically evaluating and analyzing the workload. A workload assessment helps identify workload characteristics that can inform cost optimization strategies, such as choosing the most suitable discount options based on usage patterns. You need to assess the workload characteristics to determine which available discounts are most suitable for your workload. For example, if your workload has predictable usage patterns, you might consider using a commitment-based model (reservations) to optimize costs. When you assess a workload, consider these recommendations:

- *Analyze key components*: Analyze the key components of your workload, including essential resources such as servers, databases, networks, and licenses. This identification allows for precise cost allocation within the model.

- *Understand characteristics*: Understand the workload's stability, predictability, and sensitivity to external factors like downtime and degraded performance. Such insights help anticipate fluctuating costs based on workload behavior.

- *Understand requirements*: Assess the specific requirements of your workload like performance, scalability, observability, backup, and disaster recovery. Recognizing these requirements ensures the model accounts for all potential expenses.

- *Understand supporting services.* Services that support observability, security, and governance incur costs and play a pivotal role in the operation of your workload. Observability solutions, such as monitoring tools and logging mechanisms, offer insights into workload usage and performance. Robust security measures, like encryption or access controls and regular security audits, safeguard your workload and ensure regulation compliance. Governance practices and policies ensure compliance and efficient resource utilization. Incorporate the expenses for these supporting services into your budget.

    If you include these often-forgotten factors in your budgeting early on, your cost modeling will be thorough, effective, and prevent future unexpected expenses.

### Estimate workload costs

Estimating workload costs involves assessing all potential expenditures and savings linked to the workload. It encompasses direct vendor costs, operational maintenance expenses, billing model choices, and potential savings from customer or enterprise agreements. By evaluating these factors, you can create a robust cost model, enabling precise forecasting and budgeting. To estimate workload costs, consider the following strategies:

**Select the best billing model.** A billing model determines how the cloud service provider charges for their services. Billing models include consumption-based (pay-as-you-go), commitment-based plans (reservations), and spot pricing. Identify the most suitable and cost-effective billing model by understanding the specific requirements and usage patterns of each model. Each billing model has advantages and disadvantages of cost structure and flexibility. For example, pay-as-you-go might provide flexibility but might be more expensive over time compared to commitment-base plans instances.

**Use customer agreements.** Cloud service providers offer customer agreements or enterprise agreements for customers. Some agreements offer discounts through available programs or allow you to use your existing licenses to save money. Implement these cost optimization strategies to maximize the value of your resources and reduce your overall expenses.

**Estimate license costs.** Calculate estimates for license costs to create an accurate cost model. To find the best deal, contact the software provider or the software reseller. If you're an existing customer, use existing licensing benefits and discounts.

**Estimate service costs.** Cloud service providers provide many services to support your workload. Choose services that help you meet your short-term and long-term cost objectives. For example, you might want to move an on-premises workload to the cloud with minimal changes to your workload. Choose a cloud service that supports your workload goals and provides the greatest return on your investment. Use the cloud platform's cost calculator to estimate your workload's resource costs. These tools help you understand the different cost meters in a resource and the billing model cost implications.

Consider the cost advantages and disadvantages of each service. Service-level objectives (SLOs) and platform features have cost implications. For example, downtime might cost your organization a considerable amount of money. If you invest more money into reaching higher SLOs, you can generate revenue by avoiding downtime and increasing customer satisfaction. Use built-in features as a cost-efficient alternative to building custom features that you need to develop and maintain.

**Estimate resiliency costs.** To estimate resiliency costs, consider factors such as infrastructure, maintenance, data replication, data storage, disaster recovery, and performance. Consider the specific requirements and goals of your application or system. It could include the required level of resiliency, the desired SLOs, and the availability goals for each dependency on the critical path. The costs vary based on the cloud services and technologies that you choose.

**Estimate operational costs.** To estimate the cost of workload maintenance, consider the ongoing operational expenses for monitoring, testing, and maintenance of the infrastructure. These costs include monitoring the performance and health of the infrastructure. It should include monitoring tools and services to help track system metrics, detect issues, and ensure availability.

You should estimate the cost of regular testing activities such as load testing, security testing, and performance testing. These tests are essential for maintaining the integrity of your workload. Include the resources and tools that are required for testing the system's resilience, scalability, and security. You also need to include Regular maintenance tasks, such as applying software patches, updates, and security fixes, are necessary to keep the system up to date. Routine tasks like data backup, system optimization, and configuration management contribute to ongoing maintenance costs.

### Develop the cost model

The cost model is an estimate of all costs associated with the workload. These costs include infrastructure, software licenses, personnel, maintenance, and support costs.

#### Align estimates to cost drivers

Cost drivers are specific factors or variables that influence the overall cost. It includes any factors that have a direct impact on the cost of resources, services, or operations within the workload. These drivers can include variables such as usage volume, the number of customers served, storage capacity.

Assign quantitative values to the identified cost drivers, such as estimating usage volumes or determining the number of customers or resources. Quantify the effect of each cost driver by using methods such as estimating usage volumes or determining the number of customers or resources. Based on the cost categories and drivers, establish mathematical models or formulas that relate the cost drivers to the associated costs. These models can include simple linear relationships or complex calculations, depending on the cost category.

#### Associate costs with business metrics

Associating costs with business metrics means linking workload expenses to specific business indicators, like cost per customer served or cost per transaction processed. This practice provides a clearer understanding of how the workload consumes resources. It allows you to anticipate costs related to workload fluctuations and ensures efficient resource utilization based on demand. For example, if you expect the number of customers to grow, you can estimate how much it costs to support those customers.

You should emphasize clear visibility in the workload cost models. While it can make the model more intricate, it also allows for adaptability. Such a flexible cost model aids in scenario analysis, helping to predict expenses tied to workload or business shifts. To estimate the cost associated with each customer, divide the total workload cost by the number of customers. For a precise cost per customer, account for specific resources and services they utilize, like cloud services or software licenses.

### Publish the cost model

Document the cost categories, drivers, and mathematical relationships that are used to calculate the costs. Create comprehensive and easily understood documentation for stakeholders. Ensure that the cost model is accessible to all relevant stakeholders. Publish the cost model in a format or on a platform that allows for seamless data exchange and enables efficient collaboration between stakeholders.

### Set a budget

The cost model provides a foundation for negotiating your workload budget. The cost model is an estimate. The budget is a reality. Sometimes you have to negotiate to align the two. It's important that everyone understands how the workload supports business objectives. Present the cost model in alignment with business objectives to help clarify the value of the workload.

- *Share the cost model*: When you share the cost model with stakeholders, make sure the estimates are clear. Stakeholders should be able to see the cost distribution, cost variables, and optimization efforts.
- *Modify the cost model to fit the budget*: Stakeholders might not agree to the proposed budget and they might offer a budget that's less than the cost model. It's important that stakeholders know how the budget affects the workload. Create a second cost model that fits the budget and includes a buffer. Explain any functionality loss with the reduced budget.

  The resulting budget should be realistic, but include a buffer for predicted usage changes over the budget period. The cost model helps predict these changes. A budget should also include a small and reasonable buffer for unplanned overages that result from a mistake or an unplanned business change.

- *Set budget caps and quotas*: Define budget caps and quotas to control costs and limit spending. This practice ensures that you don't exceed the allocated budget for your workload. By setting budget caps and quotas, you can monitor and manage your spending effectively.
- *Set budget alerts*: Implement alerts for cost management. Set up alerts to notify you when spending reaches certain thresholds. This practice allows you to take immediate action and make necessary adjustments to stay within the budget. Monitor usage and set alerts to help identify trends, peak usage times, and opportunities for cost optimization.

### Use the cost model

A cost model isn't just an analytical tool. It's a decision-making aid. Use the cost model for budgeting, scenario analysis, and resource optimization. To maximize the use of the cost model, consider these strategies:

- *Use the cost model for budgeting*: Use the cost model to project future expenses, allocate funds effectively, and avoid financial pitfalls. Regularly compare actual expenses against the budget and make adjustments if there are deviations.

- *Use cost model for scenario analysis*: Using the cost model for scenario analysis involves considering different scenarios and the associated costs with each one. Scenario analysis can help stakeholders understand the financial implications of business model changes, such as modifications to pricing, product offerings, or revenue streams. Scenario analysis also enables you to anticipate how changes in customer acquisition, retention, or churn rates might affect costs. You can forecast increased expenses and plan for scaling.

- *Use cost model for resource optimization*: Use the cost model to help identify areas where cloud resources are underutilized and make adjustments for significant cost savings. The cost model can also forecast the financial implications of scaling up resources in response to increased customer traffic or processing needs. It also helps compare the costs that are associated with cloud providers’ billing models, which allows you to choose the most economical option.

### Maintain the cost model

It's important to regularly update the cost model to reflect the latest data, business conditions, and any changes in the external environment. You should engage stakeholders, including product owners and the technical team, in discussions around the cost model to ensure its relevance and alignment with different teams' needs. Run simulations and review the findings to inform decision-making. Educate all team members on how to use the cost model to foster a culture of data-driven decision-making. Consider the following recommendations:

**Track resource usage.** Monitor the usage of resources in your workload. Tracking resource usage is critical for adjusting cost models and identifying opportunities for cost optimization. Conduct utilization audits to identify underutilized resources and adjust cost estimates accordingly.

**Generate and review forecasts.** Utilize usage data to generate forecasts and project the cost of the workload. Update forecasts regularly and view them often. Investigate any forecasts that deviate significantly from the current cost model. When you find an issue, update the cost model accordingly. The definition of a significant deviation from the cost model is different for each workload. The deviation might be due to changes in workload usage patterns, resource requirements, or pricing changes. By using a forecast, you can foresee exceeding your budget and make changes to the budget or workload design.

**Update the cost model.** Review the cost model periodically to ensure that the workload receives the budget it needs. Use the metrics from the workload in production to inform budget reviews. The potential effect of services or technology changes can create the need for review. As services and technologies evolve, you might need to make changes to the workload design to optimize costs or take advantage of new features. Regular review ensures that the cost model remains aligned with the changing landscape. Review the cost model before and after workload design changes.

Update the cost model whenever you change services. Use the cloud platform's calculator to estimate the cost of the cloud resources that your workload needs. For new workloads, some of the cost variables, such as data transfer and storage amount, can be difficult to estimate. A business target can help you generate estimates. For example, to create a customer-based estimation, divide the daily revenue target ($100,000) by the average purchase per visitor ($100) to get the estimated number of daily visitors that you need to support (1,000).

## Azure facilitation

**Estimating workload costs and developing a cost model**: The Azure [pricing overview](https://azure.microsoft.com/pricing/#product-pricing) provides pricing information for all Azure services. It shows a comprehensive view of the costs that are associated with different Azure services.

The Azure [pricing calculator](https://azure.microsoft.com/pricing/calculator/) is a tool that allows you to estimate the hourly or monthly costs of your workload. Input the services that you plan to use to generate an estimate of the associated costs. This estimate helps you plan and budget for your Azure usage.

The [total cost of ownership (TCO) calculator](https://azure.microsoft.com/pricing/tco/calculator/) helps you estimate the cost savings of migrating your workload to Azure. It takes into account factors such as infrastructure, management, and labor costs to provide an estimate of the total cost of ownership. This estimate helps you make informed decisions about the financial aspects of your Azure migration.

[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) is a program that cloud service providers, like Azure, offer. It allows customers to use their own licenses for certain software products on the cloud. Use your own license to take advantage of discounted pricing for using that software on the cloud platform. Sometimes Azure Hybrid Benefit is part of the customer agreement between the cloud service provider and the customer. This agreement outlines the terms and conditions for utilizing the benefit and the eligible software products that are covered under it.

When you extend your existing investments in software licenses to the cloud, you save on costs. Instead of paying the full price for using the software on the cloud, you benefit from the discounted pricing that Azure Hybrid Benefit offers.

**Setting a budget**: Azure provides tools that allow you to [create and manage budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets). Budgets help you proactively inform others about their spending, manage costs, and monitor spending over time. You can set budget thresholds, receive alerts, and track expenses to ensure cost control and optimization.

**Maintaining a cost model**: Azure automatically provides [cost forecasts](/azure/cost-management-billing/costs/quick-acm-cost-analysis#understand-your-forecast), which enable you to plan and budget for your Azure usage. These forecasts help you understand the projected costs based on your current usage patterns and allow you to make proactive decisions to optimize costs.

Azure allows you to use tag inheritance to [group and allocate costs](/azure/cost-management-billing/costs/enable-tag-inheritance). Tags are metadata that you can assign to Azure resources. With tag inheritance, you can track and manage costs for different teams or projects within your organization to help with cost allocation and analysis.

## Related links

- [Measure unit costs](/azure/cost-management-billing/finops/capabilities-unit-costs)
- [View and download Azure usage and charges](/azure/cost-management-billing/understand/download-azure-daily-usage)

## Cost Optimization checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Cost Optimization checklist](checklist.md)