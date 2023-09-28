---
title: Health monitoring
description: Provides an overview of health modeling and monitoring as it relates to the Operational Excellence pillar.
ms.author: martinek
author: martinekuan
manager: robbymillsap
ms.date: 05/09/2022
ms.topic: conceptual
categories:
  - management-and-governance
ms.custom:
  - fasttrack-edit
  - article
--- 

# Health monitoring

A system is healthy if it's running and can process requests. Health monitoring generates a snapshot of the current health of the system so that you can verify all components are functioning as expected.

## Requirements for health monitoring

If any part of the system is unhealthy, you're alerted within a matter of seconds. You'll determine which parts of the system are functioning normally and which parts are experiencing problems. A traffic light system indicates system health:

- Red for unhealthy. The system has stopped.
- Yellow for partially healthy. The system is running with reduced functionality.
- Green for healthy.

A comprehensive health monitoring system enables you to drill down to view the health status of subsystems and components. For example, if the overall system is partially healthy, you can zoom in and determine which functionality is currently unavailable.

## Best practices

- [Correlate events](#distributed-tracing) across all application components.
- Use an [Application Performance Management (APM) tool](#application-performance-management-apm-tools) used to collect application level logs.
- Use [Application Insights](#application-performance-management-apm-tools) to gather key metrics.
- Collect [application logs](#application-logs) from different application environments.
- Consider using [log levels](#log-levels) used to capture different types of application events.
- Capture [log messages](#log-messages) in a structured format.
- Set out [critical application performance targets and non-functional requirements](#performance-targets-and-non-functional-requirements-nfrs) with clarity.
- Identify [known gaps](#gaps-in-application-monitoring) in application observability that led to missed incidents or false positives in the past.
- Consider different [log aggregation technologies](#log-aggregation-technologies) to collect logs and metrics from Azure resources.
- Make logs and metrics available for [critical internal dependencies](#logs-for-internal-dependencies).
- Implement [black-box monitoring](#black-box-monitoring) to measure platform services and the resulting customer experience.
- Implement [detailed instrumentation](#instrumentation) in the application code to better understand the customer experience.
- Apply [white-box monitoring](#white-box-monitoring) to instrument the application with semantic logs and metrics.

## Distributed tracing

Trace the execution of user requests to generate raw data to determine which requests have:

- Succeeded
- Failed
- Taken too long

[Distributed tracing](/azure/architecture/microservices/logging-monitoring#distributed-tracing) allows you to build and visualize end-to-end transaction flows for the application. Events coming from different application components or different component tiers of the application should be correlated to build these flows.

For instance, using consistent correlation IDs transferred between components within a transaction achieves end-to-end transaction flows.

Event correlation between application layers allows you to connect tracing data of the complete application stack. You can create a complete picture of where time is spent at each layer through tools that can query the tracing data repositories in correlation to a unique identifier. This unique identifier represents a given transaction that flowed through the system.

## Application Performance Management (APM) tools

To successfully maintain an application, it's important to *turn the lights on* to have clear visibility into important metrics, both in real time and historically.

**Application Performance Management (APM) tools**

An APM technology, such as [Application Insights](/azure/azure-monitor/app/app-insights-overview), should be used to manage the performance and availability of the application, aggregating application level logs, and events for later interpretation. With cost in mind, consider the appropriate level of logging required.

[Application Insights](/azure/azure-monitor/app/app-insights-overview) is an extensible Application Performance Management (APM) service for developers and DevOps professionals to monitor live applications. It automatically detects performance anomalies and includes analytics tools to help users diagnose issues, and to understand what customers do with your application. Application Insights monitors diagnostic trace logs from your application so that you can correlate trace events with requests.

Application Insights allows you to:

- Verify that your application is running correctly.
- Makes application troubleshooting easier.
- Provides custom business telemetry to indicate whether your application is being used as intended.

For more information about how Application Insights helps you monitor applications, reference [Monitoring workloads](/azure/architecture/framework/devops/monitor-pipeline).

## Logs and metrics

 Logging is essential to understand how an application operates in various environments and what events occur and under which conditions. There are two types of logs: [application-generated logs](#application-logs) and [platform logs](#platform-logs).

### Application logs

*Application logs* support the end-to-end application lifecycle.
You should collect logs and events across all major application environments. A sufficient degree of separation and filtering should be in place to ensure non-critical environments don't convolute production log interpretation. Corresponding log entries across the application should capture a correlation ID for their respective transactions.

### Log levels

Use the following log levels to capture different types of application events across environments:

- **Info**
- **Warning**
- **Error**
- **Debug**

Pre-configure the preceding log levels and apply these levels within relevant environments. Changing log levels includes simple configuration changes to support operational scenarios where it's necessary to raise the log level within an environment.

### Log messages

Capture log messages and application events in a structured format, following well-known schema. The structured data type includes machine-readable data points rather than unstructured string types.

Structured data can help you:

- Parse and analyze logs.
- Index and search logs.
- Simplify reporting.

### Metrics

*Metrics* are numerical values that are collected at regular intervals and describe some aspect of a system at a particular time. They reflect the health and usage statistics of your resources.

#### Analyze health data

Analyzing health data involves quickly indicating whether the system is running through metrics. Hot analysis of the immediate data triggers an alert if a critical component is detected as unhealthy.

For example, the component fails to respond to a consecutive series of pings. You can then take the appropriate corrective action.
For more information about analyzing data, reference [Analyze monitoring data for cloud applications](monitor-analysis.md).

#### Performance targets and non-functional requirements (NFRs)

Application-level metrics should include end-to-end transaction times of key technical functions, such as:

- Database queries.
- Response times for external API calls.
- Failure rates of processing steps, and so on.

To assess fully the health of key scenarios in the context of targets and NFRs, correlate application log events, such as user login, across critical system flows.

#### Gaps in application monitoring

Known gaps in application observability lead to missed incidents and false positives.

> [!TIP]
> *What you can't see, you can't measure. What you can't measure, you can't improve.*

### Platform logs

*Platform logs* provide detailed diagnostic and auditing information for the infrastructure, and resources they depend on. Monitoring your platform includes collecting rich metrics and logs to verify the state of your complete infrastructure, and to react promptly if there are any issues.

[Application Insights](#application-performance-management-apm-tools) or a full-stack monitoring service like [Azure Monitor](/azure/azure-monitor/essentials/monitor-azure-resource) can help you keep tabs on your entire landscape.

For more information, reference [Monitoring workloads](/azure/architecture/framework/devops/monitor-pipeline).

#### Activity logs and diagnostic settings

Activity logs provide audit information about when a resource is modified, such as when a virtual machine is started or stopped. Such information is useful for the interpretation and troubleshooting of operational and performance issues because it provides transparency around configuration changes.

> [!TIP]
> We recommend collecting and aggregating activity logs.

#### Log aggregation technologies

Log aggregation technologies, such as [Azure Log Analytics](/azure/azure-monitor/logs/log-analytics-overview) or [Splunk](/azure/azure-monitor/partners#splunk), should be used to collate logs and metrics across all application components for later evaluation. Resources may include [Azure IaaS](https://azure.microsoft.com/overview/what-is-azure/iaas/) and [PaaS](https://azure.microsoft.com/free/cloud-services/) services, and third-party appliances such as firewalls or anti-malware solutions used in the application. For instance, if [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) is used, the **Diagnostic Settings** should be configured to push logs and metrics to the data sink. Understanding usage helps with right-sizing of the workload, but extra costs for logging should be accepted and included in the cost model.

All application resources should be configured to route diagnostic logs and metrics to the chosen log aggregation technology. Use [Azure Policy](/azure/governance/policy/overview) to ensure the consistent use of diagnostic settings across the application and to enforce the configuration you want for each Azure service.

#### Logs for internal dependencies

To build a robust application health model, it's vital to have visibility into the operational state of critical internal dependencies, such as a shared [Network Virtual Appliance (NVA)](/azure/architecture/reference-architectures/dmz/nva-ha?tabs=cli) or [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) connection.

#### Black-box monitoring

*Black-box monitoring* tests externally visible application behavior without knowledge of the internals of the system. This type of monitoring is a common approach to measuring customer-centric SLIs, SLOs, and SLAs.

For more information, reference [Azure Monitor](/azure/azure-monitor/app/monitor-web-app-availability).

## Instrumentation

Instrumentation of your code allows precise detection of underperforming pieces when you apply load or stress tests. It's critical to have this data available to improve and identify performance opportunities in the application code. Use Application Performance Monitoring (APM) tools, such as [Application Insights](#application-performance-management-apm-tools), to manage the performance and availability of the application, along with aggregating application-level logs, and events for later interpretation.

For more resources about instrumentation, reference [Monitor performance](/azure/azure-monitor/azure-monitor-app-hub).

### White-box monitoring

Application-level metrics and logs, such as current memory consumption or request latency, should be collected from the application to inform a health model, detect, and predict issues.

For more information, reference [Instrumenting an application with Application Insights](/azure/azure-monitor/app/app-insights-overview) and [Instrument an application](monitor-instrument.md).

## Next steps

> [!div class="nextstepaction"]
> [Usage monitoring](./usage.md)
