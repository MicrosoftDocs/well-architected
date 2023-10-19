---
title: Recommendations for aligning billing and usage
description: Learn how to align usage to billing increments.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for aligning resource usage to billing increments

**Applies to this Azure Well-Architected Framework Cost Optimization checklist recommendation:**

|[CO:06](checklist.md)| Align usage to billing increments. You should understand billing increments (meters) and align resource usage to those increments. Modify the service to align with billing increments, or modify resource usage to align with billing increments. Consider using a proof-of-concept to validate billing knowledge and design choices for major cost drivers and to reveal ways to align billing and resource usage.|
|---|---|

This guide describes the recommendations for aligning resource usage to billing increments. Resources are billed at specific increments, such as every hour or every instance. To optimizes costs, you need to align your usage to those increments. You must either adapt a resource to your workload usage or adapt your workload to the resource billing increments. Implement the following guidance so you can ensure that your workload derives the maximum value from each resource. If you fail to align billing and design, you might incur waste.

**Definitions**

| Term | Definition  |
|----|---|
| Billing increment | A usage amount, such as a unit of time, number of instances, or size of data, that incurs a cost. It's also known as a *meter*. |
| Billing factor  | The type of usage, such as time, storage amount, data transfer amount, or number of requests, that incurs costs.  |

## Key design strategies

Aligning resource usage to billing increments is about making sure that your resource consumption closely matches the intervals or quantities that you're charged for. For instance, if a service charges by the hour but you use it only for a fraction of that time, you can adjust operations to maximize the use of that hour as an alignment strategy.

To save money, ensure that you understand how you're billed for a service, especially for specific increments like hourly rates, per gigabyte, or per number of requests. Adjust the service's configuration or how you consume the service to fit the billing increments and ensure that you don't incur unnecessary costs. Evaluate your workload's specific needs and understand how you're billed for various resources. Based on your findings, adjust the usage or the resource to optimize costs.

### Determine billing factors

Various services have different billing factors. Billing factors include the instance number, time, transaction rate, transaction size, availability zone, location, storage amount, ingress data, and egress data. Familiarize yourself with the pricing models and cost structures of the services that you use. You can align your usage to maximize the value of the resource and only run its meter when necessary.

**Runtime:** The runtime refers to the duration that a resource actively runs or is utilized. It's typically measured in hours, days, or months. The runtime helps you analyze the cost implications of resource usage over time. It's important for cost optimization because you can identify opportunities to minimize resource usage and associated costs.

**Data transfer:** Data transfer refers to the movement of data into and out of a resource. Data transfer costs can vary based on the volume of data. Understand data transfer costs, so you can optimize data transfer patterns, select appropriate network configurations, and minimize costs associated with data movement.

**Specialized services:** Specialized services are services or features that you use with other resources. These services can include specialized databases, AI services, or other advanced capabilities. Evaluate the cost implications of specialized services because they might have separate pricing models or incur extra charges.

**Virtual CPUs (vCPUs):** The utilization of vCPUs within a resource is the vCPU usage. Resources such as virtual machines (VMs) are often billed based on the number of vCPUs allocated to them. Monitor and optimize vCPU usage, so you can ensure efficient utilization of resources and minimize unnecessary costs.

**Uptime guarantees:** Uptime guarantees refer to the service-level agreements (SLAs) that cloud providers offer regarding the availability and reliability of their services. Uptime guarantees aren't directly related to billing, but they're important to consider when you want to optimize costs. Higher uptime guarantees might coincide with higher costs. Evaluate the tradeoff between the cost and the service availability.

### Determine billing increments

Billing increments determine how resource usage is measured and billed. Familiarize yourself with billing increments, so you can estimate and optimize costs. Common billing increments include:

**Time:** Services can bill resources based on the duration of usage, such as per second, hour, or day.

**Per request:** Some resources, particularly in serverless or event-driven architectures, are billed based on the number of requests or invocations. Minimize unnecessary requests and optimize the design of applications to reduce the number of billable requests.

**Data transfer increments:** Data transfer costs are measured in increments, such as gigabytes (GB) or terabytes (TB).

**Storage increments:** Storage costs are often measured in increments such as GB or TB.

### Map usage to billing increments

Take advantage of online pricing calculators and total cost of ownership (TCO) calculators to get a granular summary of billing factors and increments. You can also use these tools to simulate scenarios without incurring costs.

