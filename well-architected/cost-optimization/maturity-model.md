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

#### &#10003; Share financial goals, budget details, and workload spend with all team members, managers, and decision-makers

Provide stakeholders with detailed information about the allocated budget, cost breakdowns, and financial goals for your workload. Offer detailed insights into various expenses, such as infrastructure costs, software licenses, and operational expenses. This is a practice that is **not often thought of, but is important** in building trust between the workload team and leadership teams. Sharing these insights gives leadership peace of mind that the workload team is thinking ahead about how they will manage their expenses. It also helps motivate the workload team to remain diligent in their cost management because they have made commitments to leadership.

#### &#10003; Define processes and tools for sharing cost-saving ideas and knowledge

Use collaboration tools to encourage team members to share cost optimization ideas and insights. Providing mechanisms for the team to collaborate on their ideas and insights is a great motivational tool as it empowers team members to offer suggestions that cut across all disciplines of the workload team. By fostering a culture of collaboration on cost optimization, you can reinforce the mindset that managing the workload costs relies on every team member equally.

To help motivate adopting a cost optimization mindset, consider acknowledging individuals and teams who demonstrate financial responsibility and contribute to cost optimization through performance evaluations, incentives, or other recognition programs.

#### &#10003; Gather detailed cost data from all sources, including invoiced (actual billed) and metered (predictive based on billing plans) data

Explore all of the available tools and methods provided by your service provider to compile a fully detailed picture of your workload's costs. Relying only on the monthly bill might not give stakeholders enough details about spending. Compile data to save time in investigating vague items and explaining obscure terms. After you've gathered utilization data, centralize that data into a unified system.

Take advantage of billing dashboards that can be filtered for different views to make it easier for stakeholders and workload team members to see the information that's most relevant to them.

#### &#10003; Determine the cost drivers

Using the tools you've adopted to gather billing data, you can identify how different workload components contribute to the overall costs. Pay special attention to costs related to processes, like data transfer or transactions, contribute to the overall costs as these are often not considered when estimating resource costs before deployment. 

You should also understand how factors not directly related to your cloud resources can affect your budget. This can include training for team members, and software licensing not managed by your cloud provider.

#### &#10003; Invest in your team's skills

Invest in upskilling where team knowledge is currently lacking. Strong cloud skills facilitate good long-term decision making and optimize your daily productivity. Consider training or certifications offered by your cloud provider and other third-parties. Enhancing the productivity of your workload team and other decision-makers will save you time and money by minimizing costly mistakes.

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

# [**Level 3: Incorporate signals**](#tab/level3)

![Goal icon](../_images/goal.svg) **Refine cost optimization strategies by gathering and incorporating feedback from users and stakeholders.**

In the early stages of workload development and operations, you might rely on your internal development feedback loop to optimize your workload. At this maturity level, it's important to expand your feedback loop to include both internal and external users, as well as other stakeholders. As you make cost optimization refinements to your workload, you also need to consider trade-offs with other pillars, especially the Performance Efficiency and Reliability pillars.

Level 3 of the Cost Optimization pillar focuses on preparing your workload for production by integrating both internal and external feedback into your strategy while ensuring that performance and reliability targets are met.

#### &#10003; Invest in impactful flows and fine-tune other flows

Consider the priorities assigned to your application flows. Prioritize development efforts on critical flows while you look for ways to simplify and reduce the cost of non-critical flows. Non-critical flows might have less stringent reliability requirements. As a result, you might be able to simplify their design.

For each flow, eliminate unnecessary elements, select the appropriate performance tiers, adjust scaling settings for variable demand, and fine-tune configurations to align with performance and budget needs. Monitor flows to identify inefficiencies such as idle compute instances, unused data, and low network bandwidth. Use native logging and analysis tools to help aggregate and analyze these metrics for trends.

#### &#10003; Enforce cost guardrails by using alerting

Create spending thresholds and associated alerting to enforce cost guardrails. This approach ensures that the workload stays within budget. Examples of alerts include the following types:

- Budget alerts allow you to set spending thresholds, monitor costs, and receive notifications to help control expenses and stay informed.

