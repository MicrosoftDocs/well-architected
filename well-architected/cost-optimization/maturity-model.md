---
title: Cost Maturity Model
description: Understand the maturity model levels of the cost optimization pillar.
author: claytonsiemens77
ms.author: csiemens
ms.date: 1/22/2025  
ms.topic: conceptual
---

# Cost Optimization maturity model

In the context of the Well-Architected Framework, cost optimization is about detecting spend patterns, prioritizing investments in critical areas, and optimizing in others to meet the organization's budget while meeting business requirements.

[add art]

:::image type="content" source="../_images/reliability.svg" alt-text="Example alt-text."::: 

# [**Level 1 - Trust and accountability**](#tab/level1)

![Goal icon](../_images/goal.svg) **Build out the mechanisms to manage workload costs throughout its lifecycle**

Level 1 of the maturity model is designed to help workload teams understand their budget and to put processes and tools in place to manage their costs over the course of their workload lifecycle. Before more starting on more mature processes, like building a cost model, you should focus on basic cost management best practices that will help you get your workload started without causing issues with the budget.

The recommendations in the following checklist will help you wisely manage costs while allowing you to build your workload efficiently.

#### &#10003; Share financial goals, budget details, and workload spend with all team members, managers, and decision-makers.

Provide stakeholders with detailed information about the allocated budget, cost breakdowns, and financial goals for your workload. Offer detailed insights into various expenses, such as infrastructure costs, software licenses, and operational expenses. This is a practice that is **not often thought of, but is important** in building trust between the workload team and leadership teams. Sharing these insights gives leadership piece of mind that the workload team is thinking ahead about how they will manage their expenses. It also helps motivate the workload team to remain diligent in their cost management because they have made commitments to leadership.

#### &#10003; Define processes and tools for sharing cost-saving ideas and knowledge.

Use collaboration tools to encourage team members to share cost optimization ideas and insights. Providing mechanisms for the team to collaborate on their ideas and insights is a great motivational tool as it empowers team members to offer suggestions that cut across all disciplines of the workload team. By fostering a culture of collaboration on cost optimization, you can reinforce the mindset that managing the workload costs relies on every team member equally.

To help motivate adopting a cost optimization mindset, consider acknowledging individuals and teams who demonstrate financial responsibility and contribute to cost optimization through performance evaluations, incentives, or other recognition programs.

#### &#10003; Gather detailed cost data from all sources, including invoiced (actual billed) and metered (predictive based on billing plans) data.

Explore all of the available tools and methods provided by your service provider to compile a fully detailed picture of your workload's costs. Relying on only the monthly bill might not provide enough details for stakeholders to easily understand how funds are being spent, so compiling as much data as you can will save you time investigating vague line items, or explaining obscure terms to stakeholders. After you've found the right tools to use to gather your utilization data, centralize all of that data into a unified system.

Take advantage of billing dashboards that can be filtered for different views to make it easier for stakeholders and workload team members to see the information that's most relevent to them.

#### &#10003; Determine the cost drivers.

Using the tools you've adopted to gather billing data, you can identify how different workload components contribute to the overall costs. Pay special attention to costs related to processes, like data transfer or transactions, contribute to the overall costs as these are often not considered when estimating resource costs before deployment. 

You should also understand how factors not directly related to your cloud resources can affect your budget. This can include training for team members, and software licensing not managed by your cloud provider.

#### &#10003; Add mechanisms to recover from transient failures 

In a cloud environment, transient faults are common. Any communication outside the localized environment should be resilient to those faults. 

Always use built-in SDKs and configurations to handle these faults and maintain resiliency. This is often the default setting, so test work may be needed to validate the implementation.

#### &#10003; Decide whether to build or buy 

One of the first decisions the workload team needs to make is whether they should use off-the-shelf solutions or build solutions in-house. Generally speaking, the Well-Architected Framework favors [keeping things simple](../reliability/simplify), and using off-the-shelf solutions follows that philosophy. Using well-supported solutions relieves operational burden from your team and decreases the time needed for development, allowing developers to focus on core functionality of the application.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: There are cost considerations to evaluate when making a decision about building or buying a particular solution. Building solutions in-house usually means an up-front investment for the development time and any resources needed to complete the development, but recurring costs can be dramatically less as there won't be any support contracts or licensing to maintain. 

To help you make the right decision for your team, evaluate these points.

- *The desired level of control:* Decide how much control over the functionality provided by a particular solution you really need.
- *The necessary amount of customization:* Determine how much customization you'll need to be able to offer as your workload evolves.
- *The expected time to market:* Understand stakeholders' expectations for bringing your workload to market and how you can best meet that timeline.
- *The required technical expertise:* Determine how much expertise that you'll need to retain in order to build and operate a particular solution.
- *The expected operational burden:* Estimate how much time and effort will be required by operations teams to support a particular solution.

Making these evaluations will help you understand the total costs of each option and allow you to weigh them against each other. The total cost may not be the ultimate decision-maker, but if there are drastic differences, using the lower cost option can help you maintain velocity in building your workload.

#### &#10003; Invest in your team's skills

Invest in upskilling where team knowledge is currently lacking. Strong cloud skills facilitate good long-term decision making and optimize your daily productivity. Consider training or certifications offered by your cloud provider and other third-parties. Enhancing the productivity of your workload team and other decision-makers will save you time and money by minimizing costly mistakes.


# [Level 2](#tab/level2)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: Financial assessment

<!-- No more than 5 H4 headings per tab -->

#### Example heading 

<!-- No more than 100 words under each H4 heading. -->

# [Level 3](#tab/level3)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: Prioritize and optimize

<!-- No more than 5 H4 headings per tab -->

#### Example heading

<!-- No more than 100 words under each H4 heading. -->

# [Level 4](#tab/level4)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: Refine operations

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
