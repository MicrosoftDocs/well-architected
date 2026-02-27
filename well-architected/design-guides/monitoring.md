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

Monitoring is the mindful practice of collecting and analyzing telemetry to understand system health and behavior. Its purpose is to make the state of a system observable through measurement, connecting technical signals to operational outcomes. Proper monitoring allows teams to detect anomalies, investigate failures, and make informed decisions.

As a cloud solution architect, treat monitoring as a core architectural capability, and design it alongside the functional stack rather than later. Think of it as its own system, with a dedicated stack and data flows, that observes functional stack: infrastructure, application health, and build and release processes. As the workload grows, also evolve monitoring practices from simple metrics to cross-service correlation and structured analysis. 

Typically, a monitoring flow follows a linear progression and can be divided into four logical stages, each building on the insights of the previous one.

:::image type="content" source="_images/monitor-pipeline.png" alt-text="Diagram showing the end to end flow of a monitoring system. It breaks monitoring into four logical stages, each building on the previous one.." lightbox="_images/monitor-pipeline.png" border="false":::

This guide explores the process of building those phases and provides best practices. It doesn't cover specialized monitoring use cases like security. The guidance builds on the key strategies outlined in [OE:07 Architecture strategies for designing a monitoring system](../operational-excellence/observability.md), which you should review first.

## Phase 1 - Instrumentation

_Instrumentation_ is the process of embedding code or tools in your source to generate operational signals called _telemetry_. This refers to logs, traces, and metrics. _Logs_ are timestamped records of discrete events, while _traces_ record request paths across components. _Metrics_ are numerical measurements of a system or resource at a specific point in time, often accompanied by one or more tags or dimensions. Unlike logs or traces, a single metric on its own provides limited insight; metrics help you monitor health and when captured continuously over time to reveal trends, patterns, and anomalies. 

Start by deciding _what to track from the system_. Think about the layers of the system and what information can help decision making during analysis:

- **Application logic**. Capture the flow of incoming requests like, their start times, durations, and status codes. Pay attention to the information emitted in exceptions and failures. These are often useful in  uncovering patterns and root causes. Also, track meaningful business events. For example, in an eCommerce workload, orders placed, payments processed, or messages handled.

- **External dependencies**. Track communication with external dependencies like calls to databases, web services, and infrastructure components. Include their duration, status, retries, and errors.

There are also platform and runtime components that generate telemetry. Because you aren't accountable for their source, you can only enable collection of that data. This includes components like frameworks, operating system, Azure resources. 

#### Best practices for instrumentation

When adding instrumentation, follow these simple principles: Maintain consistency, provide sufficient context, and structure information for ease of analysis in later phases. 

- **Choose technology that's language-agnostic and can integrate with multiple monitoring platforms**. OpenTelemetry is a vendor-neutral option that's adopted by a lot of applications. 

- **Record all relevant context consistently**. Include information about source component, environment, deployment, activity ID, user info, and dependency data. For example, timestamps should be recorded at the moment a log entry is created and formatted in a consistent, standardized way. All telemetry data should use Coordinated Universal Time (UTC) to ensure events can be accurately correlated across services, regions, and systems.

- **Use structured telemetry**, which is recorded in both human- and machine-readable formats (JSON, MessagePack, Protobuf).

- **Make log verbosity configurable** because verbose logging can incur significant costs. Also, it can lead to noise making querying and filtering challenging. One strategy could be to set log levels according to the environment. Pull back on verbosity in lower environments, whereas increase verbosity in production. Another strategy is to align verbosity with the criticality of the component.

- **Categorize by operational concern** to make filtering and analysis more efficient. For example, indicate purpose in telemetry data whether it's for audit, security, debugging, or performance. 

- **Enable correlation**. Distributed tracing is particularly valuable in systems where requests traverse multiple services or machines. Assign a unique activity ID to each request and propagate it across services, threads, queues, dependencies. Correlation must rely on identifiers and context, not timestamps alone.

#### Example: Instrumentation

Consider an ecommerce application. when a customer places an order, a business transaction begins. The API processes the payment, the database records the order, and a confirmation email is sent. When the system works as expected, everything completes within the expected timeframe. But suppose something breaks.

Without instrumentation, you might only see: "Checkout failed." There's no context, no clear cause, while the user experience quietly deteriorates. Troubleshooting becomes guesswork. Is the database down? Did the email service time out? Is the payment provider experiencing issues?

// Art coming soon.

