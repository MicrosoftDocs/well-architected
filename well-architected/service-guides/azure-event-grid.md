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

Azure Event Grid is a scalable, managed publish-subscribe service that distributes messages. Event Grid provides flexible message consumption patterns. It uses the Message Queuing Telemetry Transport (MQTT) and HTTP protocols to enable event-driven serverless architectures, data pipelines, and application integration.

This article assumes that as an architect, you've reviewed the [messaging decision tree](/azure/architecture/guide/technology-choices/messaging) and chose Event Grid as the messaging service for your workload.

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
> - Review service [limits and quotas](/azure/event-grid/quotas-limits) and use them to guide your reliability planning and throughput capacity decisions. Account for subscription throttling limits that affect event delivery when consumers can't process events at the required rate.
>
>   Design event payloads within message size limits (1 MB per event) and split messages when needed. Consider region-specific quotas when you plan deployment strategies and capacity needs.
>
> - **Anticipate potential failures through systematic analysis:** Use failure mode analysis to identify potential failure scenarios and create mitigation strategies to ensure Event Grid resilience.
>
>    | Failure | Mitigation |
>    |---------|------------|
>    | Event delivery failures to consumers | Configure exponential backoff retry policies and dead letter destinations. Implement consumer health monitoring with automated alerts. |
>    | Consumer service unavailability | Deploy circuit breaker patterns and alternative processing paths. Use multiple consumer instances across availability zones. |
>    | Regional Event Grid outage | Replicate topics across multiple regions and deploy consumers in multiple regions with traffic routing capabilities. |
>
> - **Implement redundancy strategies to achieve high availability:** Event Grid topics automatically use availability zones in supported regions. This setup provides zone-level redundancy without any configuration. Availability zone-enabled regions enable this capability by default.
>
>    Deploy consumer applications across availability zones by using Azure Kubernetes Service (AKS) or Azure App Service zone-redundant deployments. This approach enables zone-level failover. For cross-region active-active redundancy, use infrastructure as code (IaC) to deploy your Event Grid configurations in multiple regions. Include topics, domains, namespaces, and event subscriptions.
>
>    Configure producer applications to publish events to Event Grid resources in all deployed regions simultaneously. This setup achieves cross-region event data redundancy. To implement active-active redundancy for your workload, deploy consumer applications in multiple regions and use Azure Traffic Manager or Azure Front Door routing. Use the deployment stamps pattern to achieve workload-level redundancy across regions.
>
> - **Design reliable scaling for variable event loads:** Use autoscaling in Event Grid to handle varying event volumes without manual intervention. Configure subscription delivery policies to handle scaling operations, including retry policies and dead letter destinations. Design scale unit strategies for consumer applications by using horizontal scaling across instances and regions.
>
>    Monitor scaling operations to maintain continuous event processing when load patterns change. When you design your scaling strategies, factor in subscription throttling limits to prevent delivery failures. Set up autoscaling for consumer applications so they can handle spikes in event volumes. Base your consumer scaling decisions on Event Grid metrics like undelivered events and delivery attempt counts.
>
> - **Implement monitoring and alerting to maintain reliability:** Configure metrics to track key reliability indicators like delivery success rates, failed delivery counts, and dead letter volumes. Set up monitoring to track topic availability and consumer endpoint health.
>
>    Implement failure detection alerts with defined thresholds for delivery failures, consumer unavailability, and dependency health. Track recovery time to measure processing delays against your objectives. Monitor the health of dependencies like storage accounts, Azure Functions, and consumer services that affect event processing.
>
>    Set up composite health monitoring to combine Event Grid metrics with consumer performance indicators. Configure alerting escalation procedures for critical reliability events.
>
> - **Implement reliability testing strategies for event-driven systems:** Generate synthetic events to test delivery reliability across different scenarios, including high-volume loads and edge cases. Use chaos engineering to validate event processing resilience during simulated infrastructure failures. Configure automated testing to verify that retry policies, dead letter handling, and consumer failover work as expected.
>
> - **Design a disaster recovery strategy for business continuity:** Choose from two failover options. For Microsoft-initiated failover, use the cross-geo setting. This setting automatically replicates metadata to paired regions and attempts a best-effort failover when a regional outage occurs. For customer-initiated failover, use the regional setting and implement client-side disaster recovery patterns when you need full control over secondary region selection and failover timing.
>
>    Use IaC to set up automated backup for subscription configurations and routing rules. Design active-passive disaster recovery with standby consumer deployments in secondary regions. Define your recovery time objective (RTO) and recovery point objective (RPO) based on your business needs.
>
>    Configure geo-redundant dead letter destinations by using cross-region storage replication to prevent event loss. Test your disaster recovery procedures regularly, including cross-region failover and producer redirection. Design event replay capabilities from dead letter destinations to recover lost processing during outages.
>
> - **Implement self-preservation techniques to ensure operational resilience:** Build self-preservation into your Event Grid workload so that it can recover quickly from failures and continue to function.
>
>   - Configure retry policies with exponential backoff to automatically handle transient consumer failures.
>
>   - Use dead letter destinations to preserve undeliverable events and prevent data loss.
>   - Add circuit breaker patterns to your consumer applications to prevent cascading failures.
>   - Configure subscription filters and routing to isolate failures and minimize their impact.
>   - Create degradation strategies that keep critical event processing running when non-essential dependencies fail.
>   - Use built-in delivery guarantees and ordering to maintain data consistency during partial failures.
>   - Set appropriate time-to-live (TTL) values for events to prevent resource exhaustion during extended outages.
>

### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Set up consumer application scaling based on [Event Grid metrics](/azure/event-grid/monitor-event-delivery) like undelivered event counts and delivery attempts. Create custom scaling rules that trigger on dead letter queue volumes. <br><br> Implement subscription-level monitoring that includes different thresholds for each event type. Use Azure Logic Apps or Functions to automate scaling responses based on these metrics. | Scales your consumers based on actual event processing demand instead of only resource use. Catches capacity problems early to prevent delivery failures. Lets you tailor scaling strategies for different event types. |
| Monitor [delivery success rate metrics](/azure/event-grid/monitor-event-delivery) and [configure alerts](/azure/event-grid/set-alerts) that have service-level objective (SLO)-aligned thresholds. Create dashboards that show delivery success percentages, latency, and consumer response times. Aggregate metrics over appropriate time windows to measure your service-level indicators (SLIs). | Shows you how well you meet your SLOs through real-time metrics. Helps you identify reliability problems before they affect your business. Provides historical data to drive improvements. |
| Set up [dead letter queue monitoring with alerts](/azure/event-grid/delivery-and-retry#dead-letter-events) for volumes that exceed thresholds, typically 1% of events. Configure consumer endpoint health checks that monitor availability and response times. <br><br> Create composite health checks that combine Event Grid and consumer metrics. Set up alerting escalation when multiple indicators degrade at the same time. | Detects systemic problems early across your event pipeline. Lets you respond quickly to incidents by automatically notifying the right people when problems escalate. Gives you end-to-end visibility from event publication to consumer processing. |
| Create automated test suites that generate synthetic events that have different payload sizes and types. Use [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing) to simulate realistic event volumes. Set up automated tests to verify that your retry policies work correctly for both transient and permanent failures. | Checks delivery reliability under realistic load conditions. Verifies retry and error handling before you deploy to production. Validates your setup continuously through your continuous integration and continuous deployment (CI/CD) pipeline. |
| Use [Azure Chaos Studio](/azure/chaos-studio/chaos-studio-overview) to inject failures into your consumers and dependencies. Set up controlled failure scenarios like zone outages, consumer disruptions, and network partitions. <br><br> Design fault injection tests for storage accounts that support dead letter destinations. Set up automated recovery checks to verify that your systems return to a healthy state. | Validates event processing resilience under realistic failure conditions. Identifies reliability gaps before they cause production incidents. Builds confidence in your disaster recovery procedures through systematic simulation. |
| Enable [Microsoft-initiated failover](/azure/event-grid/geo-disaster-recovery) by configuring topics with the default cross-geo data residency setting. This setting automatically replicates metadata to paired regions during outages. Or you can configure the regional data residency setting and implement [customer-initiated failover](/azure/event-grid/custom-disaster-recovery-client-side) by using client-side disaster recovery patterns. Use this option when you need full control over secondary regions. <br><br> Set up automated failover procedures for producer applications to redirect to secondary regional topics when the primary region fails. | Automatically fails over metadata to paired regions during regional outages when you use cross-geo configuration. Provides full control over which secondary regions to use and when to trigger failover when you use regional configuration and client-side disaster recovery. Reduces manual intervention during disasters through automated failover. |
| Implement automated configuration backup by using Azure CLI or Azure PowerShell scripts. Store backup scripts and templates in version control along with disaster recovery runbooks. To protect undelivered events from regional failures, configure [geo-redundant storage](/azure/storage/common/storage-redundancy) for dead letter destinations. | Enables rapid configuration recovery during disaster scenarios and prevents event loss through geo-redundant storage. Ensures consistent recovery procedures through version-controlled automation. |
| Configure standby consumer applications in secondary regions that have pre-provisioned infrastructure. To activate secondary consumers, implement automated failover by using [Azure Automation](/azure/event-grid/handler-webhooks#azure-automation) or [Logic Apps](/azure/logic-apps/logic-apps-overview). Set up event replay capabilities from dead letter storage to recover lost processing. | Lets you quickly activate consumers during regional disasters and helps you meet your RTO and RPO targets through tested procedures. Lets you fully recover processing through replay capabilities. |
| Configure [retry policies](/azure/event-grid/delivery-and-retry) with exponential backoff (1-second start, 24-hour maximum) to handle transient consumer failures. Set up [dead letter destinations](/azure/event-grid/manage-event-delivery#set-dead-letter-location) by using Azure Storage queues or Azure Service Bus queues to preserve undeliverable events. <br><br> Set the event TTL based on business requirements (typically one to seven days) to prevent resource exhaustion. Use subscription filtering to send event types to appropriate destinations and minimize the blast radius. | Lets the platform automatically recover from transient failures without manual intervention. Preserves events during long outages so you can reprocess and analyze them later. <br><br> Contains faults through event routing and TTL to prevent system-wide overload. |
| Implement [circuit breaker patterns](/dotnet/architecture/microservices/implement-resilient-applications/implement-circuit-breaker-pattern) by using resilience libraries like Polly for .NET or resilience4j for Java to prevent cascading failures. Configure the circuit breaker to stop sending requests to a failing consumer after a specific number of failures, typically five consecutive failures. <br><br> Design fallback mechanisms so that critical events keep processing when non-essential dependencies fail. Set up health check endpoints for consumer monitoring so that traffic can automatically reroute. | Stops cascading failures by automatically isolating dependencies when consumers fail. Keeps critical events processing through fallback mechanisms during partial system failures. <br><br> Enables automatic recovery and intelligent traffic management through health-based routing. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Event Grid.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture.

> [!div class="checklist"]
>
> - **Establish a security baseline for deployments:** Review the [security baseline](/security/benchmark/azure/baselines/event-grid-security-baseline) and use it to set up controls for your operations, data protection, and access management. Your implementation should include identity and access management, network security, encryption, and logging.
>
>    Use the baseline guidance for Event Grid-specific needs like topic access control and subscription security. Align with Microsoft Cloud Security Benchmark controls to keep your security posture consistent. Review the baseline regularly as your requirements and threats change.
>
> - **Implement segmentation controls for event-driven workload isolation:** Establish identity as your primary security boundary by using Azure role-based access control (RBAC) to control access to Event Grid resources. Assign RBAC built-in roles, including Event Grid Contributor, Event Grid EventSubscription Contributor, and Event Grid Data Sender.
>
>    Use separate topics to isolate different application domains and business functions. Set up subscription-level access controls so that only authorized consumers can receive events. To create clear separation and access boundaries, organize your Event Grid resources by using resource groups and subscriptions.
>
>    Keep publisher and subscriber identities separate by using managed identities and service principals. Set up event filtering and routing to minimize the blast radius and route events only to their intended consumers.
>
> - **Integrate with Microsoft Entra ID for centralized identity and access management:** Use Microsoft Entra ID to handle authentication and authorization for Event Grid. Set up managed identities for service-to-service authentication so you don't have to store credentials. Configure Microsoft Entra Conditional Access policies that respond to user context, device status, and network location.
>
>    Keep data plane permissions (publishing and consuming events) separate from control plane permissions (managing resources). Apply just-in-time and just-enough access principles for administrative operations.
>
>    Set up audit trails through the activity log in Azure Monitor to track identity access and authorization decisions. Use Microsoft Entra ID groups to assign roles and manage access.
>
> - **Apply network security controls for secure event communication:** Set up private endpoints for Event Grid topics to eliminate internet exposure and integrate private endpoints with your virtual network. Configure IP address filtering and firewall rules at the topic level to allow access only from authorized source addresses and ranges.
>
>    Plan your virtual network integration to connect publishers, Event Grid, and consumers within your network boundaries. Use network security groups (NSGs) and route tables to control traffic flow and create micro-segmentation.
>
>    Set up network monitoring and logging to track Event Grid traffic patterns and connection attempts.
>
> - **Implement data encryption for event protection:** Event Grid automatically encrypts data at rest by using Microsoft-managed platform keys for stored events and metadata. Require Transport Layer Security (TLS) 1.2 as the minimum version for all Event Grid communications, including publishing and consuming events. Set up certificate management for TLS connections to automate renewal and validation.
>
>    The platform encrypts temporary event storage during processing and delivery retry scenarios. Event Grid topics and domains don't support customer-managed keys (CMKs).
>
> - **Harden Event Grid configurations to minimize the attack surface:** Minimize your Event Grid attack surface by systematically hardening configurations:
>
>   - Disable unnecessary authentication methods and protocols. Keep only required mechanisms.
>
>   - Set up topic and subscription settings by following least-privilege principles. Remove unused capabilities.
>   - Remove or disable legacy protocol support and authentication methods that create security risks.
>   - Configure resource-level settings with security-focused defaults for timeout values and retry policies.
>   - Use secure naming conventions and organize resources to avoid leaking information.
>   - Configure minimal required permissions for Event Grid service principals and managed identities.
>   - Assign Azure Policy built-in policies for Event Grid at the subscription level, for example *Azure Event Grid topics should use private link*.
>
> - **Secure secrets and credentials for Event Grid integrations:** We strongly recommend using Microsoft Entra ID with managed identities because it eliminates the need for stored secrets. If you don't use Microsoft Entra ID, store your connection strings, API keys, and authentication tokens in Azure Key Vault with access controls and rotation policies.
>
>    Use separate keys for different environments and consumers so you don't share credentials. Set up automatic secret rotation for Event Grid access keys and webhook tokens to minimize disruption. Don't hardcode secrets in your event handler applications, configuration files, or deployment pipelines.
>
>    Monitor secret access patterns through Key Vault auditing and set up alerts for unusual attempts. Create recovery procedures for compromised credentials, including how to quickly regenerate keys.
>
> - **Implement security monitoring and logging for Event Grid:** Set up Azure Monitor diagnostic settings to capture security-relevant events including access attempts and configuration changes. Implement activity logging for Event Grid operations including topic creation, subscription management, and policy changes. Monitor identity and access patterns through the activity log in Azure Monitor to identify unusual patterns and authorization failures.
>
>    Set up alerts for security events like failed authentication, unauthorized access, and suspicious patterns. Integrate Event Grid logs with Microsoft Sentinel for threat detection and security analytics. Use structured logging with timestamps and event metadata so that you can analyze security problems and investigate incidents.
>
>    Monitor event delivery patterns for unusual behavior that might signal security incidents. Set up log retention policies that meet your organization's security and compliance requirements.
>
> - **Validate security controls through systematic testing and assessment:** Run regular validation tests on Event Grid access controls, network security, and encryption settings. 
>
>   - Do penetration testing that focuses on endpoints, authentication mechanisms, and attempts to bypass access controls. 
>
>   - Test network security controls like private endpoint configuration, IP address filtering, and virtual network integration. 
>   - Test identity and access management, including RBAC assignments, Conditional Access policies, and managed identity authentication. Verify that events remain secure throughout the entire pipeline, from publishers through Event Grid to consumers. 
>   - Implement automated security testing in your deployment pipelines to catch misconfigurations.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Assign built-in [RBAC roles](/azure/event-grid/security-authentication) at the appropriate scopes to follow least-privilege access. Assign the [Event Grid Contributor](/azure/role-based-access-control/built-in-roles#event-grid-contributor) role to users who manage topics and subscriptions. Assign the Event Grid EventSubscription Contributor role to users who only manage event subscriptions. And assign the Event Grid Data Sender role for applications that publish events. | Matches access controls with operational responsibilities. Enforces least-privilege access by keeping management and data plane operations separate. |
| Organize [topics](/azure/event-grid/concepts#topics) by using resource groups that match security and operational boundaries. Use naming conventions that reflect security boundaries and access requirements. Set up [subscription filtering](/azure/event-grid/event-filtering) to send events only to authorized consumers. | Separates event flows based on security policies and reduces the blast radius through isolated topic structures. Establishes clear organizational boundaries for compliance. |
| Assign [managed identities](/azure/event-grid/security-authenticate-publishing-clients) to Event Grid topics, subscriptions, and event handlers for Azure service authentication. Use system-assigned identities for single-resource scenarios or user-assigned identities when you need to share identities. Set up managed identities for Azure Functions, Logic Apps, and storage accounts that handle events. | Eliminates stored credentials and their associated security risks. Provides automatic credential rotation through Azure and enables secure service-to-service authentication without manual secret management. |
| Enable [Conditional Access policies](/azure/active-directory/conditional-access/overview) for Event Grid management operations. Require multifactor authentication (MFA) for privileged operations like topic creation and policy changes. Set location-based controls that restrict management to authorized regions. <br><br> Make sure devices meet compliance requirements for administrative access. | Applies security controls based on risk and context. Reduces unauthorized access by requiring extra authentication. Lets you adapt security policies as threats change. |
| Deploy [private endpoints](/azure/event-grid/configure-private-endpoints) for Event Grid topics in your virtual network. Set up Domain Name System (DNS) settings for proper name resolution. <br><br> Set up NSGs and route tables to control traffic flow. | Eliminates internet exposure through network-level isolation and enables secure communication within your virtual network. Provides centralized access control through security groups. |
| Set up [IP address filtering](/azure/event-grid/configure-firewall) rules at the topic level by using an allow list. Deny access by default and only allow authorized source addresses and Classless Inter-Domain Routing (CIDR) ranges. Set up monitoring and alerting for blocked access attempts. | Controls network access at the service level and reduces your attack surface by limiting access to known sources. Helps you detect unauthorized access attempts. |
| Require [TLS 1.2](/azure/event-grid/transport-layer-security-configure-minimum-version) as the minimum version for all Event Grid communications. Set up TLS settings for custom domain endpoints and webhook destinations. Implement certificate validation for webhook endpoints that receive events. | Protects against protocol downgrade attacks by using industry-standard encryption for data in transit. Prevents man-in-the-middle attacks through certificate validation. |
| Use [Azure Policy](/azure/governance/policy/samples/built-in-policies#event-grid) to enforce security configuration standards and prevent drift. Use IaC for consistent security configurations across environments. Disable unnecessary authentication methods and legacy protocol support. Set minimum required timeout values and retry policies. <br><br> Monitor configuration changes and set up automated alerts for unauthorized modifications. | Reduces your attack surface by removing unnecessary features and prevents configuration drift by enforcing policies. Keeps your security posture consistent across environments and helps you quickly detect unauthorized changes. |
| Store Event Grid [client credentials](/azure/event-grid/security-authenticate-publishing-clients) in Key Vault with RBAC access controls. Enable automated secret rotation for access keys and webhook tokens. <br><br> Use separate keys for each environment and application. | Provides secure storage with access control and auditing. Automates credential life cycle management and keeps access separate through environment-specific keys. |
| Enable [diagnostic settings](/azure/event-grid/monitor-event-delivery) to send Event Grid logs to your Log Analytics workspace. Set up alert rules for failed authentication, unauthorized access, and configuration changes. Integrate with Microsoft Sentinel for threat detection and security analytics. <br><br> Set log retention policies that meet your compliance requirements. | Provides visibility into security events and potential threats. Lets you respond quickly to incidents through automated alerting and supports compliance through structured logging and retention. Detects threats through security analytics. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Event Grid and its environment.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Analyze pricing models and cost structures to optimize Event Grid expenses:** Evaluate Event Grid Basic and Standard tiers to understand how pricing models affect your solution costs. The Basic tier uses per-operation pricing, which provides predictable costs for low-volume, simple push delivery scenarios. The Standard tier combines pre-purchased throughput units (TUs) with per-operation charges.
>
>    Each TU provides fixed capacity for sessions, message ingress per second, and topics per namespace. TUs have a fixed hourly cost regardless of usage. Under-provisioning causes throttling and service degradation. Over-provisioning wastes money on unused capacity.
>
>    MQTT operations cost more than event operations. Plan for cross-region data transfer bandwidth charges when you deliver events to different Azure regions. Include event retention and dead letter storage costs on top of the base Event Grid pricing.
>
> - **Optimize capacity and throughput for cost-efficient event processing:** Configure event batching to deliver multiple events in single operations and reduce per-operation costs. Optimize batch size during load testing. Apply event filtering at the subscription level to prevent unnecessary processing and delivery of irrelevant events. Configure dead letter handling and retry policies to manage processing attempts and storage costs.
>
>    Define event retention policies to balance data availability with storage costs. Plan capacity to account for peak event volumes and processing patterns.
>
> - **Eliminate resource waste and optimize configurations:** Systematically remove Event Grid resource waste through optimization practices:
>
>   - Remove unused Event Grid topics, subscriptions, and endpoints that generate costs without value.
>
>   - Clean up abandoned resources including dead letter destinations and unused custom topics.
>   - Configure event retention policies to balance data availability with storage costs.
>   - Optimize delivery settings, retry policies, and filtering rules to improve cost efficiency.
>   - Do regular resource audits to identify underutilized resources that you can consolidate or decommission.
>   - Consolidate similar subscriptions to reduce management overhead.
>
> - **Implement cost monitoring and control mechanisms:** Monitor usage patterns, operation volumes, and spending trends to establish cost visibility. Use cost analytics to identify cost drivers including event volumes, delivery attempts, and cross-region data transfer.
>
>    Set up budget alerts, spending limits, and automated policies to control costs. For the Standard tier, monitor TU utilization percentages to find rightsizing opportunities and set up alerts for utilization thresholds. Enable real-time monitoring to catch cost anomalies early.
>
>    Implement cost reporting to get detailed visibility by resource, environment, and business unit. To help predict future costs, run historical analysis to identify trends and seasonal patterns.
>
> - **Establish cost governance policies and controls:** Define cost governance by using organizational policies to encourage responsible spending. Use resource policies to enforce cost-conscious configurations and prevent expensive misconfigurations. 
>
>    Create approval workflows for high-cost deployments and feature usage. Run regular cost reviews and optimization assessments to maintain cost discipline. Apply policy-based controls to enforce tagging requirements, resource limits, and cost allocation standards.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Use the [tier comparison guidance](/azure/event-grid/choose-right-tier) to choose your Event Grid tier based on required features and cost constraints. Check usage patterns regularly to validate your tier selection. | Prevents paying for unused premium features and matches tier capabilities to your usage patterns. |
| Set up [event batching](/azure/event-grid/delivery-and-retry#output-batching) to deliver multiple events in single operations. Apply [event filters](/azure/event-grid/event-filtering) at the subscription level to process only relevant events. | Reduces per-operation costs through batched delivery and eliminates the cost of processing irrelevant events. |
| Monitor event processing patterns and peak usage by using [event delivery monitoring](/azure/event-grid/monitor-event-delivery) to establish baseline capacity. Use throughput metrics to rightsize your resources. <br><br> Account for seasonal variations and growth projections when you plan capacity. | Prevents over-provisioning. Ensures that you have enough performance for peak loads by adjusting capacity based on your usage patterns. |
| Optimize delivery configurations, like timeout and retry settings, to balance reliability with cost. For more information, see [Delivery and retry](/azure/event-grid/delivery-and-retry). Use [event filtering](/azure/event-grid/event-filtering) to configure dead letter policies. | Reduces retry attempt costs and maintains reliability while minimizing storage costs and preserving error visibility. |
| Use [Microsoft Cost Management](/azure/cost-management-billing/costs/quick-acm-cost-analysis) to track spending patterns and implement cost analytics. Set up automated cost reporting that organizes costs by resource, environment, and business unit. <br><br> Use cost analysis to identify high-cost operations and optimization opportunities. | Provides visibility into spending patterns. Helps you assign costs to teams and identify high-cost operations. |
| Configure [cost alerts](/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending) that include multiple threshold levels at the subscription, resource group, and topic levels. Enable cost anomaly detection to identify unusual spending patterns. Set up [budget limits](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) with automated alerts and spending caps. <br><br> Implement budget forecasting based on your processing trends. | Prevents cost overruns with early warnings at multiple organizational levels, catches configuration problems or unusual usage patterns, and helps you plan for future spending based on predicted growth. |
| Apply [resource tags](/azure/azure-resource-manager/management/tag-resources) by using consistent naming conventions, like environment, application, business unit, cost center, and project. Add tags at the topic, subscription, and endpoint levels for granular tracking. | Lets you assign costs to specific business units and projects for chargeback and helps you identify high-cost event processing patterns. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**. The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Event Grid.

> [!div class="checklist"]
>
> - **Assess team readiness for Event Grid operations:** Develop your team's operational knowledge in event schema design, routing configuration, consumer patterns, dead letter handling, retry policies, and subscription management. 
>
>    Create documentation for schema design guidelines, subscription configuration patterns, and troubleshooting runbooks. Document event routing patterns, endpoint requirements, and error handling strategies. 
>
>    Establish standards for schema versioning and consumer deployment procedures. Define service life cycle management processes, including topic provisioning, subscription life cycle, and decommissioning workflows.
>
> - **Establish safe deployment practices:** Implement blue-green deployment by using parallel topic configurations with traffic routing. Use progressive deployment with schema versioning in event payloads and staged consumer rollouts. 
>
>    Define rollback procedures for rapid recovery through configuration reversion and deployment rollback. Set up quality gates including schema validation, endpoint health checks, and delivery verification.
>
> - **Implement IaC for resource management:** Use IaC tools like Azure Resource Manager templates (ARM templates), Bicep, and Terraform to set up resources and manage configurations. Configure parameter files for environment-specific settings like topic naming, subscription filters, and security policies. Include topics, subscriptions, event filters, dead letter destinations, and security settings in your configuration.
>
>    Implement layered deployment to handle dependencies between Event Grid resources, consumer services, and identity providers. Set up drift detection to catch deviations from your desired subscription filters, retry policies, and security settings.
>
> - **Design build and deployment pipelines for workloads:** Design pipeline architecture to coordinate topic deployment, subscription configuration, and consumer application releases. Implement environment-specific deployment for different configurations like topic naming, subscription filters, and security policies.
>
>    Use CI/CD tools like Azure DevOps and GitHub Actions to support resource deployment and consumer coordination. Set up security gates like schema validation, endpoint security verification, and compliance checks.
>
>    Implement artifact management for configuration templates, schema definitions, and consumer deployment packages. Set up monitoring integration for visibility into deployment success and post-deployment health.
>
> - **Implement monitoring and logging for operations:** Define an observability strategy to track delivery metrics, consumer processing telemetry, and end-to-end event flow. Monitor service metrics like delivery success rates, retry counts, dead letter volumes, and processing latency. Implement structured logging to capture event correlation IDs, processing results, error details, and troubleshooting context.
>
>    Integrate Azure Monitor to get Event Grid analytics like operational trends and insights. Configure an alerting framework to support pillar-specific alerts for reliability, performance, security, and cost.
>
>    Enable distributed tracing for end-to-end visibility from event publication through consumer processing. Create monitoring dashboards for real-time visibility into workload operations and Event Grid service health.
>
> - **Establish operations and emergency response procedures:** Define incident response processes for delivery failures, consumer outages, regional service disruptions, and cascading failures. Establish failover operations for regional outages through cross-region topic replication and consumer coordination.
>
>    Create emergency response procedures for critical event delivery scenarios including business-critical processing and restoration priorities. Define communication protocols to coordinate incident response across distributed teams that manage infrastructure and applications.
>
> - **Automate Event Grid management and support operations:** Automate administrative tasks like topic creation, subscription management, event filter configuration, and dead letter setup. Automate support operations like troubleshooting data collection, log aggregation, event replay, and maintenance tasks. Configure drift detection to monitor subscription filters, retry policies, and security settings.
>
>     Use Automation, Logic Apps, and Event Grid APIs to build your automation. Create reusable automation modules that you can share across workloads.
>
> - **Implement testing and validation strategies:** Define testing strategies like schema validation, endpoint verification, end-to-end processing, and integration testing. Implement test automation frameworks that continuously validate schema compatibility and consumer processing.
>
>    Create test environments that match your production setup, including topic configurations, subscriptions, and consumer deployments. Apply production testing approaches like canary event publishing, consumer health validation, and controlled event replay. Use testing tools like schema validation frameworks, endpoint testing utilities, and simulation capabilities.
>
>    Test your rollback procedures to make sure you can recover through configuration reversion and deployment rollback.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Develop [IaC](/azure/templates/microsoft.eventgrid/namespaces) modules by using [ARM templates, Bicep, or Terraform](/azure/azure-resource-manager/bicep/) for topics, subscriptions, domains, and security configurations. Create modular designs that have parameters for different scenarios and environment-specific settings. | Provides consistent deployments across environments, lets you reuse components, and simplifies maintenance. |
| Configure [diagnostic settings](/azure/event-grid/monitor-event-delivery) to route Event Grid system topics, custom topics, and domain logs to your [Log Analytics workspace](/azure/azure-monitor/logs/log-analytics-overview). Enable resource logs for data plane operations like event delivery attempts and webhook responses. <br><br> Create monitoring dashboards by using [Azure Monitor workbooks](/azure/azure-monitor/essentials/metrics-supported#microsofteventgridtopics) that show metrics like delivery success rates, retry counts, dead letter volumes, and processing latency. Configure [alerting rules](/azure/azure-monitor/alerts/alerts-overview) for operational health problems like delivery failures, high latency, and dead letter accumulation. | Provides visibility into event delivery performance and service health. Helps you detect problems early and troubleshoot quickly through centralized logging and alerting. |
| Monitor delivery failures, latency degradation, and dead letter queue accumulation by using [Azure Monitor alerts](/azure/azure-monitor/alerts/alerts-overview). Use [metric-based alert rules](/azure/azure-monitor/alerts/alerts-create-metric-alert-rule) to track these problems. Set up [action groups](/azure/azure-monitor/alerts/action-groups) to automatically respond by sending notifications, running Logic Apps workflows, or invoking runbooks. <br><br> Implement [intelligent alerting](/azure/azure-monitor/alerts/alerts-overview) by using dynamic thresholds. This feature learns your normal patterns and only alerts when metrics deviate from the baseline, which reduces false alarms. Suppress alerts during planned maintenance windows and deployments to reduce notifications about expected changes. | Helps you respond quickly to operational problems through automated alerts and actions. Intelligent alerting reduces false alarms and operational overhead while still keeping you informed about service health. |
| Develop incident response procedures for delivery failures, consumer outages, and regional failovers. Create [runbooks](/azure/automation/automation-runbook-types) that automate common tasks like processing dead letter queues, rolling back configurations, and restoring service. <br><br> Establish communication protocols that define who to escalate to, how to notify stakeholders, and how to report status. Define RTOs and procedures for different failure scenarios, from partial outages to complete service disruptions. | Enables coordinated incident response that minimizes service impact and recovery time. Provides standardized procedures that ensure consistent response across different failure scenarios. |
| Automate administrative tasks like managing subscriptions, provisioning topics, and maintaining configurations by using [Automation](/azure/automation/), [Logic Apps](/azure/logic-apps/), and [Event Grid APIs](/azure/event-grid/sdk-overview). Create reusable automation modules for common operations like filter updates, endpoint configuration, and security policy application. <br><br> Use Azure Policy to detect [configuration drift](/azure/governance/policy/how-to/get-compliance-data) on subscription filters, retry policies, and security settings. Implement automated remediation to fix these deviations without manual intervention. | Reduces manual operational effort while ensuring configuration consistency and compliance. Lets you proactively manage configuration to prevent operational problems through automated monitoring and remediation. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Event Grid.

> [!div class="checklist"]
>
> - **Plan capacity for event throughput and scalability:** Estimate event volume requirements based on your business needs, like peak event rates, message sizes, and subscription patterns. Analyze historical event data trends, seasonal variations, and business expansion projections to plan for growth. Incorporate service limits and quotas in your design, including events per topic, subscription limits, and regional throughput caps.
>
>    Account for consumer processing capacity when you plan throughput. Slow consumers create backpressure that affects system performance.
>
>    Before production, validate your capacity estimates through load testing and proof-of-concept deployments.
>
> - **Define scaling strategies for topics and consumers:** Choose between two scaling approaches. Use topic scaling or consumer scaling based on your workload characteristics. For topic scaling, distribute load across multiple topics when you approach throughput limits. Organize topics by functional boundaries or geographic regions. For consumer scaling, use Functions premium plans, Azure Container Apps, or AKS to automatically scale based on queue depth and processing demand.
>
>    Configure subscription filtering and routing to distribute load across consumers. To design scaling automation, use Azure Monitor metrics and autoscale capabilities.
>
>    Test scaling effectiveness through load testing to validate triggers, delays, and performance impact.
>
> - **Monitor performance for event delivery:** Establish performance baselines like latency thresholds (p50, p95, p99 percentiles), throughput targets, and processing time benchmarks. Monitor capacity utilization to determine when to adjust scaling or optimization. Monitor bottlenecks across the delivery pipeline, including publisher performance, service latency, network connectivity, and consumer processing time.
>
>    Configure alerting for performance degradation, including latency spikes, throughput reduction, and processing slowdowns. Analyze performance trends to identify patterns, seasonal variations, gradual degradation, and optimization opportunities.
>
>    Use distributed tracing to analyze performance at the transaction level and identify bottlenecks across the event delivery pipeline.
>
> - **Do performance testing for event delivery patterns and consumer capacity:** Design test scenarios that simulate normal load, peak load, and stress conditions with realistic event publishing patterns. Match your production configuration as closely as possible to get meaningful results. Use load testing tools to test delivery performance, including burst traffic and sustained load.
>
>    Run systematic tests to establish baselines. This approach helps you understand service capabilities, latencies, and throughput limitations. Test your autoscaling setup to make sure it triggers correctly and distributes load properly under different conditions.
>
>    Analyze your results to identify bottlenecks, scaling delays, and optimization opportunities.
>
> - **Optimize event delivery and consumer performance configurations:** Configure delivery settings, like retry policies, timeouts, and dead letter destinations, to maximize success rates while minimizing latency. Implement subscription filters to reduce unnecessary processing and deliver only relevant events. To maximize throughput, optimize consumer patterns, including batching, parallel processing, and connection pooling.
>
>    Optimize payloads through efficient serialization, compression, and schema design. Monitor and optimize performance by using metrics, telemetry, and testing results.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Create multiple [topics](/azure/event-grid/custom-topics) and organize them by functional boundaries, geographic regions, or event types. Configure topic naming conventions and routing logic for automatic topic selection. <br><br> Monitor topic-level metrics to identify when topics approach their [throughput limits](/azure/event-grid/custom-event-quickstart). | Overcomes single topic throughput limitations and enables logical separation that improves maintainability and security. Lets you scale specific event domains independently. |
| Design your consumer applications to scale automatically based on Event Grid event volume. For push delivery, implement consumer applications by using the [Functions Premium or Dedicated plan](/azure/azure-functions/functions-premium-plan) to scale based on incoming events. <br><br> For pull delivery with namespace topics, design consumer applications that have dynamic scaling capabilities. Configure Event Grid [retry policies and dead-letter destinations](/azure/event-grid/delivery-and-retry) to ensure reliable delivery during consumer scaling operations. Use [subscription filters](/azure/event-grid/event-filtering) to distribute events across multiple consumer instances. | Consumer applications handle traffic spikes through autoscaling. Ensures resource efficiency through dynamic scaling. Event Grid retry and dead-letter features improve resilience during scaling transitions. |
| Configure [Azure Monitor](/azure/event-grid/monitor-event-delivery) to collect performance metrics, like `DestinationProcessingDurationInMs`, `PublishSuccessDurationInMs`, and delivery latency percentiles. Set up custom metrics by using Application Insights for consumer-specific processing times and end-to-end latency. <br><br> Create performance dashboards by using [Azure Monitor workbooks](/azure/azure-monitor/essentials/metrics-supported#microsofteventgridtopics) that display latency trends, throughput rates, and processing time distributions. | Enables performance optimization through latency and throughput analysis. Identifies performance trends and degradation patterns. Supports capacity planning through [throughput monitoring](/azure/event-grid/quotas-limits). |
| Configure [alerts](/azure/azure-monitor/alerts/alerts-overview) on latency degradation, including when p95 and p99 percentile thresholds exceed your performance baselines. Set up [dynamic thresholds](/azure/azure-monitor/alerts/alerts-create-new-alert-rule) to detect throughput reduction and processing slowdowns. <br><br> Implement alerts for consumer processing time increases that indicate performance bottlenecks. Configure alerts for when capacity utilization approaches scaling thresholds. | Detects performance degradation before it affects user experience. Proactively optimizes performance through early detection. Identifies bottlenecks through processing time monitoring. |
| Use [distributed tracing](/azure/azure-monitor/app/distributed-tracing) data to analyze performance bottlenecks across the event delivery pipeline. Monitor transaction-level performance metrics to identify slow dependencies and optimize consumer processing. <br><br> Analyze the [application map](/azure/azure-monitor/app/app-map) to identify high-latency dependencies and optimize event flow paths. Configure performance profiling to get detailed analysis of publisher and consumer processing time. | Identifies specific performance bottlenecks at the transaction level. Targets optimization based on detailed performance analysis. Improves performance through dependency optimization. |
| Use [Load Testing](/azure/load-testing/overview-what-is-azure-load-testing) to generate realistic publishing patterns under various load conditions. Implement custom scripts by using [JMeter](/azure/app-testing/load-testing/how-to-create-and-run-load-test-with-jmeter-script) or K6 to publish events during testing that have realistic sizes and frequencies. <br><br> Set up test scenarios to push your system to its service [quotas and limits](/azure/event-grid/quotas-limits). Measure delivery success rates, latency, throughput, and error rates. | Validates delivery performance before production. Identifies service limits so that you can accurately plan capacity. Provides concrete data to guide optimization decisions. |
| Test autoscaling behavior in realistic workloads by using [Functions](/azure/azure-functions/functions-premium-plan), [Container Apps](/azure/container-apps/scale-app), or [AKS](/azure/aks/concepts-scale). Measure processing latency, queue depth handling, and scaling delays during load variations. <br><br> Validate retry policies, dead-letter handling, and error processing under high load. Verify subscription filtering effectiveness and load distribution across instances. | Prevents performance bottlenecks through validated scaling. Ensures that consumers can handle delivery patterns under high load. Improves confidence in end-to-end processing performance. |
| Optimize how your system handles transient failures to prevent an impact on performance. Configure subscription [retry policies](/azure/event-grid/delivery-and-retry) with exponential backoff optimized for your consumer patterns. Set delivery timeout values based on consumer capabilities and network characteristics. <br><br> Implement subscription [filtering](/azure/event-grid/event-filtering) by using advanced syntax to deliver only relevant events. Configure dead-letter destinations with storage and monitoring. | Speeds up delivery by tuning retries and filtering out unnecessary events. Reduces traffic and processing overhead. Balances reliability and performance. |

## Azure policies

Azure provides an extensive set of built-in policies related to Event Grid and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Event Grid domains use private link
- Event Grid topics use private link
- Logging by category group for Event Grid domains is enabled to Log Analytics
- Logging by category group for Event Grid topics is enabled to Log Analytics

For comprehensive governance, review the [Azure Policy built-in definitions for Event Grid](/azure/governance/policy/samples/built-in-policies#event-grid) and other policies that might affect the security of the messaging infrastructure.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Related content

- [Choose between Azure messaging services: Event Grid, Event Hubs, and Service Bus](/azure/service-bus-messaging/compare-messaging-services)
- [Event-driven architecture style](/azure/architecture/guide/architecture-styles/event-driven)
- [Enterprise integration by using message queues and events](/azure/architecture/example-scenario/integration/queues-events)
