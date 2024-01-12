---
title: Recommendations for optimizing environment costs
description: Learn how to optimize environment costs by tailoring each environment to its specific purpose and optimize it for cost effectiveness.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing environment costs

**Applies to this Azure Well-Architected Framework Cost Optimization checklist recommendation:**

|[CO:08](checklist.md)| Optimize environment costs. Align spending to prioritize preproduction, production, operations, and disaster recovery environments. For each environment, consider the required availability, licensing, operating hours and conditions, and security. Nonproduction environments should emulate the production environment. Implement strategic tradeoffs into nonproduction environments. |
|---|---|

This guide describes the recommendations for cost optimizing workload environments. Each environment should be tailored for its specific purpose and optimized for cost effectiveness. It's important to make strategic tradeoffs and allocate resources where they matter the most, without compromising on critical components. By treating environments differently and optimizing them accordingly, you can achieve a balance between cost optimization and meeting the required objectives.

**Definitions**

| Term | Definition |
|---|----|
| Recovery point objective (RPO) | The maximum acceptable duration of data loss during an incident. |
| Recovery time objective (RTO) | The maximum acceptable time that an application can be unavailable after an incident. |
| Service-level agreement (SLA) | A contractual agreement between the service provider and the service customer. The agreement defines the service-level objectives (SLOs). Failure to meet the agreement might have financial consequences for the service provider. |

## Key design strategies

The goal of optimizing environment costs is to find the right balance of value, cost, and risk for each environment, including production, preproduction, and disaster recovery (DR) environments. Customize each environment for its particular use to save money and efficiently use resources. Determine the benefits of each environment, like efficiency or customer satisfaction. You want to evaluate the return on investment (ROI) for the environment, even if it doesn't make a direct profit. Spend more money on high-risk environments to reduce issues and save money on low-risk environments. Aim to balance value, cost, and risk in each environment.

### Assess environment value

Assessing the value of each environment means understanding its broader effect on business, gauging user satisfaction, and determining how it aligns with overarching organizational goals. This assessment helps you make informed decisions about resource allocation and align cost with environmental priorities. The essence of value extends beyond how much revenue an environment generates. When evaluating an environment's value, you need to prioritize spending in a manner that resonates with the goals of the workload. To assess the value of each environment, consider the following factors:

- *Consider the user*: Consider who uses each environment and what they need from it. For example, customers use the production environment, which must be reliable and meet specific SLAs for performance and uptime.

  On the other hand, the development environment is mainly for the workload team, such as developers and testers. This environment doesn't have to meet customer-facing SLAs, but it should have the necessary tools and resources for the team to work effectively.

  When you understand the unique needs of users in each environment, you can better allocate resources and avoid extra costs. This avoidance helps ensure each environment is functional and cost effective.

- *Align with organizational measures of value*: Align your cost-cutting efforts with your organization's priorities, like profit or employee satisfaction. For each environment, understand how success is defined, so you can keep your actions on target. For example, if your organization focuses on profit maximization or employee satisfaction, align your spending decisions with those metrics.

### Determine environment costs

Determining environment costs is about knowing the costs of infrastructure, services, licenses, and operational expenses in each workload environment. Cost management tools are key to gaining insights into spending patterns and trends across environments. To determine environment costs, consider these strategies:

- *Identify cost drivers*: Identify the key factors that drive costs in each environment. These factors can include resource utilization, storage usage, data retention, data transfer, and specific services.

- *Evaluate risks*: Assess the risks that are associated with spending decisions and their potential effect on the environment and business operations. Consider factors such as data security, compliance, performance, audits, and SLA requirements.

- *Monitor and adjust your spending*: Continuously monitor and analyze spending patterns, value delivery, and risk factors. Regularly review and adjust your spending optimization strategies as the needs of the environment and business evolve.

### Optimize the production environment

Optimizing costs in the production environment involves implementing strategies to reduce unnecessary expenses and improve operational efficiencies. Focus on differentiating production deployments and meeting the needs of users. Here are recommendations for optimizing the production environment:

- *Differentiate regions*: Spend less on regions that serve fewer customers. For example, you should invest more in a region that serves 90 percent of your users than in a region that serves 10 percent of your users. Adjust your deployment strategy to meet the requirements of each region and user segment.

