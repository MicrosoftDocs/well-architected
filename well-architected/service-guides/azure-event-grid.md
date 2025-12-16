---
title: "Architecture Best Practices for Azure Event Grid"
description: "Architecture best practices for Azure Event Grid covering the Well-Architected Framework pillars: Reliability, Security, Cost Optimization, Operational Excellence, and Performance Efficiency."
author: PageWriter-MSFT
ms.author: prwilk
ms.topic: concept-article
ms.date: 12/16/2024
ms.subservice: waf-service-guide
products:
 - azure-event-grid
azure.category:
 - integration
---

<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD025 -->

# Architecture best practices for Azure Event Grid

Azure Event Grid is a highly scalable and fully managed publish-subscribe service for message distribution. Event Grid offers flexible message consumption patterns and uses the Message Queuing Telemetry Transport (MQTT) and HTTP protocols to enable event-driven serverless architectures, data pipelines, and application integration.

This article assumes that as an architect, you've reviewed the [messaging decision tree](/azure/architecture/guide/technology-choices/messaging) and chose Azure Event Grid as the messaging service for your workload.

The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](/azure/well-architected/pillars).

<!-- markdownlint-disable-next-line MD036 -->
**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:

- Azure Event Grid

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the nature of your application and the criticality of its components. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Anticipate potential failures through systematic analysis:** Failure mode analysis provides a systematic approach to anticipating potential failure scenarios and developing mitigation strategies to ensure Event Grid service resilience.
>
>    | Failure | Mitigation |
>    |---------|------------|
>    | Event delivery failures to consumers | Configure exponential backoff retry policies and dead letter destinations. Implement consumer health monitoring with automated alerts. |
>    | Consumer service unavailability | Deploy circuit breaker patterns and alternative processing paths. Use multiple consumer instances across availability zones. |
>    | Regional Event Grid outage | Replicate topics across multiple regions and deploy consumers in multiple regions with traffic routing capabilities. |
>
> - **Implement redundancy strategies for high availability:** Event Grid topics automatically leverage availability zones in supported regions, providing zone-level redundancy without configuration. When deploying in availability zone-enabled regions, this capability is enabled by default.
>
>    Deploy consumer applications across availability zones using AKS or App Service zone-redundant deployments to enable zone-level failover capabilities. For cross-region active-active redundancy, deploy your Event Grid resource configurations in multiple regions including topics, domains, namespaces, and event subscriptions using Infrastructure as Code.
>
>    Configure producer applications to publish events to Event Grid resources in all deployed regions simultaneously, achieving cross-region event data redundancy. Implement active-active redundancy by deploying consumer applications in multiple regions with Traffic Manager or Front Door routing. Deploy consumer applications using deployment stamps pattern to achieve workload-level redundancy across regions.
>
> - **Design reliable scaling for variable event loads:** Leverage Event Grid's automatic scaling that handles varying event volumes without manual intervention. Configure subscription delivery policies to handle scaling operations, including retry policies and dead letter destinations. Design scale unit strategies for consumer applications using horizontal scaling across instances and regions.
>
>    Monitor scaling operations to ensure continuous event processing during dynamic load patterns. Account for subscription throttling limits when designing scaling strategies to avoid delivery failures. Configure autoscaling for consumer applications to process increased event volumes effectively. Implement consumer scaling based on Event Grid metrics such as undelivered events and delivery attempt counts.
>
> - **Implement monitoring and alerting for reliability:** Configure metrics to track reliability indicators including delivery success rates, failed delivery counts, and dead letter volumes. Set up availability monitoring to track topic accessibility and consumer endpoint health.
>
>    Implement failure detection alerting with defined thresholds for delivery failures, consumer unavailability, and dependency health. Configure recovery time monitoring to track processing delays against defined objectives. Monitor dependency health for storage accounts, Functions, and consumer services that affect event processing.
>
>    Set up composite health monitoring to combine Event Grid metrics with consumer performance indicators. Configure alerting escalation procedures for critical reliability events.
>
> - **Implement reliability testing strategies for event-driven systems:** Design synthetic event generation to test delivery reliability including high-volume scenarios and edge cases. Implement chaos engineering to validate event processing resilience during simulated infrastructure failures. Configure automated testing to verify retry policies, dead letter handling, and consumer failover behaviors.
>
> - **Design disaster recovery strategy for business continuity:** Configure Microsoft-initiated failover using the Cross-Geo setting to enable automatic metadata replication to paired regions with best-effort failover during regional outages. Alternatively, implement customer-initiated failover using the Regional setting through client-side disaster recovery patterns that provide full control over secondary region selection and failover timing.
>
>    Configure automated backup for subscription configurations and routing rules using Infrastructure as Code. Design active-passive disaster recovery with standby consumer deployments in secondary regions. Establish Recovery Time Objective (RTO) and Recovery Point Objective (RPO) requirements aligned with business needs.
>
>    Configure geo-redundant dead letter destinations using cross-region storage replication to prevent event loss. Implement disaster recovery testing procedures that include cross-region failover and producer redirection. Design event replay capabilities from dead letter destinations to recover lost processing during outages.
>
> - **Implement self-preservation techniques for operational resilience:** Apply self-preservation techniques to ensure that your Event Grid workload can recover quickly from failures and continue to function:
>
>   - Configure retry policies with exponential backoff to handle transient consumer failures automatically.
>   - Implement dead letter destinations to preserve undeliverable events and prevent data loss.
>   - Design consumer applications with circuit breaker patterns to prevent cascading failures.
>   - Configure subscription filters and routing to minimize blast radius through isolation.
>   - Implement degradation strategies to continue critical event processing when non-essential dependencies fail.
>   - Use built-in delivery guarantees and ordering to maintain data consistency during partial failures.
>   - Configure event time-to-live values to prevent resource exhaustion during extended outages.
>

### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Set up consumer application scaling based on Event Grid metrics like undelivered event counts and delivery attempts. Configure custom scaling rules that use dead letter queue volumes as triggers.<br><br>Implement subscription-level monitoring with different thresholds for event types. Configure scaling automation through Logic Apps or Functions that respond to metrics. | Enables consumer scaling based on actual event processing demand rather than resource utilization. Prevents delivery failures through early capacity constraint detection. Supports differentiated scaling strategies through subscription-level monitoring. |
| Configure monitoring metrics and alerts with SLO-aligned thresholds for delivery success rates. Set up dashboards that display delivery success percentages, latency, and consumer response times. Configure metric aggregations over appropriate time windows for SLI measurement. | Provides visibility into SLO achievement through real-time metrics and enables response to reliability degradation before business impact. Supports data-driven improvements through historical analysis. |
| Set up dead letter queue monitoring with alerts for volumes that exceed thresholds, typically 1% of events. Configure consumer endpoint health checks that monitor availability and response times.<br><br>Implement composite health checks that combine Event Grid and consumer metrics. Set up alerting escalation when multiple indicators degrade simultaneously. | Enables early detection of systemic issues across the event pipeline and rapid incident response through escalation procedures. Provides end-to-end visibility from publication to consumer processing. |
| Create automated test suites that generate synthetic events with various payload sizes and types. Implement load testing using [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing) to simulate realistic event volumes. Configure automated testing that validates retry policy effectiveness for transient and permanent failures. | Validates delivery reliability under realistic load conditions and verifies retry and error handling before production deployment. Provides continuous validation through CI/CD pipeline integration. |
| Implement chaos engineering using [Azure Chaos Studio](/azure/chaos-studio/chaos-studio-overview) to inject failures into consumers and dependencies. Configure controlled failure scenarios like zone outages, consumer disruptions, and network partitions.<br><br>Design fault injection testing for storage accounts that support dead letter destinations. Set up automated recovery validation to ensure systems return to a healthy state. | Validates event processing resilience under realistic failure conditions and identifies reliability gaps before production incidents. Builds confidence in disaster recovery procedures through systematic simulation. |
| Enable Microsoft-initiated failover by configuring topics with the Cross-Geo data residency setting (default) that automatically replicates metadata to paired regions during outages. Alternatively, configure the Regional data residency setting and implement [customer-initiated failover](/azure/event-grid/custom-disaster-recovery-client-side) using client-side disaster recovery patterns for full control over secondary regions.<br><br>Configure automated failover procedures for producer applications that redirect to secondary regional topics during primary region outages. Set up Azure Service Health monitoring for Event Grid with automated failover notification procedures. | Provides automatic metadata failover to paired regions during regional outages with Cross-Geo configuration. Offers full control over secondary region selection and failover timing with Regional configuration and client-side DR. Reduces manual intervention during disasters through automated failover response. |
| Implement automated configuration backup using Azure CLI or PowerShell scripts. Store backup scripts and templates in version control along with disaster recovery runbooks. Configure [geo-redundant storage](/azure/storage/common/storage-redundancy) for dead letter destinations with cross-region replication. | Enables rapid configuration recovery during disaster scenarios and prevents event loss through geo-redundant storage. Ensures consistent recovery procedures through version-controlled automation. |
| Configure standby consumer applications in secondary regions with pre-provisioned infrastructure. Implement automated failover using [Azure Automation](/azure/event-grid/handler-webhooks#azure-automation) or [Logic Apps](/azure/logic-apps/logic-apps-overview) to activate secondary consumers. Configure event replay capabilities from dead letter storage to recover lost processing. | Enables rapid consumer activation during regional disasters and achieves RTO and RPO through validated procedures. Supports complete processing recovery through replay capabilities. |
| Configure retry policies with exponential backoff (1-second start, 24-hour maximum) to handle transient consumer failures. Set up dead letter destinations using Storage queues or Service Bus queues to preserve undeliverable events.<br><br>Configure event time-to-live based on business requirements (typically one to seven days) to prevent resource exhaustion. Implement subscription filtering to route event types to appropriate destinations and minimize blast radius. | Enables platform-managed automatic recovery from transient failures without manual intervention. Provides event preservation during extended outages, enabling later reprocessing and analysis.<br><br>Supports fault containment through event routing and TTL, preventing systemic overload. |
| Implement [circuit breaker patterns](/dotnet/architecture/microservices/implement-resilient-applications/implement-circuit-breaker-pattern) using resilience libraries like Polly for .NET or resilience4j for Java to prevent cascading failures. Configure circuit breaker thresholds based on failure rates, typically opening after five consecutive failures.<br><br>Design fallback mechanisms to continue critical event processing when non-essential dependencies are unavailable. Implement health check endpoints for consumer monitoring that enable automatic traffic rerouting. | Prevents cascading failures through automatic dependency isolation when consumers fail. Maintains critical event processing through fallback mechanisms during partial system failures.<br><br>Enables automatic recovery and intelligent traffic management through health-based routing. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure Event Grid.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture.

> [!div class="checklist"]
>
> - **Establish a security baseline for Event Grid deployments:** Review the [security baseline](https://learn.microsoft.com/security/benchmark/azure/baselines/event-grid-security-baseline) to implement controls for operations, data protection, and access management. Cover identity and access management, network security, encryption, and logging in your implementation.
>
>    Use the baseline guidance to address service-specific requirements such as topic access control and subscription security. Align with Microsoft Cloud Security Benchmark controls for a consistent security posture, and review the baseline regularly as requirements and threats evolve.
>
> - **Implement segmentation controls for event-driven workload isolation:** Establish identity as the primary security perimeter using Azure RBAC for Event Grid resource access control. Implement role-based access control with built-in roles: Event Grid Contributor, Event Grid EventSubscription Contributor, and Event Grid Data Sender.
>
>    Design topic-level isolation to separate application domains and business functions using distinct topics. Configure subscription-level access controls to limit event consumption to authorized consumers. Organize Event Grid resources using resource groups and subscriptions for logical separation and access boundaries.
>
>    Implement publisher and subscriber identity separation using managed identities and service principals. Configure event filtering and routing to minimize blast radius and route events to intended consumers.
>
> - **Integrate with Microsoft Entra ID for centralized identity and access management:** Use Microsoft Entra ID as the identity provider for Event Grid authentication and authorization. Implement managed identities for service-to-service authentication to eliminate stored credentials. Configure conditional access policies based on user context, device status, and network location.
>
>    Separate data plane access for event publishing and consuming from control plane resource management. Implement Just-in-Time and Just Enough Access principles for administrative operations.
>
>    Configure audit trails through Azure Activity Log for identity access and authorization decisions. Use Microsoft Entra ID groups for role assignment and access management.
>
> - **Apply network security controls for secure event communication:** Implement private endpoints for Event Grid topics to eliminate internet exposure and enable VNet integration. Configure IP filtering and firewall rules at the topic level to restrict access to authorized source addresses and ranges.
>
>    Design a VNet integration strategy that connects publishers, Event Grid, and consumers within network boundaries. Use network security groups and route tables to control traffic flow and implement micro-segmentation.
>
>    Implement network monitoring and logging for Event Grid traffic patterns and connection attempts.
>
> - **Implement data encryption for event protection:** Event Grid encrypts data at rest by default using Microsoft-managed platform keys for stored events and metadata. Enforce TLS 1.2 minimum for all Event Grid communications including event publishing and consumption. Configure certificate management for TLS connections with automated renewal and validation.
>
>    Platform encryption applies to temporary event storage during processing and delivery retry scenarios. Note that customer-managed keys are not currently supported for Event Grid topics and domains.
>
> - **Harden Event Grid configurations to minimize attack surface:** Minimize your Event Grid attack surface by systematically hardening configurations:
>
>   - Disable unnecessary authentication methods and protocols, and retain only required mechanisms.
>   - Configure topic and subscription settings with least-privilege principles, and remove unused capabilities.
>   - Remove or disable legacy protocol support and authentication methods that introduce vulnerabilities.
>   - Configure resource-level settings with security-focused defaults for timeout values and retry policies.
>   - Implement secure naming conventions and resource organization to prevent information leakage.
>   - Configure minimal required permissions for Event Grid service principals and managed identities.
>   - Assign Azure Policy built-in policies for Event Grid at subscription scope (e.g., "Azure Event Grid topics should use private link")
>
> - **Secure secrets and credentials for Event Grid integrations:** Microsoft Entra ID with managed identities is strongly recommended because it eliminates the need for stored secrets. If you're not using Entra ID, store connection strings, API keys, and authentication tokens in Azure Key Vault with access controls and rotation policies.
>
>    Implement distinct keys for different environments and consumers to avoid shared credentials. Configure automatic secret rotation for Event Grid access keys and webhook tokens with minimal disruption. Prevent hardcoding secrets in event handler applications, configuration files, and deployment pipelines.
>
>    Monitor secret access patterns through Key Vault auditing and configure alerting for anomalous attempts. Design recovery procedures for compromised credentials, including rapid key regeneration.
>
> - **Implement security monitoring and logging for Event Grid:** Configure Azure Monitor diagnostic settings to capture security-relevant events including access attempts and configuration changes. Implement activity logging for Event Grid operations including topic creation, subscription management, and policy modifications. Monitor identity and access patterns through Azure Activity Log to identify unusual patterns and authorization failures.
>
>    Configure alerting for security events including failed authentication, unauthorized access, and suspicious patterns. Integrate Event Grid logs with Microsoft Sentinel for threat detection and security analytics. Implement structured logging with timestamping and event metadata for security analysis and forensic investigation.
>
>    Monitor event delivery patterns for anomalous behavior that indicates potential security incidents. Configure log retention policies that meet organizational security and compliance requirements.
>
> - **Validate security controls through systematic testing and assessment:** Implement regular validation testing for Event Grid access controls, network security, and encryption settings. Conduct penetration testing focused on endpoints, authentication mechanisms, and access control bypass attempts. Validate network security controls including private endpoint configuration, IP filtering, and VNet integration. Test identity and access management including RBAC assignments, conditional access policies, and managed identity authentication. Test event flow security from publishers through Event Grid to consumers. Implement automated security testing in deployment pipelines to catch misconfigurations.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Assign built-in [RBAC roles](/azure/event-grid/security-authentication) at appropriate scopes for least-privilege access. Use [Event Grid Contributor](/azure/role-based-access-control/built-in-roles#event-grid-contributor) for users managing topics and subscriptions, Event Grid EventSubscription Contributor for users managing only event subscriptions, and Event Grid Data Sender for applications publishing events. | Aligns granular access control with operational responsibilities and enforces least-privilege access through separation of management and data plane operations. |
| Organize [topics](https://learn.microsoft.com/en-us/azure/event-grid/concepts#topics) using resource groups that align with security and operational boundaries. Implement naming conventions that reflect security boundaries and access requirements. Configure [subscription filtering](/azure/event-grid/event-filtering) to route events only to authorized consumers. | Separates event flows logically based on security policies and reduces blast radius through isolated topic structures. Establishes clear organizational boundaries for compliance. |
| Assign [managed identities](/azure/event-grid/security-authenticate-publishing-clients) to Event Grid topics, subscriptions, and event handlers for Azure service authentication. Use system-assigned identities for single-resource scenarios or user-assigned identities for shared identity needs. Configure managed identities for Azure Functions, Logic Apps, and storage accounts handling events. | Eliminates stored credentials and associated security risks. Provides automatic credential rotation through the Azure platform and enables secure service-to-service authentication without manual secret management. |
| Enable [conditional access policies](/azure/active-directory/conditional-access/overview) for Event Grid management operations. Require multi-factor authentication for privileged operations like topic creation and policy changes. Set location-based controls restricting management to authorized regions.<br><br>Enforce device compliance requirements for administrative access. | Applies context-aware security controls based on risk conditions and reduces unauthorized access through additional authentication factors. Enables adaptive security policies that respond to changing threats. |
| Deploy [private endpoints](/azure/event-grid/configure-private-endpoints) for Event Grid topics within VNet. Configure DNS settings for proper name resolution.<br><br>Set up network security groups and route tables to control traffic flow. | Eliminates internet exposure through network-level isolation and enables secure communication within VNet boundaries. Establishes centralized access control through security groups. |
| Configure [IP filtering](/azure/event-grid/configure-firewall) rules at the topic level using an allow-list approach. Deny access by default and explicitly permit authorized source addresses and CIDR ranges. Set up monitoring and alerting for blocked access attempts. | Controls network access at the service level and reduces attack surface by limiting access to known sources. Enables detection of unauthorized access attempts. |
| Enforce [TLS 1.2](/azure/event-grid/transport-layer-security-configure-minimum-version) as the minimum protocol version for all Event Grid communications. Configure TLS settings for custom domain endpoints and webhook destinations. Implement certificate validation for webhook endpoints receiving events. | Protects against protocol downgrade attacks with industry-standard encryption for data in transit. Prevents man-in-the-middle attacks through certificate validation. |
| Apply [Azure Policy](/azure/governance/policy/samples/built-in-policies#event-grid) to enforce security configuration standards and prevent drift. Use infrastructure as code for consistent security configurations across environments. Disable unnecessary authentication methods and legacy protocol support. Set minimum required timeout values and retry policies.<br><br>Monitor configuration changes with automated alerts for unauthorized modifications. | Reduces attack surface by eliminating unnecessary features and prevents configuration drift through policy enforcement. Maintains a consistent security posture across environments and enables rapid detection of unauthorized changes. |
| Store Event Grid credentials in Azure Key Vault with RBAC access controls. Enable automated secret rotation for access keys and webhook tokens.<br><br>Configure distinct keys per environment and application. | Provides secure storage with access control and auditing. Enables automated credential lifecycle management and ensures segregated access through environment-specific keys. |
| Enable [diagnostic settings](/azure/event-grid/monitor-event-delivery) routing Event Grid logs to Log Analytics workspace. Configure alert rules for failed authentication, unauthorized access, and configuration changes. Integrate with Microsoft Sentinel for threat detection and security analytics.<br><br>Set log retention policies meeting compliance requirements. | Provides visibility into security events and potential threats. Enables rapid incident response through automated alerting and supports compliance through structured logging and retention. Detects threats through security analytics. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure Event Grid and its environment.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Analyze pricing models and cost structures to optimize Event Grid expenses:** Evaluate Event Grid Basic and Standard tiers to understand how pricing models affect your solution costs. Basic tier uses per-operation pricing for predictable costs in low-volume, simple push delivery scenarios. Standard tier combines pre-purchased throughput units with per-operation charges.
>
>    Each throughput unit (TU) provides fixed capacity for sessions, message ingress per second, and topics per namespace. TUs have a fixed hourly cost regardless of usage. Under-provisioning causes throttling and service degradation. Over-provisioning wastes money on unused capacity.
>
>    MQTT operations cost more than event operations. Plan for cross-region data transfer bandwidth charges when you deliver events to different Azure regions. Include event retention and dead letter storage costs beyond base Event Grid pricing.
>
> - **Optimize capacity and throughput for cost-efficient event processing:** Configure event batching to deliver multiple events in single operations and reduce per-operation costs. Optimize batch size during load testing. Apply event filtering at the subscription level to prevent unnecessary processing and delivery of irrelevant events. Configure dead letter handling and retry policies to manage processing attempts and storage costs.
>
>    Define event retention policies to balance data availability with storage costs. Plan capacity to account for peak event volumes and processing patterns.
>
> - **Eliminate resource waste and optimize configurations:** Systematically eliminate Event Grid resource waste through regular optimization practices:
>
>   - Remove unused Event Grid topics, subscriptions, and endpoints that generate costs without value.
>   - Clean up orphaned resources including dead letter destinations and unused custom topics.
>   - Configure event retention policies to balance data availability with storage costs.
>   - Optimize delivery settings, retry policies, and filtering rules to improve cost efficiency.
>   - Conduct regular resource audits to identify underutilized resources for consolidation or decommissioning.
>   - Consolidate similar subscriptions to reduce management overhead.
>
> - **Implement cost monitoring and control mechanisms:** Monitor usage patterns, operation volumes, and spending trends to establish cost visibility. Use cost analytics to identify cost drivers including event volumes, delivery attempts, and cross-region data transfer.
>
>    Configure budget alerts, spending limits, and automated policies for cost control. For Standard tier, monitor throughput unit (TU) utilization percentages to identify right-sizing opportunities and set up alerts for utilization thresholds. Enable real-time monitoring to proactively identify cost anomalies.
>
>    Implement cost reporting for granular visibility by resource, environment, and business unit. Conduct historical analysis to identify trends and seasonal patterns for forecasting.
>
> - **Establish cost governance policies and controls:** Define cost governance using organizational policies for responsible spending. Implement resource policies to enforce cost-conscious configurations and prevent expensive misconfigurations. Establish approval workflows for high-cost deployments and feature usage. Conduct regular cost reviews and optimization assessments to maintain cost discipline. Apply policy-based controls to enforce tagging requirements, resource limits, and cost allocation standards.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Select your Event Grid tier based on feature requirements and cost constraints. Review usage patterns periodically to validate your tier selection. | Prevents paying for unused premium features and matches tier capabilities to your usage patterns. |
| Configure event batching to deliver multiple events in single operations. Apply event filters at the subscription level to process only relevant events. | Reduces per-operation costs through batched delivery and eliminates costs from processing irrelevant events. |
| Monitor event processing patterns and peak usage to establish baseline capacity. Use throughput metrics to right-size your resources.<br><br>Account for seasonal variations and growth projections when you plan capacity. | Prevents over-provisioning and ensures adequate performance for peak loads by adjusting capacity based on your usage patterns. |
| Optimize delivery configurations including timeout and retry settings to balance reliability with cost. Configure dead letter policies to minimize storage costs and maintain error visibility. | Reduces retry attempt costs and maintains reliability while minimizing storage costs and preserving error visibility. |
| Use Azure Cost Management to track spending patterns and implement cost analytics. Set up automated cost reporting with breakdowns by resource, environment, and business unit.<br><br>Use cost analysis to identify high-cost operations and optimization opportunities. | Provides visibility into spending patterns and enables cost attribution and identification of high-cost operations. |
| Configure cost alerts with multiple threshold levels at subscription, resource group, and topic levels. Enable cost anomaly detection to identify unusual spending patterns. Set up budget limits with automated alerts and spending caps.<br><br>Implement budget forecasting based on your processing trends. | Prevents cost overruns with early warnings at multiple organizational levels, identifies configuration issues or unusual usage patterns, and enables planning based on predicted spending growth. |
| Apply resource tags using consistent naming conventions: environment, application, business unit, cost center, and project. Tag at topic, subscription, and endpoint levels for granular tracking. | Enables cost allocation and chargeback to business units and projects and identifies high-cost event processing patterns. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**. The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure Event Grid.

> [!div class="checklist"]
>
> - **Assess team readiness for Azure Event Grid operations:** Develop operational knowledge in event schema design, routing configuration, consumer patterns, dead letter handling, retry policies, and subscription management. Create documentation for schema design guidelines, subscription configuration patterns, and troubleshooting runbooks. Document event routing patterns, endpoint requirements, and error handling strategies. Establish standards for schema versioning and consumer deployment procedures. Define service lifecycle management processes including topic provisioning, subscription lifecycle, and decommissioning workflows.
>
> - **Establish safe deployment practices:** Implement blue-green deployment using parallel topic configurations with traffic routing. Use progressive deployment with schema versioning in event payloads and staged consumer rollouts. Define rollback procedures for rapid recovery through configuration reversion and deployment rollback.
>
>    Establish quality gates including schema validation, endpoint health checks, and delivery verification.
>
> - **Implement Infrastructure as Code for resource management:** Use Infrastructure as Code tools including ARM templates, Bicep, and Terraform for resource provisioning and configuration management. Configure parameter files for environment-specific settings including topic naming, subscription filters, and security policies. Include topics, subscriptions, event filters, dead letter destinations, and security settings in your configuration.
>
>    Implement layered deployment to handle dependencies between Event Grid resources, consumer services, and identity providers. Configure drift detection to prevent deviation from desired subscription filters, retry policies, and security settings.
>
> - **Design build and deployment pipelines for workloads:** Design pipeline architecture to coordinate topic deployment, subscription configuration, and consumer application releases. Implement environment-specific deployment for varying configurations including topic naming, subscription filters, and security policies.
>
>    Integrate CI/CD tools like Azure DevOps and GitHub Actions to support resource deployment and consumer coordination. Establish security gates including schema validation, endpoint security verification, and compliance checks.
>
>    Implement artifact management for configuration templates, schema definitions, and consumer deployment packages. Configure monitoring integration for visibility into deployment success and post-deployment health.
>
> - **Implement monitoring and logging for operations:** Define an observability strategy to track delivery metrics, consumer processing telemetry, and end-to-end event flow. Monitor service metrics including delivery success rates, retry counts, dead letter volumes, and processing latency. Implement structured logging to capture event correlation IDs, processing results, error details, and troubleshooting context.
>
>    Integrate Azure Monitor for Event Grid analytics including operational trends and insights. Configure an alerting framework to support pillar-specific alerts for reliability, performance, security, and cost.
>
>    Enable distributed tracing for end-to-end visibility from event publication through consumer processing. Create monitoring dashboards for real-time visibility into workload operations and Event Grid service health.
>
> - **Establish operations and emergency response procedures:** Define incident response processes for delivery failures, consumer outages, regional service disruptions, and cascading failures. Establish failover operations for regional outages through cross-region topic replication and consumer coordination.
>
>    Create emergency response procedures for critical event delivery scenarios including business-critical processing and restoration priorities. Define communication protocols to coordinate incident response across distributed teams managing infrastructure and applications.
>
> - **Automate Event Grid management and support operations:** Implement administrative automation for topic creation, subscription management, event filter configuration, and dead letter setup. Establish support automation for troubleshooting data collection, log aggregation, event replay, and maintenance tasks. Configure drift detection to monitor subscription filters, retry policies, and security settings.
>
>    Integrate with Azure Automation, Logic Apps, and Event Grid APIs for management automation. Create reusable automation modules for operations shared across workloads.
>
> - **Implement testing and validation strategies:** Define testing strategies including schema validation, endpoint verification, end-to-end processing, and integration testing. Implement test automation frameworks for continuous validation of schema compatibility and consumer processing. Design test environments that replicate production characteristics including topic configurations, subscriptions, and consumer deployments.
>
>    Apply production testing approaches including canary event publishing, consumer health validation, and controlled event replay. Integrate testing tools including schema validation frameworks, endpoint testing utilities, and simulation capabilities.
>
>    Validate rollback testing to verify recovery procedures including configuration reversion and deployment rollback.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Develop [Infrastructure as Code](/azure/templates/microsoft.eventgrid/namespaces) modules using [ARM templates, Bicep, or Terraform](/azure/azure-resource-manager/bicep/) for topics, subscriptions, domains, and security configurations. Create modular designs with parameterization for different scenarios and environment-specific settings. | Provides consistent deployments across environments while enabling reusable components and simplified maintenance. |
| Configure [diagnostic settings](/azure/event-grid/monitor-event-delivery) to route Event Grid system topics, custom topics, and domain logs to [Log Analytics workspace](/azure/azure-monitor/logs/log-analytics-overview). Enable resource logs for data plane operations including event delivery attempts and webhook responses.<br><br>Create monitoring dashboards with [Azure Monitor workbooks](/azure/azure-monitor/essentials/metrics-supported#microsofteventgridtopics) displaying metrics including delivery success rates, retry counts, dead letter volumes, and processing latency. Configure [alerting rules](/azure/azure-monitor/alerts/alerts-overview) for operational health including delivery failures, high latency, and dead letter accumulation. | Provides comprehensive operational visibility into event delivery performance and service health. Enables proactive issue detection and rapid troubleshooting through centralized logging and alerting. |
| Configure [Azure Monitor alerts](/azure/azure-monitor/alerts/alerts-overview) for delivery failures, latency degradation, and dead letter queue accumulation using [metric-based alert rules](/azure/azure-monitor/alerts/alerts-create-metric-alert-rule). Set up [action groups](/azure/azure-monitor/alerts/action-groups) for automated response including notification, Logic Apps workflows, and runbook execution.<br><br>Implement [intelligent alerting](/azure/azure-monitor/alerts/alerts-overview) using dynamic thresholds for baseline deviation and noise reduction. Configure alert suppression during maintenance windows and known deployment activities. | Ensures rapid response to operational issues through automated alerting and response actions. Reduces operational overhead through intelligent alerting while maintaining service reliability visibility. |
| Develop incident response procedures including delivery failure troubleshooting, consumer outage handling, and regional failover coordination. Create [runbooks](/azure/automation/automation-runbook-types) for common operational tasks including dead letter queue processing, configuration rollback, and service restoration.<br><br>Establish communication protocols including escalation procedures, stakeholder notification, and status reporting. Define recovery time objectives and procedures for different failure scenarios including partial outages and complete service disruption. | Enables coordinated incident response that minimizes service impact and recovery time. Provides standardized procedures that ensure consistent response across different failure scenarios. |
| Implement administrative automation using [Azure Automation](/azure/automation/), [Logic Apps](/azure/logic-apps/), and [Event Grid APIs](/azure/event-grid/sdk-overview) for subscription management, topic provisioning, and configuration maintenance. Create reusable automation modules for common operations including filter updates, endpoint configuration, and security policy application.<br><br>Configure [configuration drift detection](/azure/governance/policy/how-to/get-compliance-data) using Azure Policy to monitor subscription filters, retry policies, and security settings. Implement automated remediation for detected configuration deviations. | Reduces manual operational effort while ensuring configuration consistency and compliance. Provides proactive configuration management that prevents operational issues through automated monitoring and remediation. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Azure Event Grid.

> [!div class="checklist"]
>
> - **Conduct capacity planning for event throughput and scalability:** Estimate event volume requirements based on your business needs, including peak event rates, message sizes, and subscription patterns. Analyze historical event data trends, seasonal variations, and business expansion projections to plan for growth. Incorporate service limits and quotas in your design, including events per topic, subscription limits, and regional throughput caps.
>
>    Account for consumer processing capacity when you plan throughput, as slow consumers create backpressure that affects system performance.
>
>    Validate your capacity estimates through load testing and proof-of-concept deployments before production.
>
> - **Define scaling strategies for topics and consumers:** Choose a scaling approach between topic scaling and consumer scaling based on your workload characteristics. Design topic scaling to distribute load across multiple topics when throughput limits are approached, organizing by functional boundaries or geographic regions. Implement consumer scaling using Functions premium plans, Container Apps, or AKS with autoscaling based on queue depth and processing demand.
>
>    Configure subscription filtering and routing to distribute load across consumers. Design scaling automation using Azure Monitor metrics and autoscale capabilities.
>
>    Test scaling effectiveness through load testing to validate triggers, delays, and performance impact.
>
> - **Implement performance monitoring for event delivery:** Establish performance baselines, including latency thresholds (p50, p95, p99 percentiles), throughput targets, and processing time benchmarks. Monitor capacity utilization to identify when you need scaling or optimization adjustments. Monitor bottlenecks across the delivery pipeline, including publisher performance, service latency, network connectivity, and consumer processing time.
>
>    Configure alerting for performance degradation, including latency spikes, throughput reduction, and processing slowdowns. Analyze performance trends to identify patterns, seasonal variations, gradual degradation, and optimization opportunities.
>
>    Use distributed tracing to analyze performance at the transaction level and identify bottlenecks across the event delivery pipeline.
>
> - **Conduct performance testing for event delivery patterns and consumer capacity:** Design test scenarios, including normal load, peak load, and stress conditions that reflect realistic publishing patterns. Use production-like configurations for realistic validation. Implement load testing using tools that test delivery performance, including burst traffic and sustained load.
>
>    Establish baselines through systematic testing to understand service capabilities, latencies, and throughput limitations. Test scaling behavior by validating autoscaling mechanisms and load distribution under various conditions.
>
>    Analyze your results to identify bottlenecks, scaling delays, and optimization opportunities.
>
> - **Optimize event delivery and consumer performance configurations:** Configure delivery settings, including retry policies, timeouts, and dead-letter destinations to maximize success rates while minimizing latency. Implement subscription filters to reduce unnecessary processing and deliver only relevant events. Optimize consumer patterns, including batching, parallel processing, and connection pooling to maximize throughput.
>
>    Apply payload optimization through efficient serialization, compression, and schema design. Monitor and optimize using metrics, telemetry, and testing results.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Create multiple [topics](/azure/event-grid/custom-topics) organized by functional boundaries, geographic regions, or event types. Configure topic naming conventions and routing logic for automatic topic selection.<br><br>Monitor topic-level metrics to identify when topics approach their [throughput limits](https://learn.microsoft.com/en-us/azure/event-grid/custom-event-quickstart). | Overcomes single topic throughput limitations and enables logical separation that improves maintainability and security. Allows independent scaling of specific event domains. |
| Design your consumer applications to scale automatically based on Event Grid event volume. For push delivery, implement consumer applications using [Functions with premium or dedicated plans](/azure/azure-functions/functions-premium-plan) that scale based on incoming events.<br><br>For pull delivery with namespace topics, design consumer applications with dynamic scaling capabilities. Configure Event Grid [retry policies and dead-letter destinations](/azure/event-grid/delivery-and-retry) to ensure reliable delivery during consumer scaling operations. Use [subscription filters](/azure/event-grid/event-filtering) to distribute events across multiple consumer instances. | Consumer applications handle traffic spikes through automatic scaling. Ensures resource efficiency through dynamic scaling. Event Grid retry and dead-letter features improve resilience during scaling transitions. |
| Configure [Azure Monitor](/azure/event-grid/monitor-event-delivery) to collect performance metrics, including DestinationProcessingDurationInMs, PublishSuccessDurationInMs, and delivery latency percentiles. Set up custom metrics using Application Insights for consumer-specific processing times and end-to-end latency.<br><br>Create performance dashboards using [Azure Monitor workbooks](/azure/azure-monitor/essentials/metrics-supported#microsofteventgridtopics) that display latency trends, throughput rates, and processing time distributions. | Enables performance optimization through latency and throughput analysis. Identifies performance trends and degradation patterns. Supports capacity planning through [throughput monitoring](/azure/event-grid/quotas-limits). |
| Configure [alerts](/azure/azure-monitor/alerts/alerts-overview) on latency degradation, including p95 and p99 percentile thresholds above your performance baselines. Set up [dynamic thresholds](/azure/azure-monitor/alerts/alerts-create-new-alert-rule) using intelligent detection for throughput reduction and processing slowdowns.<br><br>Implement alerts for consumer processing time increases that indicate performance bottlenecks. Configure alerts for capacity utilization approaching scaling thresholds. | Detects performance degradation before it affects user experience. Enables proactive performance optimization through early detection. Identifies bottlenecks through processing time monitoring. |
| Use [distributed tracing](/azure/azure-monitor/app/distributed-tracing) data to analyze performance bottlenecks across the event delivery pipeline. Monitor transaction-level performance metrics to identify slow dependencies and optimize consumer processing.<br><br>Analyze the [application map](/azure/azure-monitor/app/app-map) to identify high-latency dependencies and optimize event flow paths. Configure performance profiling for detailed analysis of publisher and consumer processing time. | Identifies specific performance bottlenecks at the transaction level. Enables targeted optimization based on detailed performance analysis. Supports performance improvement through dependency optimization. |
| Use [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing) to generate realistic publishing patterns under various load conditions. Implement custom scripts using [JMeter](/azure/app-testing/load-testing/how-to-create-and-run-load-test-with-jmeter-script) or K6 to simulate publishing with appropriate event sizes and frequencies.<br><br>Configure scenarios to validate [quotas and limits](/azure/event-grid/quotas-limits) by testing service boundaries. Measure delivery success rates, latency, throughput, and error rates. | Validates delivery performance before production. Identifies service limits that enable accurate capacity planning. Provides empirical data for evidence-based optimization. |
| Test autoscaling behavior using [Functions](/azure/azure-functions/functions-premium-plan), [Container Apps](/azure/container-apps/scale-app), or [AKS](/azure/aks/concepts-scale) with realistic workloads. Measure processing latency, queue depth handling, and scaling delays during load variations.<br><br>Validate retry policies, dead-letter handling, and error processing under high load. Verify subscription filtering effectiveness and load distribution across instances. | Prevents performance bottlenecks through validated scaling. Ensures consumers handle delivery patterns under high load. Provides confidence in end-to-end processing performance. |
| Optimize transient failure handling to avoid performance impact. Configure subscription [retry policies](/azure/event-grid/delivery-and-retry) with exponential backoff optimized for your consumer patterns. Set delivery timeout values based on consumer capabilities and network characteristics.<br><br>Implement subscription [filtering](/azure/event-grid/event-filtering) using advanced syntax to deliver only relevant events. Configure dead-letter destinations with storage and monitoring. | Optimizes delivery through tuned retry and filtering. Reduces traffic and processing overhead. Balances reliability and performance. |

## Azure policies

Azure provides an extensive set of built-in policies related to Azure Event Grid and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Azure Event Grid domains use private link
- Azure Event Grid topics use private link
- Logging by category group for Event Grid Domains is enabled to Log Analytics
- Logging by category group for Event Grid Topics is enabled to Log Analytics

For comprehensive governance, review the [Azure Policy built-in definitions for Event Grid](/azure/governance/policy/samples/built-in-policies#event-grid) and other policies that might affect the security of the messaging infrastructure.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Related content

- [Choose between Azure messaging services - Event Grid, Event Hubs, and Service Bus](/azure/service-bus-messaging/compare-messaging-services)
- [Event-driven architecture style](/azure/architecture/guide/architecture-styles/event-driven)
- [Enterprise integration using message queues and events](/azure/architecture/example-scenario/integration/queues-events)
