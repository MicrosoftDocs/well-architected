---
title: Performance efficiency in an IoT workload
description: See guidance and recommendations that apply to the performance efficiency pillar in an IoT workload.
author: dominicbetts
ms.author: dobett
ms.date: 09/30/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---

# Performance efficiency in your IoT workload

IoT systems include device, edge, and cloud components, and range from millions of small devices connected to the cloud to industrial solutions where a few powerful servers act as gateways for cloud connectivity. The number of devices, their physical and geographical placement, and the number of messages they send or receive are a few of the factors that define the performance efficiency of an IoT workload.

Performance efficiency also includes an IoT workload's ability to scale efficiently to meet demands. A benefit of the cloud is geographical availability and the ability to scale services on demand. You can scale Azure IoT services with little or no application downtime.

Performance efficiency represents performance relative to resource use under stated conditions. Performance efficiency measures how well a product or system, when performing its functions, meets requirements for:

- Time behavior, or response times, processing times, and throughput rates.
- Resource utilization, or amounts and types of resources used.
- Capacity, or maximum limits.

For more information about the performance efficiency pillar of the Azure Well-Architected Framework, see [Performance efficiency principles](/azure/architecture/framework/scalability/principles).

## Assess performance efficiency in your IoT workload

To assess your IoT workload against the Well-Architected Framework Performance Efficiency pillar, complete the performance efficiency questions for IoT workloads in the [Azure Well-Architected Review assessment](/assessments/?mode=pre-assessment&id=azure-architecture-review). After the assessment identifies key performance efficiency recommendations for your IoT solution, use the following content to help implement the recommendations.

## Best practices for IoT performance efficiency

IoT systems range from millions of small devices connected to the cloud, to industrial solutions where a few powerful servers act as gateways for cloud connectivity. The following practices address the specific performance efficiency challenges for IoT solutions:

- **Test for performance.** Be aware of the complexity of having sensors, devices, and gateways in geographically different locations with different characteristics, speed, and reliability of communication. Plan for this complexity in your testing, and make sure to test for failure scenarios like network disconnection. Do stress and load testing of all device, edge, and cloud components in your IoT solution.

- **Continuously monitor.** To monitor different types of devices in multiple geographical regions, use a distributed monitoring solution. Balance the amount of information monitored and sent to the cloud against memory and performance costs. Tune transmission for diagnostic scenarios, and monitor at multiple levels and layers. Expose gateway metrics for industrial or gateway-enabled solutions.

- **Plan for capacity.** An IoT solution can start with a few hundred devices or messages and grow to millions of devices and messages per minute. You can easily scale cloud services to an increase in load, but the situation can be more complex for IoT devices and gateways. IoT devices can be designed or deployed before the solution is finalized. Industrial IoT or similar industries can measure device lifespan in decades. Updating capacity by replacing devices is costly. In these scenarios, it's especially important to plan ahead.

## Architectural layers

