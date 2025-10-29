---
title: Reliability Maturity Model
description: Understand the maturity model levels of the reliability pillar.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 07/14/2025 
ms.topic: concept-article
ms.update-cycle: 1095-days  
---

# Reliability Maturity Model

The reliability journey is a step-by-step process where each stage builds on the previous one to ensure systems stay available and meet user expectations. This maturity model is intended to help you assess your current state and offer a structured path for improvement.

The foundation begins by bootstrapping basic reliability capabilities offered by Azure by using built-in Azure reliability features like zone redundancy for immediate improvements without extensive optimization overhead.

Counterintuitively, the way to achieve high reliability is to accept failures are inevitable. Rather than trying to prevent every issue, it's more effective to plan how your system will respond when problems occur. Your business requirements help determine which risks are worth addressing proactively. Teams invest in advanced monitoring capabilities with structured observability, extend failure mitigation to include application-level concerns, and begin testing resiliency measures. 

Next, teams integrate business insights with technical skills. Teams implement health modeling, conduct failure mode analysis, and prepare comprehensive disaster recovery plans. This stage ensures accountability through measurable objectives and systematic preparation for various failure scenarios. 

After the system is live, the emphasis moves to managing the challenges of production environments, including change management and dealing with data growth and operational complexity, and how these affect your system's reliability.

The final level runs indefinitely, and staying resilient is its goal. This level represents the evolution beyond technical controls to architectural adaptability. This level focuses on enabling systems to withstand new and unforeseen risks as workloads evolve and grow.

The model is structured into five distinct maturity levels, each with a primary goal and a set of core strategies. Use the tabbed views below to explore each level. Be sure to also review the highlighted tradeoffs and associated risks as you progress.

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

Use built-in SDKs and configurations to handle these faults to keep the system active. Built-in configurations are often the default setting, so you might need to test to validate the implementation. Also, implement patterns that are designed to handle transient failures in your architecture. For more information, see [Architecture design patterns that support reliability](./design-patterns.md).

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

# [**Level 3: Recovery readiness**](#tab/level3)

![Goal icon](../_images/goal.svg) **Set reliability objectives and targets to keep the team accountable on recovery procedures.**

At early levels, your teams focus on easy wins and basic capabilities. They start with small improvements, solving simple problems to build a strong foundation while relying mostly on Azure reliability capabilities. As your teams grow, they handle more technical challenges related to their own assets and processes.

At Level 3, your teams should integrate business insights and technical skills for recovery planning. They set objectives and plan recovery processes by using advanced monitoring. This approach helps site reliability engineers (SREs) meet reliability targets quickly.

