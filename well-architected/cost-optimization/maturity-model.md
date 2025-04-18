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

# [**Level 3 - Incorporating signals**](#tab/level3)

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

- Use the right resource types and SKUs for each environment. Research the infrastructure options your cloud platform provides and choose resource types and SKUs that are appropriate for each use case. There are often specific resource types and SKUs that are [targeted specifically for proof-of-concept and Dev/Test environments](https://azure.microsoft.com/en-us/pricing/offers/dev-test/) that are significantly less expensive than standard or high performance resources, but cannot be used for production workloads.
- Configure and enforce policies to limit allowed resource types and SKUs, regions, and users with deployment permissions. Automated policies will help you control changes to your environments, minimizing the risk of unauthorized deployments.
- Standardize using infrastructure-as-code (IaC) for all environment changes. This practice further enforces your environment change policies and simplifies your operations practices, reducing the risk of human error.
- Monitor utilization metrics like CPU, memory, and storage to help determine if resources are under-utilized and can be scaled down.
- Review nonproduction and production environments for unused resources that can safely be shutdown or deleted.
- Take advantage of tools provided by your cloud platform that help identify resource optimizations. For example, Azure Advisor offers [cost optimization recommendations](/azure/cost-management-billing/costs/tutorial-acm-opt-recommendations) highlighting potential inefficiencies and steps to address them.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Ensure that performance and reliability targets requirements are considered when making adjustments to your resources. Engage with stakeholders when compromises can be made to balance performance, reliability, and costs, or when budget requirements are an obstacle to workload enhancements.

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

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Weigh your backup and replication policies against your recovery targets. Less frequent replication or offloading older backups to cold storage can affect your recovery time, so be mindful about implementing these optimizations.

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

# [Level 4 - Refining in production](#tab/level4)

![Goal icon](../_images/goal.svg) Apply learnings from operating in production to achive cost optimizations.

The Level 4 stage of the maturity model assumes that you have deployed into production and have been operating your workload for enough time to find useful information about its normal running state. At this level, you should take these learnings and apply them in terms of environment refinements. 

When you perform any changes in production, you will directly affect other aspects of your workload. So, when you make refinements to achieve cost savings, you will impact one or more of the other Well-Architected Framework pillars. For this reason, having mature change management practices is critical. Thoroughly reviewing and testing changes and developing rollback plans are essential to mitigate performance, security, and reliability impacts when making cost savings changes to your workload.

#### Refine the cost model based on production learnings

Once you have deployed and operated your workload in production for a sufficient time period to understand your normal usage patterns, revisit your cost model to plan for the future. The cost model should project future expenses, allocate funds, and adjust for deviations. In scenario analysis, it evaluates the financial impact of different business changes and anticipates cost implications of customer behavior. For resource optimization, it identifies underutilized cloud resources, forecasts costs of scaling, and compares cloud providers' billing models to choose the most economical option.  

As part of this exercise, determine how closely your running costs align with the previously developed cost model and investigate the causes of discrepancies. Finding unexpected costs is common and assigning blame for these is generally unproductive. Instead, evaluate the value of the components driving unexpected costs and work with stakeholders to determine whether the workload design needs to be modified to better align with the cost model, or whether the cost model should be modified. 

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Keep in mind that it's difficult to accurately project variable costs, especially when you adopt new technologies, so you might have to look for compromises when planning your budget. Make sure stakeholders understand how you've arrived at your estimates for variable costs and how you plan to keep these costs under control.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Cost-cutting measures can adversely affect reliability, security, performance, and operational excellence. Carefully analyze the impact of every planned cost-cutting measure and determine if making a given change is worth the potential impact.

#### Optimize your data usage for costs

Use your production learnings to refine your data strategies. In particular, look for optimization opportunities for the following factors:

- Optimize your data lifecycle management. Review your data tiering and make adjustments where practical. You might have data that is no longer being actively used and can be moved to less expensive storage tiers, like archive storage. Likewise, if you haven't already created policies to define what types of data belong in different tiers, creating those based on production learnings will help the on-going management of your data lifecyle more efficient. Finally, implement automation to enforce these policies to further optimize your efforts.
- Optimize your storage solutions considering ease of management. Evaluate whether the storage solutions you have deployed are the best fit for your use case and whether changing to a different technology can save you operational burden or utilization costs. For example, you might have initially deployed SQL Server on VMs to easily migrate your workload or because that is the technology your workload team was most familiar with. Moving to a platform as a service (PaaS) service can significantly reduce your operational burden and save you costs, depending on certain factors.
- Optimize your use of file formats. Certain file formats, like Avro, Parquet, and Optimized Row Columnar (ORC) are best suited for [particular input/output (I/O) patterns and query patterns](./optimize-data-costs.md#optimize-file-formats) Determine whether you should be using specific file formats for your use cases. 

#### Increase resource density

When practical, you can optimize costs by consolidating services on shared resources. For example, you can host multiple web apps on a single server or PaaS instance. Databases, network appliances, gateways, and distributed denial-of-service (DDoS) protection can also be good candidates for consolidation. Perform a cost-benefit analysis to determine whether consolidating resources is valuable enough for your team to perform. After identifying candidates for consolidation, follow good change management practices to properly plan for migrating the target systems. Ensure stakeholders understand the downtime risks involved in the migration and test the migration in nonproduction environments if possible. Develop a rollback plan to deal with migration issues. 

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Consolidating resources can introduce single points of failure, making your workload less reliable and they can increase your security risks by decreasing segmentation. Mission-critical and business-critical workloads should favor segmentation over consolidation.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Poorly executed consolidation can lead to future inefficiencies. Review your consolidation plan thoroughly to ensure that you're not creating bottlenecks that can affect you in the future prior to performing the migration. Following the migration, pay extra attention in your monitoring to the consolidated resources to ensure they're performing as expected.

#### Evaluate and optimize your application features

Observe feature usage patterns in production and evaluate the value that each one has to your business. Use this analysis to determine whether a given feature is worth continued investment in or if it is a candidate for deprecation. In some cases, monetizing features might be a good business decision. These features might not be worth the current investment, but are valuable to customers, so offering them as a paid add-on or subscription can offset the investment costs.

#### Control demand against supply to minimize scaling costs

A strategy that can help you optimize your resource scaling costs is controlling demand against your supply. This approach can involve offloading demand to other resources or reducing demand through various strategies like priority queues, gateway offloading, buffering, and rate limiting. Caching, content offloading, load balancing, and database offloading are all examples of offloading demand. When considering any of these tactics, analyze how implementing it can impact other pillars of the Well-Architected Framework. Review the [Cost Optimization design patterns](./design-patterns.md) for examples of reducing demand. These patterns help minimize resource utilization and avoid overprovisioning. Additionally, controlling supply by capping scaling limits and establishing a budget ensures expenses remain within defined thresholds. Using cost management platforms, budget alerts, and tracking usage can help monitor and control spending effectively.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Offloading tasks to other resources can reduce scaling costs, but it may introduce operational and maintenance challenges. Conduct a thorough cost-benefit analysis to ensure the chosen offloading method is both efficient and feasible, balancing savings with potential complexities.

#### Enforce cost accountability through ownership

Appointing a cost DRI (Directly Responsible Individual) can help the workload team take ownership of their workload costs.  The DRI is the person accountable for managing and optimizing cloud expenses. This role involves monitoring cloud usage, implementing cost-saving strategies, and ensuring that the organization's cloud spending aligns with its budget and business objectives. The DRI is responsible for making decisions related to cloud resource allocation, identifying areas of potential cost reduction, and ensuring efficient use of cloud services to avoid unnecessary expenses.

The DRI also acts as the first line of defense for higher than expected workload costs. He or she can help identify the cause of high costs and proactively work with the workload team to reduce them back to their expected range before they become a cost emergency.

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
