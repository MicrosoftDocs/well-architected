---
title: Recommendations for safe deployment practices
description: Learn about recommendations for safe deployment practices (SDP). Define how to safely make any change to your workload through deployments.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for safe deployment practices

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:**

|[OE:11](checklist.md)| Clearly define your workload's safe deployment practices. Emphasize the ideals of small, incremental, quality-gated release methods. Use modern deployment patterns and progressive exposure techniques to control risk. Account for routine deployments and emergency, or hotfix, deployments.|
|---|---|

This guide describes the recommendations for using safe deployment practices (SDP). Safe deployment processes and procedures define how to safely make and deploy changes to your workload. Implementing SDP requires you to think about deployments through the lens of managing risk. You can minimize the risk of human error in your deployments and limit the effects of problematic deployments on your users by implementing SDP.

## Key design strategies

There are four important guidelines to keep in mind when implementing safe deployment practices:

- **Safety and consistency**: All changes to the production workload are inherently risky and must be made with a focus on safety and consistency.

- **Progressive exposure**: You can minimize the potential blast radius of deployment-caused issues by adopting a progressive exposure deployment model.

- **Health models**: Deployments must pass health checks before each phase of progressive exposure can begin.

- **Issue detection**: When issues are detected, the deployment should be immediately halted and recovery initiated.

The following sections provide detailed recommendations on each of these points.

### Safety and consistency

Whether you're deploying an update to your application code, infrastructure as code (IaC), feature flag, or a configuration update, you're introducing risk to the workload. There are no *low-risk* deployments to production. Every deployment must follow a standard pattern and should be automated to enforce consistency and minimize the risk of human error. It's critical that your workload supply chain and deployment pipelines are reliable, secure, and have clearly defined deployment standards. Treat every deployment as a possible risk and subject every deployment to the same level of risk management. Despite the risks, you should continue to deploy regular changes to your workload. Failing to deploy regular updates introduces other risks, like security vulnerabilities that must be addressed through deployments. For more information, see [Recommendations for designing a workload development supply chain](workload-supply-chain.md).

Frequent small deployments are preferable to infrequent large deployments. Small changes are easier to resolve when issues arise and frequent deployments help your team build confidence in the deployment process. It's also important that you learn from production by reviewing your workload processes when you encounter an anomaly during deployment. You might find weaknesses in the design of your infrastructure or rollout. When issues occur during deployments, ensure that _blameless_ postmortems are part of your SDP process to capture lessons about the incident.

### Progressive exposure deployment

When deployment issues occur, the goal is to catch them as early as possible to minimize the effect on end users. Implement a gradual rollout deployment model, also known as a *progressive exposure model*, to accomplish this goal. Canary deployments are a common example of progressive exposure. In this deployment model, a small group of internal or external users receive the new feature first. After the first group runs the new version without issue, the feature is deployed to successively larger groups until the entire user population is running the new version. Feature flags are typically used to enable the new version for the target users in canary deployments.

Another common deployment model is a blue-green approach. In this model, two identical sets, or pools, of workload infrastructure are deployed. Both pools are able to handle a full production load. The first (blue) pool runs the current version of the deployment where all users land. The second (green) pool is updated with the new feature and internally tested. After internal testing, a subset of the production traffic is routed from the blue pool to the green pool. Like canary deployments, the rollout is progressive as you shift more of the traffic over to the green pool in successively larger rollout waves. After you finish the rollout, the update pool becomes the blue pool and the green pool is ready for the next deployment. The two pools are logically separated from each other to protect from malfunctions. You can deploy a variation of the blue-green model in a workload that uses the [Deployment Stamps](/azure/architecture/patterns/deployment-stamp) design pattern by deploying on one stamp at a time.

In both of these models, the time between each phase of the rollout should be long enough to enable you to monitor the health metrics of the workload. You should provide ample *bake time*, time between rollout groups, to help ensure that users from different regions or users who perform different tasks have time to use the workload in their normal capacity. Bake times should be measured in hours and days rather than minutes. Bake times should also increase for each rollout group so that you can account for different time zones and usage patterns over the course of the day.

