---
title: Recommendations for using safe deployment practices
description: Learn about recommendations for safe deployment practices (SDP). Define how to safely make any change to your workload through deployments.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for using safe deployment practices

**Applies to: OE 11**

This guide describes the best practices for using safe deployment practices (SDP). SDP is a collection of processes and procedures codified by your organization that define how to safely make any change to your workload through deployments. SDP also requires a mind shift to think about deployments through the lens of managing risk. By putting the right practices into place, you can minimize the risk of introducing human error in your deployments and limit the effects of problematic deployments on your end users.

## Key design strategies

Safe deployment practices follow four themes:

- All changes to the production workload are inherently risky and therefore must be done with a focus on safety and consistency.
- Minimize the potential blast radius of deployment-caused issues by adopting progressive exposure deployments.
- Deployments must pass health checks before each phase of progressive exposure can begin
- When issues are detected, the deployment is halted and recovery initiates immediately.

The following sections provide detailed recommendations about each of the themes.

**All changes to the production workload are inherently risky and therefore must be done with a focus on safety and consistency.**

Whether you are deploying an update to your application code, infrastructure as code, feature flags, or a configuration update, you are introducing risk to the workload; there are no “low-risk” deployments to production. As such, every deployment must follow a consistent standard pattern and should be automated to the extent possible to enforce consistency and minimize risks of human error.  Your workload team and the organization at large should adopt a philosophy of treating every deployment as an introduction of risk and every deployment is subject to the same level of risk management. For this reason, it is critical that your workload supply chain and deployment pipelines are reliable and secure (link to OpEx supply chain guide) and you have clearly defined deployment standards. Be aware that this philosophy should not discourage or scare you away from deploying (and deploying frequently in fact) changes to your workload. To the contrary, failing to deploy regularly introduces risks like security vulnerabilities that must be addressed through deployments. This philosophy is a mindset of approaching deployments with a consistent, clearly defined set of standards.

Although it may seem counterintuitive at first, a standard of frequent small deployments is preferable to infrequent large deployments. Small changes are easier to recover from when issues arise and frequent deployments help your team build confidence in the deployment processes.

Another key tenet is that you should learn from production. Learning from production means that every time there is an anomaly during a deployment, you can learn something about your processes or about your workload. You may find weaknesses in your infrastructure design or in the way that you have designed your rollout stages, among many other possible areas for improvement. When issues arise during deployments, ensure that _blameless_ post-mortems are part of your SDP processes to capture learnings about the incident.

**General SDP recommendations**

- Implement versioning across your build artifacts to ensure that you can roll back and forward when necessary. 
- Rather than a Gitflow or environment-based branching structure with long-lived dev, QA or release branches, use a release flow or trunk-based branching structure, which enforces tightly synced collaboration across the development team.
- Use CI practices to regularly integrate code changes into repositories. This helps identify integration conflicts and reduces the likelihood of large, risky merges. See the [Continuous integration guide](/azure/well-architected/devops/release-engineering-ci) for detailed guidance.
- Automate as much of your SDP as possible. See the automate tasks (link to OpEx automate tasks guide) guide for detailed guidance on automating infrastructure-as-code (IaC) and application CI/CD processes.
- Utilize feature flags to selectively enable or disable new features or changes in production. This allows you to control the exposure of new code and quickly roll back if issues arise. 
- Deploy changes to staging environments that mirror your production environment. This allows you to test changes in a controlled setting before deploying to the live environment. 
- Establish a checklist of pre-deployment checks, including code review, security scans, and compliance checks, to ensure that changes are safe to deploy. 
- Implement circuit breakers to automatically halt traffic to a service experiencing issues, preventing further degradation of the system.

**Minimize the potential blast radius of deployment-caused issues by adopting progressive exposure deployments.**

When deployment issues happen, the goal is to catch them as early as possible and minimize the impact on end-users to the extent possible. To help accomplish this goal, adopt a gradual rollout deployment model, also known as a progressive exposure model. Canary deployments are a common example of progressive exposure. In this model, you identify a small group of internal or external users that will receive the new features first and after that group has had a chance to run the new version without issue, you deploy to a larger group, followed by successively larger groups until the entire user population is running the new version.  In this model, you typically use feature flags to enable the new version for the target users.

