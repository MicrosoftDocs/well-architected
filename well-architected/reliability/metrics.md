---
title: Recommendations for defining reliability targets
description: Learn how to define reliability targets for your critical workloads and flows.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for defining reliability targets RE:03

This guide describes the best practices for defining availability and recovery target metrics for critical workloads and flows. These are derived through workshop exercises with business stakeholders and refined through monitoring and testing. Creating realistic expectations for workload reliability with your internal stakeholders helps ensure that they are able to communicate those expectations to customers through contractual agreements. Realistic expectations also help stakeholders understand and support your architectural design decisions, knowing that you are designing to optimally meet the targets you have agreed upon.

## Business metrics

Here are some of the metrics that you should consider to quality the business requirements.

|Term  |Definition  |
|---------|---------|
|Service-level objectives (SLO)    | A percentage target that represents the health of the component and the reliability tier. The higher the tier, the more reliable the component. Composite SLO represents aggregate target of the entire workload considering the individual component SLOs.        |
|Service-level indicators (SLIs)     | Metrics emitted by a service, which are aggregated to quantify an SLO value.        |
|Service-level agreements (SLAs)     | Contractual agreement between the service provider and service user on the SLOs. Failure to meet the commitment may have financial consequences for the service provider.        |
|Mean time to recover (MTTR)     | Time taken to restore a component after a failure is detected.        |
|Mean time between failures (MTBF)     |Duration that the workload can perform the expected function without interruption, until it next fails.         |
|Recovery time objective (RTO)     | Maximum acceptable time an application can be unavailable after an incident.        |
|Recovery point objective (RPO)     | Maximum duration of data loss that\'s acceptable during a incident.        |

Target values for the metrics in the preceding table must be defined for the workload in the context of the user and system flows you have identified and scored by their criticality (link to RE01), given a set of requirements. The values will drive the design of your workload in terms of architecture, review, testing, and incident management operations. Failure to meet the targets will cause a business impact that's beyond the tolerance level.

## Key design strategies

The various activities involved in the exercise of defining reliability targets for your critical flows should not be driven by technical discussions. Using a customer-focused perspective, business stakeholders define the requirements that the workload must meet, and technical experts should help the stakeholders assign realistic numerical values that correlate to those requirements. This interaction should be a knowledge-sharing exercise that allows for negotiation and ultimately, mutual consensus on what realistic SLOs look like. As an example of mapping the requirements to measurable numerical values, the stakeholders may estimate that for a critical user flow, an hour of downtime during regular business hours will result in a loss of X dollars in monthly revenue. That dollar amount should be compared to the estimated costs of designing the flow to have an availability SLO of 99.95% rather than something lower, like 99.9%., A decision will need to be made about whether the risk of that revenue loss outweighs the added costs and management burden required to protect against it. Take a similar approach to this as you walk through the flows, building a complete list of targets.

Remember that reliability targets differ from performance targets. Reliability targets focus on availability and recovery. The reliability targets defining exercise should take a top-down approach with the broadest requirements defined first and then more specific metrics can be defined to meet the higher-level requirements. Examples of the highest-level reliability and recovery requirements and correlated metrics include an application availability of 99.9% for all regions or assigning a target RTO of 5 hours for the Americas region. Defining these types of targets will help you identify which critical flows are involved in meeting those targets and you can then start to think about component-level targets.

## Availability metrics

### Service-level objectives (SLOs) and service-level agreements (SLAs)

Availability metrics correlate to service-level objectives (SLOs) which are used to define your service-level agreements (SLAs). The workload SLO will define the amount of downtime tolerable in a given time period (less than 1 hour per month, for example). To ensure that you are able to meet the SLO target, review the Microsoft SLAs for each component, paying attention to the redundancy requirements to meet higher SLAs. For example, Microsoft guarantees higher SLAs for multi-region deployments of Cosmos DB than it does for single region deployments.

> [!NOTE]
> Azure SLAs do not always cover all aspects of a given service. For example, there is an availability SLA for Application Gateway, but no guarantee for the WAF functionality to stop malicious traffic from passing through. Be sure to take this limitation into account when developing your SLAs and SLOs.

After you have gathered the SLAs for the individual workload components, you can calculate a composite SLA, which should match the workload target SLO. There are several factors involved in calculating a composite SLA, depending on your architecture design.

**The SLA values provided in the examples below are hypothetical and are used for demonstration purposes only. They should not be assumed to be current values supported by Microsoft**

