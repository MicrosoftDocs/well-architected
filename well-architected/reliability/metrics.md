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

|[RE:04](checklist.md)| Define reliability and recovery targets for the components, the flows, and the overall solution. Visualize the targets to negotiate, gain consensus, set expectations, and drive actions to achieve the ideal state. Use the defined targets to build the health model. The health model defines what healthy, degraded, and unhealthy states look like. |
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

**Reliability targets represent the desired quality goal of a workload**, as promised to its users and its business stakeholders. That goal includes both availability and recoverability of the workload. 

It's important to determine the reliability targets for the critical flows of the workload. Technical discussions shouldn't drive those targets. Instead, business stakeholders should focus on customers as they define a workload's requirements. Technical experts help the stakeholders assign realistic numerical values that correlate to those requirements. As they share knowledge, technical experts allow for negotiation and mutual consensus about realistic SLOs.

To set reliability targets, start by defining the broadest requirements and then define more specific metrics to meet the high-level requirements.

Highest-level reliability and recovery requirements and correlated metrics might include, for example, an application availability of 99.9 percent for all regions or a target RTO of 5 hours for the Americas region. Defining these types of targets helps you identify which critical flows are involved in those targets. Then you can consider component-level targets.

Remember that reliability targets differ from performance targets. 

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: A conceptual gap might exist between the technical limitations of your workload's components and what that means for the business, for example, throughput in megabits per second versus transactions per second. Creating a model between these two views might be challenging. Rather than overengineering the solution, try to approach it in an economical but meaningful way.


### Availability targets

Measurement targets for availability define the quality expectations of a workload for it to remain accessible and operational, below which the workload would be considered unreliable to its users. Service Level Objectives (SLOs) are a standard way to evaluate availability targets for the end-to-end user experience. SLOs are defined by business stakeholders with help from technical stakeholders to keep the objectives realistic within the given constraints.

#### Set your SLO

The overall SLO of a workload is a collective quality indication of all its logical boundaries, all of which should be regarded as dependencies. Some of those dependencies might be individual SLOs of software services (and the responsible team), to be improved over time. A mature declaration of the overall SLO should still indicate the business target for that workload, not just a composite of those dependencies. For example, if users expect that the workload to have 99.99% availability, and one of dependencies only achieves 99.8% availability, the overall SLO is still 99.99%.

For the workload owner, objective setting exercises are driven by financial goals where business requirements are mapped to measurable metrics. The goal is to identify a set of factors that influence the user experience and define their targets, which reflect the  quality of experience for successful workload usage.

Stakeholders set estimates for user experience, which can comprise several flows. Identify the flows that are critical from the perspective of the user.

They take into consideration, for example, how an hour of downtime for a flow during regular business hours can result in a loss of $X in monthly revenue. This dollar amount is compared to the estimated cost of designing and operating that flow. Decision makers must discuss the tolerance of external budgetary influences to decide if the extra costs and management burden for reliability is worth the risk of losing revenue. Or is the SLO too low to maintain the reputation.

For the workload architect, SLOs can be the driver for many technical decisions. For example,

- Serve as a critical input into architectural decisions when you consider additional dependencies.
- Provide a near real-time view and shared understanding of the health of a workload to enable objective discussions. Also help the workload team prioritize efforts on reliability, new feature development, and other task.
- Act as a primary signal for deployment operations, driving automated rollback if issues occur, and providing validation that changes achieved the expected user experience improvement.
- Speed up redmediation by focusing objectives, drive automated notification of issues to users, and build trust between teams of the organization, who are responsible for the SLO.
    
> [!CAUTION]
>
> It's important to distinguish between Service Level Agreements (SLAs) and Service Level Objectives (SLOs). Although SLAs and SLOs may refer to similar information, their intent is different. An SLA is a formal contract between an organization and its customers that has financial and legal implications if the organization fails to deliver on the promise. SLOs are used to evaluate whether SLA terms are met or violated by using metrics, such as uptime commitments. 
>
> If SLOs are not met, organizations must react quickly to mitigate the possible outcomes of the failed SLA. Therefore, the workload's SLO must always be higher than its declared SLA to avoid negative consequences.
>
> TODO: To make sure you can meet the SLO target, review the Microsoft SLAs for each component.

##### Assess the impact of Microsoft SLAs

