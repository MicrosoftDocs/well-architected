---
title: Cost Maturity Model
description: Understand the maturity model levels of the cost optimization pillar.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 02/17/2025  
ms.topic: conceptual
---

# Cost Optimization maturity model

In the context of the Well-Architected Framework, cost optimization is about detecting spend patterns, prioritizing investments in critical areas, and optimizing in others to meet the organization's budget while meeting business requirements.

[add art]

:::image type="content" source="../_images/reliability.svg" alt-text="Example alt-text."::: 

# [**Level 1 - Trust and accountability**](#tab/level1)

![Goal icon](../_images/goal.svg) **Build out the mechanisms to manage workload costs throughout its lifecycle.**

Level 1 of the maturity model is designed to help workload teams understand their budget and to put processes and tools in place to manage their costs over the course of their workload lifecycle. Before starting on more mature processes, like building a cost model, you should focus on basic cost management best practices that will help you get your workload started without causing issues with the budget.

The recommendations in the following checklist will help you wisely manage costs while allowing you to build your workload efficiently.

#### &#10003; Share financial goals, budget details, and workload spend with all team members, managers, and decision-makers

Provide stakeholders with detailed information about the allocated budget, cost breakdowns, and financial goals for your workload. Offer detailed insights into various expenses, such as infrastructure costs, software licenses, and operational expenses. This is a practice that is **not often thought of, but is important** in building trust between the workload team and leadership teams. Sharing these insights gives leadership piece of mind that the workload team is thinking ahead about how they will manage their expenses. It also helps motivate the workload team to remain diligent in their cost management because they have made commitments to leadership.

#### &#10003; Define processes and tools for sharing cost-saving ideas and knowledge

Use collaboration tools to encourage team members to share cost optimization ideas and insights. Providing mechanisms for the team to collaborate on their ideas and insights is a great motivational tool as it empowers team members to offer suggestions that cut across all disciplines of the workload team. By fostering a culture of collaboration on cost optimization, you can reinforce the mindset that managing the workload costs relies on every team member equally.

To help motivate adopting a cost optimization mindset, consider acknowledging individuals and teams who demonstrate financial responsibility and contribute to cost optimization through performance evaluations, incentives, or other recognition programs.

#### &#10003; Gather detailed cost data from all sources, including invoiced (actual billed) and metered (predictive based on billing plans) data

Explore all of the available tools and methods provided by your service provider to compile a fully detailed picture of your workload's costs. Relying only on the monthly bill might not give stakeholders enough details about spending. Compile data to save time in investigating vague items and explaining obscure terms. After you've gathered utilization data, centralize that data into a unified system.

Take advantage of billing dashboards that can be filtered for different views to make it easier for stakeholders and workload team members to see the information that's most relevant to them.

#### &#10003; Determine the cost drivers

Using the tools you've adopted to gather billing data, you can identify how different workload components contribute to the overall costs. Pay special attention to costs related to processes, like data transfer or transactions, contribute to the overall costs as these are often not considered when estimating resource costs before deployment. 

You should also understand how factors not directly related to your cloud resources can affect your budget. This can include training for team members, and software licensing not managed by your cloud provider.

#### &#10003; Decide whether to build or buy 

One of the first decisions the workload team needs to make is whether they should use off-the-shelf solutions or build solutions in-house. Generally speaking, the Well-Architected Framework favors [keeping things simple](../reliability/simplify.md), and using off-the-shelf solutions follows that philosophy. Using well-supported solutions relieves operational burden from your team and decreases the time needed for development, allowing developers to focus on core functionality of the application.

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



# [Level 2 - Financial assessment](#tab/level2)

![Goal icon](../_images/goal.svg) **Ensure visibility into spending patterns through systematic analysis and establish team accountability for regular cost data reviews.**

After the initial deployment, it's common to focus on developing new features and sales, often neglecting cost management. At Level 2, spending is expected to increase, making a disciplined approach necessary. This involves assessing the costs of tools and technologies, building a baseline, and identifying main cost drivers. 

Although cost management may seem overwhelming, systematic cost analysis at an early stage is necessary for maintaining financial control and supporting growth.

#### &#10003; Develop a baseline cost model

A _cost model_ is an estimate of the overall cost of a workload broken down by contributing factors such as platform services, data storage, user and system flows, and operations. At the very least, conduct a systematic cost analysis and create a simple [cost model](./cost-model.md). This exercise helps in making informed decisions about resource allocation and optimization.

- Create an inventory of all Azure resources, specifying which aspects of each resource lead to higher costs.

- Ask questions about the technology choices and their alignment with goals like scalability, reliability, within the cost constraints. For each resource, note the cost and determine if the cost is too high or too low. This involves measuring against expected loads, keeping in mind potential growth.

- Note the cost of flows distinguishing between critical and noncritical flows. Critical flows, such as user experience in an e-commerce application, should be prioritized over noncritical flows, like background jobs for audit purposes.

- Calculate the cost of data storage and data access patterns.

#### &#10003; Build cost reports

Build basic cost reports for financial visibility and analyzing cost patterns. Also, set up cost alerts for when a certain amount of spending is exceeded.

It's recommended that you use [Azure Cost Management and Billing](/azure/cost-management-billing/cost-management-billing-overview) to analyze, monitor, and control your spending on services. If that option isn't available to you, build a system to track consumption and generate a report monthly. 

#### &#10003; Set up accountability

Accountability is non-negotiable for cost management. In the early stages, assign a team member to review budgets and cost reports monthly, keeping the team informed of unexpected spikes. The accountable person should evaluate workload spend and guide the team accordingly. 

Initially, accountability can be flexible. As the startup matures, enforce stricter controls.

#### &#10003; Evaluate optimization opportunities

Your cost model should give you a good idea of the main cost drivers and the cost reports should provide evidence. Evaluate those drivers and explore opportunities to optimize. Here are some considerations: 

- Evaluate the billing model for each Azure resource used by the workload. This involves analyzing the features bundled with each plan and determining if they are necessary for your needs. For instance, if you require specific features like a private endpoint with Azure Front Door, you may need the premium plan. Otherwise, consider moving to a lower plan to save costs.

- Evaluate the choice between consumption-based billing and commitment-based billing. It's recommended to start with consumption-based billing to understand the incurred costs. If the load is consistent, you can explore discounts offered by Azure for consistent loads at later stages.

- Evalute coding practices. This requires code instrumentation to identify processes that are expensive to run and provide insight into performance issues, which can incur unnecessary costs. Metrics such as process duration and failure rates should be collected to make data-driven decisions for future optimization.

#### &#10003; Decide on an initial scaling strategy

Scaling out is preferred as it's more cost-effective and easier to implement compared to scaling up, which involves downtime and higher costs.

Performance testing and metrics should be considered when making the decision to scale to scale out or scale up based on the evaluation.

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
