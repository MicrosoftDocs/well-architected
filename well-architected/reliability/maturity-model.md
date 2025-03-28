---
title: Reliability Maturity Model
description: Understand the maturity model levels of the reliability pillar.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 10/23/2024  
ms.topic: conceptual
---

<!--
This template provides the basic structure of a maturity model article. Remove all the comments in this template before you sign-off
-->

<!-- for values for the metadata tags (product and categories) see:
For values to set, see [Taxonomies for Learn](https://review.learn.microsoft.com/help/contribute/metadata-taxonomies?branch=main#azure-category). -->

# Reliability Maturity Model


Reliability is about ensuring that your solution is available and working when your users need and expect it to be. Counterintuitively, the way to achieve high reliability is to accept that things can go wrong. Instead of trying to prevent every problem, it's important to plan how your system responds when there are problems. Your business requirements help you to identify which potential problems you should harden your solution against.


:::image type="content" source="../_images/reliability.svg" alt-text="Example alt-text."::: 

# [**Level 1 - Resilient foundation**](#tab/level1)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

![Goal icon](../_images/goal.svg) **Establish a solid groundwork for resiliency in workload infrastructure and operations, rather than spending time on optimization tasks.**

Level 1 of the maturity model is designed to help workload teams build a strong foundation for system reliability. The focus is on bootstrapping, that is, setting up the basics for future reliability decisions. This stage mostly involves functional implementation with minor extensions to current practices. 

This stage includes researching, gaining insights, and creating an inventory of your systems. Additionally, it leverages Azure's built-in reliability features, like enabling zone redundancy for immediate improvements.

By establishing these basics, you can prepare your team to advance through the levels of the reliability maturity model, progressively enhancing your system's resilience and performance.

<!-- No more than 5 H4 headings per tab -->

#### &#10003; Evaluate opportunities to offload operational responsibility 

This strategy is fundamentally a _build versus buy or rely_ approach. The decision depends on how much responsibility is manageable at this stage while still supporting future development. While you want to utilize resources relevant to the workload, always explore opportunities to offload their maintenance. Here are some classic use cases where you might want to apply this approach.

- **Offload responsibilities to the cloud platform** by preferring PaaS services. They offer ready-made solutions for common resiliency needs like replication, failover, and backup. This way, the cloud provider handles hosting, maintenance, and building resilience. For example, replicating across multiple compute nodes, distributing replicas among availability zones, and others. If you build your own solution on VMs, you'll need to manage these aspects yourself, which can be time-consuming and complex. 

- **Offload responsibility of operations** that aren't directly tied to workload's business objectives. Some specialized operations can potentially affect the reliability of your workload, such as database management and security. Explore the possibility of handling those tasks by experienced teams, choice of technology, or both.  

    For example, if your team doesn't have database expertise, then using managed services can help shift the responsibility to the provider. This approach can be useful when starting out, because it allows the team to focus on the functionality of the workload. Many enterprises have shared, centrally managed services. If available, use platform teams to handle these operations. However, this approach may add dependencies and organizational complexity.

    Alternatively, if the team has the right expertise, you might make an explicit decision to use to use their skills and select services without management capabilities. 

- **Offload responsibility to third-party vendors**. Prefer off-the-shelf products as your starting point. Build customized solutions only when it contributes to your workloads's business value. 

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: If the _buy or rely_ option partially fulfills your requirements, you might need to implement custom extensions. This can often result in a "customization lock-in" situation, where updates and modernization become impractical. Regularly review your requirements and compare them with the solution's capabilities. Develop an exit strategy for when there is a significant deviation between the two.
>
>  The opposite scenario is also a risk. While the _buy or rely_ option might seem simpler initially, it may require re-evaluation and redesign later if the limitations of the PaaS service, vendor solution, or platform-owned resources don't meet the necessary granularity or level of autonomy needed for the workload.


#### &#10003; Build a comprehensive catalog of user and system flows 

Decomposing the workload into flows is crucial at this stage. Focus on _user_ and _system_ flows. User flows determine user interactions, while system flows determine communication between workload components that aren't directly associated with user tasks.

For example, in an e-commerce application, end users perform front-end activities like browsing and ordering. Meanwhile, backend transactions and system-triggered processes fulfill user requests and handle other tasks. Those distinct flows are part of the same system but involve different components and serve different purposes.  

To build the catalog, observe user interactions and component communication. List and categorize all flows, defining their start and end points, and noting dependencies. Document outcomes and exceptions. Use diagrams for clarity.

> [!NOTE]
> A natural next step is to prioritize critical flows. If an outage is expected, graceful degradation will focus on maintaining these critical flows. 
>
> In the e-commerce example, critical flows include product search, adding items to the cart, and checkout, as they are essential for business. Other processes, like updating product data and maintaining product images, aren't as critical. Ensuring critical flows remain operational during an outage prevents revenue loss by allowing users to continue searching for products and adding items to the cart.

> Refer to: [ Well-Architected Framework: Optimize workload design using flows](/azure/well-architected/design-guides/optimize-workload-using-flows).

#### &#10003; Select the right design model, resources, and features 

This strategy should be applied at these levels: 

- **Architecture**. The design of the workload should keep in mind reliability expections at various infrastructure layers. Initial decisions might be choice between containerization or PaaS for hosting the application. Or, networking setups like hub-spoke or a single virtual network. 

    Also set boundaries that creates segmentation based on functionality. For example, Instead of hosting everything on one virtual machine with a single-zone virtual disk, consider splitting compute and data storage, and using dedicated services.

    > [!CAUTION]
    > 
    > In migration scenarios, adopting a lift-and-shift approach without reviewing new opportunities can lead to missed benefits and inefficiencies. It's important to research modernization early to avoid being stuck with difficult-to-change setups and taking advantage of better options and improvements.

- **Choice of Azure services**. Use decision trees to begin to select the right services for your design. Choose components that meet current needs but remain flexible to switch services as your workload evolves and requires more features.

- **Choice of SKUs or tiers within Azure services**. Review the features of each SKU and understand the platform's availability guarantees. Evaluate SLAs to understand the coverage provided around the published percentile.

- **Enable features to support reliability**. Choosing cloud-native services can enhance availability through simple configurations without changing the code. However, it's important to be aware of these options and select configurations mindfully, such as increasing zone redundancy or replicating data to a secondary region.


#### &#10003; Deploy with the basic level of redundancy 

Within each part of your solution, avoid single points of failure, such as single instances, and instead create multiple instances for redundancy. Azure services often handle redundancy for you, especially with PaaS services, which usually include local redundancy by default and options to upgrade. Preferably, use zone redundancy to spread those instances across multiple Azure datacenters. If you don't, at least ensure local redundancy, but this comes with higher risk. For applications with higher availability requirements, consider geo-redundant deployments in two (or more) different Azure regions and introducing a global load balancer and failover mechanism.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: One significant tradeoff is increased cost of redundancy. Also, cross-zone communication can introduce latency. For legacy applications that require minimal latency, there could be a performance hit. 


> :::image type="icon" source="../_images/risk.svg"::: **Risk**: If an application isn't designed for a multi-instance environment, it may struggle with multiple active instances, causing data consistency issues. Also, if it was built for an on-premises setup with low latency, using availability zones could disrupt its performance expectations. 


#### &#10003; Add mechanisms to recover from transient failures 

In a cloud environment, transient faults are common. Any communication outside the localized environment should be resilient to those faults. 

Always use built-in SDKs and configurations to handle these faults and maintain resiliency. This is often the default setting, so test work may be needed to validate the implementation. Also, implement patterns in your architecture, which are designed specifically to handle transient failures. For more information, see [Cloud design patterns that support reliability](./design-patterns.md).

#### &#10003; Create basic test plans 

Run unit and regression tests during development to avoid introducing errors that could cause issues during rollout. This is more of an Operational Excellence concern that can impact reliability.

To enhance reliability, simulate outages with simple test cases for future analysis. This advanced approach involves early integration in the software development lifecycle and may seem excessive. However, even starting small with thorough testing of functionality and configurations is a diligent step toward improved reliability.

#### &#10003; Enable metrics, logs, traces to monitor flows 

Ensure visibility of metrics, logs, and traces in your solution to detect problems. Azure Monitor can help at level one with its out-of-the-box features, providing alerts for potential issues. Start by setting up basic monitoring and getting familiar with the tools. 

You should have basic alerting in place to send notifications and get alerts. Set up [Azure Service Health Alerts](/azure/service-health/overview) to be notified about changes in the health status of Azure services. 

Also, set up Azure Monitor action groups and basic availability tests in Application Insights.

As you progress to level two, you can refine and enhance your monitoring setup.


# [Level 2](#tab/level2)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: Redundancy, Resiliency, and Recovery

<!-- No more than 5 H4 headings per tab -->

#### Example heading 

<!-- No more than 100 words under each H4 heading. -->

# [Level 3](#tab/level3)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: Risk mitigation

<!-- No more than 5 H4 headings per tab -->

#### Example heading

<!-- No more than 100 words under each H4 heading. -->

# [Level 4](#tab/level4)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: Operations

<!-- No more than 5 H4 headings per tab -->

#### Example heading

<!-- No more than 100 words under each H4 heading. -->

# [Level 5](#tab/level5)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: 

<!-- No more than 5 H4 headings per tab -->

#### Example heading

<!-- No more than 100 words under each H4 heading. -->

---

## Next steps
<!-- Provide at least one next step and no more than three. Include some 
context so the customer can determine why they would click the link.
-->