### Health models

Develop a robust health model as part of your observability platform and reliability strategies. Your health model should provide in-depth visibility into the components and overall health of the workload. During a rollout, if you receive an alert about a health change relating to an end user, the rollout should immediately halt and an investigation into the cause of the alert should be performed to help determine the next course of action. If there are no issues reported by end users and all health indicators stay green throughout the bake time, the rollout should continue. Be sure to include usage metrics in your health model to help ensure that a lack of user-reported issues and negative health signals aren't hiding an issue. For more information, see [Building a health model](../reliability/metrics.md#building-a-health-model).

### Issue detection

When your deployment causes an issue in one of the rollout groups, the rollout must stop immediately. An investigation into the cause of the issue and the severity of the effects must be performed as soon as the alert is received. Recovery from the issue can include:

- **Rolling back** the deployment by undoing the changes made in the deployment and reverting back to the last known working configuration.

- **Rolling forward** the deployment by addressing the issue in the midst of the rollout. You can address issues mid-rollout by applying a hotfix or otherwise minimizing the issue.

- **Deploying new infrastructure** by using the last known working configuration.

Rolling back changes, especially database, schema, or other stateful component changes, can be complex. Your SDP guidelines should provide clear instructions on how to deal with data changes according to the data estate design for your workload. Similarly, rolling forward must be handled carefully to ensure that SDP isn't neglected and that the hotfix or other minimizing efforts are performed safely.

### General SDP recommendations

- Implement versioning across your build artifacts to help ensure that you can roll back and roll forward when necessary.

- Use a release flow or trunk-based branching structure, which enforces tightly synced collaboration across the development team, instead of a Gitflow or environment-based branching structure.

- Automate as much of your SDP as possible. For detailed guidance on automating IaC and application continuous integration and continuous delivery (CI/CD) processes, see [Recommendations for implementing automation](automate-tasks.md).

- Use CI practices to regularly integrate code changes into repositories. CI practices can help you identify integration conflicts and reduce the likelihood of large, risky merges. For more information, see the [Continuous integration guide](/azure/well-architected/devops/release-engineering-ci).

- Use feature flags to selectively enable or disable new features or changes in production. Feature flags can help you control the exposure of new code and quickly roll back deployment if issues arise.

- Deploy changes to staging environments that mirror your production environment. Practice environments allow you to test changes in a controlled setting before deploying to the live environment.

- Establish predeployment checks, including code review, security scans, and compliance checks, to help ensure that changes are safe to deploy.

- Implement circuit breakers to automatically halt traffic to a service that's experiencing issues. Doing so can help to prevent further degradation of the system.

### Emergency SDP protocols

Establish prescriptive protocols that define how your SDP can be adjusted for a hotfix or for emergency issues like a security breach or vulnerability exposure. For example, your emergency SDP protocols might include:

- Promotion and approval stage acceleration.

- Smoke testing and integration testing acceleration.

- Bake time reduction.

In some cases, the emergency might limit quality and testing gates, but gates should still be run as quickly as possible as an out-of-band exercise. Make sure that you define who can approve SDP acceleration in an emergency and the criteria that must be met for acceleration to be approved. Align your emergency SDP protocols with your [emergency response plan](./emergency-response.md) to help ensure that all emergencies are handled according to the same protocols.

## Considerations

Building and maintaining safe deployment practices is complex. Your success in fully implementing robust standards depends on the maturity of your practices across many areas of software development.  Use of automation, IaC-only for infrastructure changes, consistency in branching strategies, use of feature flags, and many other practices can help to ensure safe deployment. Use this guide to optimize your workload and inform your plans for improvement as your practices evolve.

## Azure facilitation

