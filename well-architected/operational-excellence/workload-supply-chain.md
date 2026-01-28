---
title: Architecture strategies for designing a workload development supply chain
description: Learn how to design a workload development supply chain based on CI/CD pipelines that ensure a predictable, efficient workload lifecycle.
author: simipaul
ms.author: simipaul
ms.date: 11/15/2023
ms.topic: concept-article
---

# Architecture strategies for designing a workload development supply chain

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:**

|**OE:06**|Build and optimize your workload supply chain with predictable, automated pipelines that test and promote changes across environments. Design these pipelines to consistently meet your reliability, security, cost-efficiency, and performance targets.|
|---|---|

To provide a predictable, standardized way to maintain your workload, design your workload development supply chain around continuous integration and continuous delivery (CI/CD). Maintain a single, standardized supply chain and implement it with automated CI/CD pipelines; you can use multiple pipelines as long as they all adhere to the same supply chain.

Use a standardized supply chain to protect your workload from the risks of unmanaged changes. Maintain continuous visibility into workload state to avoid unpredictable behavior and costly retracing of untracked changes when problems arise. To minimize these risks, standardize the processes and tools that define your supply chain, and ensure that your workload team fully commits to their use.

**Definition**

|Term  |Definition  |
|---------|---------|
|Supply chain     |In cloud workloads, a supply chain is a standardized suite of tools and processes that you use to affect infrastructure and application change across environments.        |

> [!NOTE]
> The recommendations in this guide refer to workload environments in a code promotion chain. Sandbox or other exploratory and proof-of-concept environments require less rigor and structure.

The following recommendations can help you define the core tenets of your supply chain.

## Enforce a strict policy of automated template-based deployments

**Make all proposed workload changes through your supply chain’s processes and tools, and enforce automated, template-based deployments.** This approach keeps configurations standardized, well defined, and tightly controlled across environments. For environments in a code promotion chain, prohibit manual updates or direct interaction with the cloud control plane such as the portal or APIs. Incorporate all changes to the environment through pipelines that follow your defined deployment practices. To enforce this policy, limit access to read-only and use authorization gates to grant write access when needed. 

An important aspect of this tenet is that all changes are *proposed* *changes* until they're deployed into production. Through automated testing, like integration and smoke testing, you enable your supply chain to automatically reject changes.

> :::image type="icon" source="../_images/ai.svg"::: **AI opportunity**: Manual diagnosis of deployment failures slows teams, causes frustration, and creates knowledge silos. To boost productivity and resilience, start by evaluating off-the-shelf DevOps tools with built-in AI for failure analysis. If those tools aren't enough, build a custom agentic AI solution that monitors pipeline data, detects recurring failure patterns, and recommends proactive remediations. Give the AI secure, controlled access to your logs, config files, and deployment metadata so it can automatically identify and fix common issues. Maintain oversight with audit logs and approval gates for AI-driven changes. Plan for ongoing model and integration maintenance to ensure long-term reliability and safety.

## Deploy repeatable and immutable infrastructure as code

**Deploy repeatable, immutable infrastructure by using infrastructure as code (IaC).** Manage infrastructure as declarative, version-controlled definitions that mirror your application source. Applying the same IaC consistently creates the same environment every time, just as the same source code produces the same binary when compiled. 

To standardize and automate how you deploy and configure infrastructure, adopt infrastructure as code (IaC). Replace person-dependent deployments with fully automated pipelines, shifting responsibility from individuals to tooling and reducing manual effort. Enable authorized team members to initiate deployments through this automated process to maintain consistency and quality across environments. 

Design your workload as a logical group of components that you can bundle into one template to make deployments easy and repeatable. You can think of these bundles as *stamps* or *units of scale*. For more information, see [Deployment Stamps pattern](/azure/architecture/patterns/deployment-stamp). When you need to deploy your workload to scale out into another region or zone within the same region, deploy a stamp by using a pipeline. Depending on how you design your stamps, you might deploy a subset of your workload instead of the entire workload. To ensure that your deployment stamps automatically connect to existing resources, include networking components in your IaC pipelines.

To optimize your IaC pipeline for consistency and efficiency, design an immutable infrastructure rather than a mutable infrastructure. Implement an immutable infrastructure to ensure that all systems in scope are replaced with the updated configuration simultaneously and identically with each deployment.

> :::image type="icon" source="../_images/ai.svg"::: **AI opportunity**: Streamline pipeline management and reduce manual effort by using AI-powered deployment optimization. Use AI to recommend rollback approaches and identify discrepancies between current and intended infrastructure states. Integrate AI to enhance monitoring by anticipating issues and enabling earlier intervention. To use AI safely in these workflows, standardize your pipelines and keep all relevant assets up to date. Maintain accurate resource inventories across environments and monitor resource state continuously. Add approval steps and audit trails to track all AI actions. 

## Use the same set of deployment artifacts across all environments

