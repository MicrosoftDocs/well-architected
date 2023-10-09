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

This guide describes the best practices for designing a standardized strategy to effectively handle deployment failures. Like other workload issues, deployment failures are an inevitable part of a workload lifecycle. With this mindset, you can be proactive by having a well-designed, intentional strategy for dealing with deployment failures. Such a strategy enables your workload team to efficiently mitigate failures with as little impact as possible on your end users. The absence of such a plan can lead to chaotic and potentially detrimental responses to issues, which can have serious impacts on team and organizational cohesion, customer trust, and finances.

## Key design strategies

Occasionally, despite the maturity of your development practices, deployment issues occur. Using [safe deployment practices](safe-deployments.md) and operating a robust [workload supply chain](workload-supply-chain.md) help you minimize the frequency of failed deployments. But you also need to design a standardized strategy to handle failed deployments when they happen. When you use a progressive exposure deployment model as your standard practice:

- You have the right foundation for minimizing the effects on your customers or internal users when deployments fail.
- You can mitigate issues efficiently.

A deployment failure mitigation strategy is composed of five broad phases:

- Detection: To respond to a failed deployment, you must first detect the failure. Detection can take several forms, like failed smoke tests, user reported issues, or alerts that your monitoring platform generates.

- Decision: You must decide what the best mitigation strategy is for the specific failure type.

- Mitigation: You perform the identified mitigation action. The mitigation can take the form of a fallback, rollback, roll forward, or the use of a runtime configuration to bypass the offending function.

- Communications: Stakeholders and affected end users must be made aware of the status as you detect and work through the issue as required by your [emergency response plan](emergency-response.md).

- Postmortems: Blameless postmortems provide opportunities for the workload team to identify areas for improvement and create plans to apply learnings.

The following sections provide detailed recommendations for these phases. These sections assume that you detect an issue after you deploy your changes to one or more groups of users or systems but before you update all groups in your rollout plan.

### Detection

To quickly identify issues with deployments, you need robust testing and [observability practices](observability.md) as they relate to deployments. To help you quickly catch anomalies, you can complement your workload monitoring and alerting by taking the following steps:

- Use an application performance management tool.
- Enable logging through instrumentation (link to OpEx instrumentation guide).

Smoke testing and other quality testing should happen at each phase of your rollout. Successful tests in one deployment group shouldn't influence decisions to test in subsequent groups.

Having telemetry in place that can correlate user issues with a deployment phase helps you quickly identify which rollout group a particular user is associated with. This approach is especially important with progressive exposure deployments, because you might have multiple configurations running across your user base at any given point in the deployment.

You should be ready to respond to user reported issues immediately. Whenever practical, deploy your rollout phases during working hours to ensure that you have a full support team available. Ensure support staff is trained on how to escalate deployment issues for proper routing. Escalations should align with your workload [emergency response plan](emergency-response.md).

### Decision

Deciding on an appropriate mitigation strategy for a given deployment issue involves considering many factors, including:

- The type of progressive exposure model that you use. For example, you might your a blue-green model or a canary model.

  If you use a blue-green model, falling back is more practical than rolling back, because you can easily shift traffic back to the stack that runs the non-updated configuration. You can then fix the issue in the problematic environment and try your deployment again at the appropriate time.

- The methods that you have at your disposal for bypassing the issue. Examples include the use of feature flags, environmental variables, or any other type of runtime configuration property that you can toggle on and off.

  If you can easily bypass the issue by turning off a feature flag or toggling a setting, you might be able to proceed with the rollout or avoid falling back or rolling back while you fix the offending code.

- The level of effort that's required to implement a fix in the code.

  If the level of effort to fix the code is low, rolling forward by implementing a hot fix might be the right choice for certain scenarios.

- What the level of criticality is for the affected workload.

  Business-critical workloads should always be deployed in a side-by-side manner, like in a blue-green model, to achieve zero-downtime deployments. As a result, falling back is the preferable mitigation strategy for these types of workloads.

