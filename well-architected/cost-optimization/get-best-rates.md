---
title: Recommendations for getting the best rates from providers
description: Learn how to get the best rates from providers.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for getting the best rates from providers: CO 05

This guide describes the recommendations for getting the best rates from suppliers for your workload. It’s about lowering the bottom line of your workload bills without modifying the architecture, its functionality, or switching resource types. By optimizing rates, you can reduce cloud costs without changing the workload. Without rate optimization, you end up paying more for your resources, services, and licenses than necessary.

**Definitions**

| Term | Definition                                                                                      |
|---|---|
| Rate        | The unit price of using a service or license.                                                                     |
| Consumption pricing | A pricing model where you're charged based on the actual usage of the service. Examples include the number of virtual machines deployed, the amount of storage used, or the data transferred. |
| Prepaid pricing   | A pricing model where you reserve and pay for a specific amount of usage in advance and can often receive a discounted rate compared to consumption pricing.             |

## Key strategies

A small percentage rate change on services you use a lot provides significant cost optimization. To get the best rates on the components and licenses in your workload, you should start with identifying and reducing costs in the most expensive areas. Evaluate the discounts available from vendors and choose the right discounts based on your workload needs. Regularly check for discounts and optimize any licensing fees, and determine if it's more cost effective to build or buy new workload solutions.

### Create an ordered expenses list

Understanding the workload is the first step to finding and using the best rates on infrastructure, cloud resources, licenses, and third-party services. It prepares you to make informed decisions and implement cost optimization strategies specific to the needs of the workload. Here are steps you can take to understand the workload rates:

- *Take inventory*: List all the components of your workload, including infrastructure, cloud resources, licenses, third-party services, and any other expenses related to the workload.
- *Understand spending*: Gain a clear understanding of the current spend for each item in the inventory list. It helps you identify what you are paying for and where most your expenses lie.
- *Create an ordered list*: Create an ordered list of workload expenses, starting from the most expensive components and working down the list. It allows you to prioritize your optimization efforts and focus on the areas that have the highest impact on cost.

By following these steps, you gain a comprehensive understanding of your workload's cost structure and identify areas where you can optimize rates.

### Determine the right billing model

Determining the billing model involves choosing between consumption-based (pay-as-you-go) and prepaid pricing strategies. The selection should be based on the predictability, duration, and utilization consistency of workload components. This decision requires collaboration between development and purchasing teams to evaluate resource needs, usage patterns, and potential cost optimizations. Selecting the right billing model is crucial to ensure cost-effectiveness. It helps align the workload with business objectives and get the best rates for the specific requirements of a workload. To determine the right billing model, consider the following strategies:

#### Understand consumption pricing

Consumption pricing is a flexible pricing model that allows you to pay for the services you use on a consumption basis. It’s also known as pay-as-you-go pricing. With consumption pricing, you're charged based on the actual usage of the service. Cost variables include how long a resource is running, and service meters have different billing increments, such as per hour or per second. This model provides flexibility and cost control as you only pay for what you use. Consumption pricing is best suited for the following scenarios:

- *Variable workload*: A variable workload has unpredictable spikes or seasonal variations in usage. Consumption-based pricing allows you to scale resources up or down to meet the fluctuations in demand while providing the required performance always and not overpaying during low usage times.
- *Preproduction environments*: Consumption-based pricing is preferred for development and test environments that are ephemeral. It offers the advantage of paying only during the project. Ensure you provide resources aligned with the development effort, where resources cost less when development is scaled down.
- *Short-term projects*: Short-term projects often have specific resource requirements. Pay-as-you-go pricing allows you to pay for the resources only during the project.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Many on-premises environments are always on and always available. Being intentional about services might lower rates, but you must account for some spin up time and operational overhead.

#### Understand prepaid pricing

Prepaid pricing allows you to reserve a specific amount for a specific duration and pay for it in advance. By reserving the usage upfront, you can receive a discounted rate compared to pay-as-you-go pricing. The cost savings achieved through prepaid pricing depend on factors such as the duration of the reservation, the amount of capacity reserved, and the specific service. Prepaid pricing is best suited for the following scenarios:

- *Predictable workloads*: If your workload has a consistent usage pattern, you can commit to a certain capacity over time and receive a significant discount over pay-as-you-go pricing. Those instances incur charges whether you use them or not.
- *Production environments*: Reserved capacity pricing is suitable for production environments where you have a good understanding of the workload's resource needs
- *Long-term projects*: Reserved capacity pricing can be cost-effective for projects with long-term resource requirements, even if they aren't highly predictable

