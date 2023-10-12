---
title: Recommendations for defining reliability targets
description: Learn how to define reliability targets for your critical workloads and flows. Discover key design strategies such as taking a customer-focused approach, defining service-level objectives (SLOs) and service-level agreements (SLAs), and building a health model to ensure appropriate operational prioritization. Explore examples of composite SLAs, recovery targets, and reporting strategies.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for defining reliability targets

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

|[RE:03](checklist.md)| Define reliability and recovery targets for the components, the flows, and the overall solution. Visualize the targets to negotiate, gain consensus, set expectations, and drive actions to achieve the ideal state. Use the defined targets to build the health model. The health model defines what healthy, degraded, and unhealthy states look like. |
|---|---|

This guide describes the recommendations for defining availability and recovery target metrics for critical workloads and flows. Reliability targets are derived through workshop exercises with business stakeholders. The targets are refined through monitoring and testing. 

With your internal stakeholders, set realistic expectations for workload reliability so that stakeholders can communicate those expectations to customers through contractual agreements. Realistic expectations also help stakeholders understand and support your architectural design decisions and know that you're designing to optimally meet the targets you agreed on.

Consider using the following metrics to quantify the business requirements.

|Term  |Definition  |
|---------|---------|
|Service-level objective (SLO)    | A percentage target that represents the health of the component and the reliability tier. The higher the tier, the more reliable the component. *Composite SLO* represents the aggregate target of the entire workload and accounts for the component SLOs.        |
|Service-level indicator (SLI)     | A metric emitted by a service. SLI metrics are aggregated to quantify an SLO value.        |
|Service-level agreement (SLA)     | A contractual agreement between the service provider and the service customer. The agreement defines the SLOs. Failure to meet the agreement might have financial consequences for the service provider.        |
|Mean time to recover (MTTR)     | The time taken to restore a component after a failure is detected.        |
|Mean time between failure (MTBF)     |The duration for which the workload can perform the expected function without interruption, until it fails.         |
|Recovery time objective (RTO)     | The maximum acceptable time that an application can be unavailable after an incident.        |
|Recovery point objective (RPO)     | The maximum acceptable duration of data loss during an incident.        |

Define the workload's target values for these metrics in the context of user flows and system flows. [Identify and score those flows](identify-flows.md) by how critical they are to your requirements. Use the values to drive the design of your workload in terms of architecture, review, testing, and incident management operations. Failure to meet the targets will affect the business beyond the tolerance level.

## Key design strategies

Technical discussions shouldn't drive how you define reliability targets for your critical flows. Instead, business stakeholders should focus on customers as they define a workload's requirements. Technical experts help the stakeholders assign realistic numerical values that correlate to those requirements. As they share knowledge, technical experts allow for negotiation and mutual consensus about realistic SLOs. 

Consider an example of how to map requirements to measurable numerical values. Stakeholders estimate that for a critical user flow, an hour of downtime during regular business hours results in a loss of *X* dollars in monthly revenue. That dollar amount is compared to the estimated cost of designing a flow that has an availability SLO of 99.95 percent rather than 99.9 percent. Decision makers must discuss whether the risk of that revenue loss outweighs the added costs and management burden required to protect against it. Follow this pattern as you examine flows and build a complete list of targets.

Remember that reliability targets differ from performance targets. Reliability targets focus on availability and recovery. To set reliability targets, start by defining the broadest requirements and then define more specific metrics to meet the high-level requirements. 

Highest-level reliability and recovery requirements and correlated metrics might include, for example, an application availability of 99.9 percent for all regions or a target RTO of 5 hours for the Americas region. Defining these types of targets helps you identify which critical flows are involved in those targets. Then you can consider component-level targets.

### Availability metrics

#### SLOs and SLAs

Availability metrics correlate to SLOs, which you use to define SLAs. The workload SLO determines how much downtime is tolerable in a given period, for example, less than 1 hour per month. To make sure you can meet the SLO target, review the Microsoft SLAs for each component. Pay attention to how much redundancy you need to meet high SLAs. For example, Microsoft guarantees higher SLAs for multi-region deployments of Azure Cosmos DB than it guarantees for single-region deployments.

> [!NOTE]
> Azure SLAs don't always cover all aspects of a service. For example, Azure Application Gateway has an availability SLA, but the Azure Well-Architected Framework functionality provides no guarantee to stop malicious traffic from passing through. Consider this limitation when you develop your SLAs and SLOs.

