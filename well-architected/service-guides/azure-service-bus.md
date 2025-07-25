---
title: "Architecture Best Practices for Azure Service Bus"
description: "Architecture recommendations for Azure Service Bus messaging infrastructure covering reliability, security, cost optimization, operational excellence, and performance efficiency."
author: prwilk
ms.author: prwilk
ms.topic: conceptual
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
> - **Review Service Bus quotas and limits that might pose design restrictions:** Azure Service Bus enforces various [quotas and limits](/azure/service-bus-messaging/service-bus-quotas) that can impact messaging architecture design, including maximum message size, queue depth limits, concurrent connections, and throughput unit restrictions.
>
>    Namespace-level limits include maximum number of queues, topics, and subscriptions per namespace, while message-level restrictions cover time-to-live settings, duplicate detection windows, and session capabilities. Understanding these constraints early in design helps avoid runtime limitations and ensures adequate capacity planning.
>
> - **Anticipate potential failures:** Failure mode analysis provides a systematic approach to anticipating potential failure scenarios and developing mitigation strategies to ensure messaging service resilience.
>
>    | Failure | Mitigation |
>    |---------|------------|
>    | Service Bus namespace becomes unreachable | Configure geo-disaster recovery with paired regions and implement client retry logic with exponential backoff |
>    | Message processing failures create poison messages | Enable dead letter queues and implement dead letter message monitoring and reprocessing workflows |
>    | High message volume exceeds throughput limits | Monitor throughput unit utilization and implement auto-scaling or upgrade to higher service tiers |
>
> - **Implement zone-level redundancy for high availability:** Azure Service Bus Premium tier supports availability zone redundancy to protect against datacenter-level failures within a region. Zone redundancy automatically replicates message data and metadata across multiple availability zones, ensuring service continuity when individual zones experience outages.
>
>    Premium namespaces deployed with zone redundancy maintain three copies of the messaging store across different zones, providing automatic failover capabilities without requiring manual intervention or configuration changes from applications.
>
> - **Design multi-region redundancy for disaster recovery:** Multi-region deployment strategies protect against complete regional failures through geo-disaster recovery capabilities and cross-region message replication. Service Bus Premium tier supports geo-disaster recovery pairing between primary and secondary regions, enabling automatic metadata replication and failover orchestration.
>
>    Geo-disaster recovery replicates namespace metadata (queues, topics, subscriptions, access policies) but not message data, making it suitable for most business continuity scenarios. Geo-replication provides full message data replication across regions but requires additional cost and complexity considerations for comprehensive data protection scenarios.
>
>    Regional redundancy requires careful consideration of message ordering guarantees, duplicate handling during failover scenarios, and application-level retry logic to handle cross-region latency variations. Active-passive configurations provide cost-effective regional protection while active-active patterns support geographically distributed workloads with higher complexity.
>
> - **Configure throughput scaling to handle variable message loads:** Service Bus Premium tier provides dedicated messaging units with predictable performance characteristics and supports auto-scaling throughput units to handle dynamic message volume changes. Each messaging unit delivers up to 1,000 messages per second for sends and 2,000 messages per second for receives, with automatic scaling preventing throttling during traffic spikes.
>
>    Standard tier uses shared infrastructure with variable performance but lacks dedicated throughput guarantees and auto-scaling capabilities. Throughput unit configuration should account for peak message volumes, burst capacity requirements, and cost optimization through right-sizing based on actual usage patterns.
>
> - **Design message processing patterns for reliable scaling:** Message processing scalability depends on queue and topic partitioning strategies, concurrent receiver configurations, and session-based processing patterns. Service Bus supports multiple concurrent receivers per queue to increase processing throughput, while session-enabled queues ensure ordered processing but limit parallelism to one active receiver per session.
>
>    Partitioned entities improve scalability by distributing messages across multiple message brokers but may affect ordering guarantees and transactional consistency. Topic subscriptions with message filtering enable fan-out scenarios that scale message processing across multiple consumer applications without duplicating infrastructure costs.
>
> - **Implement reliability-focused monitoring and health detection:** Reliability monitoring requires tracking Service Bus health indicators beyond general operational metrics, focusing on message delivery success rates, dead letter queue accumulation, and failover event detection. Key reliability metrics include active message count trends, server errors that indicate infrastructure issues, and throttling events that signal capacity constraints.
>
>    Dependency health monitoring extends to connected applications and external services that publish or consume messages, as messaging reliability depends on the entire message flow chain. Monitoring thresholds should trigger alerts for scenarios that compromise message delivery guarantees, such as dead letter queue growth or regional failover events.
>
> - **Configure message-level self-preservation mechanisms:** Dead letter queues automatically isolate undeliverable messages to prevent processing failures from blocking healthy message flow. Service Bus supports configurable dead letter conditions including maximum delivery count, time-to-live expiration, and explicit dead lettering from message processing logic.
>
>    Duplicate detection prevents message reprocessing during retry scenarios by maintaining a duplicate detection history window based on message identifiers.
>
>    Auto-delete capabilities for idle queues and expired messages help maintain namespace health by removing unused resources and preventing storage accumulation from abandoned message flows.
>
> - **Implement namespace-level protection and circuit breaker patterns:** Service Bus client libraries provide built-in retry policies with exponential backoff and circuit breaker capabilities to protect against cascading failures during service degradation. Connection pooling and client caching strategies prevent resource exhaustion during high-throughput scenarios while maintaining connection efficiency.
>
>    Namespace isolation through separate Service Bus namespaces for different environments or workload components prevents failures from affecting multiple applications. Message batching and prefetch optimization reduce the impact of network latency variations and improve overall messaging reliability during variable network conditions.
>
> - **Plan geo-disaster recovery strategies for business continuity:** Geo-disaster recovery provides automatic failover capabilities between paired Azure regions, replicating namespace metadata including queues, topics, subscriptions, and access policies to a secondary region. During regional outages, applications can redirect to the secondary namespace using alias connections that automatically point to the active region.
>
>    Recovery planning must account for message data loss during failover scenarios, as in-flight messages are not replicated across regions. Recovery time objectives depend on DNS propagation delays and application reconnection logic, typically requiring 5-15 minutes for complete failover completion.
>
> - **Implement backup and recovery procedures for message data:** Message-level backup strategies require application-level implementation since Service Bus does not provide built-in message backup capabilities. Dead letter queue monitoring and archival processes help preserve undeliverable messages for analysis and potential reprocessing after resolving delivery issues.
>
>    Configuration backup includes exporting namespace metadata, queue and topic definitions, subscription filters, and access policies to enable rapid namespace recreation during disaster recovery scenarios. Automated backup procedures should capture both configuration metadata and critical message flows to support comprehensive recovery capabilities.
>

### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Deploy [Service Bus Premium namespaces](/azure/service-bus-messaging/service-bus-premium-messaging) with [zone redundancy](/azure/service-bus-messaging/service-bus-outages-disasters) enabled in regions that support availability zones for production messaging workloads requiring high availability. | Zone redundancy provides automatic failover protection against datacenter failures within a region, maintaining message delivery capabilities and reducing recovery time objectives to seconds rather than minutes. |
| Configure [geo-disaster recovery pairing](/azure/service-bus-messaging/service-bus-geo-dr) between primary and secondary regions using Service Bus alias connections to enable automatic failover during regional outages. | Geo-disaster recovery provides business continuity protection against complete regional failures, automatically redirecting applications to healthy regions with minimal configuration changes and supporting recovery time objectives under 15 minutes. |
| Enable [auto-scaling for Premium tier messaging units](/azure/service-bus-messaging/automate-update-messaging-units) with appropriate minimum and maximum thresholds based on peak message volume analysis and cost optimization requirements. | Auto-scaling prevents message throttling during traffic spikes while optimizing costs during low-traffic periods, maintaining consistent message processing performance and eliminating manual capacity management overhead. |
| Configure multiple concurrent receivers per queue and implement [partitioned entities](/azure/service-bus-messaging/service-bus-partitioning) for high-throughput scenarios while considering message ordering requirements and transactional consistency needs. | Concurrent processing increases message throughput and provides better resource utilization, while partitioning distributes load across multiple message brokers to handle enterprise-scale message volumes.<br><br>Partitioning improves performance characteristics for high-volume scenarios and enables horizontal scaling of messaging infrastructure beyond single-broker limitations. |
| Set up [Azure Monitor alerts](/azure/azure-monitor/alerts/alerts-create-new-alert-rule) for Service Bus reliability metrics including dead letter message count thresholds, server error rates, and throttling events with appropriate escalation procedures. | Reliability-focused monitoring enables proactive detection of message delivery issues and capacity constraints before they impact application availability, reducing mean time to detection and supporting service level objective compliance. |
| Enable [dead letter queues](/azure/service-bus-messaging/service-bus-dead-letter-queues) for all production queues and topics with appropriate maximum delivery count settings, and implement [duplicate detection](/azure/service-bus-messaging/duplicate-detection) with suitable detection history windows for message deduplication. | Dead letter queues prevent poison messages from blocking healthy message processing while duplicate detection eliminates message reprocessing during retry scenarios, improving overall messaging reliability and reducing application complexity. |
| Implement Service Bus client [retry policies](/azure/service-bus-messaging/service-bus-performance-improvements) with exponential backoff and use separate namespaces for different environments to isolate failure impacts and prevent cascading failures. | Built-in retry policies with circuit breaker functionality protect against transient service issues while namespace isolation prevents failures in one environment from affecting other workload components, improving overall system resilience. |
| Establish automated backup procedures for Service Bus namespace configuration including queue definitions, topic subscriptions, and access policies using [Azure Resource Manager templates](/azure/azure-resource-manager/templates/overview) or PowerShell scripts. | Configuration backup enables rapid namespace recreation during disaster recovery scenarios and provides version control for messaging infrastructure changes, reducing recovery time objectives and ensuring consistent environment restoration. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure Service Bus.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture.

