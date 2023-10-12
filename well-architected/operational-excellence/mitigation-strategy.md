---
title: Recommendations for designing a deployment failure mitigation strategy
description: Learn how to design a standardized strategy to effectively handle deployment failures. Be proactive with a well-designed, intentional strategy to deal with deployment failures to enable your workload team to efficiently mitigate failures with as little impact as possible on your end users.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for designing a deployment failure mitigation strategy

**Applies to: OE 12**

This guide describes the best practices for designing a standardized strategy to effectively handle deployment failures. Like other workload issues, deployment failures are an inevitable part of a workload lifecycle, and they should be expected. With this mindset, being proactive with a well-designed, intentional strategy to deal with deployment failures will enable your workload team to efficiently mitigate failures with as little impact as possible on your end users. An absence of such a plan can lead to chaotic and potentially detrimental responses to issues that can have serious impacts on team and organizational cohesion, customer trust and finances.

## Key design strategies

Occasionally, despite the maturity of your development practices, issues with deployments will happen. Using [safe deployment practices](safe-deployments.md) and operating a robust [workload supply chain](workload-supply-chain.md) will help you minimize the frequency of failed deployments. However, you also need to design a standardized strategy to handle failed deployments when they happen. A key to having the right foundation for minimizing the effects on your customers or internal users when deployments fail and to be able to mitigate the issue efficiently is to use a progressive exposure deployment model as your standard practice. 

A deployment failure mitigation strategy is composed of five broad phases:

1. Detection: To respond to a failed deployment, you must first detect the failure. Detection can take several forms, like failed smoke tests, user reported issues, or alerts generated through your monitoring platform.

1. Decision: You must decide what the best mitigation strategy is for the specific failure type.

1. Mitigation: Perform the identified mitigation action. Mitigations can come in the form of fallback, rollback, roll forward, or using a runtime configuration to bypass the offending function.

1. Communications: Stakeholders and affected end users must be made aware of the status as you detect and work through the issue as required by your [emergency response plan](emergency-response.md).

1. Postmortems: Blameless postmortems provide opportunities for the workload team to identify areas for improvement and create plans to apply learnings.

The following sections will provide recommendations for these phases in more detail. These sections assume that you have deployed your changes to one or more groups of users or systems before an issue is detected, but not all groups in your rollout plan have been updated.

### Detection

To quickly identify issues with deployments, you need robust testing and [observability practices](observability.md) as they relate to deployments. Using an Application Performance Management tool and enabling logging through instrumentation (link to OpEx instrumentation guide) will complement your workload monitoring and alerting to help you quickly catch anomalies. 

Smoke testing and other quality testing should happen at each phase of your rollout. Successful tests in one deployment group should not influence decisions to test in subsequent groups.

Having telemetry in place that can correlate user issues with a deployment phase will help you quickly identify which rollout group a particular user is associated with. This is especially important with progressive exposure deployments, as you might have multiple configurations running across your user base at any given point in the deployment.

Finally, be ready to respond to user reported issues immediately. Whenever practical, deploy your rollout phases during working hours to ensure that you have a full support team available. Ensure support staff is trained on how to escalate deployment issues for proper routing. Escalations should align with your workload [emergency response plan](emergency-response.md).

### Decision

Deciding on what the appropriate mitigation strategy will be for a given deployment issue should rely on many factors including, but not limited to:

- What type of progressive exposure model is used (blue-green or canary, for example).

  If you use a blue-green model, falling back is more practical than rolling back as you can easily shift traffic back to the stack running the non-updated configuration. You can then fix the issue in the problematic environment and try your deployment again at the appropriate time.

- What methods are at your disposal for bypassing the issue (use of feature flags, environmental variables, or any other type of runtime configuration property that can be toggled).

  If you can easily bypass the issue by turning off a feature flag or toggling a setting, you might be able to proceed with the rollout or avoid falling back or rolling back while you fix the offending code.

