---
title: Architecture strategies for defining reliability targets
description: Learn how to define reliability targets for your critical workloads, and discover key design strategies that prioritize operations.
author: claytonsiemens77
ms.author: csiemens
ms.date: 08/16/2024
ms.topic: concept-article
---

# Architecture strategies for defining reliability targets

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

|**RE:04**| **Define reliability and recovery targets** for your workload. Use the targets to inform your design and as the foundation of your health model. |
|---|---|

This guide describes the recommendations for defining availability and recovery target metrics for critical workloads and flows. You should derive reliability targets from workshop exercises with business stakeholders. Then refine those targets by monitoring and testing your workloads.

Set realistic expectations with your internal stakeholders about workload reliability. Then they can use contractual agreements to communicate those expectations to customers. Realistic expectations also help stakeholders understand and support your architectural design decisions and know that you're designing to optimally meet the targets that you agree on.

Consider using the following metrics to quantify your business requirements.

|Term  |Definition  |
|---------|---------|
|Service-level objective (SLO)    | A measure of the performance and reliability of a workload or application. An SLO is a specific, measurable target that you set for particular customer interactions. It's a target that you set for your workload or application based on the quality of service that your customers expect to receive.        |
|Service-level indicator (SLI)     | A quantitative measurement of a particular aspect of a service's performance. You can use an SLI to measure your workload's compliance with an SLO.         |
|Service-level agreement (SLA)     | A contractual agreement between the service provider and the service customer. Failure to meet the agreement might have financial consequences for the service provider.        |
|Mean time to recover (MTTR)     | The time taken to restore a component after a failure is detected.        |
|Mean time between failure (MTBF)     |The duration for which the workload can perform the expected function without interruption until it fails.         |
|Recovery time objective (RTO)     | The maximum acceptable time that an application can be unavailable after an incident.        |
|Recovery point objective (RPO)     | The maximum acceptable duration of data loss during an incident.        |


**Reliability targets represent the desired quality goal of a workload,** as promised to its users and the business stakeholders. That goal includes both availability and recoverability of the workload. Keep in mind that reliability targets differ from performance targets, but you should include performance targets in reliability targets. Consider the following reliability targets:

- **Availability targets** define the quality standards for a system to remain accessible and functional. If it doesn't meet these standards, the system is considered unreliable. Use SLOs to help check whether your system meets these standards. Business and technical stakeholders collaborate to set realistic SLOs and consider factors like comparative analysis, user experience, and the workload profile.

- **Correctness targets** ensure that the workload properly performs its functions with consistent quality. To measure correctness, quantify the workload's indicators so that you can combine them into a unified, objective score.

- **Recovery targets** correspond to RTO, RPO, MTTR, and MTBF metrics, which quantify the effectiveness of your plans and testing for business continuity and disaster recovery. 

To set reliability targets, business stakeholders define broad requirements. Then, technical experts assess the current state of the workload and work towards achieving and maintaining targets through monitoring and alerts. Both parties must agree on realistic targets.

[Identify and score user and system flows](identify-flows.md) based on their importance to your business requirements. Use these scores to guide the design, review, testing, and incident management of your workload. Set reliability targets for these flows, and understand that failing to meet those targets can significantly affect your business.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: You might have a gap between the technical limits of your system and its business impact, like throughput versus transactions per second. Bridging this gap can be tough. Aim for a practical and cost-effective solution instead of overengineering.

## Set availability objectives

The overall SLO of a workload reflects the **holistic quality, including all its dependencies.** A mature declaration of the SLO should indicate the overall business target for that workload, not just a composite of those dependencies. For example, if customers expect 99.99% availability, the overall SLO should aim for that goal, even if one part only achieves 99.80%.

Stakeholders evaluate the customer experience and consider how downtime affects revenue. They compare this loss to the cost of designing and operating the business flow. Decision makers then decide if they should spend more money on reliability to avoid revenue loss and maintain their reputation.

Workload owners **use financial goals to determine objectives.** Business requirements map to measurable metrics. The goal is to identify a set of factors that influence the quality of the customer experience.

Workload architects **make many technical decisions based on SLOs.** SLOs can:

- Serve as critical input into architectural decisions when you consider other dependencies.

- Provide a near real-time view and shared understanding of the health of a workload to enable objective discussions. They also help the workload team prioritize efforts to improve reliability and develop new features.
- Act as a primary signal for deployment operations, which drives automated rollback if problems occur and provides validation that the changes achieve the expected improvements to the customer experience.
- Speed up remediation and recovery by focusing on objectives, drive automated notification of problems to customers, and build trust between teams in your organization.
    
