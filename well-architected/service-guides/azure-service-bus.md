---
title: "Architecture Best Practices for Azure Service Bus"
description: "Architecture recommendations for Azure Service Bus messaging infrastructure covering reliability, security, cost optimization, operational excellence, and performance efficiency."
author: PageWriter-MSFT
ms.author: prwilk
ms.topic: concept-article
ms.date: 07/24/2025
ms.subservice: waf-service-guide
products:
 - azure-service-bus
 - azure-messaging
azure.category:
 - integration
 - messaging
---

<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD025 -->

# Architecture best practices for Azure Service Bus

Azure Service Bus is a fully managed enterprise message broker that enables reliable messaging between distributed applications and services. Service Bus provides message queues and publish-subscribe topics with features like sessions, duplicate detection, dead letter queues, and advanced routing capabilities for building resilient messaging solutions.

Successful Service Bus implementation depends on teams having deep expertise in core messaging patterns including queues, topics, subscriptions, and Service Bus-specific features like sessions and dead letter queues. Teams also need good understanding of the Azure messaging ecosystem, including Event Hubs and Event Grid integration patterns, to choose the right service for each messaging scenario.

This article assumes that as an architect, you've reviewed the [messaging decision tree](/azure/architecture/guide/technology-choices/messaging) and chose Azure Service Bus as the messaging solution for your workload.

The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](/azure/well-architected/pillars).

<!-- markdownlint-disable-next-line MD036 -->
**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:  

- Azure Service Bus



## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the nature of your application and the criticality of its components. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Consider limits that might pose design restrictions:** Azure Service Bus [quotas and tier-specific limits](/azure/service-bus-messaging/service-bus-quotas) affects the design. Choose a tier that gives you headroom for message processing capacity and avoids costly redesign. For example, if your evaluation of peak and burst throughput indicate higher limits, then Premium might be suitable. Similarly, for larger payloads, consider the Premium tier.
>
>   Detect and handle [`QuotaExceeded`](/azure/service-bus-messaging/service-bus-messaging-exceptions-latest#reason-quotaexceeded) exceptions in your application logic. If this occurs, drain messages from the queue or check if the message time-to-live settings are causing storage buildup. Use the [Circuit Breaker pattern](/azure/architecture/patterns/circuit-breaker) to avoid continued retries while the quota is exceeded.
>
> - **Anticipate potential failures through failure mode analysis:** Failure mode analysis provides a systematic approach to anticipating potential failure scenarios and developing mitigation strategies to ensure messaging service resilience.
>
>    | Failure | Mitigation |
>    |---------|------------|
>    | Service Bus namespace is not reachable | Use Azure Monitor to set up alerts for Service Bus availability. Catch exceptions from the client SDKand check the [`IsTransient`](/dotnet/api/azure.messaging.servicebus.servicebusexception.istransient) property to determine if a retry is appropriate. Configure geo-disaster recovery to automatically failover to a secondary region during outages. |
>    | Message processing failures create poison messages | Configure dead letter queues to isolate undeliverable messages. Implement client retry policies with exponential backoff and circuit breaker patterns. |
>    | High message volumes exceed throughput limits | Use Azure Monitor to track throughput metrics and set up alerts. Enable auto-scaling for Premium tier messaging units to handle traffic spikes. |
>
> - **Implement redundancy across the solution layers:** Redundancy is a critical technique for ensuring reliable messaging by eliminating single points of failure across multiple layers.
>
>   - Instance-Level Redundancy: At the foundational level, distribute messaging across separate Service Bus instances to achieve instance-level redundancy. This >requires the application to implement custom failover logic or load-balancing strategies across multiple messaging endpoints.
>
>   - Zonal-Level Redundancy: Enable availability zones to protect against datacenter-level failures. In the event of such a failure, the application may experience temporary disruption; however, Service Bus will automatically replicate message data and metadata across multiple availability zones to maintain continuity.
>
>   - Region-Level Redundancy: For workloads demanding even greater resilience, consider implementing region-level redundancy. This can be achieved using active or passive replication patterns:
>
>     - **Active replication**: The client sends every message to both regions. The receiver listens on both. Messages require a unique identifier so the client can discard duplicates.
>     - **Passive replication**: The client sends to one region, falling back to the other on error. The receiver listens on both. This reduces duplicate messages, but the receiver must still handle them.
>
>    Ensure you select a Service Bus tier that supports these redundancy features, such as the Premium tier.
>
> - **Implement disaster recovery and backup strategies:** Multi-region disaster recovery protects against complete regional failures through geo-disaster recovery capabilities. Service Bus Premium tier supports geo-disaster recovery pairing between primary and secondary regions, enabling automatic metadata replication and failover orchestration. Geo-disaster recovery replicates namespace metadata but not message data, while geo-replication provides full message data replication with additional complexity.
>
>    During regional outages, applications can redirect to the secondary namespace using alias connections that automatically point to the active region. Recovery planning must account for message data loss during failover scenarios and recovery time objectives that depend on DNS propagation and application reconnection logic.
>
>    Message-level backup strategies require application-level implementation since Service Bus does not provide built-in message backup capabilities. Configuration backup includes exporting namespace metadata, queue and topic definitions, and access policies to enable rapid namespace recreation during disaster recovery scenarios.
>
>    Disaster recovery requires consideration of message ordering guarantees, duplicate handling during failover scenarios, and application-level retry logic to handle cross-region latency variations.
>
> - **Implement reliable scaling for variable message loads:** Use reliable message processing patterns that depend on partitioning strategies and concurrent receiver configurations to maintain consistent throughput without failure. Configure multiple concurrent receivers per queue to increase processing reliability by distributing load and reducing single points of failure. Session-enabled queues ensure ordered processing but limit parallelism to one active receiver per session, creating potential reliability bottlenecks.
>
>    Use partitioned entities to distribute messages across multiple message brokers for fault tolerance, though they may affect ordering guarantees and transactional consistency. Configure topic subscriptions with message filtering to enable reliable fan-out scenarios that distribute processing across multiple consumer applications without creating infrastructure dependencies.
>
>    Service Bus Premium tier provides dedicated messaging units with predictable capacity and auto-scaling throughput units to handle dynamic message volumes. Each messaging unit delivers guaranteed send and receive limits with automatic scaling that prevents throttling during traffic spikes. Standard tier uses shared infrastructure with variable capacity but lacks dedicated throughput guarantees and auto-scaling capabilities.
>
> - **Implement reliability-focused monitoring and health detection:** To maintain reliable messaging, it's essential to monitor performance traits that may signal potential issues. Focus on key indicators such as:
>
>   - Active message count trends to detect buildup or processing delays.
>   - Server errors which may point to underlying infrastructure problems.
>   - Throttling events indicating capacity constraints or resource saturation.
>
>    Beyond functional metrics, include reliability-focused indicators such as:
>
>   - Message delivery success rates to ensure consistent throughput.
>   - Dead-letter queue accumulation which can reveal persistent delivery failures.
>   - Failover event detection to track resilience during infrastructure disruptions.
>
>    Extend monitoring to dependency health, covering connected applications and external services that publish or consume messages. Because messaging reliability depends on the entire flow chain, thresholds should be configured to trigger alerts for conditions that compromise delivery guarantees, such as rapid dead-letter queue growth or regional failover events.
>
> - **Configure self-preservation mechanisms and resilience patterns:** Service Bus includes built-in self-preservation mechanisms designed to prevent failures from cascading across the messaging infrastructure. These protections can be configured at three levels: message-level, client-level, and namespace-level.
>
>   - Message-Level Protection: Configure dead-letter queues to automatically isolate undeliverable messages, preventing them from blocking healthy message flow. Set parameters such as maximum delivery count and time-to-live (TTL) based on your workload requirements.
>
>     Use `PeekLock` mode (not Receive and Delete) to ensure messages aren't lost if a receiver fails. If the lock expires, the message becomes available to other receivers. If the message exceeds the maximum delivery count, it moves to the dead-letter queue.
>
>   - Client-Level Protection: Use client SDKs with built-in retry policies that include exponential backoff and circuit breaker capabilities to mitigate cascading failures. Implement connection pooling and client caching to reduce resource exhaustion, especially during high-throughput operations.
>
>   - Namespace-Level Isolation: Prevent cross-application failures by using separate Service Bus namespaces for different environments or workload components. Consider  message batching and prefetch settings to reduce network latency.
>

### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Deploy [Premium tier with zone redundancy](/azure/service-bus-messaging/service-bus-premium-messaging) in regions that support availability zones. Premium tier provides the dedicated infrastructure required for zone redundancy capabilities.<br><br>Enable this feature for all production messaging workloads to ensure high availability and automatic zone-level fault tolerance. | Eliminates single points of failure within regional infrastructure while providing automatic failover without manual intervention.<br><br>Dramatically reduces recovery time from minutes to seconds during zone failures, ensuring continuous message processing availability for critical workloads. |
| Configure [geo-disaster recovery pairing](/azure/service-bus-messaging/service-bus-geo-dr) between primary and secondary regions to establish automated failover capabilities. Use alias connections to abstract regional endpoints, allowing applications to connect consistently regardless of active region.<br><br>Plan for DNS propagation time during [failover events](/azure/service-bus-messaging/service-bus-outages-disasters), typically requiring several minutes for complete recovery. | Provides protection against complete regional failures while reducing manual intervention during disaster scenarios.<br><br>Minimizes application changes through alias abstraction and enables rapid recovery with predictable timeframes. |
| Enable [auto-scaling](/azure/service-bus-messaging/automate-update-messaging-units) in [Premium tier](/azure/service-bus-messaging/service-bus-premium-messaging) namespaces to handle variable message loads automatically. Configure scaling based on CPU, memory, and connection metrics with appropriate thresholds to prevent performance degradation. | Supports variable workload patterns without service degradation while eliminating message throttling during traffic spikes. Reduces manual capacity management overhead for operations teams. |
| Enable multiple [concurrent receivers](/azure/service-bus-messaging/service-bus-performance-improvements) to increase message processing throughput and fault tolerance. Configure receiver patterns that balance parallelism with reliability requirements.<br><br>Use [partitioned entities](/azure/service-bus-messaging/service-bus-partitioning) to distribute load across multiple message brokers, understanding that message ordering is guaranteed only within individual partitions. | Distributes load across multiple message brokers for enhanced scalability while increasing overall message throughput through parallel processing capabilities. |
| Set up [Azure Monitor alerts](/azure/service-bus-messaging/monitor-service-bus-reference) for critical Service Bus reliability metrics including dead letter message count thresholds, server error rates above baseline, and throttling events indicating capacity constraints. | Reduces mean time to detection for reliability problems and provides [automated alerting](/azure/azure-monitor/alerts/alerts-create-new-alert-rule) for critical reliability events.<br><br>Enables proactive detection of message delivery issues while identifying capacity constraints before availability impact. |
| Enable [dead letter queues](/azure/service-bus-messaging/service-bus-dead-letter-queues) for all production queues and topics with proper monitoring procedures to track poison message accumulation and implement cleanup strategies. | Prevents poison messages from blocking healthy message processing and supports operational troubleshooting through dead letter queue inspection capabilities. |
| Enable [duplicate detection](/azure/service-bus-messaging/duplicate-detection) to prevent duplicate messages from being *sent* to the queue within a specific history window.<br><br>This feature does not prevent a receiver from processing the same message more than once, so idempotent application logic is still required. | Filters out redundant messages created by producer retries or transient network issues. This feature does not prevent a receiver from processing the same message more than once, so idempotent application logic is still required, but the exposure to duplicate messages is reduced. |
| Use separate namespaces to provide isolation between environments and prevent cross-environment failure propagation. Configure namespace-level security and access controls appropriately.<br><br>Implement Service Bus client [retry policies](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions.retryoptions) with exponential backoff and circuit breaker patterns to handle transient failures gracefully. | Reduces application complexity by handling retries transparently while providing automatic protection against transient service issues.<br><br>Namespace isolation prevents cross-environment failure propagation and enables independent scaling and management of different workload components. |
| Establish automated Service Bus configuration backup with [Azure Resource Manager templates](/azure/azure-resource-manager/templates/overview) that includes namespace metadata and definitions. Queue definitions, topic subscriptions, and access policies require backup through version control. | Automated backup procedures reduce manual effort and human error while version control for messaging infrastructure changes improves management.<br><br>Reduces recovery time objectives through automation and minimizes human error during recovery procedures. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure Service Bus.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture.

> [!div class="checklist"]
>
> - **Establish a security baseline:** Review the [Azure Service Bus security baseline](/security/benchmark/azure/baselines/service-bus-security-baseline) and incorporate applicable measures into your security foundation. Microsoft Cloud Security Benchmark provides specific security controls for Service Bus that address identity management, network security, data protection, and compliance requirements.<br><br>This standardized approach to security configuration and policy implementation enables compliance with industry standards and regulatory requirements across your messaging infrastructure.
>
> - **Enforce authorization and authentication with Microsoft Entra ID:** Service Bus offers built-in Azure RBAC roles for granular access control to messaging operations, including Azure Service Bus Data Owner, Data Sender, and Data Receiver roles for different permission levels. These data plane roles enable fine-grained control over send, receive, and manage operations with assignments that follow least privilege principles.<br><br>Use managed identities to enable secure authentication managed by Microsoft Entra ID. <br><br>Perform regular role assignment reviews and automated provisioning through group membership to maintain security posture over time.
>
> - **Implement secure network configurations:** Network security controls should be implemented in layers for defense-in-depth protection.  Use private endpoints to enable secure connectivity by creating private IP addresses for Service Bus namespaces, ensuring message traffic remains within the Azure backbone network. Alternatively service endpoints can be considered as they provide network integration without private IP addresses. For most granular control, IP firewall rules and virtual network rules should be set to restrict access to specific client IP ranges and subnets.<br><br>Disable public network access when private connectivity is established to eliminate internet-based attack vectors, while network security group rules provide additional filtering for subnets containing Service Bus consumers and producers.
>
> - **Encrypt data at rest and in-transit:** Encrypt with AMQP (Advanced Message Queuing Protocol) over TLS for secure messaging with authentication capabilities. Transport Layer Security TLS 1.2 or higher protects message data during transmission between clients and Service Bus.<br><br>Service Bus automatically encrypts data at rest using platform-managed keys where Azure handles key creation, rotation, and management. For enhanced control, Premium tier supports integration with Azure Key Vault for customer-managed key encryption with established key rotation policies.
>
> - **Harden namespace configuration and reduce attack surface:** Remove unnecessary queues, topics, and subscriptions no longer required to prevent unauthorized access to unused messaging entities. Configure appropriate message retention policies and auto-delete settings to prevent indefinite storage of sensitive message data.<br><br>Enable only required messaging patterns for the workload architecture while disabling features like message forwarding unless specifically needed. Additionally, conduct regular reviews of namespace configuration to maintain security posture over time.
>
> - **Secure connection strings and access credentials:** Prefer identity-based connections in applications to eliminate connection string storage because they contain sensitive authentication. Some scenarios may require connection strings, for those have secure handling mechanisms in place.<br><br>Azure Key Vault integration enables centralized storage and access control, reducing credential exposure risk in application configuration while providing audit logging and access policies. Rotate shared access keys regularly through key rollover procedures that minimize service disruption.
>
> - **Implement security monitoring and threat detection:** Focus security monitoring on threat detection, unauthorized access attempts, and suspicious messaging patterns including failed authentication attempts, privilege escalation, and anomalous messaging behavior. Diagnostic logs should capture authentication events and authorization failures while administrative operations provide security audit trails.<br><br>Azure Monitor integration includes authentication failures, throttling events, and unusual message volume patterns. Implement automated alerting and response procedures for security events with integration into Azure Sentinel or SIEM solutions for correlation across security infrastructure.
>
> - **Establish security testing and validation practices:** Identify potential security gaps in messaging infrastructure through security testing that validates Service Bus configuration against security baselines and verifies organizational security policies compliance.<br><br>Ideally, security testing should be integrated into deployment and operations workflows with automated security scanning tools assessing network configuration and access controls.<br><br>Test scenarios should address various attack vectors including validating DLQ policies prevent message loss without exposing sensitive data, confirming poison messages don't cause infinite retries, and verifying idempotency handling. Additionally, simulate DoS attacks to test throttling, inject malformed messages to test validation, and simulate unauthorized access attempts.

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Assign appropriate Azure RBAC roles based on the principle of least privilege. Use [Data Sender, Data Receiver, or Data Owner roles](/azure/service-bus-messaging/service-bus-managed-service-identity) for specific messaging operations. | Provides centralized access control through Azure RBAC for messaging operations while reducing attack surface by eliminating credential exposure in applications. This approach enables conditional access policies and security monitoring for comprehensive protection. |
| Configure [private endpoints](/azure/service-bus-messaging/private-link-service) for Service Bus Premium namespaces and disable public network access to ensure private network connections. Note that this requires Premium tier Service Bus namespaces for [private endpoint support](/azure/private-link/private-endpoint-overview). | Reduces attack surface for messaging infrastructure significantly as private endpoints eliminate exposure to public internet threats. This provides the highest level of network security for Service Bus communications with integration into existing virtual network security policies and controls. |
| Implement [IP firewall rules](/azure/service-bus-messaging/service-bus-ip-filtering) to restrict namespace access to known client IP ranges and configure [virtual network rules](/azure/service-bus-messaging/service-bus-service-endpoints) to allow access only from approved subnets. This approach can be combined with private endpoints for layered network security. | Limits Service Bus access to authorized network locations, reducing unauthorized access risk. Enables audit logging of network access attempts for security monitoring while integrating with existing network security policies and governance frameworks. |
| Enable [customer-managed key encryption](/azure/service-bus-messaging/configure-customer-managed-key) for Service Bus Premium namespaces using Azure Key Vault for encryption key management and access control. Implement [key rotation policies](/azure/key-vault/keys/how-to-configure-key-rotation) with appropriate access controls. | Customer-managed keys provide enhanced control over encryption key lifecycle and enable regulatory standards compliance through customer-controlled key management. Automated key rotation maintains service availability while updating encryption keys. |
| Enforce TLS 1.2 minimum version for all Service Bus client connections and disable legacy authentication mechanisms such as older shared access signature formats.<br><br>Modern authentication protocols reduce vulnerability to protocol-based attacks while protocol hardening reduces attack surface and vulnerability exposure. | Strong transport encryption and modern authentication protocols protect message data while messaging communications meet compliance requirements for data transmission security. Protocol hardening eliminates known vulnerabilities in legacy protocols. |
| Store Service Bus connection strings and shared access keys in [Azure Key Vault](/azure/key-vault/secrets/about-secrets) with appropriate access policies for credential access control. Implement key rotation procedures using automated deployment pipelines. | Centralized secret management eliminates credential exposure in applications and prevents credential storage in application configuration and source code repositories. Additionally, provides audit logging and access control for sensitive authentication information. |
| Configure [Azure Monitor diagnostic settings](/azure/service-bus-messaging/monitor-service-bus-reference) to capture Service Bus security events including authentication events, authorization failures, and administrative operations. Integrate security logs with Azure Sentinel for threat detection capabilities. | Security-focused monitoring enables rapid detection of unauthorized access attempts while suspicious messaging patterns detection helps identify potential security incidents. Integration with SIEM solutions enables correlation with broader security monitoring. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure Service Bus and its environment.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Choose the right Service Bus service tier:** Service Bus provides multiple pricing tiers with different cost structures and capabilities. Factor in technical requirements that demand sessions, duplicate detection, and geo-disaster recovery in your selection of Service Bus tiers. Avoid paying more for higher tiers if your design doesn't need it.
>
>    In tier decisions, also consider capacity planning that accounts for peak volumes, concurrent connections, and message sizes. Premium tier delivers dedicated messaging units with predictable monthly costs, making it suitable for production workloads. Standard tier offers cost savings for non-production environments.
>
> - **Establish Service Bus usage forecasting and cost modeling methodologies:** Identify cost drivers when forecasting Service Bus expenses. Usage patterns like event-driven processing, job queues, and publish-subscribe scenarios have distinct cost characteristics. Take into consideration the operation counts and message storage duration to build a realistic cost models.
>
>    Account for demand fluctuations including seasonal variations and business growth projections. Include operational overhead from disaster recovery testing and other administrative activities in your forecasting to avoid budget surprises.
>
> - **Implement cost monitoring and budget alerting for Service Bus resources:** Review cost breakdowns by Service Bus namespace to understand spending patterns. Monitor usage charges including message operations, storage consumption, and network egress fees. These metrics reveal optimization opportunities and cost trends.
>
>    Configure budget alerts with multiple threshold levels for early warning before significant overruns. Cost alerts can trigger scaling adjustments or emergency budget controls. Trigger alerts at 50%, 75%, and 90% of budget and make proactive cost management decisions. Establish alert handling procedures that include automated responses and notification workflows. Integrate approval workflows for high-cost deployments to prevent unexpected spending.
>
>    Implement cost allocation strategies for shared namespaces serving multiple applications. Resource tagging enables chargeback models through detailed monitoring, ensuring accurate cost attribution across teams and projects.
>
> - **Rightsize allocated resources maximizing their usage:** Analyze resource utilization to identify optimization opportunities. Review message throughput patterns, queue depth trends, and connection utilization to find unused or underutilized resources. Disable unnecessary features and consolidate underutilized namespaces. Here are some typical areas to consider:
>
>    - Optimize storage costs. Implement dead letter queue management to prevent indefinite accumulation of failed messages. Configure message retention policies based on actual requirements. Set duplicate detection windows according to real needs rather than maximum values.
>
>    - Reduce network and data transfer costs. Batch multiple messages into single operations to reduce billable operations. Co-locate Service Bus namespaces with consuming applications to eliminate inter-region data egress charges.
>
>    - Implement message compression and efficient serialization to minimize payload sizes. This reduces both storage costs and network transfer charges while improving throughput.
>
>    - Adjust capacity dynamically based on usage patterns. Monitor seasonal variations and business cycle fluctuations to inform capacity planning. Proactive adjustments prevent over-provisioning during low-demand periods while ensuring adequate capacity during peaks.
>
> - **Implement Azure Policy controls for Service Bus cost management:** Implement Azure Policy controls to enforce cost management standards. Define policies that restrict Service Bus tier deployments, limit deployments to cost-effective regions, and control expensive features like geo-disaster recovery. These guardrails prevent unauthorized high-cost configurations.
>
> - **Optimize Service Bus configurations across different environments:** Design automated environment provisioning with cost-optimized defaults appropriate for each environment. Templates should include sensible configurations that balance functionality with cost efficiency based on environment purpose.
>
>    Match Service Bus capabilities to actual environment requirements. Production needs zone redundancy, geo-disaster recovery, and dedicated throughput. Non-production environments can use simplified feature sets, lower throughput, and reduced availability guarantees to minimize costs.
>
> - **Implement Service Bus consolidation strategies for cost reduction:** Share Service Bus resources across applications when security and operational boundaries permit. Consolidated namespaces improve messaging unit utilization through resource sharing, reducing overall costs. Implement resource tagging to enable cost allocation across departments and projects, supporting chargeback models in multitenant environments.
>   
<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Implement consistent tagging strategies for Service Bus namespaces. Include [cost center, project, and environment tags](/azure/azure-resource-manager/management/tag-resources) to enable accurate cost allocation and support chargeback models. | Tagging enables cost attribution across shared infrastructure and supports structured chargeback models for multi-tenant Service Bus deployments. |
| Use [Azure Cost Management](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) to analyze message volume patterns and operation counts. Review historical data to identify optimal tier selection for current and projected usage patterns.<br><br>Configure budget alerts with multiple thresholds at 50%, 75%, and 90% of budget limits. Progressive alerts provide early warning before significant overruns occur. | Data-driven tier selection ensures optimal cost efficiency by matching pricing models to actual usage patterns rather than assumptions. Evidence-based decisions prevent over-provisioning.<br><br>Multi-threshold budget alerts provide a graduated warning system that enables proactive cost management without unnecessary disruption to operations. |
| Configure [message retention policies](/azure/service-bus-messaging/message-expiration) based on actual application requirements rather than using maximum values. Set [duplicate detection windows](/azure/service-bus-messaging/duplicate-detection) according to real business needs to balance functionality with cost efficiency. | Eliminates unnecessary message persistence and processing overhead while balancing duplicate prevention requirements with cost efficiency through appropriate configuration. |
| Implement [message batching](/azure/service-bus-messaging/service-bus-dotnet-get-started-with-queues?tabs=passwordless#send-messages-to-the-queue) in Service Bus client applications to reduce billable operations. Configure batch sizes based on message volume patterns and latency requirements to optimize costs. | Reduces operation counts and improves cost efficiency in pay-per-operation tiers. Reduces costs for high-volume messaging workloads through optimized operation charges. |
| Optimize [message payload sizes](/azure/service-bus-messaging/service-bus-messages-payloads) through compression and efficient serialization formats. Smaller payloads reduce storage costs, network egress charges, and improve throughput. | Reduces storage costs and network egress charges while enabling more messages per messaging unit for better capacity utilization and cost efficiency. |
| Implement automated [dead letter queue cleanup](/azure/service-bus-messaging/service-bus-dead-letter-queues) procedures to prevent unbounded storage accumulation. Configure scheduled processes to remove older failed messages based on age or resolution status.<br><br>Balance cleanup frequency with troubleshooting requirements. Maintain sufficient retention periods for operational analysis while controlling storage costs. | Prevents unbounded storage cost growth from failed message accumulation while optimizing long-term costs without compromising operational troubleshooting capabilities. |
| [Co-locate Service Bus namespaces](/azure/service-bus-messaging/service-bus-geo-dr) with message producers and consumers in the same Azure region. Regional deployment eliminates cross-region data transfer costs and reduces network latency. | Eliminates data egress charges for inter-region message transfer and simplifies network architecture while reducing both transfer costs and latency. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**. The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure Service Bus.

> [!div class="checklist"]
>
> - **Assess team readiness for Service Bus operations:** Service Bus operations require specialized expertise in messaging, such as dead letter queue management, message lifecycle handling, and security configuration. Teams need proficiency in authentication methods including shared access policies, managed identity, and Azure RBAC implementation.
>
> - **Implement Infrastructure as Code for Service Bus deployments:** Service Bus infrastructure supports ARM templates, Bicep, and Terraform for consistent deployments across environments. Templates should encompass namespace configuration, queue and topic definitions, subscription filters, and access policies for complete infrastructure management. Use template parameterization to enable environment-specific configurations while maintaining deployment consistency. For more information, see [Service Bus namespaces templates](/azure/templates/microsoft.servicebus/namespaces).
>
>   Consider layered deployment strategies to separate namespace provisioning from entity configuration when dependencies like managed identities require sequential provisioning. 
>
>   Version control for Service Bus configuration ensures auditability and provides rollback capabilities for infrastructure changes.
>
> - **Establish safe deployment practices for messaging infrastructure:** Include blue-green deployments for namespace switches and canary deployments for subscription filter changes. Blue-green strategies enable zero-downtime transitions while canary deployments validate routing before full rollout.
>
>   Make sure that there are deployment validation checks to ensure messaging continuity during infrastructure updates. Include testing of message routing, subscription filters, and dead letter queue configurations. 
>
> - **Design automation strategies for Service Bus operational tasks:** Where practical, have Service Bus operational automation target repetitive tasks. Evaluate Azure Logic Apps, Azure Functions, and Azure Automation for workflow automation capabilities.
>
>   Key automation opportunities include dead letter queue cleanup to prevent resource accumulation and expired message removal to control storage costs. Scheduled scaling operations can adjust capacity based on predictable usage patterns.
>
>   Additional automation targets can include subscription maintenance, access policy rotation, and namespace capacity monitoring to reduce operational overhead and ensure consistent management practices.
>
> - **Collect monitoring data:** Monitor message flow health, namespace performance metrics, and dependencies. Azure Monitor integration provides essential metrics, logs, and distributed tracing capabilities for messaging infrastructure. Critical telemetry includes message throughput rates, queue depth trends, dead letter accumulation, throttling events, and connection pool utilization. Application Insights enables end-to-end tracing from publishers through Service Bus to consumers for complete visibility.
>
> - **Establish incident response procedures for messaging failures:** Address namespace failover making sure there's message recovery and subscription recreation. Geo-disaster recovery capabilities provide automated regional failover when properly configured.
>
>   Response strategies must account for message ordering guarantees, duplicate handling, validation of routing and filters, and manual intervention procedures for complex recovery scenarios while preserving delivery guarantees.
>
> - **Implement testing strategies for messaging infrastructure:** Include functional testing of messaging patterns, integration testing, and validation of routing, filters, and processing logic. Load testing validates performance characteristics under expected traffic volumes.
>
>   Testing automation through CI/CD pipelines ensures consistent validation of messaging infrastructure changes and application integration scenarios.
>
> - **Evaluate messaging API selection to match the skillset of your team:** Service Bus supports both native Azure SDK and JMS 2.0 API for Java applications, each providing different feature capabilities and performance characteristics. API selection should align with team expertise and application requirements.
>
>   Native SDKs provide complete access to Service Bus features including sessions, duplicate detection, and auto-forwarding with Azure-specific optimizations. These SDKs deliver full feature access and optimal performance characteristics.
>
>   JMS API offers a standardized messaging interface but with limitations including unavailability of Service Bus Sessions and reduced performance options compared to native implementations.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Develop [ARM templates](/azure/service-bus-messaging/service-bus-resource-manager-overview) or Bicep modules for Service Bus namespace configuration with parameterization support for environment-specific settings, messaging entities, access policies, and service integrations. | Ensures consistent deployments across environments while reducing configuration drift through automated provisioning. Enables rapid recovery through template-based infrastructure recreation and maintains detailed audit trails for compliance. |
| Create automated workflows using [Azure Logic Apps](/azure/logic-apps/logic-apps-overview) or [Azure Functions](/azure/azure-functions/functions-overview) to handle operational tasks including dead letter queue cleanup, subscription maintenance, and access policy rotation. | Reduces manual overhead while ensuring consistent operational practices through event-driven automation and scheduled maintenance execution. |
| Configure Azure Monitor dashboards and alerting rules for [Service Bus metrics](/azure/service-bus-messaging/monitor-service-bus-reference) including message counts, throughput rates, error conditions, and dependency health within business context. | Enables early detection of messaging issues and supports proactive capacity management. Reduces mean time to detection and resolution while facilitating effective troubleshooting through custom dashboards. |
| Develop incident response playbooks documenting message reprocessing strategies, [namespace failover](/azure/service-bus-messaging/service-bus-geo-dr#failover-flow) procedures, and dead letter queue recovery workflows with clear escalation protocols.<br><br>Conduct regular testing to validate playbook effectiveness and ensure team readiness for emergency scenarios. | Ensures consistent incident handling and reduces recovery time through documented procedures. Minimizes message loss while enabling faster resolution through systematic response strategies. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Azure Service Bus.

> [!div class="checklist"]
>
> - **Conduct capacity planning for Service Bus workloads:** Service Bus capacity planning requires analyzing expected message volumes and peak throughput requirements through systematic evaluation. Load test with realistic message patterns to validate capacity estimates before production deployment.
>
>     Consider message size distributions and concurrent connection patterns when establishing capacity baselines. Account for message retention periods, dead letter queue storage, and subscription filter complexity that affect overall resource consumption.
>
>     Premium tier provides dedicated messaging units with predictable performance characteristics and guaranteed capacity. Lower tiers use shared infrastructure with variable performance where throttling may occur during peak usage periods due to noisy neighbor effects.
>
> - **Design scaling strategies for message processing workloads:** Service Bus scaling strategies should include horizontal, vertical, and architectural scaling approaches that address different performance requirements.
>
>     Horizontal scaling involves partitioned entities that distribute messages across multiple message brokers, improving throughput and parallelism. However, partitioning creates tradeoffs where improved scalability may affect ordering guarantees and transactional consistency with partitioned entities.
>
>     Premium tier messaging unit increases provide dedicated capacity with automatic adjustment capabilities for dynamic workloads. Architectural scaling through namespace distribution across regions supports global workloads with regional performance optimization.
>
> - **Configure message processing patterns for optimal throughput:** Message processing patterns significantly impact Service Bus throughput and overall application performance. Batch processing reduces network round trips by sending or receiving multiple messages in single operations, improving efficiency. Topic subscriptions with filtering enable fan-out scenarios for message distribution.
>
>     Parallel processing using multiple concurrent receivers increases overall throughput for queue-based workloads. Session-based messaging ensures ordered processing for related messages but creates throughput constraints by limiting parallelism to one active receiver per session.
>
> - **Monitor performance indicators and identify bottlenecks:** Service Bus performance monitoring requires tracking key metrics including message throughput rates, queue depth trends, processing latency, and throttling events. Monitor connection pool utilization for performance assessment across the messaging infrastructure.
>
>     Identify bottleneck by focusing on namespace-level throttling and subscription filter processing delays that can impact overall throughput.
>
> - **Optimize message handling and network performance:** Message handling optimization includes connection pooling, prefetch configuration, and message batching to improve client-side performance. Payload size optimization reduces network overhead and improves processing efficiency.
>
>     Prefetch settings enable clients to receive multiple messages proactively, reducing the impact of network latency on message processing throughput. Network performance optimization involves co-locating Service Bus namespaces with message producers and consumers through regional co-location that minimizes latency.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Select [Service Bus Premium tier](/azure/service-bus-messaging/service-bus-premium-messaging) for production workloads requiring guaranteed performance. Premium tier provides dedicated messaging units with predictable performance characteristics and enables advanced features like larger message sizes with auto-scaling capabilities. | Dedicated messaging units eliminate variable performance issues from shared infrastructure. Consistent throughput guarantees support predictable application performance for production messaging workloads. |
| Enable [auto-scaling](/azure/service-bus-messaging/automate-update-messaging-units) for Service Bus Premium with configured minimum and maximum messaging unit thresholds. Set minimum units to handle baseline load without scaling delays.<br><br>Implement multiple concurrent receivers to distribute message processing load across available capacity. | Improved resource utilization through dynamic capacity allocation that matches actual demand. Eliminates manual intervention for capacity management during load changes while maintaining consistent performance. |
| Implement batch message operations using [ReceiveMessages() and SendMessages() methods](/azure/service-bus-messaging/service-bus-performance-improvements) to reduce network round trips and connection overhead.<br><br>Implement error handling for batch operations to manage partial failures and maintain reliability. | Improves message processing efficiency significantly compared to individual operations through reduced network overhead. Higher overall throughput with same infrastructure resources due to reduced connection management overhead.<br><br>Better application performance for bulk message processing scenarios with optimized resource utilization. |
| Configure [Azure Monitor dashboards](/azure/service-bus-messaging/monitor-service-bus-reference) with Service Bus performance metrics including message throughput rate, queue depth trends, and processing latency. Monitor throttling events and server errors as performance indicators.<br><br>Set up latency percentile tracking and configure alert thresholds for proactive issue detection. | Provides real-time visibility into messaging infrastructure health and performance trends. Enables early detection of performance degradation before user impact through proactive alerting.<br><br>Supports data-driven capacity planning and performance optimization decisions based on empirical metrics. |
| Use [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing) to simulate realistic Service Bus workloads with expected message volumes and concurrent publisher/consumer patterns. Include scenarios with varying message sizes and processing complexity.<br><br>Test auto-scaling behavior and throttling thresholds during load spikes while measuring end-to-end latency. Document performance baselines and capacity limits for production planning. | Validates messaging infrastructure performance under realistic conditions before production deployment. Identifies potential bottlenecks and capacity limitations early in the development cycle.<br><br>Provides empirical data for accurate capacity planning decisions and performance optimization strategies. |
| Optimize Service Bus client configuration with appropriate prefetch counts, connection pooling, and message batching for improved efficiency. Optimize message size and compression to reduce network bandwidth usage.<br><br>Co-locate Service Bus namespaces with applications in the same Azure region to minimize latency. Additional resources include [Service Bus performance improvements](/azure/service-bus-messaging/service-bus-performance-improvements). | Improved message processing efficiency through client optimization and reduced connection overhead. Lower latency through regional co-location that minimizes network distance between applications and Service Bus infrastructure. |
| Use [Express Entities](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress) to store messages in memory when possible for enhanced performance. Configure express queues and topics through the EnableExpress property while evaluating the trade-off between performance optimization and durability guarantees. | Enhanced message processing performance through optimized in-memory storage when durability requirements permit. Reduced latency for high-throughput messaging scenarios with improved responsiveness. |

## Azure policies

Azure provides an extensive set of built-in policies related to Azure Service Bus and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Azure Service Bus Premium namespaces use customer-managed keys for encryption at rest to provide additional control over encryption keys and meet compliance requirements.
- Azure Service Bus namespaces exclusively require Microsoft Entra ID identities for authentication for increased security and less management overhead.
- Azure Service Bus namespaces are configure with private endpoints to reduce data leakage risks.
- Resource logs in Azure Service Bus are enabled to generate audit trails to use for investigation purposes when a security incident occurs

For comprehensive governance, review the [Azure Policy built-in definitions for Service Bus](/azure/governance/policy/samples/built-in-policies#service-bus) and other policies that might affect the security of the messaging infrastructure.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [Enterprise integration using message broker](/azure/architecture/example-scenario/integration/queues-events).

## Related content

- [Azure Service Bus messaging documentation](/azure/service-bus-messaging/)
- [Azure Service Bus samples](https://github.com/Azure/azure-service-bus/tree/master/samples)
- [Azure Architecture Center - Enterprise integration using message broker](/azure/architecture/reference-architectures/enterprise-integration/queues-events)