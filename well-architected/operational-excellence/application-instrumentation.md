---
title: Recommendations for instrumenting an application
description: Learn how to instrument an application as it relates to the health modeling and monitoring component of the Operational Excellence pillar.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
ms.custom:
  - guide
---

# Recommendations for instrumenting an application

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:**

|[OE:07](checklist.md)| Design and implement an observability platform to validate design choices and inform future design and business decisions. This platform captures and exposes operational telemetry, metrics, and logs that emit from the workload's infrastructure and code. |
|---|---|

This guide describes the recommendations for implementing observability of your application by using instrumentation. Your application must be able to generate meaningful telemetry that can be ingested by and integrated into the monitoring platform. To achieve this ability, the application will need to be instrumented. By using instrumentation, the information that you gather should be sufficient to assess performance, diagnose problems, and make decisions without requiring you to sign in to a remote production server to perform tracing, and debugging manually. Instrumentation data typically includes metrics and logs, including trace logs.

## Key design strategies

### Logs, traces, and metrics

To optimize the telemetry collection for your workload components, instrument your application to generate the following:

- [Logs](azure/azure-monitor/logs/data-platform-logs) are timestamped records of discreet events. Logs can come in three forms:
  - Plain text
  - Structured
  - Binary

- [Distributed tracing logs](/azure/azure-monitor/app/distributed-tracing) allow you to see the path of a request as it travels through different services and components.

- [Metrics](/azure/azure-monitor/essentials/data-platform-metrics) are numerical values that describe an aspect of a system at a particular point in time.

> [!NOTE]
> Several tools are available for automatically instrumenting your application, like Azure Application Insights, Dynatrace, and Elastic APM. These tools can help you instrument your application to generate telemetry easily, but can be limiting. If you use an automatic instrumentation tool, you can add more capabilities through manual instrumentation as needed.

### Logs and distributed tracing logs

Structured logging can help you easily integrate logs into monitoring and analysis platforms. Instrument your application to enable switch-able levels of verbosity. Implementing verbose logging at all times can be a waste of storage resources, so being able to toggle that mode on and off as needed for troubleshooting is a better operational practice in most cases.

The contents of a trace log can be the result of textual data that's written by the application or binary data that's created as the result of a trace event if the application is using [Event Tracing for Windows (ETW)](/azure/azure-monitor/agents/data-sources-event-tracing-windows). Trace log contents can also be generated from system logs that record events arising from parts of the infrastructure, such as a web server. Textual log messages are often designed to be human-readable, but they should also be written in a format that enables an automated system to parse them easily.

Categorize logs and use separate logs to record the trace output from different operational aspects of the system, instead of writing all trace data to a single log. You can then quickly filter log messages by reading from the appropriate log rather than having to process a single lengthy file. Never write information that has different security requirements (such as audit information and debugging data) to the same log.

> [!NOTE]
> A log might be implemented as a file on the file system, or it might be held in some other format, such as a blob in blob storage. Log information might also be held in more structured storage, such as rows in a table.

### Metrics

Metrics will generally be a measure or count of some aspect or resource in the system at a specific time, with one or more associated tags or dimensions, sometimes called a *sample*. A single instance of a metric isn't useful in isolation. Instead, metrics have to be captured over time. The key issue to consider is which metrics you should record and how frequently. Generating data for metrics too often can impose a significant extra load on the system; whereas, capturing metrics infrequently can cause you to miss the circumstances that lead to a significant event. The considerations will vary from metric to metric. For example, CPU usage on a server might vary significantly from second to second, but high usage becomes an issue only if it's long-lived over many minutes.

### Information for correlating data

You can easily monitor individual system-level performance counters, capture metrics for resources, and obtain application trace information from various log files. Some forms of monitoring require the analysis and diagnostics stage in the monitoring pipeline to correlate the data that's retrieved from several sources. This data can take several forms in the raw data, and the analysis process must be provided with sufficient instrumentation data to map these different forms. For example, at the application framework level, a task might be identified by a thread ID. Within an application, the same work might be associated with the user ID for the user who is completing that task.

Also, it's unlikely to be a 1:1 mapping between threads and user requests, because asynchronous operations might reuse the same threads to do operations for more than one user. To complicate matters further, a single request can be handled by more than one thread as it flows through the system. If possible, associate each request with a unique activity ID that's propagated through the system as part of the request context. The technique for generating and including activity IDs in trace information depends on the technology that's used to capture the trace data.

All monitoring data should be timestamped in the same way. For consistency, record all dates and times by using Coordinated Universal Time, which helps you trace sequences of events with ease.

> [!NOTE]
> Computers operating in different time zones and networks might not be synchronized. Don't depend on timestamps alone for correlating instrumentation data that spans multiple machines.

### Information to include in the instrumentation data

Consider the following points when you're deciding which instrumentation data you need to collect.

#### Human-readable data

Ensure that information captured by trace events is machine and human readable. Adopt well-defined schemas for this information to help automated processing of log data across systems, and to provide consistency to operations and engineering staff reading the logs.

Include the following environmental information, such as:

- Deployment environment
- Machine on which the process is running
- Details of the process
- Call stack

#### Invest in traceability and correlation

Provide sufficient context to enable a developer or administrator to determine the source of each request, which might include some form of activity ID that identifies a specific instance of a request.

It might also include information that can be used to correlate this activity with the computational work performed and the resources used. *This work might cross process and machine boundaries.*

For metering, the context should also include, either directly or indirectly, through other correlated information, a reference to the customer who caused the request. This context provides valuable information about the application state at the time that the monitoring data was captured.