- What the level of effort will be to implement a fix in the code.

  If the level of effort to fix the code is low, rolling forward by implementing a hotfix might be the right choice for a given scenario. 

- What the level of criticality is for the workload affected.

  Mission-critical workloads should always be deployed in a side-by-side manner, like blue-green, to achieve “zero-downtime” deployments. Therefore, falling back is the preferable mitigation strategy for these types of workloads.

- Whether the workload uses mutable or immutable infrastructure

  If your workload is designed to use mutable infrastructure, rolling forward can make sense as you will be updating infrastructure in place. Conversely, rolling back or falling back can make sense when using immutable infrastructure

Whatever choice you make, the decision tree should be codified with appropriate approvals included in the decision-making process.

### Mitigations

- **Rollback:** In a rollback scenario, the updated systems are reverted to the last known good configuration state. It is important for the entire workload team to have an agreement about what last known good means: This is the last state of the workload that was healthy before the deployment began, not necessarily just the prior application version. Be aware that rolling back can be complex, especially as it relates to data changes. Schema changes can make rolling back risky and require considerable planning to execute safely. As a general recommendation, schema updates should be additive rather than replacement changes – meaning records should not be replaced with new records; older records should be deprecated and coexist with new records until it is safe to remove deprecated records.

- **Fall-back:** In a fall-back scenario, the updated systems are removed from the production traffic routing and all traffic is directed to the non-updated stack. This is a low-risk strategy that allows you to address the issues in your code without introducing further disruptions. With canary deployments, falling back might not be straightforward or even possible depending on your infrastructure and app design. Ensure that any scaling that might need to happen to handle the load on non-updated systems is performed before falling back.

- **Bypass the offending function:** If you can bypass the issue using feature flags or another type of runtime configuration property, you might decide that continuing with the rollout is the right strategy for a given issue. You should clearly understand and be able to communicate to stakeholders what the tradeoff of bypassing the function will be, and stakeholders should approve the go-forward plan. Stakeholders will need to decide how long operating in a degraded state is tolerable and weigh that determination against your estimate on time needed to fix the offending code and deploy it.

- **Emergency deployment (hotfix):** If the issue can be addressed mid-rollout, a hotfix might be the most practical mitigation strategy. Remember that like any other code, hotfixes need to go through your safe deployment practices, but the timeline will be accelerated considerably. Code promotion through your environments needs to happen along with all quality gates but bake times might need to be dramatically shorter and tests might need to be modified to accelerate them. Ensure that full tests can be performed on the updated code as soon as possible following the deployment. Having quality assurance testing automated to a high degree will make testing in these scenarios more efficient.

### Communications

Having clearly defined communication responsibilities for the workload team to engage with support teams, emergency response team personnel, like the emergency response manager, and stakeholders will help minimize chaos during the incident. Standardize the status update cadence that the workload team must follow and ensure that stakeholders are aware of this standard, so they know when to expect updates. Likewise, if the workload team needs to communicate directly with end-users, define the appropriate details that can be shared and any other requirements that they must follow in these cases.

### Postmortems

Postmortems should follow all failed deployments, without exception. Every failed deployment is an opportunity for learning. You might be able to identify weaknesses in your deployment and development processes, or you might identify misconfigurations in your infrastructure, among many other possibilities. Postmortems should always be blameless so that individuals involved in the incident feel safe while sharing their perspectives on what can be improved. Postmortem leaders should follow up with plans for implementing the improvements that have been identified and add these plans to the workload backlog.

### Considerations and general recommendations

Ensure that deployments are thoroughly tested when deploying to lower development environments to catch bugs and misconfigurations before they get to production.

Ensure that your deployment pipeline can accept distinct versions as parameters so that you can deploy last known good configurations easily.

Ensure that you maintain consistency with the management and data planes as you roll back or roll forward. Keys, secrets, database state and configuration specific to resources and policies <!-- (link to perf re: scaling issues when rolling back) -->all need to be in scope and accounted for. For example, pay attention to how your infrastructure scaling was designed in your last known good deployment and determine if you need to adjust those configurations when you redeploy your code.

