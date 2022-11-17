---
title: Performance of deployment infrastructure
description: Read about performance considerations to make regarding your deployment infrastructure. Get advice about build times and human intervention.
author: UmarMohamedUsman
ms.author: martinek
ms.date: 12/08/2021
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - azure-pipelines
ms.custom:
  - fasttrack-edit
  - article
---

# Performance considerations for your deployment infrastructure

Build status shows if your product is in a deployable state, so builds are the heartbeat of your continuous delivery system. It's important to have a build process up and running from the first day of your product development. Since builds provide such crucial information about the status of your product, you should always strive for fast builds.

It will be hard to fix build problem if it takes longer to build, and the team will suffer from a broken window disorder. Eventually, nobody cares if the build is broken since it's always broken and it takes lot of effort to fix it.

## Build times

Here are few ways you can achieve faster builds.

* **Choosing agents that meet your performance requirements:** Speeding up your builds starts with selecting the right build machines. Fast machines can make the difference between hours and minutes. If your pipelines are in Azure Pipelines, then you've got a convenient option to run your jobs using a Microsoft-hosted agent. With Microsoft-hosted agents, maintenance and upgrades are taken care of for you. For more info see, [Microsoft-hosted agents](/azure/devops/pipelines/agents/hosted?view=azure-devops&preserve-view=true).

* **Build server location:** When you're building your code, a lot of data is sent across the wire. Inputs to the builds are fetched from a source control repository and the artifact repository. At the end, the output from the build process needs to be copied, not only the compiled artifacts, but also test reports, code coverage results, and debug symbols. It is important that these copy actions are fast. If you are using your own build server, ensure that the build server is located near the sources and a target location. Fast up- and downloads can reduce the overall build time significantly.

* **Scaling out build servers:** A single build server may be sufficient for a small product, but as the size and the scope of the product and the number of teams working on the product increases, the single server may not be enough. Scale your infrastructure horizontally over multiple machines when you reach the limit. For more info see, how you can leverage [Azure DevOps Agent Pools](/azure/devops/pipelines/agents/pools-queues?tabs=yaml&view=azure-devops&preserve-view=true).

* **Optimizing the build:**

  * Add parallel build execution so we can speed up the build process. For more info see, [Azure DevOps parallel jobs](/azure/devops/pipelines/licensing/concurrent-jobs?view=azure-devops&preserve-view=true).

  * Enable parallel execution of test suites, which is often a huge time saver, especially when executing integration and UI tests. For more info see, [Run tests in parallel using Azure Pipeline](/azure/devops/pipelines/test/parallel-testing-any-test-runner?view=azure-devops&preserve-view=true).

  * Use the notion of a multiplier, where you can scale out your builds over multiple build agents. For more info see, [Organizing Azure Pipeline into Jobs](/azure/devops/pipelines/process/phases?tabs=yaml&view=azure-devops&preserve-view=true).

  * Consider moving integration, UI, and smoke tests to a release pipeline. This improves the build speed, and hence the speed of the build feedback loop.

  * Publish the build artifacts to a package management solution. (such as NuGet or Maven) This allows you to reuse your build artifact more easily.

## Human intervention

Your organization might choose to create several different kinds of builds to optimize build times. 

* **CI builds:** The purpose of this build is to ensure code compiles and unit tests are run. This build gets triggered at each commit. It serves as the heartbeat of the project, and provides quality feedback to the team immediately. For more info see, [CI triggers or Batching CI builds](/azure/devops/pipelines/build/triggers?tabs=yaml&view=azure-devops&preserve-view=true).

* **Nightly build:** The purpose of a nightly build is not only to compile the code, but also ensure any larger test suites that are inefficient to run for each build are run on a regular cadence. Usually these are integration, UI, or smoke tests. For more information review how to [schedule builds using cron syntax](/azure/devops/pipelines/process/scheduled-triggers).

* **Release build:** In addition to compiling and running tests, this build also compiles the API documentation, compliance reports, code signing, and other steps which are not required every time the code is built. This build provides the golden copy that will be pushed to the release pipeline to finally deploy in the production environment. 

What types of builds your organization needs depends on many factors including your team's and organizational maturity, the kind of product you are working on, and your deployment strategy.

## Next steps

> [!div class="nextstepaction"]
> [Release Engineering: Deployment ](./release-engineering-cd.md)