**Use the same set of code assets and artifacts across all environments and pipelines.** A common pain point for organizations is when nonproduction environments are different from production environments. Building production and nonproduction environments manually can result in mismatched configurations between the environments. This mismatch slows down testing and makes it more likely that changes might harm a production system. An IaC approach minimizes these problems. When you use IaC automation, you can use the same infrastructure configuration files for all environments to produce almost identical environments. You can add parameters to the infrastructure configuration files and adjust them to meet the requirements for each environment. 

To control costs, there's typically a variance between production and nonproduction environments. You typically don't need the same level of redundancy or performance in nonproduction, so resource counts and SKUs can differ. Ensure that you control and understand the variance by using standardized parameters to help you maintain predictability as you make changes.

## Reflect the organizational structure in the supply chain

**Reflect your organizational structure in your supply chain and pipeline designs.** Your organization might be siloed among teams. You can organize your teams by function (for example, networking, data, and compute) or integrate them as DevOps teams that manage both infrastructure and applications. There are many ways to organize the teams that are involved in a supply chain. Regardless of structure, your supply chain relies on seamless collaboration across all teams. Ensure that all teams follow standard processes and use standard tools to make the supply chain as efficient as possible.

Your supply chain might rely on third-party vendors if you outsource parts of the workload lifecycle. These vendors are as critical to the success of your supply chain as internal resources. Ensure that there's a mutual agreement across all teams about the processes and tools that you use.

## Choose the right deployment method

**Standardize your deployment method.** Talk to the product owner about the acceptable amount of production downtime for your workload. Depending on how much downtime is acceptable, you can choose the deployment method that fits your requirements. Ideally, perform deployments during a maintenance window to reduce complexity and risk. If no downtime is acceptable, use a blue-green deployment method.

Use a progressive-exposure (canary deployments) approach to reduce the risk of introducing undetected bugs to your customers. Deploy changes to small, controlled groups in stages so you can catch problems before a broad release. The initial rollout group might be a subsection of your customers that are aware of the rollout strategy. This subsection of customers can tolerate some amount of unexpected behavior and provide feedback. Or it might be a group of internal users, which helps contain the blast radius of bugs during the rollout.

When you define your deployment method, adopt a standard policy of releasing the smallest viable change in each deployment. Determine what qualifies as the smallest viable change based on your workload’s criticality and the complexity of its components. If you use an immutable infrastructure, the smallest viable change is redeploying resources with the latest configuration to replace those resources running the previous version. If you use a mutable infrastructure, you might decide that the smallest viable change is only a single, scoped update on the group of resources.

> :::image type="icon" source="../_images/ai.svg"::: **AI opportunity**: AI-driven analysis identifies usage patterns and recommends optimal deployment times—eliminating repetitive manual log inspection. Avoid guessing low usage periods, deploying during high traffic times, or causing user disruption. Start with a low-effort GenAI approach for interactive analysis. For large-scale, high-transaction workloads, scale to ML-based predictive models that forecast optimal deployment windows as usage trends evolve. Keep predictive models accurate by continuously retraining and refining them. Provide AI systems with secure access to your workload telemetry while maintaining privacy and governance.

## Follow a layered approach

**Follow a layered approach to reflect different lifecycles.** Keep foundational layers static for most of your workload’s lifecycle, while application layers change more frequently. Use separate deployment pipelines for each layer so you can apply changes independently and appropriately. 

A landing zone sits at the lowest layer of your architecture. It’s a logical grouping of foundational elements—such as subscriptions, management groups, resource groups, governance controls, and network topology—that enables you to deploy and operate workloads consistently. It gives central operations or platform teams a repeatable environment configuration. To ensure consistency, Azure landing zones include common design areas, a reference architecture, a reference implementation, and a process to tailor deployments to your design requirements. The design principles recommend practices based on policy-driven governance alongside subscription democratization. A landing zone should require minimal changes over the course of your workload lifecycle. To see an example of a landing zone with recommended practices for Azure, see [What is an Azure landing zone](/azure/cloud-adoption-framework/ready/landing-zone). 

Keep your core infrastructure—such as ingress and egress network controllers, load balancers, network routing solutions, DNS, and core servers—stable, with infrequent major changes. Expect and plan for more frequent configuration updates to these components. 

Your application and data layers typically require frequent configuration updates and infrastructure changes. Use the most dynamic deployment pipelines for these components. 

## Incorporate comprehensive types of testing

**Plan for a holistic testing strategy.** A core tenet of system reliability is the *shift left* principle. Developing and deploying an application is a process that has steps going from left to right. Don't wait until the end to test, move tests as early as possible so you catch problems when they're cheaper to fix. Defects discovered late in the lifecycle can be costly or even impossible to correct.

Test all code, including application code, infrastructure templates, and configuration scripts. Version-control the environment that runs applications and deploy it through the same mechanisms you use for application code. Use the same testing approaches your teams use for application code to validate the environment. 

Use automated testing when possible to ensure consistency. Include the following types of testing in your supply chain design.