With proper instrumentation, the request is tracked end-to-end across every component. Logs reveal that the payment API returned a 500 error. A distributed trace shows latency spiking specifically within the payment service. Metrics confirm that the checkout failure rate has climbed from 1% to 8% in 10 minutes. That makes diagnosis systematic and data-driven rather than speculative.


## Phase 2 - Telemetry data collection and storage

While instrumentation is about what operational signals should be tracked, this phase is about *where to route the telemetry data* and manage it responsibly.

It's common to use monitoring agents that pull data and write the information directly to common storage. 

:::image type="content" source="_images/monitor-write-shared-storage.png" alt-text="Diagram that shows the use of a monitoring agent to pull information and write it to shared storage." lightbox="_images/monitor-write-shared-storage.png" border="false":::

Or, the agents can act as a passive receiver that wait for the data to be sent from the application.

Telemetry usually comes from two main sources:

- **Application-level telemetry**. After the application is instrumented by using SDKs or standards like OpenTelemetry, telemetry data can be pulled automatically using Application Performance Management (APM) tool. [Azure Application Insights](../service-guides/application-insights.md) is an APM that's well-integrated with most Azure application hosting services like Azure Functions, App Service, and Virtual Machines. For example, you have an ASP.NET Core app hosted in App Service. Application Insights will automatically capture request rate, failure rate, dependency duration, and distributed traces. Then, that data is ingested into Azure Monitor Logs for analysis. Application Insights gives you the ability to control data retention periods, configure sampling, and manage certain privacy settings.

- **Platform logs**. There's telemetry generated from the infrastructure that the application runs on. In Azure, that's mainly _activity logs_ and _resource logs_. Activity logs track  subscription-level operations (resource creation, updates, deletes). Resource log that capture resource-specific events, like storage access logs, firewall events. 

   Enable Diagnostic settings for each resource and route logs to a storage solution.

   Platform log collection requires explicit configuration. For example, Azure Monitor Agent. When installed on the VMs, it pulls local logs and delivers them to Azure Monitor, Microsoft Sentinel, and Microsoft Defender for Cloud.
   
  
#### Telemetry data storage decision

Data storage should be chosen based on how you plan to use it. Create a matrix that clearly highlights the use case, requirements, and technology choices. Here's an example:

|Use Case|Recommended storage|
|---|---|
|Interactive querying and troubleshooting|	[Azure Log Analytics](../service-guides/azure-log-analytics.md)|
|Real-time analytics|[Azure Databricks](../service-guides/azure-databricks.md)|
|Long-term archival|	[Azure Blob Storage](../service-guides/azure-blob-storage.md)|
|SIEM or security integration| [Event Hub](../service-guides/azure-event-hubs.md)|
|Aggregation and analysis for exploring trends and advanced analytics|	[Azure Data Explorer](/azure/data-explorer)|

In some cases, you might send the same telemetry to multiple destinations for different purposes.

#### Telemetry peformance considerations

A single pplication can generate massive volumes of telemetry from multiple web and worker roles, database shards, and supporting Azure services. Sending all of this data to a single central store can quickly overwhelm available I/O bandwidth and create a bottleneck. The monitoring system must scale with the system making sure critical data isn't lost. A practical way to achieve this is by introducing queuing.

:::image type="content" source="_images/queue-buffer-data.png" alt-text="Diagram that shows how you can use a queue to buffer instrumentation data." lightbox="_images/queue-buffer-data.png" border="false":::

In this architecture, a monitoring agent publishes telemetry data to a queue. A separate, asynchronous process, such as a storage writing service, consumes messages from the queue and writes them to shared storage.

Using a message queue is recommended because it provides _at-least-once_ delivery semantics, helping ensure that telemetry data is not lost after it is submitted. The storage writing service can run as an independent worker role, allowing ingestion and storage to scale independently and preventing the monitoring pipeline from becoming a system bottleneck.

#### Telemetry data lifecycle management

Not all telemetry needs to live forever. Be clear on how long you want to retain based on the use of the data.  For example, raw debugging data may only require short-term retention, whereas performance metrics, historical trends, or auditing records may need long-term storage.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Telemetry data can grow significantly even within a short period of time. Set clear retention policies so that you keep the data that truly matters while letting go of what doesn't. Like spot trends, anticipate capacity issues within the right time window. At the same time, you avoid paying for storage you don't really need.