> [!div class="checklist"]
>
> - **Establish a security baseline:** Review the [security baseline](/security/benchmark/azure/baselines/service-bus-security-baseline) for Service Bus and incorporate applicable measures in your security foundation. The Microsoft Cloud Security Benchmark provides specific security controls for Service Bus including identity management, network security, data protection, and compliance requirements.
>
>    Security baseline implementation ensures consistent security posture across messaging infrastructure and provides systematic guidance for addressing common security risks in enterprise messaging scenarios.
>
> - **Use Microsoft Entra ID for authentication and authorization:** Microsoft Entra ID integration provides centralized identity management and supports advanced security features like conditional access policies and multi-factor authentication. Service Bus offers built-in Azure RBAC roles including Azure Service Bus Data Owner, Data Sender, and Data Receiver for granular access control to messaging operations.
>
>    Identity-based authentication eliminates the need for connection string management in applications and establishes identity as the primary security perimeter for messaging workloads. Managed identities enable secure authentication from Azure services without storing credentials in application code.
>
> - **Implement network isolation and traffic segmentation:** Private endpoints enable secure connectivity from virtual networks by creating private IP addresses for Service Bus namespaces within your network address space. This approach eliminates exposure to public internet routing and ensures message traffic remains within the Azure backbone network.
>
>    Service endpoints provide an alternative network integration approach that secures traffic over the Azure backbone without requiring private IP addresses. IP firewall rules offer additional protection by restricting access to specific client IP address ranges or virtual network subnets.
>
> - **Configure traffic filtering and access control policies:** Network security rules and access policies provide defense-in-depth protection by controlling which networks and applications can access Service Bus resources. Virtual network rules restrict access to specific subnets while IP filtering rules allow or deny traffic from defined IP address ranges.
>
>    Conditional access policies based on client location, device compliance, and user risk assessment add additional security layers for identity-based authentication scenarios. These policies can enforce multi-factor authentication and restrict access during high-risk sign-in attempts.
>
> - **Manage identity lifecycle and role assignments:** Role-based access control (RBAC) assignments should follow least privilege principles by granting only the minimum permissions required for specific messaging operations. Service Bus data plane roles enable fine-grained control over send, receive, and manage operations without providing broader namespace management access.
>
>    Identity lifecycle management includes regular review of role assignments, automated provisioning and deprovisioning through group membership, and integration with identity governance tools for access certification and privileged access management.
>
>    Service principal and managed identity usage should be monitored and audited for compliance requirements while establishing appropriate approval workflows for privileged messaging access.
>
> - **Apply comprehensive network controls:** Network security controls should be implemented in layers to provide defense-in-depth protection for messaging traffic. Disable public network access when private connectivity is established to eliminate internet-based attack vectors and ensure all traffic flows through controlled network paths.
>
>    Network security group rules for subnets containing Service Bus consumers and producers can provide additional traffic filtering and logging capabilities. Azure Firewall or third-party network virtual appliances can inspect and control messaging traffic patterns when advanced threat protection is required.
>
> - **Configure encryption at rest with customer-managed keys:** Customer-managed keys (CMK) provide additional control over encryption key lifecycle and access policies for data at rest encryption. Service Bus Premium tier supports integration with Azure Key Vault for customer-managed key encryption, enabling compliance with regulatory requirements that mandate customer control over encryption keys.
>
>    Key rotation policies should be established to regularly update encryption keys while maintaining service availability. Cross-region key vault replication ensures encryption key availability during disaster recovery scenarios when geo-disaster recovery is implemented.
>
> - **Enforce encryption in transit and protocol security:** Transport Layer Security (TLS) 1.2 or higher should be enforced for all Service Bus client connections to protect message data during transmission. Disable legacy protocols and cipher suites that may have known vulnerabilities or insufficient cryptographic strength.
>
>    AMQP over TLS provides secure messaging protocol implementation with strong encryption and authentication capabilities. Certificate-based authentication can supplement or replace shared access signatures for enhanced security in environments requiring certificate-based PKI infrastructure.
>
> - **Harden namespace configuration and reduce attack surface:** Disable unused Service Bus features and protocols to minimize the attack surface and reduce potential security vulnerabilities. Remove unnecessary queues, topics, and subscriptions that are no longer required to prevent unauthorized access to unused messaging entities.
>
>    Configure appropriate message retention policies and auto-delete settings to prevent indefinite storage of sensitive message data. Enable only required messaging patterns and disable features like message forwarding or auto-forwarding unless specifically needed for the workload architecture.
>
> - **Secure connection strings and access credentials:** Service Bus connection strings and shared access signatures contain sensitive authentication information that must be protected using secure secret management practices. Azure Key Vault integration enables centralized storage and access control for connection strings with audit logging and access policies.
>
>    Rotate shared access keys regularly and implement key rollover procedures that minimize service disruption. Use managed identities where possible to eliminate connection string storage requirements and reduce the risk of credential exposure in application configuration or source code.
>
> - **Implement security monitoring and threat detection:** Security monitoring extends beyond general operational monitoring to focus on threat detection, unauthorized access attempts, and suspicious messaging patterns. Azure Monitor integration provides security-relevant metrics including authentication failures, throttling events, and unusual message volume patterns that may indicate security incidents.
>
>    Diagnostic logs should capture authentication events, authorization failures, and administrative operations for security audit trails. Integration with Azure Sentinel or other SIEM solutions enables correlation of Service Bus security events with broader security monitoring across the workload infrastructure.
>
>    Automated alerting and response procedures should be established to handle security events including failed authentication attempts, privilege escalation, and anomalous messaging behavior that may indicate compromise or attack scenarios.
>
> - **Establish security testing and validation practices:** Security testing should validate Service Bus configuration against security baselines and organizational security policies. Automated security scanning tools can assess network configuration, access control policies, and encryption settings to identify potential security gaps or misconfigurations.
>
>    Penetration testing scenarios should include attempts to access messaging resources without proper authentication, network-based attacks against Service Bus endpoints, and validation of data encryption implementation. Regular security assessments help maintain security posture as messaging infrastructure evolves.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Enable [managed identity authentication](/azure/service-bus-messaging/service-bus-managed-service-identity) for Service Bus clients running on Azure services and assign appropriate [Azure RBAC roles](/en-us/entra/identity/managed-identities-azure-resources/overview) (Data Sender, Data Receiver, or Data Owner) based on the principle of least privilege. | Managed identity eliminates connection string management and credential storage risks while providing centralized access control through Azure RBAC.<br><br>This approach reduces the attack surface and enables conditional access policies and security monitoring for comprehensive identity-based messaging protection. |
| Configure [private endpoints](/azure/service-bus-messaging/private-link-service) for Service Bus Premium namespaces and disable public network access to ensure all message traffic flows through private network connections within your [virtual network infrastructure](/azure/private-link/private-endpoint-overview). | Private endpoints eliminate exposure to public internet threats and ensure message traffic remains within the Azure backbone network, providing network-level isolation and reducing attack surface for messaging infrastructure. |
| Implement [IP firewall rules](/azure/service-bus-messaging/service-bus-ip-filtering) to restrict Service Bus namespace access to known client IP ranges and configure [virtual network rules](/azure/service-bus-messaging/service-bus-service-endpoints) to allow access only from approved subnets. | Network access controls provide defense-in-depth protection by limiting Service Bus access to authorized network locations, reducing the risk of unauthorized access and supporting compliance requirements for network-based access restrictions. |
| Enable [customer-managed key encryption](/azure/service-bus-messaging/configure-customer-managed-key) for Service Bus Premium namespaces using Azure Key Vault and implement [key rotation policies](/azure/key-vault/keys/how-to-configure-key-rotation) with appropriate access controls and audit logging. | Customer-managed keys provide enhanced control over encryption key lifecycle and access policies, supporting compliance requirements for customer-controlled encryption.<br><br>Automated key rotation maintains service availability while ensuring encryption keys are regularly updated according to security policies and regulatory standards. |
| Enforce TLS 1.2 minimum version for all Service Bus client connections and disable legacy [authentication mechanisms](/azure/service-bus-messaging/service-bus-authentication-and-authorization) such as older shared access signature formats where possible using [AMQP protocol](/azure/service-bus-messaging/service-bus-amqp-protocol-guide). | Strong transport encryption and modern authentication protocols protect message data in transit and reduce vulnerability to protocol-based attacks, ensuring messaging communications meet current security standards and compliance requirements. |
| Store Service Bus connection strings and shared access keys in [Azure Key Vault](/azure/key-vault/secrets/about-secrets) with appropriate access policies and implement key rotation procedures using automated deployment pipelines for [SAS token management](/azure/service-bus-messaging/service-bus-sas). | Centralized secret management eliminates credential exposure in application configuration and source code while providing audit logging and access control for sensitive authentication information.<br><br>This approach reduces the risk of credential compromise and enables automated rotation procedures that maintain security without manual intervention. |
| Configure Azure Monitor diagnostic settings to capture Service Bus [authentication events, authorization failures, and administrative operations](/azure/service-bus-messaging/monitor-service-bus-reference), and integrate security logs with Azure Sentinel for threat detection and incident response. | Security-focused monitoring enables rapid detection of unauthorized access attempts and suspicious messaging patterns while providing audit trails for compliance requirements and incident investigation capabilities. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure Service Bus and its environment.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Evaluate Service Bus pricing tiers and capacity planning approaches:** Azure Service Bus offers three pricing tiers with distinct cost structures and feature capabilities that significantly impact total cost of ownership. Basic tier supports queues only with pay-per-operation pricing, Standard tier adds topics and subscriptions with pay-per-operation model, while Premium tier provides dedicated messaging units with predictable costs and advanced features.
>
>    Premium tier messaging units have a higher monthly cost but include unlimited operations, making it cost-effective for high-throughput scenarios with substantial monthly operation volumes. Standard and Basic tiers charge per operation volume plus base costs, making them suitable for lower-volume messaging patterns.
>
>    Capacity planning requires analyzing peak message volumes, concurrent connections, message sizes, and feature requirements like sessions, duplicate detection, or geo-disaster recovery to determine the most cost-effective tier and sizing approach.
>
> - **Establish Service Bus usage forecasting and cost modeling methodologies:** Service Bus cost modeling requires forecasting message volume patterns, peak throughput requirements, and feature utilization across different workload scenarios. Cost drivers include operation counts, message storage duration, network egress charges, and premium features like geo-disaster recovery or customer-managed encryption keys.
>
>    Usage patterns vary significantly between different messaging scenarios including event-driven architectures, job queue processing, and publish-subscribe patterns, each with distinct cost characteristics. Seasonal variations, business growth projections, and disaster recovery testing activities affect long-term cost forecasting accuracy.
>
>    Cost allocation strategies should account for shared Service Bus namespaces across multiple applications, enabling chargeback models through resource tagging and detailed monitoring of per-application message volumes and feature usage.
>
> - **Implement cost monitoring and budget alerting for Service Bus resources:** Cost monitoring for Service Bus requires tracking both base service costs and usage-driven charges including message operations, storage consumption, and network egress fees. Azure Cost Management provides detailed cost breakdown by Service Bus namespace, enabling identification of cost drivers and anomalous spending patterns.
>
>    Budget alerts should be configured at appropriate thresholds to detect unexpected cost increases from traffic spikes, configuration changes, or operational issues. Cost alerts can trigger automated responses including scaling adjustments, notification workflows, or emergency budget controls to prevent cost overruns.
>
>    Resource tagging strategies enable cost allocation across departments, projects, or applications sharing Service Bus infrastructure, supporting chargeback models and cost accountability in multi-tenant environments.
>
> - **Analyze resource utilization for cost optimization opportunities:** Service Bus utilization analysis identifies opportunities for cost optimization through right-sizing, tier adjustments, and feature optimization. Key utilization metrics include message throughput patterns, queue depth trends, connection utilization, and feature usage analysis to determine optimal configuration settings.
>
>    Underutilized namespaces or messaging units represent cost optimization opportunities through consolidation or tier downgrading. Conversely, consistent high utilization may indicate cost-effective scaling opportunities to Premium tier or additional messaging units for better price-performance ratios.
>
>    Seasonal usage patterns and business cycle variations should inform capacity planning and cost forecasting, enabling proactive adjustments to Service Bus configuration based on predictable demand changes.
>
> - **Optimize Service Bus feature configuration to reduce unnecessary costs:** Service Bus feature optimization involves disabling unused capabilities and right-sizing enabled features to minimize costs without compromising functionality. Premium features like geo-disaster recovery, customer-managed keys, and zone redundancy should be evaluated against actual business requirements to avoid unnecessary expenses.
>
>    Message retention policies, duplicate detection windows, and session configurations impact storage costs and processing overhead. Optimizing these settings based on application requirements reduces long-term storage costs while maintaining required functionality for message processing patterns.
>
>    Namespace consolidation opportunities should be evaluated to reduce base costs by sharing Service Bus infrastructure across multiple applications when architectural boundaries and security requirements permit resource sharing.
>
> - **Implement message handling optimizations to reduce operation costs:** Message batching and efficient message processing patterns significantly reduce operation costs in pay-per-operation pricing tiers. Batching multiple messages into single operations reduces the total number of billable operations while improving throughput and reducing latency for high-volume messaging scenarios.
>
>    Message size optimization through compression, payload minimization, and efficient serialization reduces storage costs and network transfer charges. Large message support in Premium tier should be evaluated against the additional costs compared to alternative approaches like message splitting or external storage references.
>
>    Dead letter queue management and automated cleanup procedures prevent indefinite accumulation of failed messages that consume storage resources and increase long-term costs without providing business value.
>
> - **Optimize network and data transfer costs for messaging infrastructure:** Network costs for Service Bus include data egress charges when messages cross Azure regions or leave Azure infrastructure. Co-locating Service Bus namespaces with consuming applications in the same region eliminates inter-region data transfer costs while reducing message delivery latency.
>
>    Private endpoint usage may increase costs through additional endpoint charges but can reduce overall networking costs by eliminating public internet data transfer fees and improving security posture. Cost-benefit analysis should consider both direct networking costs and indirect security and compliance benefits.
>
>    Message routing and topic subscription filtering should be optimized to minimize unnecessary message delivery and processing costs by ensuring messages are only delivered to subscriptions that actively process them.
>
> - **Implement Azure Policy controls for Service Bus cost management:** Azure Policy provides automated enforcement of cost control measures through restrictions on Service Bus tier deployment, regional limitations, and feature usage controls. Policy definitions can prevent deployment of Premium tier resources without appropriate approvals or restrict geo-disaster recovery features to specific environments.
>
>    Required tagging policies ensure consistent cost allocation tracking across Service Bus resources, enabling accurate chargeback models and budget attribution. Tag-based policies can enforce cost center assignments, project codes, and environment classifications that support financial governance and spending analysis.
>
>    SKU and location restrictions through policy enforcement prevent accidental deployment of high-cost configurations or resources in expensive regions, providing automated guardrails against cost overruns without requiring manual review processes.
>
> - **Establish budget controls and spending alerts for messaging infrastructure:** Budget management for Service Bus requires setting appropriate spending thresholds based on projected message volumes, tier requirements, and feature usage patterns. Budget alerts should trigger at multiple threshold levels to provide early warning of cost overruns while allowing time for corrective action.
>
>    Subscription-level and resource group-level budgets provide hierarchical cost controls that can enforce spending limits across multiple Service Bus namespaces and related infrastructure. Emergency spending controls should be prepared to handle unexpected traffic spikes or configuration changes that could result in significant cost increases.
>
>    Integration with approval workflows for high-cost Service Bus deployments ensures appropriate review and authorization for Premium tier resources, geo-disaster recovery configurations, and large-scale messaging infrastructure investments.
>
> - **Optimize Service Bus configurations across different environments:** Environment-specific Service Bus configurations enable significant cost savings by matching service capabilities to actual requirements for development, testing, and production environments. Development and testing environments typically require reduced throughput, simplified feature sets, and lower availability guarantees that align with Standard tier capabilities.
>
>    Production environments may require Premium tier features including zone redundancy, geo-disaster recovery, and dedicated throughput capacity, while non-production environments can use Basic or Standard tiers with shared infrastructure. Environment-specific retention policies, session requirements, and monitoring configurations further optimize costs based on actual usage patterns.
>
>    Automated environment provisioning should include cost-optimized defaults for each environment type, preventing accidental over-provisioning of development resources with production-level capabilities and associated costs.
>
> - **Implement automation for cost-reducing operational tasks:** Automated cost management for Service Bus includes scheduled scaling operations, resource cleanup procedures, and dynamic configuration adjustments based on usage patterns. Automation reduces operational overhead while ensuring consistent application of cost optimization policies across messaging infrastructure.
>
>    Automated dead letter queue cleanup, expired message removal, and unused queue deletion prevent resource accumulation that increases storage costs over time. Scheduled scaling operations can adjust messaging units or tier configurations based on predictable usage patterns, optimizing costs during low-traffic periods.
>
>    Integration with Azure Automation or Azure Functions enables custom cost optimization workflows including automated reporting, budget threshold responses, and environment-specific configuration management that reduces manual administrative effort while maintaining cost control.
>
> - **Implement Service Bus consolidation strategies for cost reduction:** Namespace consolidation reduces base infrastructure costs by sharing Service Bus resources across multiple applications when security boundaries and operational requirements permit resource sharing. Consolidated namespaces benefit from improved utilization of messaging units and reduced per-namespace fixed costs.
>
>    Centralized Service Bus management through enterprise messaging platforms or Centers of Excellence provides economies of scale through standardized configurations, shared expertise, and consolidated procurement. Centralized teams can implement consistent cost optimization practices and negotiate volume-based pricing with better organizational leverage.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Analyze message volume patterns and operation counts using [Azure Cost Management](/azure/cost-management-billing/costs/cost-analysis-common-uses) to determine the break-even point between Standard tier pay-per-operation and Premium tier dedicated pricing models. Perform detailed cost analysis comparing total monthly operations against Premium tier monthly costs to identify the optimal tier selection for current and projected usage patterns using [Service Bus pricing](/azure/service-bus-messaging/service-bus-premium-messaging) information. | Data-driven tier selection ensures optimal cost efficiency by matching pricing models to actual usage patterns, preventing over-provisioning of Premium tier for low-volume scenarios or under-provisioning Standard tier for high-volume operations. |
| Implement [resource tagging strategies](/azure/azure-resource-manager/management/tag-resources) for Service Bus namespaces with consistent cost center, project, and environment tags to enable accurate chargeback models and budget allocation using [Azure Cost Management](/azure/cost-management-billing/costs/cost-analysis-common-uses) for cost analysis. | Comprehensive tagging enables accurate cost attribution across shared Service Bus infrastructure, supporting financial accountability and enabling detailed cost analysis by business unit, project, or application for improved budget management.<br><br>Structured cost allocation supports chargeback models and departmental budget tracking while providing visibility into resource usage patterns across organizational boundaries. |
| Configure [Azure Monitor budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) with alert thresholds at 50%, 80%, and 100% of planned Service Bus spending to provide early warning of cost overruns before they impact business operations. Implement automated [notification workflows](/azure/azure-monitor/alerts/alerts-create-new-alert-rule) that escalate alerts through appropriate channels and trigger review processes when spending approaches critical thresholds. | Multi-threshold budget alerts provide graduated warning system that enables proactive cost management and corrective action before significant budget overruns, maintaining financial control while allowing operational flexibility.<br><br>Progressive alerting ensures appropriate stakeholder notification and enables timely intervention to prevent cost surprises and maintain budget adherence. |
| Configure [message retention policies](/azure/service-bus-messaging/message-expiration) and [duplicate detection windows](/azure/service-bus-messaging/duplicate-detection) based on actual application requirements rather than maximum supported values to minimize storage costs and processing overhead. | Optimized retention and detection windows reduce storage costs and processing overhead by eliminating unnecessary message persistence while maintaining adequate retention periods for application recovery and duplicate prevention requirements. |
| Implement [message batching](/azure/service-bus-messaging/service-bus-performance-improvements) in Service Bus client applications to reduce operation counts and improve cost efficiency in pay-per-operation pricing tiers. Configure batch sizes based on message volume patterns and processing requirements using [Service Bus SDK batching capabilities](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync) to maximize cost savings while maintaining acceptable processing latency. | Message batching significantly reduces billable operations in Standard and Basic tiers by combining multiple messages into single API calls, lowering per-message costs while improving throughput and reducing latency for high-volume messaging scenarios. |
| Optimize [message payload sizes](/azure/service-bus-messaging/service-bus-messages-payloads) through compression and efficient serialization to reduce storage costs and network transfer charges, especially for messages approaching size limits. Apply [Service Bus performance improvements](/azure/service-bus-messaging/service-bus-performance-improvements) techniques for maximum throughput capacity and minimize resource consumption. | Payload optimization reduces both storage costs and network egress charges while improving message processing performance, enabling more messages per messaging unit and reducing overall infrastructure costs for high-volume messaging workloads.<br><br>Efficient serialization and compression techniques maximize throughput capacity and minimize resource consumption across messaging infrastructure. |
| Implement automated [dead letter queue cleanup](/azure/service-bus-messaging/service-bus-dead-letter-queues) procedures with appropriate retention policies to prevent indefinite accumulation of failed messages that consume storage resources. Configure scheduled cleanup processes using [Azure Automation](/azure/automation/overview) that maintain sufficient retention periods for troubleshooting while removing older failed messages to control storage costs. | Automated DLQ cleanup prevents unbounded storage cost growth from failed messages while maintaining adequate retention periods for troubleshooting and message recovery, optimizing long-term storage costs without compromising operational capabilities.<br><br>Systematic cleanup procedures ensure consistent cost management and prevent storage accumulation from affecting messaging performance or budget predictability. |
| Co-locate Service Bus namespaces with message producers and consumers within the same Azure region to eliminate cross-region data transfer costs and reduce message delivery latency. Consider [bandwidth pricing](/azure/service-bus-messaging/service-bus-geo-dr) when designing multi-region messaging architectures. | Regional co-location eliminates data egress charges for inter-region message transfer while improving performance through reduced latency, optimizing both cost and user experience for distributed messaging applications.<br><br>Strategic regional placement reduces networking costs and provides operational benefits through improved message delivery performance and simplified network architecture. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**. The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure Service Bus.