- *Differentiate scaling*: Implement horizontal and vertical scaling strategies. Scale resources efficiently to meet demand without over-provisioning.

- *Differentiate infrastructure*: Choose cost-effective hardware and infrastructure solutions that meet the required performance and scalability. Consider factors such as performance, cost, reliability, and scalability.

- *Tune tenant models*: Customize the environment based on the tenant model. For example, spend more on services and features for paid tenants and spend less for nonpaying tenants.

### Optimize the DR environment

A DR environment refers to infrastructure and processes that a workload uses to recover after a disruptive event. Disruptive events include natural disasters, cyber attacks, and hardware failures. Balance the cost of maintaining a DR environment and the potential affects of a disruptive event. Consider the following strategies:

- *Evaluate the criticality of systems and data*: Assess the importance of systems and data to determine the required level of protection and resources for each component.

- *Determine RTOs and RPOs*: To help determine the design of the DR environment, define the acceptable downtime and data loss limits for each system or application.

- *Optimize a cold DR environment*: A cold DR environment has little or no infrastructure or running services. You can use infrastructure as code (IaC) to quickly deploy infrastructure during a disruptive event. Your backup and storage policies need to meet the RPOs and RTOs of the environment. Ensure that the amount and frequency of data backups isn't more robust than needed.

  > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: A cold DR environment is a cost-effective option, but you might have long recovery times.

- *Optimize a hot DR environment*: All infrastructure and services run in a hot DR environment. The data mirrors the primary site in real time. It provides near-instantaneous failover and minimal data loss if there's a disaster. Consider an active-active deployment to optimize costs.

- *Optimize a warm DR environment*: A warm DR approach is a middle ground between a cold DR environment and a hot DR environment. A warm environment is partially active and periodically syncs with the primary site. It offers a balance between cost and recovery time. However, it’s the least cost-optimized approach. Consider a cold or hot approach to optimize costs.

### Optimize preproduction environments

Optimizing preproduction environments involves strategically managing resources within development, testing, and staging areas to closely simulate production while reducing unnecessary costs. Preproduction environments don't require the full scale and availability of production environments. The most opportunities lie in tailoring these environments to specific testing and development needs without duplicating production exactly. Areas of cost reduction include using lower-cost resources, turning off unneeded services, and applying discounts offered for preproduction usage. Consider the following strategies to optimize preproduction environments:

#### Evaluate preproduction environments

Insufficient or improper allocation of preproduction environments might lead to over-provisioning or under-provisioning of resources. To evaluate your preproduction environments for your workload, consider the following guidance:

- *Understand the environment types*: Identify the types of preproduction environments, such as development, testing, and staging, that you need for your workload. Each environment should have a defined role and specific function to ensure efficient resource allocation.

- *Align with users' requirements*: Before you set up preproduction environments, understand the requirements and expectations of your users. Tailor the features and specifications based on their needs to avoid unnecessary expenses of features or resources.

- *Consolidate the environment*: Determine if you can combine environments without compromising their functionality. Combine environments that have functions that don’t overlap. For instance, you can merge a user acceptance environment with a quality assurance environment. The functions are distinct, and one environment is usually idle when the other is in use.

  > :::image type="icon" source="../_images/risk.svg"::: **Risk**: Be cautious when you combine environments to ensure that you don't introduce conflicts or compromise the testing or development processes.

The following table provides examples of common preproduction environments.

| Preproduction environment example| Description|
|----|----|
| Development environment| Developers use this environment to write and test code. It provides a sandbox space, so developers can experiment, build, and integrate code changes.|
| Quality assurance environment | This environment is dedicated to quality assurance activities. It for testing to identify and fix bugs or issues before deploying to the production environment. |
| Security environment | This environment is for security testing. It's for ensuring an application is secure against threats and vulnerabilities.|
| User acceptance testing environment | In this environment, end users and stakeholders test an application to validate its functionality and ensure that it meets requirements and expectations. |
| Staging environment| This environment closely resembles the production environment. It's for final testing and validation before deploying to production.|

#### Apply governance

