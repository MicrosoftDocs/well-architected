---
title: Design principles of an ISV workload
description: Key design principles that serve as a compass for subsequent design decisions across technical domains and the critical design areas.
author: johndowns
ms.author: jodowns
ms.date: 07/02/2024
ms.topic: conceptual
---

# Design principles of an ISV workload

- Need to consider all five WAF pillars together, including the tradeoffs. There is going to be a minimum bar across all five. Don't ignore any of them.
- ISVs are different from other users of the cloud because their solution *is* their business. This means that the solution's reliability and security are critical to the health of the company. If the workload is unavailable, the company can't generate revenue; if you experience a major security breach, your entire company might be at risk.
- At the same time, many ISVs - especially startups - are often resource-constrained. They might not have teams - or even people - dedicated to concerns like cloud architecture, governance, security, or deployment automation. It's common for everyone to be involved in everything. This is coupled with a heavy focus on minimizing all unnecessary costs. Aggressive tradeoffs often have to be made so that the company can get through its next phase of growth. If you have to take risks, make sure they're calculated risks.
- Some ISVs grow rapidly, and the way that you design a solution at a low volume of scale is typically quite different to that at a high scale. This means that architectural flexibility is important. You won't get everything right upfront, so it's important to figure out how you can adapt as you go and grow. Don't expect your MVP architecture to scale indefinitely - plan to redesign as you grow.

If you don't apply these principles to your design, your business could be at stake. As an ISV, your customers depend on your solution and any problems with your solution can cascade to your customers. Expect to see **significant financial impact**, or **reputational impact from a loss of trust**.

## Reliability

|Design principle|Considerations|
|---|---|
|**Prioritize availability**|Your solution *is* your business, so you often need to be as highly available as possible. When an ISV has an outage, the effects are often exponential - their customers are affected too, and their customers in turn.|
|**Be explicit about SLAs that you offer your customers**|When you create financially backed SLAs for your customers, make sure you can meet them and that the components you depend on aren't incompatible with them. Review the composite SLAs for underlying Azure services as part of your SLA design process. Don't make assumptions. Reflect your shared responsibility model in your SLAs.|

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Reliability and cost.** Achieving higher reliability often requires deployment of more resources, such as by distributing resources across multiple availability zones or regions. Alternatively, some Azure services provide built-in geo-replication or inter-zone replication, but these features often require more costly SKUs. You might need to make an informed decision on the level of resiliency that your budget permits. If you don't need to meet strict SLAs, look for low-cost opportunities to increase your resiliency posture, such as using availability zones where you can, by backing up data regularly to another physical location, and by using infrastructure as code to quickly redeploy resources during your recovery processes.

## Performance Efficiency

|Design principle|Considerations|
|---|---|
|**Global scale enables global performance**|If you have customers globally, make sure to give them a good experience - multiregion deployments or accelerated traffic routing. Use stamps early to make this easier. Multiregion is good for other reasons too.|
|**Quantify your expected scale**|Model your best, average, and worst case for growth. Look at your trends and ask your sales team for realistic projections. Plan your scaling strategies so that it's flexible enough to accommodate the range of possibilities.|
|**Understand scale points**|Where are you likely to flex? Common triggers are number of customers (tenants), number of users, transactions per user. Understand how they change as your business grows, and how changes in the values are reflected in your architecture.|
|**Design for scale-out**|Scaling up means you hit limits. If you scale out, you can go much further - but not everything scales out. Consider deployment stamps for scaling your solution as a unit, which helps to avoid limits in scaling resources up.|

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Performance efficiency and reliability.** More reliable systems sometimes require replication of data across a wider geographic area. Depending on how you design your replication process, it can result in higher latency and lower throughput. For example, you might replicate important data between two Azure regions that are several hundred miles apart, and any real-time replication of data might add hundreds of milliseconds to your response times.

## Operational Excellence

