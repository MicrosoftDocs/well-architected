---
title: Recommendations for designing a workload development supply chain
description: Learn how to design a workload development supply chain based on CI/CD pipelines that ensure a predictable, efficient workload lifecycle across all of your environments. 
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for designing a workload development supply chain

**Applies to: OE 06**

This guide describes the recommendations for designing a workload development supply chain that's based on continuous integration and continuous delivery (CI/CD) pipelines. that ensure a predictable, efficient workload lifecycle across all your environments. Develop a supply chain to ensure that you have a predictable, standardized method of maintaining your workload. CI/CD pipelines are the manifestation of the supply chain, but you should have a single supply chain. And you might have several pipelines that follow the same processes and use the same tools.

Your supply chain is the way that you protect your workload from self-inflicted wounds caused by a lack of control of the changes that your workload undergoes. If you don't have a full accounting of the state of your workload at all times, you are at risk of experiencing unpredictable behavior compounded by a potential need to spend critical time retracing unaccounted for changes when issues arise. You can minimize these risks by standardizing on the processes and tools that define your supply chain and ensuring that your workload team fully commits to their use.

**Definitions**

|Term  |Definition  |
|---------|---------|
|Supply chain     | In cloud workloads, a supply chain is a standardized suite of tools and processes that you use to affect infrastructure and application change across environments.        |

## Key design strategies

> [!NOTE]
> The recommendations provided in this guide refer to workload environments in the code promotion chain. Sandbox or other exploratory and proof-of-concept environments require far less rigor and structure.

### Core tenets

The following recommendations can help you define the core tenets of your supply chain.

**All proposed workload changes are made through the supply chain processes and tools.** Enforce a strict policy of automated template-based deployments. This method helps ensure that your workload's configuration across all environments is standardized, well-defined, and tightly controlled. No updates to any environment in the code promotion chain should be done through any manual process nor human interaction with the cloud platform's control plane (for example, the portal or an API). Incorporate all changes to the environment through a pipeline by following the deployment practices that you defined. To help enforce this policy, consider limiting access to read-only as a default and using an access authorization gate to allow write access.

An important aspect of this tenet is that all changes are *proposed* *changes* until they're deployed into production. Through automated testing, like integration and smoke testing, your supply chain should be enabled to automatically reject changes.

**Deploy repeatable and immutable infrastructure as code (IaC).** IaC is the management of infrastructure in a descriptive model, using a versioning system that mirrors what's used for source code. When you create an application, the same source code should generate the same binary every time it's compiled. In a similar manner, an IaC model generates the same environment every time it's applied.

Follow an IaC process to ensure that your team follows a standard, well-established process. Historically, some organizations designate a single or small group of individuals to deploy and configure infrastructure. When you implement a fully automated process,infrastructure deployments require less management from individuals. The responsibility is transferred to the automation process and tooling. Team members can initiate infrastructure deployments and maintain consistency and quality.

Design your workload as a logical group of components that you can bundle into one template to make deployments easy and repeatable. You can think of these bundles as *stamps* or *units of scale*. For more information, see [Deployment Stamps pattern](/azure/architecture/patterns/deployment-stamp). When you need to deploy your workload to scale out into another region or zone within the same region, deploy the stamp through your pipeline. Depending on how you design your stamps, you might deploy a subset of your workload instead of the entire workload. Include networking components in your IaC pipelines to ensure that your deployment stamps automatically connect to existing resources.

To optimize your IaC pipeline for consistency and efficiency, design an immutable infrastructure rather than a mutable infrastructure. Implement an immutable infrastructure to ensure that all systems in scope are replaced with the updated configuration simultaneously and identically with each deployment.

**Use one set of code assets and artifacts across all environments and pipelines.** A common pain point for organizations is when nonproduction environments are dissimilar to production environments. Building production and nonproduction environments by hand can result in mismatched configurations between the environments. This mismatch slows down testing and increases the risk that changes might harm a production system. An IaC approach minimizes these problems. When you use IaC automation, you can use the same infrastructure configuration files for all environments to produce almost identical environments. You can add parameters to the infrastructure configuration files that you can adjust to meet the requirements for each environment.

