---
title: Collect workload performance data
description: Learn recommendations for collecting performance data (metrics and logs) to help you assess the performance of a workload.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023    
ms.topic: conceptual
---

# Recommendations for collecting performance data

**Applies to this Azure Well-Architected Framework Performance Efficiency checklist recommendation:**

|[PE:04](checklist.md)| Collect performance data. Workload components and flows should provide automatic, continuous, and meaningful metrics and logs. Collect data at different levels of the workload, such as the application, platform, data, and operating system levels. |
|---|---|

Collecting performance data is the process of gathering metrics and logs that provide information about the performance of a workload. This data includes numerical values, which are known as *metrics*. Metrics describe the state of the system at a particular point in time. It also includes logs that contain different types of data organized into records.

By collecting performance data, you can monitor and analyze the performance of a workload. You can use this information to identify performance bottlenecks, to troubleshoot issues, to optimize resource allocation, and to make data-driven decisions to improve the overall performance efficiency of the workload.  

Without data-driven insights, you might be unaware of underlying performance issues or opportunities for optimization. Potential results include slower response times, decreased throughput, increased resource usage, and ultimately, a suboptimal user experience. Additionally, the lack of performance data makes it difficult to diagnose and troubleshoot issues in a timely manner, leading to prolonged downtime and reduced productivity.

**Definitions**

|  Term| Definition|
|---|---|
|Activity logs  | Logs that track management operations on resources, such as deleting a resource.|
|Application logs|Logs that track information about application events, errors, and other activities, such use sign-ins and database connection failures.|
|Application performance monitoring (APM) tool  | A tool that monitors and reports the performance of an application.|
|Code instrumentation|The direct or indirect capture of performance metrics from the perspective of the application code. Captured metrics include flow metrics, resource use, and metrics specific to the language or runtime.|
|Distributed tracing|Gathering and correlating metrics across distributed workload components.|
|Metrics sink | A storage destination for your metrics that correlates time series data for analysis.|
|Platform logs |Diagnostic and auditing data that includes resource logs, activity logs, and audit logs.|
|Platform metrics  | Numerical values that record workload performance at a particular time.|
|Resource logs| Data that a system generates. It provides information about the state of the system.|
|Rx/Tx errors|The number of receive errors and transmit errors on a network interface.|
|Structured logging | Defining a meaningful format to log messages, typically as key-value pairs.|

## Key design strategies

Performance optimization requires data to measure the current performance of a workload or a flow against its performance targets. You need to collect the right amount and diversity of data to measure the performance of the code and the infrastructure against [performance targets](performance-targets.md). Ensure that every component and flow within the workload automatically generates continuous and meaningful metrics and logs. You need to source this data from diverse levels like the application, platform, storage, and operating system. Comprehensive performance data collection allows for a holistic understanding of performance, enabling precise identification of inefficiencies and avenues for improvement.

### Centralize performance data

Centralizing performance metrics and logs is the process of collecting performance metrics and logs from various sources and storing them in a central location. Create a central metrics sink and a central log sink. This centralization allows for easy access, analysis, and monitoring of performance metrics and logs across different systems and components. By centralizing metrics and logs, you gain visibility into the performance of your workload. Choose a suitable platform or tool that can aggregate and store workload performance metrics and logs.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Understand the cost of collecting metrics and logs. In general, the more metrics and logs you collect, the higher the cost.

### Segment performance data

Segmenting performance data involves organizing and categorizing metrics and logs based on their origin, purpose, or environment. For example, you should separate production data from nonproduction data or distinguish between performance targets and business metrics. Segmenting data helps with optimizing specific environments, facilitates troubleshooting, and limits inaccuracies in performance monitoring. By maintaining a clear distinction between different data types, you can capture, analyze, and respond to relevant metrics more efficiently and better align workload health with workload objectives. To segment performance data, consider the following recommendations:

