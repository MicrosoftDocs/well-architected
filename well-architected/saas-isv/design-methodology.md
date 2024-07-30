---
title: Design methodology for ISV workloads on Azure 
description: TBD - design methodology.
author: landonpierce 
ms.author: landonpierce 
ms.date: 07/02/2024
ms.topic: conceptual
---

Designing a SaaS workload, like all workloads, requires careful planning and consideration in order to design correctly. However, there are several key things that make SaaS workloads unique and more challenging to design than a standard workload. For an ISV, their SaaS workload *is their business*, which is unlike other business models where software might empower or enable the business. Your customers will be the ones using the application instead of your employees, which often means expectations are higher. Given this, you must consider not only your own requirements for your solution, but your customer's requirements as well.

It is also important to keep in mind that building a SaaS workload is an iterative process, as it is impossible to account for every possible scenario up front. For that reason, a key factor in your design should be *flexibility*. The ability to respond to evolving markets and changing customer requirements as things change is a necessary part of being an ISV, and requires great flexibility on the part of the architecture. How you work together with your marketing and sales teams becomes critical during this entire process as you need to be constantly evaluating feedback and data from your customers in order to drive your solution forward.

**Diagram depicting this iterative process**

## 1 - Design for your business model 

Every ISV has different business requirements that they're trying design their SaaS solution to meet. It is important to understand how these business requirements will affect your solution downstream. Choices such as your software delivery model, pricing model, which SKUs you are going to offer, and more have downstream implications on your technical design decisions.

You should start with an awareness of your business model. This list is not all-inclusive, but some things you should have in mind are: 
- [**The deployment model of your software.**](/azure/cloud-adoption-framework/ready/landing-zone/isv-landing-zone?#isv-deployment-models) Your deployment model will impact which technical architectures are feasible. For example, if you choose to deploy your software completely in the customer's environment, an architecture in which you share all of the components is impossible because each customer would have their own standalone environment with dedicated resources. 
- [**Your pricing model.**](/azure/architecture/guide/multitenant/considerations/pricing-models) Your pricing model decides how much revenue you have incoming to your business. This will impact how high your COGS (cost of goods sold) can be, which has a direct impact on your technical architecture.
- **The features or SKUs you offer.** Choosing to offer certain features vs other ones may warrant changes or additions to your technical architecture. Choosing to offer different SKUs to different customers may result in a more complex architecture to support it.

## 2 - Design for your customer requirements

As an ISV, it is also important to design your solution with your customer requirements in mind. The presence of customer requirements are what make ISV solutions fundamentally different than other types of solutions, because they may not always perfectly align with your business requirements. These requirements often take the form of a "superset", meaning that customers will have additional requirements they want your solution to meet in order to use your solution. For example, your solution could be designed securely in accordance with your own business standards, but you might get a customer that has stricter security standards that they want you to meet in order to win their business.

Customer's having extra requirements is fairly common and, although it doesn't necessarily mean that those requirements will be in conflict with your business requirements or other customer requirements, it is possible. Your architecture should be flexible enough to allow for these extra sets of requirements, if your business chooses to do so.

Customer requirements should also be factored into your overall business model as well. If a customer's unique requirements incurs you additional cost, you should consider charging them more than other customers.

## 3 - Design your tenancy model

The overwhelming majority of SaaS products rely on [multitenancy](/azure/architecture/guide/multitenant/overview) for a technical architecture in order to maximize their cost efficiency. Multitenancy is a large and complicated subject with many decisions and tradeoffs to make, and great care should be taken when designing a multitenant application. The [tenancy model](/azure/architecture/guide/multitenant/considerations/tenancy-models) you use for your application is critical to your design, and decisions regarding your tenancy model are interdependent on both your customer and your business requirements.

You also may need the flexibility in your architecture to change your tenancy model, depending on how you choose to handle new or incoming customer requirements. For example, consider what you might do if you adopt a [fully multitenant](/azure/architecture/guide/multitenant/considerations/tenancy-models#fully-multitenant-deployments) architecture, but then get a new customer in a highly regulated industry that requires many security assurances. You might consider [vertically partitioning](/azure/architecture/guide/multitenant/considerations/tenancy-models#vertically-partitioned-deployments) their deployment to give them their own dedicated stamp, but now you have a business choice to make: *Do they pay more than your other tenants?* Since they are incurring you more resource costs and operational complexity than your other tenants, the logical choice would be for them to pay more, but that is for your business to decide.

Multitenancy is a multidimensional spectrum of choices to make, with no standard set of patterns. Your tenancy model affects many part of your architecture and has implications on management overhead, cost, data isolation, and more.  It is critically important to find the balance between those that is right for your solution.

## 4 - Design with care

Extra care should be taken when designing a SaaS workload. You need to be sure that the system that you design is resilient, secure, efficient, and performance, all while balancing your customer requirements. Unlike enterprise applications, failures in a SaaS application are typically exponential, as they affect not only your own business, but your customers and their users as well. Ensure you have realistic [reliability targets](/azure/well-architected/reliability/metrics) that meet customer expectations and that you design your architecture to meet them.

## 5 - Design for Operations

Operating a SaaS workload as a business is fundamentally different than other business models. If your business has been operating under a different model until now, this is a drastic change from the way you might be used to doing things. You will need to consider things that you previously had not, such as as the need for 24/7 support of your platform, different skill sets you may need to hire for, and more. In non-SaaS workloads, it is common for operations to be an afterthought, which weighs down your ability to build new features for your customers by bogging you down with a heavy operations workload. You should take care to include operability in your overall design from the beginning.

If you have a legacy platform you are currently operating on, it is also important to consider how, or if, you will migrate customers onto your new SaaS platform. A smooth migration path is paramount to ensuring your existing customers remain happy as your business transforms.

You should also understand how processes you create will scale past a handful of customers. It is common to perform some operational activities manually up front as you are getting started, but this often does not scale well long term.