Prefer small frequent changes over infrequent large changes so that the delta between new and last known good is small.

Perform [failure mode analysis (FMA)](../reliability/failure-mode-analysis.md) on your CI/CD pipelines to help identify issues that can complicate mitigations. Like your workload as a whole, you can analyze your pipelines to identify areas that might be problematic when attempting a given mitigation type. 

Use automated rollback functionality judiciously. Some CI/CD tools like Azure DevOps have automatic rollback functionality built-in and you should consider using it if it provides tangible value to you. If you decide it's valuable, only adopt it if you have tested thoroughly and regularly to ensure that your pipeline is mature enough to introduce additional issues if an automatic rollback is triggered. You need to trust that the automation is going to only deploy the necessary changes and that it is only triggered when necessary. Design your triggers carefully to minimize these risks. 

Use platform provided capabilities to help rollbacks. For example, backups and point-in-time restores can help with storage and data rollbacks, or if you use VMs for hosting your application, restoring to a checkpoint immediately before the incident can be a helpful strategy.

Test your entire deployment failure mitigation strategy frequently. Like emergency response plans and disaster recovery plans, your deployment failure plan will only be successful if your team is trained on it and practices it regularly. Chaos engineering and [fault injection testing](../reliability/testing-strategy.md) can be effective techniques for testing your deployment mitigation strategy.

## Azure facilitation

- [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) provides build and release services to support continuous integration and delivery of your applications.

- [Azure Test Plans](/azure/devops/test/overview) is an easy-to-use, browser-based test management solution that provides all the capabilities required for planned manual testing, user acceptance testing, exploratory testing, and gathering feedback from stakeholders.

- [Azure Monitor](/azure/azure-monitor/overview)  is a comprehensive monitoring solution for collecting, analyzing, and responding to monitoring data from your cloud and on-premises environments. It includes a robust alerting platform that can be configured for [automatic notifications and other actions](/azure/azure-monitor/alerts/action-groups), like autoscaling and other self-healing mechanisms. 

- [Application Insights](/azure/azure-monitor/app/app-insights-overview) is an extension of Azure Monitor and provides application performance monitoring (APM) features.

- [Azure Logic Apps](/azure/logic-apps/manage-logic-apps-with-azure-portal) creates a new version of the application whenever an update is made to it. Azure maintains a history of versions and can revert or promote any previous version. 

- Many Azure database services provide point-in-time restore functionality that can help you when you need to roll back. 

  - [Azure SQL Database](/azure/azure-sql/database/recovery-using-backups) 

  - [Azure SQL Managed Instance](/azure/azure-sql/managed-instance/point-in-time-restore) 

  - [Cosmos DB](/azure/cosmos-db/continuous-backup-restore-introduction) 

  - [Azure Database for MySQL](/azure/mysql/flexible-server/concepts-backup-restore) 

  - [Azure Database for PostgreSQL](/azure/postgresql/flexible-server/concepts-backup-restore) 

- Azure Chaos Studio Preview is a managed service that uses chaos engineering to help you measure, understand, and improve your cloud application and service resilience.

## Tradeoffs

The support team needs to be able to perform their normal duties along with supporting emergencies, so additional headcount might be needed to ensure that the support team is properly staffed to carry out all of the required duties.

There are potential tradeoffs with mitigation strategies to consider:

- Being able to fall back typically means that you'll need sufficient infrastructure capacity to handle two versions of the workload configuration at the same time and your workload teams need to be able to support two versions in production at the same time.

- Being able to roll back effectively might involve refactoring elements of your workload, like decoupling functions or changing your data model for example.

## Related links

<!-- OpEx safe deployment guide -->

[Progressive experimentation with feature flags - Azure DevOps | Microsoft Learn](/devops/operate/progressive-experimentation-feature-flags)

## Next steps

We recommend that you review the Operational Excellence checklist to explore other concepts. 

> [!div class="nextstepaction"] 
> [Operational Excellence checklist](checklist.md) 