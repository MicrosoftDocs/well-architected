---
title: Recommendations for using safe deployment practices
description: Learn about recommendations for safe deployment practices (SDP). Define how to safely make any change to your workload through deployments.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for using safe deployment practices

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:**

|[OE:11](checklist.md)| Clearly define your workload's safe deployment practices. Emphasize the ideals of small, incremental, quality-gated release methods. Use modern deployment patterns and progressive exposure techniques to control risk. Account for routine deployments and emergency, or hotfix, deployments.|
|---|---|

This guide describes the recommendations for using safe deployment practices (SDP). SDP are processes and procedures codified by your organization that define how to safely make and deploy changes to your workload. Implementing SDP also requires you to think about deployments through the lens of managing risk. By putting the right practices into place, you can minimize the risk of introducing human error in your deployments and limit the effects of problematic deployments on your users.

## Key design strategies

Four important guidelines for implementing safe deployment practices are:

- All changes to the production workload are inherently risky and must be made with a focus on safety and consistency.

- Minimize the potential blast radius of deployment-caused issues by adopting progressive exposure deployments.

- Deployments must pass health checks before each phase of progressive exposure can begin.

- When issues are detected, the deployment is halted and recovery initiates immediately.

The following sections provide detailed recommendations on each of these points.

### Safety and consistency

Whether you deploy an update to your application code, infrastructure as code (IaC), feature flags, or a configuration update, you're introducing risk to the workload. There are no *low-risk* deployments to production. Every deployment must follow a standard pattern and should be automated to enforce consistency and minimize risks of human error. Your workload team and organization should treat every deployment as a possible risk and subject every deployment to the same level of risk management. It's critical that your workload supply chain and deployment pipelines are reliable, secure, and have clearly defined deployment standards. But this shouldn't discourage you from deploying changes to your workload, failing to deploy regularly introduces risks like security vulnerabilities that must be addressed through deployments. For more information, see [Recommendations for designing a workload development supply chain](workload-supply-chain.md)

Although it might seem counterintuitive at first, a standard of frequent small deployments is preferable to infrequent large deployments. Small changes are easier to resolve when issues arise and frequent deployments help your team build confidence in the deployment process. It's also important that you learn from production. Learning from production means that every time there's an anomaly during a deployment, you can learn something about your processes or about your workload. You might find weaknesses in your infrastructure design or in the way that you have designed your rollout stages, among many other possible areas for improvement. When issues arise during deployments, ensure that _blameless_ postmortems are part of your SDP process to capture learnings about the incident.

### General SDP recommendations

- Implement versioning across your build artifacts to ensure that you can roll back and forward when necessary.

- Rather than a Gitflow or environment-based branching structure with long-lived development, QA or release branches, use a release flow or trunk-based branching structure, which enforces tightly synced collaboration across the development team.

- Use CI practices to regularly integrate code changes into repositories. This helps identify integration conflicts and reduces the likelihood of large, risky merges. For more information, see the [Continuous integration guide](/azure/well-architected/devops/release-engineering-ci).

- Automate as much of your SDP as possible. For detailed guidance on automating IaC and application continuous integration and continuous delivery (CI/CD) processes, see the [Recommendations for implementing automation](automate-tasks.md).

- Use feature flags to selectively enable or disable new features or changes in production. This allows you to control the exposure of new code and quickly roll back if issues arise.

- Deploy changes to staging environments that mirror your production environment. This allows you to test changes in a controlled setting before deploying to the live environment.

- Establish a checklist of pre-deployment checks, including code review, security scans, and compliance checks, to ensure that changes are safe to deploy.

- Implement circuit breakers to automatically halt traffic to a service experiencing issues, which prevents further degradation of the system.

### Exposure deployment

When deployment issues happen, the goal is to catch them as early as possible to minimize the effect on end-users. Implement a gradual rollout deployment model, also known as a progressive exposure model, to accomplish this goal. Canary deployments are a common example of progressive exposure. Using this model, you identify a small group of internal or external users that receive the new features first. After that group runs the new version without issue, you deploy the feature to successively larger groups until the entire user population is running the new version. Using this model, you typically use feature flags to enable the new version for the target users.

Another common deployment model is a blue-green approach. In this model, two identical sets, or pools, of workload infrastructure are deployed, both able to handle a full production load. The first, blue, pool runs the current version of the deployment and all users land on this pool. The second, green, pool is updated with the new version and internally tested. After internal testing, a subset of the production traffic is routed to the from the blue pool to the green pool. Like canary deployments, the rollout is progressive as you shift more of the traffic over to the green pool in successively larger rollout waves. After you finish the rollout, the update pool becomes the blue pool and the new green pool is ready for the next deployment. The two pools are logically separated from each other to protect from malfunctions. If you deployed your workload in the [Deployment Stamps](/azure/architecture/patterns/deployment-stamp) design pattern, a variation of the blue-green model can be run by deploying on one stamp at a time.

In either model, the time between each phase of the rollout is critical for monitoring the workload closely to watch for changes in health metrics. You should provide ample *bake time*, time between rollout groups, to ensure that users from different regions or performing different tasks have time to use the workload in their normal capacity. Generally speaking, bake times should be measured in hours and days rather than minutes. Bake times for each rollout group will likely be longer so that you can account for different time zones and usage patterns changing over the course of the day.

### Health models

While building your observability platform and reliability strategies, you should develop a robust health model for your workload that gives you in-depth visibility into the workload components and the overall health of the workload. During a rollout, if you receive an alert about a health change that's correlated with one or more of the groups that has moved onto the new version, the rollout should immediately halt and an investigation into the cause of the alert must be performed to help determine the next course of action. Conversely, if all health indicators stay green throughout the bake time and no issues are reported by end-users, you can forward with the rollout. Be sure to include some metrics about usage at each rollout stage to ensure that a lack of user reported issues and negative health signals isn’t a false positive hiding an issue. For more information, see [Building a health model](../reliability/metrics.md#building-a-health-model).

