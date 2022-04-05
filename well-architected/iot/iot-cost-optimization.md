---
title: Cost optimization in an IoT workload
description: Includes guidance and recommendations that apply to the cost optimization pillar in an IoT workload.
author: dominicbetts
ms.author: dobett
ms.date: 03/07/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom:
  - e2e-iot
---

# Cost optimization in your IoT workload

According to the survey of [IoT Signals EDITION 3 report](https://azure.microsoft.com/mediahandler/files/resourcefiles/iot-signals-report/IoT%20Signals_Edition%203_Thought%20Paper_EN.pdf), the top reason of proof of concept (PoC) failure is high cost of scaling. The report also says that the high cost of scaling IoT projects comes from the complexities of integrating across layers, for example devices, edge connectivity, compatibility across applications. 40% of adopters experiencing this issue.

**Reasons for PoC failure** (IoT Signals EDITION 3)

| **High cost of scaling**                      | **32%** |
|-----------------------------------------------|---------|
| Lack of necessary technology                  | 26%     |
| Pilots demonstrate unclear business value/ROI | 25%     |
| Too many platforms to test                    | 24%     |
| Pilot takes too long to deploy                | 23%     |

IoT solution implementations are complex projects that require understanding of broad technologies and consideration of various layers. The [IoT Well-Architected Framework describes these IoT architecture layers](iot-overview.md). The layers help you simplify and understand Azure IoT architecture. You need to establish a different cost strategy for each layer because each one has a different technology and sometimes a different ecosystem such as devices, telecommunications, or edge. Cost optimization for IoT Well-Architected Framework provides guidance on how to optimize costs across the IoT architecture layers.

Cost optimization is the process of closed-loop cost control that needs to be continuously monitored, analyzed, and improved throughout a lifecycle. Understanding the IoT architecture layers helps you design an architecture for your IoT solution that enables you to define a baseline of the costs and to consider multiple architectures for cost comparison.

IoT cost results from a tradeoff between various technology options. Sometimes, it's not a simple comparison because IoT is an end-to-end solution. You should also consider synergy and cost benefits when reconciling multiple services and technologies such as IoT central with Plug and Play, or using device twins to handle events in Azure Digital Twins. In some areas, a one-time cost is more effective than recurring costs. For example, in security where hacking techniques are always changing, it's better to import a reliable commercial operating system and module such as Azure Sphere that, for a one-off payment, provides monthly security patches to devices for a long time.

The key criteria for architecture decisions are the requirements for your IoT solution. You can separate the requirements into functional and non-functional requirements. You also need to separate the cost considerations on each type of requirements because functional requirements affect system design and non-functional requirements affect system architecture.

You need to develop multiple use cases based on the requirement and compare them before finalizing your architecture choices.

A typical IoT project includes the following processes:

- Design
- Hardware sourcing
- Development
- Integration
- Onboarding
- Deployment
- Operation

The complexity of these processes leads to many opportunities to cut costs. Cost optimization in the IoT Well-Architected Framework includes guidance to simplify cost drivers from complex IoT architecture and processes and help you make better decisions to achieve cost effectiveness.

This guide considers various combinations of Azure IoT services and related technologies. However, it doesn't consider cost optimization for specific industry and IoT use cases such as connected factory, predictive maintenance, and remote monitoring.

Because IoT projects include cloud technologies, you should review [Microsoft Azure Well-Architected Framework - Cost Optimization](../cost/index.yml) and [Cloud Cost Optimization \| Microsoft Azure](https://azure.microsoft.com/overview/cost-optimization/#ways-to-optimize) for overall guidance about cloud costs.

## Prerequisites

To assess your IoT workload based on the principles described in the Microsoft Azure Well-Architected Framework, complete the assessment:

<!-- TODO: add link to assessment -->

After you complete the assessment, this guide helps you address the key cost optimization recommendations identified for your solution.

## Principles

The [Microsoft Well-Architected Framework cost optimization](../cost/overview.md) documents is a set of generic principles. This workload view focuses on specifics for an IoT solution and considers unique factors that add to the generic guidance.

Cost effectiveness is one of the key success factors in IoT projects. In a typical IoT solution, devices generate large quantities of telemetry that is then stored in a cloud repository. How you develop devices and applications, handle large volumes of data, and design your architecture have an effect on overall costs. Because an IoT solution is a multilayered technology stack, there are many cost saving factors to consider.

Apply the following design principles to build cost effective IoT solutions:

- **Understand total cost of ownership (TCO)** – Take into account  both direct and indirect costs when you decide on the architecture for your IoT solution.
- **Establish strategies for IoT technology ecosystems** – Use the best approach for each IoT technology area that has its own ecosystem.
- **Define implementation plans for each IoT architecture layer** – Identify action items for each layer in the IoT Well-Architected Framework architecture.
- **Monitor and optimize costs** – Establish ongoing activities for cost optimization after you implement your IoT solution.

### Understand total cost of ownership

Many IoT solutions fail because of the difficulty of estimating long-term aggregated costs of various cloud services. It's critical to understand how much you're spending to run and operate all services involved. Without this understanding, it's common for budgets to run into the red, leaving you with ballooning costs.

When you evaluate infrastructure costs, account for the basics first: storage, compute, and network. Then, you must account for all services your solution needs to ingest, egress, and prepare data for use in business decisions. Make sure to estimate costs based on the architecture of the solution when it's running at scale in production, not your PoC architecture. Architecture and costs evolve rapidly after the PoC.

Furthermore, don't overlook the long-term operational costs that increase in parallel with infrastructure costs. Examples include employing technicians to operate the solution, vendors to manage non-public clouds, and customer support teams.

Your costs will depend greatly on the *chattiness* of your devices and the size of the messages your devices send. Chatty devices send many messages to the cloud every minute, while others are relatively quiet, only sending data every hour or more. Clarity about device chattiness and message size helps reduce the likelihood of over-provisioning, which leads to unused cloud capacity, or under-provisioning, which leads to elastic scale challenges. As you plan your IoT solution, consider carefully the size and frequency of the message payloads to ensure your infrastructure is the correct size for where you are today and ready to scale with you. Be sure to consider the cost of developing new features as compared to using a *Platform as a Service* (PaaS) or *Application Platform as a Service* (aPaaS) based solution.

### Establish strategies for IoT technology ecosystems

#### Device management strategy

Device management requires a strategic approach because various options are available during the *Plan – Provision – Configure – Monitor – Retire* device lifecycle. Understanding [the roles for IoT solution development and operation](/azure/iot-hub/iot-hub-configuration-best-practices) and [IoT device types](/azure/iot-develop/concepts-iot-device-types) provides a foundation for establishing a strategy for device cost management.

#### Edge, cloud, or hybrid implementation strategy

Evaluate TCO for deploying edge assets. Edge solutions can be realized with different edge architectures: multiple endpoints, IoT devices, directly connected to the cloud, or connected through an edge and/or cloud gateway. Different options for sourcing the edge solution devices can affect total cost and lead time. Ongoing maintenance and support of the device fleet also affects the overall solution cost. Cost optimization is an important step in maximizing the return on investment (ROI) of any IoT solution.

Where data should be stored and processed in a given IoT solution affects many factors such as latency, security, and cost. Analyze each use case and examine where it makes most sense to use edge processing and data storage and how it affects cost. Storing and processing data  on the edge can often save storage, transportation, and processing costs. However, when you take scale into account, cloud services often become better options because of cost and development overheads. Assess each case for costs and benefits.

The [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) is a useful tool to compare these options.

#### Connectivity and IoT protocol management

There are many factors to take into account for device to IoT gateway connectivity. For example, device connectivity, network, and protocol. An understanding of IoT communication protocols, network types, and messaging patterns helps you to design and optimize a cost effective architecture.

For device connectivity, it's important to specify the type of network to use. If you select a private LAN or WAN solution, such as WiFi or LoraWAN, consider the TCO of the network as part of the overall costs. If you use carrier networks (such as 4G, 5G, or LPWAN), include the recurring connectivity costs.

#### Platform for IoT solutions

Understand [the difference between Application Platform as a Service (aPaaS) and Platform as a Service (PaaS)](/azure/iot-fundamentals/iot-solution-apaas-paas) and choose the right platform for your IoT solution. Depending on your business requirements, development, and operation capacities, you can choose an aPaaS and PaaS IoT solution platform:

- **aPaaS** – [IoT Central](https://azure.microsoft.com/services/iot-central/#overview) is an Azure aPaaS. It enables you to focus on your business and product innovation instead of maintenance, infrastructure, development, and operations. It simplifies the creation of IoT solutions.

- **PaaS** – [IoT Hub](https://azure.microsoft.com/services/iot-hub/) is and Azure PaaS. It acts as a central message hub for bi-directional communications between your IoT app and the devices it manages. You can tune services to control the overall cost.

#### Build an architecture for scale by implementing deployment stamps

*Deployment stamping* is a common design pattern for designing applications for flexible deployment strategies, predictable scale, and cost. Using this pattern for IoT solutions provides several advantages such as geo-distributing groups of devices, deploying new features to specific stamps, observability of cost-per-device. To learn more, see [Scale IoT solutions with deployment stamps](/azure/architecture/example-scenario/iot/application-stamps).

### Define implementation plans for each IoT architecture layer

[Overview of an IoT workload - IoT Well-Architected Framework](iot-overview.md) describes the elements of an IoT architecture. It enables you to easily review cost options for each layer in a complex IoT architecture. Cost optimization for IoT Well-Architected Framework provides [actionable guides for the IoT architecture layer implementations](#design).

### Monitor and optimize costs

#### Build a cost model and budget

After you review the IoT technology stacks, estimate the initial cost through a cost model:

- [Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) helps estimate the initial and operational costs.
- [Develop a cost model - Microsoft Azure Well-Architected Framework](/azure/architecture/framework/cost/design-model).

For your solution, factor in the growth in the number of devices deployed and their messaging patterns over time. Both can have a linear or non-linear growth, which affects your total solution cost over time. You might start with a limited number of connected devices that grows each year.

#### Continuously monitor and review

Continuously monitor and review costs to identify gaps between planned and actual costs. A regular cost review meeting is a good way to achieve cost optimization.

## Design

An IoT architecture consists of a set of foundational layers. Layers are realized by using specific technologies, and the IoT Well-Architected Framework highlights options for designing and realizing each layer. There are also cross-cutting layers that enable the design, building, and running of IoT solutions.

:::image type="content" source="media/architecture-layers.svg" alt-text="Diagram that shows the layers and cross-cutting activities in the I o T architecture." border="false":::

The following sections address the layer specifics for the cost optimization pillar:

## Device and gateway layer

This layer is responsible for generating the data in your solution. Cost is a key consideration for the design of this layer. Because devices and gateways can optimize data before transferring it to the cloud, a device and gateway strategy affects the costs in your solution.

### Understand design considerations

IoT Device development requires internal or outsourced engineering resources with a specific skill set. Include these resource costs in the overall device costs. Required skills include hardware design, embedded application development, cloud and local connectivity, security and privacy, and IoT solution architecture. Industry-specific expertise may also be required.

Choosing off-the-shelf hardware or a custom design affects the cost and time-to-market for an IoT device:

- An off-the-shelf device may cost more per unit, but has predictable costs and lead times. Off-the-shelf devices also remove the need for complex supply chain management.

- A custom device can reduce unit costs, but development time and non-recurring engineering costs such as design, test, certification submissions, and manufacture are incurred. Pre-certified system components or modules can reduce time-to-market to create a semi-custom device, but these components are also more expensive than discrete chips. Supply-chain and inventory management must be properly resourced.

 For a device connected to the cloud as part of an IoT Solution, you need to optimize upstream data transmissions to maintain cost boundaries. Factors to consider include minimizing payload sizes, batching of messages, and transmission during off-peak periods. These optimizations also have incurred costs to implement.

### Azure Certified Device catalog

A make-or-buy decision takes into account qualitative (for example, WiFi certification) and quantitative (for example, BOM cost or time to market) variables.

[Azure Certified Device catalog](https://devicecatalog.azure.com) helps to reduce costs and time to build for devices that work well with Azure IoT. Most of the device development process is reduced to hardware selection. [IoT Plug and Play devices](https://devicecatalog.azure.com/devices?certificationBadgeTypes=PnP) can reduce both device and cloud development costs.

:::image type="content" source="media/plug-and-play.png" alt-text="Graphic that shows savings from Plug and Play approach.":::

### LPWAN devices

If LPWAN devices, such as LoRaWAN, NB-IoT, or LTE-M, are already connected to another IoT cloud, the [Azure IoT Central Device Bridge](/azure/iot-central/core/howto-build-iotc-device-bridge) can help to bridge to Azure IoT Central without incurring costs to change your existing devices.

### Azure RTOS

[Azure RTOS](/azure/rtos/) is an embedded development suite. Azure RTOS includes a small but powerful operating system that provides reliable, ultra-fast performance for resource-constrained devices. It's easy-to-use and market-proven, having been deployed on more than 10 billion devices worldwide. Azure RTOS supports the most popular 32-bit microcontrollers and embedded development tools, so you can make the most of existing device builder skills.

Azure RTOS is free for commercial deployment using [pre-licensed processors](https://github.com/azure-rtos/threadx/blob/master/LICENSED-HARDWARE.txt). Azure RTOS comes with Azure IoT cloud capabilities and features such as device update and security that work with cloud services. These features help to reduce both device and cloud development costs.

Azure RTOS is certified for safety and security. This helps to reduce the time and cost of building compliant devices for specific verticals such as medical, automotive, and industrial.

### Azure Sphere

[Azure Sphere](/azure-sphere/product-overview/what-is-azure-sphere) is a highly secure, end-to-end IoT solution platform with built-in communication and security features for internet-connected devices. It comprises a secured, connected, crossover microcontroller unit (MCU), a custom high-level Linux-based operating system (OS), and a cloud-based security service that provides continuous, renewable security.

Azure Sphere reduces the effort to build and maintain a secure environment from device to the cloud.

Microsoft provides OS updates and zero-day renewable security for 10 years. X.509 based PKI, user app updates, error reporting, and device management will be supported beyond 10 years without any extra subscription cost. Azure Sphere reduces the operational cost of keeping millions of devices up to date with the latest security.

### Azure Stack solutions

Azure Stack solutions extend Azure services and capabilities to environments beyond Azure datacenters, such as to a customer datacenter or edge location.

Azure Stack solutions consist of Azure Stack Edge, Azure Stack HCI, and Azure Stack Hub:

- Azure Stack Edge is a Microsoft-managed appliance, ideal for hardware-accelerated machine learning workload at edge locations. Azure Stack Edge deployed in edge location can serve multiple workloads as the core of Azure Stack Edge runs on modern technology stacks such as containers. By sharing computational power with other workloads, the cost of ownership can be reduced. To learn more, see [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/#overview).

- Azure Stack HCI is a purposed-built hyperconverged solution, with native integration with Azure. Azure Stack HCI offers a scalable virtualization solution to host industry solutions. Virtualization brings extra benefits to overall IoT solutions such as security benefits on virtualization, scalability and flexibility of virtualized environments, which can then reduce total cost of ownership by sharing the hardware with other workloads. Azure Stack HCI offers more compute power compared to Azure Stack Edge and is ideal for industry process transformation. To learn more, see [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/#overview).

Whether you choose Azure Stack Edge or Azure Stack HCI, you should identify the use cases and estimated compute power. While Azure Stack solutions bring Azure capability to the edge, the hardware sizing constrains the total compute power. Therefore sizing must be factored in to optimize cost to performance needs.

### Azure public or private multi-access edge compute (MEC)

Many IoT devices are small, inexpensive, and are designed for one or a few tasks such as collecting sensor or location data and then offloading that data for further processing. Azure [public](/azure/public-multi-access-edge-compute-mec/overview) or [private](/azure/private-multi-access-edge-compute-mec/overview) MEC and 5G help to optimize the costs associated with offloading data from devices.

IoT devices can generate large quantities of data. Some IoT devices also have strong requirements for low power consumption and low costs. MEC based IoT solutions enable low-latency processing of this data at the edge instead of on the devices or in the cloud – 1-5 ms instead of the typical 100-150 ms for the cloud. MEC based IoT solutions remain flexible, and the devices themselves can remain inexpensive, operate with minimal maintenance, and use smaller, cheaper, and long-lasting batteries. With MEC, data analytics, AI, and optimization functions remain at the edge, which keeps your IoT solution simple and inexpensive.

In addition to serving as an edge processing, compute, and 5G communication device, for IoT workloads, other workloads can use the same communication device to establish high-speed connections to the public cloud or remote sites.

### Edge management and operation

[Azure IoT Edge managed devices](https://devicecatalog.azure.com/devices?certificationBadgeTypes=IoTEdgeCompatible&deviceClass=Gateway) with gateway capabilities reduce network costs and minimize the number of messages through local processing and edge scenarios.

Deploying Azure services such as [Azure Stream Analytics](/azure/iot-edge/tutorial-deploy-stream-analytics) and [Azure Functions](/azure/iot-edge/tutorial-deploy-function) to IoT Edge lets you aggregate and filter large volumes of data at the edge and send only important data to the cloud IoT gateway. IoT Edge reduces the costs of exchanging data between the edge and an IoT cloud gateway.

[Azure Blob Storage on IoT Edge](/azure/iot-edge/how-to-store-data-blob) reduces the need to transfer large quantities of data and over the network. Edge storage is useful for transforming and optimizing large quantities of data before sending it to the cloud.

There are free-of-charge Azure IoT Edge modules for open protocols such as [OPC Publisher](/azure/industrial-iot/overview-what-is-opc-publisher) and [Modbus](/azure/iot-edge/deploy-modbus-gateway). These modules help you to connect various devices with minimal development. You can search for and download the modules in the [Azure Marketplace](https://azuremarketplace.microsoft.com/en/home). Depending on your requirements, you need to decide between build and buy. For example, if upload performance is critical, choosing a proven IoT Edge module from vendors can be more cost effective than building a custom module.

Every edge solution requires IoT devices to be deployed in the field. The deployment may need networking and power supply infrastructures that affect the cost. Using pre-existing infrastructures minimizes the installation costs. However, you may need to guarantee that the installation doesn't affect existing systems. The installation of your IoT devices requires dedicated internal or external resources that may need to be trained. Logistics, such as storage,  inventory management, and transport should be organized and considered in the solution cost. Include the cost of any decommissioning activities that take place when the devices reach the end of their operational lifecycle.

The lambda architecture pattern - hot/warm/cold - is common in IoT solutions. This pattern is often fully implemented in the cloud, and there are also relevant implementations on the edge such as when you use more performant edge devices or the Azure IoT Edge runtime. Optimizing this pattern on the edge can influence overall solution costs as it lets you choose the most cost-effective service for cloud data ingestion and processing:

- Hot path processing includes near real-time processing, process alerts, or notifications at the edge. Use Azure IoT Hub event streams to process the alerts in the cloud.

- Warm path processing includes using storage solutions on the edge. For example, use open source, time series databases, or [Azure SQL Edge](/azure/azure-sql-edge/overview). Azure SQL Edge includes edge stream processing features and time series optimized storage.

- Cold path processing includes batching up events of lower importance at the edge and using a file transfer option through the Azure Blob Storage module. This approach uses a lower cost data transfer mechanism as compared to streaming everything directly through Azure IoT Hub or IoT Central. After your cold data arrives in Azure blob storage, there are many options to process the data in the cloud.

Azure IoT Edge has built-in capabilities to use when you have high message volumes. These built-in features to batch messages can help to reduce the costs of using Azure IoT Hub or IoT Central. This feature can help reduce both the number of daily messages in IoT Hub, and the number of device-to-cloud operations per second, enabling you to choose a lower SKU or scale in IoT Hub. To learn more, see [Stretching the IoT Edge performance limits](https://techcommunity.microsoft.com/t5/internet-of-things-blog/stretching-the-iot-edge-performance-limits/ba-p/2993856).

### Secure device provisioning and authentication

Azure IoT Hub with the Device Provisioning Service (DPS) and Azure Central both support device authentication with symmetric keys, trusted platform module (TPM) attestation, or X.509 certificates. There's a cost factor associated with each of these options:

- X.509 certificates are considered the most secure option for authenticating to Azure IoT but certificate management can be costly. The lack of upfront certificate lifecycle management planning can make the use of certificates even more costly in the long-run. Typically, there's a requirement to work with third-party vendors who offer CA and certificate management solutions. This option requires using a public key infrastructure (PKI). Options include a self-managed PKI, a third-party PKI, or the Azure Sphere security service.

  > [!TIP]
  > The Azure Sphere security service is only available when you use Azure Sphere devices.

- TPM when used with X.509 certificates stored in the TPM offers an added layer of security. DPS also supports authentication through TPM endorsement keys. The main effect on costs is from hardware, potential board redesign, and its complexity.

- Symmetric key authentication is the simplest and lowest cost option. However, you must evaluate the effect on security. You need to protect keys on the device and in the cloud. Options to securely store the key on the device often move you towards a more secure option anyway.

Review costs associated with each of these options, and evaluate how they affect the overall security of your solution, balancing potentially higher hardware or services costs with increased security. Integration with your manufacturing process may also influence overall costs.

To learn more, see  [Security practices for Azure IoT device manufacturers](/azure/iot-dps/concepts-device-oem-security-practices).

### Support and maintenance

After you deploy your IoT devices, you need to support and maintain them for the lifetime of the solution. Tasks include hardware repairs, software upgrades, OS maintenance, and security patching. Consider ongoing licensing costs for commercial software and proprietary drivers and protocols. If you can't do remote maintenance, then you need to budget for onsite repairs and updates. For hardware repairs or replacements, you should keep suitable spares in stock.

For solutions that use cellular or paid for connectivity media, select as suitable data plan based on the number of devices, the size and frequency of data transmissions, and where the devices are deployed.

If a service level agreement (SLA) is in place, you need a cost-effective combination of hardware, infrastructure, and trained staff to be able to meet it.

The long-term support and maintenance of field devices can escalate to become the largest cost burden for a deployed solution. Careful consideration of the TCO of a system is crucial to realizing return on investments.

## Ingestion layer

An IoT gateway is a bridge between devices and cloud services. As a front-end service to the cloud platform, a gateway can aggregate all data with protocol translation and provide bi-directional communication with devices. The following sections describe technology and capability considerations for a cost effective implementation of this layer:

### Platform for building IoT solutions

Azure IoT offers two types of platform for building IoT solutions: IoT Hub is a platform-as-a-service (PaaS) and IoT Central is an application platform as a service (aPaaS). To reduce your costs and effort, you should understand [the difference between PaaS and aPaaS](/azure/iot-fundamentals/iot-solution-apaas-paas). Choose your platform type by considering factors such as requirements, budget, timelines, operation resources, and outsourcing. An aPaaS solution typically has more predictable operational costs in the device and gateway layer.

For total cost reduction, consider extended use cases as part of platform selection. Azure IoT Central with IoT Plug and Play is one of scenarios to review for device onboarding.

:::image type="content" source="media/decreasing-costs.jpg" alt-text="Chart that shows relative costs of different platforms." border="false":::

Reduce chatty interactions to avoid overhead:

- Devices: Avoid designing device-to-cloud interactions that use many small messages. For example, group multiple device or module twin updates into a single update. Twin updates have their own throttling. Use message batching to send multiple telemetry messages to the cloud. Be aware of the message size used for the daily quota (4K for non-free IoT Hub tiers), sending smaller messages leaves some capacity unused, sending larger messages adds to the available quota. For device-to-device or module-to-module communication at the edge, avoid designing interactions that use many small messages.
- Cloud: Avoid designing cloud-to-device interactions that use many small messages. Use a single direct method when direct feedback is required. Use a single device or module twin status update to exchange configuration and status information asynchronously.

> [!TIP]
> You can monitor chatty interactions by using [Defender for IoT micro agent](/azure/defender-for-iot/device-builders/tutorial-standalone-agent-binary-installation) and [on Azure IoT Hub](/azure/defender-for-iot/device-builders/quickstart-onboard-iot-hub). You can [create custom alerts](/azure/defender-for-iot/device-builders/concept-customizable-security-alerts) on IoT Hub if any device-to-cloud or cloud-to-device interaction exceeds a certain threshold.

IoT Hub SKU selection for message ingestion: Most IoT solutions require bi-directional communication between devices and the cloud to be fully functional and secure. The basic IoT Hub SKU provides core functionality, but excludes bi-directional control. For some early implementations of a solution, you may be able to reduce costs by using the basic SKU. As your solution progresses, you can switch to a standard SKU, so that you can optimize a secure communication channel for a lower cloud-to-device messaging costs. To learn more, see [Compare IoT Hub and Event Hubs](/azure/iot-hub/iot-hub-compare-event-hubs).

Understand the Azure IoT Hub limits and quotas to optimize costs for device-to-cloud and cloud-to-device messaging:

Messages quota: Azure IoT Hub SKUs each have a set of quotas and throttling limits that apply to operations. For example, the Standard S1 SKU has a daily quota of 400,000 messages. This quota is achieved when devices send 400,000 messages each day. The messages metric increases as a combination of several factors:

- One device-to-cloud (D2C) message up to 4 KB.
- D2C messages that exceed 4 KB are charged in chunks of 4 KB.
- For messages smaller than 4 KB, use the Azure IoT SDK *SendEventBatchAsync* method to optimize batching on the device side. For example, bundling up to four 1-KB messages at the edge increases the daily meter with just one message. Batching is only applicable for [AMQP or HTTPS](/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync).
- Most operations such as cloud-to-device messages or device twin operations also charge messages in 4-KB chunks. All these operations also add to the daily throughput, and maximum quota of messages.

Review the [Azure IoT Hub pricing information documentation](/azure/iot-hub/iot-hub-devguide-pricing), which includes detailed pricing examples.To learn more, see [IoT Hub quotas and throttling](/azure/iot-hub/iot-hub-devguide-quotas-throttling).

Different throttling limits apply to different operations in Azure IoT Hub:

- In addition to message daily quotas, operations on the service have throttling limits. A key part of cost optimization with Azure IoT Hub is to optimize both message quotas and operations throttling limits. Study the differences between the different limits in the form of operations per second, or bytes per second. To learn more, see [IoT Hub quotas and throttling](/azure/iot-hub/iot-hub-devguide-quotas-throttling).

- Device-to-cloud operations have an operations per second throttle that depends on the SKU. In addition to the message size, which is metered in 4-KB chunks, you should also consider the number of operations. This is an example where the batching on the edge is useful as it lets you send more messages in a single operation.

- One single message of 2 KB, a batched message of 10 KB, or a batched message of 256 KB only counts as one single operation, allowing you to send more data to the endpoint without reaching throttling limits.

Message size:

If payload size is critical to cost management, reducing the fixed length  overhead is important when you have a long device life-cycle or a large deployment. Options to reduce this overhead include:

- Use a shorter device ID, module ID, twin name, and message topic because an MQTT packet contains this information as payload. An MQTT payload looks like: `devices/{device_id}/modules/{module_id}/messages/events/`

- Abbreviating the fixed length overhead and the message reduces bandwidth costs.

- Compressing the payload, for example by using Gzip, can help reduce bandwidth costs.

## Event processing and analytics layer

The purpose of the event processing and analytics layer is to enable  data-driven decisions. The event timing and purpose of analytics are the key factors to consider when you choose the service. The right service choice increases the efficiency of the architecture and reduces cost of processing data and events.

### Select data process path based on analytics requirements

Base on your requirements, implement hot, warm, or cold path processing for IoT data analytics. The [Azure IoT reference architecture - Azure Reference Architectures](/azure/architecture/reference-architectures/iot#insights) helps you understand the difference between those three analytics paths and reviews the available analytics services on each path. The [Microsoft Well-Architected Framework](/azure/architecture/framework/cost/provision-datastores#data-analytics-cost) includes cost considerations for these services. To get started, determine which types of data go through the hot, warm, or cold path:

- Hot path: Data is kept in-memory and analyzed in near-real-time, typically using a stream processing engine. The output may trigger an alert or be written to a structured format that can be queried immediately using analytical tools.

- Warm path: Recent data (such as from the last day, week, or month) is kept in a storage service that can be queried immediately.

- Cold path: Historical data is kept in lower-cost storage to be queried in large batches.

:::image type="content" source="media/hot-warm-cold-paths.png" alt-text="Diagram that shows the hot, warm, and cold analytics paths." border="false":::

## Storage layer

Storing data affects IoT solution costs because providing data to end-users is one of the goals in an IoT solution. There are opportunities for cost optimization in this layer. It's important to understand storage types, capacity, and pricing, to create strategy for optimizing storage costs.

### Define an end-to-end IoT scenario for choosing storage types

The choice of a repository for telemetry depends on the use case for your IoT data. If the purpose is just to monitor IoT data and volumes are low volume, you should consider using a database. However, if your scenario includes data analysis, you need to save telemetry data to storage.

For time series optimized, append-only storage and querying, consider purpose-designed solutions such as Azure Data Explorer.

Storage and database aren't mutually exclusive. Both can work together, especially if clear hot, warm, and cold analytics paths are defined. Azure Data Explorer, or databases are commonly used for hot and warm path scenarios.

From the Azure Storage perspective, it's also important to consider the data lifecycle throughout the solution. For example, access frequency, retention requirements, and backup.

- Azure Storage enables you to define the lifecycle of the data and to automate the process of moving data from the hot tier to other tiers, which reduces storage costs in the long run. To learn more, see [Configure a lifecycle management policy](/azure/storage/blobs/lifecycle-management-policy-configure?tabs=azure-portal).

For database capabilities, it's common to choose between SQL and no-SQL solutions. SQL databases are best suited for fixed schema telemetry with simple data transformation or data aggregation requirements. To learn more, see [Types of databases on Azure](https://azure.microsoft.com/product-categories/databases/)

Azure SQL Database and TimescaleDB for PostgreSQL are common choices for SQL database. Adopt best practices to optimize costs:

- [Plan and manage Azure SQL Database](/azure/azure-sql/database/cost-management)
- [Azure SQL Database cost optimization](/azure/architecture/framework/services/data/azure-sql-database/cost-optimization)
- [Azure SQL Database for PostgreSQL Extension](/azure/postgresql/concepts-extensions)
- [Performance tuning for Azure SQL Databases](/azure/azure-sql/database/monitor-tune-overview)

If the schema of the message isn't fixed and is best represented as an object or document, no-SQL is a better option. Azure Cosmos DB provides multiple APIs such as SQL or MongoDB.

For any database, the choice of partition and index strategies are important for performance optimization and reducing unnecessary costs. To learn more, see:

- [Azure Cosmos DB partitioning and scaling](/azure/cosmos-db/partitioning-overview).
- [Azure Cosmos DB cost optimization](/azure/cosmos-db/plan-manage-costs)

Azure Synapse Analytics is modern data warehouse offering from Azure. Depending on the use case, you can pause compute when no job is running to reduce operational costs. Azure Synapse Analytics scales by [Data Warehouse Units (DWU)](/azure/synapse-analytics/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu) and you should choose the right capacity to handle your solution requirements.

## Device management and modeling layer

Managing devices is a task that orchestrates complex processes such as supply chain management, device inventory, deployment, installation, operational readiness, device update, bi-directional communication with devices, and provisioning. There are many benefits of using the device management and modeling standards of Azure IoT. The Device Provisioning Service enables low/no touch device provisioning, so you don't have to train and send people on site. Using DPS reduces the cost for truck rolls and the time spent on training and configuration. DPS also reduces the risk of mistakes due to manual provisioning.

### Azure IoT Hub Device Provisioning Service (DPS)

[DPS](/azure/iot-dps/about-iot-dps) is a helper service for IoT Hub that enables low-cost, zero-touch, just-in-time provisioning to the right IoT hub without requiring human intervention to reduce error and cost. DPS enables the provisioning of millions of devices in a secure and scalable manner.

General device management stages are common in most enterprise IoT projects. In Azure IoT, there are five stages within the device lifecycle. DPS helps device lifecycle management with IoT Hub through enrollment allocation policies, zero-touch provisioning, initial configuration setting, reprovisioning, and de-provisioning.

:::image type="content" source="media/device-provisioning.png" alt-text="Diagram that shows the device lifecycle." border="false":::

To learn more, see:

- [DPS pricing](https://azure.microsoft.com/pricing/details/iot-hub/#pricing)
- [Provisioning guide](/azure/iot-dps/how-to-reprovision#send-a-provisioning-request-from-the-device)

### IoT Plug and Play

Modeling devices reduces management costs and messaging traffic volumes. Use [IoT Plug and Play](/azure/iot-develop/overview-iot-plug-and-play) and [Azure IoT Central](/azure/iot-central/) for easy deployment and management. To learn more, see [Modeling devices with DTDL](/azure/iot-develop/howto-convert-to-pnp#design-a-model).

### Modeling assets and device state in the cloud

Compare cost differences between several device topology and entity stores such as Cosmos DB, Azure Digital Twins, and SQL Databases. Each of these services has a different cost structure, but also deliver different capabilities to your IoT solution. Depending on the required usage of the service, choose the most cost-efficient service, in line with the lower development and operations effort from adopting PaaS services.

Azure Digital Twins can implement a graph-based model of the IoT environment, both for asset management, device state, and telemetry data. You can use it as a tool to model entire environments, with real time IoT data streaming, and merging business data from non-IoT sources. You can build custom ontologies, or use standards based ontologies such as RealEstateCore, CIM, or NGSI-LD to simplify data exchange with third parties. Azure Digital Twins has a [pay-per-use pricing model](https://azure.microsoft.com/pricing/details/digital-twins/) without a fixed fee.

[Cosmos DB](/azure/cosmos-db/plan-manage-costs) is a globally distributed, multi-model database. Cost is affected by storage and throughput, with regional or globally distributed and replicated data options.

[SQL Database](/azure/azure-sql/database/cost-management) can be an efficient solution for device and asset modeling. SQL Database has several pricing models to help you optimize costs.

## Transport layer

The transport layer conveys data between other layers. As data travels between layers and services, the choice of protocol affects costs. There are many options to transfer and route data at a low cost.

### Choose a proper messaging protocol and connectivity

You can choose the protocol that your IoT devices use to send telemetry. [Choosing the right protocol](/azure/iot-hub/iot-hub-devguide-protocols) for your scenario enables devices to reduce transmission sizes and costs. Use cases such as field gateways, industry open protocol, and IoT network selection also affect costs in the transport layer.

### Optimize network traffic

[Managing connectivity and reliable messaging with the SDK](/azure/iot-hub/iot-hub-reliability-features-in-sdks) helps to reduce costs of handling unexpected behavior between device and Azure IoT services.

Device clients regularly send a *keep-alive* message to IoT Hub. For flexibility, some [Azure IoT Device SDKs](/azure/iot-hub/iot-hub-mqtt-support#default-keep-alive-timeout) provide the option to set a timespan for the message if you're using the AMQP or MQTT protocols. You don't need to add a keep-alive property in the telemetry if there's no specific requirement for it. [Charges per operation](/azure/iot-hub/iot-hub-devguide-pricing#charges-per-operation) indicates that keep-alive messages aren't charged.

Reconnect or keep-alive. Battery powered IoT devices must choose between keeping connections alive or reconnecting whenever they wake up. This choice affects power consumption and network costs.

The tradeoff relates to the business scenario and costs:

- Reconnect: Consumes packets around 6 KB for TLS connection, device authentication, and retrieving a device twin, but saves battery capacity if the device wakes up once or twice per day.

- Keep-alive: Consumes hundreds of bytes, so keeping the connection alive saves network costs if the device wakes up every few hours or less.

- Bundle messages together to decrease TLS overhead.

Device provisioning and reprovisioning for optimizing network traffic

[Azure IoT Hub Device Provisioning Service (DPS)](/azure/iot-dps/) reduces the cost of device lifecycle management from zero-touch provisioning to retirement.

Connecting to DPS every time consumes network cost for TLS and authentication. To reduce network traffic, the device should cache the IoT Hub information when it's provisioned by DPS, and then connect to IoT Hub directly until it needs to reprovision. To learn more, see [Send a provisioning request from the device](/azure/iot-dps/how-to-reprovision#send-a-provisioning-request-from-the-device).

## Interaction and reporting layer

As IoT handles time series data, there are many interactions from a large number of devices. Reporting and visualizing this IoT data realizes the value of the data. Building intuitive and simplified user experiences and well-designed data interactions are costly.

### Utilize data visualization platform for visualizing time series data

[Grafana](https://grafana.com/) is an open-source data visualization tool that provides optimized dashboards for time series data. Its communities provide various examples that you can reuse and customize in your environment. You can implement metrics and dashboard from time series data with less effort. Azure provides a [Grafana plug-in for Azure Monitor](/azure/azure-monitor/visualize/grafana-plugin).

Reporting and dashboard tools such as Power BI enable a quick start from unstructured IoT data. Power BI provides an intuitive user interface and capabilities. You can easily develop dashboards and reports using time series data and get the benefits of security and deployment at less cost.

## Integration layer

Integration with other systems and services is often complex. Maximizing efficiency is the way to optimize costs within integration layer. There are many services that you should consider to optimize integration costs. Review the guides that optimize Azure IoT for integration.

### Integrate business systems and Azure services with Azure Digital Twins

Azure Digital Twins provides capabilities to integrate various systems and services with IoT data. As Azure Digital Twins transforms all data into its own digital entity, it's important to understand its service limits and tuning points for cost reductions.

Review [Azure Digital Twins service limits](/azure/digital-twins/reference-service-limits) when designing your architecture. There are many factors to consider for implementation. Understanding functional limitations helps effectively integrate with business systems.

Reduce the complexity queries and the number of query results to [optimize Query Unit (QU)](/azure/digital-twins/concepts-query-units) costs. When you use the query API, Azure Digital Twins charges prices per QU. You can trace the number of QUs the query consumed in [the response header](/rest/api/digital-twins/dataplane/query/querytwins#response). To learn more, see [Find the QU consumption in Azure Digital Twins](/azure/digital-twins/concepts-query-units#find-the-query-unit-consumption-in-azure-digital-twins).

## DevOps layer

Cloud platform transforms capital expenditure (CAPEX) to operational expenditure (OPEX). While this provides flexibility and agility, you should still have a well-defined deployment and operational model to take full advantage of the cloud platform. A well-planned deployment creates repeatable assets to shorten time to market. A cloud platform provides agility for developers to deploy resources in seconds, but there's a risk that resources are provisioned unintentionally, or that you over-provision cloud resources. A proper cloud governance model can minimize such risks, and help to avoid incurring unwanted costs.

### Deployment

It's common to deploy workloads in multiple environments, such as development and production. Through infrastructure-as-code, you can accelerate the deployment by reusing the code and reduce time to market. Infrastructure-as-code can help avoid unintentional deployments such as deploying the incorrect SKU. First-party services such as Azure Resource Manager and Azure Bicep, or third-party services such as Terraform and Pulumi are common infrastructure-as-code options.

DevOps practices for deploying applications can be applied in IoT solutions, using the same concepts of build and release pipelines to different environments. To learn more, see Please [Use a DevOps pipeline to deploy a predictive maintenance solution](/azure/architecture/example-scenario/predictive-maintenance/iot-predictive-maintenance).

Coupled with Azure Policy, you can be sure that deployments follow Well-Architected framework guidance.

### Cloud Governance

Cloud governance isn't just about compliance and security, it's essential to prevent unnecessary costs.

Resource tagging is helpful to provide labeling on deployed resources. Labeling can provide insights on ongoing costs based on the label, together with Azure Cost Management. To learn more, see  [Common cost analysis uses](/azure/cost-management-billing/costs/cost-analysis-common-uses).

Azure Policy comes with built-in policy to label resources automatically, or flag resources without tagging. To learn more, see [Assign policy definitions for tag compliance](/azure/azure-resource-manager/management/tag-policies).

Another use case for Azure Policy is to prevent provisioning of certain SKUs, which helps to prevent over-provisioning in the development or production environments.

### Development Environment

Developers can take advantage of the flexibility that Azure provides to optimize development cost. Developers can use the Azure IoT Hub free tier, limited to one instance per subscription. This tier offers standard SKU capabilities but is limited to 8000 messages a day. This SKU is sufficient for early-stage development with a limited number of devices and  messages.

If you prefer agility over control, Azure IoT Central is a good starting point for IoT solutions development. Each tier comes with two free devices, and some messages are included, depending on the SKU. Azure IoT Central bills by devices. It's a cost effective and simple option to calculate total cost of ownership for end-to-end IoT solutions.

For compute environments, serverless architecture is often adopted for cloud native IoT solutions. Some popular Azure services for IoT workload include Azure Functions and Azure Stream Analytics. The billing mechanism depends on respective Azure services, and some allow developers to pause services without incurring extra compute cost. An example is Azure Stream Analytics for real time processing needs that developers can pause. Some Azure services are billed by usage, for example, Azure Functions is billed based on number of transactions.

Developers can take advantage of these cloud native capabilities to optimize both development cost and operational cost, with the right understanding of billing mechanism for each Azure services.

An Integrated Development Environment accelerates development and deployment. Some open-source IDEs such as Visual Studio Code provide [Azure IoT extensions](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) that enable developers to develop and deploy code to Azure IoT services with no cost.

Azure IoT provides various free [sample codes in GitHub](https://github.com/Azure/iot) with guidance. These samples help developers extend device, IoT Edge, IoT gateway, and Azure Digital Twins applications. GitHub also has features to implement seamless CI/CD environments, with less cost and effort. If the project is intended to be open-sourced, GitHub Actions are free. To learn more, see [GitHub plans and features](https://github.com/pricing#compare-features).

### Testing IoT solutions for cost estimation

When you build end-to-end IoT solutions, you need to estimate overall costs including various cloud services, through load testing. As IoT solutions use large amounts of data, a simulator can help with load testing. Some simulation code samples such as [Azure IoT Device Telemetry Simulator](/samples/azure-samples/iot-telemetry-simulator/azure-iot-device-telemetry-simulator/) help you test and estimate costs  with various parameters at scale.

## Monitor

There are many tools included in your Azure subscription to get more value out of your IoT services and implement financial governance in your organization. Such tools enable organizations to track resource usage and manage costs across all your clouds with a single, unified view, and access rich operational and financial insights to make informed decisions:

### Azure Advisor

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. It analyzes your resource configuration and usage telemetry and then recommends solutions that can help you improve the cost effectiveness, performance, reliability, and security of your Azure resources.

Azure Advisor helps you optimize and reduce your overall Azure spend by identifying idle and underutilized resources. You can get cost recommendations from the cost tab on the Advisor dashboard.

Although Azure advisor doesn't have specific out-of-box recommendations for IoT services, it can be useful to gather recommendations for Azure infrastructure, storage, and analytics services. To learn more, see [Reduce service costs by using Azure Advisor](/azure/advisor/advisor-cost-recommendations).

### Azure cost management APIs

These APIs provide the ability to explore cost and usage data through multidimensional analysis. They enable you to create customized filters and expressions that let you answer consumption-related questions about your Azure resources.

Azure Cost Management has an alert feature. Alerts are generated when consumption reaches a threshold.

Azure Cost Management APIs are available for:

- [IoT Central](/rest/api/iotcentral/)
- [IoT Hub](/rest/api/iothub/)
- [Device Provisioning Service](/rest/api/iot-dps/)

### Azure Monitor

Azure Monitor and Log Analytics Workspace are commonly used for telemetry logging. Log Analytics includes 5 GB of storage and first 30 days of retention is free. Depending on business need you may need a longer retention period.

Review and decide the right retention period to avoid unintentional costs. Azure Log Analytics provides a workspace environment to query logs interactively. You can export logs periodically to external locations such as Azure Data Explorer or archive logs in a storage account for a cheaper storage option.

To learn more, see [Monitor usage and estimated costs in Azure Monitor](/azure/azure-monitor/usage-estimated-costs).

### Auto-scale for IoT Hub unit adjustment

Each IoT Hub unit has a threshold for the number of messages. Adjusting the number of IoT Hub units dynamically helps to optimize the cost when the message volume fluctuates. You can implement an auto-scale service that automatically monitors and scales your IoT Hub service. IoT Hub provides [a customizable Azure Function sample](/samples/azure-samples/iot-hub-dotnet-autoscale/iot-hub-dotnet-autoscale/) to implement auto-scale capability. You can use your own custom logic to optimize IoT Hub tier and number of units.

## Next steps

> [!div class="nextstepaction"]
> [Operational excellence in your IoT workload](iot-operational-excellence.md)

## Resources

- [Overview of an IoT workload](iot-overview.md): Includes guidance and recommendations that apply to each of the five pillars in IoT workload.
- [Cost optimization design principles - Microsoft Azure Well-Architected Framework](/azure/architecture/framework/cost/principles): Understand cost optimization principles. These principles are a series of important considerations that can help achieve business objectives and cost justification.
- [Checklist - Design for cost - Microsoft Azure Well-Architected Framework](/azure/architecture/framework/cost/design-checklist): View checklists for the cost model and architecture to use when you design a cost-effective workload in Azure
- [Capture cost requirements for an Azure - Microsoft Azure Well-Architected Framework](/azure/architecture/framework/cost/design-capture-requirements): Learn to enumerate cost requirements and considerations, and how to align costs with business goals.
- [Checklist - Optimize cost - Microsoft Azure Well-Architected Framework](/azure/architecture/framework/cost/optimize-checklist): Use these checklist considerations to help monitor and optimize workloads by using the right resources and sizes.
- [Develop a cost model - Microsoft Azure Well-Architected Framework](/azure/architecture/framework/cost/design-model): Do cost modeling to map logical groups of cloud resources to an organization's hierarchy, and then estimate costs for those groups.
