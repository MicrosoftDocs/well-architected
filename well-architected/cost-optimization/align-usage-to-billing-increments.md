---
title: Recommendations for aligning billing and usage
description: Learn how to align usage to billing increments by adapting a resource to your workload usage or adapting your workload to the resource billing increments.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for aligning usage to billing increments

**Applies to this Azure Well-Architected Framework Cost Optimization checklist recommendation:**

|[CO:06](checklist.md)| Align usage to billing increments. You should understand billing increments (meters) and align resource usage to those increments. Modify the service to align with billing increments, or modify resource usage to align with billing increments. Consider using a proof of concept to validate billing knowledge and design choices for major cost drivers and to reveal ways to align billing and resource usage.|
|---|---|

This guide describes the recommendations for aligning resource usage to billing increments. Resources are billed at specific increments, such as per hour or per instance. To optimize costs, you need to align your usage to those increments. You must either adapt a resource to your workload usage or adapt your workload to the resource billing increments, also called *meters*. Implement the following guidance so you can ensure that your workload derives the maximum value from each resource. If you fail to align billing and design, you might incur unnecessary charges.

**Definitions**

| Term | Definition  |
|----|---|
| Billing increment | A usage amount that incurs a cost (*meter*), such as a unit of time, number of instances, or size of data. |
| Billing factor  | The type of usage that incurs costs, such as time, storage amount, data transfer amount, or number of requests. |

## Key design strategies

Aligning resource usage to billing increments is about making sure that your resource consumption closely matches the intervals or quantities that you're charged for. For instance, if a service charges by the hour but you use it only for a fraction of that time, you can adjust operations to maximize the use of that hour.

To save money, ensure that you understand how you're billed for a service. You need to understand specific increments like hourly rates, per gigabyte charges, or per request costs. Adjust the service's configuration or how you consume the service to fit the billing increments and ensure that you don't incur unnecessary costs. Evaluate your workload's specific needs and understand how you're billed for various resources. Based on your findings, adjust the usage or the resource to optimize costs.

### Determine billing factors

Billing factors differ among services. Billing factors include the instance number, time, transaction rate, and transaction size. They also include availability zone, location, storage amount, ingress data, and egress data. Familiarize yourself with the pricing thresholds of the services that you use. You can align your usage to maximize the value of the resource and only run incur charges when necessary.

Here are some common billing factors:

- *Runtime:* The runtime refers to the duration that a resource actively runs or is utilized. Runtime is typically measured in hours, days, or months. The runtime helps you analyze the cost implications of resource usage over time. It's important for cost optimization because you can identify opportunities to minimize resource usage and associated costs.
- *Data transfer:* Data transfer refers to the movement of data into and out of a resource. Data transfer costs can vary based on the volume of data. Understand data transfer costs, so you can optimize data transfer patterns, select appropriate network configurations, and minimize costs associated with data movement.
- *Specialized services:* Specialized services are services or features that you use with other resources. These services can include specialized databases, AI services, or other advanced capabilities. Evaluate the cost implications of specialized services because they might have separate pricing models or incur extra charges.
- *Virtual CPUs (vCPUs):* The utilization of vCPUs within a resource is the vCPU usage. Resources such as virtual machines are often billed based on the number of vCPUs allocated to them. Monitor and optimize vCPU usage, so you can ensure efficient utilization of resources and minimize unnecessary costs.
- *Uptime guarantees:* Uptime guarantees refer to the service-level agreements (SLAs) that cloud providers offer on the availability and reliability of their services. Uptime guarantees aren't directly related to billing, but they're important to consider when you want to optimize costs. Higher uptime guarantees can coincide with higher costs. Evaluate the tradeoff between the cost and the service availability.

### Determine billing increments

Billing increments determine how resource usage is measured and billed. For each billing factor, there's a billing increment. Familiarize yourself with the billing increments of each service, so you can align resource usage to these billing increments.

Here are some common types of billing increments:

- *Time:** Resources are billed based on the duration of usage, such as per second, hour, or day.
- *Per request:* Some resources, particularly in serverless or event-driven architectures, are billed based on the number of requests or invocations. Minimize unnecessary requests and optimize the design of applications to reduce the number of billable requests.
- *Data transfer increments:* Data transfer costs are measured in increments, such as gigabytes (GB) or terabytes (TB).
- *Storage increments:* Storage costs are often measured in increments, such as GB or TB.

### Map usage to billing increments

Mapping usage to billing increments is an exercise to identify where resource consumption doesn't align with the billing increments. This mapping involves analyzing resource usage against billing increments in each billing factor to spot inefficiencies. At this step, you're only identifying areas where usage and billing increment aren't aligned. Later, you implement the changes. Consider the following guidance when mapping usage to billing increments:

- *Create an inventory of resources*. List the resources in the workload, such as compute, storage, and networking.
- *Understand usage patterns*. Use monitoring tools or past-usage data to identify the resource consumption patterns for the workload. Note periods of high and low usage.
- *Use pricing calculators*. Input the information that you gather into an online pricing calculator to get a detailed breakdown of costs, segmented by billing factors and increments.
- *Analyze billing increments*. If the calculator provides billing granularity for each component, align your actual or expected usage with the billing increments (hourly, daily, or per request).
- *Simulate scenarios*. Use the pricing calculator to simulate usage scenarios in order to understand how resource usage affects costs.

