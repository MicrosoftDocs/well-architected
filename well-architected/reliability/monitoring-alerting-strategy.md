---
title: Recommendations for designing a reliable monitoring and alerting strategy
description: Learn how to design a reliable monitoring and alerting strategy to ensure that your workload operates reliably and operations teams are aware of changes.
author: claytonsiemens77 
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for designing a reliable monitoring and alerting strategy

**Applies to: RE 09**

This guide describes the recommendations for designing a reliable monitoring and alerting strategy. Implement this strategy to keep your operations teams informed of your environment's health status and ensure that you meet the established reliability targets for your workload.

**Definitions**

| Term | Definition |
|---------|---------|
| Resource logs | Data that a system generates. It provides information about the state of the system. |
| Metrics | Numerical values that are collected at regular intervals. Metrics describe some aspects of a system at a particular time. |
| Traces | Data that provides information about the path that a request travels through services and components. |

## Key design strategies

Before you create a monitoring and alerting strategy, perform the following tasks for your workload as part of your reliability planning:

- Identify [critical and noncritical flows](identify-flows.md).

- Perform [failure mode analysis (FMA)](failure-mode-analysis.md) for your flows.

- Identify [reliability targets](metrics.md).

- Design for reliability by implementing [redundancy](redundancy.md), [scaling](scaling.md), [self-preservation, and self-healing](self-preservation.md).

- Design a robust [testing strategy](testing-strategy.md).

Create a monitoring and alerting strategy to ensure that your workload operates reliably. A monitoring and alerting strategy provides awareness to your operations teams so they're notified of changes in your workload's condition and can quickly address issues. Build a robust and reliable monitoring strategy on the [health models](metrics.md) that you develop for your critical flows and the workloads that the critical flows comprise. The health model defines healthy, degraded, and unhealthy states. Design your monitoring posture to immediately catch changes in these states. When health states change from healthy to degraded or unhealthy, alerting mechanisms trigger the [automatic corrective measures](testing-strategy.md) and the alerts to the appropriate teams.

Implement the following recommendations to design a monitoring and alerting strategy that meets the requirements of your business.

### General guidance

- Understand the difference between [metrics](/azure/azure-monitor/essentials/data-platform-metrics), [logs](/azure/azure-monitor/logs/data-platform-logs), and [traces](/azure/azure-monitor/app/asp-net-trace-logs).

- Enable [logging](/azure/azure-monitor/essentials/resource-logs) for all cloud resources. Use automation and governance in your deployments to enable diagnostic logging throughout your environment.

- Forward all diagnostic logs to a centralized data sink and analytics platform, like a [Log Analytics workspace](/azure/azure-monitor/logs/log-analytics-workspace-overview). If you have regional data sovereignty requirements, you must use local data sinks in the regions that are subject to those requirements.

- If your workloads are subject to one or more compliance frameworks, some of the component logs that handle sensitive information are also subject to those frameworks. Send the relevant component logs to a security information and event management (SIEM) system, like [Microsoft Sentinel](/azure/sentinel/overview).

- Create a [log retention policy](/azure/azure-monitor/logs/data-retention-archive) that incorporates long-term retention requirements that the compliance frameworks impose on your workload.