- Cost anomaly alerts notify you of unexpected cost variations. They enable you to investigate and address inefficiencies or abnormal spending patterns.

- Commitment-based plan usage alerts help you monitor and optimize the usage of your commitment-based resources by notifying stakeholders when usage drops below a desired threshold.

#### &#10003; Develop a strategy to optimize your resource usage

As you build out your nonproduction and production environments, you might unintentionally deploy overprovisioned resources. Over time, you might accumulate resources that were deployed for a proof of concept (PoC) or for testing that are no longer needed. Developing a strategy to identify underused and unused resources is essential for continuous improvement in maintaining an optimized cloud environment. Consider the following recommendations:

- Use the correct resource types and SKUs for each environment. Research the infrastructure options that your cloud platform provides and select resource types and SKUs that are appropriate for each use case. There are often specific resource types and SKUs that are targeted specifically for [PoC and dev/test environments](https://azure.microsoft.com/pricing/offers/dev-test/). These resource types and SKUs are significantly less expensive than standard or high-performance resources, but you can't use them for production workloads.

- Configure and enforce policies to restrict resource types, SKUs, regions, and deployment permissions for users. Automated policies help manage environment changes. This process reduces the risk of unauthorized deployments.

- Standardize by using infrastructure as code for all environment changes. This approach helps enforce your environment change policies and simplifies your operations practices to reduce the risk of human error.

- Monitor usage metrics like CPU, memory, and storage to help determine if resources are underused and can be scaled down.

- Review nonproduction and production environments for unused resources that can be safely shut down or deleted.

- Take advantage of tools that your cloud platform provides to help identify resource optimizations. For example, Azure Advisor provides [cost optimization recommendations](/azure/cost-management-billing/costs/tutorial-acm-opt-recommendations) that identify potential inefficiencies and suggest steps to address them.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Consider performance and reliability targets when you adjust your resources. Engage with stakeholders when compromises can help balance performance, reliability, and costs, or when budget constraints hinder workload enhancements.

#### &#10003; Optimize licenses and other static costs

As you deploy cloud resources, take a strategic approach to purchasing licenses and other fixed monthly costs. These costs include server and database licensing, software licensing, and prepurchased reservations. Consider the following cost-saving strategies:

- Research options, like [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit), for applying existing licenses to the systems that you migrate to the cloud or for new deployments in the cloud. Research any volume licensing or enterprise agreements that your organization maintains to take advantage of possible cost savings on cloud resources or software licensing.

- Use nonproduction and dev/test licensing for applicable systems, like dev/test licenses from Visual Studio.

- Prepurchase capacity for cloud resources when practical. Reserving capacity can significantly reduce your monthly usage costs and help maintain more consistent expenses.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** When you purchase reservations, ensure that you select the most cost-effective model for your use case. For example, if a resource can be scaled down nightly or seasonally, it might be more cost-effective to adjust its scaling intelligently. Also, identify the correct SKU to prepurchase through baseline testing and capacity planning.

#### &#10003; Refine autoscaling policies

Your initial scaling policies might be based on your internal development feedback loop. These policies focus on adjusting scaling to meet development needs. As your workload evolves, incorporate both internal and external user feedback to ensure that performance remains within the acceptable range.

Adjust scaling thresholds and introduce cooldown periods to mitigate temporary load spikes. Continuously monitor and fine-tune the system to optimize costs and meet requirements. Define the units of scale to use moving forward. These units depend on factors such as workload design, use case, components, flows, and business requirements.

For example, for mission-critical workloads, a unit of scale might be an entire [deployment stamp](/azure/architecture/patterns/deployment-stamp). Alternatively, for simpler workloads that have non-critical flows, the unit of scale might be based on the number of compute instances of a specific SKU that's allocated to the workload. Select units of scale that allow the workload to handle expected increases in load without wasted capacity. Update your cost model to include forecasted scaling needs.

#### &#10003; Optimize your data estate

Data is a primary driver of cloud costs. Proper data management can help you keep costs consistent and within budget. Use the following strategies to manage your data estate:

- Classify and label data to apply the appropriate controls and determine the appropriate levels of reliability and performance for different data types and stores.

- Capture only essential data, compress data for colder storage, and delete unneeded data by using proper retention policies. Deduplicate data to eliminate redundant data and educate users on efficient data storage practices.

- Optimize backups by using incremental backups, enabling compression, and moving older backups to cold storage.

- Optimize replication by applying the appropriate replication models, whether synchronous or asynchronous, to optimize replication based on your requirements. Adjust the replication frequency to align with your specific needs. Continuously evaluate your requirements and configuration.

- Incorporate internal and external feedback to review storage access patterns and adjust your data life cycle management strategies.

- Use tooling to help optimize queries.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Compare your backup and replication policies against your recovery targets. Less frequent replication or offloading older backups to cold storage can affect your recovery time, so be mindful about implementing these optimizations.

#### &#10003; Optimize code, software development practices, and feature development strategies

Inefficient code can degrade system performance. This inefficiency can result in higher costs for resource usage. Optimizing your code can improve efficiency and allow your workload to handle more load without needing to scale up your resources. Use the following strategies to optimize your code:

- Analyze runtime data and measure performance by using profiling tools.

- Evaluate business logic and its effect on user experience.

- Review language-specific performance recommendations.

- Remove unnecessary function calls, minimize logging, and refine loops and conditionals.

- Reduce data processing, minimize network requests and memory allocations, and assess cross-cutting implementations.

Use the following methods to optimize network paths:

- Minimize data transfers between components and across geo-distant regions by analyzing and eliminating unnecessary transfers. Ensure that only the required data fields are sent and not entire objects or data structures. This approach reduces the size and frequency of data transfers.

- Avoid transferring redundant data by only sending essential information.

- Refactor your code to reduce repeated requests and batch them when possible.

Streamlining development practices enhances build velocity. This approach helps workload teams save valuable time and efficiently deliver enhancements to production. Use the following strategies to streamline development practices:

- Speed up build times by optimizing configuration, removing unnecessary steps and processes, parallelizing tasks, using caching, and enabling incremental builds to prevent redundant compilation.

- Use production mocking to optimize testing. Production mocking allows developers to focus testing on simulated scenarios that are impractical to reproduce in a production environment.

- Optimize development planning practices by defining clear objectives and metrics, using monitoring tools to track key performance indicators, and prioritizing actionable insights.

- Use AI coding assistance tools, like GitHub Copilot, to reduce development time when practical.

Evaluate your feature development strategy to ensure that valuable features are prioritized. Incorporate feedback from both internal and external users and stakeholders to identify the features that most enhance user satisfaction and drive higher engagement. In some scenarios, features might be designed specifically to optimize costs. Compare these features with functional priorities to assess their relative value.

# [**Level 4: Applying production learnings**](#tab/level4)

![Goal icon](../_images/goal.svg) Apply learnings from operating in production to achieve cost optimizations.

The Level 4 stage of the maturity model assumes that you have deployed into production and have been operating your workload for enough time to gather useful information about its normal running state. At this level, you should apply these learnings to your environment.

When you perform any changes in your production environment, you will directly affect other aspects of your workload. So, when you make refinements to achieve cost savings, you will impact one or more of the other Well-Architected Framework pillars. For this reason, having mature change management practices is critical. Thoroughly reviewing and testing changes and developing rollback plans are essential to mitigate performance, security, and reliability impacts when making cost savings changes to your workload.

#### &#10003; Refine the cost model based on production learnings

Once you have deployed and operated your workload in production for a sufficient time period to understand your normal usage patterns, revisit your cost model to plan for the future. The cost model should project future expenses, allocate funds, and adjust for deviations. In scenario analysis, it evaluates the financial impact of different business changes and anticipates cost implications of customer behavior. For resource optimization, it identifies underutilized cloud resources, forecasts costs of scaling, and compares cloud providers' billing models to choose the most economical option.  

As part of this exercise, determine how closely your running costs align with the previously developed cost model and investigate the causes of discrepancies. Finding unexpected costs is common and assigning blame for these is generally unproductive. Instead, evaluate the value of the components driving unexpected costs and work with stakeholders to determine whether the workload design needs to be modified to better align with the cost model, or whether the cost model should be modified. 

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Keep in mind that it's difficult to accurately project variable costs, especially when you adopt new technologies, so you might have to look for compromises when planning your budget. Make sure stakeholders understand how you've arrived at your estimates for variable costs and how you plan to keep these costs under control.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Cost-cutting measures can adversely affect reliability, security, performance, and operational excellence. Carefully analyze the impact of every planned cost-cutting measure and determine if making a given change is worth the potential impact.

#### &#10003; Optimize your data usage for costs

Use your production learnings to refine your data strategies. In particular, look for optimization opportunities for the following factors:

- Optimize your storage solutions considering ease of management. Evaluate whether the storage solutions you have deployed are the best fit for your use case and whether changing to a different technology can save you operational burden or utilization costs. For example, you might have initially deployed SQL Server on VMs to easily migrate your workload or because that is the technology your workload team was most familiar with. Moving to a platform as a service (PaaS) service can significantly reduce your operational burden and save you costs, depending on certain factors.
- Optimize your data lifecycle management. Review your data tiering and make adjustments where practical. You might have data that is no longer being actively used and can be moved to less expensive storage tiers, like archive storage. Likewise, if you haven't already created policies to define what types of data belong in different tiers, creating those based on production learnings will help the ongoing management of your data lifecycle more efficient. Finally, implement automation to enforce these policies to further optimize your efforts.

#### &#10003; Increase resource density

When practical, you can optimize costs by consolidating shared services across workloads. For example, you can host multiple web apps on a single server or PaaS instance. Database, networking, security and other services can be shared by multiple workloads. Perform a cost-benefit analysis to determine whether consolidating resources is valuable enough for your team to perform. After identifying candidates for consolidation, carefully plan the implementation and follow good change management practices. Ensure stakeholders understand the risks.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Consolidating resources can introduce single points of failure, making your workload less reliable and they can increase your security risks by decreasing segmentation. Mission-critical and business-critical workloads should favor segmentation over consolidation.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Poorly executed consolidation can lead to future inefficiencies. Review your consolidation plan thoroughly to ensure that you're not creating bottlenecks that can affect you in the future prior to performing the migration. Following the migration, pay extra attention in your monitoring to the consolidated resources to ensure they're performing as expected.

#### &#10003; Evaluate and optimize your application features

Observe feature usage patterns in production and evaluate whether features align with your cost model. Determine whether a given feature is worth maintaining, refactoring, or removing (deprecating). In some cases, monetizing features might be a good business decision. These features might not be worth the current investment, but are valuable to customers, so offering them as a paid add-on or subscription can offset the investment costs.

#### &#10003; Minimize scaling costs by regulating demand

A strategy that can help you optimize your resource scaling costs is regulating demand instead of adding capacity. This approach can involve offloading demand to other resources or reducing demand through various strategies like priority queues, buffering, load balancing, caching, and others. When considering any of these tactics, analyze how implementing it can impact other pillars of the Well-Architected Framework. Additionally, controlling supply by capping scaling limits and establishing a budget ensures expenses remain within defined thresholds. Track utilization and use cost management platforms and budget alerts to help monitor and control spending effectively.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Offloading tasks to other resources can reduce scaling costs, but it may introduce operational and maintenance challenges. Conduct a thorough cost-benefit analysis to ensure the chosen offloading method is both efficient and feasible, balancing savings with potential complexities.

#### &#10003; Enforce cost accountability through ownership

Appointing a cost DRI (Directly Responsible Individual) can help the workload team take ownership of their workload costs.  The DRI is the person accountable for managing and optimizing cloud expenses. This role involves monitoring cloud usage, implementing cost-saving strategies, and ensuring that the organization's cloud spending aligns with its budget and business objectives. The DRI is responsible for making decisions related to cloud resource allocation, identifying areas of potential cost reduction, and ensuring efficient use of cloud services to avoid unnecessary expenses.

The DRI also acts as the frontline of defense for higher than expected workload costs. He or she can help identify the cause of high costs and proactively work with the workload team to reduce them back to their expected range before they become a cost emergency.

# [**Level 5: Advanced cost efficiency techniques**](#tab/level5)

![Goal icon](../_images/goal.svg) Refine the workload and operational processes to achieve additional cost efficiencies.

At Level 5 of the maturity model, you should have many cost optimization mechanisms and processes in place already. Level 5 focuses on maximizing your return on investment (ROI) by maintaining a consistent and predictable workload budget, producing highly accurate forecasts, and through advanced refinements. Consider the following recommendations to achieve these goals.

#### &#10003; Enforce spending guardrails in your software development processes

Use release gates to serve as spending guardrails by establishing cost-related criteria that must be met to pass the gate. For example, you can set spending limits to ensure that releases don't add unexpected costs to your workload budget. Incorporate these gates into your CI/CD pipeline to ensure they're included in every deployment.

#### &#10003; Invest in knowledge building

Empower your workload team to take ownership of cost optimization initiatives by developing a skills training program. Encourage team members to learn from industry thought leaders by attending conferences, webinars, and other industry events. Provide in-house training to cross-train skills and provide sandbox environments for hands-on learning experiences. Tie trainings back to cost optimization initiatives like optimizing scaling strategies, for example. Having highly skilled teams makes them more efficient and might help you minimize engaging third parties for new projects.

Ensure that your cost model includes planned trainings for a given fiscal period, like the upcoming quarter.

#### &#10003; Optimize high availability and disaster recovery (DR) costs

After running real-world DR drills or after going through real-world DR incidents, you might find areas for cost optimization. You might find that you can afford a less expensive DR strategy for certain components while still meeting your recovery targets. For example, you might not need a hot spare design for a non-critical flow, so you can consider implementing a deploy on recovery approach instead. 

As part of your continuous improvement practices, regularly revisit your recovery targets to make sure they are appropriate for both reliability and cost requirements.

#### &#10003; Refine the workload design

After observing your workload in production for a significant amount of time, you might decide that you've reached the limits of optimizations that are worth investing in without refactoring elements of it. Refactoring can be a very expensive effort in terms of labor involved, but can be worth the investment if it offers long term cost savings along with extending the lifecycle of the workload by adopting more efficient technologies and management requirmeents. Consider the following strategies:

- *Combine similar flows.* Reduce reduncancies or underutilized resources by combining similar flows onto the same resource or set of resources. For example, you can host multiple web apps on a single compute instance, or you can host multiple databases on a single logical server.

- *Separate dissimilar flows.* Separating tasks with different computational needs onto dedicated resources improves efficiency and reduces costs. This process enhances scalability, fault tolerance, and adaptability by minimizing interference and optimizing resource allocation based on each task's priority.

- *Rearchitect the workload to gain efficiencies.* Review the entire workload architecture to look for opportunities to improve efficiency. Favor a microservices design and explore using serverless or managed services that relieve operational burden and can easily be right-sized through automatic scaling. An end goal for your architecture might be a point where you can automatically deploy nonproduction environments when they are needed for deployment purposes and destroy them afterwards, minimizing any wasted utilization costs.

Continuously monitor and adjust resource sizes based on usage patterns to optimize costs.

#### &#10003; Evolve your team's operations

Research and adopt efficient development methodologies like Scrum, Kanban, and waterfall. Reassess your team's efficiency regularly to determine if your chosen methodology is the best fit. Determine the costs of tasks per employee (known as unit costs) and look for opportunities to lower those costs. Look at the most expensive tasks and evaluate their return on investment. 

Determine whether tasks can be offloaded to other teams. For example, you might have a centralized cloud team that handles some operational tasks for other business units or workloads, or a centralized security team that handles security monitoring and testing. When you find opportunities to offload tasks, carefully plan the handoff with clear communications across involved teams and follow good change management processes.

---

## Next steps
<!-- Provide at least one next step and no more than three. Include some 
context so the customer can determine why they would click the link.
-->
