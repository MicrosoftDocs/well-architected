---
title: Recommendations for optimizing environment costs
description: Learn how to optimize environment costs.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing environment costs: CO 08

This guide describes the recommendations for cost optimizing each workload environment. Each environment should be tailored for its specific purpose and optimized for cost-effectiveness. It's important to make strategic tradeoffs and allocate resources where they matter the most, without compromising on critical aspects. By treating environments differently and optimizing them accordingly, you can achieve a balance between cost optimization and meeting the required objectives.

**Definitions**

| Term | Definition |
|---|----|
| Service level agreement | A commitment between the cloud service provider and the customer about service expectations. |
| Recovery time objective | The maximum acceptable downtime expressed in units of time. |
| Recovery point objective | The maximum acceptable data loss expressed in units of time.                 |

## Key strategies

The goal is to find the right balance between value, cost, and risk for each environment. Include production, preproduction, and disaster recovery environments. Make each environment unique and fit for its particular use to save money and use resources efficiently. Evaluate the return on investment (ROI) for each setting, even if it doesn't make money directly, by looking at benefits like better efficiency or customer satisfaction. Spend more on high-risk environments to reduce issues and save money on low-risk ones. Aim to balance value, cost, and risk in each setting.

### Assess environment value

Understand the value of each environment. Value is more than revenue generation. Value refers to business impact, user satisfaction, and strategic alignment with organizational objectives. This analysis helps prioritize spending based value and ensures alignment with workload goals. To assess the value of each environment, consider these factors:

- *Consider the user*: Think about who uses each environment and what they need from it. For instance, customers use the production environment, which must be reliable and meet specific service level agreements (SLAs) for performance and uptime. On the other hand, the development environment is mainly for the workload team, such as developers and testers. While this environment doesn't have to meet customer-facing SLAs, it should have the necessary tools and resources for the team to work effectively. By understanding the unique needs of users in each environment, you can better allocate resources and avoid extra costs, ensuring that each setting is both functional and cost-effective.
- *Align with organizational measures of value*: Align your cost-cutting efforts with what the organization values most, like profits or employee satisfaction. Make sure you know how success is defined in each case to keep your actions on target. For example, if the organization focuses on profit maximization or employee satisfaction, align your spending decisions with those metrics.

### Determine environment costs

Know the costs of each environment. Include infrastructure, services, licenses, and operational expenses. Use cost management tools to gain insights into spending patterns and trends.

- *Identify cost drivers*: Identify the key factors driving costs in each environment. Consider could include resource utilization, storage usage, data retention, data transfer, or specific services.
- *Evaluate risks*: Assess the risks associated with different spending decisions and their potential impact on the environment and business operations. Consider factors such as data security, compliance, performance, audit, and SLA requirements.
- *Monitor and adjust*: Continuously monitor and analyze spending patterns, value delivery, and risk factors. Regularly review and adjust your spending optimization strategies as the needs of the environment and business evolve.

### Optimize the production environment

Cost optimizing the production environment involves implementing strategies to reduce unnecessary expenses and improve operational efficiencies. The focus should be on differentiating production deployments and meeting the specific needs of the users:

- *Differentiate regions*: Spend less on regions that server fewer customers. For example, you should invest more in a region that serves 90% of your users than in a region that services 10% of users. Adjust the deployment strategy to match the requirements of each region and user segment.
- *Differentiate scaling*: Implement horizontal and vertical scaling strategies. Scale resources efficiently to meet demand without over-provisioning.
- *Differentiate infrastructure*: Choose cost-effective hardware and infrastructure solutions that meet the required performance and scalability. Consider factors such as performance, cost, reliability, and scalability when making decisions.
- *Tune tenant models*: Customize the environment based on the tenant model. For example, spend more on services and features for paid tenants and spend less on nonpaying tenants.

### Optimize the disaster recovery environment

A disaster recovery (DR) environment refers to the infrastructure and processes that enable a workload to recover after a disruptive event. Disruptive events include natural disasters, cyber-attacks, or hardware failures. You need to balance the cost of maintaining a DR environment with the potential impact of a disruptive event. Consider the following strategies:

- *Evaluate the criticality of systems and data*: Assess the importance of different systems and data to determine the level of protection and resources required for each.
- *Determine recovery time objectives (RTO) and recovery point objectives (RPO):* Define the acceptable downtime and data loss limits for each system or application to guide the design of the DR environment.
- *Optimize a cold DR environment*: A cold DR environment has limited to no infrastructure or running services. Many use infrastructure-as-code to quickly deploy infrastructure in a disruptive event. Your backup and storage policies need to meet the RPO and RTO of the environment. Ensure the amount and frequency of data backups isn't more robust than needed.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: It's a cost-effective option but may involve longer recovery times.

- *Optimize a hot DR environment*: A hot DR environment has all the infrastructure and services running and data mirrors the primary site in real-time. It provides near-instantaneous failover and minimal data loss if there's a disaster. Consider switching to an active-active deployment to optimize cost.
- *Optimize a warm DR environment*: A warm DR approach is a middle ground between cold and hot DR. It’s partially active and is periodically synchronized with the primary site. It offers a balance between cost and recovery time. However, it’s the least cost-optimized approach. Consider shifting toward a cold or hot approach to optimize costs.