- *Keep production data and nonproduction data separate*. By separating data by environment, you can ensure focused monitoring and optimization of each environment. In production environments, you can better identify and address performance issues that directly affect users and business operations. In nonproduction environments, the data separation facilitates effective troubleshooting and fine-tuning during the testing phase before you deploy to production.

- *Use one set of data within each environment*. Don't use one set of data for performance targets and another set of data for alerts related to the performance targets. Using different sets of data leads to inaccurate alerts that undermine the effectiveness of performance monitoring.

- *Separate performance targets and business metrics*. The operations and development teams use performance targets to monitor workload health and meet business targets. Business metrics relate to business goals or customer reporting. Capture business metrics in a separate data stream, even if the data directly overlaps. The separation gives you flexibility to capture the right data and independently analyze the data.

### Define retention policies

Retention policies dictate how long performance data should be kept. Establishing these policies helps manage storage efficiently and ensures only necessary data is accessible for analysis. Such policies support better performance and meet compliance standards. You should configure retention policies for the log and metrics data to enable effective troubleshooting and monitoring in all environments. For example, the logs and metrics might need to be kept for longer time in a production environment than in the testing environment. The retention period should match your organization's requirements and compliance regulations. Decide how long to retain the data for analysis and audit purposes. Archive the data that you don't need for immediate analysis.

### Collect application performance data

Collecting application data involves monitoring and analyzing an application's performance metrics, such as throughput, latency, and completion times, primarily gathered through instrumenting code. Application performance data provides valuable insights into the health and performance of an application. By monitoring and analyzing performance data, you can identify and troubleshoot issues, optimize application performance, and make informed decisions for your application.

#### Instrument code

Instrumentation refers to the process of embedding code snippets or integrating tools into an application code. The purpose of instrumentation is to capture performance data while the application runs. It's essential to gather metrics that highlight the application's critical operations. Focus on metrics like throughput, latency, and completion time. It's important to differentiate between business-related operations and operations that aren't. For data pertaining to business operations, make sure its metadata is structured in a way that allows distinct tracking and storage. The primary reason for code instrumentation is to collect data on how the application handles its workload. It provides the following benefits:

- *Identifying performance bottlenecks:* By tracking metrics such as CPU use and memory use, you can identify bottlenecks and optimize the code accordingly.

- *Evaluating system behavior under a load:* You can see how the application performs under different workloads and stress scenarios. This data can help you identify issues related to scalability, concurrency, and resource use.

- *Tracking application health and availability:* Because key performance indicators are monitored in real time, you can get alerts about potential issues that affect the application's performance and availability.

- *Improve user experience:* You can gain insights into how users interact with the application. Use this information to optimize the user experience and identify areas for improvement.

- *Plan capacity and allocate resources:* The performance data that instrumentation gathers can provide valuable insights into the resource requirements of an application. This information can inform your decisions about planning capacity and allocating resources.

When you instrument code for performance monitoring, consider the following strategies:

- *Use APM tools*: APM tools can collect and analyze performance data, including metrics, traces, and logs. APM tools offer features like code-level instrumentation, transaction tracing, and performance profiling.

- *Use logging and tracing frameworks*: Logging and tracing frameworks are tools or libraries that developers integrate into their applications to facilitate logging and tracing. These frameworks provide functions to generate logs, trace requests, and sometimes even format or transport the generated data. By incorporating logging and tracing frameworks into the code base, developers can capture relevant data during runtime. The data can include information about the running path, I/O, and performance.

- *Custom instrumentation*: Developers can add custom code to collect performance metrics that are unique to their application and workload. The custom instrumentation can measure runtimes, track resource usage, or capture specific events. Write custom code instrumentation only when platform metrics are insufficient. In some situations, the platform resource can measure aggregate or even granular perspectives of your application. Weigh the question of whether to duplicate that effort by using custom code against excess code tradeoffs or dependency on a platform feature.