- Whether the workload uses mutable or immutable infrastructure.

  If your workload is designed to use mutable infrastructure, rolling forward can make sense, because it involves updating infrastructure in place. Conversely, rolling back or falling back can make sense when you use immutable infrastructure.

No matter which choice you make, you should include appropriate approvals in your decision-making process and codify them in your decision tree.

### Mitigations

- **Rollback**: In a rollback scenario, you revert updated systems to the last known good configuration state. It's important for the entire workload team to agree about the meaning of *last known good*. This expression refers to the last state of the workload that was healthy before the deployment began, which isn't necessarily the prior application version. Be aware that rolling back can be complex, especially as it relates to data changes. Schema changes can make rolling back risky. Implementing them safely can require considerable planning. As a general recommendation, schema updates should be additive. They shouldn't be replacement changes—records shouldn't be replaced with new records. Instead, older records should be deprecated and should coexist with new records until it's safe to remove the deprecated records.

- **Fallback**: In a fallback scenario, the updated systems are removed from the production traffic routing, and all traffic is directed to the non-updated stack. This low-risk strategy provides a way for you to address the issues in your code without introducing further disruptions. With canary deployments, falling back might not be straightforward or even possible, depending on your infrastructure and app design. If you need to perform scaling to handle load on non-updated systems, perform that scaling before you fall back.

- **Bypass the offending function**: If you can bypass the issue by using feature flags or another type of runtime configuration property, you might decide that continuing with the rollout is the right strategy for a given issue. You should clearly understand the tradeoff of bypassing the function and be able to communicate that tradeoff to stakeholders. Stakeholders should approve the go-forward plan. Stakeholders need to determine the length of time that's tolerable for operating in a degraded state. They also need to weigh that determination against your estimate of the time that's needed to fix the offending code and deploy it.

- **Emergency deployment (hot fix)**: If you can address the issue mid-rollout, a hot fix might be the most practical mitigation strategy. Like any other code, hot fixes need to go through your safe deployment practices, but with a hot fix, the timeline is considerably accelerated. You need to use a code promotion strategy throughout your environments. You also need to do something with all quality gates. But you might need to dramatically shorten bake times, and you might need to modify tests to accelerate them. Ensure that you can run full tests on the updated code as soon as possible following the deployment. Automating quality assurance testing to a high degree helps make testing efficient in these scenarios.

### Communications

Having clearly defined communication responsibilities for the workload team to engage with support teams, emergency response team personnel, like the emergency response manager, and stakeholders will help minimize chaos during the incident. Standardize the status update cadence that the workload team must follow and ensure that stakeholders are aware of this standard, so they know when to expect updates. Likewise, if the workload team needs to communicate directly with end-users, define the appropriate details that can be shared and any other requirements that they must follow in these cases.

### Post-mortems

Post-mortems should follow all failed deployments, without exception. Every failed deployment is an opportunity for learning. You may be able to identify weaknesses in your deployment and development processes, or you may identify misconfigurations in your infrastructure, among many other possibilities. Post-mortems should always be blameless so that individuals involved in the incident feel safe while sharing their perspectives on what can be improved. Post-mortem leaders should follow up with plans for implementing the improvements that have been identified and add these plans to the workload backlog.

### Considerations and general recommendations

Ensure that deployments are thoroughly tested when deploying to lower development environments to catch bugs and misconfigurations before they get to production.

Ensure that your deployment pipeline can accept distinct versions as parameters so that you can deploy last known good configurations easily.

Ensure that you maintain consistency with the management and data planes as you roll back or roll forward. Keys, secrets, database state and configuration specific to resources and policies  (link to perf re: scaling issues when rolling back) all need to be in scope and accounted for. For example, pay attention to how your infrastructure scaling was designed in your last known good deployment and determine if you need to adjust those configurations when you redeploy your code.