Another common deployment model is a blue-green approach. In this model, two identical sets (or pools) of workload infrastructure are deployed, both able to handle a full production load.  One pool (the blue pool) runs the current version of the deployment and all users land on this pool. Meanwhile, the other pool (the green pool) is updated with the new version and internally tested. After internal testing a subset of the production traffic is routed to the updated pool.  Like canary deployments, the rollout happens progressively as you shift more of the traffic over to the green pool in successively larger rollout waves. After you have finished the whole rollout, the updated pool then becomes the blue pool and the new green pool is ready for the next deployment. The two pools are logically separated from each other to protect from malfunctions.  If you have deployed your workload in the [Deployment Stamps](/azure/architecture/patterns/deployment-stamp) design pattern, a variation of the blue-green model can be run by deploying on one stamp at a time.

In either model, the time between each phase of the rollout is critical for monitoring the workload closely to watch for changes in health metrics, and you should provide ample “bake time” (time between roll out groups) to ensure that users from different regions or performing different tasks have time to use the workload in their normal capacity. Generally speaking bake times should be measured in hours and days rather than minutes. The bake times will likely be longer for each group as you progressively move forward with the rollouts so that you can account for more users using the workload over different time zones and usage patterns changing over the course of the day.

**Deployments must pass health checks before each phase of progressive exposure can begin**

As part of building your observability platform and your reliability strategies, you should have developed a robust health model for your workload (link to reliability/metrics.md/building a health model anchor) that gives you in-depth visibility into the workload components and the overall health of the workload. As you monitor the workload during each phase of the rollout, if an alert about a health change occurs and it is correlated with one or more of the groups that has been moved onto the new version, the rollout should immediately halt and an investigation into the cause of the alert must be performed to help determine the next course of action. Conversely, if all health indicators stay green throughout the bake time and no end-user reported issues come in, you can give the all-clear signal to move forward with the rollout. Be sure to include some metrics about usage at each rollout stage to ensure that a lack of user reported issues and negative health signals isn’t a false positive hiding an issue. If you can see that users in each rollout group are actively using the new version, that will give you an extra measure of confidence.

**When issues are detected, the deployment is halted and recovery initiates immediately.**

When your deployment causes an issue in one of the rollout groups, the deployment rollout must halt immediately. Whether you are alerted by your observability platform or by an end-user, the decision about how to proceed must happen as quickly as possible, so an investigation into the cause of the issue and the severity of the effects must be performed as soon as the alert is received.  

Your recovery from a deployment issue can follow one of the following paths:

- Rollback: this is the process of undoing the changes made in the deployment and reverting back to the last known good configuration.
- Roll forward: this is the process of addressing the issue in the midst of the rollout by applying a hotfix or otherwise mitigating the issue.
- Deploy new infrastructure with the last known good configuration

Rolling back changes can be complex, especially with regard to dealing with databases and schema changes and other stateful components. Ensure that your SDP guidelines provide clear instructions on how to deal with data changes according to the data estate design for your workload.

Likewise, rolling forward must be handled very carefully to ensure that SDP is not neglected, and the hotfix or other mitigation efforts are performed safely.

**Emergency SDP protocols**

When you need to deploy a hotfix to respond to a rollout that has encountered issues, or in cases of emergencies like security breaches or vulnerability exposures, you should have prescriptive protocols documented that define how your SDP can be modified in limited ways to respond to these cases:

- Promotion and approval stages can be accelerated, but should still happen to ensure quality
- Smoke testing and integration testing can be accelerated, but should still happen 
- Bake times can be reduced

In some cases, the emergency might be critical enough to force you to perform limited quality and testing gates, but they should still be run in full as quickly as possible as an out of band exercise.

Ensure that you have defined who can approve accelerating your SDP and the criteria that must be met to be approved.

