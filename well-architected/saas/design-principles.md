---
title: Design Principles of SaaS Workloads on Azure
description: Learn about the key design principles that serve as a compass for subsequent design decisions across technical domains and the critical design areas for SaaS.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/05/2025
ms.update-cycle: 1095-days
ms.topic: concept-article
ms.collection: learn-startups
---

# Design principles of SaaS workloads on Azure

As an independent software vendor (ISV) delivering a SaaS solution, **you're accountable for the solution's architectural excellence, and you share responsibility with your customer**. They rely on your solution, and problems can cascade to them. If your organization is mature and has an established customer base, reliability and security are likely your biggest concerns. Downtime and security breaches can have negative consequences for your company's revenue and reputation.

But many ISVs, especially startup ISVs, operate with limited resources to minimize costs. If your organization is in the startup phase, you might have to make aggressive tradeoffs to progress to the next phase of growth. You might not have dedicated teams for governance, security, or deployment automation, but remember to plan for future growth. If you have to take risks, make calculated decisions.

How you design a solution that operates at a low volume of scale differs from a high-scale solution. To support rapid growth, you should **design a SaaS workload architecture with flexibility and adaptability.** This article presents the underlying principles with consideration to that evolution of growth. **Consider all five Well-Architected Framework pillars together, including the tradeoffs.** There's a minimum standard across all pillars, so consider each one. If you don't apply these principles, you could introduce financial loss and reduce customer trust. 

## Reliability

|Design principle|Considerations|
|---|---|
|**Prioritize availability.**|Your solution *is* your business. Maintain high availability as much as practical. If your solution experiences an outage, the impact can affect not only your customers but also their customers.|
|**Be explicit about service-level agreements (SLAs) that you offer your customers.**|When you create financially backed SLAs for your customers, make sure that you can meet them and that the components you depend on are compatible with them. Review the composite SLAs for underlying Azure services as part of your SLA design process. Don't make assumptions. Reflect your shared responsibility model in your SLAs.|

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Reliability and cost.** To achieve high reliability, you often need to deploy extra resources. For example, you might distribute resources across multiple availability zones or regions. Some Azure services offer built-in geo-replication or inter-zone replication, but these features often come with high costs.
>
> Make informed decisions about the level of resiliency that your budget permits. If the reliability of some components or flows in the workload don't have financial implications, consider low-cost opportunities to improve your resiliency. For example, you can use availability zones of platform services, regularly back up data to another physical location, and use infrastructure as code (IaC) to quickly redeploy resources during recovery processes.

## Security

|Design principle|Considerations|
|---|---|
|**Establish governance as the foundation for security.**|Establish good governance practices from the start rather than addressing problems later. Many factors affect security, such as how you manage roles, organize resources, and implement policies. Without robust governance, security controls don't protect the system. |
|**Follow a cloud security baseline from day one.**|Expect security audits from your customers. Incorporate audit trails early in your design. |
|**Isolate your customers, and isolate your segments.**|Use your tenancy model as a strategy for data isolation. Segment your deployments and environments. |
|**Start with Zero Trust, and provide the least possible access.**|Default to a position of no access. Introduce minimum access only when necessary. Use this strategy for identity management and network traffic. Regularly review flows through the system, and take action on anomalies. |
|**Avoid credentials where possible. If you must use credentials, protect them.**|Treat credentials as a liability. Use reputable identity providers and techniques that minimize credential storage. When unavoidable, protect credentials with secure cloud-native approaches. Handle customer credentials and secrets with the utmost care. |
|**Adopt security as an ongoing process.**|Continuously reevaluate your security posture. Consider the evolving threat landscape, new capabilities and protocols, and updated compliance or regulatory requirements. |

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Security and cost optimization.** Designing and operating secure solutions are expensive. But you can achieve significant steps towards security, such as good governance and adhering to a security baseline, with minimal to no expense. Determine the balance between cost efficiency and your ideal security posture.

## Cost optimization

|Design principle|Considerations|
|---|---|
|**Pay only for what you need.**|Take advantage of Microsoft Cost Management features to understand your overall spend. Prioritize the most expensive resource categories for further review. Identify areas where you might overspend.|
|**Use what you pay for.**|Maximize the value from resources that you pay for but that you might underuse. |
|**Model your costs.**|Track your cost of goods sold. Understand the cost to deliver your solution to customers. This process is similar to manufacturing a physical product. To inform your decision making, monitor the cost for each customer relative to the revenue that they generate. To quickly understand and aggregate your Azure spend, implement governance processes, like good resource organization and tagging. |
|**Understand how your costs and revenue are related.**|Avoid situations where costs increase without a corresponding rise in revenue. For example, costs might increase if you add a new feature that offers unlimited free storage. Similarly, if you charge customers based on the number of users, ensure that you tie functionality to the users. |

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Cost optimization and reliability.** To create reliable solutions, you often need to deploy extra components, transfer more data, and use more resilient key-component SKUs, all of which increase costs. The added reliability is often worth the expense, but you must make informed decisions. To offset these cost increases, ensure that you use other components effectively and maximize their value.

## Operational excellence

|Design principle|Considerations|
|---|---|
|**Understand the shared responsibility model.**|Clearly define the responsibilities of your cloud provider, your customers, and your organization. Ensure that everyone knows who is responsible for which tasks.|
|**Prepare to operate a solution on behalf of your customers.**|Set up your organization, team, processes, and tools to support operating SaaS at scale.|
|**Adopt consistent processes.**|Use deployment stamps. Drive consistency across the stamp configuration and architecture. Automate or standardize processes. |
|**Formalize exceptions or differences.**|Define different SKUs to meet varying needs. Use this method to avoid custom deployments, configurations, or code for different customers.|
|**Roll out changes safely.**|Implement a safe deployment process that you can use for progressive exposure, constant monitoring, and rollback if problems arise. Use a consistent process for code, infrastructure, and configuration changes. Control the number of solution versions that you deploy at any given time.|

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Operational excellence and the cost of complexity.** Automated management operations can increase your solution's complexity and take time to build. Initially, the cost of automation might outweigh the benefits, especially with a small customer base. But as the number of customers grows, the cost of automation pays off, and the benefits increase.

## Performance efficiency

|Design principle|Considerations|
|---|---|
|**Implement global scale to enable global performance.**|Provide a good experience for global customers through multiregion deployments or accelerated traffic routing.|
|**Quantify your expected scale.**|Model best, average, and worst-case growth scenarios. Analyze trends and consult your sales team for realistic projections. Plan flexible scaling strategies to accommodate various growth possibilities. |
|**Understand scale points.**|Identify where you likely need flexibility. Common triggers include the number of customers or tenants, users, and transactions per user. Understand how these factors change as your business grows and how they affect your architecture.|
|**Design for scale out.**|Scaling up has limits, but scaling out allows for greater expansion. Not everything scales out, so consider using deployment stamps to scale your solution as a unit to avoid the limitations of scaling up resources.|

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Performance efficiency and reliability.** Reliable systems often require data replication across wide geographic areas. Depending on your replication design, this setup can lead to higher latency and lower throughput. For instance, if you synchronously replicate important data between two Azure regions that are several hundred miles apart, you can add hundreds of milliseconds to your response times because of real-time replication.

## Next step

Start your learning journey by optimizing your customer's billing and your cost management strategy.

> [!div class="nextstepaction"]
> [Design area: Billing and cost management for SaaS workloads on Azure](./billing-cost-management.md)
