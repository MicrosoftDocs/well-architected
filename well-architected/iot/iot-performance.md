---
title: Performance efficiency in an IoT workload
description: Includes guidance and recommendations that apply to the performance efficiency pillar in an IoT workload.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---

# Performance efficiency in your IoT workload

Performance efficiency is the ability of your workload to scale to meet the demands in an efficient manner.

IoT workloads span from millions of small devices connected to the cloud to industrial solutions where the devices are a few powerful servers acting as gateways for cloud connectivity. A device, or more specifically an [IoT Device](/azure/iot-fundamentals/iot-introduction), may generally be defined as a computing device that collects and transmits information collected from sensors. The number of devices, the number of messages sent and the physical geographical placement of the devices are a few of the factors that define the performance efficiency of an IoT workload.

A benefit of the cloud is the geographical availability and the capability to scale services to meet the demands. Scale our IoT Services without, or with minimal, application downtime.

## Prerequisites

To assess your IoT workload using the tenets found in the Microsoft Azure Well-Architected Framework, see the WAF IoT Performance Efficiency Review assessment.
<!-- TODO: add link to assessment -->

After you take the assessment, this guide will help you address the key areas that surfaced for your solution.

## Principles

This pillar represents the performance relative to the resources used under stated conditions. This characteristic is composed of the following subcharacteristics:

- **Time behavior** - Degree to which the response times, processing times and throughput rates of a product or system, when performing its functions, meet requirements.

- **Resource utilization** - Degree to which the amounts and types of resources used by a product or system, when performing its functions, meet requirements.

- **Capacity** - Degree to which the maximum limits of a product or system parameter meet requirements.

Overall performance principles can be found at [Principles of the performance efficiency](/azure/architecture/framework/scalability/principles)

### Understand the challenges of distributed IoT architectures

An IoT solution has several specific challenges that will need to be addressed. An IoT project contains both IoT devices, edge software and cloud software parts and, when deployed, may contain millions of devices connected from multiple regions of the world and sending millions of messages per minute.

#### Run performance testing in the scope of development

Be aware of the complexity of having sensors, devices and gateways in geographically different locations with different characteristics, speed and reliability of communication. Plan for this in your testing and make sure to test for failure scenarios like network disconnect etc. In addition, plan for stress/load test of device, edge and cloud components of your full IoT Solution.

#### Continuously monitor the application and the supporting infrastructure

Monitoring an IoT solution with several types of devices in multiple geographical regions requires a distributed monitoring solution that balances the amount of information monitored and sent to the cloud versus the cost in memory and performance of the monitoring (tune the transmission for diagnostics scenarios). Monitoring must occur at multiple levels / layers, exposing metrics etc. on a gateway etc. for industrial or gateway enabled solutions.

#### Invest in capacity planning

An IoT solution can easily start with a few hundred devices or messages and grow to millions of devices and messages per minute. One of the benefits of the cloud is that it can often be scaled to an increase in load. For IoT devices and gateways, the situation is much more complex as these devices are often designed long before the solution is finalized and the need to update the capacity is costly, as devices may need to be replaced. In industrial IoT or similar industries, the lifespan of a device is measured in decades so it's more important than ever in these scenarios to think ahead.

## Design

