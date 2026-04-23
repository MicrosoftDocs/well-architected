---
title: Architecture strategies for monitoring workload performance
description: Learn recommendations for collecting performance data (metrics and logs) to help you assess the performance of a workload.  
author: stephen-sumner
ms.author: ssumner
ms.date: 04/20/2026 
ms.topic: concept-article
---

# Architecture strategies for monitoring workload performance

**Applies to this Azure Well-Architected Framework Performance Efficiency checklist recommendation:**

|**PE:04**| Establish consistent performance measurement so behavior can be analyzed over time, compared against baselines, and used to detect degradation, inefficiency, and scaling gaps. |
|---|---|

Without performance data, underlying issues and optimization opportunities go unnoticed, leading to degraded user experience. 

This article describes design strategies for implementing multi-layer performance measurement that captures latency, throughput, and resource behavior to establish baselines and identify performance degradation across the workload.

The key strategies in this article build on the foundational operational practice of observability, described in [OE:07 Architecture strategies for designing a monitoring system](../operational-excellence/observability.md). Guidance on implementing the monitoring practice is available in the [Monitoring Design Guide](../design-guides/monitoring.md). We recommend reviewing those resources first. Recommendations in this guide are scoped to performance. For information about reliability, see [RE:10 Architecture strategies for Reliability monitoring](../reliability/monitoring.md). 


**Definitions**

|  Term| Definition|
|---|---|
|Activity logs  | Logs that track management operations on resources, such as deleting a resource.|
|Application logs|Logs that track information about application events, errors, and other activities, such as sign-ins and database connection failures.|
|Application performance monitoring (APM) tool  | A tool that monitors and reports the performance of an application.|
|Baselines | Expected system performance metrics used as a reference point to detect drift, regressions, and improvements over time.|
|Code instrumentation|The direct or indirect capture of performance metrics from the perspective of the application code. Captured metrics include flow metrics, resource use, and metrics specific to the language or runtime.|
|Distributed tracing|Gathering and correlating metrics across distributed workload components to understand end-to-end transaction flows.|
|Latency | The time delay between initiating a request and receiving a response, measuring system responsiveness.|
|Metrics | Numerical measurements that record workload performance behavior over time, typically aggregated for analysis.|
|Percentiles (p50, p95, p99) | Statistical measures showing performance distribution; p50 represents typical performance, p95 shows most user experience under load, p99 captures worst-case performance.|
|Platform metrics  | Numerical values that record workload performance at a particular time.|

## Use percentile-based metrics

Measure performance metrics, like latency, response time, or load times, with percentiles (p50, p95, p99), not averages. Averages can be misleading. If most requests are fast but a few are extremely slow, the average will hide the bad experience.

Percentiles show the tail behavior, which is important for understanding user experience. p50 represents typical performance, p95 shows what most users experience under load, and p99 captures the worst-case performance or the outliers.

Collect performance data using your monitoring tools and represent them as percentiles over defined time windows. Use these as your baseline for monitoring, alerting, and performance analysis.

## Define your performance improvement boundaries

Define clear performance boundaries so you know exactly what is included in your measurements. Break down latency across the system instead of treating it as a single number. For example, attribute time to each layer where that's edge services, gateways, compute, and dependencies, to see where delays actually occur.

Separate what you control from what you don't: your code, services, infrastructure, and direct dependencies versus external factors such as client-side conditions, DNS resolution, ISP latency, or device constraints. This distinction prevents misattribution and keeps optimization focused on areas where you can make changes, while still reflecting the full end-to-end user experience. 

Evaluate when and where performance issues affect user experience. Compensate that degradation through design or mitigate through improvements in the controllable parts of the system.

## Segment signals by environment and purpose

Segment performance data so each signal reflects a clear context. Separate by environment and purpose to avoid mixing signals that behave differently or serve different decisions.

Keep production and nonproduction data separate. Production data reflects real user impact and should drive monitoring and alerts. Nonproduction data is useful for testing and tuning, but mixing it with production skews results and hides real issues.

Separate performance metrics from business metrics. Performance metrics track system behavior and workload health, while business metrics track outcomes. Even when they overlap, keep them in distinct streams so each can be analyzed and used independently.

## Create scoped and actionable performance alerts

The goal of alerting is early detection of performance degradation before it becomes user-visible or business-impacting. Build alerts at two levels: end-to-end user experience and core internal transactions that represent critical system paths under load.

