---
title: IoT and reliability
description: Focuses on the IoT Hub service used in the Messaging solution to provide best-practice, configuration recommendations, and design considerations related to Reliability.
author: v-stacywray
ms.author: martinekuan
ms.date: 01/19/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - iot-hub
categories:
  - messaging
  - management-and-governance
---

# IoT Hub and reliability

[Azure IoT Hub](/azure/iot-hub/) is a managed service hosted in the cloud that acts as a central message hub for communication between an IoT application and its attached devices. You can connect millions of devices and their backend solutions reliably and securely. Almost any device can be connected to an IoT Hub.

IoT Hub supports monitoring to help you track device creation, device connections, and device failures.

IoT Hub also supports the following messaging patterns:

- Device-to-cloud telemetry
- Uploading files from devices
- Request-reply methods to control your devices from the cloud

For more information about IoT Hub, reference [IoT Concepts and Azure IoT Hub](/azure/iot-hub/iot-concepts-and-iot-hub).

To understand how IoT Hub supports a reliable workload, reference the following topics:

- [IoT Hub high availability and disaster recovery](/azure/iot-hub/iot-hub-ha-dr)
- [How to achieve cross-region High Availability with IoT Hub](/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha)
- [How to clone an Azure IoT Hub to another region](/azure/iot-hub/iot-hub-how-to-clone)

The following sections are specific to Azure IoT Hub and reliability:

- Design considerations
- Configuration checklist
- Recommended configuration options

## Design considerations

For more information about the Azure IoT Hub Service Level Agreement, reference [SLA for Azure IoT Hub](https://azure.microsoft.com/support/legal/sla/iot-hub/v1_2/).

## Checklist

**Have you configured Azure IoT Hub with reliability in mind?**

> [!div class="checklist"]
> - Provision a second IoT Hub in another region and have routing logic on the device.
> - Use the `AMQP` or `MQTT` protocol when sending events frequently.
> - Use only certificates validated by a root CA in the production environment if you're using [X.509 certificates](/azure/iot-hub/tutorial-x509-scripts#get-x509-ca-certificates) for the device connection.
> - For maximum throughput, use the maximum number of partitions (`32`) when creating the IoT Hub, if you're planning to use the built-in endpoint.
> - For scaling, increase the tier and allocated IoT Hub units instead of adding more than one IoT Hub per region.
> - In high-throughput scenarios, use batched events.
> - If you require the minimum possible latency, don't use routing and read the events from the built-in endpoint.
> - As part of your solution-wide availability and disaster recovery strategy, consider using the IoT Hub [cross-region Disaster Recovery option](/azure/iot-hub/iot-hub-ha-dr#cross-region-dr).
> - When reading device telemetry from the built-in Event Hub-compatible endpoint, refer to the [Event Hub consumers recommendation](../event-hubs/reliability.md).
> - When using an SDK to send events to IoT Hubs, ensure the exceptions thrown by the retry policy (`EventHubsException` or `OperationCancelledException`) are properly caught.
> - To avoid telemetry interruption due to throttling and a fully used quota, consider adding a [custom auto-scaling solution](/azure/iot-hub/iot-hub-scaling#auto-scale).

## Configuration recommendations

Consider the following recommendations to optimize reliability when configuring Azure IoT Hub:

|Recommendation|Description|
|--------------|-----------|
|Provision a second IoT Hub in another region and have routing logic on the device.|These configurations can be further enhanced with a [Concierge Service](/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha).|
|Use the `AMQP` or `MQTT` protocol when sending events frequently.|`AMQP` and `MQTT` have higher network costs when initializing the session, however `HTTPS` requires extra TLS overhead for every request. `AMQP` and `MQTT` have higher performance for frequent publishers.|
|Use only certificates validated by a root CA in the production environment if you're using [X.509 certificates](/azure/iot-hub/tutorial-x509-scripts#get-x509-ca-certificates) for the device connection.|Make sure you have processes in place to update the certificate before they expire.|
|For maximum throughput, use the maximum number of partitions (`32`) when creating the IoT Hub, if you're planning to use the built-in endpoint.|The number of device-to-cloud partitions for the Event Hub-compatible endpoint reflect the degree of downstream parallelism you can achieve. This will allow you to scale up to `32` concurrent processing entities and will offer the highest send and receive availability. This number can't be changed after creation.|
|For scaling, increase the tier and allocated IoT Hub units instead of adding more than one IoT Hub per region.|Adding more than one IoT Hub per region doesn't offer extra resiliency because all hubs can run on the same underlying cluster.|
|In high-throughput scenarios, use batched events.|The service will deliver an array with multiple events to the consumers, instead of an array with one event. The consuming application must process these arrays.|
|If you require the minimum possible latency, don't use routing and read the events from the built-in endpoint.|When using message routing in IoT Hub, latency of the message delivery increases. On average, latency shouldn't exceed `500 ms`, but there's no guarantee for the delivery latency.|
|As part of your solution-wide availability and disaster recovery strategy, consider using the IoT Hub [cross-region Disaster Recovery option](/azure/iot-hub/iot-hub-ha-dr#cross-region-dr).|This option will move the IoT Hub endpoint to the paired Azure region. Only the device registry gets replicated. Events aren't replicated to the secondary region. *The RTO for the customer-initiated failover is between 10 minutes to a couple of hours. For a Microsoft-initiated failover, the RTO is `2-26` hours. Confirm this RTO aligns with the requirements of the customer and fits in the broader availability strategy. If a higher RTO is required, consider implementing a client-side failover pattern.*|
|When using an SDK to send events to IoT Hub, ensure the exceptions thrown by the retry policy (`EventHubsException` or `OperationCancelledException`) are properly caught.|When using `HTTPS`, implement a proper retry pattern.|

## Next step

> [!div class="nextstepaction"]
> [IoT Hub and operational excellence](operational-excellence.md)