After you gather the SLAs for the individual workload components, calculate a composite SLA. The composite SLA should match the workload's target SLO. Calculating a composite SLA involves several factors, depending on your architecture design. Consider the following examples.

> [!NOTE]
> The SLA values in the following examples are **hypothetical** and are for **demonstration purposes only.** They **aren't intended to represent current values** supported by Microsoft.

Composite SLAs involve multiple services that support an application, with differing levels of availability. For example, consider an Azure App Service web app that writes to Azure SQL Database. Hypothetically, these SLAs might be:

-   App Service web apps = 99.95 percent

-   SQL Database = 99.99 percent

What's the maximum downtime you can expect for this application? If either service fails, the whole application fails. The probability of each service failing is independent, so the composite SLA for this application is 99.95 percent × 99.99 percent = 99.94 percent. That value is lower than the individual SLAs. This conclusion is unsurprising because an application that relies on multiple services has more potential failure points.

You can improve the composite SLA by creating independent fallback paths. For example, if SQL Database is unavailable, put transactions into a queue to be processed later:

:::image type="content" source="media/metrics/independent-fallback-paths.png" alt-text="Diagram that shows fallback paths. The web app box shows arrows branching to SQL Database or to a queue." border="false" lightbox="media/metrics/independent-fallback-paths.png":::

In this design, the application is still available even if it can\'t connect to the database. However, it fails if the database and the queue fail at the same time. The expected percentage of time for a simultaneous failure is 0.0001 × 0.001, so here's the composite SLA for this combined path:

Database or queue = 1.0 − (0.0001 × 0.001) = 99.99999 percent

The total composite SLA:

Web app and (database or queue) = 99.95 percent × 99.99999 percent = \~99.95 percent

This approach poses tradeoffs: 

- The application logic is more complex. 
- You pay for the queue. 
- You need to consider data consistency issues.

For multi-region deployments, the composite SLA is calculated as follows:

-  *N* is the composite SLA for the application that's deployed in one region.

-  *R* is the number of regions where the application is deployed.

The expected chance that the application fails in all regions at the same time is ((1 − N) \^ R). For example, if the hypothetical single-region SLA is 99.95 percent:

-   The combined SLA for two regions = (1 − (1 − 0.9995) \^ 2) = 99.999975 percent

-   The combined SLA for four regions = (1 − (1 − 0.9995) \^ 4) = 99.999999 percent

Defining proper SLOs takes time and careful consideration. Business stakeholders should understand how key customers use the app. They should also understand the reliability tolerance. This feedback should inform the targets.

#### SLA values

The following table defines common SLA values. 

|SLA  |Downtime per week  |Downtime per month  |Downtime per year  |
|---------|---------|---------|---------|
|99%      | 1.68 hours        |  7.2 hours       | 3.65 days        |
|99.9%      |  10.1 minutes       | 43.2 minutes         | 8.76 hours        |
|99.95%     | 5 minutes         | 21.6 minutes        | 4.38 hours        |
|99.99%      | 1.01 minutes        |  4.32 minutes       |  52.56 minutes       |
|99.999%     |  6 seconds        | 25.9 seconds         |  5.26 minutes       |

When you think about composite SLAs in the context of flows, remember that different flows have different criticality definitions. Consider these differences when you build your composite SLAs. Noncritical flows might have components that you should omit from your calculations because they don't affect the customer experience if they're briefly unavailable.

> [!NOTE]
> Customer-facing workloads and internal-use workloads have different SLOs. Typically, internal-use workloads can have much less restrictive availability SLOs than customer-facing workloads.

#### SLIs

Think of SLIs as component-level metrics that contribute to an SLO. The most significant SLIs are the ones that affect your critical flows from the perspective of your customers. For many flows, SLIs include latency, throughput, error rate, and availability. A good SLI helps you identify when an SLO is at risk of being breached. Correlate the SLI to specific customers when possible.

To avoid collecting useless metrics, limit the number of SLIs for each flow. Aim for three SLIs per flow if possible.

### Recovery metrics

Recovery targets correspond to RTO, RPO, MMTR, and MTBF metrics. In contrast to availability targets, recovery targets for these measurements don't depend heavily on Microsoft SLAs. Microsoft publishes RTO and RPO guarantees only for some products, like [SQL Database](/azure/azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview).