An IoT architecture consists of a set of foundational layers. Layers are realized by using specific technologies, and the [IoT Well-Architected Framework](iot-overview.md#iot-architecture-layers) highlights options for designing and implementing each layer. There are also cross-cutting layers that support the design, building, and running of IoT and other workloads.

:::image type="content" source="media/architecture-layers.svg" alt-text="Diagram that shows the layers and cross-cutting activities in the IoT architecture." border="false":::

The following sections address the layer specifics for the performance efficiency pillar:

## Device and gateway layer

An [IoT device](/azure/iot-fundamentals/iot-introduction#iot-devices) is a computing device that connects to an IoT solution and can collect, transmit, or receive data. Gateways are connection points between devices and the cloud, or between IoT and other components.

### Optimize hardware capabilities

Upgrading or replacing hardware is costly and time consuming. Size IoT devices for required capacity and functionality in advance.

To optimize hardware capabilities:

- Run compute and input-output intensive tasks on specific hardware. For example, run machine learning (ML) algorithms on local graphics processing units (GPUs).

- Optimize existing hardware capabilities by using efficient languages and frameworks like [Embedded C](https://wikipedia.org/wiki/Embedded_C) and [Rust Embedded](https://rust-embedded.org). You can use the [Azure IoT Embedded C SDK](/azure/iot-hub/iot-hub-device-embedded-c-sdk) when developing for constrained devices, or when most of the security and communication stack is already available on the device.

- Use the [Azure IoT device SDK for C](/azure/iot-hub/iot-hub-device-sdk-c-intro) for all you need to connect to the cloud gateway. The Azure IoT Device software development kits (SDKs) manage required message translation, error handling, and retry mechanisms needed for a resilient connection.

Scaling is important for the device and gateway layer. To scale this layer:

- Use gateways as units of scale. If your solution adds [OPC UA](https://opcfoundation.org/about/opc-technologies/opc-ua) servers over time, use more edge gateways to ingest data from those servers.

- Conduct a scale assessment for all upstream layers, including cloud gateways and cloud services. For more information about using multiple IoT hubs as scale units for an IoT solution, see [Tutorial: Provision devices across load-balanced IoT hubs](/azure/iot-dps/tutorial-provision-multiple-hubs).

### Run workloads at the edge

Depending on system constraints such as network throughput or latency, consider running some workloads at the edge. Separate workloads by time constraint and required latency and response times. Use local compute for low latency and intermittently connected scenarios. Run large-scale workloads in the cloud.

At the edge, use priority queues to send different data streams in the required order. Messages send in order of priority, but Azure IoT Hub still journals messages based on receipt order.

### Optimize device connectivity

Consider the following points to optimize device connectivity:

- Use the IoT hubs that have the lowest latency to your devices. You might need IoT hubs in multiple regions when devices need to connect from different geographical locations.

- Use an open stateful connection for bi-directional communications between the devices and the IoT solution to minimize the overhead of setting up connections.

- Don't connect all devices at once, for example after a regional power outage. Use truncated exponential backoff with introduced jitter when retrying.

### Optimize offline scenarios

You can provide devices with enough information and context to work without a cloud connection and to store data locally, so they can recover from disconnections and reboots. The following strategies support offline operations:

- Ensure the device is capable of storing data locally when the device isn't connected, including logs and cached telemetry per priority.

- Set a time-to-live (TTL) on the data, so that expired data is removed automatically.

- Discard less important data when the device isn't connected, to reduce local storage needed and reduce synchronization time when the device reconnects.

- Use a cache eviction strategy such as first-in last-out (FIFO), last-in first-out (LIFO), or priority-based if edge device storage reaches capacity.

- Consider using a separate disk or disk controller to store data, so the device runtime or application can continue to work when low on storage.

Use [device twins and module twins](/azure/iot-hub/iot-hub-portal-csharp-module-twin-getstarted) to asynchronously sync state information between devices and the cloud, even when the device isn't currently connected to the cloud gateway. Device and module twins contain only the current state at a point in time, not any history or removed information.

## Ingestion and communication layer

The data ingestion and communication layer sends data from the devices to the IoT solution. Patterns of communication between devices and the IoT solution include:

- Device-to-cloud messages.
- Cloud-to-device messages.
- File uploads.
- Device twins.
- Direct methods.

### Optimize messaging efficiency

The number and size of device to cloud messages is an important parameter for IoT solution performance efficiency. Azure IoT services such as [IoT Hub](https://azure.microsoft.com/services/iot-hub) and [Azure IoT Central](https://azure.microsoft.com/services/iot-central) define message limits per tier, which affects both solution performance and cost.

Consider the following messaging recommendations:

- IoT Hub and IoT Central calculate daily quota message counts based on a [4-KB message size](/azure/iot-hub/iot-hub-devguide-quotas-throttling#quotas-and-throttling). Sending smaller messages leaves some capacity unused. In general, use message sizes close to the 4-KB boundary. Group smaller device-to-cloud messages into larger messages to reduce the total number of messages, but consider the introduced latency when combining messages.

- Avoid chatty communication. For device-to-device or module-to-module edge communication, don't design interactions that send many small messages.

- Use built-in Azure IoT Edge SDK message batching for Advanced Message Queuing Protocol (AMQP) to send multiple telemetry messages to the cloud.

- Use application-level batching by combining multiple smaller messages at the downstream device and sending larger messages to the edge gateway. This batching limits the message overhead and reduces writes to local edge disk storage.

- Use AMQP connection multiplexing to reduce the dependency on Transmission Control Protocol (TCP) connections limits per SDK client. With AMQP connection multiplexing, multiple devices can use a single TCP connection to IoT Hub.

- Use direct methods for request-reply interactions that can succeed or fail immediately, after a user-specified timeout. This approach is useful for scenarios where the course of action is different depending on whether the device responded.

- Use device twins for device state information, including metadata and configurations. IoT Hub maintains a device twin for each device that you connect.

### Understand messaging quotas and throttling

The IoT Hub tier sets cloud gateway per-unit limits. The messaging *quota* defines sustained throughput and sustained send rates for the tier. IoT Hub can handle loads above these quotas for short durations to resiliently handle bursts or load overshoots.

Another important limit is the hourly or daily service load or *throttle* limit. Throttle limits protect an IoT hub from too much load for too long a time.

The following diagrams show the relationship between load, quota, and throttle limits. The left diagram shows that IoT Hub can handle sustained or constant high load up to the level of the quota for the IoT Hub tier. The right diagram shows that IoT Hub can handle load that is changing over time, as long as it doesn't hit the throttle limit and on average isn't above the quota for the IoT Hub tier.

:::image type="content" source="media/load-quota-throttle.png" alt-text="Diagrams showing high load constantly below quota compared to lower load with occasional spikes above quota but below throttle limit." border="false":::

### Optimize message processing

Messages from a device or gateway might need to be translated, processed, or enriched with additional information before storage. This step could be time-consuming, so it's important to evaluate the effect on performance. Some recommendations conflict, such as using compression for optimizing data transfer versus avoiding cloud processing in decrypting messages. These recommendations need to be balanced and evaluated against other architectural pillars and solution requirements.

To optimize cloud data processing performance:

- Select the data format for sending data to the cloud based on bandwidth cost and performance vs. performance improvements from less cloud data processing. Consider using [IoT Hub message enrichment](/azure/iot-hub/iot-hub-message-enrichments-overview) to add context to device messages.

- Do time-critical event processing on ingested data as it arrives, instead of storing unprocessed data and requiring complex queries to acquire the data. For time-critical event processing, consider the impacts of late arrival and windowing. Evaluate depending on use case, for example critical alarm handling versus message enrichment.

- [Select the right IoT Hub tier](/azure/iot-hub/iot-hub-scaling), Basic or Standard, based on solution requirements. Be aware of features that the Basic tier doesn't support.

- Select the right IoT Hub tier size, 1, 2, or 3, and the number of instances based on data throughput, quotas, and operation throttles. For IoT Central, select the right tier, Standard 0, Standard 1, or Standard 2, based on the number of messages sent from devices to the cloud.

- Consider using Azure Event Grid for publish-subscribe event routing. For more information, see [React to IoT Hub events by using Event Grid to trigger actions](/azure/iot-hub/iot-hub-event-grid) and [Compare Event Grid routing to IoT Hub](/azure/iot-hub/iot-hub-event-grid-routing-comparison).

### Prioritize data

Some data sent from devices to the cloud might be more important than other data. Classifying and handling the data based on priority is a good practice for performance efficiency.

For example, a thermometer sensor sends temperature, humidity, and other telemetry, but also sends an alarm when temperature is outside a defined range. The system classifies the alarm message as higher priority and handles it differently than the temperature telemetry.


Consider the following recommendations for data classification and handling:

- Use IoT Edge priority queues to make sure important data is prioritized while sending to IoT Hub. IoT Edge buffers messages when there's no connectivity, but after the connection is restored, sends all buffered messages in priority order first, followed by new messages.
- Use IoT Hub message routing to separate routes for different data priorities depending on use case. IoT Hub message routing adds some latency.
- Save and send low priority data at longer intervals, or by using batch or file uploads. Malware detection on uploaded files increases latency.
- Separate messages based on time constraints. For example, send messages to IoT Hub directly when there's a time constraint, and utilize file upload via IoT Hub or batch data transfer like Azure Data Factory if there's no time constraint. You can use the IoT Edge blob module for file upload.

## Device management and modeling layer

Different types of devices can connect to an IoT solution, and an IoT solution can connect to many devices and gateways at the same time. Besides connecting and configuring devices and gateways, the IoT solution must understand the data the devices and gateways capture and ingest, and must transfer and contextualize that data.

IoT components can use different protocols, connectivity, data ingestion frequencies, and communication patterns. The IoT solution must be able to manage which devices and gateways are connected and how they're configured.

To manage devices and configurations for performance efficiency:

- Optimize sizing based on device and message load.
- Know the number of messages the cloud gateway can handle, depending on tier and number of units.
- Account for anomalies in sustained throughput due to data distribution, seasonality, and bursting.
- Use multiple cloud gateways when the IoT solution must manage millions of devices. Use DPS to assign devices assigned to one of the IoT hubs.

### Provision devices with DPS

Use DPS to set up a connection to an IoT hub during provisioning, when the IoT Hub connection isn't available anymore, or during device reboot.

- Use the DPS evenly weighted distribution policy to adjust the weight for provisioning, based on use case. For more information, see [How the allocation policy assigns devices to IoT Hubs](/azure/iot-dps/tutorial-provision-multiple-hubs#how-the-allocation-policy-assigns-devices-to-iot-hubs).
- Consider provisioning devices to the IoT solution over a period of time, distributed or in smaller batches, to balance the DPS load and quota. When onboarding in batches, plan for the batches and overall migration timeline. Account for DPS limits in number of operations, device registrations, and maximum connections per minute, including latency and retries.
- Use DPS to allocate devices to IoT Hubs in different regions based on latency.
- Use a caching strategy for the DPS connection string to reduce DPS reconnect operations.

### Manage downstream devices

An IoT solution is horizontally scalable if it has multiple gateways or edge devices per site or location and downstream devices that can connect to any of these gateways or edge devices.

- Use multiple gateways and edge devices in *translation* mode when the number of downstream devices, their messages, and message sizes will change over time, and their protocol or message must be translated. Gateways and edge devices in translation mode can translate protocols or messages to and from downstream devices, but finding the gateway that a downstream device connects to requires mapping. Account for added message translation and buffering overhead at the gateway or edge device when you use translation mode.

- Use multiple gateways and edge devices in *transparent* mode to connect downstream Message Queue Telemetry Transport (MQTT) or AMQP devices when their number can change over time per site or location. Gateways and edge devices in transparent mode can connect MQTT/AMQP devices for bi-directional communication. Account for added message buffering, storage, and configuration overhead at the gateway or edge device when you use transparent mode.

## Transport layer

The transport layer handles connections between a device and the IoT solution, transforming IoT messages to network packages and sending them over the physical network. IoT solutions commonly use AMQP and MQTT connection protocols.

### Optimize resource usage

The connection between a device and the cloud needs to be secure, reliable, and scalable to handle the targeted number of devices and messages.

- Use an open stateful connection from a device to the cloud gateway. IoT Hub is optimized for managing millions of open stateful connections by using MQTT, AMQP, or WebSocket protocols). Keep open connections to the devices to minimize the overhead of security handshakes, authentication, and authorization. This practice improves performance and greatly reduces required bandwidth.

- Use an AMQP protocol that supports multiplexing multiple channels on a single connection to minimize the number of open connections the cloud gateway requires. By using multiplexing, a transparent gateway can connect multiple leaf devices using their own channels over a single connection.

- Use the device and module twins cloud gateway patterns to asynchronously exchange state information between devices and the cloud.

- Configure DPS to move the device state when a device connects to another cloud gateway.

### Optimize data communication

The number and size of device to cloud messages affects performance and cost. Evaluating data communication is key to performance efficiency in your IoT workload.

- Use an efficient data format and encoding that doesn't use extensive bandwidth to send data to the cloud. For low bandwidth networks, consider using a compressed or binary format, but understand the overhead of uncompressing or converting the data in the cloud.

- Consider storing high-volume data locally and uploading it hourly or daily.

- Group many small device-to-cloud messages into fewer larger messages to reduce the total number. However, don't send only large messages, but balance between average message size and throughput.

## Storage layer

The different types of data collected and referenced in an IoT solution often require storage types that are specialized and optimized for different scenarios on devices, gateways, and cloud. Data that must be available in multiple geographical regions globally or locally, and in some cases replicated to optimize latency, increases IoT storage complexity.

- Use a time-series database for storing time-series data that has timestamps and values. Enrich time-series data telemetry with columns for filtering, for example CustomerID, RoomID, or other use-case specific columns.

- Use device and gateway storage for caching data, or to keep data when disconnected. Account for required storage space. Don't keep all data, but use downsampling, store only aggregates, or store data for limited time periods.

- Consider separating data ingestion and event processing storage from reporting and integration storage needs.

- Use the data storage type that fits the need for required throughput, size, retention period, data volume, CRUD requirements, and regional replication. Some examples are Azure Data Lake Storage, Azure Data Explorer, Azure SQL, and Azure Cosmos DB.

## Event processing and analytics layer

You can process data that devices generate before sending it to or within the IoT solution. Data processing can include translation, contextualization, filtering and routing, or more advanced analytics like trend analysis or anomaly detection.

### Optimize edge versus cloud processing

Run real-time and near real-time workloads, or small, optimized, low-latency processing with time constraints, on devices or at the edge by using local compute. Run larger workloads, or those that have additional or external data or compute dependencies, in the cloud.

For example, run a machine learning algorithm at the edge to count people in a video stream, and send an event containing the count to the cloud. Use the cloud to compare trends between different factories.

Run analytics workloads at the edge by using the Stream Analytics Edge module. For example, you can run anomaly detection at the edge and label the events sent to the cloud with the detected anomaly. When you run analytics at the edge, account for extra latency, late arrival, and windowing impact.

Be aware of the overhead of an edge workload with many connected downstream devices. The edge node must forward or process all messages and handle caching all the data in case of intermittent cloud connectivity. Validate the performance impact on your solution by testing with the planned maximum of downstream devices and messages per edge node. Be aware of the performance impact that message translation or enrichment can have on edge, IoT Hub, or cloud event processing.

### Categorize individual workloads

Separate workloads by time constraint and required latency and response times, for example response within seconds vs. batch per hour. Hybrid hardware systems-on-a-chip (SoCs) can support workloads on the device level.

At the edge, use priority queues to separate different data streams with different priorities and time to live (TTL). For example, alarms should always be sent first but have a lower TTL than telemetry.

In the cloud, you can use consumer groups on Azure Event Hubs to separate out different data streams and handle and scale alarms differently from telemetry. You can also use IoT Hub routes to separate out different data streams, with filtering and separate endpoints. IoT Hub message routing adds some latency. Use Event Hubs, Azure Event Grid, or Azure Service Bus to distribute workloads while protecting against back pressure in the cloud.

Overly complex IoT Hub routing rules can affect throughput, especially routing rules with message body JSON filters, where every message needs to be deserialized and scanned.

### Handle high-volume cloud data

To optimize performance efficiency for high-volume cloud data:

- Use out-of-the-box service integration between IoT Hub and data destinations like Azure Data Lake Storage and Azure Data Explorer that are already optimized for high performance throughput.

- Use the Event Hubs SDK to develop custom ingestion from an IoT hub with the included event processor. The event processor can rebalance devices and hosts.

- Use the right number of IoT Hub partitions and consumer groups for the number of simultaneous data readers and required throughput.

## Integration layer

The integration layer connects an IoT solution to other services and business applications.

- Separate the IoT solution ingestion pipeline from integration processing. Make sure complex queries or loads from the integration layer don't affect data ingestion performance.

- Use well-defined and versioned APIs for access to IoT data and commands.

- Avoid tools for end users to create user-defined queries against IoT data storage. Consider using separate data stores for integration and for reporting.

## DevOps layer

Use the following DevOps mechanisms to maximize performance efficiency:

- A [connected registry](/azure/container-registry/intro-connected-registry) for local caching and deployment of container images.

- IoT Hub to update deployments to multiple devices at once, including devices and gateways.

- Device twins and module twins to update device configurations in a scalable and efficient way.

- Performance testing, including stress and load tests to replicate the production environment, such as location and heterogenous devices.

### Monitoring

Use Azure Monitor to collect IoT Hub metrics with alerts for critical metrics. Set up Azure Monitor alerts based on current scale limits, such as device to cloud messages sent per second. Set the alert to a percentage of the limit, such as 75%, for pre-notification of upcoming scalability limits. Also set up Azure Monitor alerts for logs and metrics such as number of throttling errors.

Set Azure Service Health service alerts to trigger notifications when IoT Hub status changes.

## Next steps

> [!div class="nextstepaction"]
> [Reliability in your IoT workload](iot-reliability.md)

## Related resources

- [Performance efficiency principles](/azure/architecture/framework/scalability/principles).
- [Reference: IoT Hub endpoints](/azure/iot-hub/iot-hub-devguide-endpoints#list-of-built-in-iot-hub-endpoints)
- [IoT Hub message size](/azure/iot-hub/iot-hub-devguide-messages-construct#message-size)
- [Select an Azure data store for your application](/azure/architecture/guide/technology-choices/data-store-decision-tree)
- [Monitoring Azure IoT Hub data reference](/azure/iot-hub/monitor-iot-hub-reference)
