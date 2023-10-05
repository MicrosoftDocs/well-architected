---
title: Failure mode analysis recommendations
description: Learn how to identify potential points of failure within your workload and the associated flows, and plan mitigation actions accordingly.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
ms.custom:
  - guide
categories:
  - security
---
# Recommendations for performing failure mode analysis

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

|[RE:03](checklist.md)| Use failure mode analysis (FMA) to identify and prioritize potential failures in your solution components. Perform FMA to help you assess the risk and effect of each failure mode. Determine how the workload responds and recovers.  |
|---|---|


This guide describes the best practices for performing failure mode analysis (FMA) for your workload. FMA is the practice of identifying potential points of failure within your workload and the associated flows and planning mitigation actions accordingly. At each step of the flow, you identify the blast radius of multiple failure types, which helps you design a new workload or refactor an existing workload to minimize the widespread effect of failures.

A key tenet of FMA is that failures happen no matter how many layers of resiliency you apply. More complex environments are exposed to more types of failures. Given this reality, FMA allows you to design your workload to withstand most types of failures and recover gracefully when a failure occurs.

If you skip FMA altogether or perform an incomplete analysis, your workload is at risk of unpredicted behavior and potential outages caused by suboptimal design.

**Definitions**

| Term | Definition|
|------|-----------|
| Failure mode | A type of problem that can cause one or more workload components to be degraded or severely affected to the point of being unavailable. |
| Mitigation | The activities that you have identified to address problems either proactively or reactively. |
| Detection | Your infrastructure, data, and app monitoring and alerting processes and procedures. |

## Key design strategies

### Prerequisites

Review and implement the [recommendations for identifying flows](identify-flows.md). It’s assumed that you have identified and prioritized user and system flows based on criticality.

The data that you have gathered and the artifacts that you have created in your work provide you with a concrete description of your data paths involved throughout the flows. To be successful in your FMA work, accuracy and thoroughness in your artifacts is critical.

### FMA approach

After you determine the critical flows, you can plan their required components. Next, follow each flow step by step to identify dependencies, including third-party services and potential points of failure, and plan your mitigation strategies.

#### Decompose the workload

As you move from ideation to design, you need to identify the component types that are required to support your workload. Your workload determines the necessary components that you must plan for. Typically, you need to plan for ingress control, networking, compute, data, storage, supporting services (like authentication, messaging, and secret or key management), and egress control. At this stage in your design work, you might not know the specific technologies that you'll deploy, so your design might look like the following example.

:::image type="content" source="media/failure-mode-analysis/failure-mode-example.png" alt-text="Diagram that shows the design example." border="false" lightbox="media/failure-mode-analysis/failure-mode-example.png":::

After you create your initial architecture design, you can overlay your flows to identify the discrete components that are used in those flows and create lists or workflow diagrams that describe the flows and their components. To understand the criticality of the components, use the criticality definitions that you have assigned to the flows. Consider the effect of a component malfunction on your flows.

#### Identify dependencies

Identify your workload dependencies to perform your single point-of-failure analysis. Decomposing your workload and overlaying flows provides insight into dependencies that are internal and external to the workload.

Internal dependencies are components in the workload scope that are required for the workload to function. Typical internal dependencies include APIs or secret/key management solutions like Azure Key Vault. For these dependencies, capture the reliability data, like availability SLAs and scaling limits. External dependencies are required components outside the scope of the workload, such as another application or third-party service. Typical external dependencies include authentication solutions, like Microsoft Entra ID, and cloud connectivity solutions, like Azure ExpressRoute.

Identify and document the dependencies in your workload, and include them in your flow documentation artifacts.

### Failure points

In your workload's critical flows, consider each component and determine how that component, and its dependencies, might be affected by a failure mode. Remember that there are many failure modes to consider when planning for resiliency and recovery. Any one component can be affected by more than one failure mode at any given time. These failure modes include:

- Regional outage. An entire Azure region is unavailable.

- Availability zone outage. An Azure availability zone is unavailable.

- Service outage. One or more Azure services are unavailable.

- Distributed denial-of-service (DDoS) or other malicious attack.

- App or component misconfiguration.

- Operator error.

- Planned maintenance outage.

- Component overload.

The analysis should always be in the context of the flow you're attempting to analyze, so be sure to document the effect on the user and expected result of that flow. For example, if you have an e-commerce application and you’re analyzing your customer flow, the effect of a particular failure mode on one or more components might be that all customers are unable to complete the checkout.

Consider the likelihood of each type of failure mode. Some are very unlikely, like multi-zone or multi-region outages, and adding mitigation planning beyond redundancy isn't a good use of resources and time.

#### Mitigation

Mitigation strategies fall into two broad categories: building more resiliency and designing for degraded performance.

Building more resiliency includes adding redundancy to your components, like infrastructure, data, and networking, and ensuring that your application design follows best practices for durability, for example breaking up monolithic applications into isolated apps and microservices. For more information, see [Recommendations for redundancy](redundancy.md) and [Recommendations for self-preservation](self-preservation.md).

To design for degraded performance, identify potential failure points that might disable one or more components of your flow but don't fully disable that flow. To maintain the functionality of the end-to-end flow, you might need to reroute one or more steps to other components or accept that a failed component runs a function, so the function is no longer available in the user experience. To return to the e-commerce application example, a failed component like a microservice might cause your recommendation engine to be unavailable, but the customers can still search for products and complete their transaction.

You also need to plan mitigation around dependencies. Strong dependencies play a critical role in application function and availability. If they're absent or experiencing a malfunction, there might be significant effect. The absence of weak dependencies might only affect specific features and not affect overall availability. This distinction reflects the cost to maintain the high availability relationship between the service and its dependencies. Classify dependencies as either strong or weak to help you identify which components are essential to the application.

