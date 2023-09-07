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

**Definitions**

|  Term| Definition|
|---|---|
  |Platform metrics  | Metrics are numerical values that record workload behavior at a particular time.|
  |Platform logs |Diagnostic and auditing data that includes resource logs, activity logs, and audit logs.|
  |Resource logs| Logs that track data plane operations within a resource, such as making a request to a database.|
  |Activity logs  | Logs that track management operations on resources, such as deleting a resource.|
  |Application logs|Logs that track information about application events, errors, and other activities, such use logins and database connection failures.|
  |Code instrumentation|Directly or indirectly capturing performance metrics from the perspective of the application code, including "flow" metrics ("book a flight"), resource utilization (CPU or memory consumption), and language/runtime specific metrics (garbage collection).|
  |Distributed tracing|Gathering and correlating metrics across distributed workload components.|
  |Structured logging | Defining a meaningful format to log messages, typically as key-value pairs.|
  |Application performance monitoring (APM) tool  | A tool that monitors and reports the performance of application.|
  |Metrics sink | A storage destination for your metrics that correlates time series data for analysis.|
|Rx/Tx errors|The number of receive and transmit errors on a network interface.|

## Key strategies

Performance optimization requires data to measure the current performance of a workload or flow against its performance targets. Your goal is to collect the right amount and diversity of data to measure the performance of the code and infrastructure against performance targets set in [setting performance targets](./performance-targets.md).

**Centralize the data.** Centralizing performance metrics and logs refers to the process of collecting and storing performance metrics and logs from various sources into a central location. Create a central metrics sink and a central log sink. This centralization allows for easy access, analysis, and monitoring of performance metrics and logs across different systems and components. By centralizing metrics and logs, you gain visibility into the performance of your workload. Choose a suitable platform or tool that can aggregate and store workload performance metrics and logs.

![Tradeoff](../_images/trade-off.svg) Understand the cost of collecting metrics and logs. In general, the more metrics and logs you collect, the more it costs.

**Segment data.** Performance data serves a purpose. The purpose is to monitor the performance of each environment. Within each environment, you might have distinct types of data, such as workload performance and business metrics. You should have a separate metrics and log sink for each distinct purpose.

- *Keep production and nonproduction data separate:* Separating data by environments allows for focused monitoring and optimization of each environment. In production, you can better identify and address performance issues that directly affect users and business operations. In nonproduction environments, it facilitates effective troubleshooting and fine-tuning during the testing phase before deployments to production.
- *Use one set of data within each environment:* Don't use one set of data for performance targets and another for alerts related to the performance target. It leads to inaccurate alerts that undermine the effectiveness of performance monitoring.
- *Separate performance targets and business metrics:* Performance targets are for the operations and development teams to monitor system health and meet business targets. Business metrics relate to business goals or customer reporting. You should capture business metrics in a separate data stream, even if the data directly overlaps. The separation gives you greater flexibility to capture the right data and conduct independent analysis on the data.

**Define retention policies.** Determine the retention period for your performance metrics and logs based on your organization's requirements and compliance regulations. You need to decide how long you need to retain the data for analysis and audit purposes. Archive data that's no longer needed for immediate analysis.

### Collect application data

Collecting application performance data provides valuable insights into the health and performance of an application. By monitoring and analyzing performance data, you can identify and troubleshoot issues, optimize application performance, and make informed decisions for your application.

**Instrument code.** Instrumenting code involves adding specific code and tools to gather data about the performance of an application during runtime. You need to collect metrics for key flows. The metrics should target throughput, latency, or time-to-complete values. Data on business flows should remain distinct from nonbusiness flows. Ensure the metadata of business flow data allows you to track and store it separately. There are several reasons why instrumenting code is important for collecting workload performance data:

- *Identifying performance bottlenecks:* Tracking metrics such as CPU and memory utilization, you can identify bottlenecks and optimize the code accordingly.
- *Evaluating system behavior under load:* You can see how the application performs under different workloads and stress scenarios. This data can help identify issues related to scalability, concurrency, and resource utilization.
- *Tracking application health and availability:* Instrumentation allows monitoring of key performance indicators in real-time, enabling detection and alerting of any potential issues impacting the application's performance and availability.
- *Improving user experience:* You can gain insights into how users interact with the application. This information can be used to optimize the user experience and identify areas for improvement.
- *Capacity planning and resource allocation:* Performance data gathered through instrumentation can provide valuable insights into the resource requirements of an application. This information can help with capacity planning and resource allocation decisions.

To instrument code for performance monitoring, developers can use various tools and techniques such as:

- *Application Performance Monitoring (APM) tools:* Tools like Azure Application Insights can be used to collect and analyze performance data, including metrics, traces, and logs. These tools offer features like code-level instrumentation, transaction tracing, and performance profiling.
- *Logging and tracing frameworks:* Incorporating logging and tracing frameworks into the codebase allows developers to capture relevant data during runtime. It can include information about the execution path, input/output data, and performance metrics.
- *Custom instrumentation:* Developers can add custom code to collect specific performance metrics that are unique to their application and workload. It can involve measuring execution times, tracking resource usage, or capturing specific events. Only write custom code instrumentation where platform metrics aren't sufficient. There may be situations where the platform resource can measure aggregate or even some granular perspectives of your application. Duplicating that effort with custom code should be weighed against excess code tradeoffs or dependency on a platform feature.

