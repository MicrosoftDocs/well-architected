---
title: Design methodology for ISV workloads on Azure 
description: Learn about adopting a structured approach when making architectural design decisions for a SaaS workload.
author: landonpierce 
ms.author: landonpierce 
ms.date: 07/02/2024
ms.topic: conceptual
---

Independent Software Vendors (ISVs) must carefully plan the requirements of their SaaS solution, given that the solution *is their business*. Customers of the business are direct users of the solution, whether that's other businesses or consumers. This business model sets higher expectations because as a architect, you need to consider your workload requirements _and_ the customer's requirements.

This article presents a design methodology to help you systematically define and refine requirements. With many design decisions and technology options to consider, if you feel uncertain, revisit this methodology to stay on track with the business requirements.

Keep in mind that building a SaaS workload is an iterative process that requires _flexibility_ to adapt to evolving markets and customer needs. This structure can help you collaborate with marketing and sales teams to justify technical decisions while evaluating customer feedback to drive the improvements.

**Diagram depicting this iterative process**

## 1 - Design for your business model 

Have a clear understanding how your business requirements will affect your solution downstream. While this list isn't exhaustive, here are some decision points to consider: 

- The _location where the resources are deployed_ will influence the architecture patterns that are feasible. They can be fully deployed in your Azure subscriptions, or customers can purchase the solution and deploy it in their own Azure subscriptions. Alternatively, the workload can use resources deployed in customers' Azure subscriptions. 

  For example, if you choose to deploy your software completely in the customer's environment, an architecture pattern based on only shared resources isn't feasible because each customer would have their own standalone environment with dedicated resources.

  For information about the deployment models, see [ISV deployment models](/azure/cloud-adoption-framework/ready/landing-zone/isv-landing-zone?#isv-deployment-models).
  
- Your _pricing model_ determines your business's revenue, which in turn affects your allowable COGS (cost of goods sold). This has a direct impact on your technical architecture.

  For more information, see [Pricing model](/azure/architecture/guide/multitenant/considerations/pricing-models).
  
- The features or SKUs you offer is an influencing factor. Selecting specific features over others may warrant changes or additions to your technical architecture. Offering different SKUs to various customers can also lead to a more complex architecture to support these variations.

- 
## 2 - Design for your customer requirements

It's important to design your solution with your customer requirements in mind.  Customers may have additional requirements, forming a _superset_ that your solution must meet. The superset requirements may not always conflict with your business or other customer requirements, but it's possible. For cases, where these requirements differ from your business requirements or put additional restrictions, decision-making can be challenging. 

For example, while your solution might adhere to your security standards, a customer might have stricter security requirements that you need to fulfill to secure their business. 

Your architecture should be flexible enough to accommodate these extra requirements. Try to integrage customer requirements in your overall business model, if your your requirements aren't significantly impacted. Always calculate the cost of those adjustments. If a customer's unique requirements incur additional costs, consider charging accordingly.

Ensure you have realistic [reliability targets](/azure/well-architected/reliability/metrics) that meet customer expectations and that you design your architecture to meet them.


## 3 - Design your tenancy model

Most SaaS solutions rely on multitenancy as the primary techincal strategy to maximize cost efficiency. Multitenancy involves a range of choices without standard patterns. Your tenancy model impacts various aspects of your architecture, including management overhead, cost, and data isolation. Finding the right balance for your solution is important. Also, the choice of tenancy model is a critical decision point because it must balance both customer and business requirements.

We recommend that you refer to these articles to make informed decisions:

- [What is multitenancy?](/azure/architecture/guide/multitenant/overview)
- [Tenancy models](/azure/architecture/guide/multitenant/considerations/tenancy-models)

Your architecture should have the flexibility to change the tenancy model based on new or incoming customer requirements. Suppose you adopt a _fully multitenant_ architecture but have a new customer in a highly regulated industry requiring extensive security assurances. You might consider vertically partitioning their deployment to provide a dedicated stamp. This shift introduces a business decision: _Should they pay more than your other tenants?_ Given the increased resource costs and operational complexity, it would be logical for them to pay more, but this decision is up to you.

## 4 - Design to be well-architected

Designing a SaaS workload requires extra care to ensure the system is resilient, secure, efficient, and performant, while balancing customer requirements. Unlike enterprise applications, failures in a SaaS application can have cascading impacts, affecting your business, customers, and their users.

For each decision, evaluate the tradeoffs between the Well-Architected Framework pillars. For information about the strategic approaches per pillar, see [Design principles](./design-prinicples.md). 

## 5 - Design for operations

SaaS workload operations need a different perspective. You'll need to consider new factors, such as supportability. What is your operational strategy for providing 24/7 platform support and hiring for different skill sets. Don't consider operations to be an afterthought and only invest efforts in building new features. Incorporate operability into your design from the beginning.

Additionally, understand how your processes will scale beyond a few customers. While manual operational activities may be feasible initially, they often do not scale well in the long term.

If you have a legacy platform, consider how, or if, you'll migrate customers to your new SaaS platform. A smooth migration path is essential to maintaining customer satisfaction during your business transformation.
