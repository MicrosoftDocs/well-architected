---
title: Recommendations for designing a deployment failure mitigation strategy
description: Design a strategy for handling deployment failures. Enable your workload team to efficiently mitigate failures while minimizing the impact on end users.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for designing a deployment failure mitigation strategy

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:**

|[OE:12](checklist.md)| Implement a deployment failure mitigation strategy that addresses unexpected mid-rollout issues with rapid recovery. Combine multiple approaches, such as rollback, feature disablement, or using your deployment pattern's native capabilities. |
|---|---|

This guide describes the recommendations for designing a standardized strategy to effectively handle deployment failures. Like other workload issues, deployment failures are an inevitable part of a workload lifecycle. With this mindset, you can be proactive by having a well-designed, intentional strategy for dealing with deployment failures. Such a strategy enables your workload team to efficiently mitigate failures with as little impact as possible on your end users.

The absence of such a plan can lead to chaotic and potentially detrimental responses to issues, which can significantly affect team and organizational cohesion, customer trust, and finances.

## Key design strategies

Occasionally, despite the maturity of your development practices, deployment issues occur. Using [safe deployment practices](safe-deployments.md) and operating a robust [workload supply chain](workload-supply-chain.md) can help you minimize the frequency of failed deployments. But you also need to design a standardized strategy to handle failed deployments when they happen.

When you use a progressive exposure deployment model as your standard practice:

- You have the right foundation for minimizing the effects on your customers or internal users when deployments fail.
- You can mitigate issues efficiently.

A deployment failure mitigation strategy is composed of five broad phases:

1. Detection: To respond to a failed deployment, you must first detect the failure. Detection can take several forms, like failed smoke tests, user reported issues, or alerts that your monitoring platform generates.

1. Decision: You must decide what the best mitigation strategy is for the specific failure type.

1. Mitigation: You perform the identified mitigation action. The mitigation can take the form of a fallback, rollback, roll forward, or the use of a runtime configuration to bypass the offending function.

1. Communication: Stakeholders and affected end users must be made aware of the status as you detect and work through the issue as required by your [emergency response plan](emergency-response.md).

1. Postmortem: Blameless postmortems provide opportunities for the workload team to identify areas for improvement and create plans to apply learnings.

The following sections provide detailed recommendations for these phases. These sections assume that you detect an issue after you deploy your changes to one or more groups of users or systems but before you update all groups in your rollout plan.

### Detection

To quickly identify issues with deployments, you need robust testing and [observability practices](observability.md) as they relate to deployments. To help detect anomalies quickly, you can complement your workload monitoring and alerting by taking the following steps:

- Use an application performance management tool.
- Enable logging through [instrumentation](instrument-application.md). 

Smoke testing and other quality testing should happen at each phase of your rollout. Successful tests in one deployment group shouldn't influence decisions to test in subsequent groups.

You can implement telemetry that correlates user issues with a deployment phase. Then you can quickly identify which rollout group a particular user is associated with. This approach is especially important for progressive exposure deployments, because you might have multiple configurations running across your user base at any given point in the deployment.

You should be ready to respond to user-reported issues immediately. Whenever practical, deploy your rollout phase during working hours, when you have a full support team available. Ensure support staff is trained on how to escalate deployment issues for proper routing. Escalations should align with your workload [emergency response plan](emergency-response.md).

### Decision

Deciding on an appropriate mitigation strategy for a given deployment issue involves considering many factors, including:

- The type of progressive exposure model that you use. For example, you might use a blue-green model or a canary model.

  If you use a blue-green model, falling back is more practical than rolling back. You can easily shift traffic back to the stack that runs the configuration that's not updated. You can then fix the issue in the problematic environment and try your deployment again at an appropriate time.

- The methods that you have at your disposal for bypassing the issue. Examples include the use of feature flags, environmental variables, or any other type of runtime configuration property that you can toggle on and off.

  Sometimes you can easily bypass an issue by turning off a feature flag or toggling a setting. In this case, you might be able to:

  - Proceed with the rollout.
  - Avoid falling back.
  - Roll back while you fix the offending code.

- The level of effort that's required to implement a fix in the code.

  If the level of effort to fix the code is low, rolling forward by implementing a hot fix is the right choice for certain scenarios.