### Issue detection

When your deployment causes an issue in one of the rollout groups, the deployment rollout must halt immediately. Whether you're alerted by your observability platform or by an end-user, the decision about how to proceed must happen as quickly as possible, so an investigation into the cause of the issue and the severity of the effects must be performed as soon as the alert is received. Recovery from the issue can include:

- **Rollback**: this is the process of undoing the changes made in the deployment and reverting back to the last known good configuration.

- **Roll forward**: this is the process of addressing the issue in the midst of the rollout by applying a hotfix or otherwise minimizing the issue.

- Deploy new infrastructure using the last known good configuration

Rolling back changes can be complex, especially databases and schema changes and other stateful components. Ensure that your SDP guidelines provide clear instructions on how to deal with data changes according to the data estate design for your workload. Likewise, rolling forward must be handled very carefully to ensure that SDP isn't neglected, and the hotfix or other mitigation efforts are performed safely.

### Emergency SDP protocols

When you need to deploy a hotfix to respond to a rollout that has encountered issues, or in cases of emergencies like security breaches or vulnerability exposures, you should have prescriptive protocols documented that define how your SDP can be changed in limited ways to respond to these cases. For example:

- Promotion and approval stages can be accelerated, but should still happen to ensure quality.

- Smoke testing and integration testing can be accelerated.

- Bake times can be reduced.

In some cases, the emergency might result in limited quality and testing gates, but they should still be run in full as quickly as possible as an out of band exercise.

Ensure that you have defined who can approve accelerating your SDP and the criteria that must be met to be approved.

Your emergency SDP protocols should be aligned with your [emergency response plan](./emergency-response.md) to ensure that all emergencies are handled according to the same protocols.

## Azure facilitation

- [Azure Pipelines](/azure/devops/pipelines/process/stages) and [GitHub Actions](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment) support multi-stage deployments using approval gates, which can help you design your progressive exposure rollout for deployments.

- Use [Azure App Service staging slots](/azure/app-service/deploy-staging-slots) to easily swap between versions of code. These slots are helpful for testing in staging environments or can be used for blue-green deployments.

- Store and manage your web app feature flags in [Azure App Configuration](/azure/azure-app-configuration/manage-feature-flags). Using this function gives you a unified management plane to create, change and deploy features.

- Deploy workload application in your Virtual Machines using [VM Applications](/azure/virtual-machines/vm-applications-how-to).

- [Azure Load Balancers](/azure/architecture/guide/technology-choices/load-balancing-overview) can be used to implement some of the described deployment strategies and some options provide with native resources to expose the health of your workload applications.

- [The Application Health Extension](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension) is deployed inside a Virtual Machine Scale Set instance and reports on application health from inside the scale set instance. The extension probes on a local application endpoint and will update the health status based on TCP/HTTP(S) responses received from the application.

- [Azure Logic Apps](/azure/logic-apps/manage-logic-apps-with-azure-portal) create a new version of the application whenever an update is made to it. Azure maintains a history of versions and can revert or promote any previous version.

- Many Azure database services provide point-in-time restore functionality that can help you when you need to roll back.

  - [Azure SQL Database](/azure/azure-sql/database/recovery-using-backups)
  - [Azure SQL Managed Instance](/azure/azure-sql/managed-instance/point-in-time-restore)
  - [Cosmos DB](/azure/cosmos-db/continuous-backup-restore-introduction)
  - [Azure Database for MySQL](/azure/mysql/flexible-server/concepts-backup-restore)
  - [Azure Database for PostgreSQL](/azure/postgresql/flexible-server/concepts-backup-restore)

## Tradeoffs

Building and maintaining safe deployment practices is _hard_ and your success in fully implementing robust standards depends on the maturity of your practices across many areas of software development.  Your team’s embrace of recommended practices like a robust use of automation, strict use of IaC-only for infrastructure changes, and consistency in branching strategies, use of feature flags, among many other practices will help you adopt many of the recommendations given in this guide. However, use this guide to optimize your practices to the extent that is practical for where your team is at in terms of the maturity of your development practices and use it to inform your plans for improvement as your practices evolve.

There are tradeoffs for each deployment model discussed in this guide.

For canary deployments, there will be times that you have to support two versions of the application on the same infrastructure, which increases the management burden on the workload and support teams.

For blue-green deployments, there will be times when you have two sets of production infrastructure running at the same time, which is costly from a financial standpoint and from a management standpoint.

## Example

See the [Blue-green deployment of AKS clusters](/azure/architecture/guide/aks/blue-green-deployment-for-aks) architecture guide for an example of using this deployment model.

## Related links

- [Blue-green deployment of AKS clusters](/azure/architecture/guide/aks/blue-green-deployment-for-aks)
- [Performance considerations for your deployment infrastructure](/azure/well-architected/devops/release-engineering-performance)
- [Release engineering: Application development](/azure/well-architected/devops/release-engineering-app-dev)
- [Release engineering: Continuous integration](/azure/well-architected/devops/release-engineering-ci)
- [Release engineering: Deployment](/azure/well-architected/devops/release-engineering-cd)
- [Release engineering: Rollback](/azure/well-architected/devops/release-engineering-rollback)
- [Testing your application and Azure environment](/azure/well-architected/devops/release-engineering-testing)

## Community links

- [Advancing safe deployment practices](https://azure.microsoft.com/blog/advancing-safe-deployment-practices/)
- [GitHub Actions](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment)

## Operational Excellence checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Operational Excellence checklist](checklist.md)
