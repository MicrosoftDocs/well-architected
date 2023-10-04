---
title: Recommendations for eliminating waste
description: Learn how to eliminate waste from a workload.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for eliminating waste: CO 07

This guide describes the recommendations for eliminating workload waste. The guidance applies to an existing workload, not one in the design phase. Waste reduction is fundamental for both financial savings and operational efficiency. You can reduce waste removing unused and underutilized workload components. It's also important to spot and address underutilized, prepaid commitments to further eliminate wastage. To ensure the highest return on investment, workload components and application features should be actively delivering value, and prepaid reservations and savings plans should be maximized.

**Definitions**

| Term | Definition  |
|----|----|
| Application feature | A distinct capability within the application software that enables users to perform specific tasks or access specific information.|
| Platform feature | A specific functionality or capability provided by a platform. It can vary depending on the platform, but generally, platform features are designed to enhance the user experience, improve productivity, or enable specific tasks or actions. |
| Resource| **!!use canonical definition!!** |

## Key strategies

Cut down on workload waste by making sure you only pay for the computer resources and features you actually use. Keep track of what you're using and adjust your plans accordingly to save money. Regularly review and update how you manage these resources to get the most value for your money. This way you use what you have more effectively and cut down on costs.

### Eliminate waste from the application

An application feature refers to a specific functionality or capability of application software that serves a specific purpose or provides a specific service to the users. Identify and eliminate features that aren't providing value to the workload or aren’t worth their current investment. Removing unnecessary or underutilized features reduces costs associated with maintaining and supporting them. It allows you to allocate resources more efficiently to features that bring more value to the users and the business.

**Evaluate the value.** To determine the value of a feature, consider its impact on the overall application and the value it provides to the customers. Some factors to consider include:

- *Customer needs*: Assess how well the feature meets the needs and expectations of the customers. Customer feedback, surveys, and usage data can be valuable in understanding the perceived value.
- *Business goals*: Evaluate how the feature aligns with the strategic objectives of the business. Consider how it supports revenue generation, customer satisfaction, or competitive advantage.
- *Impact on user experience*: Determine the impact the feature has on enhancing the user experience and improving usability or productivity.
- *Differentiation*: Assess whether the feature provides a unique selling point or competitive advantage compared to other applications in the market.

**Evaluate the cost.** Understanding the cost associated with each feature is essential for effective resource allocation and optimization. The cost evaluation involves considering various aspects, such as:

- *Development effort*: Assess the time, resources, and expertise required to develop and maintain the feature or surrounding features. Underutilized features often become a key source of technical debt.
- *Maintenance and support*: Consider the ongoing costs associated with maintaining and supporting the feature, including bug fixes, security updates, and troubleshooting.
- *Infrastructure and resource utilization*: Evaluate the impact of the feature on infrastructure requirements, including server resources, storage, and bandwidth.
- *Integration complexity*: Assess the complexity and cost of integrating the feature with other systems or third-party services.
- *Performance considerations*: Evaluate the impact of the feature on the application's performance, including scalability, response time, and resource usage.

**Seek input.** Get everyone involved when you're trying to figure out if an application feature is worth the cost and effort. Talk to people like product managers, software developers, and business analysts. Collect data and study it to understand how a feature affects your business goals. Your development team can give you important information about how much work it takes to maintain certain features. Encourage them to speak up about features that might be more trouble than they're worth, especially if these features distract from creating new ones.

**Determine the future of the feature**. Based on the analysis and evaluation, determine the future of the application features. Remove, reinvest, or monetize any application feature not providing a return on investment.

- *Remove the application feature.* Consider the planned end of life of an application feature based on data. Reasons for removing the feature may include low customer demand, high maintenance costs, complexity, or redundancy that’s not worth the effort to fix. Create a plan for removing the identified features. It may involve refactoring the code, updating dependencies, or reorganizing the user interface.

    :::image type="icon" source="../_images/risk.svg" ::: **Risk:** You could inadvertently remove features that are critical for certain users or scenarios and may negatively impact performance, security, operations, and security.

- *Reinvest*: Some application features might not be adding enough value in its current state but could add value if you reinvest in them. Reinvestment means reworking or promoting the application feature. Prioritize the identified improvements based on their value and feasibility. Determine the roadmap and timeline for implementing the changes, considering factors such as development resources, dependencies, and potential impact on the application.
- *Monetize*: Monetizing an application feature involves turning it into a revenue-generating opportunity. Sometimes features might provide value to users but aren’t worth the current investment. Explore opportunities to monetize these and other features, such as offering them as separate paid add-ons or licensing them to other companies.

