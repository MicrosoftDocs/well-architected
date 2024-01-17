---
title: Health modeling for reliability
description: Use health modeling to improve application reliability in Azure. Differentiate between healthy, degraded, and unhealthy states. Learn how to quantify application health and build your own health model.
author: calcof
ms.author: calcof
ms.date: 01/08/2024
ms.topic: conceptual
---

# Health modeling for reliability

Health modeling is an essential concept to maximize reliability and operational effectiveness. It focuses on using cticital business context to quantify how monitoring data is interpreted as health states, which allow you to gain meaningful operational insights. Health modeling also helps to democratize operations by making monitoring data more business meaningful​ for all.  

> [!INFO]
> Health modeling is most often used to understand the runtime health of an application and all of it's sub-systems. A well-constructed application health model provides contextualized insights into the health of your workload to determine if it is working as expected.

<video>

This cross-cutting guide offers practical and authoritative guidance on modeling health, serving as a foundation to driving effective operations for your applications and to maximize overall reliability.

> [!IMPORTANT]
> Microsoft Azure has recently launched the public preview for Azure Monitor Health Model. This new Azure Monitor service provides an Azure-native solution to construct health models and achieve business-relevant observability. Modeling health in the context of business value using this service will enable you to approach failure scenarios based on business impact, driving informed and prioritized operational responses.
> ()[More Information]

## Key points

- 

## What is a health model?

The term _health_ refers to the evaluated 'healthy', 'degraded', and 'unhealthy' states which are derived by combining raw metrics, logs, and traces with domain specific information. These _health states_ are calulated using _health signals_ against monitoring data, such as a metric signal for CPU utilization, which is then rationalized using a _health definition_, such as the expected CPU utilization  thresholds for state transitions. There are many types of health signal which can be used, and these signals can be combined within a model to derive aggregated health states and also to propogate state changes.

<image of health>

Health states provide a distinct data schema that is optimized for observability use cases through modeling your own requirments, objectives, and architectural context. Modeling ultimatly makes health data unique to your contextual scope - what 'healthy' means for your scenario is likely to be different from any other scenario. Health is therefore a relative data concept. For example, two application resources of the same type in your own application could have very different health definitions, such as two virtual machines where one is running a CPU sensative workload and the other is running a memory intensive workload. The health status of the first virtual machine will likely be heavily influenced by the CPU utilization metirc, while the other may be much more concerned with utilized memory metrics.

- A healthy green state indicates that key nonfunctional requirements and targets are fully satisfied and that resources are used optimally. For example, 95 percent of requests are processed in <=500 ms with Azure Kubernetes Service (AKS) node use at X percent.
- A yellow degraded state indicates that resources are not performing optimally but are still operational. For example, storage throttling has been detected.
- A red unhealthy state indicates that degradation has persisted longer than allowable by your reliability targets or that resources have become unavailable or surpassed an allowable performance threshold.

> [!INFO]
> Health can also be represented through a score rather than states to provide additional data granularity.

The term _model_ refers to the graphical structure of logical components which form a specific scope (typically a workload or set of workloads) and how they depend or interact with each other. The model also layers in domain information and business context in the form of health definitions which enable you to rationalize monitoring data as health states.

Typically, models are used to represent application scopes, and contain a logical representation of the application architecture and business scenarios. Components of the application are captured as _nodes_ in the graph, with _relationships_ to mirror dependency chains between components. Nodes within the application model can broadly be categorized in 3 abstract types; (1) infrastructure componets, (2) application components, and (3) user or system flows.

<image of model>

These user and system flows summarize non-functional requirements across business scenarios involving both application and infrastructure components, which ultimately reflects business value for the application. ​Not all business scenarios in an application are equal, and a health model enables you to derive a prioritized and meaningful representation of application health. 

> [!NOTE]
> A health model shouldn't treat all failures the same. It should clearly distinguish between expected-transient but recoverable failures and a true disaster state.


## ​What are the benefits of health modeling? 

