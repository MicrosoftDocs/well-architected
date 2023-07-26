---
title: Recommendations for collecting performance metrics and logs
description: Learn best practices for collecting performance metrics and logs.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for collecting performance metrics and logs: PE 04

Metrics and logs are essential for monitoring your workload and resolving performance issues fast. Without the right metrics and logs, you lose visibility into the use and performance of a workload. A loss of visibility makes troubleshooting, remediation, and tuning more difficult.

## Definitions

|  Term|                                            Definition|
|-|-|
  |Platform metrics                |                Metrics are numerical values that record workload behavior at a particular time.|
  |Platform logs                                   |Diagnostic and auditing data that includes resource logs, activity logs, and Azure Active Directory logs.|
  |Resource logs|                                   Logs that track data plan operations within an Azure resource.|
  |Activity logs                       |            Logs that track management operations on Azure resources.|
  |Application logs              |                  Logs that track information about application events, errors, and other activities.|
  |Code instrumentation                            |Directly or indirectly capturing performance metrics from the perspective of the application code. This includes "flow" metrics ("book a flight"), resource utilization (CPU or memory consumption), and language/runtime specific metrics (garbage collection).|
  |Distributed tracing|                             Gathering and correlating metrics across distributed workload components.|
  |Structured logging                             | Defining a meaningful format to log messages, typically as key-value pairs.|
  |Application performance monitoring (APM) tool  | A tool that monitors and reports the performance of application.|
  |Metrics sink                                   | A storage destination for your metrics that correlates time series data.|
|Rx/Tx errors                                    |The number of receive and transmit errors on a network interface.|

## Your responsibility

**Capture enough data**. Performance optimization requires enough data to measure the current performance of a workload or flow against its performance targets. You need to capture enough data to measure the performance of the code and infrastructure against performance targets set in PE:01. This data capture needs be built into your code and platform and collected continuously and automatically. You need to enable or build application and platform monitoring features.

**Validate the captured data.** The performance data you should align with the performance targets. The data needs to represent workload or flow performance completely and accurately as it relates to performance targets. For example, the response time for a web service has a performance target of 500 ms. You should collect response time at regular intervals to obtain a full understanding of response times.

**Reduce data noise.** Telemetry can be verbose and have too much extra data to sift through. You need to capture key performance signals or be able to efficiently pull a signal from your metrics sink.

**Separate production and non-production data.** It's important to keep production and non-production logs and metrics separate. You should use separate storage destinations for the metrics and logs you capture.

**Use one set of data within each environment.** You need to use the same data for performance targets, health models, alerts, and dashboards. Don't use one set of data for performance targets and another for alerts related to the performance target. It leads to inaccurate alerts that undermine the effectiveness of performance monitoring.

**Analyze data.** You need to regularly analyze the performance data you collect. Regular analysis helps catch performance issues sooner and resolve them faster.

**Separate performance targets and business metrics.** Performance targets are for the operations and development teams to monitor system health and meet business targets. Business metrics relate to business goals or customer reporting. You should capture business metrics in a separate data stream, even if the data directly overlaps. The separation gives you greater flexibility to capture the right data and conduct independent analysis on the data.

**Create actionable alerts.** Actionable alerts allow you to identify and resolve performance issues. They contain the violated threshold, business impact, and components involved. You should have alerts for architecture components as a whole and critical flows. This includes architecture components and code. You should pick common and recommended alert rules to start with and change them as needed. Alerts should help you predict potential performance degradation before it becomes an issue. You could create a solution that collects proxy measurements, such as dependency call duration, when you can't create an alert on a dependency outside your control.

### Application

**Instrument code.** Code instrumentation lets you detect underperforming code paths. It\'s critical to have this data available to improve and find performance opportunities in the application code. You need to collect custom metrics for key flows. The customer metrics should target throughput, latency, or time-to-complete values.

**Capture transaction times.** Application-level metrics should include end-to-end transaction times. These end-to-end transaction times should cover key technical functions such as database queries, response times for external API calls, and failure rates of processing steps.

**Implement all flows with support for [distributed tracing.](/azure/azure-monitor/app/distributed-tracing-telemetry-correlation)** You need to correlate events coming from different application components or tiers to build end-to-end transaction flows. Distributed tracing allows you to monitor the timing and performance relationships between the request flow between the involved components.

**Collect application logs.** Logging is essential in understanding how the application runs in various environments. It helps you understand the conditions that produce application events. You need to collect application logs across all application environments. Also, corresponding log entries across the application should capture a correlation ID for their respective transactions. It should correlate application log events across critical system flows such as user sign-in. This correlation allows you to assess the health of key scenarios in the context of targets and non-functional requirements.

**Use structured logging.** Sometimes performance data that is otherwise uncorrelatable can be derived by log entries. Structured logging speeds up log parsing and analysis. It makes the logs easier to index, query, and report without complexity. Add and use a structured logging library in your application code.

**Separate business flows.** Data on business flows should remain distinct from non-business flows. You need to ensure the metadata of telemetry on business flows clearly reflects that.

**Use telemetry standards.** Consider selecting Application Performance Monitoring instrumentation libraries and tools built around a telemetry standard, such as OpenTelemetry.

