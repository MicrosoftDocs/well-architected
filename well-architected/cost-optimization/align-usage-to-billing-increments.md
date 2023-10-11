---
title: Recommendations for aligning billing and usage
description: Learn how to align usage to billing increments.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for aligning usage to billing increments: CO 06

This guide describes the recommendations for aligning resource usage to billing increments. Aligning billing with workload refers to the alignment of resource usage and billing increments. Resources are billed at specific increments, such as per hour or per instance. You need to align your usage to those increments for cost optimization. For example, if a workload is designed to run for 1.2 hours but the billing model rounds up to the nearest hour, you pay for two hours of usage. It’s almost a full hour of unused resources. You must either adapt the resource to workload usage or adapt the workload to the resource billing increments. Following this guidance helps ensure the workload derives maximum value from each resource. Failing to align billing and design introduces waste.

**Definitions**

| Term | Definition  |
|----|---|
| Billing increment | A usage amount that incurs a cost, such as units of time (per minute or hour), number of instances, data size (per GB or TB). Also known as a meter. |
| Billing factors  | The types of usage that incur costs, such as time, storage amount, data transfer amount, number of requests.                     |

## Key strategies

You can save a significant amount by aligning your actual usage with the billing increments and factors that your service provider employs. Aim to minimize costs by using resources only when essential. Be cautious of choices that could lead to unnecessary expenses. Costs can arise from factors like time, the number of transactions, data size, regional boundaries, storage use, and reaching certain thresholds. For instance, just deploying a new instance of a service might result in a charge. Sometimes, starting and stopping a service may cost more than letting it run. You need to understand the workload's specific needs and familiarize yourself with how different resources bill you. Based on your findings, adjust the usage or the resource to optimize cost.

### Determine billing factors and increments

Different services have different billing factors. Billing factors include instance number, time, transactions rate, transaction size, number of availability zones, location, storage amount, ingress data, and egress data. Familiarize yourself with the pricing models and cost structures of the services you plan to use. This understanding helps you align your usage to maximize the value of the resource while only running its meter when necessary.

- *Run time*: Run time refers to the duration for which a resource is actively running or being utilized. It's typically measured in hours, days, or months. Understanding the run time factor helps in analyzing the cost implications of resource usage over different time periods. It's important for cost optimization as it allows you to identify opportunities to minimize resource usage and associated costs.
- *Data transfer*: Data transfer refers to the movement of data into and out of a resource. It includes inbound and outbound data transfer. Data transfer costs can vary based on the volume of data transferred. Understanding data transfer costs helps in optimizing data transfer patterns, selecting appropriate network configurations, and minimizing costs associated with data movement.
- *Specialized services*: Specialized services are other services or features that may be used with other resources. These services can include specialized databases, AI services, or other advanced capabilities. It's important to understand the cost implications of using specialized services as they may have separate pricing models or extra charges associated with them.
- *vCPUs*: vCPU usage represents the utilization of virtual CPUs (vCPUs) within a resource. It's an important billing factor because resources such as virtual machines (VMs) are often billed based on the number of vCPUs allocated to them. By monitoring and optimizing vCPU usage, you can ensure efficient utilization of resources and minimize unnecessary costs.
- *Uptime guarantees*: Uptime guarantees refer to the service level agreements (SLAs) provided by cloud providers regarding the availability and reliability of their services. While uptime guarantees aren't directly related to billing, they're important to consider when optimizing costs. Higher uptime guarantees may come with higher costs, so it's essential to evaluate the tradeoff between cost and service availability.

Billing increments determine how resource usage is measured and billed. Here are some common billing increments:

- *Time*: Resources can be billed based on the duration of usage, such as per second, hour, day, or longer. Understanding the billing increment helps in estimating and optimizing costs based on the expected duration of resource usage.
- *Per-request*: Some resources, particularly in serverless or event-driven architectures, may be billed based on the number of requests or invocations. Understanding per-request billing helps in optimizing resource usage by minimizing unnecessary requests and optimizing the design of applications to reduce the number of billable requests.
- *Data transfer increments*: Data transfer costs can be measured in increments such as gigabytes (GB) or terabytes (TB). Understanding the data transfer increment helps in estimating and optimizing costs based on the volume of data being transferred.
- *Storage increments*: Storage costs are often measured in increments such as gigabytes (GB) or terabytes (TB). Understanding the storage increment helps in estimating and optimizing costs based on the amount of storage required for the workload.

### Map usage to billing increments

