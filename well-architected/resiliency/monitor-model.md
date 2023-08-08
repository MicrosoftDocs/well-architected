---
title: Health modeling for reliability
description: Use health modeling to improve application reliability in Azure. Differentiate between healthy and unhealthy states. Learn how to quantify application states.
author: martinekuan
ms.author: martinek
ms.date: 04/27/2023
ms.topic: conceptual
---

# Health modeling for reliability

The health model should be able to surface the health of critical system flows or key subsystems to ensure that appropriate operational prioritization is applied. For example, the health model should be able to represent the current state of the user sign-in transaction flow.

The health model shouldn't treat all failures the same. The health model should distinguish between transient and nontransient faults. It should clearly distinguish between expected-transient but recoverable failures and a true disaster state.

## Key points

- Know how to tell if an application is healthy or unhealthy.
- Understand the effects of logs in diagnostic data.
- Ensure the consistent use of diagnostic settings across the application.
- Use critical system flows in your health model.

## Healthy and unhealthy states

A health model defines what *healthy* and *unhealthy* states represent for the application. A holistic application health model should be used to quantify what healthy and unhealthy states represent across all application components.

We highly recommend that a *traffic light* model is used to indicate a green or healthy state when key nonfunctional requirements and targets are fully satisfied. Indicate a healthy state when resources are optimally utilized. For example, 95 percent of requests are processed in `<= 500 ms` with AKS node utilization at `x%`. Once established, this health model should inform critical monitoring metrics across system components and operational subsystem composition.

## Quantify application states

Application level events should be automatically correlated with resource level metrics to quantify the current application state. The overall health state can be affected by both application level issues and resource level failures.

## Application logs

Application logs are an important source of diagnostics data. To gain insight when you need it most, follow these best practices for application logging:

- Use semantic, or *structured*, logging.

  With structured logs, it's easier to automate the consumption and analysis of the log data, which is especially important at cloud scale. We recommend storing Azure resources metrics and diagnostics data in an Azure Monitor Logs workspace rather than in a storage account. This way, you can use [Kusto queries](/azure/data-explorer/kusto/concepts/#kusto-queries) to obtain the data you want quickly and in a structured format. You can also use Azure Monitor APIs.

- Log data in the production environment.

  Capture robust data while the application is running in the production environment. You need sufficient information to diagnose the cause of issues in the production state.

- Log events at service boundaries.

  Include a correlation ID that flows across service boundaries. If a transaction flows through multiple services and one of them fails, the correlation ID helps you track requests across your application and pinpoint why the transaction failed.

- Use asynchronous logging.

  Synchronous logging operations sometimes block your application code. This situation causes requests to back up as logs are written. Use asynchronous logging to preserve availability during application logging.

- Separate application logging from auditing.

  Audit records are commonly maintained for compliance or regulatory requirements and must be complete. To avoid dropped transactions, maintain audit logs separately from diagnostic logs.

All application resources should be configured to route diagnostic logs and metrics to the chosen log aggregation technology. [Azure Policy](https://azure.microsoft.com/services/azure-policy/) should also be used to ensure the consistent use of diagnostic settings across the application, which enforces the desired configuration for each Azure service.

[Telemetry correlation](/azure/azure-monitor/app/distributed-tracing-telemetry-correlation) should be used to ensure that transactions can be mapped through the end-to-end application and critical system flows. This process is vital to root cause analysis (RCA) for failures. Platform level metrics and logs, such as CPU percentage, network in/out, and disk operations/sec, should be collected from the application to inform a health model and to detect and predict issues. This approach can also help to distinguish between transient and nontransient faults.

### White-box and black-box monitoring

Use white-box monitoring to instrument the application with semantic logs and metrics. Application level metrics and logs, such as current memory consumption or request latency, should be collected from the application to inform a health model and to detect and predict issues.

Use black-box monitoring to measure platform services and the resulting customer experience. Black-box monitoring tests externally visible application behavior without knowledge of the internals of the system. This approach is common for measuring customer-centric service-level indicators (SLIs), service-level objectives (SLOs), and service-level agreements (SLAs).

### Use critical system flows in the health model

The health model should be able to surface the respective health of critical system flows or key subsystems to ensure that appropriate operational prioritization is applied. For example, the health model should be able to represent the current state of the user sign-in transaction flow.

### Create good health probes

The health and performance of an application can degrade over time. That degradation might not be noticeable until the application fails.

Implement probes or check functions. Run them regularly from outside the application. These checks can be as simple as measuring response time for the application as a whole, for individual parts of the application, for specific services that the application uses, or for separate components.

Check functions can run processes to ensure that they produce valid results, measure latency and check availability, and extract information from the system.

:::image type="icon" source="../_images/github.png" border="false"::: The [HealthProbesSample](https://github.com/mspnp/samples/tree/master/Reliability/HealthProbesSample) sample shows how to set up health probes. It provides an Azure Resource Manager template to set up the infrastructure. A load balancer accepts public requests and load balances to a set of virtual machines. The health probe is set up so that it can check for service's path */Health*.

## Related links

- For information on monitoring metrics, see [Azure Monitor Metrics overview](/azure/azure-monitor/essentials/data-platform-metrics).
- For information on using Application Insights, see [Application Insights](/azure/azure-monitor/app/app-insights-overview).

## Next steps

> [!div class="nextstepaction"]
> [Monitoring best practices for reliability](./monitor-best-practices.md)

Go back to the main article: [Monitoring for reliability](monitor-checklist.md)
