---
title: Application profiling considerations for performance monitoring
description: Collect instrumentation data from the application and correlated events across the entire stack.
author: PageWriter-MSFT
ms.author: martinek
ms.date: 05/04/2023
ms.topic: conceptual
products:
  - azure-monitor
categories:
  - management-and-governance
ms.custom:
  - fasttrack-edit
  - article
---

# Application profiling considerations for performance monitoring

Continuously monitor the application with Application Performance Monitoring (APM) technology, such as [Application Insights](/azure/azure-monitor/app/app-insights-overview). This technology can help you manage the performance and availability of the application, aggregating application level logs and events for subsequent interpretation.

## Key points

> [!div class="checklist"]

> - Enable instrumentation and collect data using Application Insights.
> - Use distributed tracing to build and visualize end-to-end transaction flows for the application.
> - Separate logs and events of a noncritical environment from a production environment.
> - Include end-to-end transaction times for key technical functions.
> - Correlate application log events across critical system flows.

## Application logs

**Are application logs collected from different application environments**?

Application logs support the end-to-end application lifecycle. Logging is essential in understanding how the application operates in various environments and what events occur and under which conditions.

Collect application logs and events across all application environments. Use a sufficient degree of separation and filtering to ensure noncritical environments aren't mixed with production log interpretation. Also, corresponding log entries across the application should capture a correlation ID for their respective transactions.

**Are log messages captured in a structured format**?
***
Structured format in a well-known schema can help expedite parsing and analyzing logs. Structured data can be indexed, queried, and reported without complexity.

Also, application events should be captured as a structured data type with machine-readable data points rather than unstructured string types.

## Application instrumentation

**Do you have detailed instrumentation in the application code**?
***

Instrumentation of your code allows precise detection of underperforming pieces when load or stress tests are applied. It's critical to have this data available to improve and identify performance opportunities in the application code.

Use an APM such as Application Insights to continuously improve performance and usability. You need to enable Application Insights by installing an instrumentation package. The service provides extensive telemetry out of the box. You can customize what is captured for greater visibility.  After it's enabled, metrics and logs related to the performance and operations are collected. View and analyze the captured data in [Azure Monitor](/azure/azure-monitor/overview).

## Distributed tracing

Events coming from different application components or different component tiers of the application should be correlated to build end-to-end transaction flows. Use distributed tracing to build and visualize flows for the application. For example, this is often achieved by using consistent correlation IDs transferred between components within a transaction.

**Are application events correlated across all application components**?
***

Event correlation between the layers of the application provides the ability to connect tracing data of the complete application stack. Once this connection is made, you can see a complete picture of where time is spent at each layer. You can then query the repositories of tracing data in correlation to a unique identifier that represents a completed transaction that has flowed through the system.

For more information, see [Distributed tracing](/azure/architecture/microservices/logging-monitoring#distributed-tracing).

## Critical targets

**Is it possible to evaluate critical application performance targets and nonfunctional requirements (NFRs)**?
***

Application level metrics should include end-to-end transaction times of key technical functions, such as database queries, response times for external API calls, failure rates of processing steps, and more.

**Is the end-to-end performance of critical system flows monitored**?
***
It should be possible to correlate application log events across critical system flows, such as user sign-in, to fully assess the health of key scenarios in the context of targets and NFRs.

## Cost optimization

If you're using Application Insights to collect instrumentation data, there are cost considerations. For more information, see [Manage usage and costs for Application Insights](/azure/azure-monitor//app/pricing).

## Next

> [!div class="nextstepaction"]
> [Monitor infrastructure](monitor-infrastructure.md)

## Related links

- [Distributed tracing](/azure/architecture/microservices/logging-monitoring#distributed-tracing)
- [Application Insights](/azure/azure-monitor/app/app-insights-overview)
- [Azure Monitor](/azure/azure-monitor/overview)

> [Back to the main article](checklist.md)
