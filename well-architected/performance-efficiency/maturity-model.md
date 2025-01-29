---
title: Performance Efficiency Maturity Model
description: Understand the maturity model levels of the performance efficiency pillar.
author: claytonsiemens77
ms.author: csiemens
ms.date: 1/22/2025  
ms.topic: conceptual
---

# Performance Efficiency maturity model

Performance Efficiency is about maintaining user experience even when there's an increase in load by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

[add art]

:::image type="content" source="../_images/reliability.svg" alt-text="Example alt-text."::: 

# [**Level 1 - Establish baseline performance targets**](#tab/level1)

![Goal icon](../_images/goal.svg) **Choose workload components with just the right performance capabilities**

Level 1 of the maturity model focuses on gathering performance expectations and choosing cloud services that help you meet those expectations. At this level, focus on investigating your options for resources and components that will help you meet the performance expectations for the workload as a whole. Prioritize services that give you just the right performance capabilities. This approach can help you keep your costs under control and maintain your development velocity.

#### &#10003; Gather requirements and define workload performance targets

Work with stakeholders to understand general expectations for the workload performance. These might be targets related to page loading times for web apps or response times for an interactive system. At this stage of your workload development, these targets shouldn't be considered hard requirements as you aren't focusing on measuring performance metrics. Once you've gathered the workload expectations, you can start investigating the types of resources that may be good candidates for your workload.

#### &#10003; Choose appropriate networking resources

Assess your network needs to determine appropriate services and configurations. Consider network traffic, bandwidth, latency, and throughput to ensure the network supports your workload effectively. Use private virtual networks and backbone networks to reduce latency.

Ensure even distribution of network traffic to prevent server overload and reduce response times. Assess different [load balancing services](/azure/architecture/guide/technology-choices/load-balancing-overview) offered by your cloud provider. Consider traffic type, global or regional routing, service-level objectives (SLOs), and specific features like site acceleration and low-latency load balancing.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Be sure that you take time to fully investigate and understand the [different options for the foundational networking](/azure/cloud-adoption-framework/ready/azure-best-practices/define-an-azure-network-topology), as changing this at a later time might require a full redesign and redeployment. 

#### &#10003; Choose appropriate compute resources 

Assess your workload's compute needs, including instance type, scalability, and service tiers. Consider containerization for performance gains that might be realized through isolation, resource efficiency, fast startup time, and portability.

Choose a [compute service](/azure/architecture/guide/technology-choices/compute-decision-tree) that can meet your needs, while allowing you to easily scale as your workload evolves. Remember that building your workload is an iterative process and you can start small, using less performant SKUs and fewer instances than you might need later in the workload's lifecycle.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Weigh your immediate needs against your budget. Look for opportunities to shutdown or deallocate compute resources when they're not in use.


#### &#10003; Choose appropriate data store services 

Determine your workload's needs for storing, retrieving, and managing data. Consider characteristics like:

- *Data types:* What types of data will your workload ingest, process, or store?
- *Volume:* How much data do you expect to ingest, process, or store?
- *Access speed:* What are the performance requirements for accessing different data types?
- *Consistency:* What are your targets for data consistency across your data types?
- *Durability:* What are your targets for data durability across your data types?
- *Access patterns:* What types of access patterns does your workload need to support? For example, for a particular component you may need to perform lots of writes, but few reads. For another component, you may need to do the opposite.

Based on the answers to these questions, you can [choose the best data service](/azure/architecture/guide/technology-choices/data-options) for each of your workload's use cases. 

**Because of the wide variety of options for data services in cloud environments, you can tailor your design to use different services to best match the functionality of each component in your workload. This approach helps you optimize the performance of each component.**


> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Don't over-engineer your data components by choosing different data services for components that could be consolidated into a single data store. Look to strike a balance between performance vs cost and complexity. 


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