There's typically a variance between production and nonproduction environments to control costs. You often don't need the same degree of redundancy and performance in nonproduction environments as you do in production. The number and SKU of your resources might differ between environments. Ensure that you control and understand the variance by using standardized parameters to help you maintain predictability as you make changes.

**Reflect your organizational structure in your supply chain and pipeline designs.** Your organization might be siloed between teams. Each team might own a part of the supply chain. For example, many organizations have teams that own infrastructure domains, like networking, data, and compute resources. These teams are separate from development teams that own application development, testing, and deployments. In some organizations, development and infrastructure teams are incorporated into DevOps teams that collectively own all infrastructure and application deployments. There are many ways to organize the teams that are involved in a supply chain. Your supply chain relies on all the teams to seamlessly work together. Ensure that all teams follow standard processes and use standard tools to make the supply chain as efficient as possible.

Your supply chain might rely on third-party vendors if you outsource parts of the workload lifecycle. These vendors are just as critical to the success of your supply chain as internal resources. Ensure that there's a mutual agreement across all teams about the processes and tools that you standardize.

**Standardize your deployment method.** With the product owner, discuss the acceptable amount of production downtime for your workload. Depending on how much, if any, downtime is acceptable, you can choose the deployment method that's right for your requirements. Ideally perform deployments during a maintenance window to reduce complexity and risk. If no downtime is acceptable, employ a blue-green deployment method.

Use an approach of progressive exposure to reduce the risk of introducing undetected bugs to your customers at large. Also known as canary deployments, this method deploys to controlled groups of users in a gradual sequence. It catches issues before they affect more users. The initial rollout group might be a subsection of your customers that are aware of the rollout strategy. This subsection of customers can tolerate some amount of unexpected behavior and provide feedback. Or it might be a group of internal users, which helps contain the blast radius of bugs during the rollout.

As part of defining your deployment method, adopt a standard policy of only deploying the smallest viable change in each deployment. Depending on factors like the criticality of the workload and complexity of the components, determine what the smallest viable change looks like. If you use an immutable infrastructure, the smallest viable change is typically the deployment of resources with the latest configuration to replace those running the previous version. If you use a mutable infrastructure, you might decide that the smallest viable change is only a single update on the group of resources in scope.

**Follow a layered approach to reflect different lifecycles.** Foundational layers should remain static throughout most of the workload lifecycle, and application layers change frequently. To account for these differences, you should have different pipelines to affect changes at each layer.

A [landing zone](/azure/cloud-adoption-framework/ready/landing-zone) is at the lowest layer. A landing zone is a logical grouping of foundational elements, like subscriptions, management groups, resource groups, governance functions, and networking topology. A landing zone enables you to easily deploy and manage your workload, and provides central operations teams, or platform teams, with a repeatable approach to an environmental configuration. To deliver consistent environments, all Azure landing zones provide a set of common design areas, reference architecture, reference implementation, and a process to modify that deployment to fit the organization design requirements. The [Azure landing zone design principles](/azure/cloud-adoption-framework/ready/landing-zone/design-principles) provide recommended practices based on policy-driven governance alongside subscription democratization. A landing zone should require minimal change over the course of your workload lifecycle.

Your core infrastructure and functions, like ingress and egress network controllers, load balancing, network routing solutions, DNS management, and core servers, should also require infrequent major changes. But they might require frequent configuration updates.

Your application and data layer requires frequent configuration updates and frequent infrastructure changes. These components should have the most dynamic pipelines.

**Plan for a holistic testing strategy.** A core tenet of system reliability is the *shift left* principle. If developing and deploying an application is a process depicted as a series of steps going from left to right, you shouldn't limit testing to the very end of the process. As much as possible, shift testing to the beginning, or to the left. Errors are cheaper to repair when they're caught early. They can be expensive or impossible to fix later in the application lifecycle.

Test all code, including application code, infrastructure templates, and configuration scripts. The environment that runs applications should be version-controlled and deployed through the same mechanisms as application code. So you can test and validate the environment by using the same testing paradigms that your teams already use for application code.