Health modeling introduces business-relevant observability through quantified 'healthy', 'degraded', and 'unhealthy' states which provide the basis for alerting and automated action. This enables you to implicitly identify, interpret, and swiftly react to any deviations from a known healthy state. It ensures that operations are well informed and that business impact is considered in the decision-making process. This also helps to democratizes operations by making monitoring data more business meaningful​ for all.

> [!EXAMPLE]
> ​Consider a detection scenario where there is spike in failed messages on a service bus queue in an ecommerce application which is causing payments to fail. This is a critical business issue that is directly impacting revenue. A developer of the system might possess the requisite system knowledge to rationalize the system impact on payments from this the failed messages metric spike. But in complex systems this tribal knowledge is often segmented and not consistent across all of the operations team. With a well-constructed health model, the impact of this spike on both the service bus instance and the payment flows that depend on it are immediatly visible and actionable. This allows anyone to immediatly comprehend the severity of the issue, and act accordingly.

The following list captures some fo the key benefits of health modeling:

 - Improves Time To Detect (TTD) and Time To Mitigate (TTM)
 - Reduces alert noise through targeted alerts based on health
 - Democratizes operational knowledge
 - Identify the blast radius of operational issues 
 - Easily identify cascading or correlated issues
 - Assists with root cause analysis

## ​Common scenarios for health modeling 

Health modeling is most often used to quantify health states for an application as the basis for observability and operations. However, health modeling is an abstract concept and can be applied at different scopes in a variety of different scenarios.

It is possible to combine multiple health models together in a _model of models_ to achieve higher levels of abstraction and aggregation. For example, it is possible to model the health of a business unit or an entire cloud estate, with component nodes used to represent child health models for applications within the estate. Relationships in this model are then used to capture dependencies between applications.

> [!EXAMPLE]
> ​Consider an retail company with various applications for eCommerce, payments, and order processing. Each of these applications would be defined as an independent health model to quantify what health means for that particular workload. A parent model could then be used to map all of these component health models as nodes, capturing inter-application operational impacts through dependency chains, such as the cascading impact on the eCommerce application if the payment application was to become unhealthy.

<screen grab of sample model of models>

## Building a health model

​The process to build a heath model is a continuous engineering activity which should align development and operations to map changing requirements. Automation can be used to streamline the evolution of your health model in-line with changes to your application architecture, so that as application components are added or removed the model is kept up-to-date. However, the addition of new business scenarios or changing requirements will almost always require manual updates to ensure health quantification is best aligned with your needs.

<image of building a health model>

The initial process to construct an application health model is a logical design exercise which can be performed on a whiteboard. The goal is to define application health across the architecture and to configure the automated evaluation of the health states. The following activities are key to this initial exercise.