Applying governance is about limiting deployment options in preproduction environments to control expenses and mitigate risks. In preproduction, you have flexibility to tailor configurations and deploy resources. The more the preproduction environment deviates from the production environment, the greater the potential risk. Use governance to constrain preproduction environments. Consider the following guidelines:

- *Limit performance tiers*: Evaluate the performance requirements of your preproduction environments. Choose performance tiers that balance cost and performance. A service often has different performance tiers, and some of these tiers are more suitable for testing. Some services have tiers that offer production-like features but don't come with an SLA. These services reduce costs but still provide the necessary functionality for testing and development.

- *Understand preproduction SKUs*: Some SKUs are designed for development environments. To optimize costs, evaluate services and tiers. Opt for low-performance tiers if the workload doesn't require high performance.

- *Control the number of instances and CPUs*: Determine the optimal number of instances and CPU resources that your preproduction environment needs based on workload demands. Avoid over-provisioning resources to minimize costs.

- *Limit retention and logging*: Define retention policies for logs and data in preproduction environments. Consider the necessary duration for retaining logs and data based on compliance requirements and cost considerations. Avoid excessive logging and retention to reduce storage costs.

- *Use a consistent CPU architecture*: Use the same CPU architecture in preproduction and production. For example, x86 applications don't run natively on Azure Resource Manager, and vice versa. Use the same CPU architecture as your production environment to ensure compatibility and minimize potential issues.

- *Use the same operating system*: Avoid changing the operating system (for example from Windows to Linux) or kernel in preproduction environments. Software built for Windows often doesn't run natively on Linux without a compatibility layer, and vice versa. The file systems and directory structures are different, which can cause application patching issues. Consistent environments help reduce the risk of compatibility issues and ensure smooth deployments.

- *Constrain scaling*: To optimize cost, you can constrain automation to mitigate runaway automation. For example, set a maximum scaling limit at three in the development environment, and set it at 10 in the production environment. Constrain scaling to help control the resource usage and automation cost.

- *Turn off unneeded resources*: Turn off resources when they aren't actively used, such as during off hours and weekends. You can use automation tools or scripts to schedule the shutdown and startup of resources. Some vendors provide APIs that you can use to programmatically stop and start the resources. Consider using IaC to create ephemeral environments that you can remove when you no longer need them.
  
- *Restrict available regions*: Consider the potential benefit of running preproduction environments in different regions where Azure resources might be cheaper. Restrict preproduction deployments to these regions to optimize the cost of these environments.

#### Balance similarity with production

It's often unnecessary and expensive for preproduction environments to mirror the production environment exactly. The goal is to ensure each preproduction environment is appropriately different from production to avoid unnecessary costs. However, when preproduction and production are different, there's a risk of deploying a bug into production. The more different these environments are, the more risk there is. Tailoring the preproduction environment to meet your needs can help you manage risks while optimizing cost. To balance the similarity with production, consider the following recommendations:

- *Avoid exact replicas*: Avoid making the preproduction environment an exact copy of production. It can unnecessarily increase costs. Create a preproduction environment that's cost-effective but enable you to uncover and address potential risks before deployment.

- *Avoid extreme deviations*: Avoid excessive deviation from production, like the use of different services. Different services might not accurately simulate real-world risks. Determine a risk threshold, and don't cross the threshold solely to save money.

- *Shorten runtimes*: Consider shortening the runtimes of processes in the preproduction stage to save money. Be cautious of new vulnerabilities that might arise, such as undetected memory leaks.

- *Review licenses*: Review the licensing plans for your security tools. If the number of nodes vary significantly between your production and preproduction setups, reassess your needs to fine-tune costs without compromising security.

#### Optimize development environments

Development environments are designed for development, testing, and debugging purposes. They have shorter lifecycles and are often created as needed and exist for a short time. Development environments typically have lower requirements for reliability, capacity, and security compared to other preproduction and production environments. They might have fewer capabilities and can accept lower resource utilization. To optimize your development environment:

- *Evaluate tooling*: Regularly assess the cost-effectiveness of your current tooling setup, including integrated development environments (IDEs), licenses, and related tools. Consider free or open-source alternatives that offer similar functionality without compromising quality. Continuously reevaluate the necessity and efficiency of these tools as the development landscape evolves.

