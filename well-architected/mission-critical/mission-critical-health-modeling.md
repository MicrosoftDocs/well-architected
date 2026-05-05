---
title: Health modeling and observability of mission-critical workloads on Azure
description: Learn how Azure Monitor health model concepts apply to mission-critical workloads.
author: calcof
ms.author: calcof
ms.date: 05/05/2026
ms.topic: concept-article
---

# Health modeling and observability of mission-critical workloads on Azure

Health modeling adds workload and business context to monitoring data. For mission-critical workloads, the model should show the health of critical flows, deployment stamps, Azure resources, and dependencies.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected mission-critical workload](index.yml) series. If you aren't familiar with this series, start with [what is a mission-critical workload?](mission-critical-overview.md#what-is-a-mission-critical-workload).

Mission-critical operations need three levels of maturity:

1. **Detect** and respond to issues as they happen.
1. **Diagnose** issues that are occurring or have already occurred.
1. **Predict** and prevent issues before they affect users.

## Video: Define a health model for your mission-critical workload

> [!VIDEO https://learn-video.azurefd.net/vod/player?id=fd8c4e50-9d7f-4df0-97cb-d0474b581398&embedUrl=/azure/well-architected/mission-critical/mission-critical-health-modeling]

## Key health model concepts

[Azure Monitor health models](/azure/azure-monitor/health-models/overview) define and track health for Azure resources. Health models use Azure Monitor signals and add business context.

> [!NOTE]
> Azure Monitor health models are in preview. For general health modeling guidance, see [Health modeling for workloads](../design-guides/health-modeling.md).

| Concept | Mission-critical guidance |
| --- | --- |
| [Root entity](/azure/azure-monitor/health-models/concepts#root-entity) | Represents overall workload health. All other entities connect to the root directly or through relationships. |
| [Azure resource entity](/azure/azure-monitor/health-models/concepts#azure-resource-entity) | Represents an Azure resource. The same resource can appear in multiple models with different signals or thresholds. |
| [Generic entity](/azure/azure-monitor/health-models/concepts#generic-entity) | Represents flows, deployment stamps, regions, business processes, or non-resource dependencies. |
| [Relationship](/azure/azure-monitor/health-models/concepts#relationship) | Captures dependencies and aggregations so health can propagate to parent entities and the root. |
| [Signals](/azure/azure-monitor/health-models/concepts#signals) | Metrics or log queries that are already collected by Azure Monitor. Each entity has its own thresholds. |
| [Health states](/azure/azure-monitor/health-models/concepts#health-states) | Healthy, degraded, unhealthy, and unknown states defined for the workload context. |
| [Impact](/azure/azure-monitor/health-models/concepts#impact) | Standard, limited, or suppressed propagation from child health to parent health. |
| [Health objective](/azure/azure-monitor/health-models/concepts#health-objective) | Target percentage of time that an entity should be healthy. |
| [Alerts](/azure/azure-monitor/health-models/concepts#alerts) | Health-based alerts for flows and workload health, instead of separate alerts for every raw signal. |

## Mission-critical guidance

- Represent customer-facing, revenue-impacting, safety-impacting, or compliance-impacting flows as generic entities.

- Model deployment stamps and regions when regional blast radius, failover, or traffic routing affects health.

- Base signals on workload requirements, such as latency, error rate, saturation, queue age, data freshness, probe result, and dependency availability.

- Treat an [unknown health state](/azure/azure-monitor/health-models/concepts#health-states) as an operational problem for critical entities because the workload health can't be evaluated.

- Match [impact settings](/azure/azure-monitor/health-models/concepts#impact) to business criticality.

- Validate the model during load testing, failover testing, chaos testing, and incident reviews.

## Signal and data collection guidance

- The health model evaluates signals. It doesn't collect telemetry. Configure Azure Monitor data collection before you rely on model health. For more information, see [Sources of monitoring data for Azure Monitor](/azure/azure-monitor/data-sources).

- Use [Azure Monitor Metrics](/azure/azure-monitor/essentials/data-platform-metrics), logs, and application telemetry as signal sources.

- Use [Azure Monitor managed service for Prometheus](/azure/azure-monitor/metrics/prometheus-metrics-overview) to collect Prometheus metrics from AKS or Azure Arc-enabled Kubernetes clusters.

- Use health probes as functional signals. For Kubernetes workloads, probes expose application health to the orchestrator. For more information, see [Health Endpoint Monitoring pattern](/azure/architecture/patterns/health-endpoint-monitoring).

- For AKS and App Service guidance, see the [Azure Kubernetes Service service guide](../service-guides/azure-kubernetes-service.md) and the [App Service service guide](../service-guides/app-service-web-apps.md).

## Analyze and alert on health

- Use the [graph view](/azure/azure-monitor/health-models/analyze-health#graph-view) for current dependency health.

- Use the [timeline view](/azure/azure-monitor/health-models/analyze-health#timeline-view) for health history.

- Use [entity details](/azure/azure-monitor/health-models/analyze-health#entity-details) to inspect signals, alerts, resource health, and change analysis.

- Create alerts on critical entities and the root entity. For more information, see [Alerts in Azure Monitor health models](/azure/azure-monitor/health-models/concepts#alerts) and [Azure Monitor action groups](/azure/azure-monitor/alerts/action-groups).

- Keep resource-level alerts when they drive a distinct operational response. Use health alerts when multiple signals should roll up to one business-impacting state.

## Related links

- [Health models in Azure Monitor](/azure/azure-monitor/health-models/overview)
- [Azure Monitor health model concepts](/azure/azure-monitor/health-models/concepts)
- [Configure an Azure Monitor health model using the designer](/azure/azure-monitor/health-models/designer)
- [Analyze health state of Azure Monitor health models](/azure/azure-monitor/health-models/analyze-health)
- [Health modeling for workloads](../design-guides/health-modeling.md)

## Next step

Review the deployment and testing considerations.

> [!div class="nextstepaction"]
> [Deployment and testing](mission-critical-deployment-testing.md)