This section uses the layers described in the [IoT workload overview](iot-overview.md#architecture-layers) to focus on specific parts of an IoT solution and describe the guidance.

:::image type="content" source="media/architecture-layers.svg" alt-text="Diagram that shows the layers and cross-cutting activities in the IoT architecture." border="false":::

### Device and gateway layer

Devices are computing devices that connect to an IoT Solution and have the ability to transmit or receive data. Gateways are devices that serve as the connection point between an IoT Solution and other devices. Design solution accordingly, factor in the load and the limits/quotas.

#### Optimize the hardware specifications of devices and gateways

- Optimize the capabilities of your existing hardware by using more efficient languages and frameworks like C and Rust.

- Upgrading or replacing hardware is costly and takes time, therefore devices that are connected to the IoT Solution should be sized for the required capacity and functionality in advance.

- Use gateways as units of scale. For example, if your solution adds OPC UA servers over time, other edge gateways can be used to ingest data from these other servers.

- When scaling the Device and gateway layer conduct a scale assessment for all upstream layers including edge gateways, cloud gateways, and upstream cloud services.

- Use multiple cloud gateways as scale units for the IoT Solution, see [Tutorial: Provision devices across load-balanced IoT hubs](/azure/iot-dps/tutorial-provision-multiple-hubs). Use DPS to distribute devices over multiple IoT Hub instances.

- Depending on the requirements of the solution, run CPU and I/O-intensive tasks on specific hardware (for example, run ML algorithms on local GPUs) or run their workloads in the cloud (such as blurring faces in a video stream).

- Using the Azure IoT Device SDKs for connecting the cloud gateway because it manages required message translation, error handling, and retry mechanisms needed for a resilient connection.

- Consider using the [Azure IoT Embedded C SDK](/azure/iot-hub/iot-hub-device-embedded-c-sdk) when developing for constrained devices or when most of the security and communication stack is already available on the device. Consider using the [Azure IoT device SDK for C](/azure/iot-hub/iot-hub-device-sdk-c-intro) to include all that is needed to connect to the cloud gateway.

#### Categorize individual workloads

- Consider running workloads on the device/edge to use local compute for low latency and not always connected scenarios. Consider running workloads in the cloud to use scale.

- Consider separating workloads by time constraint and required latency and response times, for example response within seconds versus and batch oriented per hour. Depending on system constraints such as network throughput or latency the solution may demand running your workload at the edge.

- On the edge, use priority queues for sending different streams of data in the order that is required. Although messages will be sent in order of priority to the IoT Hub, this doesn't impact receiving the messages from the IoT Hub as they're still journaled on the IoT Hub based on receipt order.

- In the cloud, use different consumer groups on Event Hubs to separate out different streams of data, for Alarms can be handled and scaled differently from a Telemetry.

- In the cloud, use IoT Hub routes to separate out different streams of data, with filtering and separate endpoints. IoT Hub message routing will add some latency.

#### Optimize connectivity

- Consider using open stateful connections during the operational time of the devices and gateways to minimize the overhead of setting up the connection

- Use an open stateful connection to the IoT Solution for bi-directional communication between the devices and the IoT Solution

- Consider using IoT Hubs with the lowest latency to your devices. This could mean that IoT Hubs in multiple regions are needed when devices need to connect from different geographical locations.

- Use DPS to set up a connection to the cloud gateway, during provisioning, when the connection isn't available anymore or during reboot of the device.

- Use the evenly weighted distribution policy of DPS to adjust the weight for provisioning, refer to [How the allocation policy assigns devices to IoT Hubs](/azure/iot-dps/tutorial-provision-multiple-hubs#how-the-allocation-policy-assigns-devices-to-iot-hubs).

- Consider devices not to connect all at once, for example after a regional power outage. Use truncated exponential backoff with introduced jitter when retrying.

#### Optimize offline scenario

- Consider using Device and Module twins to asynchronously sync state information between devices and the cloud, even when the device isn't currently connected to the cloud gateway. Device and Module twins contain only the current state at a point in time, not any history or removed information.

- Consider that a device has enough information and context to work without the need for a connection to the cloud and store this information locally so that the device can recover from a reboot.

- Ensure a device is capable of storing the data locally, including logs and cached telemetry per priority (when the device isn't connected).

- Consider setting a time-to-live on the data, so that expired data will be removed automatically.

- Consider discarding less important data when the device isn't connected to reduce the local storage needed and reduce the synchronization time when the device reconnects.

- Consider using a cache eviction strategy such as FIFO, LIFO, or priority based for when edge device storage capacities are reached.

- Consider using a separate disk (or disk controller) to store data, so that the device runtime/application can continue to work if running low on storage.

## Ingestion and Communication layer

Data ingestion is the process used to send data from the devices into the IoT Solution. Next to data ingestion there can be other patterns of communication between devices and the IoT Solution. These include:

- Device-to-cloud messages.

- Cloud-to-device messages.

- Initiate file uploads.

- Retrieve and update device twin properties.

- Receive direct method requests.

For more information about IoT Hub endpoints, see [IoT Hub Dev Guide Endpoints](/azure/iot-hub/iot-hub-devguide-endpoints#list-of-built-in-iot-hub-endpoints)

### Limits and Throttling

The cloud IoT gateway has well defined limits per unit dependent on the tier of the IoT Hub. These limits are defined as *Quota* for sustained throughput and sustained send rates for the selected IoT Hub tier. Although these quotas are defined as the sustained throughput and send rate, IoT Hub is capable of handling loads above these quotas for short periods of time, to resiliently handle short bursts or load overshoots. This is where another limit becomes important, which is the *Throttle Limit*, which is an hourly or daily upper limit for the load of the service. The throttle limits protect an IoT Hub from too much load for a longer period of time.

The diagrams below show the relation between the load, the quota and the throttle limits. The left diagram shows that an IoT Hub can handle sustained or constant high load up to the level of the quota for the selected IoT Hub tier. The right diagram shows that an IoT Hub can handle load that is changing over time as long as it isn't hitting the throttle limit and on average not above the quota for the selected IoT Hub tier.

:::image type="content" source="media/load-quota-throttle.png" alt-text="Diagrams showing high load constantly below quota compared to lower load with occasional spikes above quota but below throttle limit." border="false":::

### Optimize interactions between device and cloud, components, and services

The number of messages sent from device to cloud is an important parameter for IoT solution performance efficiency. Also, Azure IoT services such as IoT Hub and IoT Central have a defined limit of message for a selected tier and affect both performance and cost of the solution.

Consider the following recommendations for sending messages:

- IoT Hub and IoT Central calculate daily quota message count [based on a 4-KB](/azure/iot-hub/iot-hub-devguide-quotas-throttling#quotas-and-throttling) max [message size](/azure/iot-hub/iot-hub-devguide-messages-construct#message-size). Sending smaller messages will leave some capacity unused. In general, use messages size close to the boundary of 4 KB.

- Consider message sizing. Group smaller device-to-cloud messages into larger messages to reduce the total number of messages. Consider the introduced latency when combining messages.

- Consider message frequency. Avoid using chatty communication dependent on the use case.

- Use Azure IoT SDK message batching for AMQP to send multiple telemetry messages to the cloud.

- Consider using AMQP connection multiplexing to reduce the dependency on TCP connections limits per SDK client. With AMQP connection multiplexing, a single TCP connection to IoT Hub can be used by multiple devices.

- Use built-in message batching capabilities for AMQP of IoT Edge to send multiple telemetry messages to the cloud.

- Consider using application-level batching by combining multiple smaller messages at the downstream device and send larger messages to the gateway/edge device. This will limit the message overhead and lower the writes to local disk storage at the edge.

- For device-to-device or module-to-module communication at the edge, also avoid designing interactions in which many small messages are used.

- Consider using Direct Methods for request/reply interaction with a device that can succeed or fail immediately (after a user-specified timeout). This approach is useful for scenarios where the course of immediate action is different depending on whether the device was able to respond.

- Consider using Device Twins for device state information including metadata and configurations. Azure IoT Hub maintains a device twin for each device that you connect to IoT Hub.

### Optimize communication and cloud processing of messages

It's common that messages from a device or gateway need to be translated, processed or enriched with additional information before being stored. This could potentially be a time-consuming step, so it's important to evaluate the effect on performance in your solution. Some of the recommendations are also conflicting, such as using compression for optimizing data transfer versus avoiding cloud processing decrypting messages. These recommendations need to be balanced and evaluated against other pillars.

- Optimize the data format used to send data to the cloud. Consider both performance (and cost) of bandwidth used and performance effect of any cloud processing of data that is needed.

- Consider using message enrichment in IoT Hub to add context to device messages.

- For time critical event processing on the ingested data as it arrives, instead of storing unprocessed data and requiring complex queries to acquire data. For time-critical event processing consider late arrival, windowing, and their impacts. This depends on the use case, for example critical alarm handling versus message enrichment.

- [Select the right IoT Hub tier](/azure/iot-hub/iot-hub-scaling) (Basic or Standard) based on solution feature requirements. Be aware of the features that aren't supported in the Basic tier.

- Select the right IoT Hub tier size (1, 2 or 3) and the number of instances based on data throughput, quotas and operation throttles. Select the right IoT Central tier (Standard 0, Standard 1, Standard 2) based on the number of messages that are sent from a device to the cloud.

- Consider using Event Grid for publish-subscribe event routing, see [Azure IoT Hub and Event Grid](/azure/iot-hub/iot-hub-event-grid) and [Compare Event Grid, routing for IoT Hub]/azure/iot-hub/iot-hub-event-grid-routing-comparison).

### Categorize and prioritize your data

Often some of the data sent from devices to the cloud is more important than other data. Classifying the data based on priority and handling the categories in different ways are often a good practice for performance efficiency.

One good example is a thermometer sensor sending temperature, humidity, and other telemetry, but also having a feature of sending an alarm when temperature is outside of a defined range. The alarm message would be classified as more important than the temperature values.

- Consider the following recommendations for data categories:

- Use IoT Edge priority queues to make sure important data is prioritized while sending to IoT Hub. This doesn’t impact the priority on the receiving side of IoT Hub.

- Use IoT Hub message routing to separate routes for different data priorities dependent on the use case. IoT Hub message routing will add some latency.

- Consider saving and sending low priority data at longer intervals, or even using batch/file uploads. Malware detection on uploaded files will increase latency.

- When you use IoT Edge, messages will be buffered when there's no connection, but after the connection is restored, all buffered messages will be sent in order and by priority first followed by new messages.

- Consider separating messages based on timing constraints. For example, send messages to IoT Hub directly when there's a time constraint, and utilize file upload via IoT Hub or batch data transfer (for example, Data Factory) if there's no time constraint. When you use IoT Edge, the blob module can be used for the file upload.

## Transport layer

The transport layer handles connections between a device and the IoT solution, transforming IoT messages to network packages, and sending them over the physical network.

Common protocols used in IoT solutions are:

- AMQP – Advanced Message Queuing Protocol

- MQTT – Message Queue Telemetry Transport

### Optimize resource usage

The connection between a device and the cloud needs to be secure, reliable and scalable to handle the targeted number of devices and messages.

- Use an open stateful connection from a device to the cloud gateway (for example using MQTT or AMQP). IoT Hub is optimized for managing millions of open stateful connections (using MQTT, AMQP or WebSocket protocols). If you keep open connections to the devices, the overhead of security handshakes, authentication and authorization is minimized, which will improve performance and reduce the required bandwidth considerably.

- Consider using a protocol to connect to the cloud gateway (AMQP) that supports multiplexing of multiple channels on a single connection to minimize the number of open connections required by the cloud gateway. By using multiplexing, a transparent gateway can connect multiple leaf devices using their own channel over a single connection.

- Use the cloud gateway patterns of Device and Module twins to asynchronously exchange state information between the device and the cloud.

- Consider configuring DPS to move the device state when a device is connecting to another cloud gateway.

### Optimize data communication

The number and size of messages that are sent from device to cloud have an effect on performance and cost. Evaluating data communication is key to performance efficiency in your IoT Workload.

- Use an efficient data format and encoding used to send data to the cloud, ensure that no extensive device to cloud bandwidth is used.

- Optimize the data format used to send data to the cloud especially for low bandwidth networks. Consider using a compressed or binary format to send data to the cloud using low bandwidth networks. Consider the overhead of uncompressing or converting in the cloud after the cloud gateway.

- Consider storing high volume data sent from device to cloud locally and uploading hourly/daily.

Group many smaller devices to cloud messages into fewer larger messages to reduce the total number of messages. However, don't only send large messages but balance between average message size and throughput.

## Device management and modeling layer

Different types of devices, using different protocols, connectivity, data ingestion frequencies and communication patterns can be connected to an IoT Solution and an IoT Solution can connect to many devices and gateways at the same time. The IoT Solution must be capable of managing which devices and gateways are connected and how they're configured.

Besides the physical connection and configuration of the devices and gateways, the data captured and ingested by the devices and gateways must be understood by the IoT Solution and therefore needs to be transferred and contextualized.

### Device provisioning

- Use DPS to set up a connection to the cloud gateway, during provisioning, when the connection isn't available anymore or during reboot of the device.

- Consider using DPS to allocate devices to IoT Hubs in different regions based on the latency.

- Use the evenly weighted distribution policy of DPS to adjust the weight for provisioning, based on the use cases of the IoT Solution.

- Consider provisioning devices not all at once but over a period of time (distributed or in smaller batches) to the IoT Solution to balance the load and quota of DPS. Account for maximum connections and device registrations, including latency and retries.

- When onboarding in batches, plan for the batches and overall migration timeline.

- Consider using a caching strategy for connection string returned by DPS to reduce Device Provisioning Service operations for reconnects.

- Account for the limits of DPS in number of operations and registrations per minute.

### Downstream devices

- Use multiple gateways devices in Translation mode when the number of downstream devices, their messages and message size will change/increase over time, and their protocol or message must be translated. With the ability to have multiple gateways/IoT Edge devices per site/location and downstream devices that can connect to any of these gateways/IoT Edge devices, the solution will be horizontally scalable. Gateways/IoT Edge devices in Translation mode will be able to translate protocols or messages to and from downstream devices, however a mapping is needed to find the gateway a downstream device is connected to.

- Use multiple gateways/IoT Edge devices in Transparent mode when connecting downstream MQTT/AMQP devices and their number can change/increase over time per site/location. With the ability to have multiple gateways/IoT Edge devices per site/location and downstream devices that can connect to any of these gateways/IoT Edge devices, the solution will be horizontally scalable. Gateways/IoT Edge devices in Transparent mode will be able to connect MQTT/AMQP devices for bidirectional communication.

- Account for additional message translation and buffering overhead at the gateway/IoT Edge device when using Translation mode.

- Account for additional message buffering, storage and configuration overhead at the gateway/IoT Edge device when using Transparent mode.

### Optimize sizing based on the device and message load

- Account for the number of messages the cloud gateway can handle, dependent on the tier and number of units used. Take into account anomalies in sustained throughput due to data distribution, seasonality and bursting.

- Consider using multiple cloud gateways (IoT Hubs) when millions of devices need to be managed using the IoT Solution. Use DPS to get devices assigned to one of the IoT Hubs in the IoT solution.

## Storage layer

An IoT solution often requires multiple storage types residing from devices and gateways and into the cloud. The different types of data collected and referenced in the IoT solution often require different storage types, specialized, and optimized for different scenarios.

An IoT solution that is available in multiple geographical regions might also include complexity based on data that needs to be available globally and/or locally and in some cases replicated for optimizing latency.

### Time series data

- Use a Time Series Database for storing time series data, timestamp, value(s).

- Consider enriching telemetry time series data with columns used for filtering when storing in a Time Series Database, for example CustomerID, RoomID or any use-case specific column.

### Storage on devices and gateways

- Use storage on devices and gateways for caching data.

- Use storage on devices and gateways to make sure data is kept when disconnected. Account for required storage space. Consider keeping not all data locally but use down sampling, store only aggregates, or for a limited period of time.

## Event processing and analytics layer

Data generated by devices can be processed before sending it to the IoT Solution or within the IoT Solution. The data processing can contain translation, contextualization, filtering and routing, or more advanced analytics like trend analysis or anomaly detections.

### Optimize on edge versus cloud processing

- Consider running local workloads on the device/edge using local compute. For example, running a machine learning algorithm to count people in a video stream can run on the edge and an event containing the count is sent to the cloud.

- Consider running large workloads, or workloads that have additional or external data or compute dependencies, in the cloud to use scale. For example, comparing actual trends between different factories.

- Consider running analytics workloads on the edge using the Steam Analytics Edge module. For example, anomaly detection can run on the edge and label the events sent to the cloud with the detected anomaly. When running analytics on the edge, account for extra latency, late arrival, and windowing impact.

- Consider running real-time and near real-time workloads on the device or at the edge.

- Consider running small, optimized, low-latency processing with time constraints on a device.

- Be aware of the overhead of an edge with many connected downstream devices. The edge node has to forward or process all messages and handle caching all the data in case of lost and restored connectivity to the cloud. Validate the performance impact on your solution by testing with the planned maximum of downstream devices and messages per edge node.

- Be aware of the performance impact that message translation or enrichment can have for the edge, IoT Hub or the cloud event processing.

### Categorize individual workloads

- Consider separating workloads by time constraint and required latency and response times, for example response within seconds versus and batch oriented per hour. Hybrid hardware SoCs can support this on device level.

- On the edge use priority queues to separate different streams of data with different priorities and time to live. For example, alarms should always be sent first but have a lower time to live compared to telemetry.

- In the cloud, use different consumer groups on Event Hubs to separate out different streams of data, for Alarms can be handled and scaled differently from a Telemetry.

- In the cloud use IoT Hub routes to separate out different streams of data, with filtering and separate endpoints. IoT Hub message routing will add some latency.

- Use the right messaging construct (Event Hubs, Event Grids or Service Bus) to distribute workloads while protecting against back pressure in the cloud.

- Overly complex IoT Hub routing rules can have an impact on the throughput. This is especially true for routing rules with message body JSON filters where every message needs to be deserialized and scanned.

### Optimize high volume cloud data handling

- Consider using out-of-the-box service integration between IoT Hub and data destinations (like Azure Data Lake Storage and Azure Data Explorer) as these have already been optimized for high performance throughput.

- Consider using the Event Hubs SDK to develop custom ingestion from an IoT Hub and the included event processor. The event processor can rebalance devices and hosts.

- Consider separating the storage needed for data ingestion and event processing from the storage needed for reporting and integration.

- Account for the right number of IoT Hub partitions and consumer groups regarding the number of simultaneous data readers and the required throughput.

- Consider using the data storage that fits the need based on the required throughput, size, retention period, data volume, CRUD requirements, and regional replication (ADLS, ADX, SQL, Cosmos DB; see also [Select an Azure data store for your application](/azure/architecture/guide/technology-choices/data-store-decision-tree)).

## Integration layer

The integration layer is the connection between your IoT solution and the business applications utilizing the IoT data.

### Loose coupling

- Separate the IoT solution/ingestion pipeline from processing/integration.

- Use well defined and versioned APIs for access to IoT data and commands.

- Make sure complex queries or load from integration layer doesn't affect the data ingestion from a performance perspective.

- Consider a separate data store for integration.

### Reporting

- Avoid tools for end users to create user-defined queries against IoT data storage.

- Consider a separate data store for reporting.

## DevOps

<!-- TODO: this section requires some content -->

### Optimize deployments and configuration

- Considered the use of a [connected registry](/azure/container-registry/intro-connected-registry) for local caching and deployment of container images.

- Use the deployment mechanisms provided by IoT Hub to update deployments to multiple devices at once, including devices and gateways.

- Use device twins and module twins to update configurations of devices using IoT Hub in a scalable and efficient way.

### Monitoring

- IoT Hub metrics collected using Azure Monitor with alerts for critical metrics. See [Monitoring Azure IoT Hub data reference](/azure/iot-hub/monitor-iot-hub-reference).

- Azure Service Health service alerts are set to trigger notifications when status of IoT Hub changes.

- Consider setting up Azure Monitor alerts based on current scale limits such as device to cloud messages sent per second. It's also good practice to set the alert to a percentage of the limit, such as 75%, to allow for prenotification of upcoming scalability limits.

- Consider setting up Azure Monitor alerts for metrics and logs such as "Number of throttling errors"

### Development and deployment

Plan for and execute performance testing, including stress and load tests to replicate the production environment, such as location, heterogenous devices, and so on.
