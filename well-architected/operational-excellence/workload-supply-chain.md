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

This guide describes the best practices for designing a workload development supply chain based on CI/CD pipelines that ensure a predictable, efficient workload lifecycle across all of your environments. To ensure that you have a predictable, standardized method of maintaining your workload, you should start with developing a supply chain. CI/CD pipelines are the manifestation of the supply chain, but you should have a single supply chain and you may have many pipelines that all follow the same processes, using the same tools.

Your supply chain is the way that you protect your workload from self-inflicted wounds caused by a lack of control of the changes that your workload undergoes. If you don't have a full accounting of the state of your workload at all times, you are at risk of experiencing unpredictable behavior compounded by a potential need to spend critical time retracing unaccounted for changes when issues arise. You can minimize these risks by standardizing on the processes and tools that define your supply chain and ensuring that your workload team fully commits to their use.

**Definitions**


|Term  |Definition  |
|---------|---------|
|Supply chain     | In the context of cloud workloads, a supply chain is a standardized suite of tools and processes that you use to affect infrastructure and application change across environments.        |

## Key design strategies

> [!NOTE]
> The recommendations provided in this guide refer to workload environments in the code promotion chain. Sandbox or other exploratory and proof-of-concept environments require far less rigor and structure.

### Core tenets

The following recommendations can help you define the core tenets of your supply chain.

**All proposed workload changes are made through the supply chain processes and tools.** To ensure that your workload's configuration, across all environments, is standardized, well-defined, and tightly controlled, enforce a strict policy of automated template-based deployments. No updates to any environment in the code promotion chain should be done through any manual process nor human interaction with the cloud platform's control plane (for example, the portal or an API). All changes to the environment are performed through a pipeline following the deployment practices you have defined. To help enforce this policy, consider limiting access to read-only as a default and using an access authorization gate to allow write access.

An important aspect of this tenet is that all changes are *proposed* *changes* until they are deployed into production. Through automated testing, like integration and smoke testing, your supply chain should be enabled to reject changes automatically.

**Deploy repeatable and immutable infrastructure as code**. Infrastructure as code (IaC) is the management of infrastructure in a descriptive model, using a versioning system that mirrors what\'s used for source code. When you\'re creating an application, the same source code should generate the same binary every time it\'s compiled. In a similar manner, an IaC model generates the same environment every time it\'s applied.

Following an IaC process ensures that the whole team follows a standard, well-established process. Historically, some organizations designate a single or small set of individuals responsible for deploying and configuring infrastructure. By following a fully automated process, responsibility for infrastructure deployments moves from individuals into the automation process and tooling. This move broadens the number of team members who can initiate infrastructure deployments while maintaining consistency and quality.

Repeatable infrastructure involves designing your workload as a logical group of components that can be bundled into one template, making it easy to deploy repeatedly. As described in the [deployment stamp design pattern](/azure/architecture/patterns/deployment-stamp), you can think of these bundles as "stamps" or "units of scale". When you need to deploy your workload (or a subset of your workload, depending on how you design your stamps) to scale out into another region or zone within the same region, you simply deploy the stamp through your pipeline. Be sure to include networking components in your IaC pipelines to ensure that your deployment stamps automatically connect to existing resources.

Prefer immutable infrastructure over mutable infrastructure to optimize your IaC pipeline for consistency and efficiency. Using immutable infrastructure ensures that all systems in scope are replaced with the updated configuration simultaneously and identically with each deployment.

**Use one set of code assets and artifacts across all environments and pipelines.** A common pain point for organizations is when nonproduction environments are dissimilar to production environments. Building production and nonproduction environments by hand can result in mismatched configurations between the various environments. This mismatch slows down the testing of changes and reduces confidence that changes won\'t harm a production system. When following an IaC approach, this problem is minimized. When you use IaC automation, the same infrastructure configuration files can be used for all environments (with parameters that can be changed to meet the requirements for each environment), producing almost identical environments.

