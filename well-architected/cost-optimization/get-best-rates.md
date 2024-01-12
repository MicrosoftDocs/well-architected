---
title: Recommendations for getting the best rates from providers
description: Learn how to get the best rates from providers for your workload without modifying architecture, resources, or functionality.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for getting the best rates from providers

**Applies to this Azure Well-Architected Framework Cost Optimization checklist recommendation:**

|[CO:05](checklist.md)| Get the best rates from providers. You should find and use the best rates for cloud resources and licenses. Regularly review cost savings. Cost reviews should include regional pricing, pricing tiers, pricing models (consumption or commitment-based), license portability, corporate purchasing plans, and price sheets.|
|---|---|

This guide describes the recommendations for getting the best rates from providers for your workload. Getting the best rates is the practice of finding and securing the most cost-efficient pricing options for cloud and software resources without modifying architecture, resources, or functionality. By optimizing rates, you can reduce cloud costs without changing the workload. A small rate reduction on services you use a lot provides significant cost savings. Without rate optimization, you end up paying more for your resources, services, and licenses than necessary.

**Definitions**

| Term | Definition |
|---|---|
| Consumption-based billing model | A pricing model where you're charged based on the actual usage of the service. Examples include the number of virtual machines deployed, the amount of storage used, and the amount of data transferred. |
| Commitment-based billing model | A pricing model where you reserve and pay for a specific amount of usage in advance and can often get a discounted rate compared to consumption pricing. |
| Rate | The unit price for using a service or license. |

## Key design strategies

Getting the best rates requires actively searching for the most cost-effective pricing models for all workload components. It weighs the benefits of different billing models, such as consumption (pay-as-you-go) versus commitment-based billing, software licenses and corporate discount plans, and the  price differences between regions. 

To get the best rates on the resources and licenses in your workload, you should start with identifying and reducing costs in the most expensive areas. Evaluate the discounts available from providers, and choose the right discounts based on workload needs. Regularly check for discounts and reduce licensing fees where possible. Determine if it's more cost effective to build or buy new workload solutions.

### Create an ordered list of expenses

Understanding the workload is the first step to finding and using the best rates on infrastructure, resources, licenses, and third-party services. It prepares you to make informed decisions and implement cost optimization strategies that are specific to the needs of the workload.

Here are actions that you can take to understand the workload rates:

- *Take inventory.* List all the components of your workload, including infrastructure, cloud resources, licenses, third-party services, and any other expenses related to the workload.

- *Understand spending.* Gain a clear understanding of the current spending for each item in the inventory list. Identify what you're paying for and where most of your expenses lie.

- *Create an ordered list of workload expenses.* List the most expensive components and work your way down to the least expensive. This exercise helps you prioritize your optimization efforts and focus on the areas that have the highest effects on cost.

### Determine the right billing model

For your billing model, you choose between consumption (pay-as-you-go) and commitment-based billing models. You base the selection of consumption versus commitment-based pricing on the predictability, duration, and usage consistency of workload components. When you make this decision, you must collaborate with development and purchasing teams to evaluate resource needs, usage patterns, and potential cost optimization ideas.

Selecting the right billing model is crucial for cost-effectiveness. It helps align the workload with business objectives and get the best rates for the specific requirements of a workload. To determine the right billing model, consider the following strategies:

#### Understand the consumption-based billing model

Consumption-based billing model (pay-as-you-go) is a flexible pricing model that allows you to pay for services as you use them. Cost variables for consumption pricing include how long a resource is running. Service meters have various billing increments, such as per hour or per second. This model provides flexibility and cost control, because you pay for only what you consume.

The consumption-based billing model is best suited for the following scenarios:

- *Variable workload*: A variable workload has unpredictable spikes or seasonal variations in usage. Consumption-based billing allows you to scale resources up or down to meet the fluctuations in demand. It helps you to provide the required performance and not overpay during times of low usage.

- *Preproduction environments*: Consumption-based billing is preferred for development and test environments that are ephemeral. It offers the advantage of paying only during the project. Ensure that you provide resources aligned with the development effort. Resources cost less when development is scaled down.

