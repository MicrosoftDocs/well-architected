---
title: Design a monitoring system for your Workload
description: Learn how to design and implement effective monitoring solutions for Azure workloads, including observability, alerting, and performance optimization strategies.
author: PageWriter-MSFT 
ms.author: prwilk 
ms.date: 11/18/2025
ms.topic: concept-article
ms.update-cycle: 1095-days  
---

# Design a comprehensive monitoring strategy for Azure workloads

Monitoring is the structured practice of collecting and analyzing telemetry to understand system health and behavior. Its purpose is simple: make system state observable through measurement. Effective monitoring connects technical signals to operational outcomes, enabling teams to detect anomalies, investigate failures, and make informed decisions about evolving the system.

Think of monitoring as its own system, with its own stack and data flows. Treat it as a distinct dimension of the workload, decoupled from business logic. The monitoring stack should span all layers, including infrastructure, application health, and build and release processes, so visibility is consistent and complete. Monitoring must be treated as a core architectural capability, designed with the workload, not added later.

As workloads grow more complex, monitoring must scale with them, from basic metrics in simple applications to cross-service correlation and structured analysis in distributed systems. In all cases, monitoring should align with service objectives and stakeholder needs.

The monitoring system follows a linear progression and can be divided into four logical stages, each building on the insights of the previous one.

:::image type="content" source="media/observability/monitor-pipeline.png" alt-text="Diagram showing the end to end flow of a monitoring system. It breaks monitoring into four logical stages, each building on the previous one.." lightbox="media/observability/monitor-pipeline.png" border="false":::

This guide explores those phases and provides best practices that you can use to design a monitoring system from signal to action. It doesn't cover specialized monitoring like security, reliability, or performance monitoring. The guidance builds on the key strategies outlined in  [Architecture strategies for designing a monitoring system](../operational-excellence/observability.md), which you should review first.

## Terminology

| Term | Definition |
|---|---|
|**Logs** are timestamped records of discrete events.
|**traces** record the path of a request.
|**Metrics** are numeric measurements captured at a point in time,

## Decide where to add instrumentation

_Instrumentation_ is the practice of embedding code or tools within workload components to generate telemetry that reveals system behavior. Effective instrumentation follows two core principles: it must be end-to-end, capturing different types of signals across critical components of the workload; and it must be structured and searchable to support efficient querying and correlation.

Critical components that should be instrumented to emit telemetry are:

- **Application code** – Instrument core business logic and critical operations to capture events, metrics, and traces.  
- **Frameworks and runtime** – Capture performance and operational signals from the underlying frameworks and runtime environments that support your applications.  
- **Operating system** – Monitor CPU, memory, network, and other system-level resources to understand the environment in which your applications run.  
- **Infrastructure resources** – Include virtual machines, containers, storage, networking, and other platform components that impact workload performance and availability.  
- **Dependent services** – Track interactions with databases, web services, and other external or internal services that your workload relies on.  
- **Build and release pipeline** – Capture telemetry from deployment and CI/CD processes to provide context for changes and incidents.

Consider what type of signal you want to emit from those sources: logs, traces, and metrics.

#### Logs and traces

Monitoring relies on both logs and traces to provide visibility into system behavior. _Logs_ are timestamped records of discrete events, while _traces_ capture the path of requests and operations. Distributed tracing is valuable in distributed systems where where a single request may traverse multiple services, threads, or machines. 

**Prefer structured and human- and machine-readable formats that use consistent schemas**, so that they are easier to parse and query.

Make logs concise and focus on the point. Data should **include key contextual information** such as a timestamp, the source component, environment and deployment details, and so on. Ensure timestamps are written at log creation and formatted consistently. Additional contextual data, such as the user or activity ID associated with the event, helps support meaningful correlation across components and systems.

**Make log verbosity configurable**. Constant logging can consume storage and processing resources unnecessarily, so detailed logs should be enabled primarily for troubleshooting or diagnostic purposes. **Categorize logs by operational concern**. For example, audit, security, debugging, and performance logs should be separated to prevent sensitive information from mixing with other data and to make filtering and analysis more efficient.

Logs can be stored using various technology options, including files, blobs, or structured storage such as database tables. **Choose the right storage option**. It should be efficient in parsing, querying, and long-term retention in line with your monitoring strategy.

