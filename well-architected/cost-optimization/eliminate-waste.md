---
title: Recommendations for eliminating waste
description: Learn how to eliminate waste from a workload.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for eliminating waste

**Applies to this Azure Well-Architected Framework Cost Optimization checklist recommendation:**

|[CO:07](checklist.md)| Eliminate waste. Use only what you need in workload components and application features. When possible, use prepaid reservations and savings plans. |
|---|---|

This guide describes the recommendations for eliminating workload waste. The guidance in this article applies to existing workloads that aren't in the design phase. Waste reduction is fundamental for both financial savings and operational efficiency. Remove unused and underused workload components and address underused, prepaid commitments to eliminate waste. To ensure the highest return on investment, workload components and application features should actively deliver value, and prepaid reservations and savings plans should be maximized.

**Definitions**

| Term | Definition  |
|----|----|
| Application feature | A distinct capability within the application software that enables users to perform specific tasks or access specific information.|
| Platform feature | A specific functionality or capability provided by a platform. It can vary depending on the platform, but generally, platform features are designed to enhance the user experience, improve productivity, or enable specific tasks or actions. |
| Resource | A single entity or component that you can create, configure, and utilize within a cloud service provider. |

## Key strategies

Eliminate waste by using only the necessary resources, such as workload components, application features, or prepaid plans. Waste leads to inflated expenses. Regularly review and update how you manage workload resources and prepaid plans to get the most value for your money.

### Eliminate waste from the application

Identify and remove underutilized or unnecessary features in your application to reduce costs and reallocate resources more efficiently. Evaluate potential waste based on the value of application features to users and their alignment with business goals. An application feature refers to a functionality or capability of application software that serves a specific purpose or provides a specific service to the user. Identify and eliminate features that don't provide value to the workload. Remove unnecessary features to allocate resources more efficiently to features that bring more value to the users and the business.

**Evaluate the value.** To determine the value of a feature, consider its effects on the overall application and the value it provides to the customers. Some factors to consider include:

- *Customer needs*: Assess how well the feature meets the needs and expectations of the customers. Customer feedback, surveys, and usage data can be valuable in understanding the perceived value.

- *Business goals*: Evaluate how the feature aligns with the strategic objectives of the business. Consider how features support revenue generation, customer satisfaction, or competitive advantage.

- *Effect on user experience*: Determine the effect the feature has on enhancing the user experience and improving usability or productivity.

- *Differentiation*: Assess whether the feature provides a unique selling point or competitive advantage compared to other applications in the market.

**Evaluate the cost.** It's essential that you understand the cost associated with each feature for effective resource allocation and optimization. Consider various aspects when evaluating costs, such as:

- *Development effort*: Assess the time, resources, and expertise required to develop and maintain the feature or surrounding features. Underutilized features often become a key source of technical debt.

- *Maintenance and support*: Consider the ongoing costs associated with maintaining and supporting the feature, including bug fixes, security updates, and troubleshooting.

- *Infrastructure and resource utilization*: Evaluate the effect of the feature on infrastructure requirements, like server resources, storage, and bandwidth.

- *Integration complexity*: Assess the complexity and cost of integrating the feature with other systems or third-party services.

- *Performance considerations*: Evaluate the effect of the feature on the application's performance, including scalability, response time, and resource usage.

**Seek input.** Get everyone involved when you're trying to figure out if an application feature is worth the cost and effort. Talk to product managers, software developers, and business analysts. Your development team can give you important information about how much work it takes to maintain certain features. Encourage them to speak up about features that might be more trouble than they're worth, especially if these features distract from creating new ones.

**Determine the future of the feature.** Based on your analysis and evaluation, determine the future of the application features. Remove, reinvest, or monetize any application feature that doesn't provide a return on investment:

- *Removal*: Consider the planned end of life of an application feature based on data. Reasons for feature removal might include low customer demand, high maintenance costs, complexity, or redundancy that’s not worth the effort to fix. Create a plan for the removal, which might involve refactoring the code, updating dependencies, or reorganizing the UI.

> ![Risk icon](../_images/risk.svg) **Risk**: You could inadvertently remove features that are critical for certain users or scenarios and might negatively affect performance, operations, and security in your application.