**Capture transaction times.** Application-level metrics should include end-to-end transaction times. These end-to-end transaction times should cover key technical functions such as database queries, response times for external API calls, and failure rates of processing steps.

**Implement distributed tracing.** [Distributed tracing](/azure/azure-monitor/app/distributed-tracing-telemetry-correlation) allows you to monitor the timing and performance relationships between the request flow between the involved components. Configure distributed tracing for all flows. You need to correlate events coming from different application components or tiers to build end-to-end transaction flows.

**Collect application logs.** Logging is essential in understanding how the application runs in various environments. It helps you understand the conditions that produce application events. You need to collect application logs across all application environments. Also, corresponding log entries across the application should capture a correlation ID for their respective transactions. It should correlate application log events across critical system flows such as user sign-in. This correlation allows you to assess the health of key scenarios in the context of targets and nonfunctional requirements.

**Use structured logging.**  Structured logging speeds up log parsing and analysis. It makes the logs easier to index, query, and report without complexity. Add and use a structured logging library in your application code. Sometimes log entries can help you correlate data that you couldn't correlate by other means.

**Use telemetry standards.** Consider using Application Performance Monitoring instrumentation libraries and tools built around a telemetry standard, such as OpenTelemetry.

### Capture platform data

By collecting platform data, you gain insights into the health and behavior of your workload. Platform data provides information on resource utilization, which is key for capacity planning. It also provides insights into the health of a workload and aids in issues detection and troubleshooting.

**Collect metrics and logs for every resource.** Each Azure service has a set of metrics that\'s unique to the functionality of the resource. These metrics give you visibility into their health and performance. You need to add a [diagnostic setting](/azure/azure-monitor/essentials/monitor-azure-resource#monitoring-data-from-azure-resources) for each resource and send to a destination that your workload team can access and build alerts and dashboarding from. Metric data is available for short term access, but for long term access or for access from a system outside of Azure Monitor, be sure to send the metric data as well to your unified sink.

**Use platform tooling.** Use or take inspiration from built-in and integrated monitoring solutions, such as Azure Monitor Insights. This tooling streamlines performance operations and should factor in your platform selections and your investment in custom tooling or reporting.

**Monitor network traffic.** You should collect traffic analytics and monitor traffic traversing subnet boundaries. The goal is to analyze and optimize network performance.

### Collect database and storage data

Collecting database and storage performance data allows you to identify bottlenecks, diagnose issues, and make informed decisions to improve the overall performance and reliability of your workload. Many database and storage systems provide their own monitoring tools that can collect performance data specific to those systems. Database and storage systems often generate logs that contain performance-related events and indicators, and you should collect those logs.

There are different types of database and storage performance data that you should collect, including:

- *Throughput:* Data on the amount of data read from or written to the storage system over a period of time. It indicates the data transfer capabilities.
- *Latency:* Data on the time it takes for storage operations to complete, indicating the responsiveness of the storage system.
- *IOPS (Input/Output Operations Per Second):* Data on the number of read or write operations that the storage system can perform in a second, indicating its throughput and responsiveness.
- *Capacity utilization:* Data on the amount of storage capacity used and available, helping organizations plan for future storage needs.

Additionally for databases, you should collect database-specific metrics, including:

- *Query performance:* Data on the execution time, resource usage, and efficiency of database queries. Slow or inefficient database queries can significantly slow down a workload. You should look for slow and frequently executed queries.
- *Transaction performance:* Data on the performance of database transactions, such as transaction duration, concurrency, and lock contention.
- *Index performance:* Data on the performance of database indexes, such as index fragmentation, usage statistics, and query optimization.
- *Resource utilization:* Resource utilization includes CPU, memory, disk space, input/output, and network bandwidth.
- *Connection metrics:* Connection metrics track the number of active, aborted, and failed connections. High failure rates could indicate network issues or that the database has reached its maximum number of connections.
- *Transaction rates:* Database transaction rates are the number of transactions a database executes per second. A change in transaction rates can indicate performance issues.
- *Error rates:* A database that returns a high number of errors might have a performance issue. You should collect and analyze database errors.

### Collect operating system data (if applicable)

Platform as a service (PaaS) solution eliminate the need to collect operating system performance data. However, if your workload runs on virtual machines (infrastructure as a service), you need to collect performance data on the operating system. Understand the demand on your operating system and virtual machine. Sample operating-system performance counters frequently, such as every minute. At a minimum, collect data on the following performance areas:

| Performance areas| Process or function|
|---|---|
| CPU  | - CPU utilization (user mode or privileged mode)<br>- CPU queue length (number of processes waiting for CPU time) |
| Process | - Process thread count<br>- Process handle count |
| Memory | - Committed memory<br>- Available memory <br>- Pages per second<br>- Swap space usage |
| Disk | - Disk read<br>- Disk writes    <br>- Disk throughput       <br>- Disk space usage|
| Network| - Network interface throughput<br>- Network interface Rx/Tx errors |

