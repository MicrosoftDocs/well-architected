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

# [**Level 1: Trust and accountability**](#tab/level1)

![Goal icon](../_images/goal.svg) **Build out mechanisms to manage workload costs throughout its lifecycle.**

Level 1 of the maturity model helps workload teams understand their budget and set up processes and tools to manage their costs during the workload lifecycle. Before you set up more mature processes, like building a cost model, you should focus on basic cost management best practices to get your workload started without causing problems with the budget.

The following recommendations can help you manage costs wisely while building your workload efficiently.

#### &#10003; Share financial goals, budget details, and workload spending with all team members, managers, and decision-makers

Provide stakeholders with detailed information about the allocated budget, cost breakdowns, and financial goals for your workload. Share detailed insights into various expenses, such as infrastructure costs, software licenses, and operational expenses. This practice is **not often thought of but is important**. It helps build trust between the workload team and leadership teams and shows that the workload team is thinking ahead about managing expenses. This collaboration also helps motivate the workload team to remain diligent in their cost management because of their commitments to leadership teams.

#### &#10003; Define processes and tools to share cost-saving ideas and knowledge

Use collaboration tools to encourage team members to share cost optimization ideas and insights. Provide mechanisms for the team to collaborate on their ideas and insights. This approach empowers team members to offer suggestions across all disciplines of the workload team. When you foster a culture of collaboration on cost optimization, you reinforce the mindset that managing workload costs relies on every team member equally.

To motivate a cost optimization mindset, consider acknowledging individuals and teams who demonstrate financial responsibility and contribute to cost optimization. You could use performance evaluations, incentives, or other recognition programs.

#### &#10003; Gather detailed cost data from all sources, including invoiced and metered data

Invoiced data represents actual billed amounts, and metered data is predictive based on billing plans. To get a fully detailed picture of your workload's costs, explore all available tools and methods that your service provider offers. The monthly bill might not give stakeholders enough details about spending. Compile data to save time investigating unclear charges and explaining obscure terms. After you gather usage data, centralize that data into a unified system.

Take advantage of billing dashboards that have filters for different views. Stakeholders and workload team members can use the filters to easily see the information that's most relevant to them.

#### &#10003; Determine cost drivers

To identify how different workload components contribute to overall costs, use the tools that you adopted to gather billing data. Pay special attention to costs related to processes, like data transfers or transactions, and how they contribute to overall costs. These costs are often overlooked during initial resource cost estimations before deployment. 

You should also understand how factors that aren't directly related to your cloud resources can affect your budget. These factors can include training for team members and software licensing that your cloud provider doesn't manage.

#### &#10003; Decide whether to build or buy 

One of the first decisions the workload team needs to make is whether they should use off-the-shelf solutions or build solutions in-house. Generally, the Well-Architected Framework favors [keeping things simple](../reliability/simplify.md). Using off-the-shelf solutions follows that philosophy. Well-supported solutions relieve your team of operational burden and decrease the time needed for development so that developers can focus on core functionality of the application instead.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Consider costs when you decide whether to build or buy a solution. Building solutions in-house usually means an up-front investment for the development time and any resources needed to complete the development. But you have fewer recurring costs because you don't have to maintain support contracts or licensing.

To help you make the right decision for your team, evaluate the following points.

- *The desired level of control:* Decide how much control you need over the functionality of a solution.

- *The necessary amount of customization:* Determine the level of customization that you need to adapt to your evolving workload.
- *The expected time to market:* Understand stakeholders' expectations for bringing your workload to market and how you can best meet that timeline.
- *The required technical expertise:* Determine how much expertise that you need to retain to build and operate a solution.
- *The expected operational burden:* Estimate how much time and effort operations teams need to support a solution.

These evaluations help you understand the total costs of each option so that you can weigh them against each other. The total cost might not be the sole deciding factor. But if options have significant differences, choose the lower-cost option to speed up your workload development.

#### &#10003; Invest in your team's skills