There will normally be a variance between production and nonproduction environments to control costs. You often won't need the same degree of redundancy and performance in nonproduction environments as you will in production, so the number and SKU of your resources may differ between environments but ensuring that you control and understand the variance by using standardized parameters will help you maintain the level of predictability you need as you promote your changes.

**Reflect your organizational structure in your supply chain and pipeline designs.** Your organization may be siloed between various teams, each owning some piece of the supply chain. For example, many organizations have teams that own various infrastructure domains like networking, data and compute resources, and these teams are also separate from development teams that own application development, testing and deployments. In other organizations, development and infrastructure teams have been incorporated into DevOps teams that collectively own all infrastructure and application deployments. These are just two broad examples and there are many different ways to organize the teams involved in a supply chain. Regardless of the extent to which your teams are incorporated, your supply chain relies on all of the teams to work together seamlessly. Ensure that standard processes and tools are followed throughout the organization to make the supply chain as efficient as possible.

Likewise, your supply chain may rely on third party vendors if pieces of the workload lifecycle are outsourced. These vendors are just as critical as internal resources to the success of your supply chain, so ensure that there is a mutual agreement across all teams about the processes and tools that you have standardized on.

**Standardize your deployment method.** Discuss with the product owner the acceptable amount of downtime for your workload in production. Depending on how much, if any, downtime is acceptable, you can choose the deployment method that is right for your requirements. Performing deployments during a maintenance window is preferable to reduce complexity and risk, but if the requirement is that no downtime is acceptable, employ a blue-green deployment method.

Using an approach of progressive exposure reduces your risk of introducing undetected bugs to your customers at large. Also known as canary deployments, this method deploys to controlled groups of users in a gradual sequence to catch issues before they affect more users. The initial rollout group can be a subsection of your customers that are aware of the rollout strategy and can tolerate some amount of unexpected behavior and provide feedback, or it can be a group of internal users, which helps contain the blast radius of bugs during the rollout.

As part of defining your deployment method, adopt a standard policy of only deploying the smallest viable change in each deployment. Depending on factors like the criticality of the workload and the complexity of the components, you will need to determine what the smallest viable change looks like. If you are using immutable infrastructure, the smallest viable change would normally include the deployment of resources with the latest configuration to replace those running the previous version. If you are using mutable infrastructure, you may decide that the smallest viable change would only be a single update on the group of resources in scope.

**Follow a layered approach to reflect different lifecycles**. Foundational layers should remain static throughout most of the overall workload lifecycle, whereas application layers are expected to change frequently. To account for this, you should have different pipelines to affect changes at each layer.

At the lowest layer are your landing zone, which is a logical grouping of foundational elements like subscriptions, management groups, resource groups, governance functions and networking topology that enable workloads to easily be deployed and managed. A [landing zone](/azure/cloud-adoption-framework/ready/landing-zone/) in the context of Azure provides central operations teams (also known as platform teams) with a repeatable approach to environmental configuration. To deliver consistent environments, all Azure landing zones provide a set of common design areas, reference architecture, reference implementation, and a process to modify that deployment to fit the organization design requirements. The [Azure landing zone design principles](/azure/cloud-adoption-framework/ready/landing-zone/design-principles) provide recommended practices based on policy-driven governance alongside subscription democratization. The landing zone should require very little change over the course of your workload lifecycle.

Your core infrastructure and functions like ingress and egress network controllers, load balancing and network routing solutions, DNS management and core servers should also require infrequent major changes, but may require more frequent configuration updates.

Your application and data layer will require frequent configuration updates and more frequent infrastructure changes so these components should have the most dynamic pipelines.

**Plan for a holistic testing strategy**. A core tenet of system reliability is the \"shift-left\" principle. If developing and deploying an application is a process depicted as a series of steps going from left to right, testing shouldn\'t be limited to the very end of the process. It should be shifted as much to the beginning, meaning to the left, as possible. Errors are cheaper to repair when caught early. They can be expensive or impossible to fix later in the application lifecycle.