Use a single, consistent dataset within each environment for both targets and alerts. If alerts are based on different data than your performance targets, they become unreliable and hard to trust.

Create alerts that are actionable and clearly tied to performance outcomes. Each alert should indicate what threshold had a sustained breach, the potential impact, and the components involved so it's clear where to investigate and what is affected. Start with standard, well-known thresholds, then refine them over time based on observed system behavior and workload characteristics.

When direct alerting on an external dependency is not possible, use indirect signals such as dependency call duration, error rates, or timeout behavior to approximate its impact on system performance.

## Monitor elasticity

Measure how your system responds to changes in demand and scaling events.

Track cold start and initialization latency to understand how startup overhead affects responsiveness, especially during scale-out events. Monitor scale-out and scale-in behavior to evaluate how quickly the system adapts to changes in load and whether scaling actions keep pace with demand.

## Track performance over time

Track how performance evolves with changes in your design and external factors. 

Establish baselines that represent expected system performance, then compare current behavior against them to detect drift, including regressions and improvements.

Connect performance changes to operational events such as deployments, configuration updates, and scaling actions. Annotate timelines with these events so shifts in behavior have clear context and can be traced back to likely causes.

Use this ongoing visibility as a feedback loop for engineering decisions. Feed performance insights into planning and prioritization, and treat them as inputs to regular work rather than only incident response.

Continuously refine performance objectives as the system evolves. Adjust SLOs, thresholds, and expectations based on observed behavior and usage patterns so that targets remain realistic and aligned with actual user experience.


## Collect application performance data

You need to have application's performance metrics, such as throughput, latency, and completion times, primarily gathered through instrumenting code. 

Instrument critical execution paths where performance is most visible: key request flows, resource-intensive operations, and points where external dependencies or retries occur. Ensure end-to-end transaction visibility so total execution time and its contributing steps can be measured.

Capture three core types of performance signals:

- Metrics for aggregated performance behavior (latency distributions, throughput, error rates)
- Traces for understanding how time is distributed across request paths and system components
- Logs for detailed execution context at specific steps or events

Use consistent metadata across these signals so performance data can be correlated across layers of the system and across services.

Avoid duplicating lower-level performance signals already exposed by the platform unless additional granularity is required to explain workload-specific behavior or bottlenecks.

## Collect resource performance data

Collect resource-level performance data to understand how infrastructure components behave under load and how they contribute to overall workload performance.