Invest in upskilling in areas where your team lacks knowledge. Strong cloud skills facilitate good long-term decision-making and optimize your daily productivity. Consider training or certifications that your cloud provider or other partners offer. Enhancing the productivity of your workload team and other decision-makers saves you time and money by minimizing costly mistakes.

# [**Level 2: Financial assessment**](#tab/level2)

![Goal icon](../_images/goal.svg) **Ensure visibility into spending patterns through systematic analysis and establish team accountability for regular cost data reviews.**

After the initial deployment, it's common to focus on developing new features and driving sales while overlooking cost management. At Level 2, spending is expected to increase, so you need to take a structured approach. Assess the costs of tools and technologies, build a baseline, and identify key cost drivers.

Cost management might seem overwhelming, but systematic cost analysis at an early stage is necessary to maintain financial control and support growth.

#### &#10003; Develop a baseline cost model

A *cost model* is an estimate of the overall cost of a workload. The cost is broken down by contributing factors, such as platform services, data storage, user and system flows, and operations. At a minimum, you should conduct a systematic cost analysis and create a basic [cost model](./cost-model.md). This exercise helps you make informed decisions about resource allocation and optimization.

- Create an inventory of all Azure resources and specify which aspects of each resource result in higher costs.

- Ask questions about the technology choices and their alignment with goals like scalability and reliability within the cost constraints. For each resource, note the cost and determine whether the cost is too high or too low. Measure against expected loads while you keep in mind potential growth.

- Note the cost of flows and distinguish between critical and noncritical flows. Prioritize critical flows, such as user experience in an e-commerce application, over noncritical flows, like background jobs for audit purposes.

- Calculate the cost of data storage and data access patterns.

#### &#10003; Build cost reports

Build basic cost reports for financial visibility and analyze cost patterns. Also, set up cost alerts for when you exceed a specific amount of spending.

We recommend that you use [Microsoft Cost Management](/azure/cost-management-billing/cost-management-billing-overview) to analyze, monitor, and control your spending on services. If that option isn't available to you, build a system to track consumption and generate a monthly report.

#### &#10003; Set up accountability

Accountability is nonnegotiable for cost management. In the early stages, assign a team member to review budgets and cost reports monthly and to inform the team of unexpected spikes. The accountable person should evaluate workload spend and guide the team accordingly.

Initially, accountability can be flexible. As the startup matures, enforce stricter controls.

#### &#10003; Evaluate optimization opportunities

Your cost model should give you a good understanding of the key cost drivers, and the cost reports should provide evidence. Evaluate those drivers and explore opportunities to optimize. Consider the following strategies:

- Evaluate the billing model for each Azure resource that the workload uses. Determine whether you need all of the features that each plan bundles. For instance, if you require specific features like a private endpoint with Azure Front Door, you might need the premium plan. Otherwise, consider choosing a lower plan to save costs.

- Choose between consumption-based billing and commitment-based billing. We recommend that you start with consumption-based billing so that you can understand the incurred costs. If the load is consistent, you can explore discounts that Azure provides for consistent loads at later stages.

- Evaluate coding practices. Use code instrumentation to identify processes that are expensive to run and provide insight into performance problems, which can incur unnecessary costs. Collect metrics like process duration and failure rates to make data-driven decisions for future optimization.

#### &#10003; Decide on an initial scaling strategy

Scaling out is preferred because it's more cost-effective and easier to implement compared to scaling up. Scaling up involves downtime and higher costs.

Consider performance testing and metrics, and scale out or scale up based on your evaluation.

# [**Level 3 - Incorporating signals**](#tab/level3)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

![Goal icon](../_images/goal.svg) **Refine cost optimization strategies by gathering and incorporating user and stakeholder feedback**

In the early stages of workload development and operations, you might rely on your internal development feedback loop to optimize your workload. At this maturity level, it becomes important to open up your feedback loop to internal and external users and to other stakeholders. As you make further Cost Optimization refinements to your workload, you'll also need to start considering tradeoffs with other pillars, especially Performance Efficiency and Reliability. 