**Limit custom code instrumentation.** Only write custom code instrumentation where platform metrics are not sufficient. There may be situations where the Azure platform resource can measure aggregate or even some granular perspectives of your application. Duplicating that effort with custom code should be weighed against excess code tradeoffs or dependency on a platform feature.

### Platform 

**Collect metrics and logs for every resource.** Each Azure service has a set of metrics that\'s unique to the functionality of the resource. These metrics give you visibility into their health and performance. You need to add a [diagnostic setting](/azure/azure-monitor/essentials/monitor-azure-resource#monitoring-data-from-azure-resources) for each resource and send to a destination that your workload team can access and build alerts and dashboarding from. Metric data is usually intrinsically available for short term access, but for long term access or for access from a system outside of Azure Monitor, be sure to send the metric data as well to your unified sink.

**Use platform tooling.** Use or take inspiration from built-in and integrated monitoring solutions, such as Azure Monitor Insights. This tooling streamlines performance operations and should factor in your platform selections and your investment in custom tooling or reporting.

**Monitor network traffic.** You should collect traffic analytics and monitor traffic traversing subnet boundaries. The goal is to analyze and optimize network performance.

### Operating system

**Collect relevant operating system metrics.** Applications running on virtual machines require OS performance monitoring. Performance data should help you understand the demand on your OS and virtual machine. You should sample OS performance counters frequently such as every minute. You should collect data on the following performance areas.

| Performance areas                 | Process or function                                           |
|-|-|
| CPU  | - CPU utilization (user mode or privileged mode)<br>- CPU queue length (number of processes waiting for CPU time) |
| Process    | - Process thread count<br>- Process handle count                                       |
| Memory                            | - Committed memory<br>- Available memory <br>- Pages per second<br>- Swap space usage                                           |
| Disk                              | - Disk read<br>- Disk write    <br>- Disk throughput       <br>- Disk space usage                                           |
| Network                           | - Network interface throughput           <br>- Network interface Rx/Tx errors                             |
 
### Databases

**Collect query performance data.** Slow or inefficient database queries can significantly slow down a workload. You should look for slow and frequently executed queries.

**Collect resource utilization metrics.** Resource utilization includes CPU, memory, disk space, input/output, and network bandwidth.

**Collect connection metrics.** Connection metrics track the number of active, aborted, and failed connections. High failure rates could indicate network issues or that the database has reached its maximum number of connections.

**Monitor transaction rates.** Database transaction rates are the number of transactions a database executes per second. A change in transaction rates can indicate performance issues.

**Monitor errors.** A database that returns a high number of errors might have a performance issue. You should collect and analyze database errors.

## Azure facilitation

-   Most Azure services expose both metrics and logs. These metrics and logs indicate the performance of the service. Capturing these logs or utilizing any of the metrics is an opt-in process. You cannot instrument the Azure service code, so you must opt-in to start collecting the performance data.

-   Azure Monitor is the best destination for metric and log data (sink) for most use cases. With Azure Monitor, you can query data, create dashboards, and set alerts. Azure Monitor also offers curated visualizations. The larger, more complex curated visualizations are called *Insights*. This feature collects and analyzes a subset of available telemetry for a given service. They present the telemetry in a visual layout. The visualizations vary in size and scale. Some services support out-of-the-box alerting.

-   Azure has an application performance monitoring (APM) tool via Application Insights. Application Insights supports Azure support code hosting platforms. Application Insights supports auto-instrumentation (agentless), agent-based, or code-level SDK based solutions.

-   Azure services may also implement custom monitoring solutions that capture performance data. For example, Network Watcher has a traffic analytics feature, and Azure Virtual Machines offers boot diagnostics. You need to understand the available tooling for each Azure service.

-   Azure Monitor collects platform metrics on [Azure SQL databases](/azure/azure-sql/database/monitoring-sql-database-azure-monitor#collection-and-routing) and [Cosmos DB databases](/azure/cosmos-db/use-metrics).

-   Azure SQL databases support includes [query performance insights](/azure/azure-sql/database/query-performance-insight-use), [automatic query tuning](/azure/azure-sql/database/automatic-tuning-enable), [database advisor performance recommendations](/azure/azure-sql/database/database-advisor-implement-performance-recommendations).

-   Cosmos DB allows you to [monitor and tune queries](/azure/cosmos-db/nosql/query-metrics-performance).

## Tradeoff

Understand the cost of collecting metrics and logs. In general, the more metrics and logs you collect, the more it costs. For more information, see [Log Analytics cost calculations and options](/azure/azure-monitor/logs/cost-logs) and [Pricing for Log Analytics workspace](https://azure.microsoft.com/pricing/details/monitor/).

Understand the effects on operational efficiency. The more you automate the collection, analysis, and alerting around performance the better your operational excellence. Capabilities like Azure Monitor Insights help automate the detection and remediation of performance issues. This automation allows your teams to focus on other tasks.

## Example

[Baseline highly available zone-redundant app services web application](/azure/architecture/reference-architectures/app-service-web-app/baseline-zone-redundant)

## Related links