- Use [structured logging](https://stackify.com/what-is-structured-logging-and-why-developers-need-it) for all log messages to optimize querying the log data.

- Configure alerts to trigger when values pass critical thresholds that correlate to a health model state change, like green to yellow or red.

  Threshold configuration is a practice of continuous improvement. As your workload evolves, the thresholds you define might change. In some cases, [dynamic thresholds](/azure/azure-monitor/alerts/alerts-dynamic-thresholds) are a good option for your monitoring strategy.

- Consider using alerts when states improve, such as red to yellow or red to green, so that the operations teams can track these events for future reference.

- Visualize the real-time health of your environment by using [custom dashboards](/azure/azure-monitor/visualize/tutorial-logs-dashboards).

- Use data that's gathered during incidents to continuously improve your [health models](metrics.md) and your monitoring and alerting strategy.

- Incorporate cloud platform monitoring and alerting services, including:

  - Platform-level health, like [Azure Service Health](/azure/service-health/service-health-overview).

  - Resource-level health, like [Azure Resource Health](/azure/service-health/resource-health-overview).

- Incorporate purpose-built advanced monitoring and analytics that your cloud provider offers, like Azure Monitor [insight tools](/azure/azure-monitor/overview#insights).

- Implement backup and recovery monitoring to capture:

  - The data replication status to ensure that your workload achieves recovery within the target recovery point objective (RPO).

  - Successful and failed backups and recoveries.
  
  - The recovery duration to inform your [disaster recovery planning](disaster-recovery.md).

### Monitor applications

- Create health probes or [check functions](/azure/architecture/patterns/health-endpoint-monitoring) and run them regularly from outside the application. Ensure that you test from multiple locations that are geographically close to your customers.

- Log data while the application runs in the production environment. You need sufficient information to diagnose the cause of issues in the production state.

- Log events at service boundaries. Include a correlation ID that flows across service boundaries. If a transaction flows through multiple services and one of them fails, the correlation ID helps you track requests across your application and pinpoint why the transaction failed.

- Use asynchronous logging. Synchronous logging operations sometimes block your application code, which causes requests to back up as logs are written. Use asynchronous logging to preserve availability during application logging.

- Separate application logging from auditing. Audit records are commonly maintained for compliance or regulatory requirements and must be complete. To avoid dropped transactions, maintain audit logs separate from diagnostic logs.

- Use [telemetry correlation](/azure/azure-monitor/app/distributed-tracing-telemetry-correlation) to ensure that you can map transactions through the end-to-end application and critical system flows. This process is vital for performing root cause analysis (RCA) for failures. Collect platform-level metrics and logs, such as CPU percentage, network in, network out, and disk operations per second, from the application to inform a health model and to detect and predict issues. This approach can help distinguish between transient and nontransient faults.

- Use white box monitoring to instrument the application with semantic logs and metrics. Collect application-level metrics and logs, such as memory consumption or request latency, from the application to inform a health model and to detect and predict issues.

- Use black box monitoring to measure platform services and the resulting customer experience. Black box monitoring tests externally visible application behavior without knowledge of the internals of the system. This approach is common for measuring customer-centric service-level indicators (SLIs), service-level objectives (SLOs), and service-level agreements (SLAs).

### Monitor data and storage

- Monitor the availability metrics of your storage containers. When this metric drops below 100 percent, it indicates failing writes. Transient drops in availability might happen when your cloud provider manages the load. Track the availability trends to determine if there's an issue with your workload.

  In some cases, a drop in the availability metrics for a storage container indicates a bottleneck in the compute layer that's associated with the storage container.

- There are many metrics to monitor for databases. In the context of reliability, the important metrics to monitor include:

  - Query duration

  - Timeouts

  - Wait times

  - Memory pressure

  - Locks

## Azure facilitation

- [Azure Monitor](/azure/azure-monitor/overview) is a comprehensive monitoring solution that's used to collect, analyze, and respond to monitoring data from your cloud and on-premises environments.

- [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview) is a tool in the Azure portal that's used to edit and run log queries against data in the Log Analytics workspace.

- [Application Insights](/azure/azure-monitor/app/app-insights-overview) is an extension of Azure Monitor. It provides application performance monitoring (APM) features.

- [Azure Monitor insights](/azure/azure-monitor/insights/insights-overview) are advanced analytics tools that help monitor Azure services, like virtual machines, application services, and containers. Insights are built on top of Azure Monitor and Log Analytics.

- [Azure Monitor for SAP solutions](/azure/sap/monitor/about-azure-monitor-sap-solutions) is an Azure-native monitoring product for SAP landscapes that run on Azure.

- [Azure Policy](/azure/governance/policy/overview) helps to enforce organizational standards and to assess compliance at scale.

- For multiple workspace best practices, see [Design a Log Analytics workspace architecture](/azure/azure-monitor/logs/workspace-design).

## Tradeoffs

There are cost implications for storing and querying logs. Notice how your log analysis and retention affects your budget, and determine the best balance of utilization to meet your requirements. For more information, see [Best practices for cost optimization](/azure/azure-monitor/best-practices-logs#cost-optimization).

For more information about application monitoring, see [Health Endpoint Monitoring pattern](/azure/architecture/patterns/health-endpoint-monitoring#issues-and-considerations).

## Example

For examples of real-world monitoring solutions, see [Web application monitoring on Azure](/azure/architecture/web-apps/guides/monitoring/app-monitoring) and [Baseline architecture for an Azure Kubernetes Service cluster](/azure/architecture/reference-architectures/containers/aks/baseline-aks#monitor-and-collect-metrics).

## Related links

- [Alerting for DevOps](../devops/alerts.md)
- [Alerting for operations](../devops/monitor-alerts.md)
- [Monitoring and diagnostics guidance](/azure/architecture/best-practices/monitoring)
- [Web application monitoring on Azure](/azure/architecture/web-apps/guides/monitoring/app-monitoring)