Level 3 of the Cost Optimization pillar focuses on making your workload production-ready by incorporating internal and external feedback into your strategy while meeting performance and reliability targets.

#### &#10003; Invest in impactful flows and fine-tune others

Reflect on the priorities assigned to you application flows and prioritize development efforts on critical flows, while looking for ways to simplify and reduce the cost of non-critical flows. Non-critical flows might have less stringent reliability requirements, so you might be able to reduce the complexity of their design. 

For each flow, eliminate unnecessary elements, choose appropriate performance tiers, adjust scaling settings for variable demand, and fine-tune configurations to align with performance and budget needs. Monitor flows to identify inefficiencies such as idle compute instances, unused data, and low network bandwidth. Use native logging and analysis tools to help aggregate and analyze these metrics for trends.

#### &#10003; Enforce cost guardrails with alerting

Creating spending thresholds and associated alerting is an easy way to enforce cost guardrails, ensuring that the workload stays within budget. Alerts can include the following.

- *Budgets*: Budget alerts allow you to set spending thresholds, monitor costs, and receive notifications to help control expenses and stay informed.
- *Cost anomalies*: Cost anomaly alerts notify you of unexpected cost variations, allowing you to investigate and address inefficiencies or abnormal spending patterns.
- *Commitment-based utilization*: Commitment-based plan utilization alerts help you monitor and optimize the usage of your commitment-based resources by notifying stakeholders when utilization drops below a desired threshold.

#### &#10003; Develop a strategy to optimize your resource utilization
 
As you build out your nonproduction and production environments, you might inadvertantly deploy overprovisioned resources. Likewise, over time you might end up with resources that were deployed for proof-of-concept or testing reasons that are no longer needed. Developing a strategy to identify under-utilized an unused resources is a cornerstone element of the continuous improvement practice of maintaining an optimized cloud environment. Consider the following recommended activities:

- Use the right resource types and SKUs for each environment. Research the infrastructure options your cloud platform provides and choose resource types and SKUs that are appropriate for each use case. There are often specific resource types and SKUs that are [targeted specifically for proof-of-concept and Dev/Test environments](https://azure.microsoft.com/pricing/offers/dev-test/) that are significantly less expensive than standard or high performance resources, but cannot be used for production workloads.
- Configure and enforce policies to limit allowed resource types and SKUs, regions, and users with deployment permissions. Automated policies will help you control changes to your environments, minimizing the risk of unauthorized deployments.
- Standardize using infrastructure-as-code (IaC) for all environment changes. This practice further enforces your environment change policies and simplifies your operations practices, reducing the risk of human error.
- Monitor utilization metrics like CPU, memory, and storage to help determine if resources are under-utilized and can be scaled down.
- Review nonproduction and production environments for unused resources that can safely be shutdown or deleted.
- Take advantage of tools provided by your cloud platform that help identify resource optimizations. For example, Azure Advisor offers [cost optimization recommendations](/azure/cost-management-billing/costs/tutorial-acm-opt-recommendations) highlighting potential inefficiencies and steps to address them.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Ensure that performance and reliability targets requirements are considered when making adjustments to your resources. Engage with stakeholders when compromises can be made to balance performance, reliability, and costs, or when budget requirements are an obstacle to workload enhancements.

#### &#10003; Optimize licenses and other static costs

As you deploy cloud resources, be strategic in purchasing licenses and other static monthly costs, like server and database licensing, software licensing, and prepurchased reservations. Considerations include:

- Research your options for applying existing licenses to systems that you are migrating into the cloud or for new deployments in the cloud, like [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit). Research any volume licensing or enterprise agreements that your organization already maintains to leverage possible cost savings on cloud resources or software licensing.
- Use nonproduction and dev/test licensing for applicable systems, like Visual Studio Dev/Test licenses.
- Prepurchase capacity for cloud resources when practical. Reserving capacity can significantly lower your monthly utilization costs and help keep your costs more consistent month-to-month.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: When purchasing reservations, be sure that it is the most cost-effective model for your use case. For example, if a given resource can be scaled down nightly or seasonally, it might be more cost effective to intelligently scale that resource. Also, be sure that you've identified the right SKU to pre-purchase through baseline testing and capacity planning.

#### &#10003; Refine autoscaling policies

Your initial scaling policies might be based on your internal development feedback loop - adjusting scaling to meet development needs. As your workload evolves, begin incorporating internal and external user feedback to ensure that performance remains within the acceptable range.  Adjust scaling thresholds and introduce cooldown periods to prevent temporary load spikes. Continuously monitor and fine-tune the system to optimize costs and meet requirements. Define your units of scale that will be used going forward. Units of scale depend on many factors like the workload's overall design and use case, components and flows involved, and business requirements. For example, for mission critical workloads, a unit of scale might be an entire [deployment stamp](/azure/architecture/patterns/deployment-stamp), whereas for simpler workloads with non-critical flows, the unit of scale could simply be based on the number of compute instances of a given SKU that is allocated to the workload. Choose units of scale that allow the workload to handle expected increases in load without wasted capacity. Update your cost model to include forecasted scaling needs.

#### &#10003; Optimize your data estate

Data is a primary driver of cloud costs and proper data management can help you keep costs consistent and within budget. Strategies for data estate management include:

- Classify and label data to apply appropriate controls and determine the appropriate levels of reliability and performance for different data types and stores.
- Capture only essential data, compress data for colder storage, delete unneeded data with proper retention policies, deduplicate to eliminate redundant data, and educate users on efficient data storage practices.
- Optimize backups by using incremental backups, enabling compression, and moving older backups to cold storage.
- Optimize replication by applying appropriate replication models (synchronous vs asynchronous) and replication frequency depending on your requirements. Continously evaluate your requirements and configuration.
- Incorporate internal and external feedback to review storage access patterns and adjust your data lifecycle management strategies.
- Use tooling to help optimize queries.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Weigh your backup and replication policies against your recovery targets. Less frequent replication or offloading older backups to cold storage can affect your recovery time, so be mindful about implementing these optimizations.

#### &#10003; Optimize code, software development practices, and feature development strategies

Inefficient code can lead to inefficient system performance, which can mean higher costs for resource utilization. Optimizing your code can help your workload become more efficient, handling more load without needing to scale up your resources. Strategies to optimize code include:

- Analyze runtime data, measure performance with profiling tools, evaluate business logic and user impact, and review language-specific performance recommendations.
- Optimize by removing unnecessary function calls, minimizing logging, refining loops and conditionals, reducing data processing, minimizing network requests and memory allocations, and assessing cross-cutting implementations.
- Optimize network paths by:
   - Minimizing data transfers between components and across geo-distant regions by analyzing and eliminating unnecessary transfers. Ensure only the required data fields are sent, not entire objects or data structures, to reduce the size and frequency of data transfers.
   - Avoiding transfering redundant data by only sending essential information.
   - Refactoring your code to reduce repeated requests and batch them when possible.
 
Streamlining development practices increases build velocity, saving your workload teams valuable time getting enhancements to production efficiently. Strategies for streamlining development practices include:

- Reduce build times by reviewing build configuration settings and eliminating unnessary steps or processes, parallelizing build tasks, using caching, and employing incremental builds to avoid unnecessary recompilation of unchanged componnents.
- Use production mocking to optimize testing. Mocking allows developers to focus testing on simulated scenarios that are impractical to reproduce in a production environment.
- Optimize development planning practices by defining clear objectives and metrics, using monitoring tools to track KPIs, and prioritizing actionable insights.
- Use AI coding assistance tools, like Github Copilot, to reduce development time when practical.

Evaluate your feature development strategy to ensure that valuable features are prioritized. Incorporate internal and external user and stakeholder feedback to determine which features will have the highest impact on user satisfaction, driving increased utilization. In some cases, features might be developed specifically for optimizing costs. Weigh those against functional priorities to determine their relative value.


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
