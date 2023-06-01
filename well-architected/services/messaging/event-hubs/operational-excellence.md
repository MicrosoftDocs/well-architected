---
title: Event Hubs and operational excellence
description: Focuses on the Event Hubs service used in the Messaging solution to provide best-practice, configuration recommendations, and design considerations related to Operational excellence.
author: martinekuan
ms.author: martinek
ms.date: 01/12/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - azure-event-hubs
categories:
  - messaging
  - management-and-governance
---

# Event Hubs and operational excellence

[Azure Event Hubs](/azure/event-hubs/event-hubs-about) is a scalable event processing service that ingests and processes large volumes of events and data, with low latency and high reliability. It can receive and process millions of events per second. Data sent to an event hub can be transformed and stored by using any real-time analytics provider or batching and storage adapters.

For more information about using Event Hubs, reference the [Azure Event Hubs documentation](/azure/event-hubs/) to learn how to use Event Hubs to ingest millions of events per second from connected devices and applications.

To understand ways using Event Hubs helps you achieve operational excellence for your workload, reference the following articles:

- [Monitor Azure Event Hubs](/azure/event-hubs/monitor-event-hubs)
- [Stream Azure Diagnostics data using Event Hubs](/azure/azure-monitor/agents/diagnostics-extension-stream-event-hubs)
- [Scaling with Event Hubs](/azure/event-hubs/event-hubs-scalability)

The following sections are specific to Azure Event Hubs and operational excellence:

- Design considerations
- Configuration checklist
- Recommended configuration options
- Source artifacts

## Design considerations

Azure Event Hubs provides an uptime SLA. For more information, reference [SLA for Event Hubs](https://azure.microsoft.com/support/legal/sla/event-hubs/v1_0/).

## Checklist

**Have you configured Azure Event Hubs with operational excellence in mind?**

> [!div class="checklist"]
> - Create SendOnly and ListenOnly policies for the event publisher and consumer, respectively.
> - When using the SDK to send events to Event Hubs, ensure the exceptions thrown by the retry policy (`EventHubsException` or `OperationCancelledException`) are properly caught.
> - In high-throughput scenarios, use batched events.
> - Every consumer can read events from one to `32` partitions.
> - When developing new applications, use `EventProcessorClient` (.NET and Java) or `EventHubConsumerClient` (Python and JavaScript) as the client SDK.
> - As part of your solution-wide availability and disaster recovery strategy, consider enabling the Event Hubs geo disaster-recovery option.
> - When a solution has a large number of independent event publishers, consider using Event Publishers for fine-grained access control.
> - Don't publish events to a specific partition.
> - When publishing events frequently, use the AMQP protocol when possible.
> - The number of partitions reflect the degree of downstream parallelism you can achieve.
> - Ensure each consuming application uses a separate consumer group and only one active receiver per consumer group is in place.
> - When using the Capture feature, carefully consider the configuration of the time window and file size, especially with low event volumes.

## Configuration recommendations

Consider the following recommendations to optimize reliability when configuring Azure Event Hubs:

|Recommendation|Description|
|--------------|-----------|
|When using the SDK to send events to Event Hubs, ensure the exceptions thrown by the retry policy (`EventHubsException` or `OperationCancelledException`) are properly caught.|When using `HTTPS`, ensure a proper retry pattern is implemented.|
|In high-throughput scenarios, use batched events.|The service will deliver a `json` array with multiple events to the subscribers, instead of an array with one event. The consuming application must process these arrays.|
|Every consumer can read events from one to `32` partitions.|To achieve maximum scale on the side of the consuming application, every consumer should read from a single partition.|
|When developing new applications, use `EventProcessorClient` (.NET and Java) or `EventHubConsumerClient` (Python and JavaScript) as the client SDK.|`EventProcessorHost` has been deprecated.|
|As part of your solution-wide availability and disaster recovery strategy, consider enabling the Event Hubs geo disaster-recovery option.|This option allows the creation of a secondary namespace in a different region. Only the active namespace receives messages at any time. Messages and events aren't replicated to the secondary region. The RTO for the regional failover is *up to 30 minutes*. Confirm this RTO aligns with the requirements of the customer and fits in the broader availability strategy. If a higher RTO is required, consider implementing a client-side failover pattern.|
|When a solution has a large number of independent event publishers, consider using Event Publishers for fine-grained access control.|Event Publishers automatically set the partition key to the publisher name, so this feature should only be used if the events originate from all publishers evenly.|
|Don't publish events to a specific partition.|If ordering events is essential, implement ordering downstream or use a different messaging service instead.|
|When publishing events frequently, use the AMQP protocol when possible.| AMQP has higher network costs when initializing the session, but `HTTPS` requires TLS overhead for every request. AMQP has higher performance for frequent publishers.|
|The number of partitions reflect the degree of downstream parallelism you can achieve.|For maximum throughput, use the maximum number of partitions (`32`) when creating the Event Hub. The maximum number of partitions will allow you to scale up to `32` concurrent processing entities and will offer the highest send and receive availability.|
|When using the Capture feature, carefully consider the configuration of the time window and file size, especially with low event volumes.|Data Lake will charge for minimal file size for storage (gen1) or minimal transaction size (gen2). If you set the time window so low that the file hasn't reached minimum size, you'll incur extra cost.|

## Source artifacts

To find Event Hubs namespaces with **Basic** SKU, use the following query:

```sql
Resources 
| where type == 'microsoft.eventhub/namespaces'
| where sku.name == 'Basic'
| project resourceGroup, name, sku.name
```

## Next step

> [!div class="nextstepaction"]
> [Service Bus and reliability](../service-bus/reliability.md)