- *Create an inventory of resources*: List the resources in the workload, such as compute, storage, and networking.

- *Understand usage patterns*: Use monitoring tools or past usage data to identify the resource consumption patterns for the workload. Note periods of high and low usage.
- *Use pricing calculators*: Input the information that you gather into an online pricing calculator to get a detailed breakdown of costs, segmented by billing factors and increments.
- *Analyze billing increments*: If the calculator provides billing granularity for each component, align your actual or expected usage with the billing increments (hourly, daily, or per request).
- *Simulate scenarios*: Use the pricing calculator to simulate usage scenarios in order to understand how resource usage affects costs.

### Consider building a proof of concept (POC)

To validate your understanding of billing increments, you can build a POC. It can help you refine your workload design to align with billing increments. A POC helps you understand how design decisions affect costs. A POC is important for cost drivers, such as application platform and resources that scale.

If you're unsure about your billing knowledge or want to gain more confidence in understanding the cost implications, a POC can provide a hands-on experience. You can validate your assumptions and test various scenarios to ensure that you have a clear understanding of the billing aspects. Consider the following guidance when you build a proof of concept for cost optimization.

**Scope definition:** Clearly define the scope of the proof of concept, including the specific workload or application that you want to optimize for cost and the resources involved. Include factors such as usage time, usage patterns, per instance charges, data transfer, storage, compute, and any other cost-driving components. Integrate billing increment knowledge into scope delineation to ensure cost factors are thoroughly addressed.

**POC design:** Design the POC to emulate a production environment, ensuring realistic cost estimations. You want to evaluate cost drivers, such as the effect of scalability, operational decisions (stopping and starting resources), and storage costs. Align the POC design with billing threshold knowledge, ensuring that the simulated environment accurately reflects potential cost scenarios.

**POC duration:** Limit the POC's lifespan, so you can gather conclusive evidence without incurring unnecessary costs. Extend the POC slightly beyond a billing threshold to guarantee a comprehensive understanding of costs. For instance, if billed hourly, the POC might run for just over an hour or however long it takes to capture how costs accrue at the threshold. After you have the corroborating evidence, you can confidently make decisions based on your findings. When the POC has a clear picture of billing implications, use the findings to make informed financial decisions for the actual environment.

### Align usage to maximize resource value

As you understand how resources are billed, you can align the workload design accordingly. You must understand the billing increments that are specific to each component. You have two primary options for cost optimization.

**Modify the service:** Modifying the services means using different configurations, service tiers, or services to align the workload to billing thresholds. For example, your workload might move 5 TB of data daily, but you’re charged in 4-TB increments. You can find a different service tier or configuration to transfer the data at a cheaper or faster rate.

**Modify the usage:** Modifying the usage is about redesigning the usage pattern workload to align with a billing increment. For example, you can compress the 5-TB data to 4 TB before transferring. You can also extend the usage to the billing increment. For example, if you need to transfer 2 TB of data every day, you can modify the schedule to transfer 4 TBs of data every other day.

If neither is feasible, then you need to accept the extra cost. Rework the budgets as needed if the extra cost wasn’t included.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Cost optimization decisions shouldn't compromise security requirements or compliance regulations. If you opt for cheaper solutions without adequate security measures, you can expose the workload to potential vulnerabilities.

## Azure facilitation

**SKUs.** SKUs represent different service tiers in Azure products. Azure offers different SKUs within each service. Switching SKUs can help you align billing increments with usage patterns.

**Usage analysis.** Azure billing increments, or meters, can be used to analyze resource usage patterns and identify areas of high consumption. By understanding how resources are being utilized, you can make informed decisions to optimize their usage and minimize unnecessary costs.

**Detailed cost analysis.** Microsoft Cost Management and Azure Advisor provide optimization recommendations based on usage and cost data, helping you identify opportunities for cost savings. This data can help you determine if resources are over-provisioned or underutilized, allowing you to right-size them to match the workload requirements. Right-sizing resources can help align to billing increments.

**Cost alerts and budgets.** Azure allows you to set up cost alerts and budgets. Cost alerts notify you when consumption reaches predefined thresholds, allowing you to proactively monitor spending. Budgets help you set limits and track the burn rate of your resources, ensuring cost control.

## Cost Optimization checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Cost Optimization checklist](checklist.md)