- The level of criticality for the affected workload.

  Business-critical workloads should always be deployed in a side-by-side manner, like in a blue-green model, to achieve zero-downtime deployments. As a result, falling back is the preferable mitigation strategy for these types of workloads.

- The type of infrastructure that the workload uses—mutable or immutable.

  If your workload is designed to use mutable infrastructure, rolling forward can make sense, because it involves updating infrastructure in place. Conversely, rolling back or falling back can make sense when you use immutable infrastructure.

No matter which choices you make, you should include appropriate approvals in your decision-making process and codify them in your decision tree.

### Mitigation

- **Rollback**: In a rollback scenario, you revert updated systems to the last-known-good configuration state.

  It's important for the entire workload team to agree about the meaning of *last known good*. This expression refers to the last state of the workload that was healthy before the deployment began, which isn't necessarily the prior application version.

  Rolling back can be complex, especially as it relates to data changes. Schema changes can make rolling back risky. Implementing them safely can require considerable planning. As a general recommendation, schema updates should be additive. They shouldn't be replacement changes—records shouldn't be replaced with new records. Instead, older records should be deprecated and should coexist with new records until it's safe to remove the deprecated records.

- **Fallback**: In a fallback scenario, the updated systems are removed from the production traffic routing. All traffic is directed to the stack that isn't updated. This low-risk strategy provides a way for you to address the issues in your code without introducing further disruptions.

  With canary deployments, falling back might not be straightforward or even possible, depending on your infrastructure and app design. If you need to perform scaling to handle load on systems that aren't updated, perform that scaling before you fall back.

- **Bypass the offending function**: If you can bypass the issue by using feature flags or another type of runtime configuration property, you might decide that continuing with the rollout is the right strategy for a given issue.

  You should clearly understand the tradeoff of bypassing the function, and you should be able to communicate that tradeoff to stakeholders. Stakeholders should approve the go-forward plan. Stakeholders need to determine the length of time that's tolerable for operating in a degraded state. They also need to weigh that determination against your estimate of the time that's needed to fix the offending code and deploy it.

- **Emergency deployment (hot fix)**: If you can address the issue mid-rollout, a hot fix might be the most practical mitigation strategy.

  Like any other code, hot fixes need to go through your safe deployment practices. But with a hot fix, the timeline is considerably accelerated. You need to use a code promotion strategy throughout your environments. You also need to check hot fix code at all quality gates. But you might need to dramatically shorten bake times, and you might need to modify tests to accelerate them. Ensure that you can run full tests on the updated code as soon as possible after deployment. Automating quality assurance testing to a high degree helps make testing efficient in these scenarios.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoffs**:

> - Being able to fall back typically means that you need sufficient infrastructure capacity to handle two versions of your workload configuration at the same time. Your workload teams also need to be able to support two versions in production at the same time.

> - Being able to roll back effectively might involve refactoring elements of your workload. For example, you might need to decouple functions or change your data model.

### Communication

It's important to have clearly defined communication responsibilities to help minimize chaos during incidents. These responsibilities should establish how the workload team engages with support teams, stakeholders, and emergency response team personnel, like the emergency response manager.

Standardize the cadence that the workload team follows for providing status updates. Ensure that stakeholders are aware of this standard so that they know when to expect updates.

If the workload team needs to communicate directly with end users, clarify the type of information and level of detail that are appropriate for sharing with users. Also communicate to the workload team any other requirements that apply to these cases.

### Postmortem

Postmortems should follow all failed deployments, without exception. Every failed deployment is an opportunity for learning. Postmortems can help you identify weaknesses in your deployment and development processes. You also might identify misconfigurations in your infrastructure, among many other possibilities.

Postmortems should always be blameless so that individuals who are involved in the incident feel safe when they share their perspectives on what can be improved. Postmortem leaders should follow up with plans for implementing the improvements that have been identified and adding these plans to the workload backlog.

### Considerations and general recommendations

Ensure that your deployment pipeline can accept distinct versions as parameters so that you can easily deploy last-known-good configurations.

Maintain consistency with the management and data planes as you roll back or roll forward. Keys, secrets, database state data, and configurations that are specific to resources and policies all need to be in scope and accounted for. For example, pay attention to the design of your infrastructure scaling in your last-known-good deployment. Determine whether you need to adjust that configuration when you redeploy your code.