## Validate and analyze data

The performance data you should align with the performance targets. The data needs to represent workload or flow performance completely and accurately as it relates to performance targets. For example, the response time for a web service has a performance target of 500 ms. You should collect response time at regular intervals to obtain a full understanding of response times. You need to regularly analyze the performance data you collect. Regular analysis helps catch performance issues sooner and resolve them faster.

## Create alerts

Actionable alerts allow you to identify and resolve performance issues. They contain the violated threshold, business effects, and components involved. You should have alerts for architecture components and critical flows. Pick common and recommended alert rules to start with and change them as needed. Alerts should help you predict potential performance degradation before it becomes an issue. You could create a solution that collects proxy measurements, such as dependency call duration, when you can't create an alert on a dependency outside your control.

## Set data limits

Set sensible limits around how much data to collect and how long to retain it. Additionally, telemetry can be verbose and have too much extra data to sift through. You need to capture key performance signals or be able to efficiently pull a signal from your metrics sink.

## Azure facilitation

**Collecting and analyzing performance data:** Azure Monitor is a cloud monitoring service that provides visibility into the health and performance of your applications. It allows you to collect and analyze metrics, logs, and other telemetry data. You can configure Azure Monitor to collect performance data for your workload.

Log Analytics is a service within Azure Monitor that allows you to collect, analyze, and visualize log data from your applications and systems. You can configure Log Analytics to ingest logs from your application, including application-level logs and infrastructure logs. By aggregating logs in Log Analytics, you can cross-query events and gain insights into the performance of your application. For more information, see [Log Analytics cost calculations and options](/azure/azure-monitor/logs/cost-logs) and [Pricing for Log Analytics workspace](https://azure.microsoft.com/pricing/details/monitor/).

**Application performance data:** Application Insights is a feature of Azure Monitor that helps you monitor the performance and availability of your application. It provides application-level insights by collecting telemetry data such as request rates, response times, and exception details. You can enable Application Insights for your application and configure it to collect the necessary performance data.

Performance counters are a powerful way to monitor the performance of your application. Azure provides various performance counters that you can use to collect data on CPU usage, memory usage, disk I/O, network traffic, and more. You can configure your application to emit performance counter data, and Azure Monitor collects and stores it for analysis.

**Platform performance data** Most Azure services generate platform logs and metrics that provide diagnostic and auditing information. By enabling diagnostic settings, you can specify the platform logs and metrics you want to collect and store. You should enable diagnostics for all supported services and send the logs to the same destination as your application logs for correlation purposes.

**Database and storage performance data:** Azure Monitor allows you to collect performance data for databases in Azure. You can enable monitoring for Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL, and other database services. Azure Monitor provides metrics and logs for monitoring database performance, including CPU usage, memory utilization, query performance, and more. You can also set up alerts based on performance thresholds to be notified of any issues.

Azure offers performance recommendations for databases, such as SQL Server on Azure Virtual Machines. These recommendations provide best practices and guidelines to optimize the performance of your database workloads. They include suggestions for collecting performance counters, capturing wait statistics, and gathering performance data during peak hours.

Azure Storage Analytics allows you to collect performance data for Azure Storage services like Blob storage, Table storage, and Queue storage. You can enable logging and metrics for your storage accounts and monitor key performance indicators, such as the number of read/write operations, throughput, and latency

**Operating system performance data:** Azure Diagnostics Extension enables you to collect detailed performance data from your virtual machines (VMs), including CPU, memory, disk I/O, and network traffic. This data can be sent to Azure Monitor or other storage services for analysis and alerting.

**Alerting** In Azure Monitor, you can define alert rules to monitor specific performance metrics and trigger alerts based on predefined conditions. For example, you can create an alert rule to notify you when CPU usage exceeds a certain threshold or when response time goes above a specified limit. Configure the alert rule to send notifications to the desired recipients.

When creating an alert rule, you can define the criteria that determine when an alert should be triggered. You can set thresholds, aggregation methods, time windows, and the frequency of evaluation. Define the criteria based on your performance monitoring requirements. In addition to sending notifications, you can specify actions to be taken when an alert is triggered. Actions can include sending emails, calling webhooks, or executing Azure Functions. Choose the appropriate actions to respond to the specific alert scenario.

## Example

- [Baseline highly available zone-redundant app services web application](/azure/architecture/reference-architectures/app-service-web-app/baseline-zone-redundant)
- [Monitor a microservice application in AKS](/azure/architecture/microservices/logging-monitoring)
- [Enterprise monitoring with Azure Monitor](/azure/architecture/example-scenario/monitoring/enterprise-monitoring)

## Related links

- [Platform metrics](/azure/azure-monitor/platform/data-platform-metrics#what-can-you-do-with-azure-monitor-metrics)
- [Diagnostic settings](/azure/azure-monitor/platform/diagnostic-settings)
- [Audit logs](/azure/active-directory/reports-monitoring/concept-audit-logs)