> [!div class="checklist"]
>
> - **Assess team readiness for Service Bus operations:** Service Bus operations require specialized knowledge of messaging patterns, namespace configuration, and troubleshooting distributed messaging scenarios. Teams need training in Service Bus-specific concepts including message sessions, dead letter queue management, subscription filters, and auto-forwarding behaviors that differ from traditional application architectures.
>
>    Centralized Service Bus management through enterprise messaging platforms or Centers of Excellence provides standardized configuration practices, shared operational expertise, and consistent monitoring approaches across applications. Document service configuration standards and operational procedures to ensure consistent practices and enable knowledge transfer.
>
> - **Implement Infrastructure as Code for Service Bus deployments:** Service Bus infrastructure provisioning supports multiple Infrastructure as Code approaches including ARM templates, Bicep, and Terraform for consistent, repeatable deployments across environments. IaC templates should include namespace configuration, queue and topic definitions, subscription filters, access policies, and integration settings for dependent services.
>
>    Layered deployment strategies separate namespace provisioning from entity configuration when dependencies exist on external resources like managed identities or Key Vault access policies that require propagation time. Template parameterization enables environment-specific configurations while maintaining consistent baseline settings.
>
> - **Establish safe deployment practices for messaging infrastructure:** Service Bus deployment strategies should minimize impact on active message flows through techniques like blue-green deployments for namespace switches, canary deployments for subscription filter changes, and staged rollouts for application updates that modify message processing logic. Deployment validation includes testing message routing, subscription filters, and dead letter queue configurations.
>
>    Message processing continuity during deployments requires coordination between Service Bus configuration changes and application updates to prevent message loss or processing failures. Rollback procedures should account for in-flight messages and maintain message ordering guarantees when session-based processing is enabled.
>
> - **Design automation strategies for Service Bus operational tasks:** Service Bus operational automation should target repetitive management tasks including dead letter queue cleanup, subscription maintenance, access policy rotation, and namespace capacity monitoring. Automation workflows can integrate with Azure Logic Apps, Azure Functions, or Azure Automation to handle routine operational tasks that aren't natively automated by the service.
>
>    Configuration drift prevention through automated policy enforcement ensures namespace settings remain compliant with organizational standards. Automated incident response capabilities can implement self-healing scenarios like subscription recreation, dead letter message reprocessing, or traffic redirection during service degradation.
>
> - **Implement comprehensive monitoring and observability:** Service Bus observability requires monitoring across multiple dimensions including message flow health, namespace performance, dependency relationships, and application-level message processing patterns. Azure Monitor integration provides metrics, logs, and distributed tracing capabilities that enable correlation of messaging infrastructure health with application performance.
>
>    Key telemetry includes message throughput rates, queue depth trends, dead letter accumulation, throttling events, and connection pool utilization that indicate operational health and capacity constraints. Integration with Application Insights enables end-to-end tracing of message flows from publishers through Service Bus to consumers.
>
> - **Establish emergency response procedures for messaging failures:** Emergency response for Service Bus includes procedures for namespace failover, message recovery from dead letter queues, subscription recreation, and traffic redirection during service degradation. Response playbooks should document escalation procedures, recovery time objectives, and communication protocols for messaging infrastructure incidents.
>
>    Failover scenarios require careful consideration of message ordering guarantees, duplicate handling, and application reconnection logic to maintain message processing integrity. Emergency procedures should include validation steps for message routing, subscription filters, and access policies after recovery operations.
>
> - **Implement testing strategies for messaging infrastructure:** Service Bus testing requires validation of message routing, subscription filters, dead letter queue behaviors, and end-to-end message processing flows under various load conditions. Testing strategies should include functional testing of messaging patterns, integration testing of dependent services, and chaos engineering to validate resilience during infrastructure failures.
>
>    Automated testing frameworks should validate message ordering, duplicate detection, session-based processing, and auto-forwarding rules to ensure messaging behavior meets application requirements. Production testing approaches like canary deployments and feature flags enable safe validation of messaging changes.
>
> - **Evaluate messaging API selection for application requirements:** Service Bus supports both native Azure SDK and JMS 2.0 API for Java applications, with different feature capabilities and performance characteristics. Native SDKs provide access to all Service Bus features including sessions, duplicate detection, and auto-forwarding, while JMS API provides standardized messaging interface with some feature limitations.
>
>    JMS API limitations include unavailability of Service Bus Sessions, reduced performance optimization options, and limited access to Azure-specific features. API selection should consider application portability requirements, feature needs, and performance optimization priorities.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Develop [ARM templates](/azure/service-bus-messaging/service-bus-resource-manager-overview) or [Bicep modules](/azure/azure-resource-manager/templates/overview) that include Service Bus namespace configuration, messaging entities, access policies, and dependent service integrations to enable consistent Infrastructure as Code deployments. | Infrastructure as Code ensures consistent Service Bus deployments across environments, reduces configuration drift, and enables rapid recovery through automated provisioning while maintaining audit trails. |
| Create automated workflows using [Azure Logic Apps](/azure/logic-apps/logic-apps-overview) or [Azure Functions](/azure/azure-functions/functions-overview) to handle Service Bus operational tasks including dead letter queue cleanup, subscription maintenance, and access policy rotation with scheduled execution. | Operational automation reduces manual overhead, ensures consistent maintenance procedures, and enables proactive Service Bus resource management while reducing risk of human errors. |
| Configure [Azure Monitor dashboards](/azure/azure-monitor/visualize/tutorial-logs-dashboards) and [alerting rules](/azure/azure-monitor/alerts/alerts-create-new-alert-rule) for Service Bus [metrics](/azure/service-bus-messaging/monitor-service-bus-reference) including message counts, throughput rates, error conditions, and dependency health to enable proactive operational monitoring and rapid issue detection. | Monitoring enables early detection of messaging issues, supports proactive capacity management, and provides operational visibility into message flow health, reducing mean time to detection and resolution. |
| Develop incident response playbooks for Service Bus emergency scenarios including [namespace failover procedures](/azure/service-bus-messaging/service-bus-outages-disasters), [dead letter queue recovery workflows](/azure/service-bus-messaging/service-bus-geo-dr), and message reprocessing strategies. | Emergency response playbooks ensure consistent incident handling, reduce recovery time during messaging failures, and provide clear escalation procedures that maintain service continuity while minimizing message loss. |
| Integrate [Azure Resource Graph](/azure/governance/resource-graph/) queries into operational monitoring workflows to automatically identify Service Bus instances that don't meet organizational configuration standards for tier selection and private endpoint usage to ensure compliance. | Automated configuration validation through Resource Graph queries provides ongoing compliance monitoring and enables proactive identification of Service Bus instances requiring configuration updates to meet organizational standards. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Azure Service Bus.

