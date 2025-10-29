---
title: Design Methodology for SaaS Workloads on Azure
description: Learn how to implement a structured approach when you make architectural design decisions for software as a service (SaaS) workloads on Azure.
author: landonpierce 
ms.author: prwilk
ms.topic: concept-article
ms.date: 11/04/2024
ms.collection: learn-startups
---

# Design methodology for SaaS workloads on Azure

Independent software vendors (ISVs) must carefully plan the requirements of their software as a service (SaaS) solution, given that the solution *is their business*. Business customers, such as other businesses or individual consumers, are the direct users of the solution. This business model sets high expectations because you must consider both your workload requirements and the customers' needs as the architect of the design.

This article describes a design methodology that you can use to systematically define and refine requirements. If you're uncertain about various design decisions and technology options, revisit this methodology to stay aligned with business requirements. Building a SaaS workload is an iterative process that requires flexibility to adapt to evolving markets and customer needs. This framework can help you collaborate with marketing and sales teams to validate technical decisions and assess customer feedback for continuous improvement.

## Design for your business model

It's important to understand how your business requirements affect your solution downstream. Consider the following decision points:

- The location where you deploy resources limits the architecture patterns that you can use. You can deploy all resources in your Azure subscriptions, or customers can purchase the solution and deploy it in their own Azure subscriptions. Alternatively, the workload can use resources that the customer deploys in their Azure subscriptions.

  For example, if you deploy your software in the customer's environment, you can't use an architecture pattern that's based on only shared resources because each customer has their own standalone environment with dedicated resources.

  For more information, see [ISV deployment models](/azure/cloud-adoption-framework/ready/landing-zone/isv-landing-zone#isv-deployment-models).
  
- Your pricing model determines your business's revenue, which in turn affects your allowable cost of goods sold. This dynamic directly affects your technical architecture.

  For more information, see [Pricing model](/azure/architecture/guide/multitenant/considerations/pricing-models).
  
- The features or products that you provide can affect your architecture. You might need to make changes or additions to your technical architecture when you choose specific features. Providing different products to various customers can also lead to a more complex architecture because it must support these variations.

## Design for your customer requirements

Design your solution with customer requirements in mind. Customers might have extra requirements for their solution, which creates a superset that your solution must meet. These extra requirements can sometimes conflict with your business needs or the needs of other customers. When these requirements differ from your business needs or add more restrictions, making decisions for your solution can be tough. For instance, your solution might meet your security standards, but a customer might have stricter security requirements that you must fulfill to protect their business.

Create a flexible architecture to accommodate these extra requirements. If customer requirements don't affect your own requirements, try to integrate them into your business model. Calculate the cost of these adjustments. If a customer’s unique requirements incur extra costs, consider charging them accordingly.

Make sure that you have realistic [reliability targets](/azure/well-architected/reliability/metrics) that meet customer expectations, and design your architecture to achieve them.

## Design your tenancy model

Most SaaS solutions rely on multitenancy as the primary technical strategy to maximize cost efficiency. Multitenancy involves a range of choices that don't have standard patterns. Your tenancy model affects aspects of your architecture, including management overhead, cost, and data isolation. Find the right balance for your solution. The tenancy model that you choose is crucial because it must balance customer and business needs.

To help you make informed decisions, refer to these articles:

- [What is multitenancy?](/azure/architecture/guide/multitenant/overview)
- [Tenancy models](/azure/architecture/guide/multitenant/considerations/tenancy-models)

Your architecture should have the flexibility to change the tenancy model based on new or incoming customer requirements. For example, you might use a fully multitenant architecture but get a new customer in a highly regulated industry that needs extra security. You can vertically partition their deployment to provide a dedicated stamp. This change raises a business decision about whether they should pay more than your other tenants. This setup increases resource costs and complexity, so it makes sense for them to pay more.

## Design to be well-architected

When you design a SaaS workload, use extra care to ensure that the system is resilient, secure, efficient, performant, and balances customer requirements. Unlike enterprise applications, failures in a SaaS application can also affect your business, customers, and their users.

For each decision, evaluate the tradeoffs between the [Azure Well-Architected Framework pillars](../pillars.md). For information about the strategic approaches per pillar, see [Design principles](./design-principles.md).

## Design for operations

SaaS workload operations need a different perspective. You need to consider factors like supportability. Determine how to provide all-day platform support and hire people with the right skill set. Don't treat operations as an afterthought or only focus on building new features. Include operability in your design from the start. Consider how your processes scale as you get more customers. For instance, manual operations might work at first, but they usually don’t scale well over time.

If you have a legacy platform, consider how or if you should move customers to your new SaaS platform. A smooth migration path is key to keeping customers happy during your business transformation.

## Next step

> [!div class="nextstepaction"]
> [Design principles of SaaS workloads on Azure](design-principles.md)