- *Short-term projects*: Short-term projects often have specific resource requirements. Consumption-based billing allows you to pay for the resources only during the project.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Many on-premises environments are always on and always available. Being intentional about services might lower rates, but you must account for some creation time and operational overhead.

#### Understand the commitment-based billing model

Commitment-based pricing allows you to reserve a specific amount for a specific duration and pay for it in advance. By reserving the usage up front, you can get a discounted rate compared to consumption-based billing.

The amount you save with commitment-based pricing depends on factors such as the duration of the reservation, the reserved capacity, and the service. Commitment-based pricing is best suited for the following scenarios:

- *Predictable workloads*: If your workload has a consistent usage pattern, you can commit to a certain capacity over time and get a significant discount over consumption-based billing. Those instances incur charges whether you use them or not.

- *Production environments*: Commitment-based billing is suitable for production environments where you have a good understanding of the workload's resource needs.

- *Long-term projects*: Commitment-based billing can be cost-effective for projects that have long-term resource requirements, even if they aren't highly predictable.

#### Discuss options with the workload team

To ensure effective optimization of workload costs, the development team (or architect) and the purchasing team must work together. Combining their expertise enables you to identify opportunities to optimize costs and make informed decisions.

Here's a suggested process for collaborating on rate reduction efforts:

1. *Identify opportunities for cost optimization*: Together, the teams should identify potential areas for cost optimization, such as infrastructure, cloud resources, licenses, and third-party services. Consider factors like usage patterns, scalability, workload, and regional requirements per environment.

1. *Assess resource requirements*: Determine the resources needed to support the component or workload. Consider factors such as infrastructure, maintenance, and ongoing support. Understanding these requirements can help you gauge the long-term commitment involved.

1. *Evaluate options*: Assess your options for cost optimization, such as pay-as-you-go versus commitment-based plans. Evaluate the pros and cons of each option in terms of cost savings and effect on performance. Evaluate the performance tiers in each service and the pricing differences between them.

#### Determine component permanence

It's important to assess how long you need a particular component to determine if committing to a commitment-based plan makes sense. If the expected usage duration is less than a year, don't commit to a commitment-based plan. Consider the flexibility of pay-as-you-go options for shorter-term requirements.

To determine the duration of component usage, you can follow this process:

1. *Gather usage data*: Collect data on the historical usage of the component or workload. This data can include how long the component has been in operation and the frequency of usage.

1. *Analyze usage patterns*: Analyze the collected usage data to identify patterns and trends. Look for consistent usage over a specific period of time or recurring usage patterns. This analysis helps you understand the typical duration of component usage.

1. *Consider future requirements*: Consider any future requirements or changes in your component or workload. Evaluate whether any upcoming changes might affect its usage duration.

1. *Assess business needs*: Evaluate the business needs and objectives associated with the component or workload. Consider factors such as project timelines, budget constraints, and the overall strategy of your organization.

   Anticipating future developments can help you assess the long-term commitment required and whether it aligns with your objectives. This assessment helps you determine the appropriate duration for component usage.

#### Determine usage consistency

When you're considering a commitment-based plan, commit to the maximum consistent usage of a component. By committing to the maximum consistent usage, you can maximize the potential savings and cost optimization. However, there are a few factors to consider:

- *Usage patterns*: Analyze the historical usage patterns of the component. If the usage is consistently high and stable, committing to the maximum consistent usage makes sense. But if the usage is highly variable or unpredictable, committing to the maximum consistent usage might not be feasible or cost-effective.

- *Flexibility and scalability*: Consider the flexibility and scalability of the component. If the component can easily scale up or down based on demand, it might be more suitable to opt for flexible pricing models that allow you to adjust resources dynamically. This way, you can align your costs with the actual usage of the component.

- *Engagement with the provider*: Communicate with the provider to gather information about its plans, roadmap, and commitment to the component or workload. This dialog provides valuable insights into the provider's long-term vision and commitment level.

- *Cost analysis*: Perform a cost analysis to assess whether the potential savings of committing to a higher usage level outweighs the risks of not fully utilizing the commitment.

#### Select the right commitment-based plan