Microsoft Service Level Agreement (SLA) provides insight into availability of areas that Microsoft commits to. SLAs don't guarantee an offering as a whole. When evaluating SLAs, have a good understanding of the coverage provided around the published percentile.

For instance, consider Azure App Service Web Apps. It's considered available when it returns a 200 OK status in a given use case. Within that specific context and timeframe, it doesn't guarantee availability of features such as Easy Auth. Similarly, slot switching behavior isn't covered by the SLA. Areas that aren't mentioned explicitly in the agreement should be considered as best-effort by the platform. 

So, if your workload relies on deployment slots, you cannot derive your SLO solely from the Azure App Services SLA. As a workload team, it becomes necessary to hedge and predict the uptime availability. However, this prediction can be somewhat uncertain, which is why closely tying your SLO to the platform SLA can be problematic.

Let's study another example. What does it mean for Azure Front Door to be available 99.99%? To achieve this, your design must adhere to specific criteria published in the agreement. Your backend must include storage, A GET operation should retrieve a file of at least 50KB in size, and you need agents deployed across multiple spots and at least five geographically diverse locations. This narrow use case of Front Door doesn’t guarantee features like caching, routing rules, or web application firewall. These aspects fall outside the scope of the SLA.

##### Common SLOs

Every SLO targets a specific quality criteria. Consider these common SLOs for reliability. This list isn't exhaustive. Add SLOs based on your business requirements. 

- **Success rate** measures success of requests and that not every request returns an error.
- **Latency** measures the time elapsed between when a request for an operation is initiated and returned to the caller so that it can make use of the returned information.
- **Capacity** measures the number of throttling-based responses to caller when capacity is available, and also when there is no capacity available.
- **Availability** measures uptime from the perspective of users. 
- **Throughput** measures a minimum data transfer rate over a time window, expressed in kilobytes per second.

##### Factors that influence SLOs

Have a good understanding of the scenarios and tolerances for your workload on Azure. Both Azure services and application components have a significant impact on the workload SLO. The overall SLO should be derived by combining the responses from this table. These questions are meant to serve as examples for evaulating the utility of the workload component: 

|Component characteristics|User interaction|Nuanced factors|
|---|---|---|
|<br>▪ Does it expose **request/response API?**<br>▪ Does it have **query APIs**?<br>▪ Is it a **compute** component?<br>▪ Is it a job processing component?|<br>▪ **Control/management plane access** for public-facing Azure services.<br>▪ **Data plane access** for instance, CRUD (create, read, update, delete) operations.|<br>▪ Does your **release process** involve downtime?<br>▪ What's the likelihood of **introducing bugs**? If the workload integrates with other systems, there may be integration bugs that you need to consider.<br>▪ How do **routine operations**, for instance, patching, impact the availability target? Have you factored in third-party dependencies?<br>▪ Is your **staffing** big enough to support 24/7 emergency and emergency backup on call rotation?<br>▪ Does the application have **noisy neighbors** (outside your scope of control) that could potentially cause disruptions?|

#### Measure targets

SLOs must be **measurable** and **measured within a observability window**. 

If an SLO can be measured in metrics collected from the system, the calculation should be automated. However, if the contributing factors are nuanced, it may be harder to automate. 

SLOs are commonly expressed as a percentage, such as 99.9, 99.95, or 99.995 for mission-critical workloads. However, SLOs can also be a statement. Combine both approaches to arrive at a numerical value that can be calculated through metrics emitted by the system and also cover other nuanced factors.

**SLO is a correlation or composite of measurable indicators** to determine what's acceptable, and otherwise. The team should be clear on what is measured, how it's measured, and from what perspective it's measured. Using **Service Level Indicators (SLIs)** is a common way to standardize targets from a set of metrics. 

##### Correlate indicators

SLIs represent a quantitative measurement of an aspect of a workload component, that rolls up to the SLO target. It's typically a metric that can be collected from the component whether that's part of the platform or application. **Different type components** emit SLIs that are relevant to them. When deciding on which SLIs to include, consider the factors that influence SLO.

For instance, if you want to calculate the SLO of a flow that requires the user to interact with a component through response/request API, the SLIs would require measuring server latency and time to process requests. On the other hand, throughput and error rates aren't applicable to continuous compute environments such as VMs, VMSS, or Azure Batch. 