- [Azure Pipelines](/azure/devops/pipelines/process/stages) and [GitHub Actions](https://docs.github.com/actions/deployment/targeting-different-environments/using-environments-for-deployment) support multi-stage deployments by using approval gates, which can help you design your progressive exposure rollout for deployments.

- Use [Azure App Service staging slots](/azure/app-service/deploy-staging-slots) to easily swap between versions of code. Staging slots are helpful for testing in staging environments and can be used for blue-green deployments.

- Store and manage your web app feature flags in [Azure App Configuration](/azure/azure-app-configuration/manage-feature-flags). By using this service, you can create, change, and deploy features in a unified management plane.

- Deploy workload applications in your virtual machine by using [VM Applications](/azure/virtual-machines/vm-applications-how-to).

- Use [Azure load balancers](/azure/architecture/guide/technology-choices/load-balancing-overview) to implement deployment strategies and expose the health of your workload applications by using native resources.

- Use [Application Health extension](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension) to report on application health from inside a Virtual Machine Scale Set instance. The extension probes on a local application endpoint and updates the health status based on TCP/HTTP(S) responses received from the application.

- Use [Azure Logic Apps](/azure/logic-apps/logic-apps-overview#more-about-azure-logic-apps) to create a new version of the application whenever an update is made to it. Azure maintains a history of application versions and can revert or promote to any previous version.

- Many Azure Database services provide point-in-time restore functionality that can help you roll back. Services that support point-in-time restore include:

  - [Azure SQL Database](/azure/azure-sql/database/recovery-using-backups)
  - [Azure SQL Managed Instance](/azure/azure-sql/managed-instance/point-in-time-restore)
  - [Azure Cosmos DB](/azure/cosmos-db/continuous-backup-restore-introduction)
  - [Azure Database for MySQL](/azure/mysql/flexible-server/concepts-backup-restore)
  - [Azure Database for PostgreSQL](/azure/postgresql/flexible-server/concepts-backup-restore)

## Example

See the [blue-green deployment of Azure Kubernetes Service (AKS) clusters](/azure/architecture/guide/aks/blue-green-deployment-for-aks) architecture guide for an example of how to use this deployment model.

## Related links

- [Application Health extension](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension)
- [Azure App Configuration](/azure/azure-app-configuration/manage-feature-flags)
- [Azure App Service staging slots](/azure/app-service/deploy-staging-slots)
- [Azure Cosmos DB](/azure/cosmos-db/continuous-backup-restore-introduction)
- [Azure Database for MySQL](/azure/mysql/flexible-server/concepts-backup-restore)
- [Azure Database for PostgreSQL](/azure/postgresql/flexible-server/concepts-backup-restore)
- [Azure load balancers](/azure/architecture/guide/technology-choices/load-balancing-overview)
- [Azure Logic Apps](/azure/logic-apps/logic-apps-overview#more-about-azure-logic-apps)
- [Azure Pipelines](/azure/devops/pipelines/process/stages)
- [Azure SQL Database](/azure/azure-sql/database/recovery-using-backups)
- [Azure SQL Managed Instance](/azure/azure-sql/managed-instance/point-in-time-restore)
- [Building a health model](../reliability/metrics.md#building-a-health-model)
- [Continuous integration guide](/azure/well-architected/devops/release-engineering-ci)
- [Deployment Stamps](/azure/architecture/patterns/deployment-stamp)
- [Performance considerations for your deployment infrastructure](/azure/well-architected/devops/release-engineering-performance)
- [Release engineering: Application development](/azure/well-architected/devops/release-engineering-app-dev)
- [Release engineering: Continuous integration](/azure/well-architected/operational-excellence/release-engineering-continuous-integration)
- [Release engineering: Rollback](/azure/well-architected/devops/release-engineering-rollback)
- [Testing your application and Azure environment](/devops/develop/shift-left-make-testing-fast-reliable)
- [VM Applications](/azure/virtual-machines/vm-applications-how-to)

## Community links

- [Advancing safe deployment practices](https://azure.microsoft.com/blog/advancing-safe-deployment-practices/)
- [GitHub Actions](https://docs.github.com/actions/deployment/targeting-different-environments/using-environments-for-deployment)

## Operational Excellence checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Operational Excellence checklist](checklist.md)