- Identify critical application design details.
  - Infrastructure components, such as compute clusters and databases.
  - Application components, such as kubernetes pods.
  - Logical or physical dependency chains between components. 
  - [User and system flows](https://learn.microsoft.com/en-us/azure/well-architected/reliability/identify-flows#identify-all-user-and-system-flows). For example, the health model should be able to represent the current state of the user sign-in transaction flow.
    - [Business processes for each flow](https://learn.microsoft.com/en-us/azure/well-architected/reliability/identify-flows#identify-business-processes-for-each-flow).
    - [Business impact of each flow](https://learn.microsoft.com/en-us/azure/well-architected/reliability/identify-flows#identify-business-impact-of-each-flow).
    - [Escalation paths for each flow](https://learn.microsoft.com/en-us/azure/well-architected/reliability/identify-flows#identify-escalation-paths-for-each-flow).
  - Criticality of components and 
- Identify key health signals, such as [reliability metrics](https://learn.microsoft.com/en-us/azure/well-architected/reliability/metrics), across the application design.
  - Service Level Indicators (SLI) and Service Level Objectives (SLO).
  - Service Health Indicators (SHI) and Service Health Objectives (SHO).
- Define requisite health probes to quantify composite health signals.
- Model the identified application architecture, flows, and dependecy chains as nodes and relationships. Define the model in way that is most business meaningful for health.
  - Map identified health signals across all modeled components to quantify health states at nodal level.
  - Map criticality and operational weighting across modeled components to quantify how health states should propogate through the model. For example, reporting components are seldom as important as payment components so should not have the same impact on overall application health.   
- Visualize the modal in a tabular or graphical format for integration within your dashboard tooling.
- Use evaluated health states to trigger alerts and automated action, integrating with existing operational runbooks.

### 'Traffic light' visualization

It is highly recommended that a _traffic light_ approach be used when visualizing your health model to make health states immediatly insightful. Assign green for healthy, yellow for degraded, and red for unhealthy. You can trace application health states to quickly find the root cause of application degradation.

> [!NOTE]
> ​It is important to also consider accessibility requirements for the visualy impaired.

### Continuos Improvement

The principal of continuous improvement should be applied to progressivly optimise the quantification of health states within the model. As your applications evolve, your health models must evolve with them. 

> [!IMPORTANT]
> Use your health model to optimise testing processes, such as load and chaos testing, and also to validate the model's operational effectiveness under scale and failure scenarios.

## Critical system flows

The value of health modeling is only fully realized when you consider key business scenarios. This enables you to rationalize the business impact from operational incidents. The health model should surface the respective health of critical system flows and key subsystes to encapsulate your business scenarios. For example, the health model for an ecommerce application should be able to represent the current state of critical flows like user sign-in, checkout, process payment, etc.

## Monitoring data for health signals

Health should be defined by correlating all types of monitoring data




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


### Health probes and distributed tracing

The health and performance of an application can degrade over time. That degradation might not be noticeable until the application fails.

Implement probes or check functions. Run them regularly from outside the application. These checks can be as simple as measuring response time for the application as a whole, for individual parts of the application, for specific services that the application uses, or for separate components. Check functions can run processes to ensure that they produce valid results, measure latency and check availability, and extract information from the system.

These probes can be involved as part of health checks performed by load balancers or can be invoked from an external watchdog service. A watchdog service aggregates health checks from across multiple components in the workload. Watchdogs can also host code that can perform remediation for known health conditions.

A pair of health probes can be used to distinguish two distinct application states:

- *Liveness* is an indication if the application code should continue to run or needs to be rebooted. A failure response (or lack of response) from this probe means the watchdog process should terminate the application and restart it. Applications need to be given enough time to fully start, so ensure your watchdog process doesn't aggressively trigger on liveness failures while the application is initializing.

- *Readiness* is an indication if the application should continue to receive usage. A failure response (or lack of response) from this probe means to hold off on sending traffic to this component. The component is running, but is not yet ready to receive traffic. For example, the application is healthy but is waiting on a dependency, such as a database, to be available before the application can accept traffic.


y doing so, Health enables a more efficient and effective use of the data, which can lead to better decision-making and improved outcomes for all stakeholders involved.













Health data retention

different lenses of model for different personas

health trends to inform operational runbooks






For detailed design considerations and recommendations for a layered application health model, see the health modeling guidance found in the mission-critical workload design areas.




To keep your operations teams and workload stakeholders informed about the real-time status and overall trends of the workload health model, consider creating dashboards in your monitoring solution. 












## Using a health model

The health data derived within your health model should serve as the foundation for application operations, driving the detection and interpretation of any failures or operational instability.


The health model shouldn't treat all failures the same. The health model should distinguish between transient and nontransient faults. It should clearly distinguish between expected-transient but recoverable failures and a true disaster state.





The health model shouldn't treat all failures the same. The health model should distinguish between transient and nontransient faults. It should clearly distinguish between expected-transient but recoverable failures and a true disaster state.


 































We highly recommend that a *traffic light* model is used to indicate a green or healthy state when key nonfunctional requirements and targets are fully satisfied. Indicate a healthy state when resources are optimally utilized. For example, 95 percent of requests are processed in `<= 500 ms` with AKS node utilization at `x%`. 

Once established, this health model should inform critical monitoring metrics across system components and operational subsystem composition.




Cloud applications typically generate high volumes of operational data, which makes it challenging to evaluate and determine optimal operational action. Health modeling strives to maximize observability by augmenting raw monitoring logs and metrics with key business requirements to quantify application health, driving automated evaluation of health states to achieve consistent and expedited operations.


model of models

​




Health model uses set indicators to represent health status, which makes it intuitive to understand and respond quickly to any issues that might arise. Health status typically is presented by using traffic light indicators, such as green, yellow, and red. You can trace application health scores to quickly find the root cause of service degradation.

To evaluate the overall health of your workload, you need holistic understanding of all metrics. You also need to identify and resolve reliability issues quickly.

Health modeling focuses on thorough instrumentation and monitoring, but it also adds a layer of context to give you critical insight into your application's health. A well-designed health model should clearly indicate the overall health of the workload instead of raw data metrics.

Because of the complex nature of mission-critical applications, they are expected to generate high volumes of operational data. It can be challenging to evaluate the health state of the application and figure out the right action that will resolve the identified issues.




The health model should be able to surface the health of critical system flows or key subsystems to ensure that appropriate operational prioritization is applied. For example, the health model should be able to represent the current state of the user sign-in transaction flow.

The health model shouldn't treat all failures the same. The health model should distinguish between transient and nontransient faults. It should clearly distinguish between expected-transient but recoverable failures and a true disaster state.


observability by using metrics and thresholds, which you consider important for your mission-critical workload.






A health model can assist with evaluation of the overall health status by displaying a clear indication of the health of the workload instead of raw metrics. The status is often presented as "traffic light" indicators such as red, green, or yellow. Representation of a health model with clear indicators makes it intuitive for an operator to understand the overall health of the workload and respond quickly to issues that arise.







## Azure Monitor Health Models

Azure Monitor Health Models enable customers to natively construct and interact with health models in Azure.


capabilities to enable customers to establish a health model and quantify application health.  





A new Azure Monitor solution that exposes health as a data tenet for customers to use as the basis for consistent and expedited operations. 
The solution enables customers to model health at a given scope by augmenting raw telemetry data with critical business context, enabling the automated evaluation of quantified health states.

Each health model is a data platform for health data, with alerting and OOB visualizations (graphical and timeline) to enable customers to easily interpret and react to changes in health state. For advanced users, query/API data plane experiences allow flexible and extensible use of health states within existing operational stack (e.g. Grafana).

Using a heath model, it is possible for customers to quantify healthy, degraded, and unhealthy states in the context of critical business requirements. This democratizes complex operational knowledge and domain expertise to enable customers to implicitly identify, interpret, and swiftly react to any deviations from a known healthy state.

A health model empowers operational users to troubleshoot dependencies and identify correlated/cascading issues, optimizes TTD and TTM, as well as enhancing alerting and significantly reduces monitoring/alerting noise.

Model an architectural or business scope of your solutions with quantified health status across cloud infrastructure, application components, and business scenarios. Because Health is an abstract and relative concept, any scope can be modeled, but some specific examples are applications, IT estates, business units, microservices, sub-systems, and workflows


Faster problem isolation (TTD/TTM)
Alert noise reduction (IPD)
Blast radius assessment (Impact assessment
Business impact assessment
Operational knowledge democratization
AI based scaling for workload monitoring








The health model should be able to surface the health of critical system flows or key subsystems to ensure that appropriate operational prioritization is applied. For example, the health model should be able to represent the current state of the user sign-in transaction flow.










Understand how to tell if an application is healthy or unhealthy.
- Understand the effects of logs in diagnostic data.
- Ensure the consistent use of diagnostic settings across the application.
- Use critical system flows in your health model.



















## Related links

- For implementing health probes in ASP.NET, see [Health checks in ASP.NET Core](/aspnet/core/host-and-deploy/health-checks).
- For information on monitoring metrics, see [Azure Monitor Metrics overview](/azure/azure-monitor/essentials/data-platform-metrics).
- For information on using Application Insights, see [Application Insights](/azure/azure-monitor/app/app-insights-overview).

## Next steps

> [!div class="nextstepaction"]
> [Recommendations for designing a reliable monitoring and alerting strategy](../reliability/monitoring-alerting-strategy.md)
