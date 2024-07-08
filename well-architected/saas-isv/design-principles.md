---
title: Design principles of an ISV workload
description: Key design principles that serve as a compass for subsequent design decisions across technical domains and the critical design areas.
author: johndowns
ms.author: jodowns
ms.date: 07/02/2024
ms.topic: conceptual
---

# Design principles of an ISV workload

- ISVs are different from other users of the cloud because their solution *is* their business. This means that the solution's reliability and security are critical to the health of the company. If the workload is unavailable, the company can't generate revenue; if you experience a major security breach, your entire company might be at risk.
- At the same time, many ISVs - especially startups - are often resource-constrained. They might not have teams - or even people - dedicated to concerns like cloud architecture, governance, security, or deployment automation. It's common for everyone to be involved in everything. This is coupled with a heavy focus on minimizing all unnecessary costs. Aggressive tradeoffs often have to be made so that the company can get through its next phase of growth.
- Some ISVs grow rapidly, and the way that you design a solution at a low volume of scale is typically quite different to that at a high scale. This means that architectural flexibility is important. You won't get everything right upfront, so it's important to figure out how you can adapt as you go and grow. Don't expect your MVP architecture to scale indefinitely - plan to redesign as you grow.

## Reliability

|Design principle|Considerations|
|---|---|
|**Prioritize availability**|Your solution *is* your business, so you often need to be as highly available as possible. When an ISV has an outage, the effects are often exponential - their customers are affected too, and their customers in turn.|
|**Be explicit about SLAs that you offer your customers**|Base your SLA or SLO on composite SLAs for underlying Azure services. Don't make assumptions.|

## Performance Efficiency

|Design principle|Considerations|
|---|---|
|**Global scale enables global performance**|If you have customers globally, make sure to give them a good experience - multiregion deployments or accelerated traffic routing. Use stamps early to make this easier. Multiregion is good for other reasons too.|
|**Model expected scale**|Think about your best, average, and worst case for growth. Ask your sales team for their realistic projections. Plan your scaling strategies so that it's flexible enough to accommodate the range of possibilities.|
|**Understand scale points**||Where are you likely to flex? Common triggers are number of customers (tenants), number of users, transactions per user. Understand how they change as your business grows, and how changes in the values are reflected in your architecture.|
|**Design for scale-out**|Scaling up means you hit limits. Scaling out can be effectively unbounded - but not everything scales out. Consider deployment stamps for scaling your solution as a unit, which helps to avoid limits in scaling resources up.|

## Operational Excellence

|Design principle|Considerations|
|---|---|
|**Adopt consistency to enable your processes to scale**|Use deployment stamps, and drive consistency across stamp configuration and architecture. Automate or use standard processes.|
|**Formalize exceptions or differences**|Define different SKUs where customers have different needs. Avoid custom deployments, configuration, or code for different customers.|
|**Shared responsibility model**|Customer, ISV, and Microsoft have different responsibilities. Be clear on who is responsible for what.|

## Security

|Design principle|Considerations|
|---|---|
|**Security is an ongoing process**|You need to constantly re-evaluate your security posture, taking account of the evolving thread landscape, new capabilities and protocols, and new compliance/regulatory requirements.|
|**Identity is the cornerstone of modern security**|ISVs often have to manage their internal identities for their employees as well as customers. Both parts of identity are critical to the security of the solution. Prefer the use of a hosted identity solution over creating your own.|
|**Adopt governance early**|Governance is the foundation for good security. How you manage roles, organize resources, put policies in place, etc has a huge impact on your security posture. There's no point adding other security controls if poor governance means someone can just reverse those controls. It's much easier to adopt governance upfront than to remediate later.|
|**Follow a cloud security baseline from day 1**|Even if your customers today are relaxed, assume you will eventually get customers who want to audit you. Make your life easier  by doing things right from the start - it's not much work if you do it early and often.|
|**Use explicit ingress and egress points**|Avoid things like giving direct DB access to customers. Have defined integration points. Identify and protect all endpoints|
|**Use least privilege**|Default to a position of the least required permissions, both for human users and for workload identities. Review permissions regularly. Plan for just-in-time elevation for administrative permissions.|
|**Avoid credentials where possible, and protect them where you can't avoid them**|Treat credentials as a liability. Use managed identities, workload identity federation, and other techniques that minimize the credentials you need to store. Where it's impossible to avoid, protect them with secure cloud-native approaches.|

## Cost Optimization

|Design principle|Considerations|
|---|---|
|**Track your cost of goods sold (COGS)**|Similarly to manufacturing a physical product, you need to understand the cost of delivering your solution to your customers. Track how much each customer costs you relative to the revenue they bring in, to enable your decision-making. Adopt governance processes like good resource organization and tagging to help you quickly understand and aggregate your Azure spend.|
|**Track costs over time.**|Ensure that over time your costs don't begin to exceed your revenue, especially as your solution grows.|
|**Understand how your costs and revenue are related**|Don't want to get into a situation where your costs go up but revenue doesn't. e.g. if you add a new feature that offers unlimited free storage, that could cost you.|
|**Decide on the granularity of cost measurement**|If you need to charge customers based on consumption this is really important. If you don't, consider what is "just enough" measurement to approximate your cost apportionment.|

## Prioritization and tradeoffs

Be OK about making tradeoffs, but go in with full understanding of what you're doing. Take calculated risks. Example - startup has limited funding and timelines. You will have to make choices between two competing high priorities.
