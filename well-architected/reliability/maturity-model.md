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

# [**Level 1: Resilient foundation**](#tab/level1)

![Goal icon](../_images/goal.svg) **Establish a solid groundwork for resiliency in workload infrastructure and operations, rather than spending time on optimization tasks.**

Level 1 of the maturity model is designed to help workload teams build a strong foundation for system reliability. The focus is on bootstrapping, which is the process of setting up the basics for future reliability decisions. This stage mostly involves functional implementation with minor extensions to current practices. 

This stage includes researching, gaining insights, and creating an inventory of your systems. It also uses built-in reliability features on Azure, like enabling zone redundancy for immediate improvements.

By establishing these basics, you can prepare your team to advance through the levels of the reliability maturity model to progressively enhance your system's resilience and performance.

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

Feature development is the priority, and testing can introduce friction in the development cycle. It's easier to start testing before feature development is complete. Designing nonfunctional aspects of the application at the beginning allows you to extend them as you add functional capabilities, rather than building up a backlog of problems to address later. Although this approach requires more effort initially, it's manageable and prevents larger problems later.

# [**Level 2: Self-preservation**](#tab/level2)

![Goal icon](../_images/goal.svg) **Ensure that the system remains functional and stable by incorporating self-preservation capabilities and having a basic recovery plan to manage failures.**

Failures in the cloud are inevitable. Your resiliency strategies should strive to keep the system functional under all conditions. Level 1 introduces methods for addressing transient failures. Level 2 focuses on incorporating self-preservation strategies to prevent, detect, and recover from longer-lasting failures. If left unresolved, these problems can turn into full outages.

The critical flows identified in Level 1 take priority. They require increased resiliency and recovery efforts for all components, including applications, services, and databases. Expect to adjust your initial provisioning sizes, instance counts, and autoscale policies to reduce reliability risks.

In this level, be intentional about your monitoring and testing practices. Use advanced monitoring techniques that align with technical needs and are scoped to development teams. Expand the simple playbook to cover architectural components that you develop and own, such as application code.

#### &#10003; Evaluate the current state of resiliency to protect against failures

- *Is the level of redundancy good enough to withstand failures?* Define a redundancy strategy that specifies the number of redundant resources to maintain. Determine where to place these resources, whether locally, across zones, or in geographically distributed locations. Evaluate the cloud platform's settings and select a level that meets business needs and acceptable trade-offs.

- *Are the workload components isolated enough to contain their failures?* Patterns like [Bulkhead](/azure/architecture/patterns/bulkhead) help build resilience and fault isolation. The Bulkhead pattern partitions a system into isolated components, known as *bulkheads*, to prevent failures from cascading to other parts of the system.

- *Do components on the critical path communicate asynchronously?* If not, use communication methods, such as queues. This approach keeps the system operational even if a downstream component fails. It also prevents the system from entering an indeterminate state. Explore Azure options, including Azure Service Bus for queues and Azure Event Hubs for event streams.

    > :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Asynchronous communication can help prevent cascading failures by decoupling processes. However, it introduces added latency in to the communication path, which can pose a problem for critical components. Evaluate the performance impact before you make any design pattern changes.

- *Are the operations designed for consistency?* Assets such as application secrets and certificates can expire and require regular refreshing. Inconsistencies in routine updates can result in reliability problems.

    Ideally, identify and eliminate ongoing human-operated tasks because they're error-prone and can cause inconsistencies that pose reliability risks. Offload as many operational tasks as possible to the cloud provider. For example, use managed identities that Microsoft Entra ID provides and TLS certificates that Azure Front Door manages.

    Monitoring is required for proactive measures, such as tracking certificate expiration and receiving notifications. The application should log important events, like a TLS certificate nearing expiration. Using multiple methods to check for potential failures helps ensure that necessary actions are taken.

#### &#10003; Add technical capabilities in your monitoring system

At Level 1, you have monitoring data from the workload components, with a focus on infrastructure. Basic analysis is complete and basic alerts are set. This setup is essential for understanding the baseline performance of workload components and identifying anomalous behavior.