If the application has strong dependencies that it can't operate without, the availability and recovery targets of these dependencies should align with the targets of the application itself. Minimize dependencies to achieve control over application reliability. For more information, see [Minimize coordination between application services to achieve scalability](/azure/architecture/guide/design-principles/minimize-coordination).

If the application lifecycle is closely coupled with the lifecycle of its dependencies, the operational agility of the application might be limited, particularly for new releases.

#### Detection

Failure detection is essential to ensure that you have correctly identified failure points in your analysis and properly planned your mitigation strategies. Detection in this context means the monitoring of your infrastructure, data and application, and alerting when issues arise. Automate detection as much as possible, and build redundancy into your operations processes to ensure that alerts are always caught and are responded to quickly enough to meet your business requirements. For more information, see the [Recommendations for monitoring](monitoring-alerting-strategy.md).

#### Outcome

For the outcome of your analysis, create a set of documents that effectively communicate your findings, the decisions that you have made relative to the flow components and mitigation, and the effect of the failure on your workload.

In your analysis, prioritize the failure modes and mitigation strategies that you have identified based on severity and likelihood. Use this prioritization to focus your documentation on those failure modes that are common and severe enough to warrant spending the time, effort, and resources on designing mitigation strategies around. For example, there might be some failure modes that are very rare in occurrence or detection. Designing mitigation strategies around them isn't worth the cost.

Refer to the following [example table](#example) for a documentation starting point.

During your initial FMA exercise, the documents you produce will be mostly theoretical planning. Chaos testing and real-world experiences will help you refine your analyses over time.

## Azure facilitation

Use [Azure Monitor](/azure/azure-monitor/overview) and [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview) to detect issues in your workload. For further insight into issues related to your infrastructure, apps, and databases, use tools like [Application Insights](/azure/azure-monitor/app/app-insights-overview), [Container Insights](/azure/azure-monitor/containers/container-insights-overview), [Network Insights](/azure/network-watcher/network-insights-overview), [VM Insights](/azure/azure-monitor/vm/vminsights-overview), and [SQL Insights](/azure/azure-sql/database/sql-insights-overview).

[Azure Chaos Studio Preview](https://azure.microsoft.com/services/chaos-studio) is a managed service that uses chaos engineering to help you measure, understand, and improve your cloud application and service resilience.

For information about applying FMA principles to common Azure services, see [Failure mode analysis for Azure applications](/azure/architecture/resiliency/failure-mode-analysis).

## Tradeoff

As with all resiliency and reliability decisions, the amount of resiliency you build into your systems has cost and management overhead implications. Apply the principles of FMA to help keep costs to a minimum. Your analysis provides you with a comprehensive list of failure points to address, and it helps you decide the level of resilience to apply for a given flow.

## Example

The following table shows an FMA example for an e-commerce website that's hosted on Azure App Service instances with Azure SQL databases and is fronted by Azure Front Door.

**User flow**: User sign in, product search, and shopping cart interaction

| Component | Risk | Likelihood | Effect/Mitigation/Note | Outage |
|-----------|------|------------|------------------------|--------|
| Microsoft Entra ID | Service outage | Low | Full workload outage. Dependent on Microsoft to remediate. | Full |
| Microsoft Entra ID | Misconfiguration | Medium | Users unable to sign in. No downstream effect. Help desk reports configuration issue to identity team. | None |
| Azure Front Door | Service outage | Low | Full outage for external users. Dependent on Microsoft to remediate. | External only |
| Azure Front Door | Regional outage | Very low | Minimal effect. Azure Front Door is a global service, so global traffic routing directs traffic through non-effected Azure regions. | None |
| Azure Front Door | Misconfiguration | Medium | Misconfigurations should be caught during deployment. If these happen during a configuration update, administrators must roll back changes. Configuration update causes a brief external outage. | External only |
| Azure Front Door | DDoS attack | Medium | Potential for disruption. Microsoft manages DDoS (L3 and L4) protection and Well-Architected Framework blocks most threats. Potential risk of effect from L7 attacks. | Potential for partial outage |
| Azure SQL | Service outage | Low | Full workload outage. Dependent on Microsoft to remediate. | Full |
| Azure SQL | Regional outage | Very low | Auto-failover group fails over to secondary region. Potential outage during failover. Recovery time objectives (RTOs) and recovery point objectives (RPOs) to be determined during reliability testing. | Potential full |
| Azure SQL | Availability zone outage | Low | No effect | None |
| Azure SQL | Malicious attack (injection) | Medium | Minimal risk. All Azure SQL instances are virtual network-bound through private endpoints and network security groups (NSGs) add further intra-virtual network protection. | Potential low risk |
| App Service | Service outage | Low | Full workload outage. Dependent on Microsoft to remediate. | Full |
| App Service | Regional outage | Very low | Minimal effect. Latency for users in effected regions. Azure Front Door automatically routes traffic to non-effected regions. | None |
| App Service | Availability zone outage | Low | No effect. App services have been deployed as zone redundant. Without zone redundancy, there's a potential for effect. | None |
| App Service | DDoS attack | Medium | Minimal effect. Ingress traffic is protected by Azure Front Door and Well-Architected Framework. | None |

## Related links

- [Failure mode analysis for Azure applications](/azure/architecture/resiliency/failure-mode-analysis)
- [Resiliency and dependencies](../resiliency/design-resiliency.md)
 
## Reliability checklist  

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Reliability checklist](checklist.md) 
