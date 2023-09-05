---
title: Service Bus and reliability
description: Focuses on the Service Bus service used in the Messaging solution to provide best-practice, configuration recommendations, and design considerations related to Reliability.
author: martinekuan
ms.author: martinek
ms.date: 01/24/2022
ms.topic: conceptual
products:
  - azure-service-bus
categories:
  - messaging
  - management-and-governance
---

# Service Bus and reliability

 Fully manage enterprise message brokering with message queues and publish-subscribe topics used in [Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview). This service stores messages in a *broker* (for example, a *queue*) until the consuming party is ready to receive the messages.

Benefits include:

- Load-balancing across competing workers.
- Safely routing and transferring data and control across service, and application boundaries.
- Coordinating transactional work that requires a high-degree of reliability.

For more information about using Service Bus, reference [Azure Service Bus Messaging](/azure/service-bus-messaging/). Learn how to set up messaging that connects applications and services across on-premises and cloud environments.

To understand how Service Bus contributes to a reliable workload, reference the following topics:

- [Asynchronous messaging patterns and high availability](/azure/service-bus-messaging/service-bus-async-messaging)
- [Azure Service Bus Geo-disaster recovery](/azure/service-bus-messaging/service-bus-geo-dr)
- [Handling outages and disasters](/azure/service-bus-messaging/service-bus-outages-disasters)

The following sections are specific to Azure Service Bus and reliability:

- Design considerations
- Configuration checklist
- Recommended configuration options
- Source artifacts

## Design considerations