**Key strategies**
> [!div class="checklist"]
>
> - [Formalize reliability capabilities as objectives](#-formalize-reliability-capabilities-as-objectives)
> - [Monitor proactively using your health model](#-monitor-proactively-by-using-your-health-model)
> - [Set actionable alerts](#-set-actionable-alerts)
> - [Conduct failure mode analysis](#-conduct-failure-mode-analysis)
> - [Prepare a DR plan](#-prepare-a-dr-plan)

#### &#10003; Formalize reliability capabilities as objectives 

Reliability objectives help set accountability for workload teams. It's important to have a collaborative conversation with business stakeholders to discuss recovery times and costs, and to make compromises that align with business goals. Gather the stakeholders and conduct this discussion as a workshop. Consider the following points for the workshop agenda:

- **Explain the metrics behind objectives.** Start by explaining the key metrics used to define objectives like service-level objectives, recovery time objectives (RTOs), and recovery point objectives (RPOs). Show how those metrics align with business goals. Focus on the critical user flows. For example, in an e-commerce application, the RTO for updating email preferences is less important than users checking out shopping carts.

- **Communicate the trade-offs.** Stakeholders often expect more than what can be achieved. Explain how expanding the scope affects the budget, operational requirements, and performance.

- **Propose objective targets.** Based on architectural experience and workload design, recommend targets such as 99.9% uptime, with RPO and RTO set at four hours. Facilitate a discussion for stakeholders to provide feedback and make adjustments. Ensure that both business and technical stakeholders guard against unrealistic expectations. Approach discussions with a collaborative mindset.

- **Reach a consensus or decision.** Aim for a consensus, but if that's not possible, have a decision-maker finalize the targets to ensure progress.

#### &#10003; Monitor proactively by using your health model

At Level 1, monitoring data is collected from workload components, including platform services and applications. Basic analysis and alerts are set to establish baseline performance and identify anomalies. In Level 2, the focus shifts to obtaining observability data from workload components, such as application code.

Level 3 enhances monitoring by adding business context to critical flows and defining *healthy*, *unhealthy*, and *degraded* states through health modeling. Stakeholder agreement is needed to determine acceptable user experience compromises and should be used as input for defining health states.

Health modeling requires operational maturity and expertise in monitoring tools. Your team reviews raw data, performance levels, and logs to create custom metrics and thresholds that define the health state of the flow. They must understand how these values relate to the overall health of the system. Communicate clear definitions and thresholds to stakeholders.

Visualize the health model in dashboards to help SREs quickly pinpoint problems by focusing on unhealthy or degraded flows.

The health model defines the application's status and critical flows. Green indicates that all critical flows are operating as expected. Red indicates a failure. And yellow shows trends towards problems. Iterating through health model versions ensures reliability and accuracy but requires significant effort for large applications.

A change in the health state should be configured as alerts. However, to keep alerts intentional, the criticality of the component must be taken into consideration.

> For more information, see [Well-Architected Framework: Health modeling](../design-guides/health-modeling.md).

#### &#10003; Set actionable alerts

To improve response efficiency, define alerts clearly and provide enough information for quick action. Detailed alert names and descriptions can help save time and effort during troubleshooting. Configure the severity, name, and description carefully, with special attention to severity levels. Not every event is an emergency. Thoughtfully assess severity levels and establish criteria for each level, such as whether a CPU spike from 80% to 90% qualifies as an emergency. Set appropriate thresholds to ensure that alerts are effectively defined.


Effective alert management ensures that alerts notify the right people at the right time. Frequent and disruptive alerts indicate a need for adjustment and can become counterproductive when they're ignored. Reduce unnecessary notifications by setting appropriate thresholds to filter out false alarms. Identify opportunities where automation can trigger operational procedures.

Create a single landing page that has the necessary information to troubleshoot alerts efficiently. This approach saves time compared to logging in to the Azure portal and searching for metrics. If the Azure Monitor built-in features don't fully meet your needs, consider developing a custom dashboard.

#### &#10003; Conduct failure mode analysis

In the earlier levels, you created a simple failure mitigation playbook for individual components. At this level, evolve that playbook into a formal failure mode analysis (FMA) exercise. The purpose of this exercise is to proactively identify potential failure modes.

FMA requires you to identify potential points of failure within your workload and plan mitigation actions, such as self-healing or disaster recovery (DR). To begin, monitor for increased error rates and detect impacts on critical flows. Use past experiences and test data to identify potential failures and assess their blast radius. Prioritize major problems such as a region-wide outage.

It's important to classify actions as *preventative* or *reactive*. Preventative actions identify risks before they cause an outage, which reduces their likelihood or severity. Reactive actions address problems to mitigate a degraded health state or an outage.

In the e-commerce example application, the workload team wants to do FMA to prepare themselves for a major event. One of the key user flows is adding items to the cart. The components that are part of the flow are the front end, CartAPI, ProductCatalogAPI, UserProfileAPI, PricingAPI, Azure Cosmos DB, and Azure Event Hubs.

| Problem | Risk | Potential source | Severity | Likelihood | Actions |
| :-------| :------| :------------------| :----------| :------------| :---------|
| The number of orders received drops below 100 per hour, with no corresponding drop in user session activity | Customers are unable to place orders, even though the application is available. | CartAPI, PaymentsAPI | High | Not likely | **Reactive actions:** <br>- Review the health model or monitoring data to identify the problem. <br>- Test the application to validate its functionality. <br>- If a component outage occurs, perform a failover to another set of infrastructure. <br><br> **Preventative actions:** <br>- Place synthetic orders to verify that the flow is working. <br>- Improve observability to ensure that the end-to-end flow is monitored. |
| Unexpected increase in load causes timeouts when storing orders to Azure Cosmos DB | Customers can't place orders or receive unsatisfactory performance if they can place orders. | Azure Cosmos DB | High | Not likely | **Reactive actions:** <br>- Verify load based on application telemetry. <br>- Scale up Azure Cosmos DB request units temporarily. <br><br> **Preventative actions:** <br>- Configure autoscale. <br>- Revisit expected load and recalculate scale rules. <br>- Move some activities to a background process to reduce the database load from this flow. |
| The recommendations service goes completely offline | The shopping cart page fails to load because of an exception that invokes the recommendations service. | Application | Medium | Not likely | **Reactive actions:** <br>- Implement a graceful degradation strategy to either disable the recommendation functionality or display hard-coded recommendation data on the shopping cart page. Apply this approach when an exception occurs while you assess the service. |
| Intermittent timeouts occur when accessing the pricing API from the shopping cart page under heavy load | Intermittent failures occur in the shopping cart page because of failures accessing the cart service. | Application | Medium | Likely (under heavy load) | **Reactive actions:** <br>- Implement cache pricing value in the shopping cart data store, together with a cache expiry timestamp. <br>- Access the pricing API only when the pricing data cache is expired. |

FMA is complex and can be time-consuming, so build your analysis progressively over time. This process is iterative and continues to evolve at later stages.

> For more information, see [RE:03 Recommendations for performing FMA](./failure-mode-analysis.md#example).

#### &#10003; Prepare a DR plan

In Level 2, you created a recovery plan focused on technical controls to restore system functionality. However, a disaster requires a broader approach because of catastrophic loss or failure. DR plans are process-based. They cover communication, detailed recovery steps, and potentially include technical artifacts like scripts.

First, identify the types of disasters to plan for, such as region outages, Azure-wide failures, infrastructure disruptions, database corruption, and ransomware attacks. Then, develop recovery strategies for each scenario and ensure that mechanisms are in place to restore operations. Business requirements, RTOs, and RPOs should guide DR plans. Low RTOs and RPOs require explicit automated processes, while higher RTOs and RPOs allow for simpler recovery methods and manual analysis.

DR mainly includes the following actions:

- **Notify the responsible parties.** It's important to have clarity on who to involve and when. Ensure that your team uses the correct processes, has the right permissions, and understands their roles in recovery. Some responsibilities, like the CEO reporting to the market or handling regulatory requirements, should be identified early.

  Ideally, you should have separate recovery and communication roles, and assign different people to each role. Initially, the IT operations person who discovers the problem might handle both roles. But as the situation escalates, senior personnel might handle technical recovery while a business person manages communications.

- **Make business decisions.** During a disaster, stress levels can be high, which makes clear decision-making essential. A well-structured DR plan requires continuous discussions between the technical team and business stakeholders to define preliminary decision options. For example, consider whether workload resources should run in one Azure region with backups in another region, or whether IaC assets should be prepared in advance to create new resources or restore from a backup during failover.

  Actions taken according to DR plans can be destructive or have significant side effects. It's essential to understand the options, weigh their pros and cons, and determine the right time to apply them. For instance, assess whether recovery to a different region is necessary if the primary region is expected to be operational within an acceptable time frame.

- **Restore system operations.** During a disaster, the focus should be on restoring operations and not on identifying the cause. For technical recovery, especially in region failover, decide in advance on approaches like active-active, active-passive, warm standby, or cold standby.

  Prepare specific recovery steps based on the chosen approach. Start with a concrete list of steps to restore operations. As the process matures, aim to define the DR plan as a script with minimal manual interaction. Use version control and store the script securely for easy access. This approach requires more up-front effort but minimizes stress during an actual incident.

  > For more information, see [Deploy in active-passive for DR](./redundancy.md#build-redundancy-into-your-workload-with-multiple-component-instances).

- **Conduct post-incident analysis.** Identify the cause of the incident and find ways to prevent it in the future. Make changes to improve recovery processes. This exercise might also uncover new strategies. For example, if the system switched to the secondary environment, determine if the primary environment is still needed and what the failback process should be.

A DR plan is a living document that adapts to changes in your workload. Update your DR plan as new components and risks emerge. Refine the plan based on insights gained from drills or real disasters by gathering realistic information from DR operators.

# [**Level 4: Maintain stability**](#tab/level4)

![Goal icon](../_images/goal.svg) **Control risks that stem from technical and operational changes, and prioritize incident management.**

In the previous levels, your workload team focuses on building features and making the system operational. At Level 4, the focus shifts to keeping the system reliable in production. Incident management becomes as important as making sure that any changes introduced are thoroughly tested and safely deployed to avoid making the system unstable.

This process requires improvements in operational controls, such as investing in dedicated teams to manage reliability incidents. It also requires technical controls to enhance system reliability beyond the critical components reinforced in previous levels. As the system continues to run in production, data growth might require redesigns, such as partitioning, to ensure reliable access and maintenance.

**Key strategies**
> [!div class="checklist"]
>
> - [Reliable change management](#-reliable-change-management)
> - [Invest in a dedicated team to handle incidents](#-invest-in-a-dedicated-team-to-handle-incidents)
> - [Automate self-healing processes](#-automate-self-healing-processes)
> - [Extend resiliency to background tasks](#-extend-resiliency-to-background-tasks)

#### &#10003; Reliable change management

The biggest reliability risks occur during changes, such as software updates, configuration adjustments, or process modifications. These events are critical from a reliability standpoint. The goal is to minimize the likelihood of problems, outages, downtime, or data loss. Each type of change requires specific activities to manage its unique risks effectively.

At Level 4, Reliability intersects with safe deployment practices described in Operational Excellence, in maintaining a stable environment when managing changes. Reliable change management is a requirement for achieving workload stability. Consider the following key aspects:

- **React to platform updates.** Azure services have different mechanisms to update services.

  - Familiarize yourself with maintenance processes and update the policies of each service that you use. Understand whether the service supports automatic or manual upgrades and the time frame for manual updates.

  - For services that have planned updates, manage these updates effectively by scheduling them during low-impact times. Avoid automatic updates and defer them until after you assess the risk. Some services enable you to control the timing, while other services provide a grace period. For example, with Azure Kubernetes Service (AKS), you have 90 days to opt in before the update becomes automatic. Test updates in a nonproduction cluster that mirrors your production setup to prevent regressions.

    Apply updates gradually. Even if testing shows that the update is safe, applying it to all instances simultaneously can be risky. Instead, update a few instances at a time and wait between each set.

  - Regularly check for notifications about updates, which might be available in activity logs or other service-specific channels.

  - Monitor for sudden or gradual changes after an update is applied. Ideally, your health model should notify you of these changes.

- **Thorough testing with automation.** Integrate more testing into your build and deployment pipelines when you roll out changes. Look for opportunities to convert manual processes to automated parts of your pipelines.

  Do comprehensive testing by using a combination of different types of tests at various stages to confirm that changes work as expected and don't affect other parts of the application. For example, positive testing can verify that the system operates as expected. It should validate that there are no errors and that traffic flows correctly.

  When you plan updates, identify testing gates and the types of tests to apply. Most testing should occur in pre-deployment stages, but smoke tests should also be performed in each environment when you update it.

- **Follow safe deployment practices.** Use deployment topologies that include validation windows and safe deployment practices. Implement safe deployment patterns, such as canary and blue-green deployments, to enhance flexibility and reliability.

  For example, in canary deployments, a small subset of users receives the new version first. This process enables monitoring and validation before deployment to the entire user base. Techniques like feature flags and dark launches facilitate testing in production before releasing changes to all users.

- **Update your DR plan.** Regularly update your DR plan to keep it relevant and effective. Avoid outdated instructions. This approach ensures that the plan reflects the current state of your system that's deployed to production and relied on by users. Incorporate lessons learned from drills and actual incidents.

For more information, see [Operational Excellence Level 4](../operational-excellence/maturity-model.md)

#### &#10003; Invest in a dedicated team to handle incidents

Initially, the development team might be involved during incidents. At Level 4, invest in site reliability engineering (SRE) for incident management. SREs specialize in production problems and are experts in efficiency, change management, monitoring, emergency response, and capacity management. A proficient SRE team can significantly reduce dependency on the engineering team.

Provide SREs with the tools, information, and knowledge necessary to handle incidents independently. This preparation reduces dependency on the engineering team. SREs should be trained in the playbooks and the workload health model developed in previous levels to quickly recognize common patterns and initiate the mitigation process.

The engineering team should have time to reflect on recurring problems and develop long-term strategies, instead of addressing them individually each time.

#### &#10003; Automate self-healing processes

In the previous levels, self-healing strategies are designed by using redundancy and design patterns. Now that your team has experience with real-world usage, you can integrate automation to mitigate common failure patterns and reduce dependency on the engineering team.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Automation can take time and be costly to set up. Focus on automating the most impactful tasks first, like tasks that occur often or are likely to cause outages.

Configure actions based on triggers and automate responses over time to build an automated playbook for SREs. One approach is to enhance the playbook with scripts that implement mitigation steps. Explore Azure-native options, such as using Azure Monitor action groups, to set up triggers that automatically initiate various tasks.

#### &#10003; Extend resiliency to background tasks

Most workloads include components that don't directly support user flows but play a critical role in the overall workflow of an application. For example, in an e-commerce system, when a user places an order, the system adds a message to a queue. This action triggers several background tasks, such as email confirmation, credit card charge finalization, and warehouse notification for dispatch preparation. These tasks operate separately from the functions that serve user requests on the website, which reduces load and improves reliability. Systems also rely on background tasks for data cleanup, regular maintenance, and backups.

After you evaluate and improve your primary user flows, consider the background tasks. Use the techniques and infrastructure that's already in place, and add improvements for background tasks.

- **Apply checkpointing.** Checkpointing is a technique for saving the state of a process or task at specific points. Checkpointing is especially useful for long-running tasks or processes that might be disrupted because of unexpected problems like network failures or system crashes. When the process restarts, it can pick up from the last saved checkpoint, which minimizes the impact of interruptions.

- **Keep processes idempotent.** Ensure idempotency in background processes so that if a task fails, another instance can pick it up and continue processing without problems.

- **Ensure consistency.** Prevent the system from entering an inconsistent state if a background task stops during processing. Both checkpointing and task-level idempotency are techniques to enable greater consistency across background task operations. Run each task as an atomic transaction. For a task that spans multiple data stores or services, use task-level idempotency or compensating transactions to ensure that it completes.

- **Integrate background tasks into your monitoring system and testing practices.** Detect failures and prevent unnoticed interruptions that can result in functional and nonfunctional consequences. Your monitoring system should capture data from these components, set alerts for disruptions, and use triggers to retry or resume the process automatically. Treat these assets as part of the workload, and conduct automated testing the same way that you do for critical components.

Azure provides several services and features for background jobs, such as Azure Functions and Azure App Service WebJobs. Review their best practices and limits when you implement flows that focus on reliability.

# [**Level 5: Stay resilient**](#tab/level5)

![Goal icon](../_images/goal.svg) **Remain resilient as the workload architecture evolves, which enables the system to withstand new and unforeseen risks.**

At Level 5, the focus of improving your solution's reliability shifts away from implementing technical controls. Your infrastructure, applications, and operations should be reliable enough to be resilient to outages and recover from them within the target recovery times.

Use data and future business goals to acknowledge that if your business needs to go further, architectural changes might be necessary. As your workload evolves and new features are added, strive to minimize outages related to those features while further reducing outages for existing features.

**Key strategies**
> [!div class="checklist"]
>
> - [Use reliability insights to guide architecture evolution](#-use-reliability-insights-to-guide-architecture-evolution)
> - [Run controlled tests in production](#-run-controlled-tests-in-production)
> - [Conduct disaster recovery drills](#-conduct-disaster-recovery-drills)
> - [Evaluate your data model and segment if necessary](#-evaluate-your-data-model-and-segment-if-necessary)

#### &#10003; Use reliability insights to guide architecture evolution

At this level, make decisions in collaboration with business stakeholders. Consider the following factors:

- Analyze metrics that indicate how many times your system crosses reliability thresholds within a time period and whether that's acceptable. For instance, experiencing five major outages in one year might trigger a reassessment of system design and operational practices.

- Evaluate the business criticality of the system. For example, a service that supports mission-critical workflows might require redesigning for zero-downtime deployments and instant failover, even if it increases cost or complexity. Conversely, a reduced-use service might benefit from more relaxed service-level objectives.

- Assess how changes in other pillars affect reliability. For example, increased security measures might require reliability mitigations for extra security hops, which could introduce potential points of failure.

- Assess the operational costs of maintaining reliability. If these costs exceed budget constraints, consider architectural changes to optimize and control spending.

To help stakeholders, engineers, and product managers make informed decisions, consider visualizing the preceding data points along with extra insights. This approach provides a complete picture of reliability.

#### &#10003; Run controlled tests in production

At this level, consider controlled experiments in production only if the workload requires the highest resilience guarantees. These testing practices are known as *chaos engineering*. The tests validate that the system can recover gracefully and continue functioning under adverse conditions.

Consider the following example use cases:

- **Dependency flow analysis:** A common use case is testing applications designed as microservices. You can turn off random microservice instances to ensure that failures don't cascade or disrupt the user experience. You can extend this approach to system flows by disabling specific components to analyze how downstream systems react. The goal is to identify tight coupling or hidden dependencies and test how system redundancy plans perform.

- **Graceful degradation testing:** Evaluate how the system runs with reduced functionality during failure. For example, you can hide non-critical features if a recommendation engine fails.

- **Third-party failure simulation:** Disable or throttle calls to external APIs to see how your system operates and whether fallbacks or retries are correctly implemented.

Chaos engineering is a gold standard for testing resilience. However, reserve this practice for mature systems and workload teams. Ensure that safeguards are in place to limit the blast radius and prevent user impact.

- Prepare by running game days. Begin in nonproduction environments that simulate real-world conditions by using lower-risk setups with synthetic transactions. This approach helps identify process gaps, human error paths, and architectural flaws.

  When nonproduction testing stops yielding valuable insights, it might be time to move to production if you're confident. Make sure to list all concerns, evaluate resiliency, and address any problems before you transition.

- Limit the scope of experiments. For example, you might shut down only one instance. Clearly define the purpose of the test. Understand what you're testing and why.

These tests must adhere to service-level agreements by operating within predefined limits and error budgets. Select appropriate timeframes for these experiments. Typically, performing them during a workday ensures that the team is fully staffed and has ample resources to respond to any incidents that might occur.

#### &#10003; Conduct disaster recovery drills

Chaos engineering tests the resilience of technical controls. Disaster recovery (DR) drills assess the resilience of process controls. The goal of DR drills is to validate the effectiveness of procedures, coordination, and human actions when your system recovers from major failures or disasters.

For regulatory workloads, compliance requirements might dictate the frequency of DR drills to ensure a record of effort. For other workloads, conducting these drills regularly is recommended. A six-month interval provides a good opportunity to capture workload changes and update DR procedures accordingly.

DR drills should be more than routine exercises. When conducted properly, DR drills help train new team members and identify gaps in tooling, communication, and other drill-related tasks. They can also highlight fresh perspectives that might otherwise be overlooked.

Consider the following key methods for conducting DR drills, each varying in risk and practicality:

- **Fully simulated:** These exercises are fully whiteboard-based and include procedural walkthroughs without affecting any systems. They're suitable for training and initial validation. However, they don't provide insights into real incidents.

- **Real drills in nonproduction environments:** These drills allow you to validate automation, scripts, and processes without any business risk.

- **Real drills in production:** These drills provide the highest level of confidence and realism. Conduct these drills only after you test the previous two methods. Thorough planning and rollback strategies are essential to minimize risk. Don't proceed if there's any chance of causing outages.

Regardless of the type of DR drill, clearly define the workload recovery scenarios. Conduct drills as if they're real incidents. This approach ensures that the team follows well-understood checklists. Document and classify findings to drive continuous improvement. Your DR preparation might include the following processes:

- Understand incident management systems, and ensure that the team is trained on escalation paths.

- List communication tools for collaboration and status updates, including alternatives in case primary systems are affected.

- Provide skilling materials on relevant test scenarios for the workload.

- Track discrepancies to capture problems encountered during implementation.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** These drills aren't typically disruptive, but they do take time. To maximize their effectiveness, focus on the key aspects and avoid unnecessary tasks. Make sure to allocate time for this practice in your backlog.

When you create DR plans or conduct DR drills, specifically for the first few drills, consider including specialized expertise. Their input on multiregion design, failover and failback strategies, and services or tools can be invaluable. If your organization has a Cloud Center of Excellence team, be sure to include them in the planning process.

#### &#10003; Evaluate your data model and segment if necessary

Data is dynamic and constantly evolving. Unlike other components in your architecture, data typically grows as users interact with your system. Monitoring data patterns over time and assessing their impact on other parts of your architecture is essential. At this level, consider techniques that simplify data management and improve performance to enhance overall reliability. Partitioning is a key strategy for achieving these outcomes.

Explore techniques like hot-cold partitioning, which divides data based on access patterns and stores them separately. Use criteria like frequency of access or relevancy to decide what to partition.

Hot-cold partitioning can be combined with sharding, which is a process that divides a large database into smaller units called *shards*. Each shard holds a portion of the data, and together they form the complete dataset. This approach enables independent data management.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Balancing shards requires operational processes to evaluate and confirm their distribution. This approach helps avoid hot partitions where one partition is overused. However, it also requires ongoing effort and resources to maintain balance.

When you choose a partitioning technique, consider the following reliability benefits:

- **Enhanced performance:** By distributing requests across multiple partitions, you can reduce the load on individual stores. When implemented effectively, sharding enables a system to process millions of write requests per day. This strategy improves performance and minimizes latency.

  Partitioning can simplify horizontal scaling. For instance, sharding can divide users or customers into approximately equal-sized buckets.

- **Improved data management:** Hot-cold partitioning allows different levels of data management to be applied to each storage tier. For example, moving archival data to a separate store helps prevent slowdowns in operations and backups. Similarly, not all log data needs to be stored in a relational database. It can be stored in another data store while active workload data remains relational.

- **Tailored reliability policies:** Different reliability policies can be applied to help ensure that each partition has the right level of resiliency and prevents any single store from becoming a bottleneck. Hot partitions can be fully redundant, including zone-redundancy and geo-redundancy, while cold partitions rely on backups. An added reliability benefit is that you can reduce the blast radius of some types of failures. For example, if a failure affects one shard, it might not affect the other shards.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** It can be difficult to maintain or modify partitions because of the strong interdependencies between different data partitions. These changes might affect the ability to verify data consistency and integrity, especially when compared to a single data store. As the number of partitions increases, the need for robust processes to maintain data integrity becomes more crucial. Without these measures, reliability might be compromised.

---

## Next steps

- Review the [Reliability design review checklist](checklist.md) to get details on the recommendations.
