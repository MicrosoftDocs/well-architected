---
title: Architecture Strategies for Designing a Monitoring System
description: Learn the recommendations for designing and creating an observability system. The system provides a foundation for monitoring, detection, and alerting.
author: claytonsiemens77
ms.author: csiemens
ms.date: 02/11/2026
ms.topic: concept-article
---

# Architecture strategies for designing a monitoring system

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:** 

|**OE:07**| Design a monitoring stack that captures operational telemetry, metrics, and logs from the workload's infrastructure and code to validate design decisions and guide future improvements.
|---|---| 

Observability, or monitoring, is a key operational practice that gives the workload team the ability to understand the internal state of a system based on the external data it produces. Unlike the functional stack, which implements business logic and core features, the monitoring stack runs in parallel. It collects and analyzes metrics, logs, traces, and events that show how workloads behave under real conditions.

Designing the monitoring stack requires careful planning because it provides visibility into cross-cutting concerns like reliability, performance, security, and cost. A well-architected monitoring stack enables early issue detection, effective incident response, and informed operational decisions. It forms the foundation for proactive management and continuous improvement.

This guide describes key strategies for designing a monitoring stack that supports monitoring, detection, and alerting functions. For  implementation guidance, including stepwise processes and playbooks, see the companion article: [Build a monitoring system for Azure workloads](../design-guides/monitoring.md).


**Definitions**

| Term | Definition |
|---|---|
| **Telemetry** | A collective term for logs, metrics, traces, and events. Telemetry provides the foundation for observability. |
| **Logs** | Recorded system events that capture what happened in the system. Logs can be structured or free-form text with timestamps. They're useful for detecting and investigating anomalies. |
| **Metrics** | Numerical values collected at regular intervals that describe system performance. Metrics help identify trends in workload performance and reliability. |
| **Observability** |A practice that helps teams detect issues, track performance trends, and make operational decisions. |
| **Correlation IDs** | Unique identifiers that track related events across multiple components to enable end-to-end tracing of transactions in distributed systems. |
| **Instrumentation** | Adding monitoring capabilities to applications and infrastructure to capture telemetry. Captured telemetry includes logging, metrics collection, and tracing. |
| **Health model** | A framework for measuring workload health by using indicators, KPIs, and metrics that reflect business and operational objectives. |
| **Key performance indicators (KPIs)** | Measurable values that show how effectively a workload achieves business and operational objectives. KPIs guide telemetry collection and analysis. |
| **Application performance management (APM)** | Tools and practices for monitoring application performance, availability, and user experience. APM tools provide real-time and historical visibility into important metrics. |
| **Traces** | Records that show the paths of requests through distributed systems. Traces help diagnose problems that span multiple services. |

## Align telemetry with health and KPI models

Define workload health indicators, KPIs, and performance metrics so that telemetry collection strategies reflect these targets. Track these indicators to detect anomalies so that you can make decisions on corrective action.

Tie telemetry to system and user flows, and model those flows as entities in your health model. This approach connects application-level health to resource-level signals and overall workload health, so degradation in a dependency surfaces at the level where business impact is visible.

> :::image type="icon" source="../_images/ai.svg"::: **AI opportunity:** Teams spend time manually defining KPIs and telemetry. AI-assisted tools can suggest commonly used telemetry based on architecture, service dependencies, and code. Tools like GitHub Copilot or Claude Code can also help add instrumentation and generate queries or infrastructure as code (IaC) templates. Include human oversight to ensure that AI-driven observability stays accurate and aligned with standards.

## Emit telemetry from workload components

Capture meaningful signals from application, infrastructure, and operations. Log critical exceptions with sufficient detail, but allow verbosity to be adjusted to control noise. 

