---
title: Recommendations for instrumenting an application
description: Learn how to instrument an application as it relates to the health modeling and monitoring component of the Operational Excellence pillar.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for instrumenting an application

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:**

|[OE:07](checklist.md)| Design and implement a monitoring system to validate design choices and inform future design and business decisions. This system captures and exposes operational telemetry, metrics, and logs that emit from the workload's infrastructure and code. |
|---|---|

**Related guide**: [Recommendations for designing and creating a monitoring system](observability.md)

This guide describes the recommendations for enabling observability of your application by using instrumentation. Generate meaningful telemetry that can be ingested and integrated into your monitoring system. By using instrumentation, you can gather information without signing in to a remote production server to manually perform tracing or debugging. Instrumentation data includes metrics and logs that you can use to assess performance, diagnose problems, and make workload decisions.

## Key design strategies

To optimize telemetry for your workload, instrument your application to generate the following data:

- [Logs](/azure/azure-monitor/logs/data-platform-logs) are timestamped records of discrete events. There are three forms of logs: plain text, structured, and binary.

- [Distributed tracing logs](/azure/azure-monitor/app/distributed-trace-data) allow you to see the path of a request as it travels through different services and components.

- [Metrics](/azure/azure-monitor/essentials/data-platform-metrics) are numerical values that describe an aspect of a system at a particular point in time.

> [!NOTE]
> You can use tools like Application Insights, Dynatrace, and Elastic Application Performance Monitoring to automatically instrument your application. These tools make instrumentation easier, but they can also be limiting. If you use an automatic instrumentation tool, you can add more capabilities through manual instrumentation as needed.

### Logs and distributed tracing logs

Use structured logging to easily integrate logs into monitoring and analysis platforms. Instrument your application so the levels of verbosity can be changed. Constant verbose logging can waste storage resources, so it should be switched on and off as needed for troubleshooting.

Trace logs contain textual data or binary data that's created from a trace event, if the application uses [Event Tracing for Windows (ETW)](/azure/azure-monitor/agents/data-sources-event-tracing-windows). System logs generate trace log content from events in the infrastructure, such as the web server. Textual log content is designed to be readable by humans, but you should ensure that it's written in a format that an automated system can parse as well.

Categorize logs and use separate logs to record the trace output from each operational aspect of the system. If you categorize your logs, you can quickly filter log messages instead of processing a single lengthy file. Never write information that has different security requirements, such as audit information and debugging data, to the same log.

> [!NOTE]
> A log might be implemented as a file in the file system, or it might be held in some other format, such as a blob in blob storage. Log information might also be held in structured storage, such as rows in a table.

### Metrics

Metrics, or *samples*, are a count of some aspect or resource in the system at a specific time, with one or more associated tags or dimensions. A single instance of a metric isn't useful in isolation, metrics should be captured over time. Consider which metrics you should record and how frequently. Data that's generated too often can impose a heavy load on the system, but infrequent data capture can cause you to miss the circumstances that lead to a significant event. The appropriate frequency for capturing data might vary from metric to metric. For example, CPU usage on a server might vary significantly from second to second, but high usage only becomes an issue if it's consistent over many minutes.

### Information for correlating data

You can easily monitor individual and system-level performance counters, capture metrics for resources, and obtain application trace information from various log files. Some monitoring requires data correlation during the analysis and diagnostics stage in the monitoring pipeline. This data can take several forms and the analysis process must be provided with sufficient instrumentation data to map these different forms. For example, at the application framework level, a thread ID might identify a task. Within an application, the same work might be associated with the user ID for the user who completes that task.

It's unlikely to be a 1:1 map between threads and user requests, because asynchronous operations might reuse the same threads for more than one user. To complicate matters further, a single request can correlate to more than one thread as it flows through the system. If possible, associate each request with a unique activity ID that's propagated through the system as part of the request context. The technique for generating and including activity IDs in trace information depends on the technology that's used to capture the trace data.

All monitoring data should be timestamped in the same way. For consistency, record all dates and times by using Coordinated Universal Time.

> [!NOTE]
> Computers that operate in different time zones and networks might not be synchronized. Don't depend on timestamps alone for correlating instrumentation data that spans multiple machines.

### Information to include in the instrumentation data

Consider the following points when you decide which instrumentation data you need to collect.

#### Human-readable data

Ensure that information captured by trace events is both machine and human readable. Adopt well-defined schemas for this information to help implement automated processing of log data across systems, and to provide consistency for operations and engineering staff reading the logs.

Include the following environmental information in your data:

- Deployment environment
- Processing machine
- Details of the process
- Call stack

#### Invest in traceability and correlation

Provide sufficient context, such as an activity ID that's associated with a specific instance of a  request, so that the developer or administrator can determine the source of each request.

Data context might also include information that's used to correlate an activity with the computational work performed and the resources used. *This work might cross process and machine boundaries.*

For metering, the context should directly or indirectly include a reference to the customer who caused the request. This context provides valuable information about the application state at the time that the monitoring data was captured.

#### Capture all relevant data

Record all requests and the locations or regions where they're made. You can use this information to help identify location-specific hotspots. This information can also be useful to determine whether to repartition an application or the data that it uses.

Record and capture the details of exceptions carefully. Critical debug information is often lost because of poor exception handling. Capture all exception details that the application throws, including any inner exceptions or other contextual information, such as the call stack, if possible.

#### Strive for data consistency