By utilizing online pricing and TCO calculators, you can gain a granular understanding of billing factors and increments. It allows you to optimize your resource usage in a cost-effective manner. They provide immediate clarity on billing components and let you simulate different scenarios without incurring costs.

- *Inventory resources*: List the resources that are a part of this workload, such as compute, storage, networking, etc.
- *Understand usage patterns*: Utilize monitoring tools or past usage data to identify the workload's resource consumption patterns. Note periods of high and low usage.
- *Use pricing calculators*: Input the information gathered into an online pricing calculator to get a detailed breakdown of costs, segmented by billing factors and increments.
- *Analyze billing increments*: Use the calculator’s output to understand the billing granularity for each component. If the calculator's output provides this data, align your actual or expected usage with the billing increments (hourly, daily, per request, etc.).
- *Simulate scenarios*: Simulate different usage scenarios using the pricing calculator to understand how varying levels of resource usage could impact costs.

### Consider building a proof-of-concept

To validate your understanding of billing increments, you can build a proof of concept validates understanding of billing increments and can help you refine workload design to align with those increments. A POC helps you understand how different design decisions impact the costs incurred. A POC is most important for significant cost drivers, such as your application platform and resources that scale. If you're unsure about your billing knowledge or want to gain more confidence in understanding the cost implications, a POC can provide hands-on experience. It allows you to validate your assumptions and test different scenarios to ensure you have a clear understanding of the billing aspects. Here's how you can approach building a proof of concept for cost optimization:

- *Scope definition:* Clearly define the scope of the proof of concept, including the specific workload or application you want to optimize for cost and the relevant resources involved. It includes factors such as usage time, usage patterns, per instance charges, data transfer, storage, compute, and any other cost-driving components. Integrate billing increment knowledge into scope delineation to ensure cost factors are thoroughly addressed.
*POC design:* Design the POC to emulate a production environment, ensuring realistic cost estimations. You want to evaluate cost drivers, such as the impact of scalability, operational decisions (stopping and starting resources), and storage costs. Align POC design with billing threshold knowledge, ensuring that the simulated environment accurately reflects potential cost scenarios.

- *POC duration:* Limit the POC's lifespan to gather conclusive evidence without incurring unnecessary costs. Extend the POC slightly beyond a billing threshold to guarantee a comprehensive understanding of costs. For instance, if billed hourly, the POC might run for just over an hour or however long it takes to capture how costs accrue at the threshold. Once you have the corroborating evidence, you can confidently make decisions based on your findings. Once the POC offers a clear picture of billing implications, use the findings to make informed financial decisions for the actual environment.

### Align usage to maximize resource value

As you understand how resources are billed, you can align the workload design accordingly. You must understand the billing increments specific to each component. You have two primary options for cost optimization.

- *Modify the service:* Modifying the services means using different configurations, service tiers, or services to align the workload to billing thresholds. For example, your workload might move 5 TB of data daily, but you’re charged in 4-TB increments. You can find a different service tier or configuration to transfer the data at a cheaper or faster rate.
- *Modify the usage:* Modifying the usage means redesigning the usage pattern workload to align with a billing increment. For example, you can compress the 5TB data to 4 TB before transferring. You can also extend the usage to the billing increment. For example, if you need to transfer 2 TBs of data every day, you could modify the schedule to transfer 4 TB of data every other day.

If neither is feasible, then you need to accept the extra cost. Rework the budgets as needed if the extra cost wasn’t included.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Cost optimization decisions shouldn't compromise security requirements or compliance regulations. Opting for cheaper solutions without adequate security measures can expose the workload to potential vulnerabilities.

## Azure facilitation

**Stock keeping unit (SKUs).** SKUs represent different service tiers in Azure products. Azure offers different SKUs within each service. Switching SKUs can help you align billing increments with usage patterns.

**Usage analysis.** Azure billing increments (meters) can be used to analyze resource usage patterns and identify areas of high consumption. By understanding how resources are being utilized, you can make informed decisions to optimize their usage and minimize unnecessary costs.

**Detailed cost analysis.** Azure Cost Management and Azure Advisor provide optimization recommendations based on usage and cost data, helping you identify opportunities for cost savings. This data can help you determine if resources are over-provisioned or underutilized, allowing you to right-size them to match the workload requirements. Right-sizing resources can help align to billing increments.

**Cost alerts and budgets.** Azure allows you to set up cost alerts and budgets. Cost alerts notify you when consumption reaches predefined thresholds, allowing you to proactively monitor spending. Budgets help you set limits and track the burn rate of your resources, ensuring cost control.