**Type of interaction** is a significant contributing factor. Control plane access should take into consideration the error rate and latency indicators for synchronous API responses and for long-running operations, such as resource creation, deletion, and so on. Data plane access depends the set of data plane APIs for interacting with your service, each with SLO targets. 

An indicator isn't useful unless you **set a threshold**. A good SLI helps you identify when an SLO is at risk of being breached. They are also represented in percentiles.


> For an illustrative example about how to define and measure SLO and SLIs, see the [Example](#example) section.

### Availability metrics

#### SLOs and SLAs

Availability metrics correlate to SLOs, which you use to define SLAs. The workload SLO determines how much downtime is tolerable in a given period, for example, less than 1 hour per month. To make sure you can meet the SLO target, review the Microsoft SLAs for each component. Pay attention to how much redundancy you need to meet high SLAs. For example, Microsoft guarantees higher SLAs for multi-region deployments of Azure Cosmos DB than it guarantees for single-region deployments.

> [!NOTE]
> Azure SLAs don't always cover all aspects of a service. For example, Azure Application Gateway has an availability SLA, but the Azure Web Application Firewall functionality provides no guarantee to stop malicious traffic from passing through. Consider this limitation when you develop your SLAs and SLOs.

After you gather the SLAs for the individual workload components, calculate a composite SLA. The composite SLA should match the workload's target SLO. Calculating a composite SLA involves several factors, depending on your architecture design. Consider the following examples.

> [!NOTE]
> The SLA values in the following examples are **hypothetical** and are for **demonstration purposes only.** They **aren't intended to represent current values** supported by Microsoft.

Composite SLAs involve multiple services that support an application, with differing levels of availability. For example, consider an Azure App Service web app that writes to Azure SQL Database. Hypothetically, these SLAs might be:

- App Service web apps = 99.95 percent
- SQL Database = 99.99 percent

What's the maximum downtime you can expect for this application? If either service fails, the whole application fails. The probability of each service failing is independent, so the composite SLA for this application is 99.95 percent × 99.99 percent = 99.94 percent. That value is lower than the individual SLAs. This conclusion is unsurprising because an application that relies on multiple services has more potential failure points.

You can improve the composite SLA by creating independent fallback paths. For example, if SQL Database is unavailable, put transactions into a queue to be processed later:

:::image type="content" source="media/metrics/independent-fallback-paths.png" alt-text="Diagram that shows fallback paths. The web app box shows arrows branching to SQL Database or to a queue." border="false" lightbox="media/metrics/independent-fallback-paths.png":::

In this design, the application is still available even if it can't connect to the database. However, it fails if the database and the queue fail at the same time. The expected percentage of time for a simultaneous failure is 0.0001 × 0.001, so here's the composite SLA for this combined path:

Database or queue = 1.0 − (0.0001 × 0.001) = 99.99999 percent

The total composite SLA:

Web app and (database or queue) = 99.95 percent × 99.99999 percent = \~99.95 percent

This approach poses tradeoffs:

- The application logic is more complex.
- You pay for the queue.
- You need to consider data consistency issues.

For multi-region deployments, the composite SLA is calculated as follows:

- *N* is the composite SLA for the application that's deployed in one region.

- *R* is the number of regions where the application is deployed.

The expected chance that the application fails in all regions at the same time is ((1 − N) \^ R). For example, if the hypothetical single-region SLA is 99.95 percent:

- The combined SLA for two regions = (1 − (1 − 0.9995) \^ 2) = 99.999975 percent

- The combined SLA for four regions = (1 − (1 − 0.9995) \^ 4) = 99.999999 percent

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

### Recovery metrics

Recovery targets correspond to RTO, RPO, MTTR, and MTBF metrics. In contrast to availability targets, recovery targets for these measurements don't depend heavily on Microsoft SLAs. Microsoft publishes RTO and RPO guarantees only for some products, like [SQL Database](/azure/azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview).

Definitions for realistic recovery targets rely on your [failure mode analysis](failure-mode-analysis.md) and your plans and testing for business continuity and [disaster recovery](disaster-recovery.md). Before you finish this work, discuss aspirational targets with stakeholders and ensure that your architecture design supports the recovery targets to the best of your understanding. Clearly communicate to stakeholders that any flows or entire workloads that aren't thoroughly tested for recovery metrics shouldn't have guaranteed SLAs. Make sure that stakeholders understand that recovery targets can change over time as workloads are updated. The workload can become more complex as customers are added or as you adopt new technologies to improve the customer experience. These changes can increase or decrease your recovery metrics.

> [!NOTE]
> MTBF can be challenging to define and guarantee. Platforms as a service (PaaS) or software as a service (SaaS) can fail and recover without any notification from the cloud provider, and the process can be completely transparent to you or your customers. If you define targets for this metric, cover only components that are under your control.

As you define recovery targets, define thresholds for initiating a recovery. For example, if a web node is unavailable for more than 5 minutes, a new node is automatically added to the pool. Define thresholds for all components, considering what recovery for a specific component involves, including the effect on other components and dependencies. Your thresholds should also account for [transient faults](handle-transient-faults.md) to ensure that you don't start recovery actions too quickly. Document and share with the stakeholders the potential risks of recovery operations, like data loss or session interruptions for customers.

### Monitor and visualize the targets

Use the data you gathered for your reliability targets to build your health model for each workload and associated critical flows. A health model defines *healthy*, *degraded*, and *unhealthy* states for the flows and workloads. When state changes, the model should alert the responsible parties. For detailed design considerations and recommendations, see [Health modeling guidance](../cross-cutting-guides/health-modeling.md).

To keep your operations teams and workload stakeholders informed, create visualization that reflect the real-time status and overall trends of the workload health model. Discuss visualization solutions with the stakeholders to ensure that you deliver the information that they value and that's easy to consume. They might also want to see generated reports weekly, monthly, or quarterly.

## Azure facilitation

Azure SLAs provide the Microsoft commitments for uptime and connectivity. Different services have different SLAs, and sometimes SKUs within a service have different SLAs. For more information, see [Service-level agreements for online services](https://www.microsoft.com/licensing/docs/view/Service-Level-Agreements-SLA-for-Online-Services).

The Azure SLA includes procedures for obtaining a service credit if the SLA isn't met, along with definitions of availability for each service. That aspect of the SLA acts as an enforcement policy.

Explore the [dashboards](/azure/azure-monitor/visualize/tutorial-logs-dashboards) provided by Azure Monitor for your visualization system.


## Example

Contoso Ticketing is designing a new mobile experience for their event ticketing system. Here's the high-level architecture:

//TODO: fix the actual image

![alt text](./media/metrics/example-architecture-targets.png)

##### Components

Here are some components that have been chose to illustrate the concept of SLO definition. Notice there are other components in this architecture that haven't been included for brevity. 

- **Azure Front Door** is the single point of entry that exposes an API that's used by end users to send requests.
- **Azure Container Apps** enviroment is owned by the workload team and runs business logic for the  application. 
- **SQL Managed Instance** is owned and managed by another team and is a critical dependency of the workload. 

The API team has defined an initial service-level objective (SLO) target for critical flows in the application. By adopting the direction given in [Factors that influence SLOs](#factors-that-influence-slos), they aim to define objectives that cover the core functionality without overly emphasizing ancillary features. They decide to measure the health of three critical user flows, which involve all core cloud functionality and execute code across deployments. However, these flows do not cover 100% of the code or data access. Here are the influencing factors.

#### Azure reliability

Azure's financial commitment SLA serves as a proxy to assess platform reliability.

#### Application code

Includes defects, scale issues, and other code-related considerations.

#### Resource and application configuration

Auto-scaling, SKU selection, NSG rules, and other configuration aspects.

#### Operations

Deployment processes, TLS certificate rotation, and routine operations.

#### External dependencies

Reliability considerations for SQL Managed Instance.

#### Maintenance window. 

Tolerated downtime during scheduled maintenance and downtime windows.



## Organizational alignment

Cloud Adoption Framework provides guidance for recommendations for SLOs and SLIs related to monitoring across the organization.

For more information, see [Cloud monitoring SLOs](/azure/cloud-adoption-framework/manage/monitor/service-level-objectives#how-do-you-define-slis).

## Related links

- Well-Architected Framework mission-critical guidance for health modeling: [Health modeling and observability of mission-critical workloads on Azure](/azure/well-architected/mission-critical/mission-critical-health-modeling)

## Reliability checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Reliability checklist](checklist.md)