#### Discuss options with the workload team

To ensure effective workload cost optimization, it's crucial for the development team/architect and the purchasing team to collaborate and work together. Combining their expertise enables you to identify opportunities to optimize costs and make informed decisions. Here's a suggested process:

- *Identify cost optimization opportunities*: Together, the teams should identify potential areas for cost optimization, such as infrastructure, cloud resources, licenses, and third-party services. Consider factors like utilization patterns, scalability, and workload requirements.
- *Assess resource requirements*: Determine the resources needed to support the component or workload. Consider factors such as infrastructure, maintenance, and ongoing support. Understanding these requirements can help you gauge the long-term commitment involved.
- *Evaluate different options*: Assess different cost optimization options, such as pay-as-you-go versus prepaid plans. Evaluate the pros and cons of each option in terms of cost savings and impact on performance. Evaluate the different performance tiers in each service and the pricing differences between them.

#### Determine component permanence

It's important to assess how long you need a particular component to determine if committing to a prepaid plan makes sense. If the expected usage duration is less than a year, don’t to commit to a prepaid plan. Consider the flexibility of pay-as-you-go options for shorter-term requirements. To determine the duration of component usage, you can follow the process:

- *Gather usage data*: Collect data on the historical usage of the component or workload. This data can include information on how long the component has been in operation, the frequency of usage, and any patterns or trends in its utilization.
- *Analyze usage patterns*: Analyze the collected usage data to identify patterns and trends. Look for consistent usage over a specific period of time or recurring usage patterns. This analysis helps you understand the typical duration of component usage.
- *Consider future requirements*: Consider any future requirements or changes in your workload. Evaluate if there are any upcoming changes that might affect its usage duration.
- *Assess business needs*: Evaluate the business needs and objectives associated with the component or workload. Consider factors such as project timelines, budget constraints, and the overall strategy of your organization. Evaluate any planned changes or updates to the component or workload. Understanding future developments can help you assess the long-term commitment required and whether it aligns with your objectives. This assessment helps you determine the appropriate duration for component usage.

#### Determine utilization consistency

When considering prepaid commitments, it's recommended to commit to the maximum consistent utilization of a component. By committing to the maximum consistent utilization, you can maximize the potential savings and cost optimization. However, there are a few factors to consider:

- *Utilization patterns*: Analyze the historical utilization patterns of the component. If the utilization is consistently high and stable, committing to the maximum consistent utilization makes sense. However, if the utilization is highly variable or unpredictable, committing to the maximum utilization might not be feasible or cost-effective.
- *Flexibility and scalability*: Consider the scalability and flexibility of the component. If the component can easily scale up or down based on demand, it might be more suitable to opt for flexible pricing models that allow you to adjust resources dynamically. This way, you can align your costs with the actual utilization of the component.
- *Engage with the vendor/supplier*: Communicate with the vendor or supplier to gather information about their plans, roadmap, and commitment to the component or workload. This dialogue provides valuable insights into their long-term vision and commitment level.
- *Cost analysis*: Perform a cost analysis to compare the potential savings from committing to the maximum utilization with the potential risks of underutilization. Evaluate the cost implications of committing to a higher utilization level and assess whether the potential savings outweigh the risks of not fully utilizing the commitment.

### Evaluate available discounts

Evaluating available discounts for a workload refers to the process of assessing and analyzing the potential cost savings or discounts that can be applied to a specific workload. Assessing available supplier discounts allows you to identify opportunities for cost reduction and optimize the expenditure associated with the workload. It helps you identify potential savings and allocate resources more efficiently.

- *Ask about trial offerings*: Use supplier-provided trial periods or negotiate free or reduced rates to execute proof of concepts. This approach allows you to try out the services or products with limited financial risk, enabling you to assess their suitability for your workload before committing to a purchase. Remember to review the terms and conditions of any trial periods or negotiated agreements.
- *Review vendor offerings*: Understand the discounts and pricing models offered by vendors or suppliers. Explore volume-based discounts, promotional offers, or long-term commitment discounts. Discuss the available options that can meet the variability and flexibility requirements of your workload. Include exploring different pricing models, scaling options, or prepaid agreements.
- *Analyze usage and consumption*: Assess the workload's usage and consumption patterns to determine if it meets the eligibility criteria for specific discount programs. This analysis helps you identify the most suitable discounts for your workload.
- *Evaluate contract terms*: Review the terms and conditions of existing contracts or agreements to identify any potential discount options. Consider understanding the duration of the commitment, renewal terms, and the possibility of negotiating better rates.
- *Communicate with vendors*: Know the actual and anticipated usage of a workload when you discuss discounts. Let the vendor know what environment the discussion is about. For example, you can often get discounts on preproduction environments. Ask vendors or suppliers to discuss available discount options, such as product bundling products. Ask specific questions about discount programs, eligibility criteria, and any negotiation possibilities.
- *Understand reseller options*: Consider engaging with resellers who can provide extra insights into available discounts or offer alternative pricing models. Resellers might have access to specialized programs or discounts that can benefit your workload.

