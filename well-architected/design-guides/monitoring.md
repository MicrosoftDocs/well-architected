---
title: Build a monitoring system for Azure workloads
description: Learn how to design and implement effective monitoring solutions for Azure workloads, including observability, alerting, and performance optimization strategies.
author: PageWriter-MSFT 
ms.author: prwilk 
ms.date: 02/18/2026
ms.topic: concept-article
ms.update-cycle: 1095-days  
---

# How to build a monitoring system for Azure workloads

Monitoring is the practice of collecting and analyzing telemetry to understand system health and behavior. Its purpose is to make the state of a system observable through measurement, connecting technical signals to operational outcomes. Proper monitoring allows teams to detect anomalies, investigate failures, and make informed decisions about evolving the system.

Monitoring should be treated as a core architectural capability, designed alongside the workload rather than added later. Think of it as its own system, with a dedicated stack and data flows, spanning infrastructure, application health, and build and release processes. As workloads grow, monitoring must scale from simple metrics to cross-service correlation and structured analysis in distributed systems. Across all cases, it must align with service objectives and operational needs.

The monitoring system follows a linear progression and can be divided into four logical stages, each building on the insights of the previous one.

:::image type="content" source="_images/monitor-pipeline.png" alt-text="Diagram showing the end to end flow of a monitoring system. It breaks monitoring into four logical stages, each building on the previous one.." lightbox="media/observability/monitor-pipeline.png" border="false":::

This guide explores the process of building those phases and provides best practices. It doesn't cover specialized monitoring like security, reliability, or performance monitoring. The guidance builds on the key strategies outlined in  [Architecture strategies for designing a monitoring system](../operational-excellence/observability.md), which you should review first.

## Phase 1 - Instrumentation

_Instrumentation_ is the practice of embedding code or tools in your source to generate operational signals called _telemetry_. This refers to logs, traces, and metrics. _Logs_ are timestamped records of discrete events, while _traces_ record request paths across components. Distributed tracing is particularly valuable in systems where requests traverse multiple services or machines. _Metrics_ are numerical measurements of a system or resource at a specific point in time, often accompanied by one or more tags or dimensions. Unlike logs or traces, a single metric on its own provides limited insight; metrics help you monitor health and when captured continuously over time to reveal trends, patterns, and anomalies. 

Start by deciding _what to track from the system_ so that you can analyze it later. Think about the layer of the system:

- **Application logic**. Capture the flow of incoming requests like, their start times, durations, and status codes. Pay attention to exceptions and failures in your code. to uncover patterns and root causes. Also, track meaningful business events. For example, in an ecommerce workload, orders placed, payments processed, or messages handled.

- **External dependencies**. Track communication with external dependencies like calls to databases, web services, and infrastructure components. Include their duration, status, retries, and errors.

- **Platform and runtime components**. Because they don't technically fall under your ownership, you can only enable the telemetry collection. This includes components like frameworks, operating system, Azure resources. 

#### Best practices for instrumentation

When adding instrumentation, follows these simple principles: Maintain consistency, provide sufficient context, and structure telemetry to support later analysis. 

- When choosing a technology, consider options where the solution is language-agnostic and can integrate with multiple monitoring platforms. OpenTelemetry is a vendor-neutral option that's adopted by a lot of application. 

- **Record all relevant context consistently**. Include information about source component, environment, deployment, activity ID, user info, and dependency data. For example, timestamps should be recorded at the moment a log entry is created and formatted in a consistent, standardized way. All telemetry data should use Coordinated Universal Time (UTC) to ensure events can be accurately correlated across services, regions, and systems.

- **Use structured telemetry**, which is recorded in both human- and machine-readable formats (JSON, MessagePack, Protobuf).

- **Make log verbosity configurable** because verbose logging can incur significant costs. Also, lead to noise making querying and filtering challenging. One strategy could be to set log levels according to the environment. Pull back on verbosity in lower environments, where as increase verbosity in production.

- **Categorize by operational concern** to make filtering and analysis more efficient. For example separate telemetry data for, audit, security, debugging, or performance. 

- **Enable correlation**. Assign a unique activity ID to each request and propagate it across services, threads, queues, dependencies. Correlation must rely on identifiers and context, not timestamps alone.

#### Example: Instrumentation

Consider an ecommerce application. when a customer places an order, a business transation begins. the API processes the payment, the database records the order, and a confirmation email is sent. Most of the time, everything completes within the expected timeframe. But suppose something breaks.

Without instrumentation, you might only see: "Checkout failed." There's no context, no clear cause, while the user experience quietly deteriorates. Troubleshooting becomes guesswork. Is the database down? Did the email service time out? Is the payment provider experiencing issues?

