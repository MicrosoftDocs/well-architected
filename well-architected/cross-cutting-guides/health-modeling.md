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
> Health modeling is most often used to understand the runtime health of an application and all of it's sub-systems. A well-architected application health model provides contextualized insights into the health of your workload to determine if it is working as expected.

<video>

This cross-cutting guide offers practical and authoritative guidance about modeling health, enabling you to use health models as a foundation for effective application operations and to maximize overall reliability.

> [!IMPORTANT]
> Microsoft Azure has recently launched the public preview for Azure Monitor health models. This new Azure Monitor capability provides an Azure-native solution to construct health models and achieve business-relevant observability. Using this service to model health in the context of business value will allow you to approach failure scenarios based on business impact, driving informed and prioritized operational responses.
> ()[More information]

#### Key points

- What is a health model?
- What are the benefits of health modeling?
- Common scenarios for health modeling
- Building a health model
- Using a health model
- Tooling for health modeling

## What is a health model?

The term _health_ refers to the evaluated 'healthy', 'degraded', and 'unhealthy' states which are derived by combining raw metrics, logs, and traces with domain specific information. These _health states_ are calulated using monitoring data _health signals_, such as a metric signal for CPU utilization. Health signals are rationalized using a domain specific _health definition_, such as expected CPU utilization thresholds for state transitions. There are many types of health signal which can be used, and these signals can be combined within a model to derive aggregated health states and also to propogate state changes.

<image of health>

Health states provide a distinct data schema that is optimized for observability use cases through modeling your own requirments, objectives, and architectural context. Modeling ultimatly makes health data unique to your contextual scope - what 'healthy' means for your scenario is likely to be different from any other scenario. Health is therefore a relative data concept. 

For example, Azure resources of the same type in your own application could have very different health definitions, such as two virtual machines where one is running a CPU sensative workload and the other is running a memory intensive workload. The health status of the first virtual machine will likely be heavily influenced by the CPU utilization metirc, while the other may be much more concerned with utilized memory metrics.

- A green healthy state indicates that key nonfunctional requirements and targets are fully satisfied and that resources are used optimally. For example, 95 percent of requests are processed in <=500 ms with Azure Kubernetes Service (AKS) node use at X percent.
- A yellow degraded state indicates that resources are not performing optimally but are still operational. For example, storage throttling has been detected.
- A red unhealthy state indicates that degradation has persisted beyond tolerated limits, or that resources have become unavailable or surpassed an allowable performance threshold.

> [!INFO]
> Health can also be represented through a score rather than states to provide additional data granularity.

The term _model_ refers to the graphical structure of logical components which form a specific scope and how they depend or interact with each other. The model also layers in domain information and business context in the form of dependency chains and health definitions which enable you to rationalize monitoring data as health state across the model.

Typically, models are used to represent an application scope and contain a logical representation of the application architecture and business scenarios. Components of the application are captured as _entities_ in the graph, with _relationships_ to mirror dependency chains between components. Entities within the application model can broadly be categorized as 3 abstract types; (1) infrastructure componets, (2) application components, and (3) user or system flows.

<image of model>

Moeled user and system flows summarize non-functional requirements across business scenarios involving both application and infrastructure components, which ultimately reflects business value for the application. ​Not all business scenarios in an application are equal, and a health model enables you to derive a prioritized and meaningful representation of application health. 

> [!NOTE]
> A health model shouldn't treat all failures the same. It should clearly distinguish between expected-transient but recoverable failures and a true disaster state.

## ​What are the benefits of health modeling?

Cloud applications typically generate high volumes of operational data, which makes it challenging to evaluate and determine the best operational action. Health modeling introduces business-relevant observability through quantified health states, which provide the basis to rationalise large volumes of operational data using domain information and business context.

This enables you to implicitly identify, interpret, and swiftly react to any deviations from a known healthy baseline. It ensures that operations are well informed and that business impact is considered in the decision-making process. This also helps to democratizes operations by making monitoring data more business meaningful​ for everyone

Using a health model enables you to implicitly identify, interpret, and swiftly react to any deviations from a known healthy state. It ensures that operations are well informed and that business impact is considered in the decision-making process. This also helps to democratizes operations by making monitoring data more business meaningful​ for everyone.

​Consider the example of a detection scenario where there is spike in failed messages on an Azure Service Bus queue, which is causing payments to fail within an ecommerce application. This is a critical business issue that is directly impacting revenue. An application developer might have the system knowledge needed to rationalize the impact of this metric spike on payments. But with a complex system this tribal knowledge is often segmented and not consistent across all of the operations team. By using a health model, the impact of this spike on both the Service Bus instance and the payment flows which depend on it are immediatly visible and actionable. This allows anyone to immediatly comprehend the severity of the issue, and act accordingly.