Each service exposes platform-specific metrics that reflect its health and performance characteristics. Use [diagnostic setting](/azure/azure-monitor/essentials/monitor-azure-resource#monitoring-data-from-azure-resources) to export this data so it can be accessed for alerting, dashboards, and longer-term analysis beyond short-lived platform retention.

Collect metrics and logs for all resources. Track compute and storage utilization against expected ranges to confirm under-provisioning isn't introducing latency and degrading performance under load. Overprovisioning is also detectable with this data by looking at P99 usage and comparing to remaining headroom on your resources.

Monitor network traffic as part of resource performance. Analyze traffic flow across subnets and service boundaries to understand latency, congestion, and data transfer patterns that may impact workload performance.

## Collect database and storage data

Database and storage systems produce specialized performance signals for identifying bottlenecks, validating capacity, and understanding workload behavior. These signals typically come from built-in monitoring tools and system-generated logs.

Focus on key performance dimensions:

| Area         | What to measure                  | What it tells you                   |
| ------------ | -------------------------------- | ----------------------------------- |
| Throughput   | Read/write volume over time      | Data transfer capacity              |
| Latency      | Time per storage operation       | Responsiveness of storage           |
| IOPS         | Read/write operations per second | Transaction handling capability     |
| Capacity use | Used vs available storage        | Scaling and capacity planning needs |

For databases, extend monitoring to workload-specific behavior:

| Area                    | What to measure                           | What it tells you                               |
| ----------------------- | ----------------------------------------- | ----------------------------------------------- |
| Query performance       | Execution time, frequency, resource usage | Efficiency of data access patterns              |
| Transaction performance | Duration, concurrency, lock contention    | Contention and transactional efficiency         |
| Index performance       | Fragmentation, usage, optimization impact | Effectiveness of query acceleration structures  |
| Resource use            | CPU, memory, disk, network                | System-level constraints                        |
| Connection metrics      | Active, failed, aborted connections       | Stability and connection pressure               |
| Transaction rate        | Transactions per second                   | Workload intensity and changes over time        |
| Error rates             | Database errors and failures              | Reliability and performance degradation signals |

Use these signals together to distinguish between slow queries, resource saturation, and structural inefficiencies. This enables targeted optimization across both storage systems and database workloads.

## Collect operating system performance data

For infrastructure-based workloads, collect operating system-level metrics to understand how compute resources are being utilized and where resource constraints may occur. 

Sample OS performance counters at regular intervals to capture time-based behavior of the system under load.

| Area      | What to measure                                             | What it indicates                          |
| --------- | ----------------------------------------------------------- | ------------------------------------------ |
| CPU       | CPU usage (user/privileged), CPU queue length               | Compute saturation and scheduling pressure |
| Processes | Thread count, handle count                                  | Application and OS-level process load      |
| Memory    | Committed memory, available memory, paging rate, swap usage | Memory pressure and paging activity        |
| Disk      | Read/write rate, throughput, disk utilization               | Storage I/O performance and bottlenecks    |
| Network   | Interface throughput, RX/TX errors                          | Network capacity and transmission issues   |

Use these signals to identify resource saturation at the operating system level and to distinguish between application-level inefficiencies and infrastructure constraints.


### Generate synthetic data, when necessary

If your system isn't used consistently, it's hard to tell whether it will perform well when traffic returns.

To address this, use synthetic transactions, which sends automated requests through your system. These simulate real usage without affecting actual users or data. This helps in keeping parts of your system active, generate consistent performance metrics, and reveal patterns (like time-of-day issues) that irregular usage might hide.

## Azure facilitation

[Azure Monitor](/azure/azure-monitor/overview) provides a unified platform for collecting, analyzing, and responding to performance data across your entire workload. It aggregates data from applications, infrastructure, and external sources into a common data platform.

**Data collection and storage**: Use [Log Analytics workspaces](/azure/azure-monitor/logs/log-analytics-workspace-overview) to centralize your performance data with configurable [retention policies](/azure/azure-monitor/logs/data-retention-archive). Create multiple workspaces to segment data by environment or compliance requirements.

**Application monitoring**: [Application Insights](/azure/azure-monitor/app/app-insights-overview) collects application-level telemetry including request rates, response times, and exceptions. Enable [distributed tracing](/azure/azure-monitor/app/distributed-tracing-telemetry-correlation) to correlate performance across distributed components.

**Infrastructure monitoring**: Enable [diagnostic settings](/azure/azure-monitor/essentials/monitor-azure-resource#monitoring-data-from-azure-resources) on all Azure services to collect platform logs and metrics. Use [Azure Diagnostics extension](/azure/azure-monitor/agents/diagnostics-extension-overview) for detailed VM performance data. Explore telemetry options for your specific platform. For example, Kubernetes clusters emit rich performance telemetry through [Prometheus](/azure/azure-monitor/metrics/prometheus-metrics-overview) integrations.

**Database and storage**: Azure Monitor provides built-in monitoring for Azure SQL Database, MySQL, PostgreSQL, and storage services. Azure Storage Analytics tracks key performance indicators like throughput and latency across Blob, Table, and Queue Storage.

**Alerting and analysis**: Create alert rules with customizable thresholds, time windows, and actions (email, webhooks, Azure Functions). Use Azure Monitor Logs to cross-query and correlate performance data. For pricing details, see [Azure Monitor pricing](https://azure.microsoft.com/pricing/details/monitor/).

## Examples

- [Baseline highly available zone-redundant app services web application](/azure/architecture/reference-architectures/app-service-web-app/baseline-zone-redundant)
- [Monitor a microservices application in Azure Kubernetes Service (AKS)](/azure/aks/monitor-aks)
- [Monitor Azure platforms landing zone components](/azure/cloud-adoption-framework/ready/landing-zone/design-area/management-monitor)

## Related links


- [Platform metrics](/azure/azure-monitor/platform/data-platform-metrics#what-can-you-do-with-azure-monitor-metrics)
- [Diagnostic settings](/azure/azure-monitor/platform/diagnostic-settings)
- [Audit logs](/azure/active-directory/reports-monitoring/concept-audit-logs)

## Performance Efficiency checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Performance Efficiency checklist](checklist.md)