Level 2 takes monitoring a step further by adding advanced observability capabilities to your workload resources and adopting a more structured approach to analyzing monitoring data. Take advantage of analytics tools that your cloud service provides. For example, Azure Monitor's insight tools, such as VM Insights and Network Insights, provide visualizations of health and performance across dependencies.

Plan observability capabilities at the following layers:

##### Application

- **Respond to health status probing.** Enable the application to respond to health check requests from probes. The application should have dedicated endpoints for health checks that provide status information, such as *healthy* or *unhealthy*, at a minimum. This approach allows monitoring systems to assess if the application is functioning properly and can handle requests, or if there are problems that need to be addressed.

    Azure load balancing services, such as Azure Front Door, Azure Traffic Manager, Azure Application Gateway, and Azure Load Balancer, support health probes. Health probes send health check requests to applications.

- **Advance to semantic logging.** Include structured information about events and actions in the application. With structured logging, log data is recorded in a consistent format by using a well-defined schema. This schema makes it easier to search and analyze and build automation in later stages. Include specific fields like timestamps and error codes to help quickly identify and troubleshoot problems.

- **Implement distributed tracing.** When a request flows through different components of the system, it's important to capture trace data across boundaries. This data is useful for getting insights into application behavior and identifying performance bottlenecks, errors, and latency problems. Azure Monitor supports OpenTelemetry-based data collection with Application Insights.

##### Data

Track query duration, failed queries, and other relevant metrics. Long-running queries can indicate resource constraints and possibly a need to adjust schema design.

At this stage, your database has been operating for some time. Pay attention to the data growth rate, especially in tables that grow unexpectedly fast. This information is crucial for planning future storage needs and addressing performance problems early.

As the database grows, schema problems might become more apparent, which affects performance. To optimize query efficiency, consider adding indexes or modifying the schema because these changes can affect reliability.

Monitor the status of database replication by using the tools and dashboard that the database management system provides. For example, if you use Azure Cosmos DB, use Azure Cosmos DB insights. For Azure SQL Database or Azure SQL Managed Instance, consider using the database watcher to get diagnostic details about your databases.

##### Operations

The focus of Level 1 is on the preceding layers. At Level 2, start building operations around the monitoring system.

- **Keep logs long enough to get insights.** From a reliability perspective, configure the retention duration so that you can collect enough data to detect failure patterns, troubleshoot problems, and perform root cause analysis.

- **Monitor backup and recovery processes.** Ensure that the backups are successfully stored in locations as planned and that workload data is recovered within a reasonable timeframe. Monitoring those processes are important for setting baselines for your recovery point objective (RPO) metrics at later levels.

#### &#10003; Extend your failure mitigation playbook

The focus at Level 1 is on the expected platform failures. In this level, address failure points on components and operations within your own workload. As your code runs on the platform, interaction points between the platform and application increase. Expect failures from bugs in your code, unsuccessful deployments, and human errors. Mitigate these problems by using self-preservation or recovery tactics.

Extend your failure mitigation playbook to include bugs and deployment problems. The following table builds on the template from Level 1:

| Problem | Risk | Source | Severity | Likelihood | Mitigation |
| ---------| ------| --------| ----------| ------------| ------------|
| Code doesn't handle at-least-once message delivery. | Duplicate processing of messages from the bus results in data corruption. | Application | High | Likely | - Redesign to use bus partitioning and build idempotency into the process. <br><br> - Move away from a competing consumers model, which makes performance a trade-off. |
| Daily storage backup script fails to run. | RPO is violated because the data is older than 24 hours. | Automated process | High | Not likely | Set up an alert on the backup process. |
| Regular user and usage spikes after a new release. | Performance hit on the application and user requests time out. | Application | High | Not likely | Configure schedule-based scale-out operations. |
| Concurrency bug in code | Unpredictable behavior and possible data corruption | Application | High | Likely | Use safe forms of concurrency and avoid manual handling of concurrency controls. |
| Unexpected failure during deployment leaves the environment in an inconsistent state. | Application outage | Deployment pipelines | Medium | Likely | Use blue-green deployments, canary deployments, or other approaches to progressively roll out changes. |