### Optimize preproduction environments

Preproduction workload environments are used to validate and stage deployment operations before releasing to the production environment. These environments help ensure agility, separation of production and preproduction assets, and thorough testing of operations. Consider these strategies to optimize preproduction environments. In general, you want to find and apply preproduction discounts. Using preproduction discounts is one of the safest and least risky strategies. Cloud service providers and vendors often offer discounts for preproduction environments.

#### Evaluate preproduction environments

Insufficient or improper allocation of preproduction environments may lead to over-provisioning or under-provisioning of resources. To evaluate your preproduction environments for your workload, consider the following guidance:

- *Understand the environment types*: Identify the different types of preproduction environments you need for your workload, such as development, testing, staging, and environments. Each environment should have a defined role and specific function to ensure efficient resource allocation.
- *Align with users' requirements*: Before setting up preproduction environments, understand the requirements and expectations of the users. Tailor the features and specifications based on their needs to avoid unnecessary expenses on unneeded features or resources.
- *Environment consolidation*: Evaluate if certain environments can be combined without compromising their functionality. It’s relevant where the functions of the environments don’t overlap. For instance, you could merge a user acceptance environment with the quality assurance environment. The functions are distinct and one is usually idle when the other is in use.

:::image type="icon" source="../_images/risk.svg"::: Be cautious when combining environments to ensure that it doesn't introduce conflicts or compromise the testing or development processes.

The following table provides examples of common preproduction environments.

| Preproduction environment examples| Description|
|----|----|
| Development environment| This environment is used by developers to write and test code. It provides a sandboxed space where developers can experiment, build, and integrate code changes.|
| Quality assurance environment | This environment is dedicated to quality assurance activities, where testing is conducted to identify and fix any bugs or issues before deploying to the production environment. |
| Security environment | This environment focuses on security testing and ensuring that the application is secure against threats and vulnerabilities.|
| User acceptance testing environment | In this environment, end users or stakeholders test the application to validate its functionality and ensure it meets their requirements and expectations. |
| Staging environment| The staging environment closely resembles the production environment and is used to perform final testing and validation before deploying to production.|

#### Apply governance

Use governance to control spend in preproduction environments and mitigate risk. In the preproduction, you have more flexibility to tailor configurations and deploy different resources. The more the preproduction environment deviates from the production environment the greater the potential risk. Use governance to constrain preproduction environments limiting cost overruns and minimizing risk. Consider the following guidelines:

- *Limit performance tiers*: Evaluate the performance requirements of your preproduction environments and choose the appropriate performance tiers that balance cost and performance. Services often have different tiers, and some provide of these tiers are more suitable for testing. Some services have tiers that offer production-like features but don't come with a Service Level Agreement (SLA). They reduce costs while still providing the necessary functionality for testing and development.
- *Preproduction SKUs*: Some SKUs are designed for development environments.Understand these services and the tiers available to optimize costs. Opt for lower performance tiers if the workload doesn't require high performance.
- *Control the number of instances/CPU*: Determine the optimal number of instances and CPU resources needed for your preproduction environments based on workload demands. Avoid overprovisioning resources to minimize costs.
- *Limit retention and logging*: Define retention policies for logs and data in preproduction environments. Consider the necessary duration for retaining logs and data based on compliance requirements and cost considerations. Avoid excessive logging and retention to reduce storage costs.
- *Use a consistent CPU architecture*: Use the same CPU architectures in preproduction and production. Applications compiled for the x86 architecture don't run natively on ARM, and vice versa. Use to the same CPU architecture as your production environment to ensure compatibility and minimize any potential issues.
- *Use the same operating system*: Avoid changing the operating system or kernel in preproduction environments (Windows to Linux). Software built for Windows often doesn't run natively on Linux without a compatibility layer and vice versa. The file systems and directory structures are different, which can cause application pathing issues. Consistency between environments helps reduce the risk of compatibility issues and ensures smoother deployments.
- *Constrain scaling*: To optimize cost, you can constrain automation to mitigate runaway automation. For example, set a maximum scaling limit in the development environment at three and 10 in the production environment. It helps control the resource usage and cost associated with automation.
- *Turn off unneeded resources*: Turn off resources when they aren't being actively used, such as during off hours and weekends. You can use automation tools or scripts to schedule the shutdown and startup of the resources. Some vendors provide APIs also allow you to programmatically stop and start the resources. Consider using infrastructure as code to create ephemeral environments that you tear down when done.

#### Balance similarity with production

The preproduction environment should be tailored to manage risks effectively while still being able to identify and address potential risks before transitioning to production. It's important to ensure that each instance of an environment is sufficiently different from production to avoid unnecessary costs. If the preproduction environment mirrors production exactly, it can lead to overspending.