Testing should occur on all code, including application code, infrastructure templates, and configuration scripts. The environment where applications are running should be version-controlled and deployed through the same mechanisms as application code, so the environment can be tested and validated using the same testing paradigms that teams already use for application code.

Prefer automated testing when possible to ensure consistency. The following types of testing should be included in your supply chain design and should be automated to the extent possible.

- **Unit testing**

    Unit tests are tests typically run as part of the continuous integration routine. Unit Tests should be extensive and quick, ideally covering 100% of the code, and running in under 30 seconds.
    
    Unit testing can verify that the syntax and functionality of individual modules of code are working the way they should, for example, producing a defined output for a known input. Unit tests can also be used to verify that infrastructure as code assets are valid.
    
    Unit tests should be applied to all code assets, including templates and scripts.

- **Smoke testing**

    Smoke tests verify that a workload can be stood up in a test environment and performs as expected. Smoke tests don\'t go to the extent of integration tests as smoke tests don\'t verify the interoperability of different components.
    
    Instead, they verify that the deployment methodology for both the infrastructure and the application works and that the system responds as intended once the process is complete.

- **Integration testing**

    After making sure that the different application components operate individually, integration testing determines whether they can interact with each other as they should.
    
    Running a large integration test suite can take a considerable amount of time, which is why tests should be performed as early as possible (the shift-left principle) in the software development lifecycle. Integration tests should be reserved to scenarios that can\'t be tested with a smoke or unit test.
    
    Long running test processes can be run on a regular interval if needed. A regular interval offers a good compromise, detecting interoperability issues between application components no later than one day after they were introduced.
    
    There are some testing scenarios that benefit from being run manually. Use manual testing when you need to introduce human interactivity elements into the tests.

- **Acceptance testing**

    Depending on the context, acceptance testing is sometimes performed manually. It can be partially or fully automated. Its purpose is to determine whether the software system has met the requirement specifications.
    
    The main purpose of this test is to evaluate the system\'s compliance with the business requirements and verify if it has met the required criteria for delivery to end users.

**Build quality gates throughout your code promotion process through your testing**. As you deploy your code into lower environments (like dev/test) and up through higher environments (like staging and production), you should pass through quality gates to ensure that your quality targets are met before changes hit production. Your business requirements will determine what the distinct focus of your quality gates will be, but they should take into consideration fundamental WAF principles: security, reliability, and performance. See the security (link to security testing guide), reliability (link to reliability testing guide), and performance (link to performance testing guide) guides for further testing guidance.

Additionally, approval workflows should be integrated into your quality gates. Approval workflows should be clearly defined and automated when appropriate. By defining quality acceptance criteria into your automation, you can move through your gates efficiently and safely.

## Azure facilitation

[Azure DevOps](/azure/devops/user-guide/what-is-azure-devops) is a collection of services that help you build a collaborative, efficient, and consistent development practice.

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) provides build and release services to support continuous integration and delivery of your applications.

[GitHub for Actions for Azure](https://azure.github.io/actions/) allows you to automate CI/CD processes and integrates directly with Azure to simplify deployments. You can create workflows that build and test every pull request to your repository, or deploy merged pull requests to production. 

Terraform, Bicep and ARM can all be used for IaC deployments. Depending on your requirements and your team's familiarity with the tools, you may use one or more of these tools for your deployments and management of the resources. 

## Tradeoffs

In your lower environments, there will be a tradeoff between cost and infrastructure parity with production. Each successively higher environment should be closer in parity to production generally speaking to ensure that your quality gates are as effective as possible.

## Example

See the [Azure Pipelines baseline architecture](/azure/devops/pipelines/architectures/devops-pipelines-baseline-architecture) for an example of using Azure Pipelines to build a CI/CD pipeline.

## Related links