This exercise can become overwhelming if you try to account for every possible failure. To make it easier, focus on the components that are part of the critical user flows. This living document continues to grow as the workload matures.

#### Develop a basic recovery plan

The failure mitigation playbook is the basis for creating a basic recovery plan. Mitigation strategies can include design pattern implementation, platform configuration adjustments, live site incident management, automated tests, and training personnel to detect problems during code reviews.

Start with a graceful degradation strategy, which includes temporary fixes when parts of the system don't work properly. The goal is to continue to serve users despite failures by disabling nonworking parts and adjusting the user experience. For example, if a database is down, the application can disable the affected feature and inform clients that the service is temporarily unavailable by using HTTP status codes.

For graceful degradation to work, the system should isolate components so that only the affected parts experience problems while the rest of the components continue to function. Use the Bulkhead pattern to achieve fault isolation.

Take this opportunity revisit design choices that might slow recovery. For example, pointing Domain Name System (DNS) records directly to your application on App Service can cause delays during recovery because of DNS propagation. Use a dedicated service like Azure Front Door as the ingress point for easier reconfiguration during recovery steps.

Expect this basic plan to evolve into a full disaster recovery plan at more mature levels.

#### Create test plans

Create test plans by simulating outages and problems identified in the risk mitigation playbook. Supplement those mitigations with simple test cases to ensure that they work as expected and are feasible. Verify that these features operate correctly and conduct degradation tests to see how the system performs when specific components fail. Keep the outcome simple by ensuring that the test either fails or passes.

Use test tools like mocking frameworks to inject faults into HTTP requests, which help you test retry policies more explicitly. Azure Chaos Studio provides a more comprehensive test suite for simulating component outages and other problems, which provides a good opportunity to explore its capabilities. You can gradually adopt Azure Chaos Studio as you become familiar with its features.

#### Assess the impact of scaling operations on reliability

To handle spikes in load, critical components must be able to scale out or scale up efficiently. Take advantage of autoscaling capabilities that Azure provides. These capabilities adjust the capacity limits of a service based on predefined configurations. This adjustment allows you to scale the service up or down as needed.

- Identify potential bottlenecks and understand the risks that they might pose. For example, high throughput shouldn't cause the flow to break down.

- Understand the load patterns. Static usage patterns might make bottlenecks less critical, but changes in usage and consumption dynamics can worsen the risks.

    > [!NOTE]
    >
    > There might be components that can't scale out, such as monolithic databases and legacy applications. Proactively monitor the load curve to allow for re-architecting if needed.

- Decide on scaling limits that are reasonable based on performance and reliability requirements. For performance concerns, scaling up gradually is most common. However, reliability concerns for critical flows might require more rapid scaling to avoid outages. In either case, avoid infinite scaling.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** When you deal with performance-related problems, scaling can be a useful mitigation strategy. However, scaling is a temporary fix and not a solution. Investigate and solve the underlying problem, such as a memory leak or a runaway process. Otherwise, you risk applying the same mitigation again at another tipping point and paying for resources that you don't need. By addressing the root cause, you can ensure long-term stability and cost-efficiency.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Scaling has cost implications. Use platform-provided auto-scaling features but set boundaries to avoid financial problems. Be aware of potential bottlenecks, such as monolithic databases or application kernels that can't scale out.

# [Level 3](#tab/level3)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: Risk mitigation

<!-- No more than 5 H4 headings per tab -->

#### Example heading

<!-- No more than 100 words under each H4 heading. -->

# [Level 4](#tab/level4)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: Operations

<!-- No more than 5 H4 headings per tab -->

#### Example heading

<!-- No more than 100 words under each H4 heading. -->

# [Level 5](#tab/level5)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: 

<!-- No more than 5 H4 headings per tab -->

#### Example heading

<!-- No more than 100 words under each H4 heading. -->

---

## Next steps
<!-- Provide at least one next step and no more than three. Include some 
context so the customer can determine why they would click the link.
-->
