---
title: SaaS Workloads
description: Overview of SaaS workloads on Azure.
author: johndowns
ms.author: prwilk
ms.date: 11/04/2024
ms.topic: concept-article
ms.collection: learn-startups
---

# Azure Well-Architected SaaS workloads

Building and operating software as a service (SaaS) on Microsoft Azure requires a different approach compared to other types of software. Independent software vendors (ISVs) have a unique position in the cloud ecosystem because their SaaS solutions drive their business. They sell their products to businesses, also known as *business-to-business (B2B)*, or to consumers, also known as *business-to-consumer (B2C)*. ISVs typically host and maintain the SaaS solution that they build. Their customers configure the product and manage the data.

A solution built using the Well-Architected Framework ensures that the workload can operate at scale. This series of articles provide key insights for creating scalable, performant, reliable, and secure SaaS solutions on Azure. If you're not already familiar with the Well-Architected Framework, we suggest taking some time to learn its principles.

> Refer to [**Microsoft Azure Well-Architected Framework pillars**](../pillars.md).

## What is a SaaS workload?

The term *workload* refers to a collection of application resources that support a common business goal or the execution of a common business process, with multiple services, such as APIs and data stores, working together to deliver specific end-to-end functionality.

The term *SaaS* refers to the business model of delivering software as a service. As a vendor, you are responsible for delivering and operating the entire solution. You must carefully manage customer environments at scale while meeting requirements for customer isolation, security, and compliance. SaaS solutions often rely on a multitenant architecture, where resources are shared among multiple customers. This approach influences the design and deployment of resources, as well as the pricing model offered to customers.

## What are the common challenges?

Microsoft Azure is an excellent platform for delivering SaaS, offering the necessary elasticity and scalability. It also provides features to automate various aspects of SaaS delivery. However, delivering SaaS on Azure comes with its own set of challenges:

- Customer expectations are high, demanding quality, security, and resiliency. For B2B solutions, you essentially become an extension of your customers' IT departments, taking responsibility for keeping your solution operational and healthy. This requires a shift from merely developing software to operating it at scale.

- Delivering SaaS means balancing your own business needs with those of your customers, which can sometimes conflict. You'll face pressure to reduce your cost of goods sold (COGS) and increase efficiency, while customers demand more from your solution.

- SaaS often operates at high scale or with aggressive growth targets. As you scale, maintaining performance and reliability while reducing operational complexity is crucial. Manual operations are impractical, so automation and structured processes are necessary, requiring a degree of operational maturity.

- Isolation is a critical requirement when you share infrastructure among your customers. Customers expect their data to be secure and to experience consistent performance and reliability, regardless of other customers' activities. As a vendor, you bear significant responsibility for protecting your customers' data and workloads, including from other customers.

## What's the maturity model for building SaaS?

Organizations building SaaS products are typically:

- **Startups or other small organizations.** Typically, there are fewer people, and fewer resources. No matter the size of the organization, SaaS requires some level of maturity to meet high customer expectations. Customers trust the organization to protect their data and other assets. They might also rely on the solution for important parts of their operations. Therefore, Operational Excellence and Reliability become key aspects of the solution.

  Initially, startups should prioritize the most impactful elements for their customers. In parallel, they should plan for future architectural enhancements such as automation, tenant management, cost reduction, and improved security and reliability. Even though this may not seem practical at first, this strategic planning serves as a blueprint for gradual implementation and continuous improvement. As the startup grows, it should adapt and refine its processes, adopt new technologies, and meet evolving compliance standards to scale effectively and maintain customer trust.

- **Established organizations.** Established organizations looking to modernize their existing solutions often migrate to a SaaS model. Even though the organization might have more resources, the challenges become complex. They must support existing customers while developing the new SaaS solution, which can create operational overhead. This transition requires changes in technical architecture, skill sets, and overall business operations. The focus should be on minimizing the impact on current customers and make sure they receive similar or improved reliability, security, and performance. Once the legacy solutions are less burdensome, the organization can prioritize new features and improvements.

## How to use this guidance?

**&#10004;** Start with [**Design Methodology**](./design-methodology.md), which outlines the rationale and recurring themes across technical and operational areas. This systematic approach helps define requirements and design strategies. Revisit this methodology when facing uncertain choices to stay aligned with the workload's overall goals. It also provides a framework for collaborating with marketing and sales teams to validate technical decisions and incorporate customer feedback for continuous improvement.

**&#10004;** Proceed to [**Design Principles**](./design-methodology.md) to see how the SaaS design methodology aligns with the core Well-Architected Framework pillars, considering growth evolution. Evaluate the underlying principles for all pillars collectively, including the tradeoffs.

**&#10004;** Focus on the **design areas** that have the biggest impact on your solution. Each area includes considerations and recommendations to guide you through the design decisions.

|Design areas|
|---|
|[Billing and cost management](./billing-cost-management.md): Evaluate your billing strategy and its effect on your cost of goods sold (COGS). Model and anticipate cost changes as your SaaS business scales. Look for ways to optimize cloud resource expenses.|
|[Governance](./governance.md): Manage and regulate your cloud service usage to establish a secure Azure environment.|
|[Resource organization](./resource-organization.md): Plan how you'll deploy your resources to support your scale and cost requirements. |
|[Identity and access management](./identity-access.md): Understand the challenges of managing identity in a multitenant SaaS environment. Choose an appropriate identity provider and consider the need for federation with your customers' identity systems.|
|[Compute](./compute.md): Select a compute platform that meets your needs. Plan for customer isolation, scalability, and resiliency.  |
|[Networking](./networking.md): Plan your network deployment, including topology and defenses. Isolate resources between customers and meet their connectivity needs, including integrating with their networks and deploying resources into their environments. |
|[Data](./data.md): Choose a suitable data store and plan for isolating customer data while maintaining operational efficiency. Consider capacity planning based on your scale and growth, and ensure your data meets customer resiliency requirements. |
|[DevOps practices](./devops.md): Deploy infrastructure and applications for each customer according to your tenancy model. Use a structured approach for changes, including progressive rollouts. |
|[Incident management](./incident-management.md) : Establish responsibilities of operating SaaS and the necessary cultural elements within your organization. Prepare for incidents by investing in tools and processes for investigation, remediation, and communication.|

**&#10004;** Use the [**Assessment Review Tool**](./assessment.md) to evaluate the readiness of your optimized SaaS workload in production.

> [!TIP]
> Every architectural decision involves a range of considerations and a set of acknowledged compromises that balance different aspects of the framework. These tradeoffs are indicated by this icon. :::image type="icon" source="../_images/trade-off.svg":::.

## What resources are available?

Multitenancy is a core business methodology for designing SaaS workloads. Read these additional resources to learn more.

- [SaaS and multitenant solution architecture](/azure/architecture/guide/saas-multitenant-solution-architecture/): Provides extensive and detailed guidance on how to design SaaS, including information on how specific Azure services can be used to support SaaS design goals.

## Next steps

Understand the methodology to follow when designing a SaaS workload on Azure.

> [!div class="nextstepaction"]
> [Design methodology](design-methodology.md)
