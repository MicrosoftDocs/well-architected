---
title: Design methodology for ISV workloads on Azure 
description: Learn how to implement a structured approach when you make architectural design decisions for software as a service (SaaS) workloads on Azure.
author: landonpierce 
ms.author: landonpierce
ms.topic: conceptual 
ms.date: 09/04/2024
ms.custom: template-overview
ms.service: azure-waf

---

# Design methodology for ISV workloads

Because the foundation of a business is its software as a service (SaaS) solution requirements, independent software publishers (ISVs) must carefully plan them. Business customers are the direct users of the solution, whether they're other businesses or individual consumers. This business model sets higher expectations because you must consider both your workload requirements and the customer’s needs as the architect of the design.

This article describes a design methodology that you can use to systematically define and refine requirements. If you're uncertain about various design decisions and technology options, revisit this methodology to stay aligned with business requirements. Building a SaaS workload is an iterative process that requires flexibility to adapt to evolving markets and customer needs. This framework enables you to work with marketing and sales teams to validate technical decisions and assess customer feedback for continuous improvement.

**Diagram that depicts this iterative process**

## 1 - Design for your business model

It's important to understand how your business requirements affect your solution downstream. Consider the following decision points:

- Where you deploy resources limits the architecture patterns that you can use. Resources can be fully deployed in your Azure subscriptions, or customers can purchase the solution and deploy it in their own Azure subscriptions. Alternatively, the workload can use resources that are deployed in the customers' Azure subscriptions.

  For example, if you choose to deploy your software completely in the customer's environment, you can't base an architecture pattern only on shared resources because each customer would have their own standalone environment with dedicated resources.

  For more information, see [ISV deployment models](/azure/cloud-adoption-framework/ready/landing-zone/isv-landing-zone?#isv-deployment-models).
  
- Your pricing model determines your business's revenue, which in turn affects your allowable cost of goods sold. This dynamic directly affects your technical architecture.

  For more information, see [Pricing model](/azure/architecture/guide/multitenant/considerations/pricing-models).
  
- The features or products that you provide can affect your architecture. Changes or additions to your technical architecture might be required when you choose specific features. Providing different products to various customers can also lead to a more complex architecture to support these variations.

## 2 - Design for your customer requirements

Design your solution with customer requirements in mind. Customers might have extra requirements for their solution, which creates a superset that your solution must meet. These extra requirements can sometimes conflict with your business needs or the needs of other customers. When these requirements differ from your business needs or add more restrictions, making decisions for your solution can be tough. For instance, your solution might meet your security standards, but a customer might have stricter security requirements to protect their business.

Your architecture should be flexible enough to accommodate these extra requirements. If customer requirements don't affect your own requirements, try to integrate them into your business model. Always calculate the cost of these adjustments. If a customer’s unique requirements incur extra costs, consider charging them accordingly.

Make sure that you have realistic [reliability targets](/azure/well-architected/reliability/metrics) that meet customer expectations, and design your architecture to achieve them.

## 3 - Design your tenancy model

Most SaaS solutions use multitenancy as the primary technical strategy to maximize cost efficiency. Multitenancy involves a range of choices that don't have standard patterns. Your tenancy model affects your architecture’s management overhead, cost, and data isolation. It’s important to find the right balance for your solution. Choosing a tenancy model is crucial because it must balance customer and business needs.

To make informed decisions, refer to these articles:

- [What is multitenancy?](/azure/architecture/guide/multitenant/overview)
- [Tenancy models](/azure/architecture/guide/multitenant/considerations/tenancy-models)

Your architecture should be flexible enough to change the tenancy model based on new customer requirements. For example, if you use a fully multitenant architecture but get a new customer in a highly regulated industry that needs extra security, you might consider vertically partitioning their deployment to provide a dedicated stamp. This change raises a business decision about whether they should pay more than your other tenants. Because this setup increases resource costs and complexity, it makes sense for them to pay more, but the decision is yours.

## 4 - Design for operations

A different approach is required to operate a SaaS workload. You need to consider factors like supportability, including how to provide 24/7 platform support and hire people with the right skill sets. Don’t treat operations as an afterthought or only focus on building new features. Include operability in your design from the start. Consider how your processes scale as you get more customers. For instance, manual operations might work at first, but they usually don’t scale well over time.

If you have a legacy platform, consider how or if you should move customers to your new SaaS platform. A smooth migration path is key to keeping customers happy during your business transformation.

## Target architecture evolution

When you design a SaaS workload, extra care is required to ensure that the system is resilient, secure, efficient, performant, and balances customer requirements. Unlike enterprise applications, failures in a SaaS application can have cascading effects that affect your business, customers, and their users.

For each decision, evaluate the tradeoffs between the [Azure Well-Architected Framework pillars](../pillars.md). For more information, see [Design principles](./design-principles.md).

## Next step

> [!div class="nextstepaction"]
> [Design principles of an ISV workload](design-principles.md)
