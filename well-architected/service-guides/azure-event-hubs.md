---
title: "Architecture Best Practices for Azure Event Hubs"
description: "Explore design considerations and recommendations for Azure Event Hubs to optimize reliability, security, cost, operational excellence, and performance efficiency."
author: PageWriter-MSFT
ms.author: prwilk
ms.topic: concept-article
ms.date: 12/18/2025
ms.subservice: waf-service-guide
products:
 - azure-event-hubs
 - azure-monitor
azure.category:
 - analytics
 - integration
---

<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD025 -->

# Architecture best practices for Azure Event Hubs

Azure Event Hubs is a real-time data streaming platform and event ingestion service that can receive and process millions of events per second. It provides a unified streaming platform with time-retention buffer, decoupling event producers from event consumers. Event Hubs natively integrates with Azure analytics services and supports multiple protocols including AMQP, Apache Kafka, and HTTPS for flexible event streaming architectures.

This article assumes that as an architect, you've reviewed the [messaging decision tree](/azure/architecture/guide/technology-choices/messaging) and chose Azure Event Hubs as the messaging solution for your workload.

The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](/azure/well-architected/pillars).

<!-- markdownlint-disable-next-line MD036 -->
**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:

- Azure Event Hubs

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the nature of your application and the criticality of its components. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review quotas and limits that might restrict your design:** Analyze [quotas and limits](/azure/event-hubs/event-hubs-quotas) that affect architecture decisions. For large-scale scenarios, be aware of the partition limits associated with different service tiers as they differ significantly.
>
>    Determine how many throughput units or processing units you need. These settings define message ingestion capacity and establish scaling boundaries for your workload. Keep payloads within the maximum message size for your chosen tier.
>
>    Choose appropriate message retention periods that match the service tier and meet recovery and replay requirements. Plan your application architecture around consumer group limits for each namespace.
>
> - **Anticipate potential failures through failure mode analysis:** Failure mode analysis provides a systematic approach to help you identify failures and create mitigation strategies that keep your messaging service resilient.
>
>    | Failure | Mitigation |
>    |---------|------------|
>    | Namespace unavailable | Implement monitoring and alerting for early detection (see monitoring consideration). Plan for cross-region failover capabilities. |
>    | Partition unavailable | Design partition distribution strategies to prevent single points of failure. Implement retry logic with exponential backoff in producers and consumers. |
>    | High message volumes exceed throughput limits | Plan for capacity scaling to handle traffic spikes. Implement client-side throttling protection by using circuit breaker patterns. |
>
> - **Define reliability targets for event streaming workloads:** Set clear reliability targets to guide architecture decisions and operational practices. Define service-level objectives (SLOs) that include message ingestion availability, consumer processing latency, and end-to-end delivery performance. Use service-level indicators (SLIs) to measure these metrics.
>
>    Set availability targets that align with service-level agreement (SLA) guarantees of 99.9% for the Standard tier and 99.99% for the Dedicated tier. Evaluate retention period requirements that directly affect recovery objectives and tier selection decisions for your workload.
>
> - **Implement redundancy strategies to eliminate single points of failure:** Add multiple redundancy layers, including availability zones, geo-replication, and partition distribution. Enable zone-level redundancy in Premium and Dedicated tiers for automatic replication across availability zones with transparent failover within the same region.
>
>   Use geo-replication for Dedicated tier namespaces to enable real-time asynchronous replication of events and metadata across multiple regions. This approach provides regional failover capabilities.
>
>    Add partition and application-level redundancy, and use effective partition key distribution to prevent hotspots. Deploy producer and consumer redundancy patterns to ensure that operations continue when instances fail. Add storage account redundancy to protect checkpointing data required for consumer state management.
>
> - **Design for reliable scaling to handle variable workloads:** Configure infrastructure scaling through auto-inflate in the Standard and Premium tiers. This feature automatically scales throughput units during traffic spikes. Use the Dedicated tier for predictable performance with reserved capacity.
>
>    Plan partition scaling carefully because you can't decrease the partition count after creation. Implement application-level scaling by using producer patterns that distribute load across partitions. For optimal throughput, choose appropriate partition keys and align consumer group instances with partition count.
>
> - **Implement monitoring and alerting for reliability:** Configure Azure Monitor to collect metrics. Track message ingestion volume, throughput utilization, and consumer lag. Add infrastructure metrics to monitor namespace health.
>
>    Configure thresholds that align with SLOs and business impact severity. Monitor multi-dimensional metrics for granular visibility across namespace, event hub, and partition levels.
>
> - **Configure Event Hubs for self-preservation and graceful degradation:** Event Hubs doesn't include a built-in dead-letter queue. Implement custom patterns to handle poison messages, and use circuit breaker patterns to protect traffic during recovery scenarios.
>
>    Isolate consumers by creating separate consumer groups with only one active receiver for each group. Set up SDK exception handling to properly catch `EventHubsException` or `OperationCancelledException` from retry policies. Enable the capture feature to provide durable storage for event replay and recovery scenarios.
>
> - **Do reliability testing to validate resilience:** Use reliability tests to verify that your workload meets availability and performance targets. Include the following tests:
>
>   - Test partition availability, consumer lag recovery, and throughput scaling under realistic streaming conditions.
>   
>   - Run stress testing to validate performance under expected and peak traffic conditions.
>   - Perform consumer lag testing to ensure that processing keeps pace with ingestion rates.
>   - Test the solution's resilience with chaos engineering and by introducing intentional failures.
>   - Perform failover testing to verify geo-disaster recovery and zone redundancy capabilities.
>   - Test dependency failures for Azure Storage and other service dependencies.
>
> - **Design disaster recovery strategy for business continuity:** Use geo-disaster recovery for Standard and Premium tiers or geo-replication for the Dedicated tier to provide regional failover capabilities. Define your recovery time objective (RTO) and recovery point objective (RPO) based on business requirements. Choose DR patterns, like active-passive for cost optimization or active-active for minimal RTO.
>
>    Use geo-disaster recovery for Standard and Premium tiers to enable metadata replication between paired namespaces across regions. This setup provides automated failover capabilities during regional outages. Implement client-side failover capabilities for faster recovery. Evaluate data consistency requirements for the chosen pattern.
>
>    Create operational continuity plans that include documented backup procedures for metadata and consumer offsets, execution runbooks, communication plans, and regularly scheduled DR testing.

### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Enable [availability zone redundancy](/azure/reliability/reliability-event-hubs#availability-zone-support) when you create a namespace by selecting an Azure region with availability zone support. | Provides automatic datacenter-level failover with minimal recovery time and no application changes. |
| Enable [geo-replication](/azure/event-hubs/geo-replication) for Dedicated tier namespaces to replicate events and metadata across multiple regions in real time.<br><br>Configure primary and secondary regions with appropriate network connectivity and monitor replication lag metrics to ensure data consistency across regions. | Enables regional failover capabilities and minimal data loss during regional outages.|
| Set up [geo-disaster recovery](/azure/event-hubs/event-hubs-geo-dr) pairing between primary and secondary namespaces, then configure an alias for the namespace pair. Update connection strings to use the alias for transparent failover capabilities. | Enables cross-region failover capability with transparent client failover by using the alias connection. This configuration ensures business continuity during regional outages. |
| Implement [hash-based partition keys](/dotnet/api/azure.messaging.eventhubs.producer.sendeventoptions.partitionkey) for event distribution and set the appropriate partition count during event hub creation. Monitor partition-level metrics for hotspot detection and load balancing validation. | Prevents partition hotspots and enables horizontal scaling through balanced load distribution across partitions. |
| Enable [auto-inflate](/azure/event-hubs/event-hubs-auto-inflate) for Standard tier namespaces and set maximum throughput units based on peak load projections. Use the Premium tier for sustained high-throughput workloads that require predictable performance. | Prevents ingestion throttling through automatic capacity scaling and maintains consistent availability during load variations without manual intervention. |
| Configure [Event Hubs alerts in Azure Monitor](/azure/azure-monitor/essentials/metrics-supported#microsofteventhubnamespaces) for consumer lag, throughput utilization, and throttling events. Set thresholds that align with SLOs.<br><br> Enable alerts for connection and authentication failures, then create alert action groups for incident response. | Enables early problem detection, reduces incident response time, and provides capacity planning insights through monitoring coverage. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure Event Hubs.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture.

> [!div class="checklist"]
>
> - **Establish a security baseline for Event Hubs workloads:** Review the [Event Hubs security baseline](/security/benchmark/azure/baselines/event-hubs-security-baseline) that addresses key security domains. Apply Microsoft Cloud Security Benchmark controls for Event Hubs that cover encryption for data at rest and in transit, network isolation capabilities, and granular access control mechanisms.
>
> - **Implement segmentation strategies to isolate Event Hubs resources and control access boundaries:** Apply isolation strategies across multiple aspects of the workload, like identity-based access control, network boundaries, and resource grouping. Use Microsoft Entra ID with role-based access control (RBAC) to create logical access control boundaries for identity-based segmentation.
>
>    Set up network segmentation to isolate streaming traffic. Use virtual network integration and private connectivity options to prevent public internet exposure. Configure namespace-level isolation to separate tenant boundaries and implement consumer group separation for multi-tenant event processing within a shared Event Hubs deployment.
>
> - **Set up identity and access management:** Use Microsoft Entra ID as the trusted identity provider for centralized authentication and authorization in Event Hubs workloads.
>
>    Configure role-based authorization for granular access controls through SendOnly and ListenOnly policies. These policies separate publisher and consumer permissions. Enable conditional access policies for context-aware security based on device compliance, network location, and user context.
>
>    Assign separate roles for administrative and high-privilege identities and enable just-in-time access. Use managed identities to remove credential management overhead for service-to-service communication patterns.
>
> - **Add network security controls to protect Event Hubs traffic and connectivity:** Use private endpoints, service endpoints, and IP firewall rules to secure streaming traffic from unauthorized access. Configure private endpoints to eliminate internet exposure. Enable service endpoints to provide secure virtual network connectivity without public routing.
>
>    Set up network security groups to filter traffic and control flow at the subnet level. Configure IP firewall rules to allow access only from specific IP addresses and CIDR ranges.
>
> - **Configure data encryption for Event Hubs event streams and metadata:** Enable customer-managed keys for enhanced encryption control and regulatory compliance. Use double encryption for scenarios that need extra protection. Set TLS 1.2 as the minimum configuration for secure client connections and inter-service communication.
>
>    Establish key rotation strategies with operational procedures for customer-managed keys. Deploy Managed HSM when your organization requires high-security key storage.
>
> - **Harden Event Hubs namespace and entity configurations to reduce attack surface:** Turn off auto-inflate when you don't need it for capacity management. Disable other unused features and protocols to remove potential attack vectors.
>
>    Remove unused consumer groups and event hubs, and enable only the features that your workloads need.
>
> - **Secure Event Hubs connection strings and access keys through proper secret management:** Use managed identities as the preferred authentication method to avoid storing credentials. When you must use connection strings or SAS tokens, store them securely and rotate them regularly.
>
>    Store Event Hubs connection strings in Azure Key Vault and configure separate keys for producers and consumers to enforce least privilege principles. Maintain distinct access keys for different consumers and environments so that you can revoke specific access. Deploy short-lived SAS tokens to reduce security exposure risk.
>
> - **Implement security monitoring and logging for Event Hubs operations:** Configure diagnostic logging to capture authentication events, data plane access patterns, and configuration changes. Set up identity and access monitoring to track failed authentication attempts and attempts to elevate privileges.
>
>    Monitor network access patterns to detect unauthorized access by checking where connections originate. Keep logs for configuration changes and administrative operations to support compliance and security analysis.
>
>    Enable threat detection to alert you about suspicious activity including unusual access volumes or unknown sources. Monitor consumer group operations and partition access patterns to detect anomalies. Use User and Entity Behavior Analytics to identify abnormal usage patterns that might indicate security incidents.
>
> - **Implement security testing and validation processes for Event Hubs streaming workloads:** Test assignments by validating that Data Owner, Data Sender, and Data Receiver roles function correctly across namespace, event hub, and consumer group scopes. Verify that conditional access policies block unauthorized producer and consumer connections effectively.
>
>    Validate Event Hubs network security through private endpoint connectivity testing, IP firewall rule checks for producer and consumer access, and virtual network integration validation for streaming traffic isolation.
>
>    Test customer-managed key encryption for event data and metadata, verify TLS 1.2 enforcement for AMQP and HTTPS connections, and confirm that double encryption works as expected. Run streaming-specific penetration tests that include producer authentication bypass attempts, consumer group enumeration, and partition-level access checks.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Use Microsoft Entra ID to [authorize access to Event Hubs](/azure/event-hubs/authorize-access-azure-active-directory) resources. Assign built-in roles including Data Owner, Data Sender, and Data Receiver at appropriate scope levels based on access requirements.<br><br>Scope these assignments at namespace, event hub, or consumer group levels to get granular access control that matches operational needs. | Provides granular access control across scopes and clearly separates producer, consumer, and administrative operations.|
| Deploy [private endpoints](/azure/event-hubs/private-link-service) within dedicated subnets for network isolation and configure network security groups to control traffic flow to Event Hubs subnets.<br><br> Alternatively, apply [IP firewall rules](/azure/event-hubs/event-hubs-ip-filtering) at the namespace level for known source networks and organize namespaces across virtual networks based on security zones. | Removes public internet exposure through private connectivity and aligns network-level traffic control with organizational security zones.|
| Enable [Microsoft Entra ID authentication at the namespace level](/azure/event-hubs/authenticate-application) and disable SAS authentication where managed identities are available.<br><br>Create [conditional access policies](/entra/identity/conditional-access/overview) that require compliant devices, and block access from untrusted locations. Require multifactor authentication (MFA) for users with the Event Hubs Data Owner role to enhance administrative security. | Namespace-level enablement centralizes authentication configuration across Event Hubs entities. Disabling SAS removes shared credential management overhead.|
| Set the [minimum TLS version to 1.2](/azure/event-hubs/transport-layer-security-enforce-minimum-version) at the namespace scope to block older, vulnerable protocols. Enable diagnostic logging and query for TLS version violations in Log Analytics to identify non-compliant clients. | TLS 1.2 prevents the exploitation of legacy SSL or TLS vulnerabilities that can compromise data in transit. |
| Create an RSA 2048 key in Key Vault and assign the Key Vault Crypto Service Encryption User role to the namespace managed identity. Configure the namespace to use a [customer-managed key](/azure/event-hubs/configure-customer-managed-key) and set a 90-day rotation policy in Key Vault.<br><br>Enable [double encryption](/azure/event-hubs/event-hubs-premium-overview#encryption) when you create a namespace because you can't add this capability later.| CMK configuration provides regulatory compliance for data sovereignty requirements. The 90-day rotation policy balances security with operational stability. Double encryption at creation time provides an extra layer of protection from the start.|
| Assign the [policy that configures Event Hubs to use private DNS zones](/azure/governance/policy/samples/built-in-policies#event-hubs) at the subscription scope for consistent configuration enforcement.<br><br>Create an activity log alert rule for *Microsoft.EventHub* write operations and send notifications through an action group. Review Microsoft Defender for Cloud recommendations for Event Hubs namespaces each week. | Policy assignment at the subscription scope ensures consistent configuration across namespaces. <br><br>Weekly Defender for Cloud reviews help you identify security improvements. |
| Store connection strings in [Key Vault](/azure/key-vault/general/overview) with a 90-day expiration date set and assign the Key Vault Secrets User role to the application's managed identity. If you need [SAS tokens](/azure/event-hubs/authorize-access-shared-access-signature), generate entity-level tokens with a one-hour expiry time and use Send or Listen permissions only. | 90-day expiration enforces regular credential rotation. Managed identity access to Key Vault removes secondary credential storage requirements. <br><br>One-hour SAS token expiry limits the credential exposure window. Send/Listen-only permissions reduce blast radius in case of a token compromise situation.|
| Send [diagnostic logs](/azure/event-hubs/event-hubs-diagnostic-logs) to a Log Analytics workspace and add the Event Hubs data connector in [Microsoft Sentinel](/azure/sentinel/data-connectors-reference) under Data connectors for advanced threat detection.<br><br>Create an alert for the *RequestsAuthenticationError* metric with a threshold more than 10 per hour to detect authentication attacks and potential security incidents. |The Microsoft Sentinel connector centralizes Event Hubs security events with other Azure resources for threat detection. UEBA detects abnormal access patterns that static rules miss.<br><br>The *RequestsAuthenticationError* metric alert notifies you of authentication attacks in real time so that you can respond to incidents and mitigate security threats. |
| Add the [Azure Policy compliance check task](/azure/devops/pipelines/tasks/reference/azure-policy-check-gate-v0) in Azure Pipelines and scan Bicep or ARM templates by using the Microsoft Security DevOps extension before deployment. Configure pipeline environment approval to require zero policy violations.<br><br>Submit a [penetration testing notification](/azure/security/fundamentals/pen-testing) in the Azure portal before you do penetration testing. | Policy compliance check prevents deployment of misconfigured resources. The Security DevOps extension detects common IaC security problems before deployment.<br><br>The portal notification ensures compliance with Microsoft penetration testing requirements and aligns with Azure security policies during security assessments. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure Event Hubs and its environment.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design to align the workload with the allocated budget. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Create and maintain a cost model for Event Hubs workloads:** Assess throughput unit or processing unit requirements based on peak and average traffic patterns for accurate capacity planning. Include auto-inflate costs and an analysis of service tiers—Basic, Standard, Premium, and Dedicated—in your cost estimates.
>
>    Calculate message retention storage costs based on retention periods and message volumes. Include Event Hubs Capture costs for operation and destination storage. Factor in schema registry costs, cross-region data transfer costs, and disaster recovery expenses for secondary regions.
>
> - **Implement cost monitoring and budget alerting for Event Hubs resources:** Configure Microsoft Cost Management for granular cost tracking and analysis. Apply resource tagging for accurate cost allocation across workloads and business units. Activate cost anomaly detection to flag unexpected spending patterns and monitor throughput unit utilization.
>
>    Track message retention storage costs and growth trends, monitor Capture costs and destination storage expenses, and set budget alerts for gradual increases and sudden spikes. Maintain visibility into cross-region data transfer charges.
>
> - **Optimize Event Hubs capacity planning and autoscaling configuration for cost efficiency:** Analyze traffic patterns to determine optimal throughput unit or processing unit allocation. Monitor partition utilization for optimization opportunities. Plan capacity for steady-state and peak demand with appropriate buffers for cost-effective performance.
>
>    Set auto-inflate maximum limits in Standard tier deployments to prevent unexpected cost increases. Fine-tune partition count for efficient throughput utilization and consumer scaling. Use load testing to validate capacity assumptions.
>
>    Evaluate tier cost efficiency by comparing service tier cost and performance trade-offs based on workload characteristics. Consider the Dedicated tier for high-volume, predictable workloads where consistent performance provides better total cost of ownership.
>
> - **Establish cost guardrails and governance policies for Event Hubs deployments:** Create Azure Policy definitions that enforce throughput unit limits and restrict service tier choices. Define retention period policies to prevent long‑term storage cost growth.
>
>    Set up approval workflows for high-capacity deployments and tier upgrades. Allow policy exemptions for legitimate requirements. Build governance processes that include regular reviews of resource utilization and documented procedures to help you plan capacity.
>
> - **Optimize development and testing environments to reduce Event Hubs costs:** Use the Basic or Standard tier with minimal throughput units and shorter retention periods for development and testing scenarios. Use event simulation and synthetic data to reduce traffic costs. Maintain realistic testing capabilities. Separate budgets for development environments.
>
>    Implement automated life cycle management to schedule shutdowns during non-business hours. Establish resource sharing by sharing namespaces for multiple projects when security permits. This approach improves cost efficiency without compromising productivity.
>
> - **Automate cost optimization and continuous improvement processes:** Automate throughput unit adjustments based on traffic patterns, and automate retention period adjustments based on data access patterns. Consolidate underutilized resources automatically to improve overall utilization.
>
>    Use Azure Automation to orchestrate routine tasks and life cycle management processes. Integrate Azure Advisor cost optimization recommendations.
>
> - **Consolidate and optimize Event Hubs resource utilization across workloads:** Assess whether workloads can safely share namespaces and resources, and confirm that their traffic patterns complement each other.
>
>    Implement multi-tenant architectures with clear isolation boundaries, and separate consumer groups to isolate workloads within shared Event Hubs. Consolidate Capture destinations to optimize storage costs and management overhead.
>
>    Establish cost allocation through chargeback mechanisms for shared resources. Create policies for workload onboarding approvals.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Configure [Cost Management](/azure/cost-management-billing/costs/quick-acm-cost-analysis) for granular cost tracking and analysis. Create [budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) with 50%, 75%, 90%, and 100% alert thresholds for multi-stage cost control warnings.<br><br>Set up automated cost reports on weekly and monthly schedules for consistent cost visibility and trend analysis. | Enables accurate cost allocation and chargeback across workloads, and provides early warnings for cost overruns through multi-level alerting. |
| Select the right [service tier](/azure/event-hubs/compare-tiers) (Basic, Standard, Premium, or Dedicated) based on economic analysis.<br><br>Configure [auto-inflate](/azure/event-hubs/event-hubs-auto-inflate) with maximum throughput unit limits based on budget constraints.| Matching the tier to workload needs optimizes costs. Setting a maximum for auto-inflate prevents unexpected cost increases.|
| Configure [retention periods](/azure/event-hubs/event-hubs-features#event-retention), like 1-7 days for the Standard tier or up to 90 days for the Dedicated tier. Align these periods with data access patterns and compliance requirements.<br><br>Enable [Event Hubs Capture](/azure/event-hubs/event-hubs-capture-overview) to use Azure Blob Storage or Azure Data Lake Store for long-term storage. Choose Cool or Archive storage tiers for captured data that you rarely access.| Reduces storage costs through optimized retention policies and provides affordable long-term archival options. |
| Deploy [Azure Policy definitions](/azure/governance/policy/samples/built-in-policies#event-hubs) that set throughput-unit caps and restrict allowed tiers to enforce cost controls.<br><br>Set up a policy-exemption approval workflow for controlled flexibility. | Provides automated cost governance through policy-based controls and reduces cost-overrun risks. Allows controlled exceptions for legitimate business requirements through structured approval processes. |
| Create [Automation](/azure/automation/automation-intro) runbooks to shut down lower environments on weeknights and weekends to reduce runtime costs. Use Azure Functions to configure auto-start triggers during development hours.<br><br>Apply [environment tags](/azure/azure-resource-manager/management/tag-resources) for automation targeting and cost allocation. | Reduces development and testing costs through automated lifecycle management and provides appropriate functionality for development workloads at lower costs. |
| Configure [Azure Advisor cost recommendations](/azure/advisor/advisor-cost-recommendations) with Event Hubs filters to identify optimization opportunities automatically. To systematically improve costs, use Azure Logic Apps to process Advisor recommendations automatically.<br><br>Use Azure Functions to adjust throughput units based on alert triggers for automated cost optimization. | Provides continuous cost optimization through automated analysis and adjustment.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**. The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure Event Hubs.

> [!div class="checklist"]
>
> - **Apply safe deployment practices for configuration changes:** Implement blue-green deployments for namespace-level changes. Use canary deployments for consumer applications through isolated consumer groups.
>
>    Monitor partition health during deployments, consumer lag trends, and throughput metrics to ensure deployment success. Enable rolling updates so that consumers move to new versions gradually without downtime.
>
> - **Design infrastructure as code (IaC) strategies for resource management:** Use ARM templates, Bicep, or Terraform based on your needs for Event Hubs resource property coverage and dependency management.
>
>    Separate namespace-level configurations like pricing tier, auto-inflate settings, and network security from event hub-specific settings like partition count, retention periods, and consumer groups. Add parameters for tier-specific variations like throughput unit limits and retention periods. 
>
>    Implement drift detection for streaming-critical settings, like partition counts. Set automated remediation processes for throughput unit changes and consumer group life cycle tasks.
>
> - **Establish build and deployment pipelines for workloads:** Create continuous integration and continuous deployment (CI/CD) pipelines that deploy Event Hubs infrastructure, applications, and schemas.
>
>    Treat deployment staging as a required step. Deploy infrastructure in separate stages, and then deploy consumer applications with correct sequencing and schema management.
>
> - **Implement monitoring and logging for operations:** Monitor metrics that address streaming characteristics, like partition utilization, consumer lag, throughput rates, and namespace capacity utilization.
>
>    Structured logging helps you troubleshoot and support operations. Add correlation IDs to follow the entire process of events from producers to consumers.
>
>    Build custom dashboards to view partition health and monitor consumer group performance. Configure alerting thresholds for consumer lag and throughput unit exhaustion for proactive response.
>
> - **Design operations and emergency response procedures:** Create operational procedures for consumer group management, partition rebalancing, throughput unit scaling, and retention policy adjustments.
>
>    Design incident response procedures to address specific scenarios including partition unavailability, consumer lag accumulation, and throughput throttling.
>
>    Plan geo-disaster recovery for primary region failure, coordinate consumer application failover, and test procedures regularly to ensure effectiveness.
>
> - **Automate management, monitoring, and support tasks:** Automate consumer group creation, partition scaling analysis, throughput unit adjustments, and consumer lag monitoring.
>
>    Use Automation runbooks for routine tasks including access policy rotation, Capture configuration updates, and namespace maintenance. Ensure consistent implementation and reduce the chance of human error.
>
>    Use Azure Policy to detect configuration drift across settings. Set up automated incident response to address consumer lag remediation, throughput scaling, and connection failure recovery.
>
> - **Implement testing and validation strategies for streaming workloads:** Test partition assignment algorithms to ensure balanced load distribution, validate producer batching and partition key effectiveness, and test consumer group scaling behavior under different throughput scenarios. Run end-to-end streaming tests that simulate realistic event volumes and consumer lag patterns.
>
>    Validate Event Hubs configurations including partition counts, throughput unit allocation, and retention period settings early on in development. Test schema evolution compatibility with schema registry, confirm consumer group checkpoint management, and check producer retry logic and consumer error handling patterns before production deployment.

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Create IaC templates in [Bicep](/azure/event-hubs/event-hubs-bicep-namespace-event-hub) or [Terraform](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/eventhub_namespace) that define the Event Hubs namespace, event hub configurations, partition counts, throughput units, and consumer groups by using proper parameters for environment-specific values.| Ensures consistent deployment across environments. Reduces manual configuration errors and enables version-controlled infrastructure changes with automated validation capabilities. |
| Implement multi-stage [CI/CD pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) that isolate stages for infrastructure, consumer applications, and schema deployment, including approval gates for production and automated testing stages. | Orchestrates and coordinates deployments. Enables integrated testing and validation, and provides visibility into the overall functional performance of the pipeline.
| Configure [Azure Monitor](/azure/event-hubs/monitor-event-hubs) through custom dashboards, [diagnostic logging](/azure/event-hubs/monitor-event-hubs-reference) with structured correlation IDs, and [Application Insights integration](/azure/azure-monitor/app/distributed-tracing) for end-to-end transaction tracing across producer and consumer applications. | Provides operational visibility and real-time monitoring capabilities. Supports efficient troubleshooting with distributed tracing, auditing, and anomaly detection. |
| Develop operational runbooks by using [Azure Monitor workbooks](/azure/azure-monitor/visualize/workbooks-overview). Use these runbooks to document consumer group management, throughput scaling steps, partition rebalancing procedures, and interactive troubleshooting steps for consumer lag remediation and connection problems. | Standardizes operational procedures. Speeds up incident response, ensures consistent practices across operational teams, and provides interactive guidance. |
| Implement [Automation](/azure/automation/automation-intro) runbooks for access policy rotation and throughput changes. Deploy [Azure Policy definitions](/azure/governance/policy/samples/built-in-policies#event-hubs) that enforce diagnostic settings, retention periods, and network restrictions.| Reduces manual operational overhead. Prevents configuration drift through automated compliance monitoring and proactive management.|
| Implement [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing) scenarios that simulate realistic producer event rates, consumer patterns, and partition key distribution. Integrate load tests into deployment pipelines for automated performance checks before production promotion. | Confirms performance under realistic load to minimize production problems and enables data-driven capacity planning. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Azure Event Hubs.

> [!div class="checklist"]
>
> - **Plan capacity for Event Hubs workload requirements:** Assess peak ingestion rates, concurrent producers, consumer throughput, and retention needs. Identify a baseline for current streaming patterns and consumer processing capabilities. Consider seasonal variations and expected growth projections.
>
>    Account for Event Hubs scaling options including throughput unit scaling (vertical) and partition-based scaling (horizontal). Consider downstream dependencies that can create bottlenecks in the streaming architecture.
>
>    To confirm capacity estimates, do load testing and use realistic volumes and patterns. Monitor and adjust configurations based on actual usage metrics for optimal performance over time.
>
> - **Choose your Event Hubs tier and SKU based on performance needs:** Choose the tier that aligns with your performance requirements including ingestion rates, partition limits, and retention periods. Also consider integration needs like Kafka protocol compatibility, schema registry functionality, and Stream Analytics connectivity.
>
>    - The Standard tier supports up to 32 partitions and 1-7 day retention for moderate throughput scenarios.
>      
>    - The Premium tier provides enhanced networking, customer-managed keys, and improved SLA guarantees.
>    - The Dedicated tier delivers enterprise-grade capabilities with up to 1,024 partitions and 90‑day retention for high‑throughput scenarios.
>
> - **Design your Event Hubs scaling strategy for optimal performance:** Select throughput unit scaling (vertical) or partition scaling (horizontal) based on workload characteristics. Use ingestion rate, consumer lag, and partition utilization as key scaling triggers.
>
>    Implement partition-based scaling to increase consumer parallelism. Avoid publishing events to individual partitions and implement downstream ordering if needed. Configure auto-inflation for throughput units and establish consumer group scaling patterns.
>
>    Design stateless consumers with well-distributed partition keys and balanced load distribution. Test scaling behavior under burst traffic, sustained load, and consumer lag recovery scenarios to check effectiveness.
>
> - **Implement Event Hubs performance monitoring:** Establish baseline ingestion rates, consumer lag, throughput unit utilization, and partition distribution. Collect key metrics including ingress and egress throughput, consumer lag per partition, and connection metrics. Implement distributed tracing for end-to-end visibility.
>
>    Analyze trends to identify patterns, seasonal variations, and signs of performance degradation. Configure alerts for throughput reductions, rising consumer lag, and uneven partition utilization.
>
> - **Conduct Event Hubs performance testing:** Test how Event Hubs responds to realistic workload patterns like burst ingestion, sustained throughput, and consumer lag scenarios. Test both producers and consumers by using realistic event payloads that match production characteristics.
>
>    Establish baselines for ingestion rates, consumer throughput, and end-to-end latency during testing. Validate auto-inflation effectiveness and consumer scaling behavior under diverse load conditions to ensure that scaling mechanisms function as expected. Analyze throughput metrics, consumer lag patterns, and resource utilization, then validate against the baselines.
>
> - **Optimize Event Hubs performance through configuration and design patterns:** Tune connection pooling, batch sizes, compression, and retry policies for optimal throughput.
>
>    Optimize partition key design, batching strategies, and consumer group organization for balanced throughput distribution. Utilize AMQP protocol optimization, Kafka compatibility, and schema registry integration to enhance serialization efficiency.
>
>    Choose Azure regions close to producers and consumers. Implement geo-replication for distributed scenarios. Apply architectural patterns like event sourcing, CQRS, and publish-subscribe for optimal performance.

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Set initial [throughput units](/azure/event-hubs/event-hubs-auto-inflate) based on capacity planning estimates for Standard and Premium tier namespaces and enable [auto-inflation](/azure/event-hubs/event-hubs-auto-inflate). Configure maximum throughput unit limits for cost control and set utilization threshold triggers at 80-90% utilization.<br><br>Test scaling behavior under diverse load scenarios to confirm that automated scaling works as expected.| Automatic scaling removes manual intervention during traffic spikes and optimizes costs based on demand. <br><br> Setting maximum throughput limits prevent unexpected cost overruns during peak usage.|
| Configure [capacity unit scaling](/azure/event-hubs/event-hubs-dedicated-cluster-create-portal#scale-a-dedicated-cluster) for Dedicated tier clusters based on workload performance testing and resource utilization monitoring. Enable the **Support scaling** option when you create the cluster so that you can adjust scaling.| Dedicated cluster scaling provides predictable enterprise-grade performance through pre-allocated CPU and memory resources.|
| Implement the [event processor host](/azure/event-hubs/event-hubs-programming-guide#event-processor-host) or [Azure Functions](/azure/azure-functions/functions-bindings-event-hubs) to manage partitions automatically and avoid manual assignment. Configure prefetch settings to improve throughput.<br><br>Monitor consumer lag metrics per partition to identify processing bottlenecks. | Automatic partition distribution across consumer instances simplifies scaling and removes manual intervention.<br><br>Consumer lag visibility identifies processing bottlenecks for targeted optimization efforts. |
| Enable [diagnostic settings](/azure/event-hubs/monitor-event-hubs) for namespace and event hub resources to capture metrics and logs. Create [Azure Monitor workbooks](/azure/azure-monitor/visualize/workbooks-create-workbook) for performance dashboards and configure alerts for consumer lag and throughput thresholds.<br><br>Monitor throughput utilization and auto-inflation events to validate scaling behavior and capacity management effectiveness. | Threshold-based alerts proactively detect problems and prevent performance degradation. Historical analysis supports capacity planning.<br><br>Centralized dashboards provide operational visibility. |
| Integrate the [Application Insights](/azure/azure-monitor/app/distributed-tracing) SDK in producer and consumer applications to collect telemetry. Configure correlation IDs so that you can trace events end to end and measure latency across downstream dependencies.| End-to-end visibility across the event streaming pipeline makes it easier to analyze performance and spot anomalies automatically. Correlation tracking accelerates troubleshooting. |
| Use [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing) or custom frameworks to simulate realistic production-style workloads. Implement test scenarios that include producers, consumers, and failure conditions.<br><br>Monitor performance metrics during each test run and document results, baselines, and optimal configuration settings for future reference. | Validated capacity limits and scaling behavior increase confidence in production deployment.<br><br>Established performance baselines make it easier to plan capacity and fine‑tune the environment over time.|
| Configure batch sizes, prefetch values, and retry policies in [SDK settings](/azure/event-hubs/event-hubs-programming-guide) to improve producer and consumer performance. Use connection pooling to reduce overhead and enable compression with [schema registry](/azure/event-hubs/schema-registry-overview) integration. | Batching and prefetch tuning optimizes throughput. Connection pooling and compression reduce network overhead. <br><br>Data-driven optimization through metrics-based tuning enables continuous performance improvements and efficient resource utilization. |
| Configure dedicated consumer groups for [Stream Analytics](/azure/stream-analytics/stream-analytics-define-inputs#event-hubs) and [Azure Functions](/azure/azure-functions/functions-bindings-event-hubs) to optimize downstream processing. Add caching for event metadata and processing results. Tune checkpoint frequency based on processing patterns.| Dedicated consumer groups optimize downstream processing. Caching reduces redundant processing and improves efficiency. <br><br>Optimized checkpointing balances resilience and performance requirements.|

## Azure policies

Azure provides a broad set of built-in policies related to Azure Event Hubs and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Event Hubs namespace uses a customer-managed key for encryption
- Event Hubs namespaces have double encryption enabled
- Resource logs in Event Hubs are enabled

For effective governance, review the [Azure Policy built-in definitions for Event Hubs](/azure/governance/policy/samples/built-in-policies#event-hubs) and other policies that might affect the security of the messaging infrastructure.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [Stream processing with Azure Stream Analytics](/azure/architecture/reference-architectures/data/stream-processing-stream-analytics).

## Related content

- [Event Hubs overview](/azure/event-hubs/event-hubs-about)
- [Event Hubs monitoring](/azure/event-hubs/monitor-event-hubs)
- [Event Hubs quickstart for .NET](/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)