Strategic usage of commitment-based plan can significantly minimize costs for applicable resources. It allows you to effectively plan and allocate resources. To select the right commitment-based plan, consider the following strategies:

- *Choose an appropriate commitment-based plan*: Select a commitment-based plan that covers the minimum capacity that the workload requires. Starting with the minimum commitment gives you flexibility while you still benefit from cost savings.

  Having a clear understanding of the workload's minimum capacity requirements before you commit to a commitment-based plan minimizes risk and ensures that you optimize your savings. However, there are exceptions. A commitment that requires minimal upfront costs has a lower risk. The lower the commitment risk, the quicker you can commit to a commitment-based plan. As the cost and risk of a commitment grow, you need to understand your minimum consistent usage for each component you're committing to.

- *Increment commitments*: As the capacity of your workload grows, gradually increase your commitments. Start small and scale up. Increment scaling up based on the workload's actual usage.

- *Renegotiate and consolidate*: Regularly renegotiate and normalize commitment-based plans to align their ending time. This alignment allows you to consolidate them into a single line item on your bill, so it's easier to manage and optimize costs.

- *Eliminate underutilization*: You need to evaluate and optimize commitment-based contracts to ensure they deliver their full potential value. Regularly review and analyze your charges and usage data. Understand the breakdown between actual cost and amortized costs and reconcile the data to ensure accurate billing.

    Monitor utilization. Keep an eye on how much you're using your commitment-based plans. Set up alerts to tell you if you're not using all of your reserved resources. Check how you're using them over time and get rid of any you're not using. Make sure you're using the right size of virtual machines to get the most out of your plan. You can also adjust the sizes to fit what you already paid for.

    Modify the commitment-based plan. Consider changing the scope of the reservation to share, allowing it to apply more broadly across your resources. It can help increase utilization and maximize savings. If you find underused commitment-based plans, try exchanging unused quantity or canceling and refunding plans.

### Evaluate and commit to available discounts

Assess and analyze the potential discounts that can be applied to a specific workload. This process helps you identify opportunities for cost reduction and optimize the expenditure associated with the workload. It also helps you allocate resources more efficiently.

Try these tasks:

- *Ask about trial offers*: Use a provider's trial periods or negotiate free or reduced rates to execute proofs of concept. This approach allows you to try out the services or products with limited financial risk, so you can assess their suitability for your workload before you commit to a purchase. Remember to review the terms and conditions of any trial periods or negotiated agreements.

- *Review provider offerings*: Understand the discounts and pricing models that providers offer. Explore volume-based discounts, promotional offers, or discounts for long-term commitments. Discuss the available options that can meet the variability and flexibility requirements of your workload. Include information about different pricing models, scaling options, or commitment-based agreements.

- *Analyze usage and consumption*: Assess the workload's usage and consumption patterns to determine if the workload meets the eligibility criteria for specific discount programs. This analysis helps you identify the most suitable discounts for your workload.

- *Evaluate contract terms*: Review the terms and conditions of existing contracts or agreements to identify any potential discount options. Consider the duration of the commitment, renewal terms, and the possibility of negotiating better rates.

- *Communicate with providers*: Know the actual and anticipated usage of a workload when you discuss discounts. Let the providers know what environment the discussion is about. For example, you can often get discounts on preproduction environments. Ask providers to discuss available discount options, such as product bundling. Ask specific questions about discount programs, eligibility criteria, and any negotiation possibilities.

- *Understand reseller options*: Consider engaging with resellers who can provide extra insights into available discounts or offer alternative pricing models. Resellers might have access to specialized programs or discounts that can benefit your workload.

Committing to the right discount options is where you act on your evaluation. You're equipped with the available options. You communicated your needs and workload data to the various providers. Now you need to lock in the discounted rates for a defined period, which can result in significant cost savings compared to pay-as-you-go pricing.

### Decide whether to build or buy a solution

Building a solution in-house allows for granular control over the features and configuration. This control can help you eliminate unnecessary functionality and optimize rates. However, building a solution in-house requires significant upfront investment in development time and maintenance.