The following list captures some fo the key benefits of health modeling:

 - Improves Time To Detect (TTD) and Time To Mitigate (TTM) through faster problem isolation
 - Reduces alert noise through targeted alerts based on health
 - Identifies the blast radius of operational issues accelerating impact assessments
 - Easily identify cascading or correlated issues
 - Assists with Root Cause Analysis (RCA)
 - Democratizes operational knowledge
 - Provides a consistent baseline for AIOps intelligence and insights

> [!INFO]
> Use your health model to optimise testing and validation processes, such as load and chaos testing. Incorporating health models within your engineering lifecycle enables you to validate runtime operational state during testing, but also the effectiveness of your model under scale and failure scenarios.

## ​Common scenarios for health modeling 

Health modeling is most often used to quantify health states for an application, providing a basis for application observability and operations. However, health modeling is an abstract concept and can be applied at different scopes in a variety of different scenarios.

<screen grab of sample app model>

It is possible to combine multiple health models together in a _model of models_ to achieve higher levels aggregation. For example, it is possible to model the health of a business unit or an entire cloud estate, by using health models as component entities within a larger model. Applications within the estate are represented by their health models as nodes within the top level graph. Relationships in this model are then used to capture dependencies between applications.

> [!EXAMPLE]
> ​Consider an retail company with various applications for eCommerce, payments, and order processing. Each of these applications would be defined as an independent health model to quantify what health means for that particular workload. A parent model could then be used to map all of these component health models as entities, capturing inter-application operational impacts through dependency chains, such as the cascading impact on the eCommerce application if the payment application was to become unhealthy.

<screen grab of sample model of models>

## Building a health model

​The process to build a heath model is a continuous engineering activity aligned to both development and operations. 

> [!INFO]
> Automation can be used to streamline updates to your health model in-line with changes to your application. As infrastructure and application components are added or removed the model can be kept up-to-date. However, the addition of new business scenarios or changing requirements will almost always require manual updates to ensure health quantification is best aligned with business value.

<image of building a health model>

The initial process to construct an application health model is a logical design exercise which can be performed on a whiteboard. The primary goal is to define health across components within your application architecture. The following activities are key to this initial exercise:

