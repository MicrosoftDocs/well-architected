---
title: "Architecture Best Practices for Azure Event Hubs"
description: "Explore design considerations and recommendations for Azure Event Hubs to optimize reliability, security, cost, operational excellence, and performance efficiency."
author: PageWriter-MSFT
ms.author: prwilk
ms.topic: conceptual
ms.date: 11/20/2024
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

Azure Event Hubs is a real-time data streaming platform and event ingestion service capable of receiving and processing millions of events per second. It provides a unified streaming platform with time-retention buffer, decoupling event producers from event consumers. The service offers native integration with Azure analytics services and supports multiple protocols including AMQP, Apache Kafka, and HTTPS for flexible event streaming architectures.

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
> - **Review quotas and limits that might pose design restrictions:** Analyze [quotas and limits](/azure/event-hubs/event-hubs-quotas) that significantly impact architecture decisions across key dimensions. Plan for partition limits that vary dramatically between service tiers for large-scale scenarios.
>
>    Calculate throughput units or processing units that determine message ingestion capacity and establish scaling boundaries for your workload. Design payloads within maximum message size constraints of 1 MB for large event scenarios.
>
>    Select appropriate message retention periods that vary by service tier, considering recovery and replay requirements. Plan application architecture around consumer group limits per namespace that depend on your selected tier.
>
> - **Anticipate potential failures through failure mode analysis:** Failure mode analysis provides a systematic approach to anticipating potential failure scenarios and developing mitigation strategies to ensure messaging service resilience.
>
>    | Failure | Mitigation |
>    |---------|------------|
>    | Namespace unavailable | Implement monitoring and alerting for early detection (see monitoring consideration). Plan for cross-region failover capabilities. |
>    | Partition unavailable | Design partition distribution strategies to prevent single points of failure. Implement retry logic with exponential backoff in producers and consumers. |
>    | High message volumes exceed throughput limits | Plan for capacity scaling to handle traffic spikes. Implement client-side throttling protection with circuit breaker patterns. |
>
> - **Define reliability targets for event streaming workloads:** Establish clear reliability targets to guide architecture decisions and operational practices. Define Service Level Objectives (SLOs) that encompass message ingestion availability, consumer processing latency, and end-to-end delivery performance while implementing Service Level Indicators (SLIs) that provide measurable metrics.
>
>    Set availability targets that align with SLA guarantees of 99.9% for Standard tier and 99.99% for Dedicated tier. Evaluate retention period requirements that directly impact recovery objectives and influence tier selection decisions for your workload.
>
> - **Implement redundancy strategies to eliminate single points of failure:** Deploy layered redundancy to eliminate single points of failure. Enable zone-level redundancy in Premium and Dedicated tiers for automatic replication across availability zones with transparent failover within the same region.
>
>   Implement geo-replication for Dedicated tier namespaces to enable real-time asynchronous replication of events and metadata across multiple regions, providing seamless regional failover capabilities.
>
>    Implement partition and application-level redundancy with effective partition key distribution to prevent hotspots. Deploy producer and consumer redundancy patterns to ensure continued operation when instances fail, while implementing storage account redundancy to protect checkpointing data essential for consumer state management.
>
> - **Design for reliable scaling to handle variable workloads:** Implement scaling capabilities at distinct levels to handle variable workloads reliably. Configure infrastructure scaling through auto-inflate in Standard and Premium tiers to automatically scale throughput units during traffic spikes, while using Dedicated tier for predictable performance with reserved capacity.
>
>    Plan partition scaling carefully since partition count cannot be decreased after creation. Implement application-level scaling with producer patterns that distribute load across partitions using appropriate partition keys and align consumer group instances with partition count for optimal throughput.
>
> - **Implement monitoring and alerting for reliability:** Configure Azure Monitor to collect metrics including ingestion metrics for message volume and throughput utilization, consumer lag tracking to identify processing delays, and infrastructure metrics for namespace health.
>
>    Configure thresholds aligned with SLOs and business impact severity while implementing multi-dimensional metrics for granular monitoring across namespace, event hub, and partition levels.
>
> - **Configure Event Hubs for self-preservation and graceful degradation:** Implement self-preservation patterns to protect workloads from cascading failures and enable graceful degradation. Since built-in dead-letter queues are unavailable, implement custom patterns for poison message handling and configure circuit breaker patterns for traffic protection during recovery.
>
>    Implement consumer isolation through separate consumer groups with only one active receiver per group. Configure SDK exception handling to properly catch EventHubsException or OperationCancelledException from retry policies. Enable the Capture feature to provide durable storage for event replay and recovery scenarios.
>
> - **Implement reliability testing to validate resilience:** Conduct reliability testing to validate resilience through systematic failure simulation and performance validation. Execute load testing to validate performance under expected and peak traffic conditions and implement consumer lag testing to confirm processing keeps pace with ingestion rates.
>
>    Execute resilience testing through chaos engineering with intentional failure introduction, implement failover testing to verify geo-disaster recovery and zone redundancy capabilities, and conduct dependency failure testing for Azure Storage and other service dependencies.
>
> - **Design disaster recovery strategy for business continuity:** Define Recovery Time Objective (RTO) and Recovery Point Objective (RPO) based on business requirements, then select DR patterns from active-passive for cost optimization to active-active for minimal RTO.
>
>    Use geo-disaster recovery for Standard and Premium tiers to enable metadata replication between paired namespaces across regions, providing automated failover capabilities during regional outages. Implement client-side failover capabilities for faster recovery and evaluate data consistency requirements for the chosen pattern.
>
>    Create operational continuity plans that include documented backup procedures for metadata and consumer offsets, execution runbooks, communication plans, and scheduled regular DR testing.
>

### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Enable [availbility zone redundancy](/azure/reliability/reliability-event-hubs#availability-zone-support) during namespace creation by selecting an Azure region with availability zone support. | Provides automatic datacenter-level failure protection with transparent failover and minimal recovery time, without requiring application changes. |
| Enable [geo-replication](/azure/event-hubs/geo-replication) for Dedicated tier namespaces to replicate events and metadata across multiple regions in real-time.<br><br>Configure primary and secondary regions with appropriate network connectivity and monitor replication lag metrics to ensure data consistency across regions. | Enables seamless regional failover capabilities and minimal data loss during regional outages.|
| Setup [geo-disaster recovery](/azure/event-hubs/event-hubs-geo-dr) pairing between primary and secondary namespaces, then configure an alias for the namespace pair. Update connection strings to use the alias for transparent failover capabilities. | Enables cross-region failover capability with transparent client failover using the alias connection. This configuration ensures business continuity during regional outages. |
| Implement [hash-based partition keys](/dotnet/api/azure.messaging.eventhubs.producer.sendeventoptions.partitionkey) for event distribution and set appropriate partition count during event hub creation. Monitor partition-level metrics for hotspot detection and load balancing validation. | Prevents partition hotspots while enabling horizontal scaling through balanced load distribution across partitions. |
| Enable [auto-inflate](/azure/event-hubs/event-hubs-auto-inflate) for Standard tier namespaces and set maximum throughput units based on peak load projections. Use Premium tier for sustained high throughput workloads requiring predictable performance. | Auto-inflate prevents ingestion throttling through automatic capacity scaling, ensuring consistent availability during load variations without manual intervention. |
| Configure [Event Hubs alerts in Azure Monitor](/azure/azure-monitor/essentials/metrics-supported#microsofteventhubnamespaces) for consumer lag, throughput utilization, and throttling events with thresholds aligned to SLOs<br><br> Enable alerts for connection and authentication failures, then create alert action groups for incident response. | Enables early issue detection and reduced incident response time and provides capacity planning insights through monitoring coverage. |

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
> - **Implement segmentation strategies to isolate Event Hubs resources and control access boundaries:** Design distinct isolation dimensions for Event Hubs segmentation. Leverage Microsoft Entra ID integration with RBAC to create logical access control boundaries for identity-based segmentation.
>
>    Establish network segmentation through virtual network integration and private connectivity options that isolate Event Hubs traffic from public internet exposure. Configure namespace-level isolation for environment and tenant boundaries.
>
> - **Configure identity and access management for Event Hubs workloads:** Use Microsoft Entra ID as the trusted identity provider for centralized authentication and authorization in Event Hubs workloads.
>
>    Configure role-based authorization for granular access controls through SendOnly and ListenOnly policies that separate publisher and consumer permissions. Enable conditional access policies for context-aware security based on device compliance, network location, and user context.
>
>    Establish separate role assignments for administrative and high-privilege identities with just-in-time access capabilities while leveraging managed identities to eliminate credential management overhead for service-to-service communication patterns.
>
> - **Implement network security controls to protect Event Hubs traffic and connectivity:** Deploy layered network security controls for Event Hubs including private connectivity options and network access filtering. Configure private endpoints to eliminate internet exposure. Enable service endpoints to provide secure VNet connectivity without public routing.
>
>    Set up network security groups to provide subnet-level traffic filtering and flow control while configuring IP firewall rules for allowlist-based access from specific IP addresses and CIDR ranges.
>
> - **Configure data encryption for Event Hubs event streams and metadata:** Apply end-to-end data encryption for both event data and metadata in Event Hubs. Enable customer-managed keys for enhanced encryption control and regulatory compliance. Use double encryption for additional protection in high-assurance scenarios. Set TLS 1.2 minimum configuration for secure client connections and inter-service communication.
>
>    Establish key rotation strategies with operational procedures for customer-managed keys. Deploy Managed HSM for high-security key storage for organizations with stringent security requirements.
>
> - **Harden Event Hubs namespace and entity configurations to reduce attack surface:** Focus Event Hubs namespace and entity hardening on attack surface reduction through feature minimization. Disable unused features and protocols to eliminate potential attack vectors. Turn off auto-inflate when not required for capacity management.
>
>    Remove unused consumer groups and event hubs and limit enabled capabilities to only those required for specific workload functionality.
>
> - **Secure Event Hubs connection strings and access keys through proper secret management:** Use managed identities as the preferred authentication method to eliminate credential storage requirements. When connection strings or SAS tokens are necessary, apply proper secret management techniques.
 for Event Hubs connection strings and access keys.
>
>    Utilize Azure Key Vault for secure credential storage and configure separate keys for producers and consumers to enforce least privilege principles. Maintain distinct access keys for different consumers and environments to enable granular revocation capabilities, and deploy short-lived SAS tokens to reduce security exposure.
>
> - **Implement security monitoring and logging for Event Hubs operations:** Establish centralized security monitoring for Event Hubs with logging and data collection across operational dimensions. Configure diagnostic logging to capture authentication events, data plane access patterns, and configuration changes. Set up identity and access monitoring to track failed authentication attempts and privilege escalation events.
>
>    Enable network access pattern monitoring to detect unauthorized access through connection source analysis. Maintain logging for configuration changes and administrative operations for compliance and security analysis purposes.
>
>    Activate threat detection capabilities for alerts on suspicious activity including unusual access volumes or unknown sources. Monitor consumer group operations and partition access patterns for anomaly detection. Utilize User and Entity Behavior Analytics to identify abnormal usage patterns that may indicate security incidents.
>
> - **Implement security testing and validation processes for Event Hubs configurations and access controls:** Execute systematic security testing and validation for Event Hubs through rigorous assessment of configuration security and access controls. Conduct identity and access control validation that encompasses RBAC assignments and conditional access policy effectiveness while performing network security testing to validate private endpoints, IP filtering, and virtual network integration.
>
>    Verify encryption effectiveness for data protection in both data at rest and in transit scenarios. Deploy automated testing capabilities for continuous security assessment while conducting penetration testing with proper rules of engagement and coordination with Azure security policies.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Use Microsoft Entra ID to [authorize access to Event Hubs](/azure/event-hubs/authorize-access-azure-active-directory) resources. Assign built-in roles including Data Owner, Data Sender, and Data Receiver at appropriate scope levels based on access requirements.<br><br>Scope these assignments at namespace, event hub, or consumer group levels to implement granular access control that aligns with operational needs. | Provides granular access control at the various scope levels, with clear separation between producer, consumer, and administrative operations.|
| Deploy [private endpoints](/azure/event-hubs/private-link-service) within dedicated subnets for network isolation and configure network security groups to control traffic flow to Event Hubs subnets.<br><br> Alternatively, apply [IP firewall rules](/azure/event-hubs/event-hubs-ip-filtering) at namespace level for known source networks and organize namespaces across virtual networks based on security zones. | Eliminates public internet exposure through private connectivity and provides network-level traffic control aligned with organizational security zones.|
| Enable [Entra ID authentication at namespace level](/azure/event-hubs/authenticate-application). Disable SAS authentication at namespace level where managed identities are available.<br><br>Create [conditional access policies](/entra/identity/conditional-access/overview) requiring compliant devices while blocking access from untrusted locations. Require MFA for all users with Azure Event Hubs Data Owner role to enhance administrative security. | Namespace-level enablement centralizes authentication configuration across Event Hubs entities. Disabling SAS eliminates shared credential management overhead.|
| Set [Minimum TLS version to 1.2](/azure/event-hubs/transport-layer-security-enforce-minimum-version) at the namespace scope to prevent exploitation of legacy protocols. Enable diagnostic logging and query for TLS version violations in Log Analytics to identify non-compliant clients. | TLS 1.2 minimum configuration prevents exploitation of legacy SSL/TLS vulnerabilities that could compromise data in transit. |
| Create an RSA 2048 key in Key Vault and assign the Key Vault Crypto Service Encryption User role to the namespace managed identity. Configure the namespace Encryption settings to [customer-managed key](/azure/event-hubs/configure-customer-managed-key) and set a rotation policy to 90 days in Key Vault.<br><br>Enable [double encryption](/azure/event-hubs/event-hubs-premium-overview#encryption) during namespace creation as this capability cannot be added after creation.| CMK configuration provides regulatory compliance for data sovereignty requirements while 90-day rotation policy balances security with operational stability. Double encryption at creation time provides additional protection layer that cannot be added after namespace creation.|
| Assign the [Configure Event Hubs to use private DNS zones policy](/azure/governance/policy/samples/built-in-policies#event-hubs) at subscription scope for consistent configuration enforcement.<br><br>Create an Activity Log alert rule for Microsoft.EventHub write operations with Action Group notification and review Defender for Cloud recommendations for Event Hubs namespaces weekly. | Policy assignment at subscription scope ensures consistent configuration across namespaces. <br><br>Weekly Defender reviews surface actionable security improvements for continuous security posture enhancement.|
| Store connection strings in [Key Vault](/azure/key-vault/general/overview) with expiration date set to 90 days and assign Key Vault Secrets User role to application managed identity. If [SAS tokens](/azure/event-hubs/authorize-access-shared-access-signature) are needed, generate entity-level tokens with 1 hour expiry time, using Send or Listen permissions only. | 90-day expiration enforces regular credential rotation while managed identity access to Key Vault eliminates secondary credential storage requirements. <br><br>1-hour SAS token expiry limits credential exposure window. Send/Listen-only permissions reduce blast radius in case of a token compromise situation.|
| Send [diagnostic logs](/azure/event-hubs/event-hubs-diagnostic-logs) to Log Analytics workspace and add Event Hubs data connector in [Azure Sentinel](/azure/sentinel/data-connectors-reference) under Data connectors for advanced threat detection.<br><br>Create an alert for the RequestsAuthenticationError metric with threshold greater than 10 per hour to detect authentication attacks and potential security incidents. |The Azure Sentinel connector centralizes Event Hubs security events with other Azure resources for threat detection. UEBA detects abnormal access patterns that static rules miss.<br><br>The RequestsAuthenticationError metric alert provides real-time notification of authentication attacks, enabling rapid incident response and security threat mitigation. |
| Add the [Azure Policy compliance check task](/azure/devops/pipelines/tasks/reference/azure-policy-check-gate-v0) in Azure Pipelines and scan Bicep/ARM templates using Microsoft Security DevOps extension before deployment. Configure pipeline environment approval requiring zero Policy violations.<br><br>Submit [pen test notification](/azure/security/fundamentals/pen-testing) in Azure portal under Pen Test Notification before conducting penetration testing. | Policy compliance check prevents deployment of misconfigured resources while Security DevOps extension detects common IaC security issues before deployment.<br><br>Portal notification ensures compliance with Microsoft pen testing requirements, maintaining proper coordination with Azure security policies during security assessments. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure Event Hubs and its environment.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design to align the workload with the allocated budget. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Create and maintain a cost model for Event Hubs workloads:** Assess throughput unit or processing unit requirements based on peak and average traffic patterns to establish accurate capacity planning. Build detailed cost modeling that includes analysis of service tiers (Basic, Standard, Premium, Dedicated) with consideration of auto-inflate costs.
>
>    Calculate message retention storage costs based on retention periods and message volumes, including Event Hubs Capture costs for operation and destination storage. Factor in Schema Registry costs, cross-region data transfer costs, and disaster recovery expenses for secondary regions.
>
> - **Implement cost monitoring and budget alerting for Event Hubs resources:** Configure Azure Cost Management for granular cost tracking and analysis. Apply resource tagging for accurate cost allocation across workloads and business units. Activate cost anomaly detection to identify unexpected spending patterns and monitor throughput unit utilization.
>
>    Track message retention storage costs and growth trends, monitor Capture costs and destination storage expenses, and establish budget alerts for gradual increases and sudden spikes. Maintain cross-region data transfer cost tracking for accurate cost visibility.
>
> - **Optimize Event Hubs capacity planning and auto-scaling configuration for cost efficiency:** Analyze traffic patterns to determine optimal throughput unit or processing unit allocation. Monitor partition utilization for optimization opportunities. Plan capacity for steady-state and peak demand with appropriate buffers for cost-effective performance.
>
>    Set scaling controls through auto-inflate maximum limits to prevent unexpected cost increases in Standard tier deployments. Fine-tune partition count for efficient throughput utilization and consumer scaling, using load testing to validate capacity assumptions.
>
>    Evaluate tier cost efficiency by comparing service tier cost-performance trade-offs based on workload characteristics. Consider dedicated tier for high-volume, predictable workloads where consistent performance provides better total cost of ownership.
>
> - **Establish cost guardrails and governance policies for Event Hubs deployments:** Deploy targeted policies and controls by creating Azure Policy definitions for throughput unit limits and service tier restrictions. Define retention period policies to prevent excessive storage costs that can accumulate over time without proper governance oversight.
>
>    Set up approval workflows for high-capacity deployments and tier upgrades while allowing policy exemptions for legitimate requirements. Build governance processes with regular reviews for resource utilization and documented procedures for capacity planning decisions.
>
> - **Optimize development and testing environments for reduced Event Hubs costs:** Configure development environment optimization with Basic or Standard tier using minimal throughput units and shorter retention periods for testing scenarios. Use event simulation and synthetic data to reduce traffic costs. Maintain realistic testing capabilities.
>
>    Implement automated lifecycle management through scheduled shutdown during non-business hours and separate budgets for development environments. Establish resource sharing by sharing namespaces for multiple projects when security permits, enabling cost efficiency without compromising productivity.
>
> - **Implement cost optimization automation and continuous improvement processes:** Automate throughput unit adjustments based on traffic patterns and retention period adjustments based on data access patterns. Consolidate underutilized resources automatically to maintain optimal utilization without manual intervention.
>
>    Configure automation tools using Azure Automation for scheduled tasks and lifecycle management and integrate Azure Advisor cost optimization recommendations.
>
> - **Consolidate and optimize Event Hubs resource utilization across workloads:** Identify consolidation opportunities by assessing workload compatibility for namespace and resource sharing, and complementary traffic patterns.
>
>    Implement multi-tenant architectures with appropriate isolation boundaries using consumer group separation for workload isolation within shared Event Hubs. Consolidate Capture destinations to optimize storage costs and management overhead.
>
>    Establish cost allocation through chargeback mechanisms for shared resources and policies for workload onboarding approvals.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Configure [Azure Cost Management](/azure/cost-management-billing/costs/quick-acm-cost-analysis) with for granular cost tracking and analysis. Create [budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) with 50%, 75%, 90%, and 100% alert thresholds for multi-stage cost control warnings.<br><br>Set up automated cost reports on weekly and monthly schedules for consistent cost visibility and trend analysis | Enables accurate cost allocation and chargeback across workloads and provides early warning of cost overruns through multi-level alerting. |
| Select appropriate [service tier](/azure/event-hubs/compare-tiers) (Basic, Standard, Premium, or Dedicated) based on economic analysis.<br><br>Configure [auto-inflate](/azure/event-hubs/event-hubs-auto-inflate) with maximum throughput unit limits based on budget constraints to prevent unexpected scaling costs.| Appropriate tier selection for workload characteristics optimizes costs, while setting a maximum for auto-inflate prevents unexpected cost increases.|
| Configure [retention periods](/azure/event-hubs/event-hubs-features#event-retention) (1-7 days for Standard, up to 90 days for Dedicated) based on data access patterns and compliance requirements.<br><br>Enable [Event Hubs Capture](/azure/event-hubs/event-hubs-capture-overview) to Azure Blob Storage or Azure Data Lake Store for long-term archival. Select Cool or Archive storage tiers for captured data with infrequent access patterns.| Reduces storage costs through optimized retention policies and provides cost-effective long-term archival capabilities. |
| Deploy [Azure Policy definitions](/azure/governance/policy/samples/built-in-policies#event-hubs) with throughput unit caps and allowed tier restrictions to enforce organizational cost controls.<br><br>Set up policy exemption approval workflow for controlled flexibility. | Provides automated cost governance through policy-based controls and reduces cost overrun risks. Enables controlled exceptions for legitimate business requirements through structured approval processes. |
| Create [Azure Automation](/azure/automation/automation-intro) runbooks for scheduled shutdown of lower environments (weeknights, weekends) to reduce runtime costs. Configure auto-start triggers for development hours using Azure Functions.<br><br>Apply [environment tags](/azure/azure-resource-manager/management/tag-resources) for automation targeting and cost allocation. | Reduces development and testing costs through automated lifecycle management and provides appropriate functionality for development workloads at lower costs. |
| Configure [Azure Advisor cost recommendations](/azure/advisor/advisor-cost-recommendations) with Event Hubs filters to identify optimization opportunities automatically. Create Azure Logic Apps to process Advisor recommendations automatically for systematic cost improvements.<br><br>Azure Functions can adjust throughput units based on alert triggers for automated cost optimization. | Provides continuous cost optimization through automated analysis and adjustment.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**. The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure Event Hubs.

> [!div class="checklist"]
>
> - **Implement safe deployment practices for configuration changes:** Implement blue-green deployments for namespace-level changes while using canary deployments for consumer applications through isolated consumer groups.
>
>    Establish monitoring for partition health during deployments, consumer lag trends, and throughput metrics to ensure deployment success. Enable rolling updates for gradual consumer version transitions without service disruption.
>
> - **Design Infrastructure as Code strategies for resource management:** Choose appropriate IaC tools that provide complete support. ARM templates, Bicep, or Terraform offer different capabilities for resource property coverage and dependency management.
>
>    Separate namespace configurations from event hub-specific settings while implementing parameterization for environment-specific variations. Implement drift detection for settings and establish automated remediation processes to address configuration inconsistencies.
>
> - **Establish build and deployment pipelines for workloads:** Design specialized CI/CD pipelines that coordinate deployment of infrastructure, applications, and schemas.
>
>    Make deployment staging critical for success. Coordinate infrastructure deployment in separate stages, followed by consumer application deployment with proper sequencing and schema management.
>
> - **Implement monitoring and logging for operations:** Monitor metrics that address streaming characteristics, like partition utilization, consumer lag, throughput rates, and namespace capacity utilization.
>
>    Structured logging is essential for troubleshooting and operational support. Implement correlation IDs to trace events from producers through consumers, enabling end-to-end observability.
>
>    Build custom dashboards for partition health visualization and consumer group performance monitoring. Configure alerting thresholds for consumer lag and throughput unit exhaustion for proactive response.
>
> - **Design operations and emergency response procedures:** Establish routine operational procedures for consumer group management, partition rebalancing, throughput unit scaling, and retention policy adjustments.
>
>    Design incident response procedures to address specific scenarios including partition unavailability, consumer lag accumulation, and throughput throttling.
>
>    Plan geo-disaster recovery for primary region failure, coordinate consumer application failover, and test procedures regularly to ensure effectiveness.
>
> - **Automate management, monitoring, and support tasks:** Identify opportunities for consumer group creation, partition scaling analysis, throughput unit adjustments, and consumer lag monitoring.
>
>    Use Azure Automation runbooks for routine tasks including access policy rotation, capture configuration updates, and namespace maintenance. Ensure consistent execution while reducing human error potential.
>
>    Enable configuration management through Azure Policy for automated drift detection of settings. Configure automated incident response to address consumer lag remediation, throughput scaling, and connection failure recovery.
>
> - **Implement testing and validation strategies for workloads:** Perform different types of testing including functional testing of partition assignments, producer-consumer workflows, and load testing of throughput scaling behavior.
>
>    Make shift-left testing critical for workloads to validate configurations early in the development cycle. Test schema compatibility, consumer group management, and verify integration patterns before production deployment.

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Create IaC templates in [Bicep](/azure/event-hubs/event-hubs-bicep-namespace-event-hub) or [Terraform](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/eventhub_namespace) that define Event Hubs namespace, event hub configurations, partition counts, throughput units, and consumer groups with proper parameterization for environment-specific values.| Ensures consistent deployment across environments while reducing manual configuration errors and enabling version-controlled infrastructure changes with automated validation capabilities. |
| Implement multi-stage [CI/CD pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) with stage isolation for infrastructure, consumer applications, and schema deployment including approval gates for production and automated testing stages. | Provides deployment orchestration and coordination while enabling integrated testing and validation with visibility into deployment effectiveness across all pipeline stages. |
| Configure [Azure Monitor](/azure/event-hubs/monitor-event-hubs) through custom dashboards, [diagnostic logging](/azure/event-hubs/monitor-event-hubs-reference) with structured correlation IDs, and [Application Insights integration](/azure/azure-monitor/app/distributed-tracing) for end-to-end transaction tracing across producer and consumer applications. | Provides comprehensive operational visibility and real-time monitoring capabilities. Enables effective troubleshooting with audit trail support and distributed tracing and intelligent anomaly detection. |
| Develop operational runbooks using [Azure Monitor Workbooks](/azure/azure-monitor/visualize/workbooks-overview) that document consumer group management, throughput scaling, partition rebalancing procedures, and interactive troubleshooting guides for consumer lag remediation and connection issues. | Standardizes operational procedures while accelerating incident response and ensuring consistent practices across operational teams with interactive guidance capabilities. |
| Implement [Azure Automation](/azure/automation/automation-intro) runbooks for access policy rotation and throughput adjustments. Deploy [Azure Policy definitions](/azure/governance/policy/samples/built-in-policies#event-hubs) that enforce diagnostic settings, retention periods, and network restrictions.| Reduces manual operational overhead while preventing configuration drift through automated compliance monitoring and proactive management.|
| Implement [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing) scenarios that simulate realistic producer event rates, consumer patterns, and partition key distributions. Inteegrate load tests into deployment pipelines for automated performance validation before production promotion. | Validates performance under realistic load to minimize production issues and enables data-driven capacity planning. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Azure Event Hubs.

> [!div class="checklist"]
>
> - **Conduct capacity planning for Event Hubs workload requirements:** Assess peak ingestion rates, concurrent producers, consumer throughput, and retention needs for capacity planning. Baseline current streaming patterns and consumer processing capabilities. Consider seasonal variations and expected growth projections.
>
>    Account for Event Hubs scaling approaches including throughput unit scaling (vertical) and partition-based scaling (horizontal). Consider downstream dependencies that could create bottlenecks in the streaming architecture.
>
>    Validate capacity estimates through load testing with realistic volumes and patterns. Monitor and adjust configurations based on actual usage metrics for optimal performance over time.
>
> - **Choose Event Hubs tier and SKU based on performance requirements:** Select the tier that aligns with performance requirements including ingestion rates, partition limits, and retention periods. Consider integration needs including Kafka protocol compatibility, Schema Registry functionality, and Stream Analytics connectivity in tier selection decisions.
>
>    Standard tier supports up to 32 partitions with 1-7 day retention for moderate throughput scenarios. Premium tier offers enhanced networking, customer-managed keys, and improved SLA guarantees. Dedicated tier delivers enterprise-grade capabilities with up to 1024 partitions, 90-day retention, and high-throughput capacity for demanding workloads.
>
> - **Design Event Hubs scaling strategy for optimal performance:** Choose between throughput unit scaling (vertical) and partition scaling (horizontal) based on workload characteristics. Use ingestion rate, consumer lag, and partition utilization as key scaling triggers.
>
>    Implement partition-based scaling for consumer parallelization. Avoid event publishing to individual partitions and implement ordering downstream if needed. Configure auto-inflation for throughput units and establish consumer group scaling patterns.
>
>    Design stateless consumers with optimal partition key distribution and load balancing. Test scaling behavior under burst traffic, sustained load, and consumer lag recovery scenarios to validate effectiveness.
>
> - **Implement Event Hubs performance monitoring:** Establish baselines for ingestion rates, consumer lag, throughput unit utilization, and partition distribution. Collect key metrics including ingestion/egress throughput, consumer lag per partition, and connection metrics. Implement distributed tracing for end-to-end visibility.
>
>    Analyze trends to identify patterns, seasonal variations, and performance degradation indicators. Configure alerts for throughput degradation, consumer lag thresholds, and partition utilization imbalances.
>
> - **Conduct Event Hubs performance testing:** Validate Event Hubs behavior under realistic workload patterns including burst ingestion, sustained throughput, and consumer lag scenarios. Test both producer and consumer sides using realistic event payloads that represent production characteristics.
>
>    Baseline ingestion rates, consumer throughput, and end-to-end latency during testing. Validate auto-inflation effectiveness and consumer scaling behavior under diverse load conditions for scaling mechanisms to function as expected. Analyze throughput metrics, consumer lag patterns, and resource utilization, then validate against baselines.
>
> - **Optimize Event Hubs performance through configuration and design patterns:** Configure connection pooling, batch sizes, compression, and retry policies for optimal throughput.
>
>    Optimize partition key design, batching strategies, and consumer group organization for balanced throughput distribution. Utilize AMQP protocol optimization, Kafka compatibility, and Schema Registry integration to enhance serialization efficiency.
>
>    Select Azure regions close to producers and consumers while implementing geo-replication for distributed scenarios. Apply architectural patterns including event sourcing, CQRS, and publish-subscribe for optimal performance.

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Set initial [throughput units](/azure/event-hubs/event-hubs-auto-inflate) based on capacity planning estimates for Standard and Premium tier namespaces and enable [auto-inflation](/azure/event-hubs/event-hubs-auto-inflate). Configure maximum throughput unit limits for cost control and set utilization threshold triggers at 80-90% utilization.<br><br>Test scaling behavior under diverse load scenarios to validate automated scaling effectiveness.| Automatic scaling eliminates manual intervention during traffic spikes, optimizing costs based on demand. <br><br> Setting maximum throughput limits prevent unexpected cost overruns during peak usage.|
| Configure [capacity unit scaling](/azure/event-hubs/event-hubs-dedicated-cluster-create-portal#scale-a-dedicated-cluster) for Dedicated tier clusters based on workload performance testing and resource utilization monitoring. Enable the **Support scaling** option during cluster creation to allow self-serve scaling operations.| Dedicated cluster scaling provides predictable enterprise-grade performance through pre-allocated CPU and memory resources.|
| Implement [Event Processor Host](/azure/event-hubs/event-hubs-programming-guide#event-processor-host) or [Azure Functions](/azure/azure-functions/functions-bindings-event-hubs) for automatic partition management that eliminates manual assignment complexity. Configure prefetch settings for throughput optimization.<br><br>Monitor consumer lag metrics per partition to identify processing bottlenecks. | Automatic partition distribution across consumer instances provides seamless scaling without manual intervention.<br><br>Consumer lag visibility identifies processing bottlenecks for targeted optimization efforts. |
| Enable [diagnostic settings](/azure/event-hubs/monitor-event-hubs) for namespace and event hub resources to capture metrics and logs. Create [Azure Monitor workbooks](/azure/azure-monitor/visualize/workbooks-create-workbook) for performance dashboards and configure alerts for consumer lag and throughput thresholds.<br><br>Monitor throughput utilization and auto-inflation events to validate scaling behavior and capacity management effectiveness. | Proactive issue detection through threshold-based alerts prevents performance degradation. Historical analysis supports capacity planning.<br><br>Centralized dashboards provide operational visibility. |
| Integrate [Application Insights](/azure/azure-monitor/app/distributed-tracing) SDK in producer and consumer applications for telemetry collection. Configure correlation IDs for end-to-end transaction tracing and track event flow latency with downstream dependencies.| End-to-end visibility across the event streaming pipeline enables performance analysis. Smart anomaly detection identifies issues automatically while correlation tracking accelerates troubleshooting. |
| Use [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing) or custom frameworks for realistic workload simulation that represents production scenarios. Implement test scenarios covering producers, consumers, and failure conditions.<br><br>Monitor all performance metrics during test execution and document results, baselines, and optimal configuration settings for future reference. | Validated capacity limits and scaling behavior provide confidence in production deployment.<br><br>Established performance baselines support capacity planning decisions and optimization efforts.|
| Configure batch sizes, prefetch values, and retry policies in [SDK settings](/azure/event-hubs/event-hubs-programming-guide) for optimal producer and consumer performance. Implement connection pooling to reduce overhead and enable compression with [Schema Registry](/azure/event-hubs/schema-registry-overview) integration. | Optimized throughput through batching and prefetch tuning while reducing connection and network overhead. <br><br>Data-driven optimization through metrics-based tuning enables continuous performance improvements and efficient resource utilization. |
| Configure dedicated consumer groups for [Stream Analytics](/azure/stream-analytics/stream-analytics-define-inputs#event-hubs) and [Azure Functions](/azure/azure-functions/functions-bindings-event-hubs) to optimize downstream processing. Implement caching for event metadata and processing results while optimizing checkpoint frequency based on processing patterns.| Specialized consumer groups enable optimized downstream processing while caching reduces redundant processing and improves efficiency. <br><br>Optimized checkpointing balances resilience and performance requirements.|

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

- [Azure Event Hubs overview](/azure/event-hubs/event-hubs-about)
- [Event Hubs monitoring](/azure/event-hubs/monitor-event-hubs)
- [Event Hubs quickstart for .NET](/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)