> [!div class="checklist"]
>
> - **Conduct capacity planning for Service Bus workloads:** Service Bus capacity planning requires analyzing expected message volumes, peak throughput requirements, message size distributions, and concurrent connection patterns to determine appropriate tier selection and configuration settings. Premium tier provides dedicated messaging units with predictable performance characteristics, while Standard and Basic tiers use shared infrastructure with variable performance.
>
>    Capacity planning should account for message retention periods, dead letter queue storage, and subscription filter complexity that impact resource utilization. Load testing with realistic message patterns helps validate capacity estimates and identify potential bottlenecks before production deployment.
>
> - **Choose appropriate Service Bus tiers based on performance requirements:** Service Bus tier selection significantly impacts performance characteristics, with Basic tier supporting simple queue scenarios, Standard tier adding topics and subscriptions with shared throughput, and Premium tier providing dedicated messaging units with guaranteed performance and advanced features.
>
>    Premium tier messaging units deliver predictable throughput for both send and receive operations with consistent latency characteristics. Standard and Basic tiers use shared infrastructure where performance can vary based on overall platform load and may experience throttling during peak usage periods.
>
> - **Design scaling strategies for message processing workloads:** Service Bus scaling strategies include horizontal scaling through multiple concurrent message receivers, vertical scaling through Premium tier messaging unit increases, and architectural scaling through namespace distribution across regions. Horizontal scaling provides the most cost-effective approach for increasing message processing throughput while maintaining per-message processing consistency.
>
>    Auto-scaling capabilities in Premium tier automatically adjust messaging units based on queue depth and message processing rates, enabling dynamic response to variable workload demands.
>
>    Scaling considerations must account for message ordering requirements, session-based processing constraints, and connection pool management to optimize resource utilization and processing efficiency.
>
> - **Configure message processing patterns for optimal throughput:** Message processing patterns significantly impact Service Bus throughput and latency characteristics. Batch processing reduces the number of round trips by sending or receiving multiple messages in single operations, while parallel processing using multiple concurrent receivers increases overall throughput for high-volume scenarios.
>
>    Session-based messaging ensures ordered processing for related messages but limits parallelism to one active receiver per session, requiring careful balance between ordering guarantees and throughput requirements based on application design patterns.
>
> - **Monitor performance indicators and identify bottlenecks:** Service Bus performance monitoring requires tracking key metrics including message throughput rates, queue depth trends, processing latency, throttling events, and connection pool utilization. Performance indicators should establish baselines for normal operating conditions and enable detection of performance degradation before it impacts application functionality.
>
>    Bottleneck identification focuses on namespace-level throttling, subscription filter processing delays, dead letter queue accumulation, and client-side connection management issues. Integration with Application Insights enables end-to-end performance correlation between Service Bus operations and application processing patterns.
>
> - **Conduct performance testing for messaging infrastructure:** Service Bus performance testing requires validation of message throughput under realistic load conditions, including peak message volumes, concurrent publisher and consumer scenarios, and subscription filter processing performance. Load testing should simulate production message patterns including message sizes, processing complexity, and traffic distribution across queues and topics.
>
>    Performance testing frameworks should validate auto-scaling behavior, throttling thresholds, and recovery characteristics during load spikes. Azure Load Testing integration enables comprehensive messaging performance validation with realistic traffic patterns that exercise both Service Bus infrastructure and dependent application components.
>
> - **Optimize message handling and network performance:** Message handling optimization includes techniques like connection pooling, prefetch configuration, message batching, and payload size optimization to reduce network overhead and improve processing efficiency. Prefetch settings enable clients to receive multiple messages proactively, reducing the impact of network latency on message processing throughput.
>
>    Network performance optimization involves co-locating Service Bus namespaces with message producers and consumers in the same Azure region to minimize latency, using private endpoints to eliminate public internet routing overhead, and implementing appropriate retry policies with exponential backoff to handle transient connectivity issues efficiently.
>