> [!IMPORTANT]
>
> You must know the difference between SLAs and SLOs. Although SLAs and SLOs might use or even present similar data, their intent is different. An SLA is a formal contract between an organization and its customers, and it has direct financial and legal implications if the organization fails to deliver on its promise. Organizations use SLOs to evaluate whether the potential downtime is within the tolerable limits.
>
> SLOs and SLAs share a business relationship and should be independently controlled. If the SLA serves as a business tactic, the organization might intentionally set it to a high value based on the business owner's goals. Conversely, SLOs can be higher. Consider mission-critical workloads as an example. This workload class can't afford long downtimes because the effects, including financial loss and even threats to human safety, are significant. Therefore, SLOs typically target 99.999% uptime, commonly referred to as the *five nines*. If SLOs don't meet those targets, organizations must react quickly to mitigate failures and prevent the outcomes of a failed SLA.
>
> The [example](#example) in this article sets a high SLA to support business goals. 
>
> Cloud platform and technology providers publish SLAs on their offerings. You should consider the SLAs as part of the SLO calculation, but you shouldn't use them as is without understanding the SLA's scope of coverage. For more information, see [Assess the impact of Microsoft SLAs](#assess-the-impact-of-microsoft-slas).

### Consider common SLOs and influencing factors

Every SLO targets a specific quality criteria. Consider these common SLOs for reliability. This list isn't exhaustive. Add SLOs based on your business requirements. 

- **Success rate** measures the success of requests and processes relative to those that return an error or fail their task.

- **Latency** measures the time between when a request for an operation is initiated and when the result is available or the process is complete.
- **Capacity** measures concurrent usage, such as the number of throttling-based responses.
- **Availability** measures uptime from the perspective of customers.
- **Throughput** measures the minimum data transfer rate during a certain amount of time. Throughput is measured as a data-rate unit, such as transactions per second (TPS) or requests per second (RPS).

Understand the scenarios and tolerances for your workload on Azure. Both Azure services and application components affect the workload SLO. Combine responses from the following table to derive the overall SLO. Use these questions as examples to evaluate the workload component's utility.

|Component characteristics|Customer interaction|Other factors|
|---|---|---|
|- Does it expose **request or response APIs?**<br> - Does it have **query APIs**?<br> - Is it a **compute** component?<br> - Is it a **job processing** component?|- **Control plane and management plane access** for public-facing Azure services.<br> - **Data plane access**, for example create, read, update, and delete (CRUD) operations.|- Does your **release process** involve downtime?<br> - What's the likelihood of **introducing bugs**? If the workload integrates with other systems, you might need to consider integration bugs.<br> - How do **routine operations** like patching affect the availability target? Have you factored in partner dependencies?<br> - Is your **staffing** sufficient to support constant emergency and emergency backup on-call rotation?<br> - Does the application have **noisy neighbors** outside of your scope of control that can potentially cause disruptions?|

### Determine the SLO scope

You can set SLOs at various levels, such as for each application, workload, or a specific flow, in your system. Set level-specific SLOs so that you can customize SLOs based on each component's importance.

In software as a service (SaaS) solutions, measure SLOs per customer to optimize each customer's experience. Customers might have different infrastructure resources in their segments. For such cases, a system-wide SLO that aggregates all resources across customer segments might not make sense. Instead, measure SLOs that align with each customer's specific context. For more information, see [Tenancy models for a multitenant solution](/azure/architecture/guide/multitenant/considerations/tenancy-models).

### Define composite SLO targets

SLOs must be **measurable** and **measured within an observability window**.

SLOs are often percentages like 99.90%, but they can also be statements. Use both methods to get a numerical value that includes all factors.

An SLO consists of measurable SLIs that define acceptable factors. SLIs are metrics with a set threshold that can be alerted. You can collect them from a platform or an application. Different components emit relevant SLIs. When you choose SLIs, consider factors that influence the SLO.

For example, to calculate the SLO for a flow that uses a response and request API, measure server latency and request-processing time. Throughput and error rates don't apply to continuous compute environments like virtual machines (VMs), scale sets, or Azure Batch.

For control plane access, consider error rates and latency for API responses and long-running operations like resource creation. Data plane access depends on the APIs used, each with their own SLO targets.

A good SLI shows when you might breach an SLO. It's usually measured in percentiles. Here are some commonly used percentiles and the estimated time of noncompliance to the expected availability. 

|Objective  |Noncompliance per week  |Noncompliance per month  |Noncompliance per year |
|---------|---------|---------|---------|
|99%      | 1.68 hours        |  7.20 hours       | 3.65 days        |
|99.90%      |  10.10 minutes       | 43.20 minutes         | 8.76 hours        |
|99.95%     | 5 minutes         | 21.60 minutes        | 4.38 hours        |
|99.99%      | 1.01 minutes        |  4.32 minutes       |  52.56 minutes       |
|99.999%     |  6 seconds        | 25.90 seconds         |  5.26 minutes       |


> [!IMPORTANT]
>
> A composite SLO value is a product distribution of the contributing factors.
>
> An example composite SLO is 99.95% × 99.99999% = \~99.95%.
> 
> When you create composite SLOs for different flows, consider their varying criticality and relevance. Flows might have components that you deem as noncritical and omit from your calculations. You can justify their omission based on whether their brief unavailability affects the customer's experience. In some cases, a component might not be relevant to the use case that you consider for the SLO. You can omit these components from the calculation, too.
>
> The same principle applies to operations. Certain operations might introduce risks or affect the SLO, and others are insignificant. The decision should be explicit and built on consensus.
>
> For an illustrative example of how to define and measure SLOs and SLIs, see the [Example](#example) section.


## Assess the impact of Microsoft SLAs

A Microsoft SLA provides insight into the availability of areas that Microsoft commits to. **SLAs don't guarantee an offering as a whole**. When you evaluate SLAs, have a good understanding of the coverage that's provided around the published percentile.

Consider Web Apps, a feature of Azure App Service. The feature is considered available when it returns a *200 OK* status in a given use case. Within that specific context and timeframe, it doesn't cover a financially backed guarantee on the availability of features like Easy Auth or slot switching. You should consider areas that aren't mentioned explicitly in the agreement as available by the platform's best effort.

So, if your workload relies on deployment slots, you can't derive your SLO solely from the App Service SLA. As a workload team, you need to hedge and predict the uptime availability. However, this prediction can be uncertain, which is why closely tying your SLO to the platform's SLA can be problematic.

Consider another example. If Azure Front Door has 99.99% availability, your design must adhere to specific criteria that's published in the agreement. For example, your back end must include storage, you need a `GET` operation that can retrieve a file of at least 50 KB in size, and you need to deploy agents across multiple places in at least five geographically diverse locations. This narrow use case of Azure Front Door doesn't guarantee features like caching, routing rules, or a web application firewall. These aspects fall outside the scope of the SLA.

## Implement multiregion targets

From a reliability perspective, multiregion deployment is an implementation of the principle of redundancy. The goal is to mitigate the risk of a regional outage or degraded performance. This strategy, when properly designed, can improve SLOs because it adds a secondary region for failover purposes.

There are two main use cases:

- High availability pattern, in which you distribute a load across regions for more capacity. High availability doesn't restrict workload users to a region, and the entire system's performance contributes to the SLO.

- Bulkhead pattern, in which you restrict customers to specific regions to segment them. In such cases, treat multiregion deployments as separate deployments, or *stamps*, in each region. Measure the health of each stamp separately, with the SLIs that are appropriate to your workload. Consider your overall workload's SLO based on the health of each stamp. If you can fail over between stamps, then your overall workload SLO is higher because a failure in one stamp is recoverable through a failover to another stamp.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Determine whether the risk reduction is worth the added complexity. Multiregion targets also introduce operational complexities, such as coordinating deployments, ensuring data consistency, and handling latency. Those operations are significant during recovery. Your team should weigh these complexities against the increased resiliency.

Pay attention to how much redundancy you need to meet high SLOs. For example, Microsoft guarantees higher SLAs for multiregion deployments of Azure Cosmos DB than it guarantees for single-region deployments.

## Define recovery metrics

Definitions for realistic recovery targets, like RTO, RPO, MTTR, and MTBF metrics, rely on your [failure mode analysis](failure-mode-analysis.md) and your plans and testing for business continuity and [disaster recovery](disaster-recovery.md). When you define these targets, factor in the platform-provided recovery guarantees. Microsoft publishes RTO and RPO guarantees only for some products, like [Azure SQL Database](/azure/azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview).

Before you finish this work, discuss aspirational targets with stakeholders, and ensure that your architecture design supports the recovery targets to the best of your understanding. Clearly communicate to stakeholders that any flows or entire workloads that aren't thoroughly tested for recovery metrics shouldn't have guaranteed SLAs. Make sure that stakeholders understand that recovery targets can change over time as workloads are updated. The workload can become more complex as you add customers or as you adopt new technologies to improve the customer experience. These changes can increase or decrease your recovery metrics.

> [!NOTE]
> MTBF can be challenging to define and guarantee. Platform as a service (PaaS) or SaaS models can fail and recover without any notification from the cloud provider, and the process can be completely transparent to you or your customers. If you define targets for this metric, cover only components that are under your control.

When you define recovery targets, define thresholds to initiate a recovery. For example, if a web node is unavailable for more than five minutes, automatically add a new node to the pool. Define thresholds for all components, and consider what the recovery for a specific component involves, including the effect on other components and dependencies. Your thresholds should also account for [transient faults](handle-transient-faults.md) to ensure that you don't start recovery actions too quickly. Document and share with the stakeholders the potential risks, like data loss or session interruptions for customers, of recovery operations.

## Monitor and visualize the targets

Use the data that you gather for your reliability targets to build your health model for each workload and the associated critical flows. A health model defines *healthy*, *degraded*, and *unhealthy* states for the flows and workloads. When the state changes, the model should alert the responsible parties. For detailed design considerations and recommendations, see [Health modeling guidance](../design-guides/health-modeling.md).

To keep your operations teams and workload stakeholders informed, create a visualization that reflects the real-time status and overall trends of the workload health model. Discuss visualization solutions with the stakeholders to ensure that you deliver information that they value and that's easy to consume. They might also want to see generated reports weekly, monthly, or quarterly.

## Azure facilitation

Azure SLAs provide the Microsoft commitments for uptime and connectivity. Different services have different SLAs, and sometimes products within a service have different SLAs. For more information, see [SLAs for online services](https://www.microsoft.com/licensing/docs/view/Service-Level-Agreements-SLA-for-Online-Services).

The Azure SLA includes procedures for obtaining a service credit if your workload doesn't meet the SLA, along with definitions of availability for each service. That aspect of the SLA acts as an enforcement policy.

Explore the Azure Monitor [dashboards](/azure/azure-monitor/visualize/tutorial-logs-dashboards) for your visualization system.

## Example

Contoso, Ltd. is designing a new mobile experience for their event ticketing system. Here's the high-level architecture.

:::image type="complex" source="./media/metrics/example-architecture-targets.svg" alt-text="Architecture diagram of a mobile ticketing system that's hosted in Azure Container Apps." border="false" lightbox="./media/metrics/example-architecture-targets.svg":::
   An architecture diagram with various Azure components. A mobile ticket scanning device connects to Azure Front Door, which has Transport Layer Security (TLS), web application firewall (WAF) policies, and rules. Azure Front Door connects to Container Apps through an Azure Private Link connection. Build agent VMs connect to Azure DevOps, Container Apps, and private endpoints. SQL Managed Instance is in a box that's labeled *External team*. A workload observability platform box contains a workspace in Azure Monitor Logs, Application Insights, Azure Managed Grafana, alerts, and diagnostics settings. The architecture also shows the icons for Azure DNS, Microsoft Entra ID, Azure Key Vault, and managed identities.
:::image-end:::

*The Grafana logo is a trademark of its respective company. No endorsement is implied by the use of this mark.*

### Components

Here are some components that illustrate the concept of SLO definition. There are components in this architecture that aren't included in the following list. For example, even though Key Vault is part of the critical request flow, it isn't part of the response use case. If Key Vault is unavailable, the application continues to function by using secrets that are loaded during startup. However, if the application needs to scale, Key Vault availability becomes critical because new nodes need to be loaded with secrets. In this example, scaling operations aren't considered. Other components are omitted for brevity.

- **Azure Front Door** is the single point of entry that exposes an API that customers use to send requests.

- **Azure Container Apps** is the environment that the workload team owns and uses to run business logic for the application.
- **SQL Managed Instance** is owned and managed by another team and is a critical dependency of the workload.
- **Azure Private Link** provides private connectivity between Azure Front Door and Container Apps deployments. SQL Managed Instance is also exposed to the application through a private endpoint.

In this architecture, the API team defines an initial SLO target for critical flows in the application. They adopt the strategy that's described in [Factors that influence SLOs](#consider-common-slos-and-influencing-factors). They aim to define objectives that cover the core functionality without overly emphasizing supplemental features. They measure the health of three critical user flows, which involve all core cloud functionalities and execute code across deployments. However, these flows don't cover all of the code or data access. Here are the influencing factors.

### Calculate a composite SLO

- **Azure availability SLO:** The financial commitment SLA for Azure serves as a proxy to assess platform reliability.

    |Azure component|Applicable SLA coverage |Not covered by SLA|Adjusted SLO|
    |--|--|--|--|
    |Azure Front Door| 99.99% for successful HTTP `GET` operations|Caching, rules engine|99.98%|
    |Container Apps| 99.95% based on deployed apps that are reachable by the built-in ingress| Autoscaling, token store capabilities|99.95%|
    |SQL Managed Instance|99.99% based on the connection to the SQL Server instance| Performance, data retention|99.80%|
    |Private Link|99.99% based on whole minutes when the private endpoint network didn't accept traffic or when traffic didn't flow between the endpoint and the Private Link service|Individual failures lasting less than one minute| 99.99%|

    The adjustment is based on several factors that depend on the workload team's promise to their objectives. One factor might be confidence in the platform's capability based on prior experience. For example, for Container Apps and Private Link, the team feels comfortable taking the SLA value as is.

    There are also nuanced factors. For example, the team lowers the SLO value for SQL Managed Instance to 99.80% to account for potential failures in their data operations, such as schema changes and taking backups.

    The team sets the composite SLO by calculating the impact of individual, adjusted SLO values. This value is 99.72%.

    There are other contributing factors. The architecture relies on Azure networking components like virtual networks and network security groups (NSGs) that don't have a published SLA. The workload team decides to consider those factors with 99.99% availability for each component.

    > A composite SLO based on predicted platform availability: 99.68% per month.

- **Application code SLO:** The team acknowledges that bugs in their application code or stored procedures can affect system availability, and they allocate one hour of monthly downtime to account for code-related errors.

    They use common [downtime percentiles](#define-composite-slo-targets) to estimate the SLO for individual factors like code defects, scaling problems, and other code-related considerations.

    > A composite SLO based on code and data availability: 99.86% per month.

- **Resource and application configuration SLO:** The team recognizes that cloud resources and application code must be properly configured. This target includes setting up autoscaling rules, deploying NSG rules, and selecting the correct size of SKUs. To account for configuration errors, they budget 10 minutes of monthly downtime, which is about 99.98% availability.

    > A composite SLO based on configuration availability: 99.95% per month.

- **Operations SLO:** The workload team develops good DevOps culture by following Well-Architected Framework principles for Operational Excellence. They deploy cloud resources, configuration, and code every sprint.

    The team considers deployments to be a risk because they can destabilize a running system. There might be errors as a result of TLS certificate updates, DNS changes, or tool errors. The team also considers potential downtime caused by emergency fixes. They budget a total of 20 minutes of monthly downtime, which is approximately 99.95% availability.

    Maintenance windows are designated time periods during which system maintenance or updates occur. The API is mostly unused for approximately four hours each day. To reduce the risk of unavailability, the team can schedule maintenance tasks during those less-active hours. This approach leads to a higher SLO, but the team decides not to include the maintenance window as part of their SLO. 

    > A composite SLO based on operations availability: 99.95% per month. 

- **External dependencies SLO:** The team considers SQL Managed Instance as the primary dependency, which already has a 99.80% availability factored into the overall platform availability. No other external dependencies are considered.

    > A composite SLO based on external dependencies: Not applicable.

### Determine the overall composite SLO result

**The overall composite SLO target is set at 99.45%, which is equivalent to approximately four hours of downtime per month.**

To meet the SLO target of only four hours of unavailability per month, the workload team establishes an on-call rotation. Both customer support and synthetic transaction monitoring can invoke on-call site reliability engineering (SRE) support to promptly start recovery steps to address SLO problems.

### Set the workload SLA

**The SLA for the workload is 99.90% availability per month.**

The workload team's legal and finance departments set the SLA for the workload at 99.90% availability per month, which exceeds the SLO target of 99.45%. They make this decision after they analyze financial payouts versus projected customer growth based on an attractive SLA. The SLA covers two core user flows and includes performance considerations, not just availability. It's a calculated risk taken by the business team to benefit the business, and the engineering team is aware of the commitment.

### Set the correctness SLO

The application's core user flows must be available and usably, or even competitively, responsive. The team sets a response time SLO specifically for the API, excluding client processing time and internet network traversal. They evaluate this SLO only during periods of availability. They choose the 75th percentile as both the SLO target and the performance measurement, which captures the typical customer experience and excludes worst-case scenarios. 

## Related links

[Health modeling and observability of mission-critical workloads on Azure](/azure/well-architected/mission-critical/mission-critical-health-modeling)

## Reliability checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Reliability checklist](checklist.md)