> [!NOTE]
>
> Capture all relevant data to make logs actionable. Record requests and their locations or regions to identify hotspots and guide decisions such as application or data repartitioning. Log exceptions thoroughly, including inner exceptions, call stacks, and contextual information, so critical debug details are preserved.
>
> Instrument all external calls, including database queries, web service calls, infrastructure service interactions  
>
> For each dependency, record duration, success or failure, retry attempts, and error details. Patterns of transient failures can serve as early indicators of broader system instability.


### Metrics

_Metrics_ are numerical measurements of a system or resource at a specific point in time, often accompanied by one or more tags or dimensions. Unlike logs or traces, a single metric on its own provides limited insight; metrics are most valuable when captured continuously over time to reveal trends, patterns, and anomalies.

**Identify which aspects of the system are critical to monitor** and determine the appropriate frequency for data collection. Capture too often, and you risk overloading the system; capture too infrequently, and you may miss important events or spikes. For example, CPU usage on a server can fluctuate rapidly from second to second, but sustained high usage over several minutes is typically what indicates a problem.

#### Facilitate correlation across components

Telemetry is most valuable when it can be **correlated across components**. Individual metrics, logs, or traces provide insight at a single layer, but understanding overall system behavior requires connecting data across the entire stack. For example, a thread ID might identify a task within a framework, while the same work could correspond to a user request in the application layer. By propagating identifiers and contextual information through all components, monitoring systems can link events, metrics, and traces into a unified view, supporting root cause analysis, performance optimization, and operational decision-making.

Correlation is not always straightforward. Threads can be reused for multiple asynchronous operations, and a single request may span multiple threads or services. To address this complexity, **assign a unique activity ID to each request or operation and propagate it throughout its lifecycle**. The exact method for generating and including activity IDs depends on the technology used for tracing, but the principle remains the same: every unit of work should be identifiable across processes and machines.

**Provide sufficient context** with each telemetry event to enable traceability. This should include environment details, process and machine identifiers, dependencies, and, when relevant, the customer or user who initiated the request. Capturing this context allows teams to correlate each activity with the computational work performed, the resources consumed, and the application state at the time of the event. Context should support correlation across processes, threads, and machines, ensuring telemetry can be reliably analyzed in distributed systems.

All telemetry should be **timestamped consistently using Coordinated Universal Time (UTC)**. While timestamps are essential for correlation, they are not sufficient on their own, as machines in different time zones or networks may not be perfectly synchronized. Correlation should rely on unique identifiers and contextual information in addition to timestamps.





Strive for data consistency
Consistent data can help you analyze events and correlate them with user requests. Consider using a comprehensive and configurable logging package to gather information. Logging packages can help you avoid dependence on developers to adopt your approach as they implement different parts of the system.

Gather data, such as input/output volume, number of requests, and memory, network, and CPU usage, from key performance counters. Some infrastructure services provide their own performance counters, such as:

The number of connections to a database.
The transaction rate.
The number of transactions that succeed or fail.
Applications might also define their own performance counters.

Consider external dependencies
Log all external service calls. Externals calls might be made to:

Database systems.
Web services.
Other system-level services that are part of the infrastructure.
Record information about the duration of each call and the success or failure of the call. If possible, capture information about all retry attempts and failures for any transient errors that occur.

Ensure telemetry system compatibility
In many cases, the instrumentation information is generated as a series of events and passed to a separate telemetry system for processing and analysis. A telemetry system is typically independent of any specific application or technology.

Telemetry systems use defined schemas to parse information. The schema specifies a contract that defines the data fields and types that the telemetry system can ingest. Generalize the schema to allow for data arriving from various platforms and devices. A common schema should include fields relevant to all instrumentation events, such as:

Event name.
Event time.
IP address of the sender.
Details required for event correlation, including:
User ID
Device ID
Application ID
Remember that many devices can raise events for the same application, so the schema shouldn't depend on the device type. The application should support roaming or cross-device distribution. The schema can also include relevant domain fields for a particular scenario that's common across applications, such as:

Information about exceptions.
Application start and end events.
Success or failure of web service API calls.
Establish domain fields that produce the same set of events to build a set of common reports and analytics across applications. You might need to configure a schema to contain custom fields for capturing the details of application-specific events.

OpenTelemetry is a vendor-neutral collection of APIs, SDKs, and other tools. You can use OpenTelemetry to instrument applications and generate meaningful telemetry consistently across languages. OpenTelemetry is tool-agnostic, so it's compatible with many observability platforms including open-source and commercial offerings. Microsoft is adopting OpenTelemetry as the standard tool for instrumentation.

Optimize instrumentation code
The following list summarizes best practices for instrumenting a distributed application running in the cloud:

Make logs easy to read and easy to parse. Use structured logging where possible.

Be concise and descriptive in log messages.

Identify the source of the log.

Add timestamp information as each log record is written.

Use the same time zone and format for all timestamps.

Categorize logs and write messages in the appropriate place.

Don't reveal sensitive information about the system or personal information about users. Scrub this information before it's logged, but keep any relevant details.

Log all critical exceptions but enable the administrator to turn logging on and off as needed for fewer exceptions and warnings.

Capture and log all retry logic information. This data is useful in monitoring the transient health of the system.

Trace out process calls, such as requests to external web services or databases.

Don't mix log messages with different security requirements in the same log file.

Ensure that all logging calls are fire-and-forget operations that don't block the progress of business operations. Exclude auditing events from this rule because they're critical to the business.

Ensure that logging is extensible and doesn't have any direct dependencies on a concrete target.

Ensure that all logging is fail-safe and doesn't trigger cascading errors.

Treat instrumentation as an ongoing iterative process and review logs regularly.

Use application profiling
Implement profiling only when necessary because it can impose a significant overhead on the system. By using instrumentation, profiling records an event, such as a method call, every time it occurs. However, sampling records only selected events.

Profiling selections can be time-based, such as once every n seconds, or frequency-based, such as once every n requests. If events occur frequently, profiling might cause too much of a burden on the system and affect overall performance. In this case, the sampling approach is preferable. However, if the frequency of events is low, sampling might miss them. In this case, profiling might be the better approach.


## Terminology

| Term | Definition |
|---|---|
| **Alerting threshold** | The specific value or condition that triggers a notification when system metrics exceed normal operating parameters. |
| **Baseline** | The normal performance characteristics and behavior patterns of a system during typical operations, used as a reference for anomaly detection. |
| **Dashboard** | A visual interface that displays real-time and historical monitoring data, metrics, and system health indicators in an organized format. |
| **Golden signals** | The four key metrics for monitoring distributed systems: latency, traffic, errors, and saturation. |
| **Health model** | A structured approach to defining and measuring system health based on business-critical functions and performance indicators. |
| **Observability** | The ability to understand the internal state of a system based on the data it produces, including metrics, logs, and traces. |
| **Service level indicator (SLI)** | A quantitative measure of service performance, such as response time, availability, or error rate. |
| **Service level objective (SLO)** | A target level of service performance defined by specific SLI thresholds over a time period. |
| **Signal-to-noise ratio** | The proportion of useful, actionable alerts compared to false positives or non-critical notifications. |
| **Telemetry** | The automated collection and transmission of data from systems, applications, and infrastructure for monitoring and analysis. |

## 


Effective monitoring must satisfy the following design principles:

| Property | Meaning |
|---|---|
| **Comprehensive** | All critical components (infrastructure, platform, application, network) emit telemetry. |
| **Structured & searchable** | Logs and metrics follow schemas enabling query, correlation, and alerting. |
| **Observable** | System state and trends are visible in dashboards and analysis tools. |
| **Actionable** | Alerts have clear owners, severity, and documented responses. |
| **Cost-aware** | Telemetry retention and processing balance operational value with cost. |
| **Continuous improvement** | Telemetry collection and alert definitions evolve based on real behavior. |


## Terminology