Your emergency SDP protocols should be aligned with your emergency response plan (link to OpEx emergency response guide) to ensure that all emergencies are handled according to the same protocols.

## Azure facilitation

- [Azure DevOps Pipelines](/azure/devops/pipelines/process/stages?view=azure-devops) and [GitHub Actions](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment) support multi-stage deployments with approval gates, which can help you design your progressive exposure rollout for deployments.
- Use [Azure App Service staging slots](/azure/app-service/deploy-staging-slots) to easily swap between versions of code. These slots are helpful for testing in staging environments or can be used for blue-green deployments.
- Store and manage your web app feature flags in [Azure App Configuration](/azure/azure-app-configuration/manage-feature-flags). Using this function gives you a unified management plane to create, modify and deploy features.
- Deploy workload application in your Virtual Machines using [VM Applications](/azure/virtual-machines/vm-applications-how-to).
- [Azure Load Balancers](/azure/architecture/guide/technology-choices/load-balancing-overview) can be used to implement some of the described deployment strategies and some options provide with native resources to expose the health of your workload applications.
- [The Application Health Extension](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension) is deployed inside a Virtual Machine Scale Set instance and reports on application health from inside the scale set instance. The extension probes on a local application endpoint and will update the health status based on TCP/HTTP(S) responses received from the application.
- [Azure Logic Apps](/azure/logic-apps/manage-logic-apps-with-azure-portal) create a new version of the application whenever an update is made to it. Azure maintains a history of versions and can revert or promote any previous version.
- Many Azure database services provide point-in-time restore functionality that can help you when you need to roll back.
  - [Azure SQL Database](/azure/azure-sql/database/recovery-using-backups?view=azuresql)
  - [Azure SQL Managed Instance](/azure/azure-sql/managed-instance/point-in-time-restore?view=azuresql)
  - [Cosmos DB](/azure/cosmos-db/continuous-backup-restore-introduction)
  - [Azure Database for MySQL](/azure/mysql/flexible-server/concepts-backup-restore)
  - [Azure Database for PostgreSQL](/azure/postgresql/flexible-server/concepts-backup-restore)

## Tradeoffs

Building and maintaining safe deployment practices is _hard_ and your success in fully implementing robust standards depends to some extent on the maturity of your practices across many areas of software development.  Your team’s embrace of recommended practices like a robust use of automation, strict use of IaC-only for infrastructure changes, and consistency in branching strategies, use of feature flags, among many other practices will help you adopt many of the recommendations given in this guide. However, use this guide to optimize your practices to the extent that is practical for where your team is at in terms of the maturity of your development practices and use it to inform your plans for improvement as your practices evolve.

There are tradeoffs for each deployment model discussed in this guide.

For canary deployments, there will be times that you have to support two versions of the application on the same infrastructure, which increases the management burden on the workload and support teams.

For blue-green deployments, there will be times when you have two sets of production infrastructure running at the same time, which is costly from a financial standpoint and from a management standpoint.

## Example

See the [Blue-green deployment of AKS clusters](/azure/architecture/guide/aks/blue-green-deployment-for-aks) architecture guide for an example of using this deployment model.

## Related links

WAF guide: Release engineering: Deployment  (/azure/well-architected/devops/release-engineering-cd) 

WAF guide: Performance considerations for your deployment infrastructure (/azure/well-architected/devops/release-engineering-performance) 

WAF guide: Release engineering: Rollback (/azure/well-architected/devops/release-engineering-rollback) 

WAF guide: Release engineering: Application dev 

WAF guide: Release engineering: Application development . (/azure/well-architected/devops/release-engineering-app-dev) 

WAF guide: Release engineering: Continuous integration (/azure/well-architected/devops/release-engineering-ci) 

WAF guide: Testing your application and Azure environment (/azure/well-architected/devops/release-engineering-testing)

https://azure.microsoft.com/blog/advancing-safe-deployment-practices/

## Next steps

We recommend that you review the Operational Excellence checklist to explore other concepts. 

> [!div class="nextstepaction"] 
> [Operational Excellence checklist](checklist.md) 