In addition to retention policies that archive older data to lower-cost storage, there are other techniques to optimize costs. Where appropriate, down-sample it to reduce storage usage while preserving sufficient resolution for analysis. Aggregate related events to reduce storage footprint and process data faster.

#### Telemetry data security

While telemetry isn't workload functional data, it's still critical from an operational perspective. From a security point: protect data against accidental deletion; control access with role-based access control (RBAC). Consider the use of:

- Enable soft delete to allow recovery within a defined period. Most Azure storage services offer soft delete. Refer to product documentation for details on the service you've chosen for your workload.

- Apply [resource locks](/azure/azure-resource-manager/management/lock-resources) to prevent unintended changes or deletions to important resources.

- Sensitive data must be encrypted and scrubbed of personally identifiable information. If your workload needs to store telemetry for regulatory purposes, follow the governance policies according to those compliance requirements. 

## Phase 3 - Correlation, aggregation, analysis

Once telemetry is collected and stored, the next step is to turn raw data (logs, metrics, and traces) into insight. The outcome of this phase is to understand *what does the data mean*?

Analyzing telemetry begins by structuring data around defined KPIs and performance metrics. Start by asking these questions:

- Are users experiencing failures?
- Is performance degrading?
- Are dependencies slowing down?
- Is capacity reaching limits?
- Are business KPIs trending negatively?

To prepare data for analysis, a common step is to **aggregate data from multiple sources**. For example, for distributed tracing, aggregation involves combining events with the same activity or transaction ID.

:::image type="content" source="_images/service-instrumentation-data.png" alt-text="Diagram that shows an example of using a service to consolidate instrumentation data." lightbox="_images/service-instrumentation-data.png" border="false":::

Data preparation is another task during aggregation. Here, duplicates are removed, and irrelevant data is filtered out. Consolidation or partitioning services can periodically retrieve, preprocess, and route data to appropriate storage. For example, data needed for alerts or rapid analysis should be stored in fast, indexed storage, and local copies may reduce alert latency.

#### Best practices for alerts

Analysis can also generate alerts to notify you when action is needed. Here are some best practices:

- Define thresholds based on historical data.
- Include sufficient context and filter out false alarms.
- Integrate with automated workflows like your support ticketing. systems, auto-remediation, scaling actions.
- Assign ownership to the responsible team.
- Set prioritization indicators like severity, which separate critical issues from informational notifications.

#### Example: Correlation, aggregation, analysis

Let's continue with the eCommerce example. With instrumentation in place, the team detected that checkout failure rate increased from 1% to 8% in 10 minutes. 

//Art coming soon


Analysis revealed that the cause was the dependency on the database, which was under heavy load, because:

- Application logs showed errors with timeout exceptions
- Database latency doubled during that time
- Platform logs showed that CPU on database server is at 90%

By retaining analysis data over time, teams can perform historical analysis to uncover trends. For example, consistent increase in database load or gradually rising response times can indicate a change in architecture or optimization. For example, scaling the database or optimizing queries may be necessary.

## Phase 4 - Visualization

With analysis in place, the next stage is to take action. *How do we make the insights visible and ensure the right people can act on them?*

Visualization converts complex telemetry into actionable insights using dashboards, charts, and reports.

#### Best practices for visualization

- **Align with your [health model](./health-modeling.md)** that show components should be labeled as Healthy, Degraded, or Unhealthy according your service level objectives (SLOs).

- **Prioritize the display of actionable data**. Avoid cluttering dashboards with non-critical details. Focus on information or trends that support decisions. For example, environment, service, region

- **Support interactive exploration**. Allow filtering by time range, service, or deployment. Enable ad-hoc queries to investigate anomalies.

- Tailor dashboards for intended audience. For example, 

   |Audience| View|
   |---|---|
   |Developers| Real-time logs, traces, error trends|
   |Operators|	KPIs, resource utilization, alerts|
   |Business stakeholders| High-level metrics, revenue-impact KPIs|

- Tailor dashboards for intended use. For example, 

   |Use| View|
   |---|---|
   |Operational reports|Resource usage, trends, exceptions, efficiency|
   |Security reports|Audit user actions, track anomalies for compliance|
   |Business reports| KPI trends, revenue per user, transactions over time|

#### Example: Visualization

Let's return to the eCommerce application. A developer oriented dashboard visualizes:

- Latency trends over the past 24 hours
- Failure rates by endpoint
- Database CPU usage
- Orders processed per minute