Composite SLAs involve multiple services supporting an application, with differing levels of availability. For example, consider an App Service web app that writes to Azure SQL Database. Hypothetically, these SLAs could be:

-   App Service web apps = 99.95%

-   SQL Database = 99.99%

What is the maximum downtime you would expect for this application? If either service fails, the whole application fails. The probability of each service failing is independent, so the composite SLA for this application is 99.95% × 99.99% = 99.94%. That value is lower than the individual SLAs, which isn\'t surprising because an application that relies on multiple services has more potential failure points.

You can improve the composite SLA by creating independent fallback paths. For example, if SQL Database is unavailable, put transactions into a queue to be processed later:

:::image type="content" source="media/metrics/independent-fallback-paths.png" alt-text="Diagram that shows fallback paths. The web app box shows arrows branching to SQL Database or to queue." border="false" lightbox="media/metrics/independent-fallback-paths.png":::

With this design, the application is still available even if it can\'t connect to the database. However, it fails if the database and the queue both fail at the same time. The expected percentage of time for a simultaneous failure is 0.0001 × 0.001, so the composite SLA for this combined path is:

Database or queue = 1.0 − (0.0001 × 0.001) = 99.99999%

The total composite SLA is:

Web app and (database or queue) = 99.95% × 99.99999% = \~99.95%

There are tradeoffs to this approach. The application logic is more complex, you\'re paying for the queue, and you need to consider data consistency issues.

For multi-region deployments, the composite SLA is calculated as follows:

-   N is the composite SLA for the application deployed in one region.

-   R is the number of regions where the application is deployed.

The expected chance that the application fails in all regions at the same time is ((1 − N) \^ R). For example, if the hypothetical single-region SLA is 99.95%:

-   The combined SLA for two regions = (1 − (1 − 0.9995) \^ 2) = 99.999975%

-   The combined SLA for four regions = (1 − (1 − 0.9995) \^ 4) = 99.999999%

Defining proper SLOs takes time and careful consideration. The business stakeholders should understand how key customers use the app and their reliability tolerance and use this feedback to inform their targets.

### SLA values

|SLA  |Downtime per week  |Downtime per month  |Downtime per year  |
|---------|---------|---------|---------|
|99%      | 1.68 hours        |  7.2 hours       | 3.65 days        |
|99.9%      |  10.1 minutes       | 43.2 minutes         | 8.76 hours        |
|99.95%     | 5 minutes         | 21.6 minutes        | 4.38 hours        |
|99.99%      | 1.01 minutes        |  4.32 minutes       |  52.56 minutes       |
|99.999%     |  6 seconds        | 25.9 seconds         |  5.26 minutes       |

When thinking about composite SLAs in the context of flows, remember that different flows have different criticality definitions, so be sure to consider this when building your composite SLAs. Non-critical flows may have components that should not be included in your calculations as they do not effect the user experience if they are unavailable for a given amount of time.

> [!NOTE]
> There will be different SLOs for customer-facing and internal-use workloads. Typically, internal-use workloads can have much less restrictive availability SLOs than customer-facing workloads.

### Service-level indicators (SLIs)

SLIs can be thought of as component-level metrics that contribute to an SLO. The SLIs that matter most are the ones which can impact your critical flows most from the perspective of your customers. For many flows this includes latency, throughput, error rate, and availability. A good SLI helps you identify when an SLO is at risk of being breached and should be correlated to specific customers when possible.

In order to avoid collecting metrics that aren't useful, be economical about the number of SLIs that you identify for each flow: try to target three SLIs per flow if possible.

## Recovery metrics

