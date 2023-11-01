---
title: Recommendations for optimizing component costs.
description: Learn how to optimize component costs.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing component costs: CO 07

**Applies to this Azure Well-Architected Framework Cost Optimization checklist recommendation:**

| [CO:07](checklist.md) | Optimize component costs. Regularly remove or optimize legacy, unneeded, and underutilized workload components, including application features, platform features, and resources.|
|---|---|

This guide describes the recommendations for optimizing workload component costs. Optimizing component costs refers to the process of evaluating and improving the cost-efficiency of individual elements within a workload. It emphasizes the continuous review and potential removal or improvement of outdated, unnecessary, or rarely used components, such as application features, platform features, and resources. It also covers cost optimization of disaster recovery environments and how to avoid introducing unoptimized components. The guidance applies to an existing workload, not one in the design phase. Neglecting regular component optimization can lead to inflated costs, resource waste, and inefficient workloads that drain both time and money.

**Definitions**

| Term | Definition  |
|----|----|
| Application feature | A distinct capability within the application software that enables users to perform specific tasks or access specific information.|
| Platform feature | A specific functionality or capability provided by a platform. It can vary depending on the platform, but generally, platform features are designed to enhance the user experience, improve productivity, or enable specific tasks or actions. |
| Resource| A single entity or component that you can create, configure, and utilize within a cloud service provider. |

## Key design strategies

Optimizing workload components is about refining the various elements of a workload, including application features, platform capabilities, and resource. The goal is to ensure the workload uses all components efficiently and cost-effectively. Strategies include removing, modifying, and avoiding components that cause you to spend more than you need. The component cost optimization process ensures you allocate resources to features and components that deliver the most value, avoiding unnecessary expenses.

### Optimize application features

Optimizing application features involves identifying and removing underutilized or unnecessary features to reduce costs and reallocate resources more efficiently. The evaluation should be based on the value of application features to users and their alignment with business goals. An application feature refers to a specific functionality or capability of application software that serves a specific purpose or provides a specific service to the users. Identify and eliminate features that aren't providing value to the workload or aren’t worth their current investment. Removing unnecessary or underutilized features reduces costs associated with maintaining and supporting them. It allows you to allocate resources more efficiently to features that bring more value to the users and the business.

#### Evaluate application feature value

To determine the value of a feature, consider its effects on the overall application and the value it provides to the customers. Some factors to consider include:

- *Customer needs*: Assess how well the feature meets the needs and expectations of the customers. Customer feedback, surveys, and usage data can be valuable in understanding the perceived value.
- *Business goals*: Evaluate how the feature aligns with the strategic objectives of the business. Consider how it supports revenue generation, customer satisfaction, or competitive advantage.
- *Effect on user experience*: Determine the effect the feature has on enhancing the user experience and improving usability or productivity.
- *Differentiation*: Assess whether the feature provides a unique selling point or competitive advantage compared to other applications in the market.

#### Evaluate application feature cost

Understanding the cost associated with each feature is essential for effective resource allocation and optimization. The cost evaluation involves considering various aspects, such as:

- *Development effort*: Assess the time, resources, and expertise required to develop and maintain the feature or surrounding features. Underutilized features often become a key source of technical debt.
- *Maintenance and support*: Consider the ongoing costs associated with maintaining and supporting the feature, including bug fixes, security updates, and troubleshooting.
- *Infrastructure and resource utilization*: Evaluate the effect of the feature on infrastructure requirements, including server resources, storage, and bandwidth.
- *Integration complexity*: Assess the complexity and cost of integrating the feature with other systems or third-party services.
- *Performance considerations*: Evaluate the effect of the feature on the application's performance, including scalability, response time, and resource usage.

#### Review application feature value with stakeholders

Reviewing the value of application features with stakeholders involves engaging key personnel, such as product managers, software developers, and business analysts, to assess the value of specific features on business objectives. This collaboration is essential for cost optimization as it provides insights into maintenance efforts and identifies features that might hinder productivity or detract from developing new, valuable features. Your development team can give you important information about how much work it takes to maintain certain features. Encourage them to speak up about features that might be more trouble than they're worth, especially if these features distract from creating new ones.

