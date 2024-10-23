---
title: SaaS Workloads
description: Overview of SaaS workloads on Azure.
author: johndowns
ms.author: jodowns
ms.date: 09/24/2024
ms.topic: conceptual
ms.collection: learn-startups
---

# Azure Well-Architected SaaS workloads

Delivering software as a service (SaaS) is different to how you might design or operate other forms of software on Microsoft Azure. This section describes some of the key things that independent software vendors (ISVs) need to know to build scalable, performant, reliable, and secure SaaS on Azure. This section provides actionable and authoritative guidance that applies Well-Architected best practices as the technical foundation for building and operating a SaaS solution on Azure at-scale.

## What is a SaaS workload?

The term *workload* refers to a collection of application resources that support a common business goal or the execution of a common business process, with multiple services, such as APIs and data stores, working together to deliver specific end-to-end functionality.

The term *SaaS* refers to the business model of delivering software as a service. As a SaaS vendor, your whole business is about delivering and operating the solution. This brings along many different concerns, including how you deploy and manage customer environments at scale, and how you meet requirements for customer isolation, security, and compliance. SaaS solutions frequently shares component between multiple customers, which is referred to as *mulitenant architecture* or *multitenancy*.

A *SaaS workload* therefore describes the resources that you design, deploy, and manage, to operate software as a service at scale. All five Well-Architected Framework pillars are important for SaaS.

## What are the common challenges?

Microsoft Azure is a great platform for delivering SaaS. Cloud platforms provide the necessary elasticity and scalability required for many SaaS solutions, and you can use platform features to automate many aspects of how you deliver SaaS. However, SaaS does present some challenges:

- SaaS is a business model, and it changes how you think about delivering software. Customer expectations are typically high, and they demand quality as well as security and resiliency. For B2B solutions, you become an extension of your customers' IT departments and you take responsibility for keeping your solution operational and healthy. You need to be prepared to shift from developing software to operating it at scale.

- When you deliver SaaS, your business requirements are driven both by your own business needs and by the requirements of your customers. It's important to balance these two types of requirements, which sometimes compete. You'll be driven by pressure to reduce your cost of goods sold (COGS) and increase your efficiency, while your customers demand more from your solution.

- SaaS often operates at high scale, or with aggressive growth targets that require scale in the future. As you scale, it's important to maintain performance and reliability, and to reduce operational complexity. Manual operations aren't practical when you deliver SaaS, and automation and structured processes require a degree of operational maturity.

- Although you might share infrastructure between your customers, isolation is critical. Customers expect their data to be kept secure, and to see consistent performance and reliability regardless of whatever other customers might be doing. As a SaaS platform vendor, you take significant responsibility to protect your customers' data and workloads, including from your other customers.

## What level of maturity do you need to build SaaS?

New SaaS products are often built by startups and other small organizations. These organizations might have fewer people, and fewer resources, than larger organizations. No matter the size of your organization, SaaS requires some level of maturity. Customer expectations are often high, and your customers trust you to protect their data and other assets. They might also rely on your solution for important parts of their operations. You need to invest in good operational processes as well as building reliability into your solution.

It's possible to start small and grow over time. When you're starting out, think about the elements that are most impactful to your customers and focus on those. In parallel, sketch out a plan for where you want your architecture to get to, which might include elements like automation, tenant management, cost reduction, and improvements in your security and reliability over time. You won't be able to implement your whole plan immediately, but it can provide a blueprint for you to work towards, and you can adjust it over time. Importantly, you should strive to continuously improve your solution, whether that means adopting new technologies, streamlining your operations, or meeting stricter compliance requirements as you grow and scale.

## What are the key design areas?

SaaS solutions consist of the same kinds of components as other solutions on Azure. However, there are often special considerations that apply for each area to ensure isolation, scalability, reliability, cost efficiency, and operational excellence.

| Design area | Summary |
|---|---|
| Billing and cost management | Consider how you bill your customers and how billing relates to your COGS. Model your costs and how they change as your SaaS business grows and scales. Look for opportunities to optimize your cloud resource costs.  |
| Governance | Organize, control, and regulate your use of cloud services. Set up the foundations for a secure Azure environment. |
| Resource organization | Plan how you'll deploy your resources to support your scale and cost requirements. |
| Identity and access management | Understand the unique challenges of managing identity in a multitenant environment like SaaS. Select an appropriate identity provider, including the need for federation with your customers' identity systems. |
| Compute | Select a compute platform that meets your needs. Plan for customer isolation, scalability, and resiliency.  |
| Networking | Plan how you'll deploy network resources, including an overall network topology and your perimeter defenses. Understand the ways in which you can isolate network resources between customers. Meet your customers' requirements for connectivity, including connecting to networks in customer environments, and deploying your SaaS solution resources into customer environments. |
| Data | Select an appropriate data store for your data, and plan how you'll isolate customer data while retaining operational efficiency. Plan capacity based on your scale and growth, and meet customer needs for resiliency in your data. |
| DevOps practices | Deploy infrastructure and applications for each customer based on your tenancy model. Follow a structured approach to changes, including progressive rollouts across your customers. |
| Incident management | Understand the responsibilities of operating SaaS and the cultural elements required in your organization. Plan for incidents, investing in tooling and processes to investigate, remediate, and communicate. |

## What resources are available?

This section provides guidance that helps ISVs to design and build well-architected SaaS solutions, and introduces many of the important decisions and concepts that you'll need to be successful.

After you've read the information here, use these additional resources:

- [SaaS and multitenant solution architecture](/azure/architecture/guide/saas-multitenant-solution-architecture/): Provides extensive and detailed guidance on how to design SaaS, including information on how specific Azure services can be used to support SaaS design goals.
- [Azure Well-Architected Framework pillars](/azure/well-architected/): A set of quality-driven tenets architectural decision points, and review tools intended to help solution architects build a technical foundation for their workloads.

## Next steps

Understand the methodology to follow when designing a SaaS workload on Azure.

> [!div class="nextstepaction"]
> [Design methodology](design-methodology.md)