From this dashboard, a developer can narrow in investigations about failure spikes when database CPU exceeds 85%. Meanwhile when latency exceeds 2 seconds for the checkout user flow, developer sees request ID, database load, and recent errors. At this point, with sufficient testing an automated workflow is triggered that scales database tier. 

## Monitoring antipatterns and how to avoid them

The following antipatterns commonly undermine what monitoring can do for workloads. 

| Antipattern | Guidance |
|---|---|
|**Logging too Little, or too much**<br><br>- Missing contextual information required for troubleshooting. <br>- Excessive verbosity creating noise, high ingestion costs, and slow queries.|Strive for high signal, low noise: <br>- Define logging standards at design time.<br>- Log structured data with consistent fields.<br>- Include contextual fields such as: Correlation ID, User/tenant ID (when allowed), Operation name, Environment.<br>- Use log levels properly (Debug, Info, Warning, Error).<br>- Implement sampling for high-volume components.<br>- Periodically review log usefulness and cost.|
|**Missing correlation across services**<br><br>Correlation IDs are not propagated across services. Logs, metrics, and traces are analyzed in isolation.|Enable end-to-end traceability: <br>- Generate at the entry point if not included in the request, propagate the provided one if included.<br>- Adopt distributed tracing standards.<br>- Ensure logs, metrics, and traces share common identifiers.<br>- Correlate telemetry types during analysis.|
|**Ignoring business context**<br><br>Monitoring is treated as purely technical and disconnected from business objectives. Only technical metrics (CPU, memory, latency) are emitted and analyzed.|Align monitoring to business value: <br>- Define business KPIs alongside technical SLIs.<br>- Instrument business events.<br>- Build dashboards that show business KPIs with supporting technical indicators.<br>- Align alerts severity with customer impact.|
|**Unconfigured or mismanaged telemetry**<br><br>Logging is enabled without proper diagnostic configuration. All telemetry is stored in one place without purpose or categorization.|Design telemetry architecture intentionally: <br>- Define telemetry categories (Operational, Security, Audit, Business).<br>- Route data to appropriate stores based on usage.<br>- Configure retention and indexing policies.<br>- Regularly audit diagnostic settings.|
|**Poor data governance**<br><br>Logs are considered harmless technical artifacts rather than regulated data. Raw telemetry is kept indefinitely. Sensitive data appears in logs. Retention and compliance are ignored.|Treat monitoring data as production data: <br>- Define retention policies per data type.<br>- Mask or avoid logging sensitive data (PII, secrets).<br>- Enforce encryption in transit and at rest.<br>- Automate lifecycle management aligned with compliance requirements.|
|**Ignoring monitoring costs**<br><br>No planning for telemetry growth, ingestion volume, or storage expansion.|Plan for observability cost in your cost model: <br>- Estimate telemetry volume during design.<br>- Model ingestion and retention costs.<br>- Apply sampling, aggregation, and tiered storage.<br>- Regularly review cost versus value.|
|**Inconsistent and fragmented telemetry**<br><br>Different teams emit logs in different schemas and naming conventions. Multiple disconnected tools are used.|Standardize observability practices: <br>- Establish naming conventions and common schemas.<br>- Define tagging taxonomy.<br>- Provide shared logging libraries or SDKs.<br>- Implement centralized governance and periodic reviews.|
|**Poorly designed dashboards**<br><br>Dashboards are cluttered, overly complex, or not aligned with KPIs or system health model.|Design dashboards for decisions: <br>- Define audience (Executive, Ops, Engineering).<br>- Align to health model (Availability, Latency, Errors, Saturation).<br>- Focus on clarity and actionable insights.<br>- Remove decorative or redundant metrics.|
|**Alert fatigue**<br><br>Too many alerts, no severity differentiation, no ownership, and alerts lack context.|Make alerts actionable: <br>- Classify alerts (Critical, Warning, Informational).<br>- Define ownership per alert.<br>- Include contextual metadata in alerts.<br>- Use dynamic thresholds where appropriate.<br>- Regularly prune unnecessary alerts.|
|**No trend analysis or forecasting**<br><br>Monitoring focuses only on real-time failures with no long-term insight.|Enable proactive monitoring: <br>- Implement weekly/monthly trend dashboards.<br>- Analyze error rates, latency, and capacity growth.<br>- Forecast scaling needs and cost growth.<br>- Include observability insights in architecture reviews.|


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
- [Recommendations for designing a reliable monitoring and alerting strategy](../reliability/monitoring-alerting-strategy.md)