|Design principle|Considerations|
|---|---|
|**Shared responsibility model**|Customer, ISV, and Microsoft have different responsibilities. Be clear on who is responsible for what.|
|**Adopt consistency to enable your processes to scale**|Use deployment stamps, and drive consistency across stamp configuration and architecture. Automate or use standard processes.|
|**Formalize exceptions or differences**|Define different SKUs where customers have different needs. Avoid custom deployments, configuration, or code for different customers.|
|**Safely roll out changes**|Use a safe deployment process that enables progressive rollout of changes, with constant monitoring and rollback if a problem is discovered. Use a consistent process for code changes, infrastructure changes, and configuration changes. Control how many versions of your solution are deployed at one time.|

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Operational excellence and complexity.** The automation of management operations can increase your solution's complexity and take time to build. At first, the cost of automation might outweigh the advantages, especially when an ISV has only a small number of customers. However, as an ISV grows to a larger numbers of customers, the cost of automation will be amortized and the benefits will increase.

## Security

|Design principle|Considerations|
|---|---|
|**Governance as the foundation for security**|How you manage roles, organize resources, put policies in place, etc has a huge impact on your security posture. There's no point adding other security controls if poor governance means someone can just reverse those controls. It's much easier to adopt governance upfront than to remediate later.|
|**Follow a cloud security baseline from day 1**|Even if your customers today are relaxed, assume you will eventually get customers who want to audit you. Make your life easier  by doing things right from the start - it's not much work if you do it early and often.|
|**Identity is the cornerstone of modern security**|ISVs often have to manage their internal identities for their employees as well as customers. Both parts of identity are critical to the security of the solution. Prefer the use of a hosted identity solution over creating your own.|
|**Isolate your customers and isolate your segments**|Use your tenancy model to plan how you'll isolate data. Segment your deployments and environments.|
|**Defined ingress and egress points**|Avoid things like giving direct DB access to customers. Have defined integration points. Identify and protect all endpoints|
|**Start with zero trust, and adopt least privilege where necessary**|Default to a position of no access. Introduce the minimum least required permissions, both for human users and for workload identities. Review permissions regularly. Plan for just-in-time elevation for administrative permissions.|
|**Avoid credentials where possible, and protect them where you can't avoid them**|Treat credentials as a liability. Use managed identities, workload identity federation, and other techniques that minimize the credentials you need to store. Where it's impossible to avoid, protect them with secure cloud-native approaches. Handle customer credentials and secrets with the utmost care.|
|**Security as an ongoing process**|You need to constantly re-evaluate your security posture, taking account of the evolving thread landscape, new capabilities and protocols, and new compliance/regulatory requirements.|

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Security and cost optimization.** Secure solutions often cost more to design and operate. However, some of the most meaningful steps to a secure solution, like good governance and following a security baseline, can be achieved with little to no cost. You can decide where to draw the line between cost efficiency and your ideal security posture.

## Cost Optimization

|Design principle|Considerations|
|---|---|
|**Pay only for what you need**|Use Azure's cost management features to understand your overall spend and prioritize the most expensive categories of resources for further review. Look for places where you might be overspending.|
|**Use what you pay for**|Find ways to get more value from resources that you already pay for but that are underutilized.|
|**Model your costs**|Track your cost of goods sold (COGS): similarly to manufacturing a physical product, you need to understand the cost of delivering your solution to your customers. Track how much each customer costs you relative to the revenue they bring in, to enable your decision-making. Adopt governance processes like good resource organization and tagging to help you quickly understand and aggregate your Azure spend.|
|**Understand how your costs and revenue are related**|Don't want to accidentally get into a situation where your costs go up but revenue doesn't. e.g. if you add a new feature that offers unlimited free storage, that could cost you; similarly if you charge based on users/seats and have some functionality that isn't tied to users|
|**Decide on the granularity of cost measurement**|If you need to charge customers based on consumption this is really important. If you don't, consider what is "just enough" measurement to approximate your cost apportionment.|

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Cost optimization and reliability.** More reliable solutions often require more components to be deployed, more data to be transferred, and more resilient SKUs of key components. Each of these elements increases the cost of your solution. In many situations, reliability is worth the increase in cost - but you need to make an informed decision, and you should identify ways to offset any cost increases by ensuring that your other components are used effectively and that you're getting the most value you can out of them.
