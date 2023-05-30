---
title: Testing your app and Azure environment
description: Learn about DevOps testing considerations to make when designing your workload. Get information about automated testing and application manual testing in Azure.
author: erjosito
ms.author: martinek
ms.date: 05/09/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-pillar-excellence
---

# Testing your application and Azure environment

Testing is a fundamental component in DevOps and agile development in general. If automation gives DevOps the required speed and agility to deploy software quickly, then only the extensive testing of those deployments achieves the required reliability that customers demand.

A main tenet of system reliability is the "shift-left" principle. If developing and deploying an application is a process depicted as a series of steps going from left to right, testing shouldn't be limited to the very end of the process. It should be shifted as much to the beginning, meaning to the left, as possible. Errors are cheaper to repair when caught early. They can be expensive or impossible to fix later in the application life cycle.

Testing should occur on all code, including application code, infrastructure templates, and configuration scripts. As described in [Repeatable infrastructure][iac], the environment where applications are running should be version-controlled and deployed through the same mechanisms as application code. The environment can then be tested and validated by using the same testing paradigms that teams already use for application code.

A range of testing tools are available to automate and streamline different kinds of testing. These tools include [Azure Load Testing][alt], [Azure Pipelines][pipelines] for automated testing and [Azure Test Plans][devopstests] for manual testing.

There are multiple stages at which tests can be performed in the life cycle of code, and each of them has particularities that are important to understand. In this guide, you can find a summary of the different tests that you should consider while developing and deploying applications.

## Automated testing

Automating tests is the best way to make sure that they're used consistently. Depending on how frequently tests are performed, they're typically limited in duration and scope, as the following different types of automated tests show.

### Unit testing

Unit tests are tests typically run as part of the continuous integration routine. Unit Tests should be extensive and quick, ideally covering 100% of the code, and running in under 30 seconds.

Unit testing can verify that the syntax and functionality of individual modules of code are working the way they should, for example, producing a defined output for a known input. Unit tests can also be used to verify that infrastructure as code assets are valid.

Unit tests should be applied to all code assets, including templates and scripts.

### Smoke testing

Smoke tests verify that a workload can be stood up in a test environment and performs as expected. Smoke tests don't go to the extent of integration tests as smoke tests don't verify the interoperability of different components.

Instead, they verify that the deployment methodology for both the infrastructure and the application works and that the system responds as intended once the process is complete.

### Integration testing

After making sure that the different application components operate individually, integration testing determines whether they can interact with each other as they should.

Running a large integration test suite can take a considerable amount of time, which is why tests should be performed as early as possible (the shift-left principle) in the software development lifecycle. Integration tests should be reserved to scenarios that can't be tested with a smoke or unit test.

Long running test processes can be run on a regular interval if needed. A regular interval offers a good compromise, detecting interoperability issues between application components no later than one day after they were introduced.

## Manual testing

Manual testing is more expensive than automated testing, so it's usually run less frequently. Manual testing is fundamental for the correct functioning of the DevOps feedback loop. It corrects errors before they become too expensive to repair or cause customer dissatisfaction.

## Acceptance testing

Depending on the context, acceptance testing is sometimes performed manually. It can be partially or fully automated. Its purpose is to determine whether the software system has met the requirement specifications.

The main purpose of this test is to evaluate the system's compliance with the business requirements and verify if it has met the required criteria for delivery to end users.

Tools that include [Usage analysis with Application Insights][telemetry] can help you work out how people use your application. These tools let you decide whether a new feature has improved your applications without bringing unintended adverse effects.

The next section of this article covers methods for testing in production, which can be used to verify if features have met business targets with real world user behavior.

## Testing and experimentation in production

Depending on your chosen deployment strategy, Azure platform features might help you conduct experiments in production.

Azure AppService, for example, comes with the slot functionality to [Set up staging environments][slots] that let you have two different versions of the same application running at the same time. Slots let you use A/B testing by directing part of the user traffic to one version of the application and the rest of the traffic to another.

There are multiple popular approaches to experimentation in production:

- **Blue/Green deployments:** When deploying a new application version, you can deploy it in parallel to the existing one. Deploying in parallel lets you start redirecting clients to the new version. If everything goes well, you can then decommission the old version. If there's any problem with the new deployment, you can redirect the users back to the deployment with the previous version.
- **Canary releases:** You can expose the new functionality of your application in a staggered way to select groups of users. You can extend the functionality to a larger group of users if:
  - Users are satisfied with the new functionality.
  - The functionality performs as expected with the control group.
- **A/B testing:** A/B testing is similar to canary release testing. While canary release testing focuses on mitigating risk, A/B testing focuses on evaluating two versions of an application or feature side by side. For example, if you have two versions of the layout of your application, you could send half of your users to one, half to the other, and use metrics to see which layout is more successful.

## Stress testing

As other sections of this framework have explained, designing your application code and infrastructure for scalability is of paramount importance. Stress tests help you verify regularly that your application and your environment adapt to changing load conditions.

During these stress tests, it's critical to monitor all the components of the system to identify potential bottlenecks and to establish a clear baseline to test against.

Every component of the system that isn't able to scale out can become a scale limitation, such as active or passive network components or databases. It's important to know the limits of components so that you can mitigate their effect when it comes to application scale.

Another important part of stress tests is to verify that the infrastructure scales back down to its normal condition in order to keep costs under control.

## Business continuity testing

Business continuity testing offers opportunities for improving prevention and recovery methods for large-scale incidents. It also increases resilience to mundane incidents such as isolated hardware failure or data corruption.

### Disaster recovery drills

Disaster recovery drills are important for verifying that the existing backup strategy is complimented by a working recovery procedure. These drills should be conducted regularly.

Tools such as Azure Site Recovery make it possible to start an isolated copy of the primary workload location in a secondary environment, so that it can be verified that the workload has recovered as it should in an emergency.

In case a problem occurs during the drill, the disaster recovery procedure can be optimized, and the infrastructure in the secondary environment can be deleted safely.

### Exploratory testing

During exploratory testing, experts explore the application in its entirety trying to find faults or suboptimal implementations of functionality. These experts could be developers, UX specialists, product owners, actual users, and other profiles.

Structured test plans are typically not used since testing is left to the ability of the individual tester.

### Fault injection

Fault injection tests if the application is resilient to infrastructure failures. The technique introduces faults in the underlying infrastructure and observes how the application behaves.

Fault injection testing is fundamental to build trust in your redundancy mechanisms. Shutting down infrastructure components, purposely degrading performance, or introducing faults are ways of verifying that the application is going to react as expected when these situations occur.

Products like [Azure Chaos Studio](/azure/chaos-studio/chaos-studio-overview) aim to simplify the process of fault injection testing. Many larger organizations have built their own chaos engines that use automated testing tools to achieve fault injection.

## Summary

In order to deploy software quickly and reliably, testing is a fundamental component of the development and deployment life cycle. It's important to make sure that the application is going to perform as expected in every situation. We therefore need to not only test application code but all aspects of our workload.

## Next steps

> [!div class="nextstepaction"]
> [Release Engineering: Performance ](./release-engineering-performance.md)

<!-- testing -->
[iac]: automation-infrastructure.md
[pipelines]: /azure/devops/pipelines
[devopstests]: /azure/devops/test
[telemetry]: /azure/azure-monitor/app/usage-overview
[slots]: /azure/app-service/deploy-staging-slots
[alt]: /azure/load-testing/overview-what-is-azure-load-testing
