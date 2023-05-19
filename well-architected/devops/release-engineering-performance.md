---
title: Performance of deployment infrastructure
description: Learn about boosting the performance of your deployment infrastructure. Review the considerations about build times and human intervention.
author: UmarMohamedUsman
ms.author: martinek
ms.date: 05/09/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-pillar-excellence
products:
  - azure-pipelines
ms.custom:
  - fasttrack-edit
  - article
---

# Performance considerations for your deployment infrastructure

Build status shows if your product is in a deployable state, so builds are the heartbeat of your continuous delivery system. It's important to have a build process up and running the first day of your product development. Since builds provide such crucial information about the status of your product, you should always strive for fast builds.

It's difficult to fix a build problem if it takes longer to build. When delays happen and become normalized, teams tend to become less motivated to fix the problem.

## Build times

Here are few ways you can achieve faster builds:

* **Choosing agents that meet your performance requirements:** Speeding up your builds starts with selecting the right build machines. Fast machines can make the difference between hours and minutes. If your pipelines are in Azure Pipelines, then you've got a convenient option to run your jobs by using a Microsoft-hosted agent. With Microsoft-hosted agents, maintenance and upgrades are taken care of for you. For more information, see [Microsoft-hosted agents](/azure/devops/pipelines/agents/hosted?view=azure-devops&preserve-view=true).

* **Build server location:** When you're building your code, data is sent across the wire. Inputs to the builds are fetched from a source control repository and the artifact repository. At the end, the output from the build process needs to be copied, including not only the compiled artifacts, but also the test reports, the code coverage results, and the debug symbols. It's important that these copy actions are fast. If you use your own build server, ensure that the build server is located near the sources and a target location. Fast uploads and downloads can reduce the overall build time.

* **Scaling out build servers:** A single build server might be sufficient for a small product. As the size and the scope of the product and the number of teams working on the product increases, a single server might not be enough. Scale your infrastructure horizontally over multiple machines when you reach the limit. For more information, see [Create and manage agent pools](/azure/devops/pipelines/agents/pools-queues?tabs=yaml&view=azure-devops&preserve-view=true).

* **Optimizing the build:**

  * Add parallel jobs to speed up the build process. For more information, see [Configure and pay for parallel jobs](/azure/devops/pipelines/licensing/concurrent-jobs?view=azure-devops&preserve-view=true).

  * Enable parallel test suite runs, which often save a large amount of time, especially when running integration and UI tests. For more information, see [Run tests in parallel for any test runner](/azure/devops/pipelines/test/parallel-testing-any-test-runner?view=azure-devops&preserve-view=true).

  * Use the notion of a multiplier, where you can scale out your builds over multiple build agents. For more information, see [Specify jobs in your pipeline](/azure/devops/pipelines/process/phases?tabs=yaml&view=azure-devops&preserve-view=true).

  * Consider moving integration, UI, and smoke tests to a release pipeline. Moving to a release pipeline improves the build speed and the speed of the build feedback loop.

  * Publish the build artifacts to a package management solution, such as NuGet or Maven. Publishing to a package management solution lets you to reuse your build artifact more easily.

## Human intervention

Your organization might choose to create several different kinds of builds to optimize build times.

* **CI builds:** The purpose of this build is to ensure code compiles and unit tests are run. This build gets triggered at each commit. It serves as the heartbeat of the project and provides quality feedback to the team immediately. For more information, see [Specify events that trigger pipelines](/azure/devops/pipelines/build/triggers?tabs=yaml&view=azure-devops&preserve-view=true).

* **Nightly build:** The purpose of a nightly build isn't only to compile the code, but also to ensure any larger test suites that are inefficient run for each build on a regular cadence. Usually, these cadences are integration, UI, or smoke tests. For more information, see [Configure schedules for pipelines](/azure/devops/pipelines/process/scheduled-triggers).

* **Release build:** In addition to compiling and running tests, this build also compiles the API documentation, compliance reports, code signing, and other steps that aren't required every time the code is built. This build provides the golden copy that's pushed to the release pipeline to finally deploy in the production environment.

The types of builds needed by your organization depend on factors including your team's and organization's maturity, the kind of product you're working on, and your deployment strategy.

## Next steps

> [!div class="nextstepaction"]
> [Release Engineering: Deployment ](./release-engineering-cd.md)