### Commit to the right discounts

Committing to the right discount options is where you act on your evaluation. You’re equipped with the available option. You’ve communicated your needs and workload data to the various vendors. Now you need to lock in the discounted rates for a defined period, which can result in significant cost savings compared to pay-as-you-go pricing.

- *Choose an appropriate prepaid plan*: Select a prepaid plan that covers the minimum capacity required by the workload. Starting with the minimum commitment provides you with flexibility while still benefiting from cost savings. Before committing to a prepaid plan, wait until you have a clear understanding of the workload's minimum capacity requirements. It minimizes the risk and ensures that you optimize your savings effectively. However, there are exceptions. If the commitment requires minimal upfront costs, then it lowers the risk of commitment. The lower the commitment risk, the quicker you can commit to a prepaid plan. As the cost and risk of a prepaid commitment grows, you need to have a clear understanding of your minimum consistent usage per component you’re committing to.
- *Increment prepaid commitments*: As the capacity of your workload grows, gradually increase your prepaid commitments. Start small and scale up. Increment scaling up based on the workload's actual usage.
- *Renegotiate and consolidate*: Regularly renegotiate and normalize reservations and savings plans to align their ending time. It allows you to consolidate them into a single line item on your bill, making it easier to manage and optimize costs.

### Determine to build or buy a solution

Building a solution in-house allows for granular control over the features and configuration, which can help eliminate unnecessary functionality and optimize rates. However, building a solution in-house requires significant upfront investment in terms of development time and maintenance over time. When you buy a solution, such as from a marketplace, it offers quicker deployment with potentially lower upfront costs, but buying a solution might involve ongoing subscription or licensing fees. Here are key considerations when choosing to build or buy a solution:

- *Control and customization*: Assess the specific functionality needed for your product or solution. Determine whether buying a solution meet your requirements or if building allows for more customization and flexibility that provide better rates. Building a solution offers greater control over component selection and configuration. You can add customization to fit business needs and minimize unneeded features that might incur charges. Buying solutions provide preconfigured options with limited customization capabilities.
- *Time-to-Market*: Assess the urgency and time constraints for deploying the workload component/solution. Building a solution in-house might take longer due to development and testing, while buying a solution allows for quicker deployment.
- *Technical expertise*: Building might require higher technical expertise to ensure proper configuration and maintenance over time. A custom solution requires effort over time and not only upfront. Buying a solution is often more user-friendly and requires less technical knowledge.
- *Cost*: Evaluate the total cost of building a solution, including development resources, infrastructure, ongoing maintenance, and support. Compare the cost of building a solution with the cost of buying a solution. Include any support plans, licensing, or subscription fees. Buying a solution might provide more predictable pricing and potential discounts due to economies of scale.
- *Support and updates*: Consider the availability of support and updates for both building and buying. Assess the level of technical expertise required for each option and the ease of accessing support resources. Updates for custom solutions add costs, requiring separate environments, testing, and backups. For purchased solutions, research the reputation and track record of the marketplace vendors. Consider factors such as vendor reliability, customer reviews, and the level of support provided. Consider the billing cycles. For example, subscription billing cycles are incentivized to maintain the quality of the solution over time. One-time purchases don’t have same cost incentive to maintain the solution.

### Optimize licensing costs

Explore licensing programs offered by your vendor to reduce licensing costs. Include any license across your design, build, and run stack. Include software development tooling, security tooling, observability tooling, and design tools. These programs might include options for hybrid use, bundling, negotiations, dev/test pricing, and per-seat/site-wide purchases.

