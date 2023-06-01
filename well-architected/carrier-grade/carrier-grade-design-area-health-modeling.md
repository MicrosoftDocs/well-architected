---
title: Health modeling for carrier-grade workloads
description: This article provides an overview of the Health modeling design area for carrier-grade workloads.
author: mikedell73
ms.author: mikedell
ms.date: 08/23/2022
ms.topic: conceptual
ms.industry: telecommunications
products: 
- azure-app-service
- azure-traffic-manager
- cosmos-db
ms.custom:
  - carrier-grade
---

# Health modeling for carrier-grade workloads

High availability requires careful health monitoring to automatically detect and respond to issues within seconds. This monitoring requires built-in telemetry of key dependencies to reliably detect the failure.  The application itself requires additional telemetry (Service Level Indicators) which accurately report the health of the application in a way that is perceived by users of the application. Evaluation against SLOs may be necessary.

The failure rate analysis and general health modeling of the application should generate clear metrics indicative of the service and health of its constituent elements. These metrics must be included in the design so that the true service availability can be monitored. By including metrics, you can track the most useful leading indicators to trigger the automated failure responses and to generate the necessary alerts for human intervention.

 > [!IMPORTANT]
> More details on how to build a health model for your mission-critical workload are available [here](/azure/architecture/framework/mission-critical/mission-critical-health-modeling#video-define-a-health-model-for-your-mission-critical-workload).

## Management and monitoring

Monitoring and management require the following thought processes:

- How will the application handle bugs in the framework?
- How is the application upgraded?
- What actions should be taken during an incident?

For example, a solution may rely on Azure DevOps (ADO) to host its Git repository for all configuration. If the Azure region hosting that ADO repo fails, the recovery time is two hours. If the solution is deployed in the same region, it's not possible to modify configuration to add capacity elsewhere for that entire two hour period. As a result, the application architect must consider correlated failure modes for key services, such as:

- [Azure Traffic Manager](/azure/traffic-manager/)
- [Azure Key Vault](/azure/key-vault/)
- [Azure Kubernetes Service](/azure/aks/)

Correlated failure modes for these key services may be a necessary part of the application-level response to failure. It's vital to create control planes which aren't impacted by the same application failure.

The management tooling required to issue diagnosis and troubleshooting must be the same as tooling used for normal day-to-day operations tasks. Similar tooling ensures that it's familiar and proven to work. Similar tooling also maximizes the users' familiarity with the user interface and process steps. Requiring operators to switch to a different tool set to resolve a high-pressure outage isn't conducive to identifying and resolving the issue effectively.

### Federated model

A highly available application or service must have a highly available management and monitoring infrastructure built using the same well-architected principles of federation and fault tolerance. Infrastructures built on these well-architected principles ensure individual regions can be self sufficient if disconnected.

If there's a disconnect event, the system degenerates into individually functioning islands, instead of using a primary/backup system. The federated model is flexible and resilient, and automatically adapts to partition and reconnection events.

For example, logs and metrics are stored in the Availability Zone (AZ) where they're generated. A query of metrics uses an opaque process of federated search to query the metric stores in all reachable AZs. It comes down to the requirements of the specific application about what level of logs, metrics, and alarms data should be replicated to other regions. Typically, alarms should be replicated, but there may be insufficient justification to replicate logs and metrics.

## Health and unhealth metrics

Internal metrics are useful as *unhealth* metrics. These metrics reliably indicate the presence of an issue, but the reverse isn't true. No evidence of poor health isn't evidence of good health, as the customer perceives health.

For example, a DNS issue indicates requests aren't arriving at the database service. The DNS error doesn't affect the database read-success metric because this metric isn't seeing any errors. However, the end user perceives a total outage because they aren't able to access the database. At least a portion of *health* metrics must be measured externally, so that these metrics include everything the end user will experience.

## Monitoring and tracing

The support team's ability to detect, diagnose, and resolve issues is an important part of delivering a highly available application. To ensure success, the monitoring and tracing element must deliver high levels of visibility, so that the one in a thousand type events can be found and resolved.

A tracing solution that only logs 0.1% of requests only has a one in one million chance of recording such events, which means that diagnosis and resolution are highly unlikely. Yet, failure to resolve such issues will have a meaningful impact on availability.

## Next step

Review the testing and validation design area for carrier-grade workloads.

> [!div class="nextstepaction"]
> [Design area: Testing and validation](./carrier-grade-design-area-testing.md)

