---
title: Cost optimization in your IoT workload
description: See guidance and recommendations that apply to the cost optimization pillar in a well-architected IoT workload.
author: asergaz
ms.author: sergaz
ms.date: 04/27/2023
ms.topic: conceptual
ms.custom:
  - engagement-fy23
categories:
  - iot
---

# Cost optimization in your IoT workload

Cost effectiveness is one of the key success factors for IoT projects. In a typical IoT solution, devices generate large quantities of telemetry that they send to the cloud for cloud technologies to process and store. How you develop devices and applications, handle large volumes of data, and design your architecture affects overall costs.

Because an IoT solution is a multilayered technology stack, there are many cost-saving factors to consider, and many opportunities to optimize costs. Cost optimization is a process of closed-loop cost control that needs to be continuously monitored, analyzed, and improved throughout a solution lifecycle.

Solution requirements are the key criteria for IoT architecture decisions. You can separate requirements into functional and operational requirements. Separate the cost considerations for each type of requirement, because functional requirements determine system design, while operational requirements affect system architecture. Develop multiple use cases based on requirements and compare them before finalizing your design.

This article presents cost considerations for various combinations of Azure IoT services and technologies. For cost optimization for specific industries or use cases like connected factories, predictive maintenance, or remote monitoring, see [Industry specific Azure IoT reference architectures](/azure/architecture/reference-architectures/iot/industry-iot-hub-page).

## Assess cost optimization in your IoT workload

To assess your IoT workload through the lenses of the Well-Architected Framework Cost Optimization pillar, complete the cost optimization questions for IoT workloads in the [Azure Well-Architected Review](/assessments/azure-architecture-review/). After the assessment identifies key cost optimization recommendations for your IoT solution, use the following content to help implement the recommendations.

## Design Principles