When you buy a solution, such as from a marketplace, it offers quicker deployment with potentially lower upfront costs. But buying a solution might involve ongoing subscription or licensing fees.

Here are key considerations when you're deciding whether to build or buy a solution:

- *Control and customization*: Assess the specific functionality that you need for your product or solution. Determine whether buying a solution meets your requirements or whether building allows for the customization and flexibility that provide better rates.

  Building a solution offers greater control over component selection and configuration. You can add customization to fit business needs and minimize unneeded features that might incur charges. Buying solutions provides preconfigured options with limited customization capabilities.

- *Time to market*: Assess the urgency and time constraints for deploying the workload component or solution. Building a solution in-house might take longer because of development and testing, whereas buying a solution allows for quicker deployment.

- *Technical expertise*: Building might require greater technical expertise to ensure proper configuration and maintenance over time. A custom solution requires effort up front and over time. Buying a solution is often more user-friendly and requires less technical knowledge.

- *Cost*: Evaluate the total cost of building a solution, including development resources, infrastructure, ongoing maintenance, and support. Compare the cost of building a solution with the cost of buying a solution. Include any support plans, licensing, or subscription fees. Buying a solution might provide more predictable pricing and potential discounts due to economies of scale.

- *Support and updates*: Consider the availability of support and updates for both building and buying. Assess the level of technical expertise required for each option and the ease of accessing support resources.

  Updates for custom solutions add costs by requiring separate environments, testing, and backups. For purchased solutions, research the reputation and track record of the marketplace providers. Consider factors such as provider reliability, customer reviews, and the provided level of support.

  Also consider the billing cycles. For example, subscription billing cycles are incentivized to maintain the quality of the solution over time. One-time purchases don't have the same cost incentive to maintain the solution.

### Optimize licensing costs

Optimizing licensing costs means using various licensing programs and options to minimize expenses while maximizing value. This approach helps ensure you get the best rates from providers, preventing overpayment for software and services. It's important to review the license associated with its design, build, and deployment phases. This review should encompass tools used in its software development, security, monitoring, and design components. These licensing programs might include options for:

- *Hybrid use and bundling*: In addition to exploring licensing programs, consider using hybrid use and bundling options. These programs can provide extra cost savings by optimizing licensing for both on-premises and cloud environments.

- *Negotiations*: Don't hesitate to negotiate with your provider to secure better licensing terms. Negotiations can often lead to more favorable pricing and discounts.

- *Dev/test pricing*: Take advantage of dev/test pricing options that your provider offers. These programs typically provide discounted rates for nonproduction environments, so you can save costs during development and testing phases.

- *Volume discounts*: As your usage increases, you might become eligible for volume discounts. Cloud service providers often offer discounted rates based on the scale of usage, so it's important to monitor your usage and explore opportunities for cost optimization.

- *Existing enterprise agreements*: Check your existing enterprise agreements to see if any licensing benefits or cost-saving opportunities are available. Your procurement department or license reseller can provide valuable insights in this area.

## Azure facilitation

**Microsoft Cost Management**: Azure provides tools and features for managing and optimizing costs, such as Microsoft Cost Management. These tools allow you to track and analyze your cloud spending, set budgets, get cost alerts, and access detailed cost reports.

**Azure reservations and Azure savings plans**: Reservations and savings plans allow you to commit to using specific resources for a term and get significant discounts on Azure services. Here are the details:

- [Azure reservations](/azure/cost-management-billing/reservations/save-compute-costs-reservations) help you save money by committing to one-year or three-year plans for multiple products. Committing allows you to get a discount on the resources that you use.

  Reservations can significantly reduce your resource costs from pay-as-you-go prices. Reservations provide a billing discount and don't affect the runtime state of your resources. After you purchase a reservation, the discount automatically applies to matching resources.
  
  You should use reserved instances when you don't expect certain services, products, and locations to change over time. We highly recommend that you begin with a reservation for optimal cost savings.