Maximize reliability with an Azure Service Bus uptime SLA. Properly configured applications can send or receive messages, or do other operations on a deployed Queue or Topic. For more information, reference the [Service Bus SLA](https://azure.microsoft.com/support/legal/sla/service-bus/v1_1/).

Other design considerations include:

- [Express Entities](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress?view=azure-dotnet&preserve-view=true)
- [Partitioned queues and topics](/azure/service-bus-messaging/service-bus-partitioning)

Besides the documentation on [Service Bus Premium and Standard messaging tiers](/azure/service-bus-messaging/service-bus-premium-messaging), the following features are only available on the Premium Stock Keeping Unit (SKU):

- Dedicated resources.
- Virtual network integration: Limits the networks that can connect to the Service Bus instance. Requires Service Endpoints to be enabled on the subnet. There are Trusted Microsoft services that are not supported when implementing Virtual Networks(for example, integration with Event Grid). For more information, reference [Allow access to Azure Service Bus namespace from specific virtual networks](/azure/service-bus-messaging/service-bus-service-endpoints).
- Private endpoints.
- [IP Filtering/Firewall](/azure/service-bus-messaging/service-bus-ip-filtering): Restrict connections to only defined `IPv4` addresses or `IPv4` address ranges.
- [Availability zones](/azure/availability-zones/az-overview): Provides enhanced availability by spreading replicas across availability zones within one region at no extra cost.
- Event Grid integration: [Available event types](/azure/event-grid/event-schema-service-bus?tabs=event-grid-event-schema).
- [Scale messaging units](/azure/service-bus-messaging/service-bus-premium-messaging#how-many-messaging-units-are-needed)).
- [Geo-Disaster Recovery](/azure/service-bus-messaging/service-bus-geo-dr) (paired namespace).
- [CMK (Customer Managed Key)](/azure/service-bus-messaging/configure-customer-managed-key): Azure Service Bus encrypts data at rest and automatically decrypts it when accessed, but customers can also bring their own customer-managed key.

When deploying Service Bus with Geo-disaster recovery and in availability zones, the Service Level Operation (SLO) increases dramatically, but does not change the uptime SLA.

## Checklist

**Have you configured Azure Service Bus with reliability in mind?**

> [!div class="checklist"]
> - Evaluate Premium tier benefits of Azure Service Bus.
> - Ensure that [Service Bus Messaging Exceptions](/azure/service-bus-messaging/service-bus-messaging-exceptions) are handled properly.
> - Connect to Service Bus with the Advanced Messaging Queue Protocol (AMQP) and use Service Endpoints or Private Endpoints when possible.
> - Review the [Best Practices for performance improvements using Service Bus Messaging](/azure/service-bus-messaging/service-bus-performance-improvements?tabs=net-standard-sdk-2).
> - Implement geo-replication on the sender and receiver side to protect against outages and disasters.
> - Configure Geo-Disaster.
> - If you need mission-critical messaging with queues and topics, Service Bus Premium is recommended with Geo-Disaster Recovery.
> - Configure Zone Redundancy in the Service Bus namespace (*only available with Premium tier*).
> - Implement high availability for the Service Bus namespace.
> - Ensure related messages are delivered in guaranteed order.
> - Evaluate different Java Messaging Service (JMS) features through the JMS API.
> - Use .NET Nuget packages to communicate with Service Bus messaging entities.
> - Implement resilience for transient fault handling when sending or receiving messages.
> - Implement auto-scaling of messaging units.

## Configuration recommendations

Consider the following recommendations to optimize reliability when configuring Azure Service Bus:

|Recommendation|Description|
|--------------|-----------|
|Evaluate Premium tier benefits of Azure Service Bus.|Consider migrating to the Premium tier of Service Bus to take advantage of platform-supported outage and disaster protection.|
|Connect to Service Bus with the AMQP protocol and use Service Endpoints or Private Endpoints when possible.|This recommendation keeps traffic on the Azure Backbone. *Note: The default connection protocol for `Microsoft.Azure.ServiceBus` and `Windows.Azure.ServiceBus` namespaces is `AMQP`.*|
|Implement geo-replication on the sender and receiver side to protect against outages and disasters.|Standard tier supports only the implementation of sender and receiver-side geo-redundancy. An outage or disaster in an Azure Region could cause downtime for your solution.|
|Configure Geo-Disaster.|- [Active/Active](/azure/service-bus-messaging/service-bus-outages-disasters#active-replication) <br>- [Active/Passive](/azure/service-bus-messaging/service-bus-outages-disasters#passive-replication) <br>- [Paired Namespace (Active/Passive)](/azure/service-bus-messaging/service-bus-geo-dr) <br>- *Note: The secondary region should preferably be an [Azure paired region](/azure/availability-zones/cross-region-replication-azure)*.|
|If you need mission-critical messaging with queues and topics, Service Bus Premium is recommended with Geo-Disaster Recovery.| Choosing the pattern is dependent on the business requirements and the recovery time objective (RTO).|
|Configure Zone Redundancy in the Service Bus namespace (*only available with Premium tier*).|Zone Redundancy includes three copies of the messaging store. One zone is allocated as the primary messaging store and the other zones are allocated as secondaries. If the primary zone becomes unavailable, a secondary is promoted to primary with no perceivable downtime. *Availability Zones are available in a subset of Azure Regions with new regions added regularly.*|
|Implement high availability for the Service Bus namespace.|Premium tier supports Geo-disaster recovery and replication at the namespace level. At this level, Premium tier provides high availability for metadata disaster recovery using primary and secondary disaster recovery namespaces.|
|Ensure related messages are delivered in guaranteed order.|Be aware of the requirement to set a Partition Key, Session ID, or Message ID on each message to ensure related messages send to the same partition in the messaging entity.|
|Evaluate different JMS features through the JMS API.|Features available through the JMS 2.0 API (and its Software Development Kit (SDK)) are not the same as the features available through the native SDK. For example, Service Bus Sessions are not available in JMS.|
|Implement resilience for transient fault handling when sending or receiving messages.|It is essential to implement suitable transient fault handling and error handling for send and receive operations to maintain throughput and to prevent message loss.|
|Implement [auto-scaling of messaging units](/azure/service-bus-messaging/automate-update-messaging-units), to ensure that you have enough resources available for your workloads.|

## Source artifacts

- To identify premium Service Bus Instances that are not using private endpoints, use the following query:

  ```Kusto
  Resources
  | where
    type == 'microsoft.servicebus/namespaces'
  | where
    sku.tier == 'Premium'
    and isempty(properties.privateEndpointConnections)
  ```

- To identify Service Bus Instances that are not on the premium tier, use the following query:

  ```Kusto
  Resources
  | where
    type == 'microsoft.servicebus/namespaces'
  | where
    sku.tier != 'Premium'
  ```

- To identify premium Service Bus Instances that are not zone redundant, use the following query:

  ```Kusto
  Resources
  | where
    type == 'microsoft.servicebus/namespaces'
  | where
    sku.tier == 'Premium'
    and properties.zoneRedundant == 'false'
  ```

## Next step

> [!div class="nextstepaction"]
> [Service Bus and operational excellence](operational-excellence.md)