Prefer structured telemetry so that the data is queryable and searchable. Use consistent schemas and include contextual information like the source component, timestamps, and so on. 
Strive for consistency because that enables more accurate analysis of events and clearer correlation with user requests. To achieve this, adopt a configurable logging framework that standardizes how information is captured across the system.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Increase logging detail to improve debuggability and traceability, but be aware that there's higher storage and processing costs. To manage this tradeoff, use verbose logging in development and reduced verbosity in production, and rely on correlation IDs to preserve end-to-end transaction visibility without excessive log volume.

Have a way to classify telemetry by operational concern, such as audit, security, debugging, and performance, to simplify filtering and enforce proper access controls. Make sure workload data doesn't get mixed with telemetry. Scrub sensitive system or user information before logging, while preserving enough context for diagnostics.

Ensure that instrumentation practices are operationally safe. Logging should be fire-and-forget so it doesn't block business operations, except for critical auditing scenarios. Keep instrumentation extensible and decoupled from specific backends, and ensure failures in telemetry do not cascade into application failures.

Treat instrumentation as an iterative discipline. Regularly review and refine telemetry to maintain clarity, relevance, and performance as the system evolves.

> [!NOTE]
>
> Application profiling can be another way of analyzing how a running application uses system resources, such as CPU, memory, disk I/O, and network. A profiler attaches to your application (during development or in production) and collects detailed runtime data. There are two approaches: full profiling or sample-based. Full profile is more precise but can add significant burden and slow down the system. Opt for sample-based where data is collected based on time, such as once every n seconds, or frequency, such as once every n requests. If events are frequent, use sampling to reduce overhead. If events are rare, use more full profiling so you don't miss them.

## Collect telemetry across the workload

Telemetry systems use two fundamental collection models. In a pull model, a component collects telemetry by querying a target system. In a push model, components emit telemetry as they send data outward. Choose a model based on factors that apply to your workload. For instance, are periodic snapshots sufficient, or do you need near real-time data? What's the expected telemetry volume? What's the data type: state-based or logs, events, and traces?

It's common to use a combined approach. For example, monitoring agents can use a pull model, running locally alongside each application instance to periodically collect data and write it to shared storage. At the same time, you can use a push model for application telemetry, where each instance emits logs, traces, and metrics to a message queue or eventstream as events occur.

Prioritize data transfer based on importance. Less urgent data can be transferred in batches, but time-sensitive information should be sent immediately.

## Standardize data consolidation

Move telemetry out of local silos and consolidate it into a central repository, if that's mandated by the organization. For multiregion solutions, collect and store data regionally first, and then aggregate it centrally. However, for business-critical workloads, we recommend autonomous storage of data. 

Use consistent formats and collection methods so that data can be accessed for analysis, dashboards, alerting, and reporting. Avoid manual retrieval from components, because it adds overhead and inconsistencies.

Use data consolidation services to:

- Deduplicate data.
- Merge related events by using correlation IDs.
- Filter unnecessary information.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Be aware that there are cost implications to having regional and centralized data stores.

## Tailor storage and retention for usage patterns

Select storage solutions primarily based on query needs and access patterns. For instance, data that generates alerts must be accessed quickly, so it should be held in fast data storage and indexed or structured to optimize the queries. 

Use polyglot persistence to store different data types in technologies that are suited for their use:

- SQL databases for performance counters
- Azure Monitor logs or Azure Data Explorer for trace logs
- Hadoop Distributed File System (HDFS) for security information

Also, separate data storage by environment. This separation prevents noncritical environment data from complicating production monitoring.

Plan retention based on how you'll use the data. Keep high-resolution data for short-term analysis and debugging, and retain lower-resolution aggregates for long-term trends. Move older or infrequently accessed data to cheaper storage, and keep recent data in faster systems for quick analysis. This balances performance with cost. Set retention periods to match operational needs and compliance requirements so data is available when needed without unnecessary storage overhead.

Treat monitoring data like any other critical data. Apply appropriate protection—access control, soft delete, and safeguards against accidental changes. 

## Correlate data for end-to-end insights

Design observability to connect telemetry from metrics, logs, and traces across all components. This design enables distributed tracing of operations across services, which helps you diagnose problems that span multiple tiers.