- An [Azure savings plan](/azure/cost-management-billing/savings-plan/savings-plan-compute-overview) for compute is a flexible pricing model. It provides savings off pay-as-you-go pricing when you commit to spending a fixed hourly amount on compute services for one or three years.

  Committing to a savings plan allows you to get discounts, up to the hourly commitment amount, on the resources that you use. Savings plan commitments are priced in US dollars for Microsoft Customer Agreement and Cloud Solution Provider customers, and in local currency for Enterprise Agreement customers. Savings plan discounts vary by meter and by commitment term (one year or three years), not commitment amount.
  
  Savings plans provide a billing discount and don't affect the runtime state of your resources. You should use Azure savings plans for more flexibility in covering diverse compute expenses by committing to specific hourly spending.

**Eliminating unused reservations and savings plans**: To eliminate unused reservations and savings plans, you can use the Microsoft Cost Management and Billing tools. They provide insights into your reservation and savings plan usage, allowing you to identify any unused or underutilized commitments and make adjustments accordingly. Utilization can be viewed in the Azure portal under the Reservations section.

**Azure dev/test**: [Azure dev/test](/azure/devtest/offer/overview-what-is-devtest-offer-visual-studio) is an offer that comes with Visual Studio subscription benefits. With this offer, you get some Azure monthly credits to try various Azure services at no cost. Credit amounts vary by subscription level. You can also benefit from discounted Azure dev/test rates for various Azure services, which enable cost-efficient development and testing.

**Azure services**: Many Azure services offer both consumption and commitment-based billing models. You can switch to better align to your usage, potentially without sacrificing functionality.

**Azure Hybrid Benefit**: With [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/), you can reduce the overall cost of ownership by using your existing on-premises licenses to cover the cost of running resources in Azure. This benefit applies to both Windows and Linux virtual machines, along with SQL Server workloads. To take advantage of Azure Hybrid Benefit, you need to ensure that your licenses are eligible and meet the requirements.

**License Mobility**: Azure supports [License Mobility](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility). You can bring your own licenses for certain software products and apply them to Azure resources. This ability can help reduce licensing costs and simplify license management.

**Licensing agreements**: Microsoft offers commitment-based and transactional licensing options for organizations that want to purchase Microsoft cloud services subscriptions, on-premises software licenses, or [Software Assurance](https://www.microsoft.com/Licensing/licensing-programs/software-assurance-default). Use these offers for your workload as applicable. Microsoft offers various volume licensing programs and agreements based on your workload's needs, including:

- [Microsoft Enterprise Agreement](https://www.microsoft.com/licensing/licensing-programs/enterprise)
- [Microsoft Customer Agreement](https://www.microsoft.com/Licensing/how-to-buy/microsoft-customer-agreement)
- [Microsoft Open Value program](https://www.microsoft.com/Licensing/licensing-programs/open-license)
- [Microsoft Products and Services Agreement](https://www.microsoft.com/licensing/MPSA/default?rtc=1)

For more information, see the [Microsoft licensing resources](https://www.microsoft.com/licensing/default).

**Azure spot instances**: Azure spot instances provide access to unused Azure compute capacity at discounted prices. By using spot instances, you can save money on workloads that are flexible and can handle interruptions.

## Related links

- [Azure reservations](/azure/cost-management-billing/reservations/save-compute-costs-reservations)
- [Azure savings plan](/azure/cost-management-billing/savings-plan/savings-plan-compute-overview)
- [Azure dev/test](/azure/devtest/offer/overview-what-is-devtest-offer-visual-studio)
- [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)
- [License Mobility](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility)
- [Software Assurance](https://www.microsoft.com/Licensing/licensing-programs/software-assurance-default)
- [Microsoft Enterprise Agreement](https://www.microsoft.com/licensing/licensing-programs/enterprise)
- [Microsoft Customer Agreement](https://www.microsoft.com/Licensing/how-to-buy/microsoft-customer-agreement)
- [Microsoft Open Value program](https://www.microsoft.com/Licensing/licensing-programs/open-license)
- [Microsoft Products and Services Agreement](https://www.microsoft.com/licensing/MPSA/default?rtc=1)
- [Microsoft licensing resources](https://www.microsoft.com/licensing/default)

## Cost Optimization checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Cost Optimization checklist](checklist.md)