When possible, use automated testing to ensure consistency. Include the following types of testing in your supply chain design.

- *Unit testing*: Unit tests are tests that you typically run as part of a continuous integration routine. Unit tests should be extensive and quick. They should ideally cover 100 percent of the code and run in under 30 seconds.

    Implement unit testing to verify that the syntax and functionality of individual modules of code work the way they should, for example producing a defined output for a known input. You can also use unit tests to verify that IaC assets are valid.

    Apply unit tests to all code assets, including templates and scripts.

- *Smoke testing*: Smoke tests verify that a workload can be stood up in a test environment and performs as expected. Smoke tests don't verify the interoperability of components.

    Smoke tests verify that the deployment methodology for the infrastructure and the application works, and that the system responds as intended after the process finishes.

- *Integration testing*: Integration tests ensure that the application components operate individually, and then determine whether components can interact with each other as they should.

    It can take a considerable amount of time to run a large integration test suite. That's why it's best to incorporate the shift left principle and perform testing early in the software development lifecycle. Reserve integration tests for scenarios that you can't test with a smoke or unit test.

    You can run long-running test processes on a regular interval if needed. A regular interval offers a good compromise, and detects interoperability issues between application components no later than one day after they are introduced.

    There are some testing scenarios that benefit from running them manually. Use manual testing when you need to introduce human interactivity elements into the tests.

- *Acceptance testing*: Depending on the context, you can manually perform acceptance testing. It can be partially or fully automated. Acceptance testing determines whether the software system meets the requirement specifications.

    The main purpose of this test is to evaluate the system's compliance with the business requirements and verify if the system meets the required criteria for delivery to end users.

**Build quality gates throughout your code promotion process through your testing**. Deploy your code into lower environments, like development and testing, and up through higher environments, like staging and production. You should pass through quality gates to ensure that you meet your quality targets before changes go to production. Your business requirements determine what the focus of your quality gates are. But they should take into consideration the fundamental Well-Architected Framework principles: [Security](../security/testing.md), [Reliability](../reliability/testing-strategy.md), and [Performance Efficiency](../performance-efficiency/performance-testing.md).

Also integrate approval workflows into your quality gates. Clearly define and automate approval workflows when appropriate. Define quality acceptance criteria into your automation, so you can move through your gates efficiently and safely.

## Azure facilitation

[Azure DevOps](/azure/devops/user-guide/what-is-azure-devops) is a collection of services that help you build a collaborative, efficient, and consistent development practice.

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines) provides build and release services to support continuous integration and delivery of your applications.

[GitHub Actions for Azure](https://azure.github.io/actions) integrates with Azure to simplify deployments. Use GitHub Actions to automate CI/CD processes. You can create workflows that build and test every pull request to your repository, or deploy merged pull requests to production.

You can use Terraform, Bicep, and Azure Resource Manager for IaC deployments. Depending on your requirements and your team's familiarity with the tools, you might use one or more of these tools for your deployments and management of the resources.

## Tradeoffs

In your lower environments, there's a tradeoff between cost and infrastructure parity with production. Each successively higher environment should be closer in parity to production to ensure that your quality gates are as effective as possible.

## Example

For an example that shows how to use Azure Pipelines to build a CI/CD pipeline, see [Azure Pipelines baseline architecture](/azure/devops/pipelines/architectures/devops-pipelines-baseline-architecture).

## Related links

- [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops)
- [Azure landing zone design principles](/azure/cloud-adoption-framework/ready/landing-zone/design-principles)
- [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines)
- [Deployment Stamps pattern](/azure/architecture/patterns/deployment-stamp)
- [GitHub Actions for Azure](https://azure.github.io/actions)
- [Landing zones](/azure/cloud-adoption-framework/ready/landing-zone)
- [Performance Efficiency pillar](../performance-efficiency/performance-testing.md)
- [Reliability pillar](../reliability/testing-strategy.md)
- [Security pillar](../security/testing.md)

## Next steps

We recommend that you review the Operational Excellence checklist to explore other concepts.

> [!div class="nextstepaction"]

> [Operational Excellence checklist](checklist.md)