- *Reinvest*: Some application features might not add enough value in their current state but could add value if you reinvest in them. Reinvestment means reworking or promoting the application feature. Prioritize the identified improvements based on their value and feasibility. Determine the roadmap and timeline for implementing the changes. Consider factors such as development resources, dependencies, and potential effect on the application.

- *Monetize*: Turn application features into a revenue-generating opportunity via monetization. Sometimes features provide value to users but aren’t worth the current investment. Explore opportunities to monetize these features, such as offering them as separate paid add-ons or licensing them to other companies.

### Eliminate waste from workload resources

Remove any unused or underutilized resources in a workload across its various environments to eliminate waste from workload resources. You can also optimize idle resources in disaster recovery environments to eliminate waste.

**Remove unused workload resources.** Unused resources are deployed services your workload or operations processes don't use. These resources might be long-term idled, orphaned, or forgotten. They provide no return on investment, and you should remove them. Common causes of resources waste include:

- Storage accounts.
- Snapshots.
- IP addresses.
- Network firewalls.
- Alerts.
- Proof of concept.
- Demo builds.
- Temporary triage environments.
- Temporary testing environments.
- Feature decommissioning.
- Environment decommissioning.

To remove unused resources in a workload, consider these steps:

1. *Take inventory*: Conduct a thorough inventory of all resources within the workload across environments.

1. *Find orphaned resources*: Resources can become orphaned when they're no longer needed or when their parent resources are deleted. For example, you might delete a virtual machine, but its associated storage account isn't removed. Review your workload to identify resources that are no longer needed or are orphaned.

1. *Remove idle components*: There’s typically a cost associated with a deployed resource. Even if the resource allows you to stop or reallocate, you might continue to pay for the resource. Consider removing idle resources. If you need the data, back it up first and then remove the resource. You’re better off redeploying the resource and restoring the data than allowing the resource to remain idle.

**Evaluate underutilized resources.** Underutilized resources represent wasted expenditure as you pay for resource capacity that isn't fully utilized. Identify and optimize these resources to reduce costs and allocate resources more effectively. To evaluate and optimize the cost of underutilized resources, follow these steps:

1. *Monitor resources*: Use tools to monitor how much CPU, memory, and storage you're actually using. Choose the best plan that matches your needs based on this information.

1. *Analyze utilization*: Look at the data to find out which resources you're not using. Pay attention to the resources that have low usage over time or large differences in usage between busy and slow times.

1. *Right-sizing*: Check if there are too many resources allocated to features that aren't in use. If so, adjust their size to better match what you actually need.

1. *Automatic scaling*: Use automatic scaling to adjust the resources you're using based on how busy you are. Make sure that you set a maximum scaling limit to avoid sudden spikes that could be costly and unnecessary.

After you make these adjustments, test to make sure everything still works as it should. Continuously monitor resource utilization and adjust resource allocation as workload demands change over time. Regularly review and optimize resource utilization to maintain cost efficiency and performance optimization.

**Eliminate disaster recovery waste.** Warm (active-passive) disaster recovery plans are a common source of underutilization. One environment receives all the load while the other environment is idle, awaiting a situation you hope never happens. Consider hot (active-active), cold (active-off), and active-redeploy plans to help avoid underutilized resources.

- *Hot plans*: A hot disaster recovery plan is a failover solution where the primary and secondary environments actively serve traffic, so you can use cheaper resources and minimize unused capacity.

- *Cold plans*: A cold disaster recovery plan is a failover solution where the standby environment isn't actively running until there's a need for failover. The production environment is replicated as backups, virtual machine (VM) images, or Azure Resource Manager templates to a different region. This approach is cost-effective but takes longer.

- *Active-redeploy*: The active-redeploy approach uses infrastructure-as-code (IaC) to deploy resources to the secondary environment in a failover event. Before a failover event, there are no deployed resources in the disaster recovery environment.

### Eliminate waste from platform features

Identify and remove unnecessary or unused platform features to optimize resource usage and reduce costs. Here are some tips to eliminate wasteful platform features:

- *Know the capabilities of the things you’ve purchased*. Understand the features and functionalities of the platforms or services in your workload.

- *Disable unused features*: Identify and disable unused platform features that cost money.

- *Use the right features*: Newer versions of a service can provide similar performance for the same price. For example, a VM with hardware updates could provide the same performance for less.