<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Perform detailed capacity planning analysis using [Service Bus metrics](/azure/service-bus-messaging/service-bus-premium-messaging) and [load testing](/azure/load-testing/overview-what-is-azure-load-testing) to determine optimal tier selection and messaging unit allocation based on peak message volumes, concurrent connections, and performance requirements. | Comprehensive capacity planning ensures appropriate resource allocation that meets performance requirements while optimizing costs, preventing both over-provisioning waste and under-provisioning performance issues that could impact application functionality. |
| Select Service Bus Premium tier for production workloads requiring predictable performance, guaranteed throughput, and sub-second message processing latency, especially for scenarios with high message volumes. | Premium tier provides dedicated messaging units with consistent performance characteristics and guaranteed throughput, eliminating variable performance issues common in shared infrastructure tiers.<br><br>Premium tier enables advanced features like larger message sizes and geo-disaster recovery capabilities that support enterprise messaging requirements and business continuity planning. |
| Enable [auto-scaling](/azure/service-bus-messaging/automate-update-messaging-units) for Service Bus Premium tier with appropriate minimum and maximum throughput unit settings, and implement horizontal scaling through [multiple concurrent message receivers](/azure/service-bus-messaging/service-bus-performance-improvements) to optimize processing throughput. | Auto-scaling ensures optimal resource allocation during variable load conditions while horizontal scaling through concurrent receivers maximizes message processing throughput.<br><br>Combined scaling approaches provide better resource utilization across available messaging units and enable cost-effective capacity management that responds automatically to workload demands. |
| Implement batch message operations using [ReceiveMessages()](/azure/service-bus-messaging/service-bus-performance-improvements) and [SendMessages()](/azure/service-bus-messaging/service-bus-performance-improvements) methods to reduce network round trips and increase throughput for high-volume messaging scenarios. | Batch operations reduce network overhead and improve message processing efficiency by up to 10x compared to individual message operations, enabling higher throughput with lower resource consumption. |
| Configure [Azure Monitor performance dashboards](/azure/azure-monitor/visualize/tutorial-logs-dashboards) with [Service Bus metrics](/azure/service-bus-messaging/monitor-service-bus-reference) including message throughput, queue depth, latency percentiles, and throttling events to establish performance baselines and enable proactive bottleneck identification. | Performance monitoring dashboards provide visibility into messaging infrastructure health and enable early detection of performance degradation.<br><br>Monitoring capabilities support proactive optimization and capacity planning decisions based on actual usage patterns and performance trends that guide resource allocation and scaling strategies. |
| Implement comprehensive [load testing](/azure/load-testing/overview-what-is-azure-load-testing) using Azure Load Testing to validate Service Bus [performance](/azure/service-bus-messaging/service-bus-performance-improvements) under realistic message volumes, concurrent access patterns, and subscription filter complexity before production deployment. | Load testing validates messaging infrastructure performance under realistic conditions, identifies potential bottlenecks and capacity limits, and provides empirical data for capacity planning and performance optimization decisions. |
| Optimize Service Bus client configuration with appropriate [prefetch counts](/azure/service-bus-messaging/service-bus-performance-improvements), connection pooling, and message batching, and co-locate namespaces with applications in the same Azure region to minimize network latency. | Client optimization techniques improve message processing efficiency and reduce network overhead, while regional co-location minimizes latency and eliminates cross-region data transfer costs.<br><br>Combined optimization approaches result in better overall performance characteristics and lower operational costs for messaging workloads through reduced latency and improved resource utilization. |
| Evaluate the use of [Express Entities](/azure/service-bus-messaging/service-bus-queues-topics-subscriptions#express-entities) for scenarios where peak performance is prioritized over maximum durability guarantees. Configure express queues and topics to store messages in memory when possible to reduce message processing latency for high-throughput scenarios that can tolerate potential message loss during infrastructure failures. | Express Entities provide enhanced message processing performance through optimized in-memory storage, reducing latency for high-throughput scenarios.<br><br>Express configuration maintains acceptable durability levels for most business applications while delivering performance improvements that support demanding messaging workloads with strict latency requirements. |

## Azure policies

Azure provides an extensive set of built-in policies related to Azure Service Bus and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Service Bus Premium namespaces use customer-managed keys for encryption at rest to provide additional control over encryption keys and meet compliance requirements.

For comprehensive governance, review the [Azure Policy built-in definitions for Service Bus](/azure/governance/policy/samples/built-in-policies#service-bus) and other policies that might affect the security of the messaging infrastructure.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Next step

> [!div class="nextstepaction"]
> [Get started with Azure Service Bus queues](https://learn.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-get-started-with-queues)

## Related content

- [Azure Service Bus messaging documentation](https://learn.microsoft.com/azure/service-bus-messaging/)
- [Azure Service Bus samples](https://github.com/Azure/azure-service-bus/tree/master/samples)
- [Azure Architecture Center - Enterprise integration using message broker](https://learn.microsoft.com/azure/architecture/reference-architectures/enterprise-integration/queues-events)