| Term | Definition |
|---|---|
| **Alerting threshold** | The specific value or condition that triggers a notification when system metrics exceed normal operating parameters. |
| **Baseline** | The normal performance characteristics and behavior patterns of a system during typical operations, used as a reference for anomaly detection. |
| **Dashboard** | A visual interface that displays real-time and historical monitoring data, metrics, and system health indicators in an organized format. |
| **Golden signals** | The four key metrics for monitoring distributed systems: latency, traffic, errors, and saturation. |
| **Health model** | A structured approach to defining and measuring system health based on business-critical functions and performance indicators. |
| **Observability** | The ability to understand the internal state of a system based on the data it produces, including metrics, logs, and traces. |
| **Service level indicator (SLI)** | A quantitative measure of service performance, such as response time, availability, or error rate. |
| **Service level objective (SLO)** | A target level of service performance defined by specific SLI thresholds over a time period. |
| **Signal-to-noise ratio** | The proportion of useful, actionable alerts compared to false positives or non-critical notifications. |
| **Telemetry** | The automated collection and transmission of data from systems, applications, and infrastructure for monitoring and analysis. |

## Planning and design

Before implementing monitoring solutions, establish a comprehensive strategy that aligns observability requirements with business objectives. Define what success looks like for your workload, identify critical monitoring targets, and design data collection approaches that provide actionable insights without overwhelming operations teams.

1. **Define monitoring ownership and roles** to ensure accountability and clear responsibilities:

   Assign the following key responsibilities:

   | Role | Responsibilities |
   |------|------------------|
   | **Monitoring Owner** | Accountable for the end-to-end monitoring pipeline (data collection → storage → alerts → dashboards). |
   | **Telemetry Instrumentation Lead** | Ensures application and infrastructure emit structured telemetry reliably. |
   | **Alert Operator** | Maintains alert rules, severity definitions, and response playbooks. |
   | **Dashboard Consumer/Reviewer** | Business or platform stakeholders who validate health KPIs. |

   > [!TIP]
   > Embed monitoring responsibilities in team SLOs and sprint plans to ensure continuity.

1. **Establish monitoring requirements** by documenting key indicators and objectives:

   - **Key Health Indicators (KHIs):** Define critical metrics like request latency, error rates, queue depths, and throughput.

   - **Service Level Objectives (SLOs):** Set measurable thresholds tied to business outcomes and user expectations.

   - **Data Retention Needs:** Determine retention periods based on compliance, trend analysis, and troubleshooting windows.

   These requirements inform telemetry granularity, retention policies, and alert thresholds.

1. Define your workload health model by identifying business-critical functions and their performance requirements:

   - Map user journeys and critical business processes to understand what constitutes healthy system behavior.

   - Establish service level objectives (SLOs) based on user expectations and business requirements for availability, performance, and reliability.

   - Document dependencies between components to understand how failures propagate and which monitoring points provide early warning signals.

   - Identify the golden signals (latency, traffic, errors, saturation) relevant to each component of your workload.

1. Design your telemetry collection strategy with structured instrumentation:

   **Application Tier:**
   - Implement structured logging (JSON) with consistent fields (timestamp, correlation ID, severity, context).
   - Capture application metrics such as throughput, latency, error rates.
   - Use OpenTelemetry or native SDKs to export logs and metrics into your pipeline.
   - Avoid excessive verbosity in production to control cost and storage.

   **Infrastructure Tier:**
   - Enable OS and service-level diagnostics on VMs (performance counters, syslogs).
   - Use platform diagnostics for PaaS services (resource logs, diagnostic settings).
   - Capture Azure Activity Logs and subscription events to track control plane changes.

   **Collection Patterns:**
   Choose the appropriate pattern based on your scale and requirements:

   - **Push Model:** Agents or components emit telemetry to a collector that forwards to storage. Use when immediate dispatch is critical.
   - **Pull Model:** Central collector queries sources on schedule. Use when pull semantics fit resource models.
   - **Hybrid:** Event Hubs → Collector → Storage for high-throughput scenarios with buffering needs.

   > **Decision Checklist:**
   > - Does the workload scale across regions? *Yes* → Use central aggregation with regional buffering.
   > - Do you expect high telemetry volume? *Yes* → Use Event Hubs with multiple consumers.