Definitions for realistic recovery targets rely on your [failure mode analysis](failure-mode-analysis.md) and your plans and testing for business continuity and [disaster recovery](disaster-recovery.md). Before you finish this work, discuss aspirational targets with stakeholders and ensure that your architecture design supports the recovery targets to the best of your understanding. Clearly communicate to stakeholders that any flows or entire workloads that aren't thoroughly tested for recovery metrics shouldn't have guaranteed SLAs. Make sure that stakeholders understand that recovery targets can change over time as workloads are updated. The workload can become more complex as customers are added or as you adopt new technologies to improve the customer experience. These changes can increase or decrease your recovery metrics.

> [!NOTE]
> MTBF can be challenging to define and guarantee. Platforms as a service (PaaS) or software as a service (SaaS) can fail and recover without any notification from the cloud provider, and the process can be completely transparent to you or your customers. If you define targets for this metric, cover only components that are under your control.

As you define recovery targets, define thresholds for initiating a recovery. For example, if a web node is unavailable for more than 5 minutes, a new node is automatically added to the pool. Define thresholds for all components, considering what recovery for a specific component involves, including the effect on other components and dependencies. Your thresholds should also account for [transient faults](handle-transient-faults.md) to ensure that you don't start recovery actions too quickly. Document and share with the stakeholders the potential risks of recovery operations, like data loss or session interruptions for customers.

### Building a health model 

Use the data you gathered for your reliability targets to build your health model for each workload and associated critical flows. A health model defines *healthy*, *degraded*, and *unhealthy* states for the flows and workloads. The states ensure appropriate operational prioritization. This model is also known as a *traffic light model*. The model assigns green for healthy, yellow for degraded, and red for unhealthy. A health model ensures that you know when a flow's state changes from healthy to degraded or unhealthy. 

How you define healthy, degraded, and unhealthy states depends on your reliability targets. Here are some examples of ways you might define the states:

-   A **green or healthy** state indicates that key nonfunctional requirements and targets are fully satisfied and that resources are used optimally. For example, 95 percent of requests are processed in \<=500 ms with Azure Kubernetes Service (AKS) node use at *X* percent.

-   A **yellow or degraded** state indicates that one or more components of the flow are alerting against their defined threshold, but the flow is operational. For example, storage throttling has been detected.

-   A **red or unhealthy** state indicates that degradation has persisted longer than allowable by your reliability targets or that the flow has become unavailable.

> [!NOTE]
> The health model shouldn't treat all failures the same. The health model should distinguish between *transient* and *nontransient* faults. It should clearly distinguish between expected-transient but recoverable failures and a true disaster state.

This model works by using a monitoring and alerting strategy that's developed and operated on the principles of continuous improvement. As your workloads evolve, your health models must evolve with them.

For detailed design considerations and recommendations for a layered application health model, see the [health modeling guidance](/azure/well-architected/mission-critical/mission-critical-health-modeling) found in the mission-critical workload design areas. For detailed guidance about monitoring and alerting configurations, see the [health monitoring](monitoring-alerting-strategy.md) guide.

### Visualization

To keep your operations teams and workload stakeholders informed about the real-time status and overall trends of the workload health model, consider creating [dashboards](/azure/azure-monitor/visualize/tutorial-logs-dashboards) in your monitoring solution. Discuss visualization solutions with the stakeholders to ensure that you deliver the information that they value and that's easy to consume. They might also want to see generated reports weekly, monthly, or quarterly.

## Azure facilitation

Azure SLAs provide the Microsoft commitments for uptime and connectivity. Different services have different SLAs, and sometimes SKUs within a service have different SLAs. For more information, see [Service-level agreements for online services](https://www.microsoft.com/licensing/docs/view/Service-Level-Agreements-SLA-for-Online-Services).

The Azure SLA includes procedures for obtaining a service credit if the SLA isn\'t met, along with definitions of availability for each service. That aspect of the SLA acts as an enforcement policy.

## Tradeoffs 

A conceptual gap might exist between the technical limitations of your workload's components and what that means for the business, for example, throughput in megabits per second versus transactions per second. Creating a model between these two views might be challenging. Rather than overengineering the solution, try to approach it in an economical but meaningful way.

## Related links

- Cloud Adoption Framework guidance for SLOs and SLIs: [Cloud monitoring SLOs](/azure/cloud-adoption-framework/manage/monitor/service-level-objectives#how-do-you-define-slis)

- Well-Architected Framework mission-critical guidance for health modeling: [Health modeling and observability of mission-critical workloads on Azure](/azure/well-architected/mission-critical/mission-critical-health-modeling)

## Reliability checklist  

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Reliability checklist](checklist.md) 