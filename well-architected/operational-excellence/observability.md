---
title: Recommendations for designing and creating an observability framework
description: Learn the recommendations for designing and creating an observability framework. The framework provides a foundation for monitoring, detection, and alerting.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for designing and creating an observability framework

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:** 

|[OE:07](checklist.md)| Design and implement an observability platform to validate design choices and inform future design and business decisions. This platform captures and exposes operational telemetry, metrics, and logs that emit from the workload's infrastructure and code.| 
|---|---| 

This guide describes the recommendations for designing and creating an observability framework. To effectively monitor your workload for security, performance, and reliability, you need a comprehensive framework that provides the foundation for all monitoring, detection, and alerting functions. 

**Definitions**

| Term | Definition |
|---|---|
| Logs | Recorded system events. Logs can contain different types of data in a  structured or free-form text format. They contain a timestamp. |
| Metrics | Numerical values that are collected at regular intervals. Metrics describe some aspects of a system at a particular time. |

## Key design strategies

To implement a comprehensive monitoring framework design for your workload, follow these core tenets:

- Collect logs and metrics from the entire workload stack. All infrastructure resources and application functions should be configured to produce standardized, meaningful data, and that data needs to be collected.

- Store the collected data in a standardized, reliable, and secure storage solution.

- Process stored data so that it can be handled by analysis and visualization solutions.

- Analyze processed data to accurately determine the state of the workload.

- Visualize the state of the workload in meaningful dashboards or reports for workload teams and other stakeholders.

- Configure actionable alerts and other automatic responses to intelligently defined thresholds to notify workload teams when issues arise.

- Include monitoring and alerting systems in your overall workload testing practices.

- Ensure that monitoring and alerting systems are in scope for continuous improvement. Application and infrastructure behavior in production provides continuous learning opportunities. Incorporate those lessons into monitoring and alerting designs.

You should automate all functions of the monitoring framework as much as possible, and they should all run continuously, all day, every day. 

This workflow pipeline illustrates the monitoring framework: 

:::image type="content" source="media/observability/monitor-pipeline.png" alt-text="Diagram that shows the stages of a comprehensive monitoring framework as a pipeline." lightbox="media/observability/monitor-pipeline.png" border="false":::

### Collection

> [!Note]
> You need to instrument your application to enable logging. For more information, see the [instrumentation guide](../devops/monitor-instrument.md).

You should configure all workload components, whether they're infrastructure resources or application functions, to capture telemetry and/or events like logs and metrics.

Logs are primarily useful for detecting and investigating anomalies. Typically, logs are produced by the workload component and then sent to the monitoring platform or pulled by the monitoring platform via automation.