- Identify the application design.
  - Infrastructure components, such as compute clusters and databases.
  - Application components, such as kubernetes pods.
  - Logical or physical dependency chains between components. 
  - [User and system flows](https://learn.microsoft.com/en-us/azure/well-architected/reliability/identify-flows#identify-all-user-and-system-flows). For example, the health model should be able to represent the current state of the user sign-in transaction flow.
    - [Business processes for each flow](https://learn.microsoft.com/en-us/azure/well-architected/reliability/identify-flows#identify-business-processes-for-each-flow).
    - [Business impact of each flow](https://learn.microsoft.com/en-us/azure/well-architected/reliability/identify-flows#identify-business-impact-of-each-flow).
    - [Escalation paths for each flow](https://learn.microsoft.com/en-us/azure/well-architected/reliability/identify-flows#identify-escalation-paths-for-each-flow).
  - Criticality of components

- Identify key health signals across the application design, such as [reliability metrics](https://learn.microsoft.com/en-us/azure/well-architected/reliability/metrics).
  - Service Level Indicators (SLI) and Service Level Objectives (SLO).
  - Service Health Indicators (SHI) and Health Objectives.
  - Composite health probes for distributed tracing.
  
- Model the identified application design as entities and relationships. Define the model so that health is business meaningful.
  - Map identified health signals across all modeled components to quantify health states at an entity level.
  - Use criticality to determine how health states should propogate through the model. For example, reporting components are seldom as important as payment components. They should therefore not have the same impact on overall health.   

- Visualize the modal in a tabular or graphical format for integration within your dashboard tooling.

- Use evaluated health states to trigger alerts and automated action. Health should be integrated within existing operational runbooks as a core observability data tenet.

### 'Traffic light' visualization

It is highly recommended that a _traffic light_ approach be used when visualizing your health model to make health states immediatly insightful. Assign green for healthy, yellow for degraded, and red for unhealthy. This will help you to trace application health states across dependency chains to quickly find the root cause of application degradation.

> [!NOTE]
> ​It is also recomended to consider accessibility requirements for the visualy impaired when dashboarding your health model.

### Critical system flows

The value of health modeling can only be fully realized when you incorprate business scenarios and context. This enables you to rationalize the business impact from operational incidents.

Your health model should surface the respective health of [critical system flows](https://learn.microsoft.com/en-us/azure/well-architected/reliability/identify-flows#identify-all-user-and-system-flows) and key subsystems to encapsulate your business scenarios. For example, the health model for an ecommerce application should be able to represent the current state of critical flows like user sign-in, checkout, process payment, etc.

Modeled flows should also capture the relative significance of the business processes they relate to. For example, in most scenarios the failure of a payment process is likely to be more significant than the failure of a reporting process. Hence, modeled system flows should consider their [relative importance and overall impact ](https://learn.microsoft.com/en-us/azure/well-architected/reliability/identify-flows#identify-business-impact-of-each-flow) on the health of the application.

### Monitoring data for health signals

It is essential that health be defined by correlating all types of monitoring data; metrics, logs, traces. This is necessary to ensure the health truly represents what it means for a particular entity or the application as a whole to be healthy.

Health should be used to automatically correlate application level events with resource level metrics to quantify the current application state. The overall health state should be affected by both application level issues and resource level failures.

> [!NOTE]
> All application resources should be configured to route diagnostic logs and metrics to the chosen log aggregation technology. [Azure Policy](https://azure.microsoft.com/services/azure-policy/) should also be used to ensure the consistent use of diagnostic settings across the application, which enforces the desired configuration for each Azure service.

#### Platform metrics

Platform level metrics and logs, such as CPU percentage, network in/out, and disk operations/sec, should be collected from underlying application resources to inform your health model and to detect or predict issues. This approach can also help to distinguish between transient and nontransient faults.

#### Application logs

Application logs are an important source of diagnostics data and a vital input to health. To gain insights when you need it most, follow these best practices for application logging:

- Use semantic, or *structured*, logging.

  With structured logs, it's easier to automate the consumption and analysis of log data at scale. We recommend storing Azure resource metrics and diagnostics data in an Azure Monitor Logs workspace rather than in a storage account. This way, you can create health signals using [Kusto queries](/azure/data-explorer/kusto/concepts/#kusto-queries) for quick and easy evaluation.

- Log data in the production environment.

  Capture robust data while the application is running in the production environment. You need sufficient information to inform health and to diagnose the cause of detected production issues.

- Log events at service boundaries.

  Include a correlation ID that flows across service boundaries. If a transaction flows through multiple services and one of them fails, the correlation ID helps you track requests across your application and pinpoint why the transaction failed.

- Use asynchronous logging.

  Synchronous logging operations sometimes block application code. This situation causes requests to back up as logs are written. Use asynchronous logging to preserve availability during application logging.

- Separate application logging from auditing.

  Audit records are commonly maintained for compliance or regulatory requirements. To avoid dropped transactions, maintain audit logs separately from diagnostic logs.

#### White-box and black-box monitoring

Use _white-box monitoring_ to instrument the application with semantic logs and metrics. Application level metrics and logs, such as current memory consumption or request latency, should be collected from the application to inform your health model, alowing the model to detect and predict issues.

Use _black-box monitoring_ to measure platform services and the resulting customer experience. Black-box monitoring tests externally visible application behavior without knowledge of the internals of the system. This approach is common for measuring customer-centric service-level indicators (SLIs), service-level objectives (SLOs), and service-level agreements (SLAs).

> [!IMPORTANT]
> It is highly recomended that you define SLIs and SLOs for the application as a whole and component business processes. These SLIs and SLOs should be mapped to health signals within the model. This ensures that your definition of health fully reflects the attainment of an acceptable level of service for the application.

#### Distributed tracing

[Telemetry correlation](/azure/azure-monitor/app/distributed-tracing-telemetry-correlation) should be used to ensure that transactions can be mapped through the end-to-end application across critical system flows. This process is vital for the root cause analysis (RCA) of failures. 

#### Health probes

The health and performance of an application can sometimes be difficult to detect using just raw monitoring data. To enhance the evaluation of health, implement health probes and run them regularly from outside your application. These probes should be created to explicitly check the health of your application and their response should be used as a signal input within your health model.

These health checks can be as simple as measuring response time for the application as a whole, for individual parts of the application, for specific services that the application provides, or for individual components. Probes can run processes to measure latency and check availability, or to extract information from the system. 

Health probes can be used in health checks performed by load balancers or can be invoked from an external watchdog service. A watchdog service aggregates health checks from across multiple components in the workload. Watchdogs can also host code that can perform immediate remediation for known health conditions.

Below is a pair of example health probe use cases which can be used to distinguish two distinct application states:

- *Liveness* is an indication if the application code should continue to run or needs to be rebooted. A failure response (or lack of response) from this probe means the process should be terminated and restarted.

- *Readiness* is an indication if the application component should continue to be used. A failure response (or lack of response) from this probe means to hold off on sending traffic to this component.

### Continuos improvement

The principal of continuous improvement should be applied to progressivly optimise the evaluation of health states within your model. As your application evolves, your health model must also evolve. 

Furthermore, health models should be treated as living and breathing application artefacts. This means that management best practices, such as adopting the principal of Infrastructure as Code (IaC), should also be applied to your health model.

## Using a health model

The health data within your health model should serve as the foundation for application operations, driving the detection and interpretation of any failures or operational instability.

### Health based alerting

Typically, there is a one-to-one mapping between monitoring data and an alert rule. This direct relationship often leads to negative operational characteristics, such as alert storms and alert noise. 

Consider the example of a cluster where alerts are configured at a vm level based on key metrics such as CPU utilization and error count. If the cluster suffers a failure, it is very likely that many alerts would be generated for each node. This can very quickly become overwhelming for operators, making issue identification and resolution challenging. Similarly, with the high number of configured alerts it is typical that a consistent alert volume can arise as a 'norm'. This ambient alert noise often leads to alerts going unnoticed or ignored.  

Health introduces a layer of seperation between monitoring data and alert rules. Many different signals can be used to define health, with a single alert rule configured to use the evaluated health state. Health therefore allows for intelligent alerting based on many signal inputs.

Since a health moel encapsulates business scenarios, it is also possilbe to elevate alerting to be scenario and business centric. For example, rather than alerting on the health of a cluater, it is possible to alert on the health of the system flows which depend on that cluster. Reusing the previous retail detection scenario, an alert can be defined to notify changes in the health of the process payments flow rather than underlying resources such as the Service Bus queue.

> [!NOTE]
> The ability to alert across all layters of the health model provides flexibility where different application personas are concerned. Application owners and product managers could be alerted to health state changes in key business scenarios or the application as a whole, while DevOps or operational resources could be alerted based on the health of compinent infrastructure or application components.

### Health model lenses

Health is a relative data concept that can vary across different personas, even in the context of the same application scope.For example a DevOps user may have a very different definition of health to a SecOps user who may be more concerned with intrusion indicators an specific exposed components. Similarly, a DBA user might only be interested in a subset of the application model through the database resources only.

Do not be afraid to derive varied lenses on the same health model, by interacting with the health data and model differently to satisfy the unique requirments of specific personas. Seperate models can also be constructed from an overlapping data set to achieve the same outcome.

### Health data retention

Like all monitoring data, health gradually loses it's value over time for most observability use cases. It is not recomended to keep health data for longer than 60 days to avoid unecessary costs for data that is no longer relevant to the runtime operational state of your application. Archiving can be performed if necessary to satisfy audit requirements or to empower AIOps scenarios orientated around long term pattern analysis.

> [!NOTE]
> Health is inherently an abstract data type, and without the context of the broader model it is very difficult to rationalise why state changes occured. When archiving health data outside the contextual scope of the model, be mindful that it might lose all meaning unless also coupled with the configuration state of the model.  

### Health trends and AIOps

Health provides a quantified baseline for detection investigation. It therefore serves as the foundation for AIOps capabilities to apply machine learning intelligence. AIOps scenarios enabled by health range from deriving additional insights from state changes and identifying recomended actions, to pattern analysis of health trends to drive prediction and model refinement. In essence, the health model itself can be used as the principal input to the machine learning models analysing health trends.

## Tooling for health modeling

Health modeling is a logical exercise that is tooling and technology agnostic. However, it is an intensive process that is often complex and requires significant engineering investment. 

Third-party observability tooling can be used to reduce this burden, providing a way to interface directly with and evaluate raw monitoring data. However, even with the use of third-party observability solutions, the process to build and use a health model remains complex and requires significant engineering investment as well as bespoke code. 

Until recently, third-party tooling was most feasible way to approach modeling the health of Azure applications. But that all changed with the release of health model capabilites within Azure Monitor. 

Azure Monitor health models enable you to natively construct and interact with health models in Azure. These capabilities expose health as a core data tenet under Azure Monitor. You can now natively use health as the basis for consistent and expedited operations within your Azure applications. 

Each health model is a data platform for health data, with native integrations across monitoring data stores, alerting, and out-of-the-box visualizations, both graphical and tabular. For advanced users, query/API data plane experiences allow flexible and extensible use of health states within your existing operational stack (e.g. Grafana dashboard).

Using Azure Monitor health model empowers operational users to troubleshoot dependencies and identify correlated/cascading issues, optimizes TTD and TTM, as well as enhancing alerting and significantly reduces monitoring/alerting noise.

<banner for ahm>

## Related links

- For implementing health probes in ASP.NET, see [Health checks in ASP.NET Core](/aspnet/core/host-and-deploy/health-checks).
- For information on monitoring metrics, see [Azure Monitor Metrics overview](/azure/azure-monitor/essentials/data-platform-metrics).
- For information on using Application Insights, see [Application Insights](/azure/azure-monitor/app/app-insights-overview).
- For design considerations and recommendations about a layered application health model, see the health modeling guidance found in the mission-critical workload design areas.
- For more hands on experience with health modeling, see [link to learn module]

## Next steps

> [!div class="nextstepaction"]
> [Recommendations for designing a reliable monitoring and alerting strategy](../reliability/monitoring-alerting-strategy.md)