1. Establish data retention and storage architecture:

   **Storage Technology Selection:**
   Choose storage technologies optimized for different purposes:

   | Telemetry Type | Recommended Storage |
   |---|---|
   | Metrics | Time-series stores (Azure Monitor Metrics) |
   | Logs | Azure Monitor Logs / Log Analytics |
   | Traces | Azure Data Explorer or Log Analytics |
   | Archived data | Blob Storage with lifecycle rules |

   **Retention Strategy:**
   Design retention based on operational utility and cost:
   - **Production hot path:** Retain at high resolution (e.g., 90 days).
   - **Trend analysis:** Down-sample older data (e.g., hourly aggregates after 90 days).
   - **Audit/compliance:** Store long-term in cost-effective storage tiers.

   Implement lifecycle policies to transition data between tiers automatically.
   Ensure role-based access control and resource locks to protect critical telemetry.

1. Design alerting strategies with clear standards and categories:

   **Alert Definition Standards:**
   For each alert, define:
   - **Name and description:** Clear intent and scope.
   - **Severity level:** (Critical / High / Medium / Low).
   - **Owner/team:** Person/team responsible.
   - **Threshold criteria:** Explicit numeric or behavioral triggers.
   - **Response playbook:** Documented action steps and fallback options.

   **Alert Categories:**
   | Category | Example |
   |---|---|
   | Health Alerts | High error rates, service timeouts |
   | Performance Alerts | Latency breaches, saturation indicators |
   | Capacity Alerts | CPU, memory, queue exhaustion |
   | Platform Alerts | Azure service degradations |
   | Cost Alerts | Budget thresholds |

   Keep alerts actionable and eliminate noise by prioritizing signals with clear impact.
   Design alert correlation and grouping to reduce notification fatigue and improve signal-to-noise ratio.

1. Plan dashboard and visualization strategies for different stakeholders:

   **Dashboard Types:**
   - **Operational Dashboards:** Live status for on-call engineers (latency, error rates, queue sizes).
   - **Executive Dashboards:** High-level trends tied to business KPIs (SLA attainment, customer impact patterns).
   - **Drill-down Workbooks:** Interactive views to pivot from symptoms to root causes.

   **Design Guidelines:**
   - Align visuals with workload health states (Healthy/Degraded/Unhealthy).
   - Group related metrics for quick root-cause localization.
   - Include contextual annotations for releases, deployments, or outages for trend interpretation.
   - Consider mobile accessibility for on-call scenarios and emergency response.

1. Establish monitoring governance and maintenance processes:

   - Define responsibilities for monitoring configuration, alert tuning, and system maintenance.

   - Plan regular review cycles for threshold adjustments and monitoring effectiveness.

   - Establish processes for adding monitoring to new components and deprecating obsolete monitoring.

## Implementation and configuration

This phase focuses on implementing the monitoring infrastructure and configuring data collection, alerting, and visualization components. Successful implementation requires careful attention to performance impact, security considerations, and operational sustainability.

1. Deploy monitoring infrastructure following security and performance best practices:

   - Implement monitoring agents and data collection components with minimal performance overhead.

   - Configure secure data transmission and storage with appropriate access controls and encryption.

   - Set up redundant monitoring infrastructure to avoid single points of failure in your observability stack.

   - Implement monitoring for the monitoring system itself to ensure reliability of your observability pipeline.

1. Configure comprehensive data collection following best practices:

   **Correlation Strategy:**
   - Correlate logs and metrics by correlation IDs across services.
   - Combine application and infrastructure data in unified queries (e.g., Kusto in Log Analytics).
   - Validate health models by tying indicators to business flows.
   - Set up predefined queries that map events to known failure or performance patterns.

   **Implementation:**
   - Deploy APM agents and configure custom instrumentation for business-specific metrics.
   - Set up log forwarding from all application and infrastructure components to centralized logging systems.
   - Configure distributed tracing for multi-service applications with appropriate sampling rates.
   - Implement synthetic monitoring to proactively test critical user journeys.

   **Testing Integration:**
   Include monitoring in release pipelines:
   - Validate that instrumentation emits expected telemetry.
   - Verify alert rules fire under simulated conditions.
   - Test dashboards for completeness and performance.

1. Implement intelligent alerting:

   - Configure alerts based on your predefined thresholds and SLO targets.

   - Set up alert correlation rules to group related notifications and reduce noise.

   - Implement alert fatigue prevention through proper threshold tuning and intelligent grouping.

   - Configure notification channels and escalation procedures with appropriate routing rules.