- *Consider hardware*: Evaluate the cost and performance of your current hardware setups. Investing in better and more efficient hardware can enhance productivity and reduce long-term costs. Instead of frequent hardware replacements, consider upgrading existing systems to prolong their lifespan and improve performance.

- *Optimize the number of environments*: Analyze the advantages and disadvantages of individualized development environments versus a shared environment. Individual environments can mimic production setups, prevent interference among developers, and offer customized setups. However, scaling becomes more costly as the number of developers increases. Shared environments can save costs, but reliability concerns might arise if issues affect the entire development team simultaneously. Find the right balance based on cost, risk mitigation, efficiency, and developer satisfaction.

- *Regularly clean up*: Routinely clean up and optimize your development environment to avoid the accumulation of orphaned resources, unused data, and proof-of-concept experiments. Implement clean-up processes or automated tools to identify and remove unused resources. Keep only essential and active components. Regular clean-up helps reduce storage costs and ensures efficient resource utilization.

- *Implement sampled scaling*: Instead of scaling all components to their maximum capacity, consider a sampled approach in which you selectively scale vital components. This approach can be cost-effective while minimizing risks. Evaluate the risk-to-benefit ratio of not scaling certain elements and consider the potential effect on the environment.

- *Optimize data management*: Development environments might have low needs for data retention and backup frequency.

#### Consider endpoint emulation

You can optimize costs in a preproduction environment by using endpoint emulation or mock endpoints, particularly for expensive resources like GPUs. Identify components or services in your preproduction environment that are the most expensive or resource-intensive. Use mock endpoints to simulate the responses of these costly components without invoking them. To simulate API responses, you can use tools like WireMock, Postman's mock server, or custom implementations.

Emulation and mock endpoints help save costs, but you must ensure that they represent the production environment to a sufficient degree for testing. Strike a balance between accuracy and cost to help avoid future issues in production. For example, if GPUs are a major cost factor, consider GPU emulation for tasks that don't require real GPU processing power in preproduction stages. Emulation might not fully represent the performance or quirks of real GPUs, so use it when exact GPU behavior isn't critical for preproduction testing.

## Azure facilitation

**Determining and optimizing environment costs:** [Microsoft Cost Management](/azure/cost-management-billing/cost-management-billing-overview) is a suite of tools that help organizations monitor, allocate, and optimize the cost of their Microsoft Cloud workloads. Cost Management is available to anyone with access to a billing or resource management scope.

[Azure Advisor](/azure/advisor/advisor-overview) is a tool that provides cost optimization recommendations, including identifying areas of virtual machine usage that need optimization. Use Advisor to help you make informed decisions and optimize costs in your Azure environment. Azure provides cost management tools and features that help prioritize spending. You can use these tools to track and analyze costs across environments, set budgets, and receive cost optimization recommendations.

**Applying governance**: With Azure Policy, you can limit resource types, SKUs, and instances by defining policy rules that enforce restrictions on the types of resources that you can deploy in your Azure environment. You can maintain control over the provisioned resources and ensure compliance with your organization's policies and best practices.

To limit resource types by using Azure Policy, you can define policy rules that specify the allowed resource types. Apply those rules to the relevant Azure subscriptions or resource groups. Azure Policy prevents users from deploying resources that aren't allowed.

Use Azure Resource Manager to define and manage resources in a declarative manner. You can tune resources that are allocated to each environment based on their specific requirements. Use templates and parameterize resource configurations to optimize costs.

**Optimizing preproduction environments:** Azure offers dev/test pricing options that provide discounted rates for nonproduction environments. You can allocate more resources and budget to critical production environments, which optimizes costs in nonproduction environments. You can also use the Azure licensing offer, Azure Hybrid Benefit.

You can use [Azure API Management](/azure/api-management/api-management-key-concepts) for API mocking. API Management acts as a facade to back-end services, which allows API providers to abstract API implementations and evolve back-end architecture without affecting API consumers.

## Organizational alignment

Cloud Adoption Framework provides guidance to stress the importance of optimizing environment costs across the organization.

For more information, see [Best practices for costing and sizing Azure resources](/azure/cloud-adoption-framework/govern/cost-management/best-practices#best-practice-reduce-nonproduction-costs).

## Cost Optimization checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Cost Optimization checklist](checklist.md)