Metrics are primarily useful for [building a health model](../reliability/metrics.md#building-a-health-model) and identifying trends in workload performance and reliability. Metrics are also useful for identifying trends in the usage behavior of your customers. These trends can help guide decisions about improvements from the customer perspective. Typically, metrics are defined in the monitoring platform, and the monitoring platform and other tools poll the workload to capture metrics.

#### Application data

For applications, the collecting service can be an application performance management (APM) tool that can run autonomously from the application that generates the instrumentation data. After APM is enabled, you have clear visibility into important metrics, in real time and historically. Use an appropriate level of logging. Verbose logging can incur significant costs. Set log levels according to the environment. Lower environments don't need the same level of verbosity as production, for example.

Application logs support the end-to-end application lifecycle. Logging is essential to understanding how the application operates in various environments, which events occur, and the conditions under which they occur.

We recommend that you collect application logs and events across all major environments. Separate the data between environments as much as possible by using different data stores for each environment, if doing so is practical. Use filters to ensure that noncritical environments don't complicate the interpretation of production logs. Finally, corresponding log entries across the application should capture a correlation ID for their respective transactions.

You should capture application events in structured data types with machine-readable data points rather than unstructured string types. A structured format that uses a well-known schema can make parsing and analyzing logs easier. Also, structured data can easily be indexed and searched, and reporting can be greatly simplified.

Data should be in an agnostic format that's independent of the machine, operating system, or network protocol. For example, emit information in a self-describing format like JSON, MessagePack, or Protobuf rather than ETL/ETW. A standard format enables the system to construct processing pipelines. Components that read, transform, and send data in the standard format can be easily integrated.

#### Infrastructure data

For infrastructure resources in your workload, ensure that you collect both logs and metrics. For infrastructure as a service (IaaS) systems, capture OS, application-layer, and diagnostic logs in addition to metrics related to workload health. For platform as a service (PaaS) resources, you might be limited in your ability to capture logs that are related to underlying infrastructure, but be sure that you can capture diagnostic logs in addition to metrics related to workload health. 

As much as possible, collect logs from your cloud platform. You might be able to collect activity logs for your subscription and diagnostic logs for the management plane. 

#### Collection strategies

Avoid retrieving telemetry data manually from every component. Move data to a central location and consolidate it there. For a multi-region solution, we recommend that you first collect, consolidate, and store data on a region-by-region basis, and then aggregate the regional data into a single central system. 

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Be aware that there are cost implications to having regional and centralized data stores.

To optimize the use of bandwidth, prioritize based on the importance of data. You can transfer less urgent data in batches. However, this data must not be delayed indefinitely, especially if it contains time-sensitive information.

There are two primary models that the collection service can use to collect instrumentation data:

- **Pull model**: Actively retrieves data from the various logs and other sources for each instance of the application.

- **Push model**: Passively waits for the data to be sent from the components that constitute each instance of the application.

**Monitoring agents**

You can use monitoring agents in the pull model. Agents run locally in a separate process with each instance of the application, periodically pulling data and writing the information directly to common storage that's shared by all instances of the application. 

:::image type="content" source="media/observability/monitor-write-shared-storage.png" alt-text="Diagram that shows the use of a monitoring agent to pull information and write it to shared storage." lightbox="media/observability/monitor-write-shared-storage.png" border="false":::

> [!NOTE]
> Using a monitoring agent is ideally suited to capturing instrumentation data that's naturally pulled from a data source. It's appropriate for a small-scale application that runs on a limited number of nodes in a single location. Examples include information from SQL Server dynamic management views or the length of an Azure Service Bus queue.

**Performance considerations**

A complex and highly scalable application might generate huge volumes of data. The amount of data can easily overwhelm the I/O bandwidth available for a single, central location. The telemetry solution must not act as bottleneck and must be scalable as the system expands. Ideally, the solution should incorporate a degree of redundancy to reduce the risks of losing important monitoring information (like auditing or billing data) if part of the system fails.

One way to buffer instrumentation data is to use queuing:

:::image type="content" source="media/observability/queue-buffer-data.png" alt-text="Diagram that shows how you can use a queue to buffer instrumentation data." lightbox="media/observability/queue-buffer-data.png" border="false":::

In this architecture, the data-collection service posts data to a queue. A message queue is suitable because it provides "at least once" semantics that helps ensure that queued data won't be lost after it's posted. You can implement the storage-writing service by using a separate worker role. You can use the [Priority Queue pattern](/azure/architecture/patterns/priority-queue) to implement this architecture.

For scalability, you can run multiple instances of the storage-writing service. If a high volume of events or a high number of data points is being monitored, you can use Azure Event Hubs to dispatch the data to a different compute instance for processing and storage.

**Consolidation strategies**

The data collected from a single instance of an application provides a localized view of the health and performance of that instance. To assess the overall health of the system, you need to consolidate some aspects of the data from the local views. You can do that after the data is stored, but, in some cases, you can do it as the data is collected.

:::image type="content" source="media/observability/service-instrumentation-data.png" alt-text="Diagram that shows an example of using a service to consolidate instrumentation data." lightbox="media/observability/service-instrumentation-data.png" border="false":::

The instrumentation data can pass through a separate data consolidation service that combines data and acts as a filter and cleanup process. For example, you can amalgamate instrumentation data that includes the same correlation information, like an activity ID. (A user might start a business operation on one node and then get transferred to another node if the first node fails, or because of how load balancing is configured.) This process can also detect and remove any duplicated data. (Duplication can occur if the telemetry service uses message queues to push instrumentation data out to storage.)

### Storage

When you choose a storage solution, consider the type of data, how it's used, and how urgently it's required. 

> [!NOTE]
> Use separate storage solutions for non-production and production environments to ensure that data from each environment is easy to identify and manage.

#### Storage technologies

Consider a polyglot persistence approach, where different types of information are stored in technologies that are most appropriate to the way each type is likely to be used.

For example, Azure Blob Storage and Azure Table Storage are accessed in similar ways. But the operations that you can perform on them differ, as does the granularity of the data that they hold. If you need to perform more analytical operations or require full-text search capabilities on the data, it might be more appropriate to use data storage that provides capabilities that are optimized for specific types of queries and data access. For example:

- Performance counter data can be stored in a SQL database to enable ad hoc analysis.

- It might be better to store trace logs in Azure Monitor Logs or Azure Data Explorer.

- You might store security information in an HDFS solution.

The same instrumentation data might be required for more than one purpose. For example, you can use performance counters to provide a historical view of system performance over time. This information might be combined with other usage data to generate customer billing information. In these situations, the same data might be sent to more than one destination, like to a document database that can be a long-term store for holding billing information, and to a multidimensional store for handling complex performance analytics.

Be sure to enable functionality to protect the data from accidental deletion, like resource locks and soft delete. 

Also, be sure that you secure access to storage by using role-based access control to help ensure that only individuals who need to access the data can do so.

#### Consolidation service

You can implement another service that periodically retrieves the data from shared storage, partitions and filters it according to its purpose, and then writes it to an appropriate set of data stores.

:::image type="content" source="media/observability/partition-move-data.png" alt-text="Diagram that shows a data partitioning service that moves data to an appropriate data store based on its type." lightbox="media/observability/partition-move-data.png" border="false":::

An alternative approach is to include this functionality in the consolidation and cleanup process and write the data directly to these stores as it's retrieved rather than saving it in an intermediate shared storage area.

Each approach has its advantages and disadvantages. Implementing a separate partitioning service reduces the load on the consolidation and cleanup service, and it enables at least some of the partitioned data to be regenerated if necessary (depending on how much data is retained in shared storage). However, this approach consumes additional resources. Also, there might be a delay between the receipt of instrumentation data from each application instance and the conversion of this data into actionable information.

#### Querying considerations

Consider how urgently the data is required. Data that generates alerts must be accessed quickly, so it should be held in fast data storage and indexed or structured to optimize the queries that the alerting system performs. In some cases, it might be necessary for the collection service to format and save data locally so that a local instance of the alerting system can send notifications quickly. The same data can be dispatched to the storage writing service shown in the previous diagrams and stored centrally if it's also required for other purposes.

#### Data retention considerations

In some cases, after data is processed and transferred, you can remove the original raw source data that was stored locally. In other cases, it might be necessary or useful to save the raw information. For example, you might want to keep data that's generated for debugging available in its raw form but then discard it quickly after any bugs are resolved.

Performance data often has a longer life so that you can use it for spotting performance trends and for capacity planning. The consolidated view of this data is usually kept online for a finite period to enable fast access. After that, it can be archived or discarded.

It's useful to store historical data so you can spot long-term trends. Rather than saving old data in its entirety, you might be able to down-sample the data to reduce its resolution and save storage costs. For example, rather than saving minute-by-minute performance indicators, you can consolidate data that's more than a month old to form an hour-by-hour view.

Data gathered for metering and billing customers might need to be saved indefinitely. Additionally, regulatory requirements might dictate that information collected for auditing and security needs to be archived and saved. This data is also sensitive and might need to be encrypted or otherwise protected to prevent tampering. You should never record user passwords or other information that might be used to commit identity fraud. You should scrub these details from the data before it's stored.

To ensure that you comply with laws and regulations, minimize the storage of any identifiable information. If you do need to store identifiable information, be sure, when you design your solution, to take into account requirements that allow individuals to request that their information be deleted.

### Analysis

After you collect data from various data sources, analyze it to assess the overall well-being of the system. For this analysis, have a clear understanding of:

- How to structure data based on KPIs and performance metrics that you've defined.

- How to correlate the data captured in different metrics and log files. This correlation is important when you're tracking a sequence of events and can help you diagnose problems.

In most cases, data for each component of the architecture is captured locally and then accurately combined with data that's generated by other components.

For example, a three-tier application might have:

- A presentation tier that allows a user to connect to a website.

- A middle tier that hosts a set of microservices that processes business logic.

- A database tier that stores data associated with the operation.

The usage data for a single business operation might span all three tiers. This information needs to be correlated to provide an overall view of the resource and processing usage for the operation. The correlation might involve some preprocessing and filtering of data on the database tier. On the middle tier, aggregation and formatting are common tasks.

#### Recommendations

- **Correlate application-level and resource-level logs.** Evaluate data at both levels to optimize the detection of issues and the troubleshooting of those issues. You can aggregate the data in a single data sink or take advantage of methods that query events across both levels. We recommend a unified solution, like Azure Log Analytics, to aggregate and query application-level and resource-level logs.

- **Define clear retention times on storage for cold analysis.** We recommend this practice to enable historic analysis over a specific period. It can also help you control storage costs. Implement processes that ensure data is archived to cheaper storage and aggregate data for long-term trend analysis.

- **Analyze long-term trends to predict operational issues.** Evaluate long-term data to form operational strategies and also to predict what operational issues are likely to occur, and when. For example, you might note that average response times are slowly increasing over time and approaching the maximum target.

For detailed guidance about these recommendations, see [Analyze monitoring data for cloud applications](../devops/monitor-analysis.md).

### Visualization

#### Dashboards

The most common way to visualize data is to use dashboards that can display information as a series of chart or graphs, or in some other visual form. These items can be parameterized, and an analyst can select the important parameters, like the time period, for any specific situation.

Align your dashboards with your [health model](../reliability/metrics.md#building-a-health-model) so that they indicate when the workload or components of the workload are healthy, degraded, or unhealthy. 

For a dashboard system to work effectively, it must be meaningful to the workload team. Visualize information that relates to workload health and that's also actionable. When the workload or a component is degraded or unhealthy, members of the workload team should be able to easily identify where in the workload the issue originates and begin their corrective actions or investigations. Conversely, including information that isn't actionable or that's not related to workload health can make the dashboard needlessly complex and frustrating to team members who are trying to discern background noise from actionable data.

A good dashboard doesn't just display information. It also enables an analyst to pose improvised questions about that information. Some systems provide management tools that an operator can use to complete these tasks and explore the underlying data. Instead, depending on the repository that's used to hold the information, it might be possible to query the data directly or import it into tools like Excel for further analysis and reporting.

> [!NOTE]
> Restrict dashboard access to authorized personnel. Information on dashboards might be commercially sensitive. You should also protect the underlying data to prevent users from changing it.

#### Reporting

Reporting is used to generate an overall view of the system. It might incorporate historical data and current information. Reporting requirements fall into two broad categories: operational reporting and security reporting.

Operational reporting typically includes the following:

- Aggregating statistics that you can use to understand resource utilization of the overall system or specified subsystems during a specified time window.

- Identifying trends in resource usage for the overall system or specified subsystems during a specified period.

- Monitoring exceptions that have occurred throughout the system or in specified subsystems during a specified period.

- Determining the efficiency of the application for the deployed resources, and understanding whether the volume of resources, and their associated costs, can be reduced without affecting performance unnecessarily.

Security reporting tracks customer use of the system. It can include:

- Auditing user operations. This task requires recording the individual requests that each user completes, together with dates and times. The data should be structured to enable an administrator to quickly reconstruct the sequence of operations that a user completes during a specified period.

- Tracking resource use by user. This task requires recording how each request from a user accesses the various resources that compose the system, and for how long. An administrator can use this data to generate a utilization report, by user, for a specified period, possibly for billing.

In many cases, batch processes can generate reports according to a defined schedule. Latency isn't normally an issue. You should also have batch processes that can generate reports on a spontaneous basis, as needed. For example, if you store data in a relational database like Azure SQL Database, you can use a tool like SQL Server Reporting Services to extract and format data and present it as a set of reports.

### Alerts

To help ensure that the system remains healthy, responsive, and secure, set alerts so that operators can respond to them in a timely manner. An alert can contain enough contextual information to help them quickly get started on diagnostic activities. Alerting can be used to invoke remediation functions like [autoscaling or other self-healing mechanisms](../reliability/monitoring-alerting-strategy.md). Alerts can also enable cost-awareness by providing visibility into budgets and limits.

#### Recommendations

- Define a process for alert response that identifies the accountable owners and actions.

- Configure alerts for a well-defined scope (resource types and resource groups) and adjust the verbosity to minimize noise.

- Use an automated alerting solution, like Splunk or Azure Monitor, instead of requiring people to actively look for issues.

- Use alerts to operationalize remediation processes. For example, automatically create tickets to track issues and resolutions.

- Track the health of your cloud platform services in regions, communication about outages, planned maintenance activities, and other health advisories.

#### Thresholds

Alerts are generated when thresholds are crossed, as detected by your monitoring system. Ensure that the thresholds you set generally give you enough time to implement the necessary changes to your workload to avoid degradation or outages. For example, set your automatic scaling threshold to initiate scaling before any of the running systems become overwhelmed to the point of a degraded user experience. Base the threshold values that you assign on your past experience in managing infrastructure and validate them through the testing that you perform as part of your testing practices.

For detailed guidance on alerting use cases and other considerations, see [Designing a reliable monitoring and alerting strategy](../reliability/monitoring-alerting-strategy.md).

## Azure facilitation

- [Azure Monitor](/azure/azure-monitor/overview) is a comprehensive monitoring solution for collecting, analyzing, and responding to monitoring data from your cloud and on-premises environments. 

- [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview) is a tool in the Azure portal that you can use to edit and run log queries against data in the Log Analytics workspace.

   If you're using multiple workspaces, see the Log Analytics workspace [architecture guide](/azure/azure-monitor/logs/workspace-design) for best practices.

- [Application Insights](/azure/azure-monitor/app/app-insights-overview) is an extension of Azure Monitor. It provides APM features.

- [Azure Monitor Insights](/azure/azure-monitor/insights/insights-overview) are advanced analytics tools for specific Azure technologies (like VMs, app services, and containers). These tools are part of Azure Monitor and Log Analytics.

- [Azure Monitor for SAP solutions](/azure/sap/monitor/about-azure-monitor-sap-solutions) is an Azure monitoring tool for SAP landscapes that run on Azure.

- [Azure Policy](/azure/governance/policy/overview) can help you enforce organizational standards and assess compliance at scale.

## Tradeoffs

Storing logs and telemetry data, running queries against that data, and other factors like replication all have cost implications that you need to consider when you plan your strategy. Consider options like archive storage and selective replication when they're practical.

<!--
## Example

_Placeholder for IaaS baseline architecture_
-->

## Related links

- [Instrumentation guide](../devops/monitor-instrument.md)
- [Recommendations for designing a reliable monitoring and alerting strategy](../reliability/monitoring-alerting-strategy.md)
- [Recommendations for monitoring and threat detection](../security/monitoring.md)
- [Recommendations for collecting performance data](../performance-efficiency/collect-metrics-logs.md)

## Operational Excellence checklist

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Operational Excellence checklist](checklist.md) 