[Five pillars of architectural excellence](../index.md) underpin the IoT workload design methodology. These pillars serve as a compass for subsequent design decisions across the [key IoT design areas](iot-overview.md#iot-design-areas). The following design principles extend the quality pillar of the Azure Well-Architected Framework - [Cost Optimization](../cost-optimization/overview.md).

|Design principle|Considerations|
|---|---|
|[**Set up budgets and maintain cost constraints**](../cost-optimization/principles.md#set-up-budgets-and-maintain-cost-constraints)|[Understand total cost of ownership (TCO)](#total-cost-of-ownership-tco) by accounting for both direct and indirect costs when planning.|
|**Use industry-standard strategies and approaches**|For [IoT specific industries](/azure/architecture/reference-architectures/iot/industry-iot-hub-page) with their own ecosystems, for example manufacturing, energy and environment, or automotive and transportation, use industry-standard strategies and approaches.|
|[**Choose the correct resources**](../cost-optimization/principles.md#choose-the-correct-resources)|Define implementation plans for each [IoT architecture layer](#iot-architecture-layers).|
|[**Continuously monitor and optimize cost management**](../cost-optimization/principles.md#continuously-monitor-and-optimize-cost-management)|Monitor and optimize costs with ongoing cost optimization activities after you implement your solution.|

## Total cost of ownership (TCO)

IoT costs are a tradeoff between various technology options. Sometimes it's not a simple comparison, because IoT is an end-to-end system. Consider the cost benefits of synergy when reconciling multiple services and technologies. For example, you can use Azure IoT Hub device twins to handle events in Azure Digital Twins. Device Twins in IoT Hub are available only in the [standard tier of IoT Hub](/azure/iot-hub/iot-hub-scaling#basic-and-standard-tiers).

It's important to correctly estimate long-term aggregated costs. Review the IoT technology stacks, and [develop a cost model](../cost-optimization/design-model.md) that includes the costs to implement and operate all services involved. The [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) helps estimate both startup and operational costs.

In some areas, a one-time cost can be more effective than recurring costs. For example, in security where hacking techniques are always changing, it can be best to import a reliable commercial operating system and module such as Azure Sphere. For a one-time payment, such services provide ongoing monthly device security patches.

Estimate solution costs based on running at scale in production, not proof-of-concept (PoC) architecture. Architecture and costs evolve rapidly after the PoC. According to the [IoT Signals EDITION 3 report](https://azure.microsoft.com/mediahandler/files/resourcefiles/iot-signals-report/IoT%20Signals_Edition%203_Thought%20Paper_EN.pdf), the top reason for PoC failure is the high cost of scaling. The high cost of scaling IoT projects comes from the complexities of integrating across layers, such as devices, edge connectivity, and compatibility across applications.

Your cost model should include the following areas:

- Devices: Starting with a limited number of connected devices, estimate growth in the number of devices deployed and their messaging patterns. Both devices and messages can have linear or non-linear growth over time.

- Infrastructure: To evaluate infrastructure costs, account for the basics first: storage, compute, and network. Then account for all services your solution needs to ingest, egress, and prepare data.

- Operations: Include long-term operational costs that increase in parallel with infrastructure costs, such as employing operators, vendors, and customer support teams.

- Monitoring: Continuously monitor and review costs to identify gaps between planned and actual costs. A regular cost review meeting helps achieve cost optimization.

## IoT architecture layers

Cost Optimization design principles help clarify considerations to ensure your IoT workload meets requirements across the [foundational IoT architecture layers](iot-overview.md#iot-architecture-layers).

Understanding the IoT architecture layers helps you define a cost baseline and consider multiple architectures for cost comparison. Each layer has multiple technologies and ecosystems options, such as devices, telecommunications, or the edge location, so you need to establish a cost strategy for each layer.

The IoT core layers: device and gateway, device management and modeling, and ingestion and communication, identify IoT-specific solutions. The other layers and cross-cutting activities are also common to, and often shared with, other workloads. However, TCO and cost optimization must take all costs into account, so you need to consider the IoT-related costs of common and cross-cutting activities as well as the IoT-specific layers.

:::image type="content" source="media/architecture-layers.svg" alt-text="Diagram that shows the layers and cross-cutting activities in the IoT architecture." border="false":::

## Device and gateway layer

This layer is responsible for generating, in some cases optimizing, and transferring data to the cloud. Cost is a key consideration for designing this layer. Cost optimization should account for the entire device lifecycle of plan, provision, configure, monitor, and retire.

:::image type="content" source="media/device-provisioning.png" alt-text="Diagram that shows the device lifecycle." border="false":::

Edge solutions require IoT devices to be deployed in the field. The deployment might need networking and power supply infrastructure that affects costs. Pre-existing infrastructure can minimize installation costs, but might require ensuring that the installation doesn't affect existing systems.

Developing or installing IoT devices might require training and employing dedicated internal or external personnel. Required skills include hardware design, embedded application development, cloud and local connectivity, security and privacy, and IoT solution architecture. Industry-specific expertise might also be required. Include these costs in overall device costs.

Device costs include organizing logistics, such as storage, inventory management, and transport. Include the cost of any decommissioning activities when devices reach the end of their operational lifecycle.

For devices connected to the cloud, optimize data transmissions to maintain cost boundaries. Strategies include minimizing payload sizes, batching messages, and transmitting during off-peak periods. These optimizations also incur costs to implement.

To learn more about Azure IoT devices, see:
 
- [Overview of Azure IoT device types](/azure/iot-develop/concepts-iot-device-types)
- [Best practices for device configuration within an IoT solution](/azure/iot-hub/iot-hub-configuration-best-practices)

### Hardware selection

Most of the device development process depends on hardware selection. A make-or-buy decision for devices takes into account qualitative factors like WiFi certification and quantitative factors like bill of materials cost and time to market. Choosing between off-the-shelf hardware or a custom design affects IoT device cost and time to market.

- Off-the-shelf devices might cost more per unit, but have predictable costs and lead times. Off-the-shelf devices also remove the need for complex supply chain management.

- Custom devices can reduce unit costs, but involve development time, and incur non-recurring engineering costs such as design, test, certification submissions, and manufacture.

- Pre-certified system components or modules can reduce time to market and create a semi-custom device, but are more expensive than discrete chips. You need to properly resource supply-chain and inventory management.

The [Azure Certified Device catalog](https://devicecatalog.azure.com) offers devices that work well with Azure IoT and can help reduce costs and time to market. You'll focus on designing and architecting the IoT solution with the flexibility to select the hardware from an extensive list of certified devices. [IoT Plug and Play devices](https://devicecatalog.azure.com/devices?certificationBadgeTypes=PnP) can reduce both device and cloud development costs. When you select an Azure Certified Device, you can skip device customizations and integration straight to onboarding into your IoT Solution.

:::image type="content" source="media/plug-and-play.png" alt-text="Graphic that shows savings from Plug and Play approach.":::

### Lambda architectural pattern

IoT solutions commonly use the hot/warm/cold lambda architectural pattern in the cloud. This pattern also applies to the edge when you use more performant edge devices or the Azure IoT Edge runtime. Optimizing this pattern on the edge reduces overall solution costs. You can choose the most cost-effective service for cloud data ingestion and processing.

- Hot path processing includes near real-time processing, process alerts, or edge notifications. You can use Azure IoT Hub event streams to process alerts in the cloud.

- Warm path processing includes using storage solutions on the edge, such as open-source time-series databases or [Azure SQL Edge](/azure/azure-sql-edge/overview). Azure SQL Edge includes edge stream processing features and time-series optimized storage.

- Cold path processing includes batching lower importance events and using a file transfer option through the Azure Blob Storage module. This approach uses a lower cost data transfer mechanism compared to streaming through IoT Hub. After cold data arrives in Azure Blob storage, there are many options to process the data in the cloud.

### Device security

Both IoT Hub with Device Provisioning Service (DPS) and IoT Central support device authentication with symmetric keys, trusted platform module (TPM) attestation, and X.509 certificates. There's a cost factor associated with each option.

- X.509 certificates are the most secure option for authenticating to Azure IoT Hub, but certificate management can be costly. Lack of certificate lifecycle management planning can make certificates even costlier. Typically, you work with third-party vendors who offer CA and certificate management solutions. This option requires using a public key infrastructure (PKI). Options include a self-managed PKI, a third-party PKI, or the Azure Sphere security service, which is available only with Azure Sphere devices.

- TPMs with X.509 certificates offer an added layer of security. DPS also supports authentication through TPM endorsement keys. The main costs are from hardware, potential board redesign, and complexity.

- Symmetric key authentication is the simplest and lowest cost option, but you must evaluate the impact on security. You need to protect keys on the device and in the cloud, and securely storing the key on the device often requires a more secure option.

Review costs associated with each of these options, and balance potentially higher hardware or services costs with increased security. Integration with your manufacturing process can also influence overall costs.

For more information, see [Security practices for Azure IoT device manufacturers](/azure/iot-dps/concepts-device-oem-security-practices).

### Azure RTOS

[Azure RTOS](/azure/rtos) is an embedded development suite for devices. Azure RTOS includes a small but powerful operating system that provides reliable, ultra-fast performance for resource-constrained devices. Azure RTOS is easy to use and has been deployed on more than 10 billion devices. Azure RTOS supports the most popular 32-bit microcontrollers and embedded development tools, so you can make the most of existing device builder skills.

Azure RTOS is free for commercial deployment using [pre-licensed hardware](https://github.com/azure-rtos/threadx/blob/master/LICENSED-HARDWARE.txt). Azure RTOS comes with Azure IoT cloud capabilities and features such as device update and security. These features help reduce both device and cloud development costs.

Azure RTOS is certified for safety and security, helping to reduce the time and cost of building compliant devices for specific verticals such as medical, automotive, and manufacturing.

### LPWAN devices

If LPWAN devices, such as LoRaWAN, NB-IoT, or LTE-M, are already connected to another IoT cloud, the [Azure IoT Central Device Bridge](/azure/iot-central/core/howto-build-iotc-device-bridge) can help bridge to Azure IoT Central. Azure IoT Central Device Bridge lets you focus on adding industry knowledge, and evaluating the solution without incurring costs to change existing devices.

When building your enterprise ready solution, you'll need to consider the costs to integrate LPWAN devices with Azure IoT Hub.

### Azure Sphere

[Azure Sphere](/azure-sphere/product-overview/what-is-azure-sphere) is a secure, end-to-end IoT solution platform with built-in communication and security features for internet-connected devices. Azure Sphere comprises a secured, connected, crossover microcontroller unit (MCU), a custom high-level Linux-based operating system (OS), and a cloud-based security service that provides continuous, renewable security. Azure Sphere reduces the effort to build and maintain a secure environment from device to the cloud.

Azure Sphere provides OS updates and zero-day renewable security for 10 years on top of X.509 based PKI, user app updates, error reporting, and device management beyond 10 years without extra cost. Azure Sphere reduces the operational cost of keeping millions of devices up to date with the latest security.

### Azure Stack

[Azure Stack solutions](https://azure.microsoft.com/products/azure-stack) extend Azure services and capabilities to environments beyond Azure datacenters, such as on-premises datacenters or edge locations. Azure Stack solutions include Azure Stack Edge and Azure Stack HCI.

- [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge) is an Azure-managed appliance that's ideal for hardware-accelerated machine learning workloads at edge locations. Azure Stack Edge runs on modern technology stacks such as containers, so Azure Stack Edge deployed in an edge location can serve multiple workloads. Sharing computational power among workloads reduces TCO.

- [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci) is a purpose-built, hyperconverged solution with native Azure integration. Azure Stack HCI offers scalable virtualization to host IoT solutions. Virtualization brings extra benefits such as security, scalability, and flexible environments, which can reduce TCO by sharing the hardware with other workloads. Azure Stack HCI offers more compute power than Azure Stack Edge and is ideal for industry process transformation.

Azure Stack solutions bring Azure capability to the edge, but hardware sizing constrains the total compute power. Identify use cases and estimated compute power, and factor in sizing to match costs to performance needs.

### Azure public or private MEC

IoT devices can generate large quantities of data, and might also have strong requirements for low power consumption and low costs. Small, inexpensive IoT devices are designed for one or a few tasks, such as collecting sensor or location data and offloading it for further processing.

Azure [public](/azure/public-multi-access-edge-compute-mec/overview) or [private](/azure/private-multi-access-edge-compute-mec/overview) multi-access edge compute (MEC) and 5G help optimize the costs of offloading data from devices. MEC-based IoT solutions enable low-latency data processing at the edge instead of on devices or in the cloud. Latency is 1-5 ms instead of the typical 100-150 ms for the cloud. MEC-based IoT solutions are flexible, and the devices themselves are inexpensive, operate with minimal maintenance, and use smaller, cheaper, and long-lasting batteries. MEC keeps data analytics, AI, and optimization functions at the edge, which keeps IoT solutions simple and inexpensive.

In addition to serving as an edge processing, compute, and 5G communication device for IoT workloads, MEC serves other workloads as a communication device to establish high-speed connections to the public cloud or remote sites.

### Azure IoT Edge

Azure IoT Edge has built-in capabilities for high message volumes. [Azure IoT Edge managed devices](https://devicecatalog.azure.com/devices?certificationBadgeTypes=IoTEdgeCompatible&deviceClass=Gateway) with gateway capabilities can reduce network costs and minimize the number of messages through local processing and edge scenarios.

Avoid device-to-device or module-to-module edge communications or device-to-cloud interactions that use many small messages. Use built-in message batching features to send multiple telemetry messages to the cloud. These features can help reduce the costs of using IoT Hub. Reducing both the number of daily messages and the number of device-to-cloud operations per second can allow choosing a lower tier in IoT Hub. To learn more, see [Stretching the IoT Edge performance limits](https://techcommunity.microsoft.com/t5/internet-of-things-blog/stretching-the-iot-edge-performance-limits/ba-p/2993856).

To reduce data exchange costs, you can deploy Azure services such as [Azure Stream Analytics](/azure/iot-edge/tutorial-deploy-stream-analytics) and [Azure Functions](/azure/iot-edge/tutorial-deploy-function) to IoT Edge. Azure Stream Analytics and Azure Functions can aggregate and filter large volumes of data at the edge and send only important data to the cloud. [Azure Blob Storage on IoT Edge](/azure/iot-edge/how-to-store-data-blob) can reduce the need to transfer large quantities of data over the network. Edge storage is useful for transforming and optimizing large quantities of data before sending it to the cloud.

Free Azure IoT Edge modules for open protocols such as [OPC Publisher](/azure/industrial-iot/overview-what-is-opc-publisher) and [Modbus](/azure/iot-edge/deploy-modbus-gateway) help connect various devices with minimal development. If upload performance is critical, choosing a proven IoT Edge module from a vendor can be more cost effective than building a custom module. You can search for and download IoT Edge modules from the [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?subcategories=iot-edge-modules).

## Ingestion and communication layer

A cloud IoT gateway is a bridge between devices and cloud services. As a front-end service to the cloud platform, a gateway can aggregate all data with protocol translation and provide bi-directional communication with devices.

There are many factors to take into account for device to IoT gateway communications, such as device connectivity, network, and protocol. An understanding of IoT communication protocols, network types, and messaging patterns helps you design and optimize a cost effective architecture.

For device connectivity, it's important to specify the network type. If you select a private LAN or WAN solution, such as WiFi or LoraWAN, consider network TCO as part of overall costs. If you use carrier networks such as 4G, 5G, or LPWAN, include recurring connectivity costs.

### IoT solution platform

To build an IoT solution for your business, you typically evaluate your solution by using the managed app platform approach and build your enterprise ready solution by using the platform services.

- Platform services let you fine-tune services and control overall costs. It provides all the building blocks for customized and flexible IoT applications. You have more options to choose and code when you connect devices, and ingest, store, and analyze your data. Azure IoT platform services include the products [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) and [Azure Digital Twins](https://azure.microsoft.com/services/digital-twins/).

- [Azure IoT Central](https://azure.microsoft.com/services/iot-central/) is a managed app platform that lets you quickly evaluate your IoT solution by reducing the number of decisions needed to achieve results. IoT Central takes care of most infrastructure elements in your solution, so you can focus on adding industry knowledge, and evaluating the solution.

### IoT Hub tiers

Most IoT solutions require bi-directional communication between devices and the cloud to be fully functional and secure. The basic IoT Hub tier provides core functionality, but excludes bi-directional control. For some early solution implementations, you might be able to reduce costs by using the basic tier. As your solution progresses, you can switch to a standard tier to optimize a secure communication channel for lower cloud-to-device messaging costs. For more information, see [Choose the right IoT Hub tier for your solution](/azure/iot-hub/iot-hub-scaling).

### IoT Hub message size and frequency

Messaging costs depend greatly on device *chattiness* and message size. Chatty devices send many messages to the cloud every minute, while relatively quiet devices only send data every hour or more. Avoid device-to-cloud interactions that use many small messages. Clarity about device chattiness and message size helps reduce the likelihood of over-provisioning, which leads to unused cloud capacity, or under-provisioning, which leads to scale challenges. Consider the size and frequency of message payloads to ensure your infrastructure is the correct size and ready to scale.

Avoid cloud-to-device interactions that use many small messages. For example, group multiple device or module twin updates into a single update, which have their own throttling. Be aware of the message size used for the daily quota, 4K-byte for non-free IoT Hub tiers. Sending smaller messages leaves some capacity unused, while larger messages are charged in 4-KB chunks.

Use a single direct method to get direct feedback. Use a single device or module twin status update to exchange configuration and status information asynchronously.

> [!TIP]
> You can monitor chatty interactions by using [Microsoft Defender for IoT on Azure IoT Hub](/azure/defender-for-iot/device-builders/quickstart-onboard-iot-hub) and the [Defender for IoT micro agent](/azure/defender-for-iot/device-builders/tutorial-standalone-agent-binary-installation). You can create IoT Hub [custom alerts](/azure/defender-for-iot/device-builders/concept-customizable-security-alerts) for device-to-cloud or cloud-to-device interactions that exceed a certain threshold.

If message size is critical to cost management, reducing overhead is especially important with long device lifecycles or large deployments. Options to reduce this overhead include:

- Use a shorter device ID, module ID, twin name, and message topic to reduce the payload in MQTT packets. An MQTT payload looks like `devices/{device_id}/modules/{module_id}/messages/events/`.
- Abbreviate the fixed length overhead and the message.
- Compress the payload, for example by using Gzip.

### IoT Hub message quotas and throttling limits

IoT Hub tiers have different sizes with specific quotas and throttling limits for operations. Understand IoT Hub limits and quotas to optimize costs for device-to-cloud and cloud-to-device messaging.

For example, the Standard S1 tier has a daily quota of 400,000 messages. Charges increase in 4-KB chunks based on several factors:

- One device-to-cloud (D2C) message can be up to 4-KB.
- D2C messages that exceed 4-KB are charged in chunks of 4-KB.
- Messages smaller than 4-KB can use the Azure IoT SDK `SendEventBatchAsync` method to optimize batching on the device side. For example, bundling up to four 1-KB messages at the edge increases the daily meter by just one message. Batching is only applicable for [AMQP or HTTPS](/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync).
- Most operations, such as cloud-to-device messages or device twin operations, also charge messages in 4-KB chunks. All these operations add to the daily throughput and maximum quota of messages.

Review the [Azure IoT Hub pricing information documentation](/azure/iot-hub/iot-hub-devguide-pricing) for detailed pricing examples.

Besides daily message quotas, service operations have throttling limits. A key part of IoT Hub cost optimization is to optimize both message quotas and operations throttling limits. Study the differences between the limits in the form of operations per second or bytes per second. For more information, see [IoT Hub quotas and throttling](/azure/iot-hub/iot-hub-devguide-quotas-throttling).

Different throttling limits apply to different IoT Hub operations. Device-to-cloud operations have an operations per second throttle that depends on the tier. In addition to the message size, which is metered in 4-KB chunks, consider the number of operations. Batching on the edge lets you send more messages in a single operation.

A single message of 2-KB, a batched message of 10-KB, or a batched message of 256-KB only counts as a single operation, letting you send more data to the endpoint without reaching throttling limits.

### IoT Hub autoscaling

Dynamically adjusting the number of IoT Hub units helps optimize costs when message volume fluctuates. You can implement an autoscale service that automatically monitors and scales your IoT Hub service. See [Auto-scale your Azure IoT Hub](/samples/azure-samples/iot-hub-dotnet-autoscale/iot-hub-dotnet-autoscale/) for a customizable sample to implement autoscale capability. You can use your own custom logic to optimize IoT Hub tier and number of units.

### Deployment stamps for scaling

*Deployment stamping* is a common design pattern for flexible deployment strategies, predictable scale, and cost. This pattern provides several advantages for IoT solutions, such as geo-distributing groups of devices, deploying new features to specific stamps, and observing cost per device. For more information, see [Scale IoT solutions with deployment stamps](/azure/architecture/example-scenario/iot/application-stamps).

## Device management and modeling layer

Managing devices is a task that orchestrates complex processes such as supply chain management, device inventory, deployment, installation, operational readiness, device update, bi-directional communication, and provisioning. Device modeling can reduce management costs and messaging traffic volumes.

### IoT Plug and Play

For TCO reduction, consider extended use cases as part of platform selection. [IoT Plug and Play](/azure/iot-develop/overview-iot-plug-and-play) enables solution builders to integrate devices with IoT Hub or Azure Digital Twins without any manual configuration. IoT Plug and Play uses the [Digital Twins Definition Language (DTDL) V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Both are based on open W3C standards such as JSON-LD and RDF, which enables easier adoption across services and tooling.

There's no extra cost for using IoT Plug and Play and the DTDL. Standard rates for IoT Hub, Azure Digital Twins and other Azure services remain the same.

For more information, see [How to convert an existing device to be an IoT Plug and Play device](/azure/iot-develop/howto-convert-to-pnp).

### IoT Hub DPS

[IoT Hub DPS](/azure/iot-dps/about-iot-dps) is a helper service for IoT Hub that enables low-cost, zero-touch, just-in-time provisioning to the correct IoT hub without requiring human intervention. DPS enables secure and scalable provisioning of millions of devices to reduce error and cost.

DPS enables low or no-touch device provisioning, so you don't have to train and send people on site. Using DPS reduces the cost for truck rolls and time spent on training and configuration. DPS also reduces the risk of errors due to manual provisioning.

DPS supports device lifecycle management with IoT Hub through enrollment allocation policies, zero-touch provisioning, initial configuration setting, reprovisioning, and de-provisioning. For more information, see:

- [DPS pricing](https://azure.microsoft.com/pricing/details/iot-hub/#pricing)
- [How to reprovision devices](/azure/iot-dps/how-to-reprovision)

### Asset and device state modeling

Compare cost differences between several device topology and entity stores such as Azure Cosmos DB, Azure Digital Twins, and Azure SQL Database. Each service has a different cost structure and delivers different capabilities to your IoT solution. Depending on required usage, choose the most cost-efficient service.

- [Azure Digital Twins](https://azure.microsoft.com/services/digital-twins) can implement a graph-based model of the IoT environment for asset management, device status, and telemetry data. You can use Azure Digital Twins as a tool to model entire environments, with real-time IoT data streaming, and merge business data from non-IoT sources. You can build custom ontologies, or use standards based ontologies such as RealEstateCore, CIM, or NGSI-LD to simplify data exchange with third parties. Azure Digital Twins has a [pay-per-use pricing model](https://azure.microsoft.com/pricing/details/digital-twins) with no fixed fee.

- [Azure Cosmos DB](/azure/cosmos-db/plan-manage-costs) is a globally distributed, multi-model database. Cost is affected by storage and throughput, with regional or globally distributed and replicated data options.

- [Azure SQL Database](/azure/azure-sql/database/cost-management) can be an efficient solution for device and asset modeling. SQL Database has several pricing models to help you optimize costs.

### Asset deployment model

You can deploy edge solutions with different architectures: multiple endpoints, IoT devices, direct-connected to the cloud, or connected through an edge and/or cloud gateway. Different options for sourcing edge devices can affect TCO and time to market. Ongoing maintenance and support of the device fleet also affects the overall solution cost.

Where data is stored and processed in a given IoT solution affects many factors such as latency, security, and cost. Analyze each use case and examine where it makes most sense to use edge processing and data storage, and how it affects costs. Storing and processing data at the edge can save storage, transportation, and processing costs. But when you take scale into account, cloud services are often better options because of cost and development overhead.

The [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) is a useful tool to compare these options.

## Event processing and analytics layer

The purpose of the event processing and analytics layer is to enable data-driven decisions. Event timing and the purpose of analytics are key factors to consider. The right service choice increases architectural efficiency and reduces the cost of processing data and events.

Based on your requirements, implement hot, warm, or cold path processing for IoT data analytics. The [Azure IoT reference architecture](/azure/architecture/reference-architectures/iot) helps you understand the difference between these analytics paths and reviews the available analytics services on each path. The Cost Optimization pillar in the Azure Well-Architected Framework includes the [cost considerations for data analytics stores](../cost-optimization/provision-datastores.md#data-analytics-cost), considering data storage, multiple servers to maximize scalability, and the ability to access large data as external tables.

To get started, determine which types of data go through the hot, warm, or cold path:

- Hot path data is kept in memory and analyzed in near real-time, typically using stream processing. The output may trigger an alert or be written to a structured format that analytics tools can query immediately.
- Warm path data, such as from the last day, week, or month, is kept in a storage service that can be queried immediately.
- Cold path historical data is kept in lower-cost storage to be queried in large batches.

:::image type="content" source="media/hot-warm-cold-paths.png" alt-text="Diagram that shows the hot, warm, and cold analytics paths." border="false":::

## Storage layer

One of the goals of an IoT solution is to provide data to end users. It's important to understand storage types, capacity, and pricing to create a strategy for optimizing storage costs.

### Storage types

The choice of a repository for telemetry depends on the use case for your IoT data. If the purpose is just to monitor IoT data, and volumes are low, you can use a database. If your scenario includes data analysis, you should save telemetry data to storage. For time series optimized, append-only storage and querying, consider purpose-designed solutions such as [Azure Data Explorer](/azure/data-explorer/time-series-analysis).

Storage and databases aren't mutually exclusive. Both services can work together, especially with well-defined hot, warm, and cold analytics paths. Azure Data Explorer and databases are commonly used for hot and warm path scenarios.

For Azure Storage, it's also important to consider data lifecycle factors like access frequency, retention requirements, and backups. Azure Storage helps you define the data lifecycle and automate the process of moving data from the hot tier to other tiers, which reduces long-term storage costs. For more information, see [Configure a lifecycle management policy](/azure/storage/blobs/lifecycle-management-policy-configure).

### Database solutions

For database capabilities, it's common to choose between SQL and no-SQL solutions. SQL databases are best suited for fixed schema telemetry with simple data transformation or data aggregation requirements. To learn more, see [Types of databases on Azure](https://azure.microsoft.com/product-categories/databases).

Azure SQL Database and TimescaleDB for PostgreSQL are common choices for SQL database. For more information, see the following articles:

- [Plan and manage costs for Azure SQL Database](/azure/azure-sql/database/cost-management)
- [Azure SQL Database and cost optimization](/azure/well-architected/services/data/azure-sql-database-well-architected-framework#azure-sql-database-and-cost-optimization)
- [Azure SQL Database for PostgreSQL Extension](/azure/postgresql/concepts-extensions)
- [Performance tuning for Azure SQL Databases](/azure/azure-sql/database/monitor-tune-overview)

If the data is best represented as an object or document without a fixed schema, no-SQL is a better option. Azure Cosmos DB provides multiple APIs such as SQL or MongoDB. For any database, partition and index strategies are important for performance optimization and reducing unnecessary costs. For more information, see:

- [Partitioning and horizontal scaling in Azure Cosmos DB](/azure/cosmos-db/partitioning-overview)
- [Plan and manage costs for Azure Cosmos DB](/azure/cosmos-db/plan-manage-costs)

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) is a modern Azure data warehouse. Synapse Analytics scales by [Data Warehouse Units (DWU)](/azure/synapse-analytics/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu), and you should choose the right capacity to handle your solution requirements. Depending on use case, you can pause compute when no job is running to reduce operational costs.

## Transport layer

The transport layer transfers and routes data between other layers. As data travels between layers and services, the choice of protocol affects costs. Use cases such as field gateways, industry open protocol, and IoT network selection also affect costs in the transport layer. 

To reduce transmission sizes and costs, [choose the right protocol](/azure/iot-hub/iot-hub-devguide-protocols) for your IoT devices to send telemetry.

Device clients regularly send *keep-alive* messages to IoT Hub. According to [Charges per operation](/azure/iot-hub/iot-hub-devguide-pricing#charges-per-operation), there's no charge for keep-alive messages. But you don't need to add a keep-alive property in the telemetry if there's no specific requirement for it. For flexibility, some [Azure IoT Device SDKs](/azure/iot-hub/iot-hub-mqtt-support#default-keep-alive-timeout) provide the option to set a timespan for these messages if you're using the AMQP or MQTT protocols.

For battery-powered IoT devices, you can choose between keeping connections alive or reconnecting when the devices wake up. This choice affects power consumption and network costs.

Reconnecting consumes packets around 6-KB for TLS connection, device authentication, and retrieving a device twin, but saves battery capacity if the device wakes up only once or twice per day. You can bundle messages together to decrease TLS overhead. Keeping alive consumes hundreds of bytes, but keeping the connection alive saves network costs if the device wakes up every few hours or less.

For high-level guidance about the connectivity and reliable messaging features in Azure IoT device SDKs, see [Manage connectivity and reliable messaging by using Azure IoT Hub device SDKs](/azure/iot-hub/iot-hub-reliability-features-in-sdks). This guidance helps you reduce the costs of handling unexpected behavior between device and Azure IoT services.

DPS reduces device lifecycle management costs from zero-touch provisioning to retirement, but connecting to DPS consumes network cost for TLS and authentication. To reduce network traffic, devices should cache IoT Hub information during provisioning, and then connect to IoT Hub directly until they need to reprovision. For more information, see [Send a provisioning request from the device](/azure/iot-dps/how-to-reprovision#send-a-provisioning-request-from-the-device).

## Interaction and reporting layer

As IoT handles time-series data, there are many interactions from a large number of devices. Reporting and visualizing realizes the value of this data. Intuitive and simplified user experiences and well-designed data interactions can be costly to build.

[Grafana](https://grafana.com/) is an open-source data visualization tool that provides optimized dashboards for time-series data. Grafana communities provide examples that you can reuse and customize in your environment. You can implement metrics and dashboard from time-series data with little effort. Azure provides a [Grafana plug-in for Azure Monitor](/azure/azure-monitor/visualize/grafana-plugin).

Reporting and dashboard tools like Power BI allow a quick start from unstructured IoT data. Power BI provides an intuitive user interface and capabilities. You can easily develop dashboards and reports using time-series data, and get the benefits of security and deployment at low cost.

## Integration layer

Integration with other systems and services can be complex. Many services can help maximize efficiency to optimize costs in the integration layer.

Azure Digital Twins can integrate various systems and services with IoT data. Azure Digital Twins transforms all data into its own digital entity, so it's important to understand its service limits and tuning points for cost reductions. Review [Azure Digital Twins service limits](/azure/digital-twins/reference-service-limits) when designing your architecture. Understand functional limitations to help integrate effectively with business systems.

When you use the query API, Azure Digital Twins charges per [Query Unit (QU)](/azure/digital-twins/concepts-query-units). You can trace the number of QUs the query consumed in [the response header](/rest/api/digital-twins/dataplane/query/querytwins#response). Reduce query complexity and the number of results to optimize costs. For more information, see [Find the QU consumption in Azure Digital Twins](/azure/digital-twins/concepts-query-units#find-the-query-unit-consumption-in-azure-digital-twins).

## DevOps layer

Cloud platforms transform capital expenditure (CAPEX) to operational expenditure (OPEX). While this model provides flexibility and agility, you still need a well-defined deployment and operational model to take full advantage of the cloud platform. A well-planned deployment creates repeatable assets to shorten time to market.

A cloud platform provides agility for developers to deploy resources in seconds, but there's a risk of provisioning resources unintentionally, or over-provisioning. A proper cloud governance model can minimize such risks and help avoid unwanted costs.

### Development environments

Developers can take advantage of the flexibility that Azure provides to optimize development cost. The IoT Hub free tier, limited to one instance per subscription, offers standard capabilities but is limited to 8000 messages a day. This tier is sufficient for early-stage development with a limited number of devices and messages.

For compute environments, you can adopt serverless architecture for cloud-native IoT solutions. Some popular Azure services for IoT workloads include Azure Functions and Azure Stream Analytics. The billing mechanism depends on the service. Some services, like Azure Stream Analytics for real-time processing, let developers pause services without incurring extra costs. Other services bill by usage. For example, Azure Functions bills based on number of transactions. Developers can take advantage of these cloud-native capabilities to optimize both development and operational cost.

An integrated development environment (IDE) accelerates development and deployment. Some open-source IDEs like Visual Studio Code provide [Azure IoT extensions](https://github.com/microsoft/vscode-azure-iot-tools) that let developers develop and deploy code to Azure IoT services at no cost.

Azure IoT provides free [GitHub code samples](/samples/browse/?expanded=azure&terms=iot) with guidance. These samples help developers extend device, IoT Edge, IoT Hub, and Azure Digital Twins applications. GitHub also has features to implement seamless continuous integration and continuous deployment (CI/CD) environments with low cost and effort. GitHub Actions are free for open-source projects. For more information, see [GitHub plans and features](https://github.com/pricing#compare-features).

### Load testing for cost estimation

You can use load testing to estimate overall costs, including cloud services, for end-to-end IoT solutions. Because IoT solutions use large amounts of data, a simulator can help with load testing. Simulation code samples like the [Azure IoT Device Telemetry Simulator](/samples/azure-samples/iot-telemetry-simulator/azure-iot-device-telemetry-simulator) help you test and estimate costs at scale with various parameters.

### Deployment environments

It's common to deploy workloads in multiple environments, such as development and production. Through infrastructure-as-code (IaC), you can accelerate deployment and reduce time to market by reusing code. IaC can help avoid unintentional deployments such as incorrect tiers. Azure services like Azure Resource Manager and Azure Bicep, or third-party services such as Terraform and Pulumi, are common IaC options.

You can apply DevOps deployment practices to IoT solutions by using build and release pipelines to different environments. For an example, see [Use a DevOps pipeline to deploy a predictive maintenance solution](/azure/architecture/example-scenario/predictive-maintenance/iot-predictive-maintenance).

### Support and maintenance

Long-term support and maintenance of field devices can escalate to become the largest cost burden for a deployed solution. Careful consideration of system TCO is crucial to realizing Return on Investment (ROI).

You need to support and maintain IoT devices for the lifetime of the solution. Tasks include hardware repairs, software upgrades, OS maintenance, and security patching. Consider ongoing licensing costs for commercial software and proprietary drivers and protocols. If you can't do remote maintenance, you need to budget for onsite repairs and updates. For hardware repairs or replacements, you must keep suitable spares in stock.

For solutions that use cellular or paid connectivity media, select a suitable data plan based on the number of devices, the size and frequency of data transmissions, and device deployment location. If you have a service level agreement (SLA), you need a cost-effective combination of hardware, infrastructure, and trained staff to meet the SLA.

### Cloud governance

Cloud governance is essential for compliance, security, and preventing unnecessary costs.

- Cost management APIs let you explore cost and usage data through multidimensional analysis. You can create customized filters and expressions that help answer Azure resource consumption-related questions. Cost management APIs can generate alerts when consumption reaches configured thresholds. Cost management APIs are available for [IoT Central](/rest/api/iotcentral), [IoT Hub](/rest/api/iothub), and [DPS](/rest/api/iot-dps).

- Resource tagging applies labels to deployed resources. Along with Azure Cost Management, tagging provides insights on ongoing costs based on the labels. For more information, see [Common cost analysis uses](/azure/cost-management-billing/costs/cost-analysis-common-uses).

- Azure Policy comes with built-in policies to label resources automatically, or flag resources without tagging. To learn more, see [Assign policy definitions for tag compliance](/azure/azure-resource-manager/management/tag-policies). Another use case for Azure Policy is to prevent provisioning of certain tiers, which helps prevent over-provisioning in development or production environments.

### Monitoring

Many tools included in your Azure subscription can help your organization implement financial governance and get more value out of your IoT services. These tools help you track resource usage and manage costs across all of your clouds with a single, unified view. You can access rich operational and financial insights to make informed decisions.

Telemetry logging commonly uses [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview) workspaces in [Azure Monitor](https://azure.microsoft.com/services/monitor). Log Analytics includes 5 GB of storage, and the first 30 days of retention are free. Depending on business needs, you might need a longer retention period. Review and decide the right retention period to avoid unintentional costs.

Log Analytics provides a workspace environment to query logs interactively. You can export logs periodically to external locations such as Azure Data Explorer, or archive logs in a storage account for a less expensive storage option. For more information, see [Monitor usage and estimated costs in Azure Monitor](/azure/azure-monitor/usage-estimated-costs).

### Azure Advisor

[Azure Advisor](/azure/advisor) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Advisor analyzes your resource configuration and usage telemetry, and recommends solutions that can help you improve cost effectiveness, performance, reliability, and security.

Advisor helps you optimize and reduce your overall Azure spending by identifying idle and underutilized resources. You can get cost recommendations from the cost tab on the Advisor dashboard.

Although Advisor doesn't offer specific recommendations for IoT services, it can provide useful recommendations for Azure infrastructure, storage, and analytics services. For more information, see [Reduce service costs by using Azure Advisor](/azure/advisor/advisor-cost-recommendations).

## Next steps

> [!div class="nextstepaction"]
> [Operational excellence in your IoT workload](iot-operational-excellence.md)

## Related resources

- [Cost optimization design principles](../cost-optimization/principles.md)
- [Capture cost requirements](../cost-optimization/design-capture-requirements.md)
- [Develop a cost model](../cost-optimization/design-model.md)
- [Checklist - Design for cost](../cost-optimization/design-checklist.md)
- [Checklist - Optimize cost](../cost-optimization/optimize-checklist.md)
- [Azure IoT reference architecture](/azure/architecture/reference-architectures/iot)
- [Azure IoT documentation](/azure/iot-fundamentals)