With proper instrumentation, the request is tracked end-to-end across every component. Logs reveal that the payment API returned a 500 error. A distributed trace shows latency spiking specifically within the payment service. Metrics confirm that the checkout failure rate has climbed from 1% to 8% in 10 minutes. That makes diagnosis systematic and data-driven rather than speculative.


## Phase 2 - Telemetry data collection and storage

While instrumentation is about what operational signals should be tracked, this phase is about *where to route the telemetry data* and manage it responsibly.

Telemetry usually comes from two main sources:

- **Application-level telemetry**. Once the application is instrumented by using SDKs or standards like OpenTelemetry, telemetry data can be collected automatically using Application Performance Management (APM) tool. [Azure Application Insights](../service-guides/application-insights.md) is an APM that's well-integrated with most Azure application hosting services like Azure Functions, App Service, and Virtual Machines. For example, you have an ASP.NET Core app hosted in App Service. Application Insights will automatically capture request rate, failure rate, dependency duration, and distributed traces. Then, that data is ingested into Azure Monitor Logs for analysis. Application Insights gives you the ability to control data retention periods, configure sampling, and manage certain privacy settings.

- **Platform logs**. There's telemetry generated from the infrastructure that the application runs on. In Azure, that's mainly _activity logs_ and _resource logs_. Activity logs track  subscription-level operations (resource creation, updates, deletes). Resource log that capture resource-specific events, like storage access logs, firewall events. 

   Platform log collection requires explicit configuration. Enable Diagnostic settings for each resource and route logs to a storage solution. 

  
#### Telemetry data storage decision

Data storage should be chosen based on how you plan to use it. Create a matrix that clearly highlights the use case, requirements, and technology choices. Here's an example:

|Use Case|Recommended storage|
|---|---|
|Interactive querying and troubleshooting|	[Azure Log Analytics](../service-guides/azure-log-analytics.md)|
|Aggregate data storage|[Azure Databricks](../service-guides/azure-databricks.md)|
|Long-term archival|	[Azure Blob Storage](../service-guides/azure-blob-storage.md)|
|SIEM or security integration| [Event Hub](../service-guides/azure-event-hubs.md)|
|Exploring trends and advanced analytics|	[Azure Data Explorer](/azure/data-explorer?utm_source=chatgpt.com)|

In some cases, you might send the same telemetry to multiple destinations for different purposes.

#### Telemetry data lifecycle management

Not all telemetry needs to live forever. Be clear on how long you want to retain based on the use of the data.  For example, raw debugging data may only require short-term retention, whereas performance metrics, historical trends, or auditing records may need long-term storage.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Telemetry data can grow significantly even within a short period of time. Set clear retention policies so that you keeo the data that truly matters while letting go of what doesn't. Like spot trends, anticipate capacity issues within the right time window. At the same time, you avoid paying for storage you don't really need.

In addition to retention policies that archive older data to lower-cost storage. There are other techniques to optimize costs. Where appropriate, down-sample it to reduce storage usage while preserving sufficient resolution for analysis. Aggregate related events to reduce storage footprint and process data faster.

#### Telemetry data security

While telemetry isn't workload functional data, it's still critical from an operation perspective. From a security point: protect data against accidental deletion; control access with role-based access control (RBAC). Consider the use of:

- Enable soft delete to allow recovery within a defined period. Most Azure storage services offer soft delete. Refer to product documentation for details on the service you've chosen for your workload.

- Apply [resource locks](/azure/azure-resource-manager/management/lock-resources) to prevent unintended changes or deletions to important resources.

- Sensitive data must be encrypted and scrubbed of personally identifiable information. If you're workload needs to store telemetry for regulatory purposes, follow the governance policies according to those compliance requirements. 

## Phase 3 - Correlation, aggregation, analysis

Once telemetry is collected and stored, the next step is to turn raw data into insight. Analysis transforms logs, metrics, and traces into operational decisions. The outcome of this stage is to understand *what does the data mean*?

Analyzing telemetry begins by structuring data around defined KPIs and performance metrics. Start by asking these questions:

- Are users experiencing failures?
- Is performance degrading?
- Are dependencies slowing down?
- Is capacity reaching limits?
- Are business KPIs trending negatively?

To prepare data for analysis, a common step is to **aggregate data from multiple sources**. For example in the case of distrubuted tracing, aggregation involves combining events with the same activity or transaction ID are merged.

Data preparation is another task during aggregation. Here, duplicates are removed, and irrelevant data is filtered out. Consolidation or partitioning services can periodically retrieve, preprocess, and route data to appropriate storage. For example, data needed for alerts or rapid analysis should be stored in fast, indexed storage, and local copies may reduce alert latency.

#### Example: Correlation, aggregation, analysis

Let's continue with the eCommerce example. With instrumentation in place, the team detected that checkout failure rate increased from 1% to 8% in 10 minutes. 