### Eliminate waste from workload resources

Eliminating waste from workload resources means to remove any unused or underutilized resources in a workload across its various environments. It also includes optimizing idle resources in disaster recovery environments.

**Remove unused workload resources.** Unused resources are deployed services your workload or operations processes don't use. These resources might be long-term idled, orphaned, or forgotten. They provide no return on investment, and you should remove them. Common causes of resources waste include:

- Storage accounts
- Snapshots
- IP addresses
- Network firewalls
- Alerts
- Proof of concept
- Demo builds
- Temporary triage environments
- Temporary testing environments
- Feature decommissioning
- Environment decommissioning

To remove unused resources in a workload, consider these steps:

- *Take an inventory*: Conduct a thorough inventory of all resources within the workload across environments.
- *Find orphaned resources*: Resources can become orphaned when they're no longer needed or when their parent resources are deleted. For example, you delete a virtual machine, but its associated storage account isn't. Review your resources to identify resources that are no longer needed or have become orphaned.
- *Remove idle component*: There’s typically a cost associated with a deployed resource. Even if the resource allows you to stop or deallocate, you might be paying for the resource. Consider removing idle resources. If you need the data, back up the data first and then remove the resource. You’re better off redeploying the resource and restoring the data than allowing the resource to remain idle.

**Evaluate underutilized resources.** Underutilized resources represent wasted expenditure as you pay for resource capacity that isn't fully utilized. By identifying and optimizing these resources, you can reduce costs and allocate resources more effectively. To evaluate and optimize the cost of underutilized resources, follow these steps:

- *Resource monitoring*: Use tools to keep an eye on how much CPU, memory, and storage you're actually using. Pick the best plan that matches your needs based on this info.
- *Utilization analysis*: Look at the data to find out which resources you're not using much. Pay special attention to the resources that have low usage over a long time or large differences between busy and slow times.
- *Right-sizing*: For resources you're not using fully, see if you've allocated too much to them. If so, adjust their size to better match what you actually need.
- *Autoscaling*: Use autoscaling to automatically adjust how much resources you're using based on how busy you are. But set a maximum limit to avoid sudden spikes that could be costly and unnecessary.

Once you've made these adjustments, test to make sure everything still works as it should. Continuously monitor resource utilization and adjust resource allocation as workload demands change over time. Regularly review and optimize resource utilization to maintain cost efficiency and performance optimization.

**Eliminate disaster recovery waste.** Warm (active-passive) disaster recovery plans are a common source of underutilization. One environment receives all the load while the other environment is idle, awaiting a situation you hope never happens. Consider hot (active-active), cold (active-off), and active-redeploy approach can help avoid underutilized resources.

- *Hot (active-active)*: The primary and secondary environments actively serve traffic, allowing you to use cheaper resources and minimizing unused capacity.
- *Cold (active-off)*: A cold disaster recovery plan is a failover solution where the standby environment isn't actively running until there's a need for failover. The production environment is replicated as backups, VM images, or Resource Manager templates to a different region. This approach is cost-effective but takes longer to perform a complete failover.
- *Active-redeploy*: The active-redeploy approach uses infrastructure-as-code to deploy resources to the secondary environment in a failover event. Before a failover event, there are no deployed resources in the disaster recovery environment.

### Eliminate waste from platform feature set

Eliminating wasteful platform features involves identifying and removing unnecessary or unused features to optimize resource utilization and reduce costs. Here are some steps to eliminate wasteful platform features:

- *Know the capabilities of the things you’ve purchased*: Understand the features and functionalities of the platforms or services you have acquired.
- *Disable unused features*: Identify and disable platform features that cost money and aren't utilized.
- *Use the right features*: Newer versions of a service can provide similar performance for the same price. For example, a virtual machine with hardware updates could provide the same performance for less.
- *Use the right configurations*: You might be paying for more availability or performance than you need. Eliminate availability or performance guarantees the workload doesn’t need.
- *Eliminate unneeded automation*: Evaluate your automation processes and eliminate any unnecessary automation that may incur extra costs.
- *Eliminate tool redundancy*: Get rid of tools that you don't really need or that do the same thing as other tools you already have. Consider tools used for building software, writing code, security, or monitoring how things are running. For instance, if you're already using GitHub actions to build your software, you don't need to buy another tool that does the same thing. Before you buy more features or tools, check if what you already have can do the job. This way, you avoid wasting money and make the most of what you already have.