Recovery targets correspond to recovery time objectives (RTOs), recovery point objectives (RPOs), mean-time to recovery (MMTR) and mean-time between failure (MTBF) metrics. Defining targets for these measurements is less tightly bound to Microsoft SLAs than availability targets as Microsoft only publishes RTO and RPO guarantees for certain products like [Azure SQL Database](/azure/azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview?view=azuresql#recover-a-database-to-the-existing-server).

Defining realistic recovery targets relies on work that you have done in your failure mode analysis (link to RE:02) and your business continuity and disaster recovery (link to RE:09 here) planning and testing. Before this work has been completed, you can discuss aspirational targets with stakeholders and ensure that your architecture design will support the recovery targets to the best of your understanding. Be clear with stakeholders that any flows or entire workloads that have not been thoroughly tested for recovery metrics should not have guaranteed SLAs associated with them. It is also important to ensure that stakeholders understand that all of the recovery targets can change over time as updates to workloads happen: the workload may become more complex as more customers are added, or you may adopt new technologies to improve the user experience, and these changes can increase or decrease your recovery metrics.

> [!NOTE]
> MTBF will be challenging to define and provide guarantees for in some cases. PaaS or SaaS services can fail and recover without any notification from the cloud provider and the process can be completely transparent to you or your customers. If you define targets for this metric, ensure that your targets only cover those components that are under your control.

As part of the exercise of defining recovery targets, you should also define thresholds for initiating a recovery. For example, if a web node has been unavailable for more than 5 minutes, a new node will automatically be added to the pool. Go through this exercise for all components, taking into consideration what recovery for that specific component involves including impact on other components and dependencies. Be sure to consider transient faults (link to re06-transient-fault-handling) when determining your thresholds to ensure that you aren't initiating recovery actions too quickly. Document and share with the stakeholders the potential risks involved with recovery operations, like data loss or session interruptions for end users.

## Building a health model 

Using the data you have gathered while building your reliability targets, you can then build your health model for each workload and associated critical flows. A health model defines "healthy", "degraded", and "unhealthy" states for the flows and workloads to ensure that appropriate operational prioritization is applied. This model is also known as a *traffic light model*, that assigns green for healthy, yellow for degraded and red for unhealthy states. The overall purpose of a health model is to ensure that you know when the state of a given flow changes from a healthy to state to a degraded or unhealthy state. How you define healthy, degraded and unhealthy states depends on the work you have done with stakeholders to identify reliability targets, but examples of ways you might define them are:

-   A green or healthy state indicates that key nonfunctional requirements and targets are fully satisfied and when resources are optimally utilized. For example, 95 percent of requests are processed in \<=500 ms with AKS node utilization at x%.

-   A yellow or degraded state indicates that one or more components of the flow are alerting against their defined threshold, but the flow is operational. For example, storage throttling has been detected.

-   A red or unhealthy state indicates that degradation has persisted for a longer period than deemed allowable by your reliability targets or the flow has become unavailable.

> [!NOTE]
> The health model shouldn't treat all failures the same. The health model should distinguish between *transient* and *non-transient* faults. It should clearly distinguish between expected-transient but recoverable failures and a true disaster state.

The method by which this model functions is through a monitoring and alerting strategy developed and operated in the principles of continuous improvement. As your workloads evolve, your health models will need to evolve with them.

Refer to the [health modeling guidance](/azure/well-architected/mission-critical/mission-critical-health-modeling) found in the mission-critical workload design areas to find detailed design considerations and recommendations for building a layered application health model. Refer to the health monitoring (link to RE: 10) guide in this series for detailed guidance on monitoring and alerting configurations.

## Visualization

To keep your operations teams and workload stakeholders apprised of the real-time status and overall trends of the workload health model, consider creating [dashboards](/azure/azure-monitor/visualize/tutorial-logs-dashboards) in your monitoring solution. Ensure that the visualization solution is another topic that you discuss with the stakeholders to ensure that you are delivering the information that they deem important and in a way that is easily consumable for them. Instead, or in addition to, of dashboards, they may prefer that reports are generated on a regular reoccurring basis (like weekly, monthly or quarterly, for example).

## Azure facilitation

Azure SLAs provide Microsoft\'s commitments for uptime and connectivity. Different services have different SLAs, and in some cases different SKUs within a given service have different SLAs. For detailed information, see [Service Level Agreement](https://www.microsoft.com/licensing/docs/view/Service-Level-Agreements-SLA-for-Online-Services?lang=1).

Azure SLA also includes procedures for obtaining a service credit if the SLA isn\'t met, along with specific definitions of availability for each service. That aspect of the SLA acts as an enforcement policy.

## Tradeoffs 

Be aware that there might be a conceptual gap between the technical limitations inherent in components of your workload and what that means for the business (for example, throughput in Mbit/s vs transactions per second). Creating a model between these two "views" might be challenging, so it is important that you don't over-engineer the solution, but try to approach it in an economic, yet meaningful way.

## Related links

- Cloud Adoption Framework guidance for SLOs and SLIs: [Cloud monitoring service level objectives - Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/monitor/service-level-objectives#how-do-you-define-slis)

- Well-Architected Framework mission-critical guidance for health modeling: [Health modeling and observability of mission-critical workloads on Azure - Microsoft Azure Well-Architected Framework](/azure/well-architected/mission-critical/mission-critical-health-modeling)
