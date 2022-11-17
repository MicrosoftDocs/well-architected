---
title: Service Bus and operational excellence
description: Focuses on the Service Bus service used in the Messaging solution to provide best-practice, configuration recommendations, and design considerations related to Operational excellence.
author: v-stacywray
ms.author: martinek
ms.date: 01/24/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - service-bus
categories:
  - messaging
  - management-and-governance
---

# Service Bus and operational excellence

 Fully manage enterprise message brokering with message queues and publish-subscribe topics using [Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview). This service stores messages in a *broker* (for example, a *queue*) until the consuming party is ready to receive the messages.

Benefits include:

- Load-balancing work across competing workers.
- Safely routing and transferring data and control across service, and application boundaries.
- Coordinating transactional work that requires a high-degree of reliability.

For more information about using Service Bus, reference [Azure Service Bus Messaging](/azure/service-bus-messaging/). Learn how to set up messaging that connects applications and services across on-premises and cloud environments.

To understand how Service Bus promotes operational excellence, reference the following topics:

- [Handling outages and disasters](/azure/service-bus-messaging/service-bus-outages-disasters)
- [Throttling operations on Azure Service Bus](/azure/service-bus-messaging/service-bus-throttling)

The following sections are specific to Azure Service Bus and operational excellence:

- Design considerations
- Configuration checklist
- Recommended configuration options
- Source artifacts

## Design considerations

Maximize reliability with an Azure Service Bus uptime Service Level Agreement (SLA). Properly configured applications can send or receive messages, or do other operations on a deployed Queue or Topic. For more information, reference the [Service Bus SLA](https://azure.microsoft.com/support/legal/sla/service-bus/v1_1/).

Other design considerations include:

- [Express Entities](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress?view=azure-dotnet&preserve-view=true)
- [Partitioned queues and topics](/azure/service-bus-messaging/service-bus-partitioning)

Besides the documentation on [Service Bus Premium and Standard messaging tiers](/azure/service-bus-messaging/service-bus-premium-messaging), the following features are only available on the Premium Stock Keeping Unit (SKU):

- Dedicated resources.
- Virtual network integration: Limits the networks that can connect to the Service Bus instance. Requires Service Endpoints to be enabled on the subnet. There are Trusted Microsoft services that are not supported when implementing Virtual Networks (for example, integration with Event Grid). For more information, reference [Allow access to Azure Service Bus namespace from specific virtual networks](/azure/service-bus-messaging/service-bus-service-endpoints).
- Private endpoints.
- IP Filtering/Firewall: Restrict connections to only defined `IPv4` addresses or `IPv4` address ranges.
- [Availability zones](/azure/availability-zones/az-overview): Provides enhanced availability by spreading replicas across availability zones within one region at no extra cost.
- Event Grid integration: [Available event types](/azure/event-grid/event-schema-service-bus?tabs=event-grid-event-schema).
- Scale messaging units.
- [Geo-Disaster Recovery](/azure/service-bus-messaging/service-bus-geo-dr) (paired namespace).
- BYOK (Bring Your Own Key): Azure Service Bus encrypts data at rest and automatically decrypts it when accessed, but customers can also bring their own customer-managed key.

When deploying Service Bus with Geo-disaster recovery and in availability zones, the Service Level Objective (SLO) increases dramatically, but does not change the uptime SLA.

## Checklist

**Have you configured Azure Service Bus with operational excellence in mind?**

> [!div class="checklist"]
> - Ensure that [Service Bus Messaging Exceptions](/azure/service-bus-messaging/service-bus-messaging-exceptions) are handled properly.
> - Connect to Service Bus with the Advanced Message Queuing Protocol (AMQP) and use Service Endpoints or Private Endpoints when possible.
> - Establish a process to actively monitor the dead-letter queue (dlq) messages.
> - Review the [Best Practices for performance improvements using Service Bus Messaging](/azure/service-bus-messaging/service-bus-performance-improvements?tabs=net-standard-sdk-2).
> - Analyze the differences between Azure Storage Queues and Azure Service Bus Queues.

## Configuration recommendations

Consider the following recommendation to optimize reliability when configuring Azure Service Bus:

|Recommendation|Description|
|--------------|-----------|
|Connect to Service Bus with the AMQP protocol and use Service Endpoints or Private Endpoints when possible.|This recommendation keeps traffic on the Azure Backbone. *Note: The default connection protocol for `Microsoft.Azure.ServiceBus` and `Windows.Azure.ServiceBus` namespaces is `AMQP`.*|
|Establish a process to actively monitor the dead-letter queue (dlq) messages.|The dead-letter queue holds messages that cannot be processed or cannot be delivered to any receiver. It is important to monitor this queue to examine the issue cause, apply required corrections, and to resubmit messages.|
|Analyze the differences between Azure Storage Queues and Azure Service Bus Queues.|You will find that Azure Service Bus Messaging Entities are more advanced, reliable, and feature-rich than Azure Storage Queues. If your requirement is for simple queue messaging without requirements for reliable messaging, then Azure Storage Queues may be a more suitable option.|

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

- To identify Service Bus Instances that are not on the premium tier, use the following query:

  ```sql
  Resources
  | where
    type == 'microsoft.servicebus/namespaces'
  | where
    sku.tier != 'Premium'
  ```

## Next step

> [!div class="nextstepaction"]
> [Queue Storage and reliability](../queue-storage/reliability.md)
