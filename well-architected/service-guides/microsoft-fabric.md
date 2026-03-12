---
title: "Architecture Best Practices for Microsoft Fabric"
description: "Learn best practices for architecting Microsoft Fabric solutions with recommendations for reliability, security, cost optimization, operational excellence, and performance efficiency."
author: PageWriter-MSFT
ms.author: prwilk
ms.topic: conceptual
ms.date: 11/17/2025
products:
 - microsoft-fabric
azure.category:
 - analytics
---

# Architecture best practices for Microsoft Fabric

Microsoft Fabric is a unified analytics platform that brings together data movement, processing, ingestion, transformation, real-time event routing, and report building in a single environment. It's designed to help organizations build and deploy complete analytics solutions at scale, providing an integrated platform for data teams to collaborate efficiently across the entire data lifecycle. Across these diverse use cases for Microsoft Fabric, there are common considerations and best practices for using the platform effectively. This article addresses these considerations and gives architectural recommendations that are mapped to the principles of the Well-Architected Framework pillars.

It's assumed that as an architect, you've reviewed the analytics platform options available and chosen Microsoft Fabric as the  data platform for your workload.

**Technology scope**

This review focuses on the interrelated decisions for Microsoft Fabric components and services:

- TBD

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the nature of your application and the criticality of its components. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
>

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Configure [Microsoft Fabric capacity](/fabric/admin/service-admin-premium-capacity-management) with appropriate scaling policies and reserve additional capacity units to handle peak workloads. Monitor capacity utilization metrics and implement automated scaling triggers based on performance thresholds. | Prevents capacity exhaustion during peak periods while maintaining cost efficiency through dynamic capacity allocation that scales based on actual workload demands and performance requirements. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Microsoft Fabric.

### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture.

> [!div class="checklist"]
>

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Configure [Microsoft Entra ID integration](/fabric/admin/service-admin-portal-tenant-settings) with conditional access policies and multi-factor authentication for all Microsoft Fabric workspace access. Enable automatic user provisioning and implement role-based access controls aligned with organizational security policies. | Provides enterprise-grade authentication and authorization controls that eliminate password-related security risks while ensuring compliance with organizational security standards and regulatory requirements. |

## Cost Optimization

The purpose of the Cost Optimization pillar is to **manage costs to maximize the value delivered**.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs within the Microsoft Fabric architecture.

### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md). Define policies and procedures to continuously monitor and optimize costs while meeting your performance requirements.