- *Capture transaction times.* Capturing transaction times relates to measuring the end-to-end times for key technical functions as a part of performance monitoring. Application-level metrics should include end-to-end transaction times. These transaction times should cover key technical functions such as database queries, response times for external API calls, and failure rates of processing steps.

- *Use telemetry standards.* Consider using APM tool instrumentation libraries and tools that are built around a telemetry standard, such as OpenTelemetry.

##### Enable distributed tracing

Distributed tracing is a technique used to track and monitor requests as they flow through a distributed system. It allows you to trace the path of a request as it travels across multiple services and components, providing valuable insights into the performance and efficiency of your workload. Distributed tracing is important for performance efficiency because it helps identify bottlenecks, latency issues, and areas for optimization within a distributed system. You can pinpoint where delays or inefficiencies occur and take appropriate actions to improve performance by visualizing the flow of a request. Follow these steps to enable distributed tracing:

1. Start by instrumenting your applications and services to generate trace data. Use libraries or frameworks that support distributed tracing, such as OpenTelemetry.

1. Ensure that trace information is propagated across service boundaries. You should typically pass a unique trace ID and other contextual information with each request.

1. Set up a centralized trace collection system. This system collects and stores the trace data generated by your applications and services.

1. Use the trace data collected to visualize the end-to-end flow of requests and analyze the performance characteristics of your distributed system.

#### Collect application logs

When you instrument code, one of the primary outputs should be application logs. Logging helps you understand how the application runs in various environments. Application logs record the conditions that produce application events. Collect application logs across all application environments. Corresponding log entries across the application should capture a correlation ID for their respective transactions. The correlation ID should correlate application log events across critical application flows such as user sign-in. Use this correlation to assess the health of key scenarios in the context of targets and nonfunctional requirements.

You should use structured logging. Structured logging speeds up log parsing and analysis. It makes the logs easier to index, query, and report without complexity. Add and use a structured logging library in your application code. Sometimes log entries can help you correlate data that you couldn't correlate by other means.

### Collect resource performance data

By collecting resource performance data, you can gain insights into the health and behavior of your workload. Resource performance data provides information about resource use, which is key for capacity planning. This data also provides insights into the health of a workload and can help you detect issues and troubleshoot. Consider the following recommendations:

