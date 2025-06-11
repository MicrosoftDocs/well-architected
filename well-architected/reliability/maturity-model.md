---
title: Reliability Maturity Model
description: Understand the maturity model levels of the reliability pillar.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 10/23/2024  
ms.topic: conceptual
---

# Reliability Maturity Model


Reliability is about ensuring that your solution is available and working when your users need and expect it to be. Counterintuitively, the way to achieve high reliability is to accept that things can go wrong. Instead of trying to prevent every problem, it's important to plan how your system responds when there are problems. Your business requirements help you to identify which potential problems you should harden your solution against.



:::image type="content" source="../_images/reliability.svg" alt-text="Example alt-text."::: 

# [**Level 1: Get resilient**](#tab/level1)

![Goal icon](../_images/goal.svg) **Establish a solid groundwork for resiliency in workload infrastructure and operations, rather than spending time on optimization tasks.**

Level 1 of the maturity model is designed to help workload teams build a strong foundation for system reliability. The focus is on bootstrapping, which is the process of setting up the basics for future reliability decisions. This stage mostly involves functional implementation with minor extensions to current practices. 

This stage includes researching, gaining insights, and creating an inventory of your systems. It also uses built-in reliability features on Azure, like enabling zone redundancy for immediate improvements.

By establishing these basics, you can prepare your team to advance through the levels of the reliability maturity model to progressively enhance your system's resilience and performance.