### Eliminate waste from operational processes

Cut out unnecessary steps in your work process to save time and money. Look for tasks that you're doing too much or don't really need, like excessive security tests. Focus on the biggest time-wasters first. You can make things more efficient by simplifying how you work, automating repetitive tasks, or changing who does what. For example, you could do fewer security tests or make them less detailed to save resources.

### Eliminate waste from prepaid agreements

Maximizing the usage of prepaid plans starts with buying the right plan and fully utilizing it. Strategic usage of reservations and savings plans can significantly minimize costs for applicable resources. It allows you to effectively plan and allocate resources, optimize spending, and achieve significant cost reductions.

- *Monitor utilization*: Keep an eye on how much you're using your prepaid plans. Set up alerts to tell you if you're not using all of your reserved resources. Check how you're using them over time and get rid of any you're not using. Make sure you're using the right size of virtual machines to get the most out of your plan. You can also adjust the sizes to fit what you've already paid for.
- *Modify the prepaid plan*: Consider changing the scope of the reservation to share, allowing it to apply more broadly across your resources. It can help increase utilization and maximize savings. If you find underused prepaid plans, try exchanging unused quantity or canceling and refunding plans.
- *Review prepaid plans*: Regularly review and analyze your charges and usage data. Understand the breakdown between actual cost and amortized costs and reconcile the data to ensure accurate billing.

### Avoid introducing waste

The best way to get rid of waste is to avoid it in the first place. You want to use strategies that prevent it from being introduced and allow you to catch waste faster. Audit processes to avoid introducing waste. To help avoid introducing waste, consider these strateiges:

- *Find the root cause before changing solutions*: Before you fix a problem, make sure you know what's actually causing it. For example, if your website is slow, don't just switch to a new system. First, figure out why it's slow. You might find out that the real issue is something else, like bad database queries. Fixing the real problem saves you time and money.
- *Apply metadata* Applying metadata provides a way to organize and track resources. You can categorize and group resources, making it easier to track, delete, and avoid orphaned resources. Create a consistent metadata strategy and apply consistently across resources. Consider adding owners, anticipated resource duration (`sunset-30d`), and others.
- *Document nonstandard changes*: Documenting any changes made to your infrastructure or configurations performed outside of the normal change control process of your workload can cut unexpected costs. For example, you might increase a resource’s scaling (up or out) capacities to meet a short-term demand or triage an issue but forget to scale it back down. Make a list of nonstandard changes and use it as a reminder to revert the changes when no longer necessary.
- *Keep things simple*: Simplifying your infrastructure and minimizing complexity can help reduce costs. Focus on using only the necessary resources and services that meet your requirements.

## Azure facilitation

**Eliminating unused application features**: Azure Monitor and Azure Application Insights helps you monitor the usage of your application and identify areas what areas are being used and which ones aren't. Based on the insights gathered, you can make informed decisions to remove or optimize unused or underutilized features.

**Eliminating unused resources**: Azure Advisor provides recommendations to identify and eliminate unused resources. It analyzes your resource usage and provides suggestions on resources that can be safely deleted or scaled down.

**Finding orphaned resources:** Azure Monitor supports Workbooks. These workbooks allow you to create custom reports that can find and report orphaned resources across a defined scope.

**Eliminating unused reservations and savings plans**: To eliminate unused reservations and savings plans, you can use the Azure Cost Management and Billing tools. They provide insights into your reservation and savings plan usage, allowing you to identify any unused or underutilized commitments and make adjustments accordingly. Utilization can be viewed in the Azure portal under the Reservations section.

**Eliminating idle resources in disaster recovery environments**: Azure load balancers can distribute load across availability zones and regions. These load balancers can help eliminate waste in disaster recovery approaches. You can use Azure Automation and Azure Logic Apps to schedule resource shutdowns during periods of inactivity. It can help reduce costs by minimizing the usage of idle resources. Azure Autoscale allows you to automatically scale your application based on predefined conditions, so you don’t have to overprovision capacity. It helps ensure that resources are allocated efficiently and cost-effectively.