- *Collect metrics and logs for every resource.* Each Azure service has a set of metrics that's unique to the functionality of the resource. These metrics help you understand the resource's health and performance. Add a [diagnostic setting](/azure/azure-monitor/essentials/monitor-azure-resource#monitoring-data-from-azure-resources) for each resource to send metrics to a location that your workload team can access as they build alerts and dashboards. Metric data is available for short-term access. For long-term access or for access from a system that's outside of Azure Monitor, send the metric data to your unified sink to the access location.

- *Use platform tooling.* Gather inspiration from built-in and integrated monitoring solutions, such as Azure Monitor Insights. This tooling streamlines performance operations. Consider platform tooling as you select a platform and invest in custom tooling or reporting.

- *Monitor network traffic.* Monitoring network traffic means to track and analyze the flow and patterns of data as it moves across network pathways. Collect traffic analytics and monitor the traffic that traverses subnet boundaries. Your goal is to analyze and optimize network performance.

### Collect database and storage data

Many database and storage systems provide their own monitoring tools. These tools collect performance data specific to those systems. Database and storage systems often generate logs that contain performance-related events and indicators. Collect database data and storage performance data so you can identify bottlenecks, diagnose issues, and make informed decisions to improve the overall performance and reliability of your workload. Consider collecting the following types of performance data:

- *Throughput*: Throughput measure the amount of data read from or written to the storage system over a period of time. Throughput data indicates the data transfer capabilities.

- *Latency*: Latency measures how long storage operations last. Latency data indicates the responsiveness of the storage system.

- *IOPS (I/O operations per second)*: Data about the number of read operations or write operations that the storage system can perform in a second. IOPS data indicates the storage system's throughput and responsiveness.

- *Capacity use*: Capacity use is the amount of storage capacity used and the amount that's available. Capacity-use data helps organizations plan for future storage needs.

For databases, you should also collect database-specific metrics:

- *Query performance*: Data about the execution time, resource usage, and efficiency of database queries. Slow or inefficient database queries can significantly slow down a workload. Look for queries that are slow and that run frequently.

- *Transaction performance*: Data about the performance of database transactions, such as transaction duration, concurrency, and lock contention.

- *Index performance*: Data about the performance of database indexes, such as index fragmentation, usage statistics, and query optimization.

- *Resource use*: Data that includes CPU, memory, disk space, I/O, and network bandwidth.

- *Connection metrics*: Metrics that track the number of active, aborted, and failed connections. High failure rates could indicate network issues or could indicate that the database reached its maximum number of connections.

- *Transaction rates*: The number of transactions that a database runs per second. A change in transaction rates can indicate performance issues.

- *Error rates*: Data that indicates a database performance. High error rates might indicate a performance issue. Collect and analyze database errors.

### Collect operating system data (if applicable)

A platform as a service (PaaS) solution eliminates the need to collect operating system performance data. However, if your workload runs on virtual machines (infrastructure as a service), you need to collect performance data about the operating system. You need to understand the demand on your operating system and virtual machine. Frequently sample operating system performance counters. For example, you could sample the performance counters every minute.

At a minimum, collect data about the following performance areas.

| Performance area| Process or function|
|---|---|
| CPU  | - CPU usage (user mode or privileged mode)<br>- CPU queue length (number of processes that are waiting for CPU time) |
| Process | - Process thread count<br>- Process handle count |
| Memory | - Committed memory<br>- Available memory <br>- Pages per second<br>- Swap space usage |
| Disk | - Disk read<br>- Disk writes    <br>- Disk throughput       <br>- Disk space usage|
| Network| - Network interface throughput<br>- Network interface Rx/Tx errors |

### Validate and analyze data

Your performance data should align with the performance targets. The data needs to represent workload or flow performance completely and accurately as it relates to performance targets. For example, the response time for a web service has a performance target of 500 ms. Make it a routine to analyze the data, as frequent evaluations allow for early detection and mitigation of performance issues.

- *Create alerts.* It's beneficial to have alerts that are actionable, enabling prompt identification and rectification of performance problems. These alerts should clearly indicate the breached performance threshold, the potential business effect, and the involved components. Start by setting common and recommended alert. Over time, you can modify these criteria based on your specific needs. The primary objective of these alerts should be to forecast potential performance drops before they escalate into significant issues. If you can't set an alert for an external dependency, consider devising a method to gather indirect measurements, like the duration of a dependency call.

- *Set data collection limits.* Determine and set logical limits on the volume of data you collect and its retention duration. Telemetry can sometimes produce overwhelming amounts of data. It's essential to focus on capturing only the most vital performance indicators or have an efficient system in place to extract meaningful insights from your performance data.

## Azure facilitation

**Centralizing, segmenting, and retaining performance data**: [Azure Monitor](/azure/azure-monitor/overview) collects and aggregates data from every layer and component of your workload across multiple Azure and non-Azure subscriptions and tenants. It stores the data in a common data platform for consumption by a common set of tools that can correlate, analyze, visualize, and/or respond to the data.

You need at least one [Log Analytics workspace](/azure/azure-monitor/logs/log-analytics-workspace-overview) to enable Azure Monitor Logs. You can use a single workspace for all your data collection. You can also create multiple workspaces based on requirements to segment performance data. It also allows you to define [retention policies](/azure/azure-monitor/logs/data-retention-archive).

**Collecting application performance data**: [Application Insights](/azure/azure-monitor/app/app-insights-overview) is a feature of Azure Monitor that helps you monitor the performance and availability of your application. It provides application-level insights by collecting telemetry data such as request rates, response times, and exception details. You can enable Application Insights for your application and configure it to collect the necessary performance data. Application Insights also supports [distributed tracing](/azure/azure-monitor/app/distributed-tracing-telemetry-correlation). Configure distributed tracing for all flows. To build end-to-end transaction flows, correlate events that come from different application components or tiers.

Performance counters are a powerful way to monitor the performance of your application. Azure provides various performance counters that you can use to collect data about CPU usage, memory usage, disk I/O, network traffic, and more. If you configure your application to emit performance counter data, Azure Monitor collects and stores the data for analysis.

**Collecting resource performance data**: Most Azure services generate platform logs and metrics that provide diagnostic and auditing information. By enabling diagnostic settings, you can specify the platform logs and metrics to collect and store. For correlation purposes, enable diagnostics for all supported services and then send the logs to the same destination as your application logs.

**Collecting database and storage performance data**: Azure Monitor allows you to collect performance data for databases in Azure. You can enable monitoring for Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL, and other database services. Azure Monitor provides metrics and logs for monitoring database performance, including CPU use, memory use, and query performance. To be notified of issues, you can set up alerts based on performance thresholds.

Azure offers performance recommendations for databases, such as SQL Server on Azure Virtual Machines. These recommendations help you optimize the performance of your database workloads. They include suggestions for collecting performance counters, capturing wait statistics, and gathering performance data during peak hours.

Azure Storage Analytics allows you to collect performance data for Azure Storage services like Blob Storage, Table Storage, and Queue Storage. You can enable logging and metrics for your storage accounts to monitor key performance indicators, such as the number of read/write operations, throughput, and latency.

**Collecting operating system performance data:** The Azure Diagnostics extension enables you to collect detailed performance data from your virtual machines (VMs), including CPU, memory, disk I/O, and network traffic. This data can be sent to Azure Monitor or other storage services for analysis and alerting.

**Validating and analyzing performance data**: Within Azure Monitor, you can use Azure Monitor Logs to collect, analyze, and visualize log data from your applications and systems. You can configure Azure Monitor Logs to ingest logs from your application, including application-level logs and infrastructure logs. By aggregating logs, you can cross-query events and gain insights into the performance of your application. For more information, see [Azure Monitor Logs cost calculations and options](/azure/azure-monitor/logs/cost-logs) and [Pricing for Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

In Azure Monitor, you can define alert rules to monitor specific performance metrics and trigger alerts based on predefined conditions. For example, you can create an alert rule to notify you when CPU usage exceeds a certain threshold or when response time goes above a specified limit. Configure the alert rule to send notifications to the desired recipients.

When you create an alert rule, you can define the criteria that determine when an alert should be triggered. You can set thresholds, aggregation methods, time windows, and the frequency of evaluation. Define the criteria based on your performance monitoring requirements. In addition to sending notifications, you can specify actions to be taken when an alert is triggered. Actions can include sending emails, calling webhooks, or running Azure functions. Choose the appropriate actions to respond to the specific alert scenario.

## Examples

- [Baseline highly available zone-redundant app services web application](/azure/architecture/reference-architectures/app-service-web-app/baseline-zone-redundant)
- [Monitor a microservices application in Azure Kubernetes Service (AKS)](/azure/architecture/microservices/logging-monitoring)
- [Enterprise monitoring with Azure Monitor](/azure/architecture/example-scenario/monitoring/enterprise-monitoring)

## Related links

- [Platform metrics](/azure/azure-monitor/platform/data-platform-metrics#what-can-you-do-with-azure-monitor-metrics)
- [Diagnostic settings](/azure/azure-monitor/platform/diagnostic-settings)
- [Audit logs](/azure/active-directory/reports-monitoring/concept-audit-logs)

## Performance Efficiency checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Performance Efficiency checklist](checklist.md)