### Consider building a proof of concept (POC)

A proof of concept is a concrete way to validate your understanding of billing factors and billing increments. A POC helps you see the effects of design decisions on cost. It can help you refine your workload design to align with billing increments. A POC is important for leading cost drivers, such as the application platform and resources that scale.

If you're unsure about your billing knowledge or want to gain more confidence in understanding cost implications, a POC can provide a hands-on experience. You can validate your assumptions and test various scenarios to ensure that you have a clear understanding of the billing aspects. Consider the following guidance when you build a POC for cost optimization:

**Define POC scope:** Clearly define the scope of the POC, including the specific workload or application that you want to optimize for cost and the resources involved. Include factors such as usage time, usage patterns, per instance charges, data transfer, storage, compute, and any other cost-driving components. Consider billing increments when you delineate the scope to ensure that cost factors are thoroughly addressed.

**Emulate production:** Design the POC to emulate the production environment, ensuring realistic cost estimations. You should evaluate cost drivers, such as the effect of scalability, operational decisions (stopping and starting resources), and storage costs. Align the POC design with billing threshold knowledge to ensure that the simulated environment accurately reflects potential cost scenarios.

**Limit POC duration:** Limit the lifespan of the POC, so you can gather conclusive evidence, but you don't incur unnecessary costs. Extend the POC slightly beyond a billing threshold to guarantee a comprehensive understanding of costs. For instance, if a resource is billed hourly, the POC might run for just over an hour or however long it takes to capture how costs accrue at the threshold. After you have the corroborating evidence, you can confidently make decisions based on your findings. When the POC provides a clear picture of billing implications, use the findings to make informed financial decisions for the actual environment.

### Align usage to maximize resource value

Aligning usage to maximize resource value involves implementing the changes identified in the mapping exercise to realign resource usage with billing increments. This step is about making adjustments to how resources are consumed. There are two primary options for aligning usage to billing increments:

**Modify the service.** Modifying the services means using different configurations, service tiers, or services to align the workload to billing thresholds. For example, your workload might move 5 TB of data daily, but you’re charged in 4-TB increments. You can find a different service tier or configuration, so you can transfer the data at a cheaper or faster rate.

**Modify usage.** Modifying usage is about redesigning the usage pattern workload to align with a billing increment. For example, you can compress the 5-TB data to 4 TB before transferring. You can also extend the usage to the billing increment. For example, if you need to transfer 2 TB of data each day, you can modify the schedule to transfer 4 TBs of data every other day.

If neither option is feasible, you need to accept the extra cost. Rework the budgets as needed if the extra cost isn’t included in the budget.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Cost optimization decisions shouldn't compromise security requirements or compliance regulations. If you opt for cheaper solutions without adequate security measures, you can expose the workload to potential vulnerabilities.

## Azure facilitation

**Determining billing factors and increments**: Azure has product pricing details for every [Azure product](https://azure.microsoft.com/pricing/#product-pricing). Search for the products in your workload and catalog the different billing factors and increments for each billing factor. You can also use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) to estimate the cost of different increments.

**Mapping usage to billing increments**: You can use your [Azure bill](/azure/cost-management-billing/understand/review-individual-bill) to analyze resource usage patterns and identify areas of high consumption. You can [view and download your Azure invoice](/azure/cost-management-billing/understand/download-azure-invoice). These features help you understand how resources are utilized, so you can make informed decisions about optimizing their usage and minimizing unnecessary costs.

You can get a quick overview of your [invoiced usage and charges](/azure/cost-management-billing/understand/review-individual-bill) on the Subscriptions page in the Azure portal. It's important to [understand the terms in your Azure usage and charges file](/azure/cost-management-billing/understand/understand-usage).

**Aligning usage to maximize value**: [Microsoft Cost Management and Billing](/azure/cost-management-billing/cost-management-billing-overview) and Azure Advisor provide optimization recommendations that are based on usage and cost data. These recommendations help you identify opportunities for cost savings. With this data, you can determine if resources are over-provisioned or underutilized, and right-size them to match the workload requirements. Right-sizing resources can help align to billing increments.

Product SKUs represent the service tiers in Azure products. Azure offers various SKUs within each service. Switching SKUs can help you align billing increments with usage patterns. You can use the [Azure product pricing pages](https://azure.microsoft.com/pricing/#product-pricing) to compare the different tiers for each product.

With Azure, you can set up cost alerts and budgets. [Cost alerts](/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending) notify you when consumption reaches predefined thresholds, allowing you to proactively monitor spending. [Budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) help you set limits and track the burn rate of your resources, which helps ensure cost control.

## Next steps

- [Recommendations for collecting and reviewing cost data](collect-review-cost-data.md)
- [Recommendations for optimizing data costs](optimize-data-costs.md)

## Cost Optimization checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Cost Optimization checklist](checklist.md)