Prefer small, frequent changes over infrequent, large changes so that the delta between your new and last-known-good deployments is small.

Perform a [failure mode analysis (FMA)](../reliability/failure-mode-analysis.md) on your continuous integration and continuous delivery (CI/CD) pipelines to help identify issues that can complicate mitigations. Like your workload as a whole, your pipelines can be analyzed to identify areas that might be problematic when you attempt a given mitigation type.

Use automated rollback functionality judiciously:

- Some CI/CD tools like Azure DevOps have automatic rollback functionality that's built in. Consider using this functionality if it provides tangible value to you.
- You should adopt automatic rollback functionality only after you test your pipeline thoroughly and regularly. Ensure that your pipeline is mature enough to introduce extra issues if an automatic rollback is triggered.
- You need to trust that the automation deploys only necessary changes and that it's triggered only when necessary. Design your triggers carefully to meet these requirements.

Use platform-provided capabilities during rollbacks. For example, backups and point-in-time restores can help with storage and data rollbacks. Or if you use virtual machines (VMs) to host your application, it can be helpful to restore your environment to a checkpoint that's immediately before an incident.

Test your entire deployment failure mitigation strategy frequently. Like emergency response plans and disaster recovery plans, your deployment failure plan is only successful if your team is trained on it and practices it regularly. Chaos engineering and [fault injection testing](../reliability/testing-strategy.md) can be effective techniques for testing your deployment mitigation strategy.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Support team members need to be able to perform their normal duties and also support emergencies. You might need to increase head count to help ensure that the support team is properly staffed and able to carry out all required duties.
Test deployments thoroughly when you deploy to lower development environments. This practice helps you detect bugs and misconfigurations before they get to production.

## Azure facilitation

- [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) provides build and release services to support CI/CD of your applications.

- [Azure Test Plans](/azure/devops/test/overview) is a browser-based test management solution that's easy to use. This solution offers capabilities that are required for planned manual testing, user acceptance testing, and exploratory testing. Azure Test Plans also provides a way for you to gather feedback from stakeholders.

- [Azure Monitor](/azure/azure-monitor/overview) is a comprehensive monitoring solution for collecting, analyzing, and responding to monitoring data from your cloud and on-premises environments. Monitor includes a robust alerting platform. You can configure that system for [automatic notifications and other actions](/azure/azure-monitor/alerts/action-groups), like autoscaling and other self-healing mechanisms.

- [Application Insights](/azure/azure-monitor/app/app-insights-overview) is an extension of Monitor that provides application performance monitoring (APM) features.

- [Azure Logic Apps](/azure/logic-apps/manage-logic-apps-with-azure-portal) is a cloud-based platform for running automated workflows that integrate apps, data, services, and systems. You can use Logic Apps to create a new version of your application whenever an update is made to it. Azure maintains a history of the versions and can revert or promote any previous version.

- Many Azure database services provide point-in-time restore functionality that can help you when you need to roll back:

  - [Azure SQL Database](/azure/azure-sql/database/recovery-using-backups)

  - [Azure SQL Managed Instance](/azure/azure-sql/managed-instance/point-in-time-restore)

  - [Azure Cosmos DB](/azure/cosmos-db/continuous-backup-restore-introduction)

  - [Azure Database for MySQL](/azure/mysql/flexible-server/concepts-backup-restore)

  - [Azure Database for PostgreSQL](/azure/postgresql/flexible-server/concepts-backup-restore)

- [Azure Chaos Studio Preview](/azure/chaos-studio/chaos-studio-overview) is a managed service that uses chaos engineering to help you measure, understand, and improve your cloud application and service resilience.

## Related links

- [Progressive experimentation with feature flags](/devops/operate/progressive-experimentation-feature-flags)
- [Recommendations for designing and creating an observability framework](observability.md)
- [Recommendations for designing an emergency response strategy](emergency-response.md)
- [Recommendations for designing a reliability testing strategy](../reliability/testing-strategy.md)
- [Recommendations for designing a workload development supply chain](workload-supply-chain.md)
- [Recommendations for performing failure mode analysis](../reliability/failure-mode-analysis.md)
- [Recommendations for safe deployment practices](safe-deployments.md)

## Operational Excellence checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Operational Excellence checklist](checklist.md)