Consistent data can help you analyze events and correlate them with user requests. Consider using a comprehensive and configurable logging package to gather information. Logging packages can help you avoid dependence on developers to adopt your approach as they implement different parts of the system.

Gather data, such as input/output volume, number of requests, and memory, network, and CPU usage, from key performance counters. Some infrastructure services provide their own performance counters, such as:

- The number of connections to a database.
- The transaction rate.
- The number of transactions that succeed or fail.

Applications might also define their own performance counters.

#### Consider external dependencies

Log all external service calls. Externals calls might be made to:

- Database systems.
- Web services.
- Other system-level services that are part of the infrastructure.
  
Record information about the duration of each call and the success or failure of the call. If possible, capture information about all retry attempts and failures for any transient errors that occur.

### Ensure telemetry system compatibility

In many cases, the instrumentation information is generated as a series of events and passed to a separate telemetry system for processing and analysis. A telemetry system is typically independent of any specific application or technology.

Telemetry systems use defined schemas to parse information. The schema specifies a contract that defines the data fields and types that the telemetry system can ingest. Generalize the schema to allow for data arriving from various platforms and devices. A common schema should include fields relevant to all instrumentation events, such as:

- Event name.
- Event time.
- IP address of the sender.
- Details required for event correlation, including:
  - User ID
  - Device ID
  - Application ID

Remember that many devices can raise events for the same application, so the schema shouldn't depend on the device type. The application should support roaming or cross-device distribution. The schema can also include relevant domain fields for a particular scenario that's common across applications, such as:

- Information about exceptions.
- Application start and end events.
- Success or failure of web service API calls.

Establish domain fields that produce the same set of events to build a set of common reports and analytics across applications. You might need to configure a schema to contain custom fields for capturing the details of application-specific events.

[OpenTelemetry](https://opentelemetry.io/) is a vendor-neutral collection of APIs, SDKs, and other tools. You can use OpenTelemetry to instrument applications and generate meaningful telemetry consistently across languages. OpenTelemetry is tool-agnostic, so it's compatible with many observability platforms including open-source and commercial offerings. [Microsoft is adopting OpenTelemetry](/azure/azure-monitor/app/opentelemetry-overview?tabs=aspnetcore#opentelemetry) as the standard tool for instrumentation.

### Best practices for instrumenting applications

The following list summarizes best practices for instrumenting a distributed application running in the cloud:

- Make logs easy to read and easy to parse. Use structured logging where possible.

- Be concise and descriptive in log messages.

- Identify the source of the log.

- Add timestamp information as each log record is written.

- Use the same time zone and format for all timestamps.

- Categorize logs and write messages in the appropriate place.

- Don't reveal sensitive information about the system or personal information about users. Scrub this information before it's logged, but keep any relevant details.

- Log all critical exceptions but enable the administrator to turn logging on and off as needed for fewer exceptions and warnings.

- Capture and log all retry logic information. This data is useful in monitoring the transient health of the system.

- Trace out process calls, such as requests to external web services or databases.

- Don't mix log messages with different security requirements in the same log file.

- Ensure that all logging calls are *fire-and-forget* operations that don't block the progress of business operations. Exclude auditing events from this rule because they're critical to the business.

- Ensure that logging is extensible and doesn't have any direct dependencies on a concrete target.

- Ensure that all logging is fail-safe and doesn't trigger cascading errors.

- Treat instrumentation as an ongoing iterative process and review logs regularly.

## Considerations

- Implement profiling only when necessary because it can impose a significant overhead on the system. By using instrumentation, profiling records an event, such as a method call, every time it occurs. However, sampling records only selected events.

- Profiling selections can be time-based, such as once every *n* seconds, or frequency-based, such as once every *n* requests. If events occur frequently, profiling might cause too much of a burden on the system and affect overall performance. In this case, the sampling approach is preferable. However, if the frequency of events is low, sampling might miss them. In this case, profiling might be the better approach.

## Azure facilitation

[Autoinstrumentation](/azure/azure-monitor/app/codeless-overview) is available for many types of Azure and on-premises applications monitored with [Application Insights](/azure/azure-monitor/app/app-insights-overview). The autoinstrumentation function automatically configures your application to provide rich telemetry to Application Insights and provides easy access to experiences such as the [application dashboard](/azure/azure-monitor/app/overview-dashboard) and [application map](/azure/azure-monitor/app/app-map). For supported hosting platforms and development languages, see [Supported environments, languages, and resource providers](/azure/azure-monitor/app/codeless-overview#supported-environments-languages-and-resource-providers).

## Related links

- [Application Insights overview](/azure/azure-monitor/app/app-insights-overview)
- [What is autoinstrumentation for Application Insights?](/azure/azure-monitor/app/codeless-overview)
- [Azure Monitor logs overview](/azure/azure-monitor/logs/data-platform-logs)
- [Azure Monitor metrics overview](/azure/azure-monitor/essentials/data-platform-metrics)
- [Collecting ETW events for analysis Azure Monitor logs](/azure/azure-monitor/agents/data-sources-event-tracing-windows)
- [Recommendations for designing and creating an observability framework](observability.md)
- [What is distributed tracing and telemetry correlation?](/azure/azure-monitor/app/distributed-trace-data)

## Community links

- [OpenTelemetry](https://opentelemetry.io/)

## Operational Excellence checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Operational Excellence checklist](checklist.md)