- *Use the right configurations*: You might be paying for more availability or performance than you need. Eliminate availability or performance guarantees that the workload doesn’t need.

- *Eliminate unnecessary automation*: Evaluate your automation processes and eliminate any unused automation that might incur extra costs.

- *Eliminate tool redundancy*: Get rid of tools that you don't need or tools that do the same thing. Evaluate potential redundancy in the tools you use for building software, writing code, security, and monitoring. For example, if you use GitHub actions to build your software, you don't need to buy another tool that does the same thing. Before you buy features or tools, check if there's already a tool in your workload that can do the job. Eliminate tool redundancy to avoid wasted money and make the most of what you already have.

### Eliminate waste from prepaid agreements

Buy the right plan and fully utilize it to maximize the usage of prepaid plans. Strategic usage of reservations and savings plans can significantly minimize costs for applicable resources. You can use savings plans to effectively plan and allocate resources, optimize spending, and achieve significant cost reductions.

- *Monitor usage*: Regularly check your prepaid plan usage. Set up alerts to tell you if you're not using all of your reserved resources. Evaluate usage over time and eliminate any plans you're not using regularly. Make sure you're using the right size of VMs and adjust the sizes to fit what you've already paid for.

- *Adjust the prepaid plan*: Consider changing the scope of the reservation to share, which allows it to apply more broadly across your resources. If you find underused prepaid plans, try exchanging unused quantity or canceling and refunding plans.

- *Review prepaid plans*: Regularly review and analyze your charges and usage data. Understand the breakdown between actual cost and amortized costs. Reconcile the data to ensure accurate billing.

### Prevent waste

The best way to get rid of waste is to avoid it in the first place. Use strategies that prevent waste and allow you to catch waste faster. To help prevent waste, consider these strategies:

- *Find the root cause before changing solutions*: Before you fix a problem, make sure you know what's actually causing it. For example, if your website is slow, don't immediately switch to a new system. First, figure out why it's slow. You might find out that the real issue is something else, like bad database queries. Fix the real problem to save time and money.

- *Apply metadata*: Apply metadata to organize and track resources. You can use metadata to categorize and group resources, making it easier to track, delete, and avoid orphaned resources. Create a consistent metadata strategy that's applied across resources. Consider adding owners, anticipated resource duration (`sunset-30d`), and other tags.

- *Document nonstandard changes*: Document any changes made to your infrastructure or configurations performed outside the normal control process of your workload to cut unexpected costs. For example, you might increase a resource’s scaling (up or out) capacities to meet a short-term demand or triage an issue but forget to scale it back down. Make a list of nonstandard changes and use it as a reminder to revert the changes when no longer necessary.

- *Keep things simple*: Simplify your infrastructure and minimize complexity to help reduce costs. Use only the necessary resources and services that meet your requirements.

## Azure facilitation

**Eliminate unused application features**: You can use Azure Monitor and Application Insights to monitor the usage of your application and identify areas that are or aren't used. Based on the insights gathered, you can make informed decisions to remove or optimize unused or underutilized features.

**Eliminate unused resources**: Azure Advisor provides recommendations to identify and eliminate unused resources. You can use Advisor to analyze your resource usage and receive suggestions on resources to delete or scale down.

**Find orphaned resources:** Azure Monitor supports workbooks. You can use workbooks to create a custom report that finds and reports orphaned resources across a defined scope.

**Eliminate unused reservations and savings plans**: Use Microsoft Cost Management tools to eliminate unused reservations and savings plans. These tools provide insights into your reservation and savings plan usage, so that you can identify any unused or underutilized commitments and make adjustments accordingly. Utilization can be viewed in the Azure portal under the **Reservations** section.

**Eliminate idle resources in disaster recovery environments**: Azure load balancers can distribute loads across availability zones and regions. These load balancers can help eliminate waste in disaster recovery approaches. You can use Azure Automation and Azure Logic Apps to schedule resource shutdowns during periods of inactivity. Resource shutdowns help reduce costs by minimizing the usage of idle resources. You can use the Azure autoscale feature to automatically scale your application based on predefined conditions, so you don’t have to overprovision capacity. Automatic scaling can help to ensure that resources are allocated efficiently and cost-effectively.

## Related links

TBD - S. Sumner will provide.

## Cost Optimization checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Cost Optimization checklist](checklist.md)