Use correlation IDs consistently to track transactions through presentation, middle, and data tiers.

Aggregate application-level and resource-level logs to improve troubleshooting and speed up issue detection. Consider a unified solution, like Azure Log Analytics, to query and analyze data across levels.

Align telemetry with system and user flows to correlate flow health with overall workload health. Understanding these flows helps you ensure that your observability strategy reflects both component-level and end-to-end system behavior.

## Analyze and visualize to support actionable decisions

Design dashboards and reports around operational health models. Visualizations should allow teams to quickly identify problems, understand trends, and prioritize responses.

A health model provides the semantic layer between telemetry and operational decisions. Instead of metric-based dashboards, visualize health states which drill-down from workload-level health to individual resources. Take advantage of Azure Monitor health models to get built-in visualizations of health state across the entity hierarchy and API access for integrating health data into tools like Grafana.

Use proven monitoring patterns and architectures rather than custom implementations or ad hoc solutions. Ensure that dashboards are meaningful and actionable. Analysts can use parameterized dashboards to explore underlying data.

For database workloads, evaluate built-in monitoring dashboards that cloud services provide. For example, Azure Database for PostgreSQL offers [built-in Grafana dashboards](https://aka.ms/azure-postgres-dashboards-grafana) in the Azure portal through Azure Monitor integration. These dashboards show CPU usage, storage, active connections, and query throughput with log correlation, reducing the need for separate monitoring deployments.

> :::image type="icon" source="../_images/ai.svg"::: **AI opportunity:** Dashboards often focus on either business or engineering metrics. AI can analyze data from all relevant sources and help you design integrated dashboards with the right configurations and visualization. This design reduces manual effort and surfaces insights that might otherwise be overlooked.

## Define alerts around meaningful operational conditions

Set alerts based on workload health, not arbitrary values. Alerts should be actionable and provide context. Establish a clear, accountable alerting process that defines owners, actions, and scope. Configure alerts with appropriate granularity and verbosity to minimize noise while ensuring that critical problems are promptly detected.

Use a health model that aggregates multiple correlated signals into health states, then alert on state transitions, not isolated metric thresholds.

Validate thresholds based on past experience and regular testing. Use fast storage for alert-generating data to enable rapid notification. Configure alerts for well-defined scopes and adjust verbosity to minimize noise.

Automate alerting and link alerts to ticketing systems. Monitor cloud platform service health, outages, maintenance, and advisories.

AI-powered operations tools such as [Azure SRE Agent](https://aka.ms/sreagent/ga) can analyze alert patterns and diagnose common issues like pod crash loops or elevated error rates. These tools support configurable autonomy, starting with recommended actions and progressively enabling automated responses within defined guardrails.

> :::image type="icon" source="../_images/ai.svg"::: **AI opportunity:** You can use AI to dynamically define "healthy" system behavior. Use AI to learn patterns across business contexts like peak traffic, promotions, quiet periods, and regional variations. AI can then analyze metrics, logs, and incident data to predict problems and recommend thresholds.

## Design scalable, durable telemetry pipelines

Observability systems must handle high scale without bottlenecks or data loss. Include buffering, queuing, and scalable ingestion paths to maintain telemetry flow under load.

Use queuing mechanisms for high-scale environments to handle spikes. Implement redundancy to prevent losing important data. Plan for scaling during design to ensure that monitoring systems grow with workload demands.

For complex workloads, use message queues with *at-least-once* semantics. Run multiple storage-writing services to handle high volumes. Consider using Azure Event Hubs to distribute telemetry processing and prevent single-point I/O bottlenecks.

## Use observability to support continuous improvement

Treat observability as a feedback loop. Use production data to refine workload design, telemetry capture, and monitoring thresholds.

Balance automation and human oversight to ensure accuracy. Continuously review and evolve monitoring approaches as workloads change. Use telemetry to identify optimization opportunities, validate architecture decisions, and guide future designs.

Include monitoring and alerting in overall workload testing. Automate functions while keeping the ability to analyze trends for predicting operational issues and planning capacity.

## Watch out for antipatterns

Many monitoring failures stem from poor architectural choices rather than tooling limitations. 

Don't just fix the symptoms, but analyze why the antipattern emerged and address the underlying design weakness. Then apply mitigation whether that's using clear telemetry standards, drive towards business-aligned metrics, or cost awareness.

We recommend reading this section in the companion implementation guide: [Antipatterns and how to avoid them](../design-guides/monitoring.md#monitoring-antipatterns-and-how-to-avoid-them). 

## Azure facilitation

- [Azure Monitor](/azure/azure-monitor/overview) is a  monitoring solution for collecting, analyzing, and responding to monitoring data from your cloud and on-premises environments. 

- [Azure Monitor health models](/azure/azure-monitor/health-models/overview) help you define, measure, and visualize workload health by correlating metrics, logs, and traces into actionable health states across Azure resources and components.

- [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview) is a tool in the Azure portal that you can use to edit and run log queries against data in the Log Analytics workspace.

   If you're using multiple workspaces, see the Log Analytics workspace [architecture guide](/azure/azure-monitor/logs/workspace-design) for best practices.

- [Application Insights](/azure/azure-monitor/app/app-insights-overview) is an extension of Azure Monitor. It provides APM features.

- [Azure Monitor Insights](/azure/azure-monitor/insights/insights-overview) are advanced analytics tools for specific Azure technologies (like VMs, app services, and containers). These tools are part of Azure Monitor and Log Analytics.

- [Azure Monitor for SAP solutions](/azure/sap/monitor/about-azure-monitor-sap-solutions) is an Azure monitoring tool for SAP landscapes that run on Azure.

- [Azure Policy](/azure/governance/policy/overview) can help you enforce organizational standards and assess compliance at scale.

- [Azure Network Watcher](/azure/network-watcher/network-watcher-overview) is a tool that monitors, manages, and audits your network to ensure security, compliance, and performance.

- [Connection troubleshoot](/azure/network-watcher/connection-troubleshoot-overview) is a diagnostic tool in Network Watcher. It provides on-demand diagnostics and packet capture (PCAP) to help investigate connectivity problems.

- [Connection monitor](/azure/network-watcher/connection-monitor-overview) is a monitoring tool in Network Watcher. It runs continuous synthetic tests and sends real-time alerts for connectivity and performance problems.

- [Traffic analytics](/azure/network-watcher/traffic-analytics) is a traffic analysis solution in Network Watcher. It visualizes traffic distribution, identifies top talkers, and reveals bandwidth utilization trends. These capabilities provide a unified view of network health.

- Virtual network flow logs now have a [native Microsoft Sentinel connector](https://aka.ms/TrafficAnalytics-with-Sentinel) that exports network telemetry to your organization's SIEM. This integration brings flow data into the same analytics plane as identity and endpoint signals, improving correlation during security investigations. Govern log volume and analytics costs as you scale ingestion.

## Related links

- [Instrumentation guide](../devops/monitor-instrument.md)
- [Recommendations for designing a reliable monitoring and alerting strategy](../reliability/monitoring.md)
- [Recommendations for monitoring and threat detection](../security/monitor-threats.md)
- [Recommendations for collecting performance data](../performance-efficiency/monitoring.md)

## Community links

- [Azure Monitor Baseline Alerts (AMBA)](/azure/azure-monitor/alerts/alert-options#azure-monitor-baseline-alerts-amba) is a central repository of alert definitions that customers and partners can use to improve their observability experience through the adoption of Azure Monitor.

## Operational Excellence checklist

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Operational Excellence checklist](checklist.md) 

<!-- Updated: 2026-03-21 for Azure Update 558140 -->
<!-- Updated: 2026-03-21 for Azure Update 558321 -->
<!-- Updated: 2026-06-04 for Azure Update 564689 -->