**Key strategies**
> [!div class="checklist"]
>
> - [Evaluate opportunities to offload operational responsibility](#-evaluate-opportunities-to-offload-operational-responsibility)
> - [Identify the critical user and system flows](#-identify-the-critical-user-and-system-flows)
> - [Select the right design model, resources, and features](#-select-the-right-design-model-resources-and-features)
> - [Deploy with a basic level of redundancy](#-deploy-with-a-basic-level-of-redundancy)
> - [Enable metrics, logs, and traces to monitor flows](#-enable-metrics-logs-and-traces-to-monitor-flows)
> - [Start building a failure mitigation playbook](#-start-building-a-failure-mitigation-playbook)
> - [Add mechanisms to recover from transient failures](#-add-mechanisms-to-recover-from-transient-failures)
> - [Run basic tests](#-run-basic-tests)

#### &#10003; Evaluate opportunities to offload operational responsibility 

This strategy is fundamentally a _build_ versus a _buy or rely_ approach. The decision depends on how much responsibility is manageable at this stage while still supporting future development. You want to use resources that are relevant to the workload, but you should always explore opportunities to offload their maintenance. Here are some classic use cases where you might want to apply this approach.

- **Offload responsibilities to the cloud platform** by choosing platform as a service (PaaS) solutions. They provide ready-made solutions for common resiliency needs like replication, failover, and backup stores. When you take this approach, the cloud provider handles hosting, maintenance, and resilience improvements. 

   For example, the cloud provider replicates data across multiple compute nodes and distributes the replicas across availability zones. If you build your own solution on virtual machines (VMs), you need to manage these aspects yourself, which can be time-consuming and complex.

- **Offload responsibilities for operations** that aren't directly tied to the workload's business objectives. Some specialized operations, such as database management and security, can potentially affect the reliability of your workload. Explore the possibility of having experienced teams, technology, or both handle those tasks.

    For example, if your team doesn't have database expertise, use managed services to help shift the responsibility to the provider. This approach can be useful when you start out because it allows your team to focus on the functionality of the workload. Many enterprises have shared, centrally managed services. If platform teams are available, use them to handle these operations. However, this approach might add dependencies and organizational complexity.

    Alternatively, if your team has the right expertise, you might make an explicit decision to use their skills and select services that don't include management capabilities. 

- **Offload responsibilities to non-Microsoft vendors.** Choose off-the-shelf products as the starting point. Build customized solutions only when they contribute to your workload's business value.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** If the _buy or rely_ option partially fulfills your requirements, you might need to implement custom extensions. This method can result in a "customization lock-in" situation, where updates and modernization become impractical. Regularly review your requirements and compare them with the solution's capabilities. Develop an exit strategy for when there is a significant deviation between the two.
>
>  The opposite scenario is also a risk. Although the _buy or rely_ option might seem simpler at first, it might require re-evaluation and redesign later if the limitations of the PaaS service, vendor solution, or platform-owned resources don't meet the necessary granularity or level of autonomy needed for the workload.

#### &#10003; Identify the critical user and system flows 

Breaking down the workload into flows is crucial at this stage. Focus on _user_ and _system_ flows. User flows determine user interactions, and system flows determine communication between workload components that aren't directly associated with user tasks.

For example, in an e-commerce application, customers perform front-end activities like browsing and ordering. Meanwhile, back-end transactions and system-triggered processes fulfill user requests and handle other tasks. Those distinct flows are part of the same system, but they involve different components and serve different purposes.

Start building a catalog of flows at this stage. Observe user interactions and component communication. List and categorize flows, define their start and end points, and note dependencies. Document outcomes and exceptions by using diagrams for clarity. This catalog can serve as an important tool for the initial conversation with business stakeholders to identify the most important aspects from their perspective. This conversation can inform the first level of prioritization.

Classify a flow as critical by evaluating the risk and impact on primary business activities. If you expect an outage, graceful degradation focuses on maintaining these critical flows. In the e-commerce example, critical flows include product searches, adding items to the cart, and checkout because these tasks are essential for business. Other processes, like updating product data and maintaining product images, aren't as critical. Ensure that critical flows remain operational during an outage to prevent revenue loss by allowing users to continue searching for products and adding items to the cart.

> [!NOTE]
> A business process can be critical even if it's not time sensitive. Time criticality is a key factor. For example, meeting auditing requirements is a critical process, but you might not need to present data for an audit immediately. The process remains important, but its reliability isn't time critical because recovery within a few hours is acceptable.

For more information, see [Azure Well-Architected Framework: Optimize workload design by using flows](/azure/well-architected/design-guides/optimize-workload-using-flows).

#### &#10003; Select the right design model, resources, and features 

You should apply this strategy at the following levels: 

- **Architecture:** The workload design should account for reliability expectations at various infrastructure layers. Your initial decisions might be the choice between containerization or PaaS for hosting the application. Or, you might consider networking setups like hub and spoke or a single virtual network.

    You should also set boundaries that create segmentation based on functionality. For example, instead of hosting everything on one VM with a single-zone virtual disk, consider splitting compute and data storage and using dedicated services.

    > [!CAUTION]
    > In migration scenarios, adopting a lift-and-shift approach without reviewing new opportunities can lead to missed benefits and inefficiencies. It's important to research modernization early to avoid being stuck with setups that are difficult to change and to take advantage of better options and improvements.

- **Azure services:** Use decision trees to help you select the right services for your design. Choose components that meet your current needs, but remain flexible so that you can switch services as your workload evolves and requires more features.

- **SKUs or tiers within Azure services:** Review the features of each SKU and understand the platform's availability guarantees. Evaluate service-level agreements to understand the coverage provided around the published percentile.

- **Features that support reliability:** Choose cloud-native services to enhance availability through simple configurations without changing the code. It's important to understand the options and intentionally select configurations, such as increasing zone redundancy or replicating data to a secondary region.

#### &#10003; Deploy with a basic level of redundancy

Within each part of your solution, avoid single points of failure, such as single instances. Create multiple instances for redundancy instead. Azure services often handle redundancy for you, especially with PaaS services, which usually include local redundancy by default and options to upgrade. Preferably, use zone redundancy to spread those instances across multiple Azure datacenters. If you don't, at least ensure local redundancy, but this method comes with higher risk. In future levels, you evaluate whether your reliability requirements might be met by extending the solution with geo-redundant components.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** One significant trade-off is the increased cost of redundancy. Also, cross-zone communication can introduce latency. For legacy applications that require minimal latency, redundancy can degrade performance.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** If an application isn't designed for a multiple-instance environment, it might struggle with multiple active instances, which can lead to inconsistent data. Also, if an application is built for an on-premises setup that has low latency, using availability zones might disrupt its performance.

#### &#10003; Enable metrics, logs, and traces to monitor flows

Choose platform-native tools like Azure Monitor to ensure visibility of metrics, logs, and traces. Use built-in features to set alerts for potential problems. You should have basic alerting in place to send notifications and get alerts. Take advantage of Azure platform capabilities that indicate changes in the health status of services, such as: 

- [Azure Service Health](/azure/service-health/service-health-overview) to check the platform-level health status.

- [Azure Resource Health](/azure/service-health/resource-health-overview) to check the resource-level health status.

Set up [Azure Monitor action groups](/azure/azure-monitor/alerts/action-groups) for both the infrastructure and the application.

>  :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** As you collect more logs, you need to manage the increasing volume, which affects the storage-related costs of those logs. Use retention policies to manage the volume. Use Azure Monitor to set a daily cap on a workspace. For more information, see [Configuration recommendations for Reliability](/azure/well-architected/service-guides/azure-log-analytics#configuration-recommendations-for-reliability).

Start building observability at the following layers.

##### Infrastructure

Start by enabling diagnostic logs and making sure that you gather native metrics from platform components for analysis. Gather information about resource usage, such as CPU, memory, input/output, and network activity. 

##### Application

Collect application-level metrics, such as memory consumption or request latency, and log application activities. Do logging operations in a thread or process that's separate from the main application thread. This approach doesn't cause logging to slow down the application's primary tasks.

Also, check the [basic availability tests](/azure/azure-monitor/app/availability) in Application Insights.

##### Data

To monitor databases at a basic level, collect key metrics that the database resources emit. Similar to infrastructure components, track resource usage in the context of data stores, such as networking metrics. Gathering data about how connections are pooled is important for improving efficiency at later stages. 

For reliability, it's important to track connection metrics, such as monitoring active and failed connections. For example, in Azure Cosmos DB, a 429 status code is returned when the number of requests exceeds the allocated request units and connections start failing.

#### &#10003; Start building a failure mitigation playbook

Failures range from intermittent to slightly extended transient failures and catastrophic outages.

In Level 1, focus on platform failures. Even though they're beyond your control, you should still have strategies for handling them. For example, address zonal outages by using availability zones. Anticipate transient faults at the platform level and handle them in your workload.

The process of handling these failures varies based on complexity. Start documenting potential platform-level failures, their associated risks, and mitigation strategies. This exercise is primarily theoretical and matures with automation at later levels.

You should document failures, including factors like their likelihood, impact, and mitigation strategies. Use a criticality scale that aligns with your workload's goals. Your scale might include:

- High. A complete system outage that results in significant financial loss and a decline in user trust.

- Medium. A temporary disruption that affects part of the workload and causes user inconvenience.

- Low. A minor software problem that affects a nonessential feature of the application and causes minimal downtime for users.

Here's an example template:

|Problem|Risk|Source|Severity|Likelihood|Mitigation
|---|---|---|---|---|---|
|Transient network failure | The client loses their connection to the application server. | Azure platform | High | Very likely | Use design patterns in client-side logic, such as retry logic and circuit breakers.|
|Zone outage | The user can't reach the application. | Azure platform | High | Not likely | Enable zone resiliency on all components. |
|Transport Layer Security (TLS) certificate expiration | The client can't establish a TLS session with the application. | Human error | High | Likely | Use automated TLS certificate management. |
|CPU or memory usage reaches defined limits and causes the server to fail. | Requests time out. | Application | Medium | Likely | Implement automatic restarts. |
|Component is unavailable during an update. | The user experiences an unhandled error in the application. | Deployment or change in configuration | Low | Highly likely during deployments and not likely at other times | Handle components in client-side logic. |

At Level 1, don't strive for completeness because there are always unforeseen failure cases. If you experience unexpected outages, document the causes and mitigations in the playbook. Treat this asset as a living document that you update over time.

#### &#10003; Add mechanisms to recover from transient failures

In a cloud environment, transient failures are common. They indicate short-term problems that retries can usually resolve within seconds.

Use built-in SDKs and configurations to handle these faults to keep the system active. Built-in configurations are often the default setting, so you might need to test to validate the implementation. Also, implement patterns that are designed to handle transient failures in your architecture. For more information, see [Cloud design patterns that support reliability](./design-patterns.md).

Persistant problems might indicate a failure that isn't transient or the start of an outage. This scenario requires more than just fixing localized problems within the application. It involves examining the critical user and system flows of the system and adding self-preservation techniques and recovery efforts. These methods are mature practices that Level 2 describes.

#### &#10003; Run basic tests

Integrate basic reliability testing in the early stages of the software development lifecycle. Look for opportunities to do testing, starting with unit tests to validate functionality and configurations.

Also, develop simple test cases for the problems that you identify in the risk mitigation playbook. Focus on higher impact, lower effort mitigations. For example, simulate network outages or intermittent connectivity problems to see how your retry logic resolves the disruptions.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Testing often introduces friction in the development cycle. To mitigate this risk, make reliability testing trackable alongside development tasks.
>
> Feature development is the priority, and testing can introduce friction in the development cycle. It's easier to start testing before feature development is complete. Designing nonfunctional aspects of the application at the beginning allows you to extend them as you add functional capabilities, rather than building up a backlog of problems to address later. Although this approach requires more effort initially, it's manageable and prevents larger problems later.

# [**Level 2: Self-preservation**](#tab/level2)

![Goal icon](../_images/goal.svg) **Ensure that the system remains functional and stable by incorporating self-preservation capabilities and having a basic recovery plan to manage failures.**

Failures in the cloud are inevitable. Your resiliency strategies should strive to keep the system functional under all conditions. Level 1 introduces methods for addressing transient failures. Level 2 focuses on incorporating self-preservation strategies to prevent, detect, and recover from longer-lasting failures. If left unresolved, these problems can turn into full outages.

The critical flows that you identify in Level 1 take priority. They require increased resiliency and recovery efforts for all components, including applications, services, and databases. Expect to adjust your initial provisioning sizes, instance counts, and autoscale policies to reduce reliability risks.

In this level, be intentional about your monitoring and testing practices. Use advanced monitoring techniques that align with technical needs and are scoped to development teams. Expand the simple playbook to cover architectural components that you develop and own, such as application code.

**Key strategies**
> [!div class="checklist"]
>
> - [Evaluate the current state of resiliency to protect against failures](#-evaluate-the-current-state-of-resiliency-to-protect-against-failures)
> - [Add technical capabilities in your monitoring system](#-add-technical-capabilities-in-your-monitoring-system)
> - [Extend your failure mitigation playbook](#-extend-your-failure-mitigation-playbook)
> - [Develop a basic recovery plan](#--develop-a-basic-recovery-plan)
> - [Create test plans](#--create-test-plans)
> - [Assess the impact of scaling operations on reliability](#-assess-the-impact-of-scaling-operations-on-reliability)

#### &#10003; Evaluate the current state of resiliency to protect against failures

- *Is the level of redundancy good enough to withstand failures?* Define a redundancy strategy that specifies the number of redundant resources to maintain. Determine where to place these resources, whether locally, across zones, or in geographically distributed locations. Evaluate the cloud platform's settings and select a level that meets business needs and acceptable trade-offs.

- *Are the workload components isolated enough to contain their failures?* Patterns like the [Bulkhead pattern](/azure/architecture/patterns/bulkhead) help build resilience and fault isolation. The Bulkhead pattern partitions a system into isolated components, known as *bulkheads*, to prevent failures from cascading to other parts of the system.

- *Do components on the critical path communicate asynchronously?* If not, use communication methods, such as queues. This approach keeps the system operational even if a downstream component fails. It also prevents the system from entering an indeterminate state. Explore Azure options, including Azure Service Bus for queues and Azure Event Hubs for event streams.

    > :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Asynchronous communication can help prevent cascading failures by decoupling processes. However, it adds latency in the communication path, which can pose a problem for critical components. Evaluate the performance impact before you make any design pattern changes.

- *Are the operations designed for consistency?* Assets such as application secrets and certificates can expire and require regular refreshing. Inconsistencies in routine updates can result in reliability problems.

    Ideally, identify and eliminate ongoing human-operated tasks because they're error-prone and can cause inconsistencies that pose reliability risks. Offload as many operational tasks as possible to the cloud provider. For example, use managed identities that Microsoft Entra ID provides and Transport Layer Security (TLS) certificates that Azure Front Door manages.

    Monitoring is required for proactive measures, such as tracking certificate expiration and receiving notifications. The application should log important events, like a TLS certificate nearing expiration. Using multiple methods to check for potential failures helps ensure that necessary actions are taken.

#### &#10003; Add technical capabilities in your monitoring system

At Level 1, you gathered monitoring data from the workload components, with a focus on infrastructure. Basic analysis is complete and basic alerts are set. This setup is essential for understanding the baseline performance of workload components and identifying anomalous behavior.

Level 2 takes monitoring a step further by adding advanced observability capabilities to your workload resources and adopting a more structured approach to analyzing monitoring data. Take advantage of analytics tools that your cloud service provides. For example, Azure Monitor insight tools, such as VM insights and network insights, provide visualizations of health and performance across dependencies.

Plan observability capabilities at the following layers.

##### Application

- **Respond to health status probing.** Enable the application to respond to health check requests from probes. The application should have dedicated endpoints for health checks that provide status information, such as *healthy* or *unhealthy*, at a minimum. This approach allows monitoring systems to assess whether the application is functioning properly and can handle requests, or if there are problems that need to be addressed.

    Azure load balancing services, such as Azure Front Door, Azure Traffic Manager, Azure Application Gateway, and Azure Load Balancer, support health probes. Health probes send health check requests to applications.

- **Advance to semantic logging.** Include structured information about events and actions in the application. With structured logging, log data is recorded in a consistent format by using a well-defined schema. This schema makes it easier to build automation, search, and analyze in later stages. Include specific fields like timestamps and error codes to help quickly identify and troubleshoot problems.

- **Implement distributed tracing.** When a request flows through different components of the system, it's important to capture trace data across boundaries. This data is useful for getting insights into application behavior and identifying performance bottlenecks, errors, and latency problems. Azure Monitor supports OpenTelemetry-based data collection with Application Insights.

##### Data

- **Track query duration, failed queries, and other relevant metrics.** Long-running queries can indicate resource constraints and possibly a need to adjust schema design.

  At this stage, your database has been operating for some time. Pay attention to the data growth rate, especially in tables that grow unexpectedly fast. This information is crucial for planning future storage needs and addressing performance problems early. 

- **Monitor the status of database replication by using the tools and dashboard that the database management system provides.** For example, if you use Azure Cosmos DB, use Azure Cosmos DB insights. For Azure SQL Database or Azure SQL Managed Instance, consider using the database watcher to get diagnostic details about your databases.
  
  As the database grows, schema problems might become more apparent, which affects performance. To optimize query efficiency, consider adding indexes or modifying the schema because these changes can affect reliability.

##### Operations

Level 1 focuses on the preceding layers. At Level 2, you start building operations around the monitoring system.

- **Keep logs long enough to get insights.** From a reliability perspective, configure the retention duration so that you can collect enough data to detect failure patterns, troubleshoot problems, and perform root cause analysis.

- **Monitor backup and recovery processes.** Ensure that the backups are successfully stored in locations as planned and that workload data is recovered within a reasonable timeframe. Monitoring those processes are important for setting baselines for your recovery point objective (RPO) metrics at later levels.

#### &#10003; Extend your failure mitigation playbook

Level 1 focuses on the expected platform failures. In Level 2, you address failure points on components and operations within your own workload. As your code runs on the platform, interaction points between the platform and application increase. Expect failures from bugs in your code, unsuccessful deployments, and human errors. Mitigate these problems by using self-preservation or recovery tactics.

Extend your failure mitigation playbook to include bugs and deployment problems. The following table builds on the template from Level 1:

| Problem | Risk | Source | Severity | Likelihood | Mitigation |
| ---------| ------| --------| ----------| ------------| ------------|
| Code doesn't handle at-least-once message delivery. | Duplicate processing of messages from the bus results in data corruption. | Application | High | Likely | - Redesign to use bus partitioning and build idempotency into the process. <br><br> - Move away from a competing consumers model, which makes performance a trade-off. |
| Daily storage backup script fails to run. | RPO is violated because the data is older than 24 hours. | Automated process | High | Not likely | Set up an alert on the backup process. |
| Regular user and usage spikes after a new release. | Application performance degrades and user requests time out. | Application | High | Not likely | Configure schedule-based scale-out operations. |
| A concurrency bug is in code. | Unpredictable behavior and possible data corruption. | Application | High | Likely | Use safe forms of concurrency and avoid manual handling of concurrency controls. |
| Unexpected failure during deployment leaves the environment in an inconsistent state. | Application outage. | Deployment pipelines | Medium | Likely | Use blue-green deployments, canary deployments, or other approaches to progressively roll out changes. |

This exercise can become overwhelming if you try to account for every possible failure. To make it easier, focus on the components that are part of the critical user flows. This living document continues to grow as the workload matures.

#### &#10003;  Develop a basic recovery plan

The failure mitigation playbook is the basis for creating a basic recovery plan. Mitigation strategies can include design pattern implementation, platform configuration adjustments, live site incident management, automated tests, and training personnel to detect problems during code reviews.

Start with a graceful degradation strategy, which includes temporary fixes when parts of the system don't work properly. The goal is to continue to serve users despite failures by disabling nonworking parts and adjusting the user experience. For example, if a database is down, the application can disable the affected feature and inform clients that the service is temporarily unavailable by using HTTP status codes.

For graceful degradation to work, isolate system components so that only the affected parts experience problems while the rest of the components continue to function. Use the Bulkhead pattern to achieve fault isolation.

Take this opportunity revisit design choices that might slow recovery. For example, pointing Domain Name System (DNS) records directly to your application on Azure App Service can cause delays during recovery because of DNS propagation. Use a dedicated service like Azure Front Door as the ingress point for easier reconfiguration during recovery steps.

Expect this basic plan to evolve into a full disaster recovery plan at more mature levels.

#### &#10003;  Create test plans

Create test plans by simulating outages and problems identified in the risk mitigation playbook. Supplement those mitigations with simple test cases to ensure that they work as expected and are feasible. Verify that these features operate correctly, and conduct degradation tests to see how the system performs when specific components fail. Keep the outcome simple by ensuring that the test either fails or passes.

Use test tools like mocking frameworks to inject faults into HTTP requests, which help you test retry policies more explicitly. Azure Chaos Studio provides a comprehensive test suite for simulating component outages and other problems, which makes it a valuable service to explore. You can gradually adopt Chaos Studio as you become familiar with its features.

#### &#10003; Assess the impact of scaling operations on reliability

To handle spikes in load, critical components must be able to scale out or scale up efficiently. Take advantage of autoscaling capabilities that Azure provides. These capabilities adjust the capacity limits of a service based on predefined configurations. This adjustment allows you to scale the service up or down as needed.

- Identify potential bottlenecks and understand the risks that they might pose. For example, high throughput shouldn't cause the flow to break down.

- Understand the load patterns. Static usage patterns might make bottlenecks less critical, but changes in usage and consumption dynamics can worsen the risks.

    > [!NOTE]
    >
    > There might be components that can't scale out, such as monolithic databases and legacy applications. Proactively monitor the load curve to allow for re-architecting if needed.

- Decide on scaling limits that are reasonable based on performance and reliability requirements. For performance concerns, scaling up gradually is most common. However, reliability concerns for critical flows might require more rapid scaling to avoid outages. In either case, avoid infinite scaling.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** When you deal with performance-related problems, scaling can be a useful mitigation strategy. However, scaling is a temporary fix and not a solution. Investigate and solve the underlying problem, such as a memory leak or a runaway process. Otherwise, you risk applying the same mitigation again at another tipping point and paying for resources that you don't need. By addressing the root cause, you can ensure long-term stability and cost-efficiency.

# [**Level 3 - Recovery readiness**](#tab/level3)

![Goal icon](../_images/goal.svg) **Set reliability objectives and targets to keep the team accountable on recovery mechanisms.**

At the initial levels, teams focus on easy wins and basic capabilities. They start small, solving simple issues to build a strong foundation, relying mostly on Azure reliability capabiltiies. As teams grow, they handle more technical challenges related to their own assets and processes.

At Level 3, the teams should integrate business insights and technical skills for recovery planning. They set objectives and plan recovery processes with advanced monitoring. This approach helps Site Reliability Engineers (SREs) meet reliability targets quickly.

**Key strategies**
> [!div class="checklist"]
>
> - [Formalize reliability capabilities as objectives](#-formalize-reliability-capabilities-as-objectives)
> - [Monitor proactively using your health model](#-monitor-proactively-using-your-health-model)
> - [Set actionable alerts](#-set-actionable-alerts)
> - [Conduct failure mode analysis (FMA)](#-conduct-failure-mode-analysis-fma)
> - [Prepare a disaster recovery plan](#-prepare-a-disaster-recovery-plan)

#### &#10003; Formalize reliability capabilities as objectives 

Reliability objectives help set accountability on the workload teams. It's important to have a collaborative conversation with business stakeholders,  discussing recovery times and costs, and making compromises to align with business goals. Gather the stakeholders and conduct this discussion as a workshop. Here are some points that you can consider as workshop agenda:

- **Explain metrics behind objectives**. Start by explaining the key metrics that are used to define objectives like Service Level Objective (SLO), Recovery Time Objective (RTO), Recovery Point Objective (RTO). Show how those metrics align with business goals. Focus on the critical user flows. For example, in an eCommerce application the RTO for updating email preferences is less important than users checking out shopping carts.

- **Be clear on the tradeoffs**. Stakeholders often have unrealistic expectations. Explain why it's impossible to achieve everything; how expanding the scope of the objectives affects budget, operational requirements, and performance.

- **Propose objective targets**: Based on architectural experience and workload design, suggest targets such as 99.9% uptime, and RPO and RTO of 4 hours. Facilitate a discussion for stakeholders to provide feedback and make adjustments. Ensure that both business and technical stakeholders guard against unrealistic expectations, and approach discussions with a collaborative mindset.

- **Reach consensus or decision**. Aim for consensus, but if it's not possible, have a decision-maker finalize the targets to ensure progress. 


#### &#10003; Monitor proactively using your health model

At Level 1, monitoring data from workload components, including platform services and applications, is gathered. Basic analysis and alerts are set to establish baseline performance and identify anomalies. In Level 2, the focus shifts to obtaining observability data from workload components, such as application code.

Level 3 enhances monitoring by adding business context to critical flows and defining Healthy, Unhealthy, and Degraded states through health modeling. Stakeholder agreement is needed to determine acceptable user experience compromises and should be used as input for defining health states. 

Health modeling requires operational maturity and proficiency in monitoring tools. The team analyzes raw data, performance levels, and logs to define custom metrics and thresholds, which establish the health state of the flow. They should understand how those values map to the overall health state of the system. Clear definitions and thresholds should be shared with stakeholders.

Visualize the health model in dashboards to help SREs quickly pinpoint issues by focusing on unhealthy or degraded flows. 

The health model defines the application's status and critical flows. Green indicates all critical flows are operating well, red indicates a failure, and yellow shows trends towards issues. Iterating through health model versions ensures reliability and accuracy, requiring significant effort for large applications.

A change in the health state should be configured as alerts. However, the criticality of the component must be taken into consideration to keep alerts intentional.

> Refer to: [Well-Architected Framework: Health modeling](../design-guides/health-modeling.md).

#### &#10003; Set actionable alerts 

To improve response efficiency, clearly define alerts with sufficient information for quick action. Configure the severity, name, and description of alerts carefully, especially severity levels. Every event isn't an emergency. Carefully consider severity levels and define what constitutes each level, such as whether a CPU spike from 80% to 90% is an emergency. Determine appropriate thresholds to effectively define alerts. 

Detailed alert names and descriptions can help save time and effort during troubleshooting. 

Effective alert management involves ensuring that alerts notify the right people at the right time. Frequent and annoying alerts indicate a need for adjustment and are counterproductive because they're easily ignored. Minimize noise by setting appropriate thresholds to discard false alarms. Explore opportunities where automation can trigger operational procedures. 

Create a single landing page with all necessary information to troubleshoot alerts efficiently. This saves time compared to logging into the Azure portal and searching for metrics. If Azure Monitor's built-in capabilities don't meet your needs, consider building a custom dashboard.

#### &#10003; Conduct failure mode analysis (FMA)

In the earlier levels, you created a simple failure mitigation playbook for individual components. At this level, evolve that playbook into a formal  failure mode analysis (FMA) exercise, where the goal is to proactively identify potential failure modes.

FMA involves identifying potential points of failure within your workload and planning mitigation actions, such as self-healing or disaster recovery. To start, monitor for increased error rates and detect impacts on critical flows. Based on past experiences and test data, identify potential failures and their blast radius, focusing on major issues like region down.

It's important to classify the actions as preventative or reactive. While preventative measures are intended to anticipate risks before they can cause an outage and reduce their likelihood or severity, reactive actions take place to mitigate a degraded health state or an outage.

In the eCommerce example application, the workload team wants to do FMA to prepare themselves for a big event. One of the key user flows is adding items to the card. The components that are part of the flow are the frontend, CartAPI, ProductCatalogAPI, UserProfileAPI, PricingAPI, Azure CosmosDB, and Event Hubs.


| Issue | Risk | Potential Source | Severity | Likelihood | Actions |
|-------|------|------------------|----------|------------|---------|
| Number of orders received drops below 100 per hour, with no corresponding drop in user session activity | Customers are unable to place orders even though the application is available | CartAPI, PaymentsAPI | High | Not likely | **Reactive actions:** <br>- Review health model or monitoring data to identify the issue <br>- Test the application to validate its functionality <br>- If a component outage occurs, perform a failover to another set of infrastructure <br> **Preventative actions:** <br>- Place synthetic orders to verify the flow is working <br>- Improve observability to ensure end-to-end flow is monitored |
| Unexpected increase in load causes timeouts when storing orders to Azure Cosmos DB | Customers are unable to place orders, or receive unsatisfactory performance if they can | Cosmos DB | High | Not likely | **Reactive actions:** <br>- Verify load based on application telemetry <br>- Scale up Cosmos DB RUs temporarily <br> **Preventative actions:** <br>- Configure autoscale <br>- Revisit expected load and recalculate scale rules <br>- Move some activities to a background process to reduce the database load from this flow |
| Recommendations service goes completely offline | Shopping cart page fails to load due to exception invoking recommendations service | Application | Medium | Not likely | **Reactive actions:** <br>- Implement graceful degradation strategy to disable recommendation functionality, or display hard-coded recommendations data, in the shopping cart page when an exception happens accessing the service |
| Intermittent timeouts accessing the pricing API from the shopping cart page under heavy load | Intermittent failures in the shopping cart page due to failures accessing the cart service | Application | Medium | Likely (under heavy load) | **Reactive actions:** <br>- Implement cache pricing value in the shopping cart data store, together with a cache expiry timestamp <br>- Only access the pricing API when the pricing data cache is expired |

FMA analyses are complex and can be time-consuming, so build your analysis progressively over time. This is an iterative process that continues to evolve at later stages. 

> Refer to: [RE:03 Recommendations for performing failure mode analysis - Example](./failure-mode-analysis.md#example).

#### &#10003; Prepare a disaster recovery plan

In Level 2, you created a recovery plan focusing on technical controls to restore system functionality. However, a disaster requires a more comprehensive plan. A disaster involves catastrophic loss or failure, and disaster recovery plans are process-based, covering communication, detailed recovery steps, and may include technical artifacts like scripts.

First, identify the types of disasters to plan for, such as region outages, Azure-wide outages, infrastructure failures, database corruption, and ransomware attacks. Then, develop recovery strategies for each disaster, ensuring mechanisms are in place to restore operations. Business requirements, RTO, and RPO, should inform disaster recovery plans. Low RTO and RPO require explicit and automated processes, while forgiving RTO and RPO allow for simpler recovery methods and manual analysis and response. 

Disaster recovery mainly involves these actions: 

- **Notify the responsible parties**. It's important to have clarity on who to involve and when. The team should be trained in processes, have the right permissions, and understand their roles in recovery. Some responsibilities, like the CEO reporting to the market or handling regulatory requirements, should be identified early.

    Ideally, have separate recovery and communication roles, assigning different people to each. Initially, the IT operations person who discovers the issue might handle both roles, but as the situation escalates, senior personnel handle technical recovery while a business person manages communications.

- **Make business decisions**. During a disaster, stress levels will be high, so when creating a DR plan, have ongoing discussions between the technical team and business stakeholders on "what-if" situations to prepare some preliminary decision options. Decision-making should cover downtime and data loss consequences. For example, should workload resources run in one Azure region with backups in another? Should IaC assets be prepared in advance to create new resources or restore from backup during failover? 

    Actions taken as per DR plans can be destructive in nature or have significant side effects. Understand the options, weigh their pros and cons, and know when to apply them. For example, evaluate if recovery to a different region is worthwhile if the primary region is expected to be up and running within a tolerable time window.

- **Restore system operations**. During a disaster, focus should be on restoring operations and not identification of the cause. For technical recovery, especially in region failover, decide in advance on approaches like active-active, active-passive, warm, or cold standby.   
    
    Prepare specific recovery steps based on the chosen approach. Start with a concrete list of steps to restore operations. As the process matures, aim to define the disaster recovery plan as a script with minimal manual interaction. Version-control and store the script securely for easy access. This approach requires more upfront work but reduces worry during the actual incident.
    
    For key strategies, see [Deploy in active-passive for disaster recovery](./highly-available-multi-region-design.md#deploy-in-active-passive-for-disaster-recovery).

- **Conduct post-incident analysis**. Identify the cause of the incident and find ways to prevent it in the future. Make changes to improve recovery processes. This exercise might also uncover new strategies. For example, if the system switched to the secondary, determine if the primary is still needed and what the failback process should be.

A disaster recovery plan is a living document that evolves with your workload. As new components and risks are introduced, update the plan accordingly. Also, revise it based on experiences from drills or real disasters, gathering realistic information from DR operators.

# [**Level 4 - Maintain stability**](#tab/level4)

![Goal icon](../_images/goal.svg) **Control risks stemming from technical and operational changes and prioritize incident management.**

In the previous levels, the workload team was focused on building features and getting the system up and running. At maturity level 4, the focus shifts to keeping the system reliable in production. This means that incident management becomes just as important as making sure any changes introduced are thoroughly tested and deployed safely to avoid making the system unstable.

This requires enhancements in operational controls, such as investing in dedicated teams to handle reliability incidents, as well as technical controls to further improve system reliability beyond the critical parts bolstered in previous levels.  As the system remains in production, data is expected to grow, which may require some redesign, such as partitioning, to ensure reliable data access and maintenance.

**Key strategies**
> [!div class="checklist"]
>
> - [Reliable change management](#-reliable-change-management)
> - [Invest in dedicated team to handle incidents](#-invest-in-dedicated-team-to-handle-incidents)
> - [Automate self-healing processes](#-automate-self-healing-processes)
> - [Extend resiliency to background tasks](#-extend-resiliency-to-background-tasks)

#### &#10003; Reliable change management

The biggest reliability risks occur during changes, such as software updates, configuration adjustments, or process modifications. These events are critical from a reliability standpoint. The goal is to minimize the likelihood of issues, outages, downtime, or data loss. Each type of change requires specific activities to manage its unique risks effectively.

At Level 4, Reliability intersects with safe deployment practices described in Operational Excellence, in maintaining a stable environment and managing change based on production learnings. Reliable change management is a requirement for achieving workload stability. Here are some key aspects:

- **Reacting to platform updates**. Azure services have different mechanisms to update services. Here are some pointers:

    - Familiarize yourself with the maintenance and update policies of each service you use. This includes knowing whether the service supports automatic or manual upgrades and the timeframe for manual upgrades.

    - For services with planned updates, manage them effectively by scheduling during low-impact times. Avoid automatic updates and defer them until you've assessed the risk. Some services let you control the timing, while others offer a grace period. For example, with AKS, you have 90 days to opt-in before the update is automatic. Test updates in a non-production cluster that mirrors your production setup to avoid regressions.

        Apply updates gradually, not all at once. Even if testing shows the update is safe, applying it to all instances simultaneously can be risky. Instead, update a few instances at a time, waiting between each set.

    - Regularly check for notifications about updates, which may be available in activity logs or other service-specific channels. 

    - Monitor for sudden or gradual changes after an update is applied. Ideally, your health model should notify you of such changes.

- **Thorough testing with automation**. Integrate more testing into your build and deployment pipelines when rolling out changes. Look for opportunities to convert manual processes to automated parts of your pipelines.

    Do comprehensive testing (using combination of different types of tests at various stages) to confirm changes work as expected and don't affect other parts of the application. For example, positive testing verifies that the system behaves as expected. It should validate that there are no errors and traffic flows correctly.

    When planning updates, identify testing gates and the types of tests to apply. Most testing should occur in pre-deployment stages, but smoke tests should also be conducted in each environment as it gets updated.

- **Follow safe deployment practices**. Use deployment topologies with bake times and SDP processes. Implement safe deployment patterns like Canary and Blue-Green deployments for flexibility and reliability. 

    For example, in Canary deployments, a small subset of users receives the new version first, allowing for monitoring and validation before rolling it out to the entire user base. Techniques like feature flags and dark launches allow testing in production before rolling out changes to all users.

 - **Update disaster recovery plan**. Regularly update your plan so that remains relevant and effective. Avoid using outdated instructions and make sure the plan reflects the current state of your system now that it's deployed to production and users rely on it. Incorporating lessons learned from drills and actual incidents. 

For more information, see [Operational Excellence Level 4](../operational-excellence/maturity-model.md#level-4---change-management).

#### &#10003; Invest in dedicated team to handle incidents

Initially, the development team might be involved during incidents. At Level 4, invest in Site Reliability Engineering (SRE) for incident management. SREs specialize in production issues and are experts in efficiency, change management, monitoring, emergency response, and capacity management. An proficient SRE team can significantly reduce dependency on engineering teams. 

Equip SREs with the tools, information, and knowledge to handle incidents independently, reducing dependency on the engineering team. They should be trained in the playbooks and workload health modele developed in the previous levels to quickly identify common patterns and start the mitigation process.

The engineering team should have time to reflect on recurring issues and develop strategies for dealing with them, rather than addressing them individually.

#### &#10003; Automate self-healing processes

In the previous levels, self healing strategies were designed by using redundancy and design patterns. Now, that the team has gained experience with real-world usage, integrate automation to mitigate common failure patterns and also minimize dependency on the engineering team.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Automation can take time and be expensive to set up. Focus on automating the most impactful tasks first, like those that happen often or are likely to cause outages.

Configure actions based on triggers. Automate responses over time, creating an automated playbook for SREs. One option is to enhance the playbook with scripts that execute steps for mitigation. Also, explore Azure native options. For example, use Azure Monitor's Action Groups to set up triggers that automatically initiate various tasks.

#### &#10003; Extend resiliency to background tasks

Most workloads include components that aren't directly tied to user flows but play a role in maintaining the overall workflow of an application. For example, in an e-commerce application, when a user places an order, a message is added to a queue. This action initiates several background tasks, such as sending an email confirmation, contacting the credit card company to finalize charges, and notifying the warehouse to prepare the dispatch. These tasks operate independently of the functions involved in serving user requests on the website, reducing the load and enhancing reliability. Background tasks are also used for data cleanup, regular maintenance, and backups.

After evaluating and improving your primary user flows, it's a good time to consider the background tasks. Use the techniques and infrastructure that's already in place, with additions specific to background tasks.

- **Apply checkpointing**. Checkpointing is a technique used to save the state of a process or task at specific points. This is particularly useful for long-running tasks or processes that may be disrupted due to unexpected issues like network failures or system crashes. When the process restarts, it can pick up from the last saved checkpoint, minimizing the impact of interruptions. 

- **Keep processes idempotent**. Ensure idempotency in background processes so that if a task fails, another instance can pick it up and continue processing without issues.

- **Ensure consistency**. Prevent the system from entering an inconsistent state if a background task stops midway. Both checkpointing and task-level idempotency are techniques to enable higher greater consistency across background task executions. Additionally, run each task as an atomic transaction. For a task that spans multiple data stores or services, use task-level idempotency or compensating transactions to ensure it's completed. 

- **Integrate background tasks in your monitoring system and testing practices**. Detect failures and prevent unnoticed interruptions that can lead to functional and non-functional consequences. Your monitoring system should include data from these components, set alerts for disruptions, and use triggers to retry or resume the process automatically. Treat these assets as part of the workload and conduct automated testing just like you would for critical components. 

Azure offers several services that are used for background jobs, such as Azure Functions, App Service WebJobs. Review their best practices and limits when implementing flows with focus on reliability. 

# [**Level 5** - Stay resilient](#tab/level5)

![Goal icon](../_images/goal.svg) **Remain reslient as the workload architecture evolves, enabling the system to withstand new and unforeseen risks.**

By Level 5, the focus of improving your solution's reliability shifts away from implementing technical controls. Your infrastructure, applications, and operations should be reliable enough to be resilient to outages and recover from them within the target recovery times.

Use data and future business goals to acknowledge that if the business needs to go further, architectural changes might be necessary. As your workload evolves and new features are added, strive to minimize outages related to those features while further reducing outages for existing features even more.

**Key strategies**
> [!div class="checklist"]
>
> - [Use reliability insights to guide architecture evolution](#-use-reliability-insights-to-guide-architecture-evolution)
> - [Run controlled tests in production](#-run-controlled-tests-in-production)
> - [Conduct disaster recovery (DR) drills](#-conduct-disaster-recovery-dr-drills)
> - [Evaluate your data model, segment if necessary](#-evaluate-your-data-model-segment-if-necessary)

#### &#10003; Use reliability insights to guide architecture evolution

Decisions at this level are made in collaboration with business stakeholders. Here are some key considerations:

- Analyze metrics that indicate how many times reliability thresholds were crossed within a time period and whether that's accetable. For instance, experiencing five major outages in a year might trigger a reassessment of system design and operational practices.

- Evaluate the business criticality of the system. For example, a service supporting mission-critical workflows may need to be redesigned for zero-downtime deployments and instant failover, even if it increases cost or complexity. Conversely, a reduced-use service might need more relaxed service level objectives (SLOs).

- Assess the impact of changes in other pillars on reliability. For example,increased security measures, might need reliability mitigations for extra security hops that can potential be points of failure. 

- Evaluate the operational costs associated with maintaining reliability. If these costs exceed budget constraints, consider making architectural changes to optimize and control spending.

To make it easier for stakeholders, engineers, and product managers to make informed decisions, consider visualizing the the preceding data points and others to give a complete picture of reliability.

#### &#10003; Run controlled tests in production

At this level, controlled experiments in production can be considered only if the workload needs highest guarantees on resilience. These testing practices are known as chaos engineering. The tests are conducted to validate that the system can recover gracefully and continue functioning even under adverse conditions. 

Here are the example test cases:

- **Dependency flow analysis**. A common use case is testing applications designed as microservices. You can turn off random microservice instances to confirm that failures don't cascade or disrupt the user experience. This can be extended to system flows by disabling certain system components to analyze how downstream systems react. The goal is to identify tight coupling or hidden dependencies and test how system redundancy plans perform.

- **Graceful degradation testing**. Evaluate how system functions with reduced functionality during failure without completely breaking. For example, hiding non-critical features if a recommendation engine fails.

- **Third-party failure simulation**. Disable or throttle calls to external APIs to see how your system behaves and whether fallbacks or retries are correctly implemented.

Chaos engineering is a gold standard for testing resilience, however, this practice should be reserved for mature systems and workload teams. There should be safeguards to limit blast radius and prevent user impact.

- Start in non-production environments that simulate real world conditions in lower-risk setups with synthetic transactions. Also, prepare by running game days. This helps uncover process gaps, human error paths, and architectural flaws.

    When non-production testing stops yielding valuable insights, it might be time to move to production if you're confident. Make sure to list all concerns, evaluate resiliency, and address any issues before making the transition.

-  Limit the scope of experiments. For example shutting down only one instance. Clearly define the purpose of the test. Know what you're testing and why. 

- These tests shouldn't violate service level agreements (SLAs) by operating within predefined limits and error budgets. choose the right timeframes for these experiments. Typically, it's best to perform them during a workday when the team is fully staffed and there are ample resources available to respond to any incidents that may arise.

#### &#10003; Conduct disaster recovery (DR) drills

Just as chaos engineering tests the resiliency of technical controls, DR drills test the resiliency of process controls. The goal of this process is to validate the effectiveness of the procedures, coordination, and human actions needed to recover from significant failures or disasters. 

For regulatory workloads, the frequency of DR drills may be dictated by compliance requirements to keep a record of effort. For other workloads, it's recommended to conduct these drills regularly. A six-month interval is a good window to capture changes in workload and update DR procedures accordingly.

Regardless, DR drills should be more than just routine exercises. When done correctly, they are helpful in training new team members, identify gaps in tooling, communication, and  other tasks included in the drill. Additionally, they can highlight fresh perspectives, which might overlooked otherwise.

There are three main ways to conduct DR drills, each with its own level of risk and realism:

- Fully simulated. These are entirely whiteboard exercises. They involve walking through procedures without impacting any systems. They're great for training and initial validation, but they don't offer insights of a real incident.

- Real drills in non-production environments. These drills allow you to validate automation, scripts, and processes without any business risk.

- Real drills in production. These offer the highest level of confidence and realism. Make sure they are conducted only after trying out the preceding two ways. They require thorough planning and rollback strategies to minimize risk. Don't move forward if there's a chance of causing outages.

No matter the type of DR drill, be clear about the workload scenarios for recovery. They should be done as though they are actual incidents. Have clear checklists that the team understands. Also document and classify findings from the drill to drive improvements. Your DR preparation might contain processes, such as:

- Familiarity with incident management systems. Make sure the team is trained on escalation paths.

- List communication tools for collaboration and status updates. Include alternate communication tools in case the primary ones are impacted during the incident.

- Skilling material about the scenarios to test that are relevant to the workload.

- Issue tracking to collect glitches during execution.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Although these drills typically aren't very disruptive, they do require time. Concentrate on the essential aspects and steer clear of unnecessary tasks to get the most out of them. Additionally, make sure to allocate time for this practice in your backlog.

When creating DR plans or running DR drills, especially the first few, consider bringing in specialized expertise. For example, get their input about multi-region design, failover, and failback, and services or tools that might be used in DR drills. If you have a Cloud Center of Excellence (CCoE) team in your organization, include them as part of planning.

#### &#10003; Evaluate your data model, segment if necessary

Data is dynamic and constantly evolving. Unlike other components in your architecture, data almost always continues to grow as users interact with your system. It's important to monitor how data patterns change over time and assess their impact on other parts of your architecture. At this level, explore techniques that can simplify data management and enhance performance with the outcome of better reliability. Partitioning is one key strategy for those outcomes. 

Explore techniques like hot-cold partitioning divides data based on access patterns and stores them separately. Use criteria like frequency of access or relevancy to decide what to partition. 

That technique can be combined with sharding that splits a large database into smaller pieces called shards. Each shard holds part of the data, and together, they form the complete dataset. This approach allows for independent management of data.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Balancing shards requires operational processes to evaluate and confirm their distribution. While this helps avoid hot partitions where one partition is overused, it also demands ongoing effort and resources to maintain balance.

When choosing a partitioning technique, consider its reliability benefits:

- **Enhanced performance**. By directing requests to different partitions, load on individual stores can be reduced. When used effectively, sharding can enable a system to handle millions of write requests per day, enhancing performance and reducing latency.

    Partinioning can make it easier to scale horizontally. For instance, sharding can subdivide users/customers together into approximately even-sized buckets.

- **Improved data management**. Hot-cold partitioning allows for separate rigor of data management applied to different tier. For example, move archival data to a different store to avoid slowing down operations and backups. Similarly, not all log data needs to be in a relational database; it can be stored in another data store, while active workload data stay relational.

- **Tailored reliability policies**. Different reliability policies can be applied to making sure each partition has appropriate level of of resiliency and prevents any single store from becoming a bottleneck. Hot partitions can be fully redundant, including zone and geo redundancy, while cold partitions can rely on backups. Consequently there are cost benefits. An added reliability benefit is that you can reduce the blast radius of some failure types - if a failure affects one shard it might not spread to others.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Maintaining or modifying partitions can be complex due to strong relationships between different partitions of data. It may impact the ability to verify data consistency and integrity compared to a single data store. As the number of partitions grows, the need for strong processes increases to maintain data integrity. Without them, reliability could suffer.

## Next steps
