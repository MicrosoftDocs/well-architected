---
title: Usage monitoring and billing for SaaS workloads on Azure
description: Learn about usage monitoring and billing considerations for SaaS workloads.
author: johndowns
ms.author: jodowns
ms.date: 11/01/2024
ms.topic: conceptual
---

# Usage monitoring and billing for SaaS workloads on Azure

[!INCLUDE [header_file](includes/temporary-warning.md)]

Running a successful SaaS business involves careful financial planning. You need to manage both resource expenditures and customer billing. While related, these are distinct concerns. You must optimize both to succeed.

Understanding the costs of running your solution is critical. You need to analyze, manage, optimize, and control these costs. SaaS differs from many other software types because its business model and pricing strategy are directly linked to the solution architecture.

This article describes some strategies for understanding and optimizing costs within your business model, along with guidance on billing customers for your solution.

## Develop a cost model

Before you can optimize your costs, you need to itemize them. Your *cost of goods sold (COGS)* is the direct cost of delivering your solution, with Azure spend often being a significant part. Knowing those costs and measuring cloud spend provides a baseline for your solution. It's also important to have a cost model because it can help you to reduce your COGS by identifying high-value items for optimization.

In SaaS development, understanding how customers impact costs is crucial. A cost model represents the marginal cost per customer and identifies how business metrics affect costs. Key metrics include the number of customers, users, and transactions. Azure resource consumption is measured by:

- Direct resource costs.
- Usage metrics indicating the cost proportion for specific customers, such as operations performed on behalf of a specific customer, or data volume that you need to store for a customer.

To learn more about fundamentals of cost modeling, see [CO:02 Recommendations for creating a cost model](/azure/well-architected/cost-optimization/cost-model).

### Design considerations

- **Estimate your Azure costs and understand how Azure resources are billed.** Use tools like the [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) to forecast expenses before deployment. Once your resources are deployed, [Microsoft Cost Management](/azure/cost-management-billing/costs/overview-cost-management) helps you analyze, manage, and optimize your cloud spending.

- **Understand how your costs relate to your tenancy model.** Your cost model's granularity should reflect and depend on your tenancy model and resource deployment for each of your customer.

    - **Dedicated resources**. If you host resources for each customer, use tools like Microsoft Cost Management to track costs per customer and roll up costs based on customer-specific resource tags.
    - **Shared resources**. If the deployed resources are shared among multiple customers, approximate cost splits based on customer size or usage metrics. For example, you can allocate costs by estimating each customer's size using selected criteria. Alternatively, measure transactions or other metrics per customer. However, the latter method can be complex and time-consuming.
    - **Customer-hosted resources**. If customers host their resources in their own Azure environments, you might not have direct resource costs but should consider management expenses.

    For more information, see [Measure the consumption of each tenant](/azure/architecture/guide/multitenant/considerations/measure-consumption).

- **Start simple and build gradually**. A rough cost model is better than none. While cost modeling can be time-consuming and complex, it's crucial for business planning and optimizing costs. Start with a high-level model using approximate values, such as:

    - Each customer requires resources X and Y, costing $100 each.
    - Customers over 500 users need resource Z, costing $50.
    - Every 10 customers require a new load balancing system, costing $100.

   Add more details as needed, like if you need to directly charge customers for their consumption, and include other expenses like staff time and support costs.

    For more information, see [Architectural approaches for cost management and allocation in a multitenant solution](/azure/architecture/guide/multitenant/approaches/cost-management-allocation).

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Understand how your Azure resources are billed. | You'll be able to model your costs more effectively, and you can identify ways to optimize costs. |
| Develop a service catalog of specific Azure resources and resource SKUs, which are part of your architecture.| Knowing which specific resources that are required helps determine the total cost of your solution by identifying the required resources and their sizes. |
| Understand Azure services [quotas and limits](/azure/azure-resource-manager/management/azure-subscription-service-limits).<br><br>Quotas can limit resource deployment in a subscription, restrict request volumes for a resource, or alter resource behavior. | SaaS solutions are at particular risk of exceeding quotas because of the way they scale. Understanding quotas helps you to avoid hard limits or incur unnecessary costs. |
| Create a baseline cost model. | Cost models help you to understand and forecast your costs, and to make informed decisions about your architecture based on the effects to your COGS. |
| Focus on identifying important metrics or approximating costs, rather than measuring every detail. <br><br>Identify important metrics, or find ways to approximate your costs. | Collecting excessive metrics for usage measurement can be counterproductive. It complicates data processing, making it harder to understand customer usage accurately. Additionally, it increases storage and processing costs. |
| Set a budget per customer or per service. | You'll have a systematic way to avoid over-spending on customers. |
| Determine your scale points. <br><br> Scaling decisions often rely on key metrics such as the number of customers, users, and transactions. Sales teams can provide projections for these metrics to aid in planning. | Scale points help you to forecast your costs, to relate costs to revenue, and to plan for growth in your technical architecture based on business metrics. |

## Optimize your costs

After establishing a baseline for your cloud spending through cost measurement, you can start optimizing costs. This involves reducing overall expenses while maintaining performance targets.
Cost optimization should be done in conjunction with good governance practices. For more information about how to apply cost governance practices, see the cost governance guidance in [Governance for SaaS workloads on Azure](./governance.md).

### Design considerations