#### Determine the future of the feature

Based on the analysis and evaluation, determine the future of the application features. Remove, reinvest, or monetize any application feature not providing a return on investment.

- *Remove the application feature.* Consider the planned end of life of an application feature based on data. Reasons for removing the feature might include low customer demand, high maintenance costs, complexity, or redundancy that’s not worth the effort to fix. Create a plan for removing the identified features. It might involve refactoring the code, updating dependencies, or reorganizing the user interface.

    > :::image type="icon" source="../_images/risk.svg"::: **Risk**: You could inadvertently remove features that are critical for certain users or scenarios and might negatively affect performance, security, operations, and security.

- *Reinvest*: Some application features might not be adding enough value in its current state but could add value if you reinvest in them. Reinvestment means reworking or promoting the application feature. Prioritize the identified improvements based on their value and feasibility. Determine the roadmap and timeline for implementing the changes, considering factors such as development resources, dependencies, and potential effect on the application.
- *Monetize*: Monetizing an application feature involves turning it into a revenue-generating opportunity. Sometimes features might provide value to users but aren’t worth the current investment. Explore opportunities to monetize these and other features, such as offering them as separate paid add-ons or licensing them to other companies.

### Optimize workload resources

Optimizing workload resources involves removing any resources that aren't needed or used. It also includes adjusting the size and scale of the underutilized resources that are needed. This effort can save money, avoid waste, and ensure that the workload only uses the resources that add value to its performance.

**Remove unused workload resources.** Unused resources are deployed services your workload or operations processes don't use. These resources might be long-term idled, orphaned, or forgotten. They provide no return on investment, and you should remove them. Common sources of unused resources include:

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

**Optimize underutilized resources.** Underutilized resources represent wasted expenditure as you pay for resource capacity that isn't fully utilized. By identifying and optimizing these resources, you can reduce costs and allocate resources more effectively. To evaluate and optimize the cost of underutilized resources, follow these steps:

- *Resource monitoring*: Use tools to keep an eye on how much CPU, memory, and storage you're actually using. Pick the best plan that matches your needs based on this info.
- *Utilization analysis*: Look at the data to find out which resources you're not using much. Pay special attention to the resources that have low usage over a long time or large differences between busy and slow times.
- *Right-sizing*: For resources you're not using fully, see if you've allocated too much to them. If so, adjust their size to better match what you actually need.
- *Autoscaling*: Use autoscaling to automatically adjust how much resources you're using based on how busy you are. But set a maximum limit to avoid sudden spikes that could be costly and unnecessary.

Once you've made these adjustments, test to make sure everything still works as it should. Continuously monitor resource utilization and adjust resource allocation as workload demands change over time. Regularly review and optimize resource utilization to maintain cost efficiency and performance optimization.

**Optimize disaster recovery resources.** Optimizing disaster recovery environments is about ensuring the resources allocated for disaster recovery are used efficiently. A warm (active-passive) disaster recovery strategy is a common source of underutilization. In a warm disaster recovery strategy, one environment receives all the load while the other environment is idle until there's a disaster scenario. To optimize a disaster recovery environment, consider how a hot (active-active), cold (active-off), or active-redeploy approach can help avoid underutilized resources. Here's an overview of these three disaster recovery approaches:

- *Hot (active-active)*: Both the primary and secondary environments serve traffic concurrently. Your workload can balance loads between these environments and respond to demands in real-time. Distributing the load between two active environments, allows you to use cheaper resources, reduce single-point bottlenecks, and utilize capacities to the fullest. It can lead to reduced costs in terms of resource wastage or idling. A hot approach might demand more investment in synchronization and maintaining parity between the two environments.

- *Cold (active-off)*: A cold disaster recovery model involves a standby environment that remains dormant until a disaster triggers the need for failover. Since the standby environment isn't actively running, costs related to compute, storage, and network operations are minimized. Your expenses  revolve around storing backups, virtual machine images, or templates. Failover in the cold model can take longer because resources need to be booted up and data might need to be restored. Ensure that the recovery time aligns with your business's recovery time objectives (RTO) before committing to this approach.

- *Active-redeploy*: This strategy uses infrastructure as code. When a failover event occurs, you deploy the secondary environment, using predefined templates and scripts. With no predeployed compute resources in the disaster recovery environment, you save on the costs associated with maintaining idle resources. You only incur costs during the actual deployment in a failover scenario. Like the cold approach, this model might introduce longer recovery times, especially if the infrastructure's complexity is high. You should test and measure the recovery time to ensure it meets your recovery time objective.

### Optimize platform features

Optimizing platform features involves identifying and removing unnecessary or unused features to optimize resource utilization and reduce costs. Here are some steps to eliminate wasteful platform features:

- *Know the capabilities of the things you’ve purchased*: Understand the features and functionalities of the platforms or services you have acquired.
- *Disable unused features*: Identify and disable platform features that cost money and aren't utilized.
- *Use the right features*: Newer versions of a service can provide similar performance for the same price. For example, a virtual machine with hardware updates could provide the same performance for less.
- *Use the right configurations*: You might be paying for more availability or performance than you need. Eliminate availability or performance guarantees the workload doesn’t need.
- *Eliminate unneeded automation*: Evaluate your automation processes and eliminate any unnecessary automation that might incur extra costs.
- *Eliminate tool redundancy*: Get rid of tools that you don't really need or that do the same thing as other tools you already have. Consider tools used for building software, writing code, security, or monitoring how things are running. For instance, if you're already using GitHub actions to build your software, you don't need to buy another tool that does the same thing. Before you buy more features or tools, check if what you already have can do the job. This way, you avoid wasting money and make the most of what you already have.

### Avoid introducing unoptimized components

Avoiding introducing unoptimized components is about proactively ensuring components are efficient and essential, rather than adding or making changes without careful analysis. The best way to cost optimize workload components is to avoid unoptimized components in the first place. It prevents unnecessary expenses and resource wastage by addressing inefficiencies at the root, ensuring a workload runs cost-effectively from the outset. To help avoid introducing unoptimized components, consider these strategies:

- *Find the root cause before changing solutions*: Before you fix a problem, make sure you know what's actually causing it. For example, if your website is slow, don't just switch to a new system. First, figure out why it's slow. You might find out that the real issue is something else, like bad database queries. Fixing the real problem saves you time and money.
- *Apply metadata* Applying metadata provides a way to organize and track resources. You can categorize and group resources, making it easier to track, delete, and avoid orphaned resources. Create a consistent metadata strategy and apply consistently across resources. Consider adding owners, anticipated resource duration (`sunset-30d`), and others.
- *Document nonstandard changes*: Documenting any changes made to your infrastructure or configurations performed outside of the normal change control process of your workload can cut unexpected costs. For example, you might increase a resource’s scaling (up or out) capacities to meet a short-term demand or triage an issue but forget to scale it back down. Make a list of nonstandard changes and use it as a reminder to revert the changes when no longer necessary.
- *Keep things simple*: Simplifying your infrastructure and minimizing complexity can help reduce costs. Focus on using only the necessary resources and services that meet your requirements.

## Azure facilitation

**Eliminating unused application features**: Azure Monitor and Azure Application Insights helps you monitor the usage of your application and identify areas what areas are being used and which ones aren't. Based on the insights gathered, you can make informed decisions to remove or optimize unused or underutilized features.

**Eliminating unused resources**: Azure Advisor provides recommendations to identify and eliminate unused resources. It analyzes your resource usage and provides suggestions on resources that can be safely deleted or scaled down.

**Finding orphaned resources:** Azure Monitor supports Workbooks. These workbooks allow you to create custom reports that can find and report orphaned resources across a defined scope.

**Eliminating idle resources in disaster recovery environments**: Azure load balancers can distribute load across availability zones and regions. These load balancers can help eliminate wasted spending in disaster recovery approaches. You can use Azure Automation and Azure Logic Apps to schedule resource shutdowns during periods of inactivity. It can help reduce costs by minimizing the usage of idle resources. Azure Autoscale allows you to automatically scale your application based on predefined conditions, so you don’t have to overprovision capacity. It helps ensure that resources are allocated efficiently and cost-effectively.