- *Explore licensing programs*: It's important to investigate the licensing programs offered by your provider to identify potential cost-saving options.
- *Hybrid use and bundling*: In addition to exploring licensing programs, consider using hybrid use and bundling options. These programs can provide extra cost savings by optimizing licensing for both on-premises and cloud environments.
- *Negotiations*: Don't hesitate to negotiate with your provider to secure better licensing terms. Negotiations can often lead to more favorable pricing and discounts.
- *Dev/test pricing*: Take advantage of dev/test pricing options offered by your provider. These programs typically provide discounted rates for nonproduction environments, allowing you to save costs during development and testing phases.
- *Volume discounts*: As your usage increases, you might become eligible for volume discounts. Cloud service providers often offer discounted rates based on the scale of usage, so it's important to monitor your usage and explore opportunities for cost optimization.
- *Existing enterprise agreements*: Check your existing enterprise agreements to see if there are any licensing benefits or cost-saving opportunities available. Your procurement department or license reseller can provide valuable insights in this regard.

## Azure facilitation

**Cost Management and Billing**: Azure provides tools and features for managing and optimizing costs, such as Microsoft Cost Management and Billing. These tools allow you to track and analyze your cloud spending, set budgets, receive cost alerts, and access detailed cost reports.

**Azure Reservations and Azure Savings Plans**: Azure offers Reservations and Savings Plans. Both allow you to commit to using specific resources for a term and receive significant discounts in return. By committing to reserved capacity, you can achieve cost savings on various Azure services.

- Azure [Reservations](/azure/cost-management-billing/reservations/save-compute-costs-reservations): Azure Reservations help you save money by committing to one-year or three-year plans for multiple products. Committing allows you to get a discount on the resources you use. Reservations can significantly reduce your resource costs from pay-as-you-go prices. Reservations provide a billing discount and don't affect the runtime state of your resources. After you purchase a reservation, the discount automatically applies to matching resources. You should use reserved instances when certain services, SKUs, and locations aren't expected to change over time. It's highly recommended to begin with a reservation for optimal cost savings.

- [Azure Savings Plan](/azure/cost-management-billing/reservations/save-compute-costs-reservations): Azure savings plan for compute is a flexible pricing model. It provides savings off pay-as-you-go pricing when you commit to spend a fixed hourly amount on compute services for one or three years. Committing to a savings plan allows you to get discounts, up to the hourly commitment amount, on the resources you use. Savings plan commitments are priced in USD for MCA and CSP customers, and in local currency for EA customers. Savings plan discounts vary by meter and by commitment term (1-year or 3-year), not commitment amount. Savings plans provide a billing discount and don't affect the runtime state of your resources. You should use Azure Savings Plans more flexibility in covering diverse compute expenses, committing to a specific hourly spend.

**Azure Dev/Test**: [Azure Dev/Test](/azure/devtest/offer/overview-what-is-devtest-offer-visual-studio) is an offer that comes with your Visual Studio subscription benefits. With this offer, you get some Azure monthly credits to explore and try various Azure services at no cost. Credit amounts vary by subscription level. Users can also benefit from discounted Azure dev/test rates for various Azure services, enabling cost-efficient development and testing.

**Azure services:** Many Azure services offer both consumption and prepaid billing models to potential switch to better align to your usage, potentially without sacrificing functionality.

**Azure Hybrid Benefit**: [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) (AHUB) enables you to use your existing on-premises licenses to cover the cost of running resources in Azure, reducing the overall cost of ownership. This benefit applies to both Windows and Linux virtual machines and SQL Server workloads. To take advantage of the Azure Hybrid Benefit, you need to ensure that their licenses are eligible for the benefit and meet the necessary requirements. They can then apply the benefit to their Azure resources and reduce costs.

**License portability**: Azure supports [license mobility](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility), allowing you to bring your own licenses for certain software products and apply them to Azure resources. It can help reduce licensing costs and simplify license management.

**Licensing agreements.** Microsoft offers commitment-based and transactional licensing options for organizations that want to purchase Microsoft cloud services subscriptions, on-premises software licenses, and/or [Software Assurance](https://www.microsoft.com/Licensing/licensing-programs/software-assurance-default). You should apply these offers to your workload as applicable. Microsoft offers various volume licensing programs and agreements based on your workload need, including:

- [Microsoft Enterprise Agreement](https://www.microsoft.com/licensing/licensing-programs/enterprise)
- [Microsoft Customer Agreement](https://www.microsoft.com/Licensing/how-to-buy/microsoft-customer-agreement)
- [Microsoft Open Value program](https://www.microsoft.com/Licensing/licensing-programs/open-license)
- [Microsoft Products and Services Agreement](https://www.microsoft.com/licensing/MPSA/default?rtc=1)

For more information, see [Microsoft Licensing Resources](https://www.microsoft.com/licensing/default)

**Azure Spot instances**: Azure Spot Instances provide access to unused Azure compute capacity at discounted prices. By using Spot Instances, you can save money on workloads that are flexible and can handle interruptions.
