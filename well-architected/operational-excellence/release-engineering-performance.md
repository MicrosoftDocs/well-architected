---
title: Recommendations for improving build velocity
description: Learn about boosting the performance of your deployment infrastructure. Review the considerations about build times and human intervention.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for improving build velocity

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:**

|[OE:04](checklist.md)| Optimize software development and quality assurance processes by following industry-proven practices for development and testing. For unambiguous role designation, standardize practices across components such as tooling, source control, application design patterns, documentation, and style guides. |
|---|---|

**Related guides**: [Recommendations for optimizing development practices through standardization](optimize-development-practices.md) | [Recommendations for using continuous integration](release-engineering-continuous-integration.md)

 It's important to have a build process up and running the first day of your product development. The build status shows if your product is deployable, so builds are the heartbeat of your continuous delivery system. Since builds provide such crucial information about the status of your product, you should always strive for fast builds.

It's difficult to fix a build problem if it takes longer to build. When delays happen and become normalized, teams tend to become less motivated to fix the problem.

## Key design strategies

### Build times

There are a few ways you can achieve faster builds, such as:

- **Choose agents that meet your performance requirements**: Speed up your builds by selecting the right build machines. Fast machines can make the difference between hours and minutes. If your pipelines are in Azure Pipelines, you can run your jobs conveniently by using a Microsoft-hosted agent. Using Microsoft-hosted agents, maintenance and upgrades are taken care of for you. For more information, see [Microsoft-hosted agents](/azure/devops/pipelines/agents/hosted?view=azure-devops&preserve-view=true).

- **Build server location**: When you're building your code, data is sent across the wire. Inputs to the builds are fetched from a source control repository and the artifact repository. The output from the build process needs to be copied, including the compiled artifacts, test reports, the code coverage results, and the debug symbols. It's important that these copy actions are run quickly. If you use your own build server, ensure that the build server is located near the sources and a target location. Fast uploads and downloads can reduce the overall build time.

- **Scale out build servers**: A single build server might be sufficient for a small product. As the size and scope of the product and the number of teams working on the product increases, a single server might not be enough. Scale your infrastructure horizontally over multiple machines when you reach the limit. For more information, see [Create and manage agent pools](/azure/devops/pipelines/agents/pools-queues?tabs=yaml&view=azure-devops&preserve-view=true).

- **Optimize the build**:

  - Add parallel jobs to speed up the build process. For more information, see [Configure and pay for parallel jobs](/azure/devops/pipelines/licensing/concurrent-jobs?view=azure-devops&preserve-view=true).

  - Enable parallel test suite runs, which often save a large amount of time, especially when running integration and UI tests. For more information, see [Run tests in parallel for any test runner](/azure/devops/pipelines/test/parallel-testing-any-test-runner?view=azure-devops&preserve-view=true).

  - Use the notion of a multiplier, where you can scale out your builds over multiple build agents. For more information, see [Specify jobs in your pipeline](/azure/devops/pipelines/process/phases?tabs=yaml&view=azure-devops&preserve-view=true).

  - Consider moving integration, UI, and smoke tests to a release pipeline. Moving to a release pipeline improves the build speed and the speed of the build feedback loop.

  - Publish the build artifacts to a package management solution, such as NuGet or Maven. Publishing to a package management solution lets you reuse your build artifact more easily.

### Human intervention

Your organization might choose to create several different kinds of builds to optimize build times. Possible builds include: 

- **Continuous integration (CI) build**: The purpose of this build is to ensure code compiles and unit tests are run. This build gets triggered at each commit. It serves as the heartbeat of the project and provides quality feedback to the team immediately. For more information, see [Specify events that trigger pipelines](/azure/devops/pipelines/build/triggers?tabs=yaml&view=azure-devops&preserve-view=true).

- **Nightly build**: The purpose of a nightly build isn't only to compile the code, but also to ensure any larger test suites that are inefficient run on a regular cadence for each build. Usually, these cadences are integration, UI, or smoke tests. For more information, see [Configure schedules for pipelines](/azure/devops/pipelines/process/scheduled-triggers).

- **Release build**: In addition to compiling and running tests, this build also compiles the API documentation, compliance reports, code signing, and other steps that aren't required every time the code is built. This build provides the golden copy that's pushed to the release pipeline to finally deploy in the production environment.

The types of builds needed by your organization depend on factors including your team's and organization's maturity, the kind of product you're working on, and your deployment strategy.

## Azure facilitation

[Azure DevOps](/azure/devops/user-guide/what-is-azure-devops) is a collection of services that help you build a collaborative, efficient, and consistent development practice.

Use [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) to build and release services to support continuous integration and delivery of your applications.

[GitHub Actions for Azure](https://azure.github.io/actions/) allows you to automate continuous integration and continuous delivery (CI/CD) processes and integrates directly with Azure to simplify deployments. You can create workflows that build and test every pull request to your repository, or deploy merged pull requests to production.

[Microsoft-hosted agents](/azure/devops/pipelines/agents/hosted?view=azure-devops&preserve-view=true&tabs=yaml) are available natively in [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines). These agents are single-use virtual machines that are only used for one job and then discarded, which provides an easy-to-manage option for your builds.

## Related links

- [Create and manage agent pools](/azure/devops/pipelines/agents/pools-queues?tabs=yaml&view=azure-devops&preserve-view=true)
- [Configure and pay for parallel jobs](/azure/devops/pipelines/licensing/concurrent-jobs?view=azure-devops&preserve-view=true)
- [Run tests in parallel for any test runner](/azure/devops/pipelines/test/parallel-testing-any-test-runner?view=azure-devops&preserve-view=true)
- [Specify jobs in your pipeline](/azure/devops/pipelines/process/phases?tabs=yaml&view=azure-devops&preserve-view=true)
- [Specify events that trigger pipelines](/azure/devops/pipelines/build/triggers?tabs=yaml&view=azure-devops&preserve-view=true)
- [Configure schedules for pipelines](/azure/devops/pipelines/process/scheduled-triggers)

## Community links

- [GitHub for Actions for Azure](https://azure.github.io/actions/)

## Operational Excellence checklist

> [!div class="nextstepaction"]
> [Design review checklist for Operational Excellence](checklist.md)