> [!div class="checklist"]
>
> - **Right-size and plan compute capacity.** Compute is the primary cost driver in Microsoft Fabric, billed by provisioned capacity (CUs), not actual usage. Underutilized capacity still incurs cost. Plan for growth by scaling up (larger capacities) or scaling out (additional capacities).
>
>   Do cost modeling exercise based on workloads usage patterns: peak or average usage, interactive or background operations, and continuous or intermittent ingestion. 
> 
>   Align use of Fabric capabilities with the cost model. For example, background operations like dataset refreshes, Notebook jobs, can count against capacity if scheduled at the same time. Take advantage of Fabric's surge protection to stagger usage.
>
>   Validate sizing and pricing with POCs or load tests.
>
> - **Consolidate workloads to improve utilization.** A single capacity can host multiple workspaces and workloads. Determine which workloads can be pooled on shared capacity can improve utilization.  
>
> - **Choose the appropriate billing model.** Use pay-as-you-go (PAYG) for bursty workloads that can be paused or resized, and reserved capacity for predictable workloads. Most often, hybrid approaches are applied. For example, reserving capacity for baseline demand and using PAYG to handle peak usage.
>
> - **Manage capacity usage and scaling.** Costs accrue while capacity is active, regardless of utilization. 
>
>   Fabric capabilities like bursting can prevent performance degradation and should be used. But make sure cost model accounts for the extra usage that is tracked as capacity debt. Fabric slows or throttles workloads later to repay that debt. If that happens frequently, you might need to upgrade capacity.
> 
>   Autoscale capabilities for workloads such as Spark and data warehousing should be used carefully to prevent unexpected compute charges. Configure upper limits to prevent runaway costs, and monitor CU consumption to avoid unexpected charges.
> 
> - **Optimize storage usage.** Storage is billed separately in Microsoft OneLake. Apply data lifecycle management (archiving, retention, cleanup) and maximize in-place sharing (shortcuts, free mirroring) to reduce duplication. Keep in mind that storage costs continue even when compute is paused; backup and soft-deleted items may create hidden costs.
>
> - **Minimize additional operational costs.** Track data ingestion, query complexity, data transfers, and cross-region movement to avoid hidden charges.
>
> - **Review licensing requirements.** Power BI content creation or publishing may require Power BI Pro licenses, even when using Fabric capacity. Verify requirements per Fabric SKU. 
>
> - **Establish cost visibility and governance.** Monitor capacity utilization by workspace and workload, investigate spikes, and configure budget alerts for anomalies. 
>
>   Use tagging and reporting to attribute consumption to teams, and export billing/usage to Fabric dashboards for CU usage, storage, and forecasts. 
>
>   Apply workspace-level controls. For example, capacity policies, limits on SKUs, pause schedules for non-production environments. Assign capacity-level cost ownership, and use the Fabric Chargeback App for shared capacities. Integrate with governance frameworks such as Microsoft Purview.
>
> - **Optimize environment costs.** Microsoft Fabric SKUs provide the same functional capabilities, regardless of the environment. 
>
>   Non-production environments should use smaller capacities and be paused when idle. Also, Fabric supports ephemeral environments, allowing capacities to be provisioned and de-provisioned automatically. Use deployment pipelines to separate dev/test/production workloads and enforce governance.
>
> - **Use automation to reduce costs.** Automate scaling, pausing, and resizing via Azure Resource Manager APIs or tools like Bicep, Terraform, Azure CLI, PowerShell, orchestrated through Azure Automation, Logic Apps, or Fabric Pipelines. Automation can also manage lifecycle tasks, such as provisioning temporary environments, archiving obsolete data, and detecting unused artifacts. Ensure automation aligns with the pricing model; pausing reserved capacities doesn't reduce costs.
>

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
|Understand the cost drivers for your Fabric workload. <br><br> [Understand your Azure bill on a Fabric capacity](/fabric/enterprise/azure-billing)|The invoice meters will help you with cost analysis. |
|Use the [Fabric Capacity Estimator](https://www.microsoft.com/microsoft-fabric/capacity-estimator) for estimating new workloads. <br><br> Validate estimates with POCs or load tests to ensure they reflect real-world conditions.|Helps predict the required capacity and associated costs based on expected usage patterns.|
|Use [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) for Azure-based resources used with Fabric.|Provides estimates for resources that are part of the workload.|
|Use [Microsoft Cost Management + Billing](/azure/cost-management-billing/) for reviewing costs.|Helps track, analyze, and optimize ongoing Fabric costs.|
|Monitor [Microsoft Fabric capacity metrics](/fabric/admin/service-admin-premium-capacity-management) and implement automated capacity scaling policies based on usage patterns. Configure alerts for capacity utilization thresholds to prevent performance degradation and cost overruns.|Optimizes capacity costs by automatically adjusting resources based on actual demand while preventing performance issues during peak usage periods, resulting in 20-40% cost savings compared to fixed capacity allocation.|
|Monitor costs with [Solution Accelerator: Fabric Cost Analysis](https://github.com/microsoft/fabric-toolbox/tree/main/monitoring/fabric-cost-analysis).|Provides a holistic view on Microsoft Fabric cost aspects founded on FinOps practices.|
|Use [Fabric Chargeback App](/fabric/enterprise/chargeback-app) to build chargeback processes that fairly allocate costs based on actual consumption.|Helps you understand which teams, users, and workloads are driving capacity usage.|
|Take advantage of [Fabric surge protection](/fabric/enterprise/surge-protection) for background operations to avoid unnecessary scaling.|Prevents overload from too many simultaneous background jobs, reducing unnecessary capacity spikes and associated costs.|
|Mitigate capacity spikes with [smoothing](/fabric/enterprise/throttling#smoothing) to spread usage across time windows.|Prevents sharp usage spikes in capacity metrics by averaging workload demand, leading to more predictable cost patterns and reduced peak capacity requirements.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**. The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Workload design checklist

In general, keep in mind that Fabric is a SaaS platform, so there's less day-to-day administration, but Fabric can support rigourous DevOps processes and sophisticated administrative workflows. You'll need to define operational practices and the rigor should be aligned to the business criticality of the workload.  Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Microsoft Fabric.

> [!div class="checklist"]
> 
> - **Select the right tools and frameworks**. Use workload-specific testing tools: pytest or nutter for notebooks, Great Expectations for data quality, Azure DevOps Test Plans for coordination, Fabric REST APIs for automation, and custom Python/PowerShell scripts for integration validation. Use Fabric and KQL queries to monitor results.
>
> - **Validate solution functionality and configuration**. Have unit tests on notebooks and pipelines, data quality checks using frameworks like Great Expectations, end-to-end workflow testing in dedicated test workspaces, and pre-production business acceptance testing via deployment pipelines. Use test stages as gates before promoting changes to production.
> 
> - **Configure test environments properly**. Maintain separate dev/test/prod workspaces on dedicated capacities. Use sample or anonymized datasets and parameterized rules to ensure tests do not impact production. Non-production capacities can be smaller, but scale up temporarily for load or performance testing. Test environments should mirror production workloads, concurrency, and data patterns.
> 
> - **Do incremental updates**. Compartmentalize solution components into dedicated workspaces to reduce blast radius and enable controlled release promotion. 
>
>   Require passing unit tests, integration tests, and data quality validations before promoting artifacts. For example, you might have test stages in dedicated workspace to validate that appropriate items exist, they function as expected, do data integrity checks in data stores. Some of those checks can be automated, but also combine with manual testing, particularly those involving user interacativity, such as reports and dashboards. Include manual approvals from business stakeholders and assess capacity utilization post-deployment to detect potential performance or scaling issues.
>
>   >[!NOTE] 
>   >Microsoft Fabric does not provide native rollback capabilities. Rollback is achieved by redeploying a previous version from version control or reapplying earlier configuration and provisioning settings. Because Fabric solutions span multiple data stores, any rollback strategy must also address data integrity and consistency, and may require additional measures to restore data state safely.
>
> - **Design for incremental and safe releases**. Compartmentalize solutions into dedicated workspaces and use separate dev, test, and production environments to reduce blast radius. Design validation stages that include functional checks and data integrity testing, combining automation with manual validation for interactive workloads. Because Fabric has no native rollback, ensure your design relies on version control and redeployment, with explicit consideration for restoring data consistency if a rollback is required.
>
> - **Design a layered monitoring approach**. Plan monitoring across tenant, capacity, and workspace scopes so you can see both platform health and workload behavior. At design time, decide how you will track capacity utilization and storage growth, workload execution and failures, workspace-level operations, user activity and data access, and key dependencies such as other Azure services or even centrally-managed technologies like identity platforms, data sources, and gateways.
>
> - **Plan alerts and notifications early**.  Define which events require alerts and who should receive them. Where possible, also define the automated actions that should be triggered. 
>
>   Capacity-related alerts are recommended. Enable service outage notifications and job failure alerts by default, and design escalation paths using multi-channel notifications based on severity levels that you define at a granular level for various components and items in Fabric.
>
>   Use CI/CD pipeline error triggers to detect deployment failures and prevent promoting faulty deployments.
>
> - **Account for log retention and data volume**. Native Fabric logs and metrics are retained for limited periods, so decide upfront whether historical data is needed and where it should be stored. Use detailed workspace monitoring and OneLake Diagnostics selectively, and design for aggregation and sampling to control monitoring costs.
> 
> - **Design around automation capabilities**. Rely on Fabric's built-in automation for consistent provisioning and deployment using Git and Deployment Pipelines. Where needed, design custom automation to handle drift remediation, workspace setup, access configuration, refresh scheduling, and so on. Integrate automated tests into Azure DevOps or GitHub Actions pipelines, triggered on pull requests and deployments.
>
>   Make sure to align deployment rigor with business impact, and for mission-critical or large-scale workloads, prioritize Fabric items that support automated deployment and account for capacity and service limits in your design.
>
> - **Plan for both failover and failback procedures**. Fabric provides a Business Continuity and Disaster Recovery (BCDR) feature that replicates OneLake data to a secondary region. Failover is initiated by Microsoft based on internal failure detection.  After which you need to have new capabilities in secondary region where redeploy Fabric items and rehydrate data from replicated stores. For high availability, select zones and regions that offer zone redundancy, enable BCDR on capacities, and document disaster recovery plans.
>
>   >[!NOTE] 
>   >Make sure you test disaster recovery. For mission-critical workloads, you might  run multiple instances of a solution on capacities in multiple regions and simulate failover by pausing a capacity in one of the regions.
>
>   After a disaster is resolved, safely return to primary operations by validating and reconciling data across OneLake and other stores. Where needed, re-deploy workspaces and restore affected data. Maintain versioned artifacts and Git history to enable safe promotion of prior stable versions.
>
> - **Plan for partial failures**. Not all failures affect the platform globally. Consider component-specific failures (Lakehouses, warehouses, notebooks), item metadata corruption, OneLake-only issues, capacity-specific outages, workspace-level problems, and dependent service outages (e.g., Entra ID, SQL, Storage). Create recovery procedures for each scenario, including item-level restore and environment synchronization strategies.
>
> - **Maintain production-like test environments**. Use separate dev/test/prod workspaces on dedicated capacities, with anonymized or sample data and parameterized rules to avoid production impact. Non-production capacities can be smaller but should scale temporarily for performance or load testing and mirror production concurrency and data patterns.
>
> - **Define roles, ownership, and required skills**. Clearly define responsibilities across tenant, capacity, and workspace administrators, and document the intent behind key design decisions such as workspace strategy, capacity sizing, security controls, data lifecycle, and CI/CD standards. Expect shared ownership between platform and workload teams, supported by a collaborative DevOps mindset. 
> 
>   Ensure the team has strong data engineering and analytics skills—Lakehouse and Delta Lake concepts, Spark and SQL, Power BI semantic models, and Fabric Data Factory—and invest in Fabric training and certifications such as DP-600 and DP-700.


### Recommendations

| Recommendation | Benefit |
| --- | --- |
| Enable **Workspace Monitoring** to collect logs and metrics into an Eventhouse within the workspace. <br><br> Consider tracking workload execution, including job failures, retries, and long-running jobs. Use this data to troubleshoot deployments and operational issues. | Provides workload-level visibility for troubleshooting performance, failures, and operational behavior without relying solely on tenant-wide logs. |
| Export **Microsoft Fabric audit logs** (from Microsoft 365 audit logs) to **Azure Monitor / Log Analytics**. <br><br> Use these logs to monitor user activity and data access patterns, investigate usage, enforce governance, and support security requirements. | Enables centralized monitoring, long-term retention, and correlation with other Azure platform signals. |
| Use the **Microsoft Fabric Capacity Metrics app** as the primary dashboard for capacity health, compute, and storage, and enable built-in threshold notifications. <br><br> Track CU consumption, throttling events, autoscale behavior, and storage usage trends at both capacity and workspace levels to prevent unexpected pressure. | Delivers  insight into CU consumption, throttling, autoscale behavior, and storage usage over rolling 14-day windows. |
| Use [**Fabric Activator**](/fabric/real-time-intelligence/data-activator/activator-introduction?utm_source=chatgpt.com) to monitor Fabric capacity events and trigger real-time alerts or automated actions. <br><br> Include upstream and downstream dependencies such as Entra ID, Azure Storage, networking, and customer-managed gateways to maintain operational continuity. | Enables proactive monitoring and faster response to capacity pressure, failures, or operational anomalies. |
| Enable **OneLake Diagnostics** selectively for workspaces that require detailed data-access visibility. | Provides insight into how data is accessed within a workspace, supporting governance, security investigations, and performance analysis. |
| Configure **Fabric monitoring settings** at the tenant level to collect telemetry across all workspace components. <br><br> Define alert escalation paths, validate pipelines and semantic model refreshes, and implement proactive monitoring for data quality issues. | Ensures consistent, platform-wide observability and faster detection of capacity, pipeline, or data-quality issues. |
| Use **Capacity Utilization Events** in the Real-Time Intelligence workload for near real-time alerts and automation when thresholds are exceeded. | Allows rapid detection and automated handling of capacity pressure scenarios before users are impacted. |
|Enable BCDR feature||


## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Microsoft Fabric. 

> [!div class="checklist"]
>
> - **Do capacity planning**.  Estimate optimal capacity units (CUs) and CU-seconds based on workload characteristics. Right-size capacities to meet peak demand without over-provisioning. Unused CUs don't carry over, so careful estimation is key. 
>
> - **Conduct performance testing with realistic workloads**. There's no single testing tool that fits all workload types. Use workload-specific test tools and combine those with general-purpose load testing tools. For example, JMeter and Locust can be used to test for concurrent user queries where applicable. SQL endpoints can be benchmarked using standard datasets and queries such as TPC-H/TPC. Certain items may require simulating large volume of data to measure throughput and latency for ingestion and processing workloads. Gradually ramp up load, include realistic concurrency, data variety, and peak scenarios. Avoid extrapolating results from small test environments—performance rarely scales linearly.
>
> - **Align test environments with production**. Match capacity size, region, and other configuration characteristics to your production environment. For consumption-oriented workloads, include realistic user concurrency, data volumes, and query diversity; for ingestion and processing, use varied data to avoid misleading results from compression or repetition. For scenarios where data is continuously updated, make sure in your test environment is able to ingest, process, and consume data concurrently.
>
> - **Monitor and analyze performance metrics**. Track Fabric CU consumption as the primary metric, and supplement with workload-specific metrics such as response time, throughput, concurrency, error rates, CPU, and memory usage. Capture statistical distributions (min, max, mean, median, p95, p99) to understand variability and peak behavior.
> 
> - **Establish performance baseline with iterative testing**. Establish performance baselines iteratively. Deploy representative workload samples to measure actual usage and critical operation metrics. Adjust capacity based on observed results, retest, and refine estimates.
>
> - **Align SKU size to compute demand and peak concurrency**. Fabric SKUs differ mainly in available compute (CUs), which drives latency, throughput, and concurrent usage. Select a tier based on peak workload requirements and SLAs. Avoid a one-size-fits-all approach, because different workloads consume capacity in different ways. Classify workloads by behavior and features: for example, data warehouse resources are used only when queries execute; interactive workloads require planning for peak demand; continuously running background tasks can be sized based on daily averages. Understand those patterns to map CU consumption and determine whether to increase capacity, redesign workload distribution, or leverage autoscalingy averages. Map these patterns to CU usage to decide whether to increase capacity, redesign workload distribution, or use autoscaling.
>
>   Plan for optimization before going bigger. Higher tiers provide more compute and memory headroom but not new features necessarily. Where possible, remain on lower SKUs explore techniques like caching, pre-aggregation in OneLake, PowerBI import mode. Take advantage of the built-in bursting, smoothing, or autoscale billing to absorb short-term spikes without permanently overprovisioning capacity.
>
>   Design solutions across multiple workspaces so they can later scale vertically or be distributed across multiple capacities as growth demands.
>
> - **Be aware of the quotas and limits**. Each Fabric SKU also enforces fixed compute, memory, and service-level quotas that can become hard ceilings if they are not explicitly accounted for in the architecture. For example, for Spark there are limits on vCore. Concurrency can be a hidden constraint. A large Spark job, a heavy SQL query, or a pipeline with parallel activities can consume a significant share of available CUs. 
>
>   Each Azure subscription has region-specific quotas on the number of CUs that can be provisioned, which can limit workload deployment in certain regions. If quotas are reached, you can request an increase or consider deploying workloads in adjacent regions, balancing proximity to users and data with available capacity. Be aware that even with the same SKU, performance may vary across regions.
>
>   Workspaces also have limits on the number of items and users that can be assigned.
>   
>   Lower SKUs, like F8, can lead to memory and compute constraints and aren't recommended for production workloads.
>   
> - **Account for the dependencies**. Capacity bottlenecks are often the result of shared resources and platform dependencies that aren't always obvious. Performance can be constrained by underlying data sources resulting in slow queries, gateway limits, or network latency. There might be situations where multiple solutions compete for the same capacity ("noisy neighbor issues"), or by tightly coupling many different item types within a single workspace. 
>
>   A practical approach is to design with isolation and growth in mind: break larger solutions into purpose-driven workspaces, and place them on shared or dedicated capacities depending on their scale and criticality. For example, you might separate ingestion, storage, engineering, orchestration, and reporting components. It's also important to actively manage underlying dependencies. For example, optimizing OneLake data layout and partitioning, placing capacities close to key data sources, right-sizing and caching gateways, and cleaning up idle Spark sessions. 
> 
> - **Design for both vertical and horizontal scaling from the start**. Vertical scaling increases resources but may interrupt in-flight jobs. Horizontal scaling distributes workloads across capacities and workspaces and is generally less disruptive. Build workloads to tolerate retries and transient failures during scaling.
>
> - **Plan around Fabric capacity limits**. Scaling up increases available capacity, but Fabric SKUs have hard upper limits. Some workloads, like single Power BI datasets, can't span multiple capacities. For large or growing workloads, scaling out across multiple workspaces and smaller capacities than scaling up. Not all workloads are parallelizable, so adding capacity may not always improve performance. Long-term capacity planning should include partitioning workloads, using aggregations, sequencing large jobs, and avoiding hard scaling ceilings.
>
> - **Take advantage of decoupled storage and compute**. Fabric allows independently scaling of storage, compute, and engines (Spark, Warehouses, Pipelines) providing more granular control over performance and resource usage.
>
> - **Use balanced partitioning**. Partitioning can improve performance by reducing the volume of data processed per operation, but it must be carefully balanced against the management overhead and the risk of overly small or fragmented partitions. Common approaches include partitioning by date, key columns, or incremental refresh in Power BI to reduce per-operation data volumes. Test partition strategies to ensure they improve performance without unnecessary complexity.
>   
> - **Scale based on sustained pressure and validate with targeted testing**. T Use CU utilization and secondary indicators (memory errors, latency, operation duration) to trigger scaling actions. Validate scaling strategies through targeted load and benchmarking tests across different scaling configurations, measuring throughput, concurrency, execution duration, latency, CU utilization, and error rates during scale-up/down or scale-in/out events. Include test cases for retry or recover situations from interruptions. For example, if you plan to scale out by using two capacities, perform a test where you simulate the distribution of load between them and see if response times improve proportionally. 
>
>   Scaling validation must also include the resources hosted on Fabric capacities, such as Spark clusters, semantic models, Eventstreams, and Eventhouses, since some workloads require explicit configuration to benefit from additional compute, while others may not scale due to limited parallelism. 

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
|Take advantage of the [**Microsoft Fabric Capacity Estimator**](https://www.microsoft.com/microsoft-fabric/capacity-estimator?msockid=0d8bfe5e72c36a7b1318edb276c364ab) to get an initial estimate the capacity units (CUs) required for your workload. |Helps ensure workloads have the right resources to run without performance degradation and also and supports in better scaling decisions.|
|What tools are recommended for performance load testing?||
|Choose the optimal [**Microsoft Fabric SKU**](/fabric/enterprise/licenses#capacity) based on your capacity planning.|Ensures that workloads have the right level of compute and memory resources, supporting reliable performance, efficient scaling, and alignment with expected usage patterns.|
|Start with the [Fabric Capacity Metrics app](/fabric/enterprise/metrics-app) to track overall utilization trends, bursts, and sustained load over time.|This insight helps in making informed scaling decisions, validate whether current capacity aligns with workload demands, and proactively address performance bottlenecks before they impact users.|
|Use [Capacity Events in Real-Time hub](/fabric/real-time-hub/explore-fabric-capacity-overview-events) to get details on your capacity.|You'll get real-time visibility into capacity-level events, enabling faster root-cause analysis of performance issues or throttling scenarios.|


## Azure policies

Azure provides an extensive set of built-in policies related to Microsoft Fabric and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Microsoft Fabric workspaces implement appropriate capacity governance policies
- Microsoft Fabric workspaces use encryption for data protection requirements
- Microsoft Fabric workspaces have proper access controls and authentication configured
- Microsoft Fabric workspaces implement required monitoring and logging capabilities
- Microsoft Fabric workspaces follow organizational naming and tagging conventions
- Microsoft Fabric workspaces are deployed in approved geographic regions for compliance

For more information about governance, review the [Azure Policy built-in definitions](/azure/governance/policy/) and other policies that might affect the security of the analytics platform.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Tradeoffs

You might have to make design tradeoffs if you use the approaches in the pillar checklists.

:::image type="icon" source="../_images/trade-off.svg"::: **Cost and Performance**

- In Microsoft Fabric, hosting multiple workspaces and workloads on a single capacity can improve compute utilization and reduce storage duplication through in-place data sharing. However, consolidation introduces potential resource contention ("noisy neighbor"), where heavy workloads (for example pipelines or refreshes) may affect interactive performance or reliability. 

- While bursting accelerates workloads, the additional compute usage is smoothed across future timepoints, which can temporarily consume capacity later and potentially affect other workloads. In highly shared environments, this may lead to resource contention or delayed background tasks if capacity remains busy repaying smoothed usage.

## Scenario architecture

Foundational architecture that demonstrates the key recommendations: [Analytics end-to-end with Microsoft Fabric](/azure/architecture/example-scenario/analytics/analytics-end-to-end-fabric).

## Related content

- [Microsoft Fabric documentation](/fabric/)
- [Microsoft Fabric best practices](/fabric/get-started/fabric-adoption-roadmap)