Prefer small frequent changes over infrequent large changes so that the delta between new and last known good is small.

Perform [failure mode analysis (FMA)](../reliability/failure-mode-analysis.md) on your CI/CD pipelines to help identify issues that can complicate mitigations. Like your workload as a whole, you can analyze your pipelines to identify areas that may be problematic when attempting a given mitigation type. 

Use automated rollback functionality judiciously. Some CI/CD tools like Azure DevOps have automatic rollback functionality built-in and you should consider using it if it provides tangible value to you. If you decide it is valuable, only adopt it if you have tested thoroughly and regularly to ensure that your pipeline is mature enough to introduce additional issues if an automatic rollback is triggered. You need to trust that the automation is going to only deploy the necessary changes and that it is only triggered when necessary. Design your triggers carefully to minimize these risks. 

Use platform provided capabilities to help rollbacks. For example, backups and point-in-time restores can help with storage and data rollbacks, or if you use VMs for hosting your application, restoring to a checkpoint immediately before the incident can be a helpful strategy.

Test your entire deployment failure mitigation strategy frequently. Like emergency response plans and disaster recovery plans, your deployment failure plan will only be successful if your team is trained on it and practices it regularly. Chaos engineering and [fault injection testing](../reliability/testing-strategy.md) can be effective techniques for testing your deployment mitigation strategy.

## Azure facilitation

- [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) provides build and release services to support continuous integration and delivery of your applications.

- [Azure Test Plans](/azure/devops/test/overview) is an easy-to-use, browser-based test management solution that provides all the capabilities required for planned manual testing, user acceptance testing, exploratory testing, and gathering feedback from stakeholders.

- [Azure Monitor](/azure/azure-monitor/overview)  is a comprehensive monitoring solution for collecting, analyzing, and responding to monitoring data from your cloud and on-premises environments. It includes a robust alerting platform that can be configured for [automatic notifications and other actions](/azure/azure-monitor/alerts/action-groups), like auto-scaling and other self-healing mechanisms. 

- [Application Insights](/azure/azure-monitor/app/app-insights-overview) is an extension of Azure Monitor and provides application performance monitoring (APM) features.

- [Azure Logic Apps](/azure/logic-apps/manage-logic-apps-with-azure-portal) create a new version of the application whenever an update is made to it. Azure maintains a history of versions and can revert or promote any previous version. 

- Many Azure database services provide point-in-time restore functionality that can help you when you need to roll back. 

  - [Azure SQL Database](/azure/azure-sql/database/recovery-using-backups) 

  - [Azure SQL Managed Instance](/azure/azure-sql/managed-instance/point-in-time-restore) 

  - [Cosmos DB](/azure/cosmos-db/continuous-backup-restore-introduction) 

  - [Azure Database for MySQL](/azure/mysql/flexible-server/concepts-backup-restore) 

  - [Azure Database for PostgreSQL](/azure/postgresql/flexible-server/concepts-backup-restore) 

- Azure Chaos Studio Preview is a managed service that uses chaos engineering to help you measure, understand, and improve your cloud application and service resilience.

## Tradeoff

The support team needs to be able to perform their normal duties along with supporting emergencies, so additional headcount may be needed to ensure that the support team is properly staffed to carry out all of the required duties.

There are potential tradeoffs with mitigation strategies to consider:

- Being able to fall back typically means that you will need sufficient infrastructure capacity to handle two versions of the workload configuration at the same time and your workload teams need to be able to support two versions in production at the same time.

- Being able to rollback effectively may involve refactoring elements of your workload, like decoupling functions or changing your data model for example.

## Related links

OpEx safe deployment guide 

[Progressive experimentation with feature flags - Azure DevOps | Microsoft Learn](/devops/operate/progressive-experimentation-feature-flags)

## Next steps

We recommend that you review the Operational Excellence checklist to explore other concepts. 

> [!div class="nextstepaction"] 
> [Operational Excellence checklist](checklist.md) 