Analyis revealed that the cause was the dependency on the database, which was under heavy load, because:

- Application logs showed errors with timeout exceptions
- Database latency doubled during that time
- Platform logs showed that CPU on database server is at 90%

By retaining analysis data over time, teams can perform historical analysis to uncover trends. For example, consistent increases in database load or gradually rising response times can signal a change in architecture or optimization. For example, scaling the database or optimizing queries may be necessary.

## Phase 4 - Visualization

With analysis in place, the next phase is to take action. *How do we make the insights visible and ensure the right people can act on them?*

Visualization converts complex telemetry into actionable insights using dashboards, charts, and reports.

#### Best practices for visualization

- Align with your health model that show components should be labeled as Healthy, Degraded, or Unhealthy according your service level objectives (SLOs).

- Display actionable data. Avoid cluttering dashboards with non-critical details. Focus on information or trends that support decisions. For example, environment, service, region

- Support interactive exploration. Allow filtering by time range, service, or deployment. Enable ad-hoc queries to investigate anomalies.

- Tailor dashboards for intended audience. For example, 

   |Audience| View|
   |---|---|
   |Developers|	Real-time logs, traces, error trends|
   |Operators|	KPIs, resource utilization, alerts|
   |Business stakeholders| High-level metrics, revenue-impact KPIs|

- Tailor dashboards for intended use. For example, 

   |Use| View|
   |---|---|
   |Operational reports|Resource usage, trends, exceptions, efficiency|
   |Security reports|Audit user actions, track anomalies for compliance|
   |Business reports| KPI trends, revenue per user, transactions over time|

#### Best practices for alerts

Visualization shows trends, but alerts notify you when action is needed.

- Define thresholds based on historical data.
- Include sufficient context and filter out false alarms
- Integrate with automated workflows like your support ticketing systems, auto-remediation, scaling actions
- Assign ownership to the responsible team
- Set prioritization indicators like severity, which separate critical issues from informational notifications.

#### Example: Visualization and alerts

Let's return to the eCommerce applciation. A developer oriented dashboard visualizes:

- Latency trends over the past 24 hours
- Failure rates by endpoint
- Database CPU usage
- Orders processed per minute

From this dashboard, a developer can narrow in investigations about failure spikes when database CPU exceeds 85%. Meanwhile when latency exceeds 2 seconds for the checkout user flow, developer sees request ID, database load, and recent errors. At this point, with sufficient testing an automated workflow is triggered that scales database tier. 

## Azure facilitation

**Core Monitoring Platform:**

- [Azure Monitor](/azure/azure-monitor/overview) is the central platform for collecting, analyzing, and acting on telemetry data from cloud and on-premises environments. It provides unified metrics, logs, and application monitoring.

  - [Azure Monitor Metrics](/azure/azure-monitor/platform/metrics-supported) collects time-series data from monitored resources and supports real-time analysis and alerting.

  - [Azure Monitor Logs](/azure/azure-monitor/logs/data-platform-logs) provides Log Analytics with KQL for analyzing log data and creating custom insights.

  - [Azure Monitor Alerts](/azure/azure-monitor/platform/alerts-overview) enables intelligent alerting with action groups, notification routing, and automated responses.

**Application Performance Monitoring:**

- [Application Insights](/azure/azure-monitor/app/app-insights-overview) provides APM capabilities with end-to-end tracing, dependency mapping, and user behavior analytics.

**Visualization and Analysis:**

- [Azure Workbooks](/azure/azure-monitor/platform/workbooks-overview) enables custom dashboard creation with interactive analytics and rich visualizations for drill-down scenarios.

- [Azure Dashboards](/azure/azure-portal/azure-portal-dashboards) provides centralized views of Azure resources and monitoring data with role-based access controls.

- [Log Analytics](/azure/azure-monitor/log-query/log-query-overview) is the analytics engine for querying and correlating multi-tier logs with advanced trending capabilities.

- [Azure Monitor for VMs](/azure/azure-monitor/insights/vminsights-overview) provides specialized monitoring for virtual machine workloads with performance maps and dependency tracking.

- [Azure Monitor for containers](/azure/azure-monitor/insights/container-insights-overview) offers container-specific monitoring with cluster health, node performance, and pod-level insights.

- [Network Watcher](/azure/network-watcher/network-watcher-monitoring-overview) provides network-specific monitoring, diagnostics, and analytics for Azure networking resources.

## Related links

- [Recommendations for designing and creating an observability framework](../operational-excellence/observability.md)
- [Create an effective incident management plan to manage disruptions](./incident-management.md)
- [Performance efficiency design principles](../performance-efficiency/principles.md)
- [Recommendations for designing a reliable monitoring and alerting strategy](../reliability/monitoring.md)