1. Create comprehensive dashboards:

   - Build operational dashboards that display real-time system health and performance metrics.

   - Create executive summary dashboards that show high-level business and system health indicators.

   - Implement team-specific views that highlight metrics most relevant to different roles and responsibilities.

   - Set up automated reporting for regular stakeholder communications.

1. Establish monitoring automation:

   - Implement automated response to common monitoring scenarios where appropriate.

   - Set up auto-scaling triggers based on performance and utilization metrics.

   - Configure automated health checks and self-healing mechanisms for known failure patterns.

   - Implement change tracking to correlate system changes with performance impacts.

## Analysis and optimization

Ongoing analysis of monitoring data drives continuous improvement in both system performance and monitoring effectiveness. This phase focuses on extracting actionable insights, optimizing alert configurations, and evolving monitoring strategies based on operational experience.

1. Conduct regular monitoring effectiveness reviews:

   - Analyze alert patterns to identify false positives, missed incidents, and optimization opportunities.

   - Review dashboard usage and effectiveness to ensure visualizations provide actionable insights.

   - Evaluate monitoring coverage gaps and opportunities for improved observability.

   - Assess monitoring system performance and resource consumption for optimization opportunities.

1. Optimize alerting based on operational experience:

   **Threshold Management:**
   - Adjust thresholds based on baseline shifts and seasonal patterns.
   - Define trend detection rules to catch slow degradation (e.g., 5% daily latency increase over baseline).
   - Use anomaly detection for dynamic thresholding where fixed thresholds are insufficient.

   **Continuous Improvement:**
   - Refine alert correlation rules to improve signal-to-noise ratio.
   - Update escalation procedures based on response effectiveness and team structure changes.
   - Review incidents periodically to adjust thresholds and add missing telemetry.
   - Remove obsolete metrics that no longer drive decisions.
   - Implement machine learning-driven anomaly detection where appropriate.

1. Enhance monitoring based on insights and trends:

   - Add new monitoring for emerging components and changing workload patterns.

   - Implement predictive monitoring for capacity planning and proactive scaling.

   - Expand user experience monitoring based on business growth and changing user patterns.

   - Update retention and archival policies based on actual usage patterns and requirements.

1. Drive performance optimization through monitoring insights:

   - Use performance data to identify optimization opportunities and resource waste.

   - Implement monitoring-driven capacity planning and scaling strategies.

   - Leverage user experience data to prioritize development and infrastructure improvements.

   - Use dependency mapping and performance correlation to optimize system architecture.

## Example: E-commerce application monitoring implementation

The following example describes implementing comprehensive monitoring for an e-commerce application with web frontend, API services, database, and payment processing components.

The development team for this workload needs to ensure high availability during peak shopping periods, optimize performance across different geographic regions, and maintain visibility into business metrics alongside technical performance.

The team implements monitoring in the following phases:

1. **Planning:** The team identifies critical user journeys including product search, cart management, checkout process, and payment completion. They establish SLOs for page load times (under 3 seconds), checkout completion rates (above 95%), and overall system availability (99.9%). They map dependencies between frontend, API gateway, microservices, database, and third-party payment services.

2. **Implementation:** The team deploys Application Insights for the web application, configures custom telemetry for business metrics like conversion rates and cart abandonment, sets up infrastructure monitoring for all VMs and containers, and implements distributed tracing across the microservices architecture. They create alerts for critical thresholds including error rate spikes, response time degradation, and payment processing failures.

3. **Configuration:** The team builds operational dashboards showing real-time system health, creates business dashboards for stakeholders with conversion metrics and revenue impacts, sets up automated alerts with appropriate escalation paths, and implements synthetic monitoring to test critical user flows continuously.

4. **Optimization:** After several months of operation, the team analyzes patterns and optimizes alert thresholds based on actual system behavior, adds predictive scaling based on traffic patterns, implements anomaly detection for unusual user behavior, and creates custom dashboards for different teams (development, operations, business stakeholders).

The comprehensive monitoring implementation enables the team to maintain high availability during peak traffic periods, quickly identify and resolve performance issues, and make data-driven optimization decisions for both technical and business improvements.

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