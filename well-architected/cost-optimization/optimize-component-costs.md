---
title: Recommendations for optimizing component costs.
description: Learn how to optimize component costs by eliminating sources of wasted spending from an existing workload.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing component costs

**Applies to this Azure Well-Architected Framework Cost Optimization checklist recommendation:**

| [CO:07](checklist.md) | Optimize component costs. Regularly remove or optimize legacy, unneeded, and underutilized workload components, including application features, platform features, and resources.|
|---|---|

This guide describes the recommendations for optimizing workload component costs. Optimizing component costs refers to the process of evaluating and improving the cost-efficiency of individual elements within a workload. It emphasizes the continuous review and potential removal or improvement of outdated, unnecessary, or rarely used components, such as application features, platform features, and resources. It also covers cost optimization of disaster recovery environments and how to avoid introducing unoptimized components. The guidance in this article applies to existing workloads that aren't in the design phase. Neglecting regular component optimization can lead to inflated costs, resource waste, and inefficient workloads that drain both time and money.

**Definitions**

| Term | Definition  |
|----|----|
| Application feature | A distinct capability within the application software that enables users to perform specific tasks or access specific information.|
| Platform feature | A specific functionality or capability provided by a platform. It can vary depending on the platform, but generally, platform features are designed to enhance the user experience, improve productivity, or enable specific tasks or actions. |
| Resource | A single entity or component that you can create, configure, and utilize within a cloud service provider. |

## Key design strategies

Optimizing workload components is about refining the various elements of a workload, including application features, platform capabilities, and resource. The goal is to ensure the workload uses all components efficiently and cost-effectively. Strategies include removing, modifying, and avoiding components that cause you to spend more than you need. The component cost optimization process ensures you allocate resources to features and components that deliver the most value, avoiding unnecessary expenses.

### Optimize application features

Optimizing application features is the process of either removing, reinvesting, or monetizing application features based on value. It ensures you allocate resources to application features that provide the most value to customers. Optimizing application features helps avoid investing in features that contribute to technical debt or don't yield enough return on investment.

#### Evaluate application feature value

To determine the value of a feature, consider its effects on the overall application and the value it provides to the customers. Some factors to consider include:

- *Customer needs*: Assess how well the feature meets the needs and expectations of customers. Customer feedback, surveys, and usage data can be valuable in understanding the perceived value.

- *Business goals*: Evaluate how the feature aligns with the strategic objectives of the business. Consider how features support revenue generation, customer satisfaction, or competitive advantage.

- *Effect on user experience*: Determine the effect the feature has on enhancing the user experience and improving usability or productivity.

- *Differentiation*: Assess whether the feature provides a unique selling point or competitive advantage compared to other applications in the market.

#### Evaluate application feature cost

It's essential that you understand the cost associated with each feature for effective resource allocation and optimization. Consider various aspects when evaluating costs, such as:

- *Development effort*: Assess the time, resources, and expertise required to develop and maintain the feature or surrounding features. Underutilized features often become a key source of technical debt.

- *Maintenance and support*: Consider the ongoing costs associated with maintaining and supporting the feature, including bug fixes, security updates, and troubleshooting.

- *Infrastructure and resource utilization*: Evaluate the effect of the feature on infrastructure requirements, including server resources, storage, and bandwidth.

- *Integration complexity*: Assess the complexity and cost of integrating the feature with other systems or third-party services.

- *Performance considerations*: Evaluate the effect of the feature on the application's performance, including scalability, response time, and resource usage.

#### Review application feature value with stakeholders

Review the value of application features with stakeholders by engaging key personnel, such as product managers, software developers, and business analysts, to assess the value of specific features on business objectives. This collaboration is essential for cost optimization as it provides insights into maintenance efforts and identifies features that might hinder productivity or detract from developing new, valuable features. Your development team can give you important information about how much work it takes to maintain certain features. Encourage them to speak up about features that might be more trouble than they're worth, especially if these features distract the team from creating new ones.

#### Determine the future of the feature

Based on your analysis and evaluation, determine the future of the application features. Remove, reinvest, or monetize any application feature that doesn't provide a return on investment:

- *Removal*: Consider the planned end of life of an application feature based on data. Reasons for feature removal might include low customer demand, high maintenance costs, complexity, or redundancy that’s not worth the effort to fix. Create a plan for the removal, which might involve refactoring the code, updating dependencies, or reorganizing the UI.

    > ![Risk icon](../_images/risk.svg) **Risk**: You can inadvertently remove features that are critical for certain users or scenarios and might negatively affect performance, operations, and security in your application.

- *Reinvest*: Some application features might not add enough value in their current state but could add value if you reinvest in them. Reinvestment means reworking or promoting the application feature. Prioritize the identified improvements based on their value and feasibility. Determine the roadmap and timeline for implementing the changes. Consider factors such as development resources, dependencies, and the potential effect on the application.