#### Capture all relevant data

Record all requests, and the locations, or regions from which these requests are made. This information can help determine whether there are any location-specific hotspots. This information can also be useful to determine whether to repartition an application or the data that it uses.

Record and capture the details of exceptions carefully. Often, critical debug information is lost because of poor exception handling.

Capture the full details of exceptions that the application throws, including any inner exceptions and other context information. Include the call stack, if possible.

#### Strive for data consistency

Consistent data can help analyze events and correlate them with user requests. Consider using a comprehensive and configurable logging package to gather information, rather than depending on developers to adopt the same approach as they implement different parts of the system.

Gather data from key performance counters, such as the volume of I/O being performed, network usage, number of requests, memory use, and CPU usage.

Some infrastructure services provide their own specific performance counters, such as:

- The number of connections to a database.
- The rate at which transactions are performed.
- The number of transactions that succeed or fail.
Applications might also define their own specific performance counters.

#### Consider external dependencies

Log all external service calls made. For example to:

- Database systems
- Web services
- Other system-level services that are part of the infrastructure
  
Record information about the time taken to perform each call, and the success or failure of the call. If possible, capture information about all retry attempts and failures for any transient errors that occur.

### Ensure compatibility with telemetry systems

In many cases, the instrumentation information is generated as a series of events and passed to a separate telemetry system for processing and analysis. A telemetry system is typically independent of any specific application or technology, but it expects information to follow a specific format that's defined by a schema. The schema effectively specifies a contract that defines the data fields and types that the telemetry system can ingest. The schema should be generalized to allow for data arriving from a range of platforms and devices.

A common schema should include fields that are common to all instrumentation events, such as:

- Event name
- Event time
- IP address of the sender
- Details required for event correlation, such as:
  - User ID
  - Device ID
  - Application ID

Remember that any number of devices can raise events, so the schema shouldn't depend on the device type. Various devices can raise events for the same application. The application can support roaming or some other form of cross-device distribution.

The schema can also include domain fields that are relevant to a particular scenario that's common across different applications, such as:

- Information about exceptions
- Application start and end events
- Success or failure of web service API calls

All applications that use the same set of domain fields should emit the same set of events, enabling a set of common reports and analytics to be built.

Finally, a schema might contain custom fields for capturing the details of application-specific events.

[OpenTelemetry](https://opentelemetry.io/) is a vendor-neutral collection of APIs, SDKs and other tools for instrumenting applications and generating meaningful telemetry consistently across languages. It's also tool-agnostic so it can be used with many observability platforms including open source and commercial offerings. [Microsoft is adopting OpenTelemetry](/azure/azure-monitor/app/opentelemetry-overview?tabs=aspnetcore#opentelemetry) as the standard tooling for instrumentation.

### Best practices for instrumenting applications

The following list summarizes best practices for instrumenting a distributed application running in the cloud:

- Make logs easy to read and easy to parse. Use structured logging where possible.
- Be concise and descriptive in log messages.
- Identify the source of the log.
- Add timing information as each log record is written.
- Use the same time zone and format for all timestamps.
- Categorize logs and write messages to the appropriate place.
- Don't reveal sensitive information about the system or personal information about users.
  - Scrub this information before it's logged, but ensure that you keep the relevant details. 
- Log all critical exceptions, but enable the administrator to turn logging on and off for lower levels of exceptions and warnings.
- Capture and log all retry logic information. This data can be useful in monitoring the transient health of the system.
- Trace out process calls, such as requests to external web services or databases.
- Don't mix log messages with different security requirements in the same log file. 
- Except for auditing events, make sure that all logging calls are *fire-and-forget* operations that don't block the progress of business operations. 
  - Auditing events are exceptional because they're critical to the business.
- Make sure that logging is extensible (for example in code through the use of an interface) and doesn't have any direct dependencies on a concrete target. 
- Make sure that all logging is fail-safe and never triggers any cascading errors. 
- Treat instrumentation as an ongoing iterative process and review logs regularly, not just when there's a problem.

## Azure facilitation

[Autoinstrumentation](/azure/azure-monitor/app/codeless-overview) is available for many types of Azure and on-premises hosted applications monitored with [Application Insights](/azure/azure-monitor/app/app-insights-overview). Autoinstrumentation function automatically configures your application to provide rich telemetry to Application Insights and provides easy access to experiences such as the [application dashboard](/azure/azure-monitor/app/overview-dashboard) and [application map](/azure/azure-monitor/app/app-map). See the [documentation](/azure/azure-monitor/app/codeless-overview#supported-environments-languages-and-resource-providers) for supported hosting platforms and development languages.

## Tradeoffs

Implement profiling only when necessary because it can impose a significant overhead on the system. By using instrumentation, profiling records an event, such as a method call, every time it occurs. However, sampling records only selected events.

Profiling selection can be time-based, such as once every `n` seconds, or frequency-based, such as once every `n` requests. If events occur frequently, profiling by instrumentation might cause too much of a burden and affect overall performance. In this case, the sampling approach is preferable.

However, if the frequency of events is low, sampling might miss them. In this case, instrumentation might be the better approach.

## Related links

- [Application Insights documentation](/azure/azure-monitor/app/app-insights-overview)
- [Autoinstrumentation for Application Insights documentation](/azure/azure-monitor/app/codeless-overview)
- [Event Tracing for Windows](/azure/azure-monitor/agents/data-sources-event-tracing-windows)
- [Recommendations for designing and creating an observability framework](observability.md)

## Operational Excellence checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Operational Excellence checklist](checklist.md)
