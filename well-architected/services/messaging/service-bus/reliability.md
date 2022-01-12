---
title: Service Bus and reliability
description: Focuses on the Service Bus service used in the Messaging solution to provide best-practice, configuration recommendations, and design considerations related to Reliability.
author: v-stacywray
ms.author: robbymillsap
ms.date: 01/12/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - service-bus
categories:
  - messaging
  - management-and-governance
---

# Service Bus and reliability

[Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview) is a fully-managed enterprise message broker with message queues and publish-subscribe topics. This service stores messages in a *broker* (for example, a *queue*) until the consuming party is ready to receive the messages.

Benefits include:

- Load-balancing work across competing workers.
- Safely routing and transferring data and control across service, and application boundaries.
- Coordinating transactional work that requires a high-degree of reliability.

For more information about using Service Bus, reference [Azure Service Bus Messaging documentation](/azure/service-bus-messaging/) to learn how to set up messaging that connects applications and services across on-premises and cloud environments.

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

Azure Service Bus provides an uptime SLA so that properly configured applications can send or receive messages, or perform other operations on a deployed Queue or Topic. For more information, reference the [Service Bus SLA](/support/legal/sla/service-bus/v1_1/).

Other design considerations include:

- [Express Entities](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress?view=azure-dotnet)
- [Partitioned queues and topics](/azure/service-bus-messaging/service-bus-partitioning)

Besides the documentation on [Service Bus Premium and Standard messaging tiers](/azure/service-bus-messaging/service-bus-premium-messaging), the following features are only available on the Premium SKU:

- Dedicated resources.
- Virtual network integration: Limits the networks that can connect to the Service Bus instance. Requires Service Endpoints to be enabled on the subnet. There are Trusted Microsoft services that aren't supported when Virtual Networks are implemented (for example, integration with Event Grid). For more information, reference [Allow access to Azure Service Bus namespace from specific virtual networks](/azure/service-bus-messaging/service-bus-service-endpoints).
- Private endpoints.
- IP Filtering/Firewall: Restrict connections to only defined `IPv4` addresses or `IPv4` address ranges.
- [Availability zones](/azure/availability-zones/az-overview): Provides enhanced availability by spreading replicas across availability zones within one region at no extra cost.
- Event Grid integration: [Available event types](/azure/event-grid/event-schema-service-bus?tabs=event-grid-event-schema).
- Scale messaging units.
- [Geo-Disaster Recovery](/azure/service-bus-messaging/service-bus-geo-dr) (paired namespace).
- BYOK (Bring Your Own Key): Azure Service Bus encrypts data at rest and automatically decrypts it when accessed, but customers can also bring their own customer-managed key.

When deploying Service Bus with Geo-disaster recovery and in availability zones, the SLO increases dramatically, but doesn't change the uptime SLA.

## Checklist

**Have you configured Azure Service Bus with reliability in mind?**

> [!div class="checklist"]
> - Ensure that [Service Bus Messaging Exceptions](/azure/service-bus-messaging/service-bus-messaging-exceptions) are handled properly.
> - Connect to Service Bus with the AMQP protocol and utilize Service Endpoints or Private Endpoints when possible.
> - Review the [Best Practices for performance improvements using Service Bus Messaging](/azure/service-bus-messaging/service-bus-performance-improvements?tabs=net-standard-sdk-2).
> - Configure Geo-Disaster.
> - If you need mission critical messaging with queues and topics, Service Bus Premium is recommended with Geo-Disaster Recovery.
> - Make the namespace zone redundant (only available with Premium tier).

## Configuration recommendations

Consider the following recommendations to optimize reliability when configuring Azure Service Bus:

|Recommendation|Description|
|--------------|-----------|
|Connect to Service Bus with the AMQP protocol and utilize Service Endpoints or Private Endpoints when possible.|This recommendation keeps traffic on the Azure Backbone. *Note: The default connection protocol for `Microsoft.Azure.ServiceBus` and `Windows.Azure.ServiceBus` namespaces is `AMQP`.*|
|Configure Geo-Disaster.|- [Active/Active](/azure/service-bus-messaging/service-bus-outages-disasters#active-replication) <br>- [Active/Passive](/azure/service-bus-messaging/service-bus-outages-disasters#passive-replication) <br>- [Paired Namespace (Active/Passive)](/azure/service-bus-messaging/service-bus-geo-dr) <br>- *Note: The secondary region should preferably be an [Azure paired region](/azure/availability-zones/cross-region-replication-azure)*.|
|If you need mission critical messaging with queues and topics, Service Bus Premium is recommended with Geo-Disaster Recovery.| Choosing the pattern is dependent on the business requirements and the recovery time objective (RTO).|

## Source artifacts

- To identify premium Service Bus Instances that aren't using private endpoints, use the following query:

```sql
Resources
| where
    type == 'microsoft.servicebus/namespaces'
| where
    sku.tier == 'Premium'
    and isempty(properties.privateEndpointConnections)
```

- To identify Service Bus Instances that aren't on the premium tier, use the following query:

```sql
Resources
| where
    type == 'microsoft.servicebus/namespaces'
| where
    sku.tier != 'Premium'
```

- To identify premium Service Bus Instances that aren't zone redundant, use the following query:

```sql
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
