---
title: Failure mode analysis
description: Learn about failure mode analysis.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---
Best practices for Failure Mode Analysis: RE:02  
This guide describes the best practices for performing failure mode analysis (FMA) for your workload. FMA is the practice of identifying potential points of failure within your workload and associated flows and planning mitigation actions accordingly. At each step of the flow, you will have identified the blast radius of multiple failure types, helping you design your new workload or refactor an existing workload to minimize the widespread impact of failures.

A key tenet of FMA is that failures will happen no matter the number of layers of resiliency you have applied and more complex environments are at risk to more types of failures. Given this reality, FMA allows you to design your workload to withstand most types of failures and recover gracefully when impact occurs.

Skipping FMA altogether or performing an incomplete analysis puts your workload at risk of unpredicted behavior and potential outages caused by suboptimal design.

# Definitions

| **Term** | **Definition**|
|----------|---------------|
| Failure mode | A particular type of issue that can cause one or more workload component to be degraded or severely impacted to the point of being unavailable |
| Mitigation | The activities that you have identified to address issues either proactively or reactively |
| Detection | Your infrastructure, data, and app monitoring and alerting processes and procedures |

# Key design strategies

## Prerequisites

It is assumed that you have already reviewed and implemented the recommendations as detailed in (link to RE01):

1.  Identified and prioritized user and system flows based on criticality.

The data you have gathered and artifacts that you have created coming out of your work in these areas will provide you with a concrete description of your data paths involved throughout the flows. To be successful in your FMA work, accuracy and thoroughness in your artifacts is critical.

## FMA Approach 

After you have determined the critical flows, you can plan the components that they require. Next, you can follow each flow step-by-step to identify dependencies including 3^rd^ party services, potential points of failure, and plan your mitigation strategies.

### Decomposing the workload

As you move from ideation to design, you will need to identify component types that will be required to support your workload. Typically, you will need to plan for ingress control, networking, compute, data, storage, supporting services (like authentication, messaging, and secret/key management) and egress control, but your workload will determine the necessary components that you'll need to plan for. At this stage in your design work, you may not have decided on specific technologies that you will deploy, so your design may look like the following example.

![](media=./media/image1.png){width="4.739583333333333in" height="5.0in"}

After you have created your initial architecture design, you can overlay your flows to identify the discreet components involved in those flows and build lists and/or workflow diagrams that describe the flows and their components. Using the criticality definitions you have assigned to the flows, you can then understand the criticality of the components involved and consider the effect of a malfunction of a given component on your flows.

### 

### Identifying dependencies

Identifying your workload dependencies is essential to your single-point-of-failure analysis. The exercise of decomposing your workload and overlaying flows will give you insights into dependencies internal to the workload and external to it.

Internal dependencies describe components in the workload scope that are required for the workload to function. External dependencies capture required components outside the scope of the workload, such as another application or third-party service. Examples of typical external dependencies include authentication solutions like Azure Active Directory, and cloud connectivity solutions like Azure ExpressRoute. Typical internal dependencies include APIs or secret/key management solutions like Azure KeyVault. Be sure to capture the reliability data for these dependencies, like availability SLAs and scaling limits.

Document the dependencies you have identified and include them in your flow documentation artifacts.

## Failure points

As you walk through your workload's critical flows, you will consider each component involved and determine how that component (and its dependencies) may be affected by a given failure mode. Remember that there are many failure modes that you should consider when planning for resilience and recovery, and that any one component can be affected by more than one failure mode at any given time. These failure modes include:

-   Regional outage (an entire Azure region is unavailable)

-   Availability Zone outage (an Azure availability zone is unavailable)

-   Service outage (one or more Azure services is unavailable)

-   DDoS or other malicious attack

-   App or component misconfiguration

-   Operator error

-   Planned maintenance outage

-   Component overload

The analysis should always be in the context of the flow you are attempting to analyze, so be sure to document the impact on the user and expected result of that flow. For example, if you have an e-commerce application and you are analyzing your end-user flow, the impact of a particular failure mode on one or more components may be that all end-users will be unable to complete the checkout.

Be sure to consider the likelihood of each type of failure mode as well. There are some that are very unlikely, like multi-zone or multi-region outages, and adding mitigation planning beyond redundancy wouldn't be a good use of resources and time.

### Mitigation

Mitigation strategies fall into two broad categories: building additional resiliency and designing for degraded performance.

Building additional resiliency includes adding redundancy to your components (infrastructure, data, and networking) and ensuring that your application design follows best practices for durability (like breaking up monolithic applications into isolated apps and microservices, for example). For detailed guidance on adding redundancy, refer to (link to RE04) and for guidance on self-preservation design patterns, refer to (link to RE:06).

Designing for degraded performance generally means that you have identified potential failure points that may take down one or more components of your flow, but not fully take that flow down. In order to maintain the functionality of the end-to-end flow, you may need to reroute one or more steps to other components or accept that a function provided by a failed component will no longer be available in the user experience. To return to the ecommerce application example, a failed component like a microservice may result in your recommendation engine becoming unavailable, but the end user would still be able to search for products and complete their transaction.

You also need to plan mitigation around dependencies. Strong dependencies play a critical role in application function and availability. If they are absent or experiencing a malfunction, it may have a significant effect. The absence of weak dependencies might only affect specific features and not affect overall availability. This distinction reflects the cost that is needed to maintain the high availability relationship between the service and its dependencies. Classifying dependencies as either strong or weak helps you identify which components are essential to the application.