- *Monetize*: Turn application features into a revenue-generating opportunity via monetization. Sometimes features provide value to users but aren’t worth the current investment. Explore opportunities to monetize these features, such as offering them as separate paid add-ons or licensing them to other companies.

### Optimize workload resources

Optimizing workload resources involves removing any resources that unused and optimizing any underutilized resources that the workload needs. This effort can save money, avoid waste, and ensure that the workload only uses the resources that add value.

**Remove unused workload resources.** Unused resources are deployed services your workload or operations processes don't use. These resources might be long-term idled, orphaned, or forgotten. They provide no return on investment, and you should remove them. Common causes of unused resources include:

- Alerts.
- Demo builds.
- Environment decommissioning.
- Feature decommissioning.
- IP addresses.
- Network firewalls.
- Proof of concept.
- Snapshots.
- Storage accounts.
- Temporary testing environments.
- Temporary triage environments.

To remove unused resources in a workload, consider these steps:

1. *Take an inventory*: Conduct a thorough inventory of all resources within the workload across environments.

1. *Find orphaned resources*: Resources can become orphaned when they're no longer needed or when their parent resources are removed. For example, you might remove a virtual machine, but its associated storage account isn't removed. Review your workload to identify unneeded or orphaned resources.

1. *Remove idle components*: There’s typically a cost associated with a deployed resource. Even if the resource allows you to stop or reallocate, you might continue to pay for the resource. Consider removing idle resources. If you need the data, back it up first and then remove the resource. You’re better off redeploying the resource and restoring the data than allowing the resource to remain idle.

**Optimize underutilized resources.** Underutilized resources represent wasted expenditure as you pay for resource capacity that isn't fully utilized. Identify and optimize these resources to reduce costs and allocate resources more effectively. To evaluate and optimize the cost of underutilized resources, follow these steps:

1. *Monitor resources*: Use tools to monitor how much CPU, memory, and storage you actually use. Choose the best plan that matches your needs based on this information.

1. *Analyze utilization*: Look at the data to find out which resources you don't use. Pay attention to the resources that have low usage over time or large differences in usage between busy and slow times.

1. *Right-sizing*: Check if there are too many resources allocated to features that aren't in use. If so, adjust their size to better match what you actually need.

1. *Automatic scaling*: Use automatic scaling to adjust the resources you use based on how busy you are. Make sure that you set a maximum scaling limit to avoid sudden spikes that can be costly and unnecessary.

After you make these adjustments, test to make sure everything still works as it should. Continuously monitor resource utilization and adjust resource allocation as workload demands change over time. Regularly review and optimize resource utilization to maintain cost efficiency and performance optimization.

**Optimize disaster recovery resources.** Optimizing disaster recovery environments is about ensuring the resources allocated for disaster recovery are used efficiently. A warm (active-passive) disaster recovery strategy is a common source of underutilization. In a warm disaster recovery strategy, one environment receives all the load while the other environment is idle until there's a disaster scenario. To optimize a disaster recovery environment, consider how a hot (active-active), cold (active-off), or active-redeploy approach can help avoid underutilized resources. Here's an overview of these three disaster recovery approaches:

- *Hot plans*: Both the primary and secondary environments serve traffic concurrently. Your workload can balance loads between these environments and respond to demands in real-time. Distributing the load between two active environments, allows you to use cheaper resources, reduce single-point bottlenecks, and utilize capacities to the fullest. It can lead to reduced costs in terms of resource wastage or idling. A hot approach might demand more investment in synchronization and maintaining parity between the two environments.

- *Cold plans*: A cold disaster recovery model involves a standby environment that remains dormant until a disaster triggers the need for failover. Since the standby environment isn't actively running, costs related to compute, storage, and network operations are minimized. Your expenses revolve around storing backups, virtual machine (VM) images, or templates. Failover in the cold model can take longer because resources need to be booted up and data might need to be restored. Ensure that the recovery time aligns with your business's recovery time objectives (RTO) before committing to this approach.

- *Active-redeploy*: This strategy uses infrastructure as code. When a failover event occurs, you deploy the secondary environment, using predefined templates and scripts. With no predeployed compute resources in the disaster recovery environment, you save on the costs associated with maintaining idle resources. You only incur costs during the actual deployment in a failover scenario. Like the cold approach, this model might introduce longer recovery times, especially if the infrastructure's complexity is high. You should test and measure the recovery time to ensure it meets your recovery time objective.

### Optimize platform features

Optimizing platform features involves eliminating or updating platform features, such as performance tiers and configuration settings, to optimize costs. It helps align spending with the requirements of the workload and avoids unnecessary expenses on unneeded features. Here are some tips to optimize the cost of platform features:

- *Know the capabilities of the things you purchase*: Before you can optimize, you need a clear inventory of the services and their features across your cloud platforms. Understand the features and functionalities of the platforms or services in your workload. Be aware of the specific tier you chose and the features each tier offers. For example, if you don't require autoscaling or advanced networking, a lower-tier plan might suffice.

- *Disable unused features*: Identify and disable platform features that cost money. You might have unneeded storage snapshots, unused disks, redundant security features, or underutilized networking capabilities.

- *Use the right versions*: Newer versions of a service can provide similar performance for the same price. For example, a virtual machine with newer hardware can often provide the same performance for less money.

- *Use the right configurations*: You might be paying for more availability or performance than you need. Eliminate availability or performance guarantees that the workload doesn’t need.

- *Eliminate unneeded automation*: Evaluate your automation processes and eliminate any unused automation that might incur extra costs.

- *Eliminate tool redundancy*: Get rid of tools that you don't need or tools that provide the same function. Evaluate potential redundancy in the tools you use for building software, writing code, security, and monitoring. For example, if you use GitHub Actions to build your software, you don't need to buy another tool that builds software. Before you buy features or tools, check if there's already a tool in your workload that can do the job. Eliminate tool redundancy to avoid wasted money and make the most of what you already have.

### Prevent unoptimized components

Preventing unoptimized components is about proactively ensuring components are essential and optimized before adding or modifying. The best way to get rid of waste is to avoid it in the first place. Use strategies that prevent unnecessary expenses by addressing inefficiencies at the root, ensuring a workload runs cost-effectively from the outset. To help prevent waste, consider these strategies:

- *Find the root cause before changing solutions*: Before you fix a problem, make sure you know what's actually causing it. For example, if your website is slow, don't immediately switch to a new system. First, figure out why it's slow. You might find out that the real issue is something else, like bad database queries. Fix the real problem to save time and money.

- *Apply metadata*: Apply metadata to organize and track resources. You can use metadata to categorize and group resources, making it easier to track, delete, and avoid orphaned resources. Create a consistent metadata strategy across resources. Consider adding owners, the anticipated resource duration (for example, `sunset-30d`), or other tags.

- *Document nonstandard changes*: Document any changes made to your infrastructure or configurations performed outside the normal control process of your workload to cut unexpected costs. For example, you might increase a resource’s scaling (up or out) capacities to meet a short-term demand or triage an issue but forget to scale it back down. Make a list of nonstandard changes and use it as a reminder to revert the changes when they're no longer necessary.

- *Keep things simple*: Simplify your infrastructure and minimize complexity to help reduce costs. Use only the necessary resources and services that meet your requirements.

## Azure facilitation

**Optimizing application features**: You can use [Azure Monitor](/azure/azure-monitor/overview) and [Application Insights](/azure/azure-monitor/app/app-insights-overview) to monitor the usage of your application and identify areas that are or aren't used. Based on the insights gathered, you can make informed decisions to remove or optimize unused or underutilized features.

**Optimizing workload resources and platform features**: Azure Advisor provides [cost recommendations](/azure/advisor/advisor-reference-cost-recommendations) provides recommendations to identify and eliminate unused resources. You can use Advisor to analyze your resource usage and receive suggestions about resources to remove or scale down. The [Cost Optimization workbook](/azure/advisor/advisor-cost-optimization-workbook) in Azure Advisor serves as a centralized hub for some of the most commonly used tools that can help you drive utilization and efficiency goals. It offers a range of recommendations, including Azure Advisor cost recommendations. It also helps identify idle resources and manage of improperly deallocated virtual machines.

Azure Monitor supports [workbooks](/azure/azure-monitor/visualize/workbooks-overview). With Azure Monitor workbooks, you can find or create a workbook that finds and reports orphaned resources across a defined scope. You can use [Azure Automation](/azure/automation/automation-solution-vm-management-config) to shut down virtual machines during periods of inactivity. Resource shutdowns help reduce costs by minimizing the use of idle resources.

You can use the [autoscale feature](/azure/azure-monitor/autoscale/autoscale-overview) in Azure to automatically scale your application based on predefined conditions, so you don’t have to overprovision capacity. Automatic scaling can help you allocate resources efficiently and cost-effectively.

From a design perspective, [Azure load balancers](/azure/architecture/guide/technology-choices/load-balancing-overview) can distribute loads across availability zones and regions. These load balancers can help eliminate idle resources, for example, in disaster recovery approaches.

## Related links

- [Recommendations for continuous performance optimization](../performance-efficiency/continuous-performance-optimize.md)
- [Recommendations for defining reliability targets](../reliability/metrics.md)
- [Recommendations for highly available multi-region design](../reliability/highly-available-multi-region-design.md)
- [Recommendations for optimizing scaling and partitioning](../performance-efficiency/scale-partition.md)
- [Recommendations for setting spending guardrails](set-spending-guardrails.md)

## Cost Optimization checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Cost Optimization checklist](checklist.md)