- *Avoid exact replicas*: Avoid making the preproduction environment an exact copy of production. It can escalate costs unnecessarily. The goal is to create a preproduction setting that is cost-effective yet still capable of uncovering and addressing potential risks before deployment.
- *Avoid extreme deviations*: Avoid over deviating from production, like using different services. They may not accurately simulate real-world risks. Determine a risk threshold that shouldn't be crossed solely for cost-saving reasons.
- *Shorten runtimes*: Consider shortening the runtime of specific processes in the preproduction stage to save money. Be cautious of new vulnerabilities that may arise, such as undetected memory leaks.
- *Review licenses*. Review the licensing plans for your security tools. If the number of nodes varies significantly between your production and preproduction setups, reassess your needs to fine-tune costs without compromising on security.

#### Optimize development environments

Development environments are designed for development, testing, and debugging purposes. They have shorter lifecycles and are often created as needed and exist for a short time. Development environments typically have lower requirements for reliability, capacity, and security compared to other preproduction and production environments. They may have fewer capabilities and can accept lower resource utilization.

- *Evaluate tooling*: Regularly assess the cost-effectiveness of your current tooling setup, including Integrated Development Environments (IDEs), licenses, and related tools. Consider free or open-source alternatives that offer similar functionality without compromising quality. Continuously reevaluate the necessity and efficiency of these tools as the development landscape evolves.
- *Consider hardware*: Evaluate the cost and performance of your current hardware setups. Investing in better and more efficient hardware can enhance productivity and reduce long-term costs. Instead of frequent hardware replacements, consider upgrading existing systems to prolong their lifespan and improve performance.
- *Optimize the number of environments*: Analyze the advantages and disadvantages of individualized development environments versus a shared environment. Individual environments can mimic production setups, prevent interference between developers, and offer customized setups. However, scaling becomes more costly as the number of developers increases. Shared environments can save costs, but reliability concerns may arise if issues affect the entire development team simultaneously. Find the right balance based on cost, risk mitigation, efficiency, and developer satisfaction.
- *Regularly clean up*: Routinely clean up and optimize your development environment to avoid the accumulation of orphaned resources, unused data, and proof-of-concept experiments. Implement a clean-up process or automated tools to identify and remove unused resources, keeping only essential and active components. It helps reduce storage costs and ensures efficient resource utilization.
- *Implement sampled scaling*: Instead of scaling all components to their maximum capacity, consider a sampled approach where you selectively scale vital components. This approach can be cost-effective while minimizing risks. Evaluate the risk-to-benefit ratio of not scaling certain elements and consider the potential impact on the environment.
- *Optimize data management*: Development environments may have lower needs for data retention and backup frequency.

#### Consider endpoint emulation 

Cost optimization in a preproduction environment can be achieved using endpoint emulation or mock endpoints, particularly for expensive resources like GPUs. Identify the components or services in your preproduction environment that are most expensive or resource intensive. Use mock endpoints to simulate the responses of these costly components without invoking them. Tools like WireMock, Postman's mock server, or custom implementations can be used to simulate API responses.

While emulation and mock endpoints help save costs, ensure they accurately represent the production environment to a sufficient degree for testing. Striking a balance between accuracy and cost helps avoid future issues in production. For example, if GPUs are a major cost factor, consider GPU emulation for tasks that don't require real GPU processing power in preproduction stages. Emulation may not fully represent the performance or quirks of real GPUs, so use it when exact GPU behavior isn't critical for preproduction testing.
## Azure facilitation

**Governance.** Azure Policy allows you to limit resource types, SKUs, and instances by defining policy rules that enforce restrictions on the types of resources that can be deployed in your Azure environment. It helps you maintain control over the resources being provisioned and ensures compliance with your organization's policies and best practices.

To limit resource types using Azure Policy, you can define policy rules that specify the allowed resource types and apply those rules to the relevant Azure subscriptions or resource groups. Azure Policy prevents users from deploying resources that aren't allowed.

**Turn off resources:** You can use Azure Automation or Azure Logic Apps to create a schedule that turns off the resources during specific time periods, such as outside of normal working hours or on weekends.

**Cost visibility and analysis.** Azure Advisor is a tool that provides cost optimization recommendations, including identifying VM usage that needs optimization. It helps you make informed decisions to optimize costs in your Azure environment. Azure provides cost management tools and features that help prioritize spending and optimize costs. These tools allow you to track and analyze costs across different environments, set budgets, and receive cost optimization recommendations.

**Configure resources.** Azure Resource Manager enables you to define and manage resources in a declarative manner, making it easier to tune the resources allocated to each environment based on their specific requirements. You can use templates and parameterize resource configurations to optimize costs.

**Pre-production discounts.** Azure offers Dev/Test pricing options that provide discounted rates for nonproduction environments. It allows you to allocate more resources and budget to critical production environments while optimizing costs in nonproduction environments. Azure also allows you to use Hybrid Benefits and Licensing.

**Endpoint emulation.** [Azure API Management](/azure/api-management/api-management-key-concepts) can help in API mocking. It acts as a facade to the backend services, allowing API providers to abstract API implementations and evolve backend architecture without impacting API consumers.

## Related links

- [Costing and sizing Azure resources](/azure/cloud-adoption-framework/govern/cost-management/best-practices#best-practice-reduce-nonproduction-costs)
