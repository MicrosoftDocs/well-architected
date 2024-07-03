---
title: Design principles of an ISV workload
description: Key design principles that serve as a compass for subsequent design decisions across technical domains and the critical design areas.
author: johndowns
ms.author: jodowns
ms.date: 07/02/2024
ms.topic: conceptual
---

# Design principles of an ISV workload

- ISVs are different from other users of the cloud because their solution *is* their business. This means that the solution's reliability and security are critical to the health of the company. If the workload is unavailable, the company can't generate revenue.
- At the same time, many ISVs - especially startups - are often resource-constrained. They might not have teams - or even people - dedicated to concerns like cloud architecture, governance, security, or deployment automation. It's common for everyone to be involved in everything. This is coupled with a heavy focus on minimizing all unnecessary costs. Aggressive tradeoffs often have to be made so that the company can get through its next phase of growth.
- Some ISVs grow rapidly, and the way that you design a solution at a low volume of scale is typically quite different to that at a high scale. This means that architectural flexibility is important. You won't get everything right upfront, so it's important to figure out how you can adapt as you go and grow.

Maybe: don't expect your MVP architecture to scale indefinitely. Plan to redesign as you grow.

## Reliability

|Design principle|Considerations|
|---|---|
|**Prioritize availability**|Your solution *is* your business, so you often need to be as highly available as possible. When an ISV has an outage, the effects are often exponential - their customers are affected too, and their customers in turn.|
|**Be explicit about SLAs that you offer your customers**|...|

## Performance Efficiency

|Design principle|Considerations|
|---|---|
|**Global scale enables global performance**|If you have customers globally, make sure to give them a good experience - multiregion deployments or accelerated traffic routing. Use stamps early to make this easier. Multiregion is good for other reasons too.|
|**Model expected scale**|Talk to your sales leadership about their plans and understand the best, average, and worst case for growth. Plan your scaling strategies accordingly.| 
|**Understand scale points**||Where are you likely to flex? Common triggers are number of customers (tenants), number of users, transactions per user. Understand how they change as your business grows, and how changes in the values are reflected in your architecture.|
|**Design for scale-out**|Scaling up means you hit limits. Scaling out can be effectively unbounded - but not everything scales out.|

## Operational Excellence

|Design principle|Considerations|
|---|---|
|**Adopt consistency to enable your processes to scale**|Use stamps. Automate or use standard processes.|
|**Formalize exceptions or differences**|Define different SKUs where customers have different needs. Avoid custom deployments, configuration, or code for different customers.|
|**Shared responsibility model**|Customer, ISV, and Microsoft have different responsibilities. Be clear on who is responsible for what.|

## Security

|Design principle|Considerations|
|---|---|
|**Security is an ongoing process**|...|
|**Follow a cloud security baseline from day 1**|...| Even if your customers today are relaxed, assume you will eventually get customers who want to audit you. Make your life easier by doing |**things right from the start - it's not much work if you do it early and often.
|**Use least privilege**|...|
|**Avoid credentials where possible, and protect them where you can't avoid them**|...|
|**Identify and protect all endpoints**|...|

## Cost Optimization

|Design principle|Considerations|
|---|---|
|**Understand how your costs and revenue are related**|Don't want to get into a situation where your costs go up but revenue doesn't. e.g. if you offer unlimited free storage, that could cost you.|
|**Decide on the granularity of cost measurement**|If you need to charge customers based on consumption this is really important. If you don't, consider what is "just enough" measurement to approximate your cost apportionment.|

## Prioritization and tradeoffs

Be OK about making tradeoffs, but go in with full understanding of what you're doing. Take calculated risks.