- *Unit testing*: Run unit tests as part of a continuous integration routine. Unit tests should be extensive and quick. They should ideally cover 100 percent of the code and run in under 30 seconds.

    Implement unit testing to verify that the syntax and functionality of individual modules of code work the way that they should, such as producing a defined output for a known input. You can also use unit tests to verify that IaC assets are valid.

    Apply unit tests to all code assets, including templates and scripts.

- *Smoke testing*: Smoke tests verify that a workload can be stood up in a test environment and performs as expected. Smoke tests don't verify the interoperability of components.

    Smoke tests verify that the deployment methodology for the infrastructure and the application works, and that the system responds as intended after the process finishes.

- *Integration testing*: Integration tests ensure that the application components operate individually, and then determine whether components can interact with each other as they should.

    It can take a considerable amount of time to run a large integration test suite. For this reason, it's best to incorporate the shift-left principle and test early in the software development lifecycle. Reserve integration tests for scenarios that you can't test with a smoke test or unit test.

    You can run long-running test processes on a regular interval if needed. A regular interval offers a good compromise and detects interoperability problems between application components no later than one day after they're introduced.

    Some testing scenarios benefit from manual runs. Use manual testing when you need to introduce human interactivity elements into tests.

- *Acceptance testing*: Depending on the context, you can manually perform acceptance testing. It can be partially or fully automated. Acceptance testing determines whether the software system meets the requirement specifications.

    The main purpose of this test is to evaluate the system's compliance with the business requirements and determine whether the system meets the required criteria for delivery to end users.

> :::image type="icon" source="../_images/ai.svg"::: **AI opportunity**: Add AI to your testing strategy to find customer-focused edge cases and complex scenarios that are hard to automate. Start by using your existing analytics and reporting to identify coverage gaps, then use tools like GitHub Copilot to generate new test cases and scripts. Use AI to optimize your test suite—surface hard-to-detect defects and remove redundant tests to streamline execution and improve speed and efficiency.
> Consider an AI solution that analyzes production usage, monitoring data, and historical defects to find patterns and automatically create tests in your codebase that match your organization's standards. Sanitize and protect sensitive information before you send data to AI systems. Enforce least-privilege access to telemetry and code artifacts. Maintain audit logs for all AI-generated changes. Plan for integration, ongoing operations, and regular model updates to address model drift and maintain effectiveness over time.

## Implement quality gates in code promotion processes

**Implement quality gates throughout your code promotion process via testing**. Promote your code through lower environments—development and testing—before advancing to higher environments like staging and production. Define clear quality gates and targets for each stage and only promote changes to production after they meet those criteria. Your business requirements determine the focus of your quality gates. Also consider the fundamental Well-Architected Framework principles: [Security](../security/test.md), [Reliability](../reliability/testing-strategy.md), and [Performance Efficiency](../performance-efficiency/performance-test.md).

Integrate approval workflows into your quality gates. Clearly define and automate approval workflows when appropriate. Define quality acceptance criteria into your automation, so you can move through your gates efficiently and safely.

> :::image type="icon" source="../_images/ai.svg"::: **AI opportunity**: Use AI to eliminate review bottlenecks by auto routing PRs to the right SMEs. Begin with GitHub Copilot to assess scope and impact, then add an agentic CI/CD integration to evaluate code changes, configurations, and approval patterns. Grant least privilege access only to required artifacts—repositories, pipelines, configuration data, incident history, and approval logs—to assess impact, assign reviewers, surface bottlenecks, and recommend auto approval or extra review. For high-value workloads, train on historical data to predict deployment risk and approval outcomes. Keep humans in the loop. Be cautious with auto approval. Plan for integration, operations, and regular model updates.

## Azure facilitation

[Azure DevOps](https://azure.microsoft.com/products/devops) is a collection of services that help you build a collaborative, efficient, and consistent development practice.

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines) provides build and release services to support CI/CD in your applications.

[GitHub Actions for Azure](https://azure.github.io/actions) integrates with Azure to simplify deployments. Use GitHub Actions to automate CI/CD processes. You can create workflows that build and test every pull request to your repository, or deploy merged pull requests to production.

You can use Terraform, Bicep, and Azure Resource Manager for IaC deployments. Depending on your requirements and your team's familiarity with the tools, you might use one or more of these tools for your deployments and management of the resources.

## Example

For an example that shows how to use Azure Pipelines to build a CI/CD pipeline, see [Azure Pipelines baseline architecture](/azure/devops/pipelines/architectures/devops-pipelines-baseline-architecture).

## Related links

- [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops)
- [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines)
- [Deployment Stamps pattern](/azure/architecture/patterns/deployment-stamp)
- [GitHub Actions for Azure](https://azure.github.io/actions)
- [Performance Efficiency pillar](../performance-efficiency/performance-test.md)
- [Reliability pillar](../reliability/testing-strategy.md)
- [Security pillar](../security/test.md)

## Operation Excellence checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Operational Excellence checklist](checklist.md)