If the application has strong dependencies that it can\'t operate without, the availability and recovery targets of these dependencies should align with the targets of the application itself. Make an effort to minimize dependencies to achieve control over application reliability. For more information, see [Minimize coordination](https://learn.microsoft.com/en-us/azure/architecture/guide/design-principles/minimize-coordination).

If the application lifecycle is closely coupled with the lifecycle of its dependencies, it can limit the operational agility of the application. This fact is true particularly for new releases.

### Detection

Failure detection is essential to ensure that you have correctly identified failure points in your analysis and properly planned your mitigation strategies. Detection in this context means the monitoring of your infrastructure, data and application, and alerting when issues arise. Your detection should be automated to the extent possible, and you should have redundancy built into your operations processes to ensure that alerts are always caught and are responded to quickly enough to meet your business requirements. Refer to the monitoring guide (link to RE:10) in this series for detailed guidance.

### Outcome

The outcome of your analysis should be a set of documents that effectively communicate your findings, the decisions that you have made relative to the flow components and mitigations, and the impact of the failure on your workload.

Through your analysis, you should be able to prioritize the failure modes and mitigation strategies that you have identified based on severity and likelihood. Use this prioritization to focus your documentation on those failure modes that are common and severe enough to warrant spending the time, effort and resources on designing mitigation strategies around. For example, there may be some failure modes that are very rare in occurrence or detection and designing mitigation strategies around those is not worth the cost.

Refer to the example table below to give you a starting point for your documentation efforts.

During your initial FMA exercise, the documents you produce will be mostly theoretical planning. Chaos testing and real-world experiences will help you refine your analyses over time.

# Azure facilitation

[Azure Monitor](https://learn.microsoft.com/en-us/azure/azure-monitor/overview) and [Log Analytics](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/log-analytics-overview) are the primary tools that you can use to detect issues in your workload. Built on top of these technologies are more robust tools like [Application Insights](https://learn.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview), [Container Insights](https://learn.microsoft.com/en-us/azure/azure-monitor/containers/container-insights-overview), [Network Insights](https://learn.microsoft.com/en-us/azure/network-watcher/network-insights-overview), [VM Insights](https://learn.microsoft.com/en-us/azure/azure-monitor/vm/vminsights-overview) and [SQL Insights](https://learn.microsoft.com/en-us/azure/azure-sql/database/sql-insights-overview?view=azuresql) that can give you further insight into issues related to your infrastructure, apps, and databases.

[Azure Chaos Studio Preview](https://azure.microsoft.com/services/chaos-studio) is a managed service that uses chaos engineering to help you measure, understand, and improve your cloud application and service resilience.

Refer to the FMA article ([Failure mode analysis - Azure Architecture Center \| Microsoft Learn)](https://learn.microsoft.com/en-us/azure/architecture/resiliency/failure-mode-analysis) to find guidance on applying FMA principles for several common Azure services.

# Tradeoff

As with all decisions related to resilience and reliability, the amount of resilience you build into your systems has cost and management overhead implications. Applying the principles of FMA will help you keep costs to a minimum however, as your analysis will provide you a comprehensive list of failure points that you will need to address and help you decide the level of resilience to apply for a given flow.

# Example

An example of an FMA table is provided below. In this fictitious example, an ecommerce website is hosted in Azure on App Service instances with Azure SQL databases and is fronted by Azure Front Door.

**User flow:** User login, product search and shopping cart interaction

| Component | Risk | Likelihood | Impact/Mitigation/Note | Outage |
|-----------|------|------------|------------------------|--------|
| Azure AD | Service outage | Low | Full workload outage. Dependent on Microsoft to remediate | Full |
| Azure AD | Misconfiguration | Medium | User(s) unable to login. No downstream impact. Help desk reports configuration issue to Identity team. | None |
| Front Door | Service outage | Low | Full outage for external users. Dependent on Microsoft to remediate | External only |
| Front Door | Regional outage | Very low | Minimal impact -- AFD is a global service, so global traffic routing will direct traffic through non-impacted Azure regions | None |
| Front Door | Misconfiguration | Medium | Misconfigurations should be caught during deployment. If these happen during a configuration update, admins must roll back changes. Configuration update will cause a brief external outage. | External only |
| Front Door | DDoS attack | Medium | Potential for disruption. Microsoft manages DDoS (L3 and L4) protection and WAF blocks most threats. Potential risk of impact from L7 attacks. | Potential for partial outage |
| Azure SQL | Service outage | Low | Full workload outage. Dependent on Microsoft to remediate | Full |
| Azure SQL | Regional outage | Very low | Auto-failover group fails over to secondary region. Potential outage during failover. RTO/RPO TBD during reliability testing. | Potential full |
| Azure SQL | AZ outage | Low | No impact | None |
| Azure SQL | Malicious attack (injection) | Medium | Minimal risk -- All SQL instances are VNet bound through Private Endpoints and NSGs add further intra-VNet protection. | Potential -- low risk |
| App Service | Service outage | Low | Full workload outage. Dependent on Microsoft to remediate | Full |
| App Service | Regional outage | Very low | Minimal impact -- latency for users in impacted regions. Front Door will automatically route traffic to non-impacted regions | None |
| App Service | AZ outage | Low | No impact -- App Services have been deployed as zone redundant. Without zone redundancy, there is a potential for impact. | None |
| App Service | DDoS attack | Medium | Minimal impact -- Ingress traffic is protected by Front Door and WAF | None |

## Related links