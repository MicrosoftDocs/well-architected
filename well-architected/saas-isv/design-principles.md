---
title: Design principles of an ISV workload
description: Key design principles that serve as a compass for subsequent design decisions across technical domains and the critical design areas for SaaS.
author: johndowns
ms.author: jodowns
ms.date: 07/02/2024
ms.topic: conceptual
---

# Design principles of an ISV workload

[!INCLUDE [header_file](includes/temporary-warning.md)]

Independent Software Vendors (ISVs) have a unique position in the cloud ecosystem, where their SaaS solutions drive their business. Their products are sold to businesses, also known as business-to-business (B2B) or to consumers, also known as business-to-consumer (B2C). When ISVs build software as a service (SaaS), the ISV is typically responsible for hosting and maintaining the solutions, while their customers configure the product and manage their data.

As the workload owner in the ISV space, **you are accountable for the solution's architectural excellence and also have shared responsibility with your customer**. They rely on your solution, and any issues can cascade to them.
If your organization is mature and has an established customer base, reliability and security are mostly likely your biggest concerns. Any downtime or security breaches can have negative consequences for your company's revenue and reputation.

However, many ISVs, especially startups, operate with limited resources keeping the focus on minimizing costs. If your organization is in this phase, you might have to make aggressive tradeoffs to progress to the next phase of growth. While there might not be dedicated teams for governance, security, or deployment automation today, plan for future growth. If you have to take risks, make sure they're calculated.

A solution operating at a low volume of scale is designed quite different to that at a high scale. To support rapid growth, a SaaS workload **architecture should be designed with flexibility and adaptability**. This article presents the underlying principles, keeping in mind that evolution. **Consider all five WAF pillars together, including the tradeoffs.** There will be a minimum standard across all pillars, so don't ignore any of them. Failing to apply these principles could lead to significant financial impact and loss of trust from your customers. 

 
## Reliability

|Design principle|Considerations|
|---|---|
|**Prioritize availability**|Your solution *is* your business. Maintain high availability, as much as practical. If your solution experiences an outage, the impact can be exponential, affecting not only your customers but also their customers.|
|**Be explicit about SLAs that you offer your customers**|When you create financially backed SLAs for your customers, make sure you can meet them and that the components you depend on aren't incompatible with them. Review the composite SLAs for underlying Azure services as part of your SLA design process. Don't make assumptions. Reflect your shared responsibility model in your SLAs.|


> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Reliability and cost.** Achieving higher reliability often requires deploying more resources, such as distributing them across multiple availability zones or regions. Some Azure services offer built-in geo-replication or inter-zone replication, but these features often come with higher costs. Make informed decisions about what level of resiliency you want to support that your budget permits. If the reliability of some components or flows in the workload don't have financial implications, consider low-cost opportunities to improve your resiliency. For example, using availability zones of platform services, regularly backing up data to another physical location, and using Infrastructure-as-Code (IaC) to quickly redeploy resources during recovery processes.

## Security

|Design principle|Considerations|
|---|---|
|**Establish governance as the foundation for security**|Establish good governance practices from the start rather than addressing issues later. How you manage roles, organize resources, and implement policies significantly impacts your security. Without robust governance, security controls are ineffective in protecting the system. |
|**Follow a cloud security baseline from day 1**|Assume security audits from your customers. Incorporate the practice of audit trails early in your design. |
|**Isolate your customers, isolate your segments**|Use your tenancy model as a strategy for data isolation. Segment your deployments and environments. |
|**Start with zero trust, give least access, where necessary**|Default to a position of no access. Introduce minimum access, only when needed. Use this strategy for identity management and network traffic. Regularly review flows through the system and take action on anomalies. |
|**Avoid credentials where possible, protect them where you can't avoid them**|Treat credentials as a liability. Use reputable Identity Providers (IdPs) and techniques that minimize credential storage. When unavoidable, protect them with secure cloud-native approaches. Handle customer credentials and secrets with the utmost care. |
|**Adopt security as an ongoing process**|Continuously re-evaluate your security posture, considering the evolving threat landscape, new capabilities and protocols, and updated compliance/regulatory requirements. |

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Security and cost optimization.** Designing and operating secure solutions are expensive. However, significant steps towards security, such as good governance and adhering to a security baseline, can be achieved with minimal to no expense. You can determine the balance between cost efficiency and your ideal security posture.

## Cost Optimization

|Design principle|Considerations|
|---|---|
|**Pay only for what you need**|Take advantage of Azure cost management features to understand your overall spend. Prioritize the most expensive resource categories for further review. Identify areas where you might be overspending.|
|**Use what you pay for**|Maximize the value from resources you already pay for but are underutilized. |
|**Model your costs**|Track your cost of goods sold (COGS). Similar to manufacturing a physical product, understand the cost of delivering your solution to customers. Monitor how much each customer costs relative to the revenue they generate to inform your decision-making. Implement governance processes like good resource organization and tagging to quickly understand and aggregate your Azure spend. |
|**Understand how your costs and revenue are related**|Avoid situations where costs increase without a corresponding rise in revenue. For example, adding a new feature that offers unlimited free storage could be costly; similarly, if you charge based on users/seats, ensure functionality is tied to users. |

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Cost optimization and reliability.** More reliable solutions often need deploying additional components, transferring more data, and using more resilient SKUs of key components, all of which increase costs. While the added reliability is often worth the expense, it's crucial to make informed decisions. Identify ways to offset these cost increases by ensuring other components are used effectively and maximizing their value.

## Operational Excellence

|Design principle|Considerations|
|---|---|
|**Understand the shared responsibility model**|Clearly define the responsibilities of your cloud provider, your customers, and your organization. Ensure everyone knows who is responsible for what.|
|**Adopt consistency in processes**|Use deployment stamps, and drive consistency across stamp configuration and architecture. Automate or use standard processes. |
|**Formalize exceptions or differences**|Define different SKUs to meet varying needs. Avoid custom deployments, configurations, or code for different customers.|
|**Safely roll out changes**|Implement a safe deployment process that allows for progressive exposure, constant monitoring, and rollback if issues arise. Use a consistent process for code, infrastructure, and configuration changes. Control the number of solution versions deployed at any given time.|

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Operational excellence and the cost of complexity.** Automating management operations can increase your solution's complexity and take time to build. Initially, the cost of automation might outweigh the benefits, especially with a small customer base. However, as the number of customers grows, the cost of automation will be amortized, and the benefits will increase.

## Performance Efficiency

|Design principle|Considerations|
|---|---|
|**Global scale enables global performance**|Provide a good experience for global customers through multiregion deployments or accelerated traffic routing.|
|**Quantify your expected scale**|Model best, average, and worst-case growth scenarios. Analyze trends and consult your sales team for realistic projections. Plan flexible scaling strategies to accommodate various growth possibilities. |
|**Understand scale points**|Identify where you are likely to need flexibility. Common triggers include the number of customers (tenants), users, and transactions per user. Understand how these factors change as your business grows and how they impact your architecture.|
|**Design for scale-out**|Scaling up has limits, but scaling out allows for greater expansion. Not everything scales out, so consider using deployment stamps to scale your solution as a unit, avoiding the limitations of scaling resources up.|

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Performance efficiency and reliability.** More reliable systems often require data replication across wider geographic areas. Depending on your replication design, this can lead to higher latency and lower throughput. For instance, synchronously replicating important data between two Azure regions several hundred miles apart can add hundreds of milliseconds to your response times due to real-time replication.