- **Identify cost optimization opportunities.** Your cost model, aligned with growth plans, can help identify high or increasing costs for optimization. It can also set customer budgets for ongoing monitoring. Starting with the largest costs, look for opportunities to optimize.  

- **Share resources among customers.** This approach can help you gain cost efficiency. For example, you might use shared, multitenant infrastructure for the frontend and dedicated infrastructure for the backend data layer.

    > [!NOTE]
    > Ensure you can manage both shared and dedicated usage, mitigate noisy neighbor issues, and meet data residency and other customer constraints.  Sometimes, it might not be appropriate to share resources, and you might instead need to deploy  dedicated infrastructure for each customer by using the [Deployment Stamps pattern](/azure/architecture/patterns/deployment-stamp).

- **Take advantage of Azure offers and discounts.** Azure provides a variety of different subscription types, such as Microsoft Customer Agreement, an Enterprise Agreement, or pay-as-you-go. Special subscriptions and credits are available through the Microsoft AI Cloud Partner Program.

    [Azure Dev/Test pricing](https://azure.microsoft.com/pricing/offers/dev-test/?msockid=1cab8246c181615e309496c8c0ee6050#faqs) provides reduced rates on certain Azure services for non-production use. Even after you are running your production worklod, you can continue to access the rates through a separate dev/test subscription.

    Discounted pricing is available for some services if you commit to a certain spend, such as [Azure Reservations and savings plans](/azure/architecture/guide/multitenant/approaches/cost-management-allocation#use-azure-reservations-and-azure-savings-plan-to-reduce-costs). If you know you'll need the resources for a certain period of time, these discounts can be beneficial. Consolidating customer resources can help qualify for these discounts.

- **Right-size your resources, eliminate resources you no longer use.** Consider the different options Azure provides for resources. For example, Azure offers various options, such as different series of virtual machines, to help you optimize resource allocation. The [Virtual Machine Selector Tool](https://azure.microsoft.com/pricing/vm-selector/) can help choose the right compute for your solution.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Review the [Cost Optimization checklist](../cost-optimization/principles.md) as a guide for cost management in the cloud. | You'll learn approaches that can be used across a variety of Azure services and solution types. |
| Share costs between customers when feasible, ensuring you meet requirements like isolation.<br><br>For resources with limited capacity, consider [bin packing](/azure/architecture/guide/multitenant/approaches/resource-organization#bin-packing) to share the resources.| You'll reduce your overall COGS and your marginal cost for each customer. |
| Use Azure billing constructs, like credits, subscription types, reservations, and saving plans, to reduce your costs. <br><br>For reservations, choose the longest duration you can commit to for the highest discount. | By using the right type of subscription or by pre-committing to a certain level of consumption, you'll receive significant discounts and reduce your overall COGS. |
| Adjust the uptime, size and type of resources to match your business needs and business hours. | You'll take advantage of the elasticity of cloud infrastructure,  focusing spending on critical times for your business. |
| Identify and remove unused resources. | You'll cut out waste. |
| Enable [Microsoft Cost Management](/azure/cost-management-billing/costs/overview-cost-management). | You'll get access to tools that analyze, monitor and optimize your spend in the Microsoft Cloud. |
| Monitor each resource's utilization to ensure optimal use. <br><br> Use [Azure Advisor](/azure/advisor/advisor-overview) and its library of cost optimization recommendations. | You'll utilize deployed and paid resources more effectively. |

## Billing

Most *billing models* are based on customer usage. A billing model typically requires one or more *meters*, which track usage the way your customers use your solution. Common models include license-based billing (such as per user or flat monthly rate) and consumption-based billing (for example, per transaction). Multiple meters can be used for various aspects, like combining per-user and transaction charges.

For more information, see [Pricing models for a multitenant solution](/azure/architecture/guide/multitenant/considerations/pricing-models) and [Measure the consumption of each tenant](/azure/architecture/guide/multitenant/considerations/measure-consumption).

### Design considerations

- **Align billing with costs**. Use customer-friendly billing meters, while your COGS relies on technical metrics like data volumes and API calls. Mismatches between billing and costs can be risky. Identify and address scenarios where high resource usage doesn't lead to higher customer bills, and adjust your pricing and cost model accordingly.

- **Design for billing.** The way in which you bill your customers can influence your solution design.

    For example, you might offer different billing tiers with varying functionality, performance, or deployment models. For example,  bronze, silver, and gold editions of the solution. Bronze customers might use shared infrastructure, silver a mix of shared and dedicated, and gold dedicated and isolated environments. Or, enable or disable features based on billing plans. 
    
    Planning your billing model early is crucial, as retroactive changes can be challenging, though commercial pressures may require adjustments.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Design billing meters that are meaningful to your customers. <br><br> For example, the number of users or business transactions processed are meters that your customers can understand. <br> Avoid using metrics that are easy for you to measure but hard for customers to understand, such as API requests. | You'll give your customers confidence in their understanding of your service, and help them to model their own costs. |
| Plan the implementation of billing plans or SKUs carefully. <br><br> If you opt for multiple billing tiers, ensure a systematic approach. | You'll avoid making last-minute changes to your solution, or customizing your solution for a single customer, which will cause operational complexity in the future. |
| Plan the implementation of discounts carefully. <br><br> Pricing discounts can be complex to manage, even if they only impact billing processes. | You'll prevent customer disappointment for  discounts that your solution or processes can't deliver. |
