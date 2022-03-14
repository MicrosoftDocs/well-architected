---
title: Overview of an IoT workload
description: Includes guidance and recommendations that apply to each of the five pillars in an IoT workload.
author: dominicbetts
ms.author: dobett
ms.date: 03/04/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom:
  - e2e-iot
---

# Overview of an IoT workload

Internet of Things (IoT) refers to a collection of managed and platform services across edge and cloud. These services connect, monitor, and control billions of IoT assets. The IoT workload for the Microsoft Azure Well-Architected Framework helps you meet the architectural challenges, and gives you the ability to design, build, and run your IoT solution according to your requirements and constraints. The services discussed in the IoT Well-Architected Framework work together across three components:

- *Things*: The physical objects, or things such as industrial equipment, devices, and sensors, that connect to the cloud persistently or intermittently.

- *Insights*: The information collected by the things that's analyzed and turned into actionable knowledge either by people or AI.

- *Actions*: The way people or systems respond to insights and connect them to their business, as well as the systems and tools.

Your architecture decisions should be driven by a set of guiding principles and use a layered approach that identifies the logical elements of an IoT solution. The following sections describe these IoT layers.

The IoT Well-Architected Framework provides specific IoT principles for each of the five pillars: *cost optimization*, *operational excellence*, *performance efficiency*, *reliability*, and *security*. These pillars help to create clarity on what you should consider to ensure your workloads meet your requirements across the IoT layers.

## Guiding principles

The IoT Well-Architected Framework identifies the following set of high-level guiding principles to facilitate good IoT solution design in the cloud and on the edge:

- **Heterogeneity**: IoT solutions must accommodate various scenarios, environments, devices, processing patterns, and standards. The solutions should be able to handle different types of hardware and software. Identifying the level of heterogeneity at design time for each layer enables the creation of a well-architected IoT solution.

- **Security**: IoT solutions must consider security and privacy measures across all layers. Measures include device and user identity, authentication and authorization, data protection for data at rest and data in motion, and strategies for data attestation.

- **(Hyper-) Scalability**: IoT solutions must be able to support millions of connected devices and events. IoT solutions must enable proof of concept and pilot projects that start with a few devices and events and then scale-out to hyper-scale dimensions. IoT solutions must be designed to ingest large amounts of data at high frequency. Considering the scalability of each layer is essential to the success of the IoT solution.

- **Flexibility**: IoT solutions must be built upon a principle of composability. Composability enables you to combine several building blocks and use various first-party or third-party technologies for the individual conceptual components. A well-architected IoT solution has extension points that enable integration with existing devices, systems, and applications. A high-scale, event-driven architecture with brokered communication is part of the backbone, with loosely coupled composition of services and processing modules.

- **Serviceability**: IoT solutions must consider how easy it is to maintain and repair the components, devices, and other system elements. Early detection of potential problems is critical in this respect. Ideally, a well-architected IoT solution can correct problems automatically before serious trouble occurs, and maintenance and repair operations should cause as little downtime or disruption as possible.

- **Intermittent connectivity**: IoT solutions must be able to handle extended periods of offline and low-bandwidth connectivity and create metrics to track devices that aren't communicating regularly.

- **Hybridity**: IoT solutions must address hybrid complexity, running on different hardware and platforms across on-premises, edge, and multi-cloud environments. Managing these disparate IoT workload architectures, ensuring uncompromised security, and enabling developer agility are critical to success.

As well as these high-level guiding principles, each [pillar](#pillars) has its own specific guiding principles.

## Choose the right architectural approach

Most IoT solutions follow one of two patterns: *connected products* and *connected operations*. Each pattern has its own specific requirements and constraints in respect to the well-architected framework.

### Connected products

The connected products pattern focuses on the hot path, enabling end-users and customers to manage and interact with products and telemetry by using real-time applications. For example, this pattern is applicable for smart device builders, who build devices that are distributed to consumers and businesses in a wide range of locations and settings. Example devices include smart coffee machines, smart TVs, and smart production machines. In general, these are IoT solutions where the builder of the product provides connected and smart services to the user of the product.

The following list describes how thethe connected products pattern affects the high-level guiding principles:

- **Heterogeneity** is affected by the variety of environments where you can deploy the smart product, such as network and type of user.

- **Security** is affected by the heterogeneity and distribution of environments, and the limited control over the use of the product. According to [STRIDE](/azure/security/develop/threat-modeling-tool-threats), the highest risk to the devices is from tampering, and to the services is from denial of service attacks if devices are hijacked.

- **(Hyper-) Scalability** is affected by the number of devices. In most cases, each device has a limited set of data and interactions, controlled by the device builder. The scale comes from the number of devices deployed.

- **Flexibility** is affected by changing end-user requirements. The solution must let you easily change device behavior and end-user services in the cloud, and provide new services.

- **Serviceability** is affected by the wide distribution of devices and the ability to monitor, manage, and update the devices within the end-user context and control, without direct access to that environment.

- **Intermittent connectivity** is affected by unexpected extended periods of offline and low-bandwidth connectivity. Connected products run in uncontrolled consumer environments, and therefore connectivity is unknown and hard to sustain.

- **Hybridity** is affected by the wide distribution of devices. As the builder of the IoT solution, you have control over the hardware and runtime platform. Hybridity for connected products must focus on the heterogeneity of the deployment environments.

### Connected operations​

The connected operations pattern focuses on edge devices, alerts, and the warm/cold path. These IoT solutions analyze data from multiple sources and build machine learning models to gather valuable operational insights. This pattern applies to enterprises and smart service providers, where they connect existing machines and devices. Examples include smart factories and smart buildings. In general, these are IoT solutions where the builder of the service delivers connected and smart services that provide insight into the effectiveness and efficiency of the connected environment.

The following list describes how the connected operations pattern affects the high-level guiding principles:

- **Heterogeneity** is affected by the varieties in machines and devices that need to be supported. Heterogeneity focuses on support for different operational technology (OT) protocols and connectivity.

- **Security** is affected by the security requirements for the deployment environment. Security focuses on specific OT environment requirements and deployment models, such as [ISA95](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa95) and Purdue, and the integration with a cloud-based IoT platform. Based on [STRIDE](/azure/security/develop/threat-modeling-tool-threats), the highest risks for connected operations solutions are spoofing, tampering, information disclosure, and elevation of privilege.

- **(Hyper-) Scalability** is affected by the number of messages and events that need to be processed. In general, the number of machines and devices is limited, but OT machines and devices typically send large numbers of messages and events.

- **Flexibility** is affected by the addition of different types of machines and devices. Solutions should be able to easily connect legacy and proprietary protocols.

- **Serviceability** is affected by the given context, controls, and procedures of the OT environment and depend on already available and used systems and protocols for serviceability.

- **Intermittent connectivity** is affected by the deployment model of the OT environment. Typically, connectivity is well known and managed in OT scenarios, including intermittent connectivity.

- **Hybridity** is affected by the distribution data and processing logic. Based on the scale and latency requirements, you must determine where data is processed and how fast the digital feedback must be.

## Architecture layers

An IoT architecture consists of a set of foundational layers. Layers are realized by using specific technologies, and the IoT Well-Architected Framework highlights options for designing and realizing each layer. There are also cross-cutting layers that enable the design, building, and running of IoT solutions. The different layers each have specific pillar related requirements and implementations. Each IoT Well-Architected Framework pillar addresses the different layer specifics.

:::image type="content" source="media/architecture-layers.svg" alt-text="Diagram that shows the layers and cross-cutting activities in the I o T architecture." border="false":::

**Device and gateway**, **Device management and modeling**, and **Ingestion and communication** are *core layers* for an IoT solution. These layers identify whether the solution is an IoT solution. To realize these layers, Microsoft provides IoT specific services such as Azure IoT Hub, Azure IoT Edge, Device Provisioning Service, and IoT Central. The other layers are *commonly used* and aren't specific to the IoT workload. The IoT Well-architected Framework workload focuses on the *core layers* and identifies the specific impact of the IoT workload on the *common layers*. Other workloads, such as Data & AI and Modern Applications also use the common layers. The top-level Microsoft Azure Well-Architected Framework addresses the generic elements of these common layers.

The following sections describe these layers in more detail:

## Core layers and services

The IoT core layers and services identify whether a solution is an IoT solution. The IoT Well-Architected Framework focuses primarily on these layers.

### Device and gateway layer

This layer represents the physical or virtual device and gateway hardware deployed at the edge or on premise. Elements in this layer include the operating systems that manage the processes on the devices and gateways, and the device and gateway firmware, which is the software and instructions programmed onto devices and gateways. This layer is responsible for:

- Sensing and acting on other peripheral devices and sensors
- Processing and transferring IoT data
- Communicating with the IoT cloud platform
- Base level device security, encryption, and trust root
- Device level software and processing management

Common use cases include reading sensor values from a device, processing and transferring data to the cloud, and enabling local communication.

Relevant Microsoft technologies include:

- [Azure IoT Edge](/azure/iot-edge/about-iot-edge)
- [Azure IoT Device SDKs](/azure/iot-develop/about-iot-sdks)
- [Azure RTOS](/en-us/azure/rtos/overview-rtos)
- [Azure Defender for IoT](/azure/defender-for-iot/)
- [Azure Sphere](/azure-sphere/)
- [Windows for IoT](/en-us/windows/iot/)

### Ingestion and communication layer

This layer aggregates and brokers communications between the device and gateway layer and the IoT cloud solution. This layer is important because it enables:

- Support for bi-directional communication with devices and gateways.
- Aggregation and combination of communications from different devices and gateways and routing communications to a specific device, gateway, or service.
- Bridging and transformation between different protocols. For example, to offer cloud or edge side services that are mediated into an MQTT message going to the device or gateway.

Relevant Microsoft technologies include:

- [Azure IoT Hub](/azure/iot-hub/iot-concepts-and-iot-hub)
- [Azure IoT Central](/azure/iot-central/core/overview-iot-central)

### Device management and modeling layer

This layer maintains the list of devices and gateway identities, their state, and their capabilities. This layer also enables the creation of device type models and relationships between devices.

Relevant Microsoft technologies include:

- [Azure IoT Hub - Device Twins](/azure/iot-hub/iot-hub-devguide-device-twins)
- [Azure IoT Central - Device Templates](/azure/iot-central/core/concepts-device-templates)
- [Azure IoT Hub DPS](/azure/iot-dps/about-iot-dps)
- [Azure Digital Twins](/azure/digital-twins/)
- [Azure IoT Plug and Play](/azure/iot-develop/overview-iot-plug-and-play)

## Common layers and services

These layers can also be used in other workloads. This section touches on the IoT related impact on requirements. The generic Well-Architected framework and other workloads address other requirements. The following sections include links to that other guidance.

### Transport layer

This layer represents the way devices, gateways, and services connect and communicate, the protocols they use, and how events are moved or routed from one to the other, both on-premises and in the cloud.

Relevant Microsoft technologies include:

- OT and IoT protocols, such as MQTT(S), AMQP(S), HTTPS, OPC-UA, Modbus.
- [Azure IoT Hub Routing](/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Azure IoT Edge Routing](/azure/iot-edge/module-composition#declare-routes)

### Event processing and analytics layer

This layer takes the events from the ingestion and communication layer and then processes and acts on those IoT events:

- *Hot path* event stream processing and analysis takes place in near real-time to identify immediate insights and actions. For example, to generate alerts on temperature rises.
- *Warm path* event processing and analysis identifies short-term insights and actions. For example, to predict a trend from rising temperatures.
- *Cold path* event processing and analysis creates intelligent models to use in hot or warm paths.

Relevant Microsoft technologies include:

- [Azure Stream Analytics](/azure/stream-analytics/)
- [Azure Functions](/azure/azure-functions/)
- [Azure Databricks](/azure/databricks/)
- [Azure Machine Learning](/azure/machine-learning/overview-what-is-azure-machine-learning)
- [Azure Synapse](/azure/synapse-analytics/)

### Storage layer

This layer enables the solution to persist IoT device events and state data for a period. The type of storage depends on the required use for that data:

- Streaming storage decouples IoT services and communication availability. For example, message queues.
- Time series-based storage enables warm-path analysis.
- Long-term storage enables machine learning and AI model creation.

Relevant Microsoft technologies include:

- [Azure Event Hubs](/azure/event-hubs/event-hubs-about)
- [Azure Data Explorer](/azure/data-explorer/)
- [Azure Cosmos DB](/azure/cosmos-db/introduction)
- [Azure SQL Server](/azure/azure-sql/)
- [Azure Data Lake](/azure/storage/blobs/data-lake-storage-introduction)

### Interaction and reporting layer

This layer enables end-users to have a role-based view into device state, analytics, event processing, and to interact with the IoT platform as an end-user.

Relevant Microsoft technologies include:

- [Azure Web Apps](/azure/app-service/overview)
- [Azure Power Apps](/powerapps/powerapps-overview)
- [Power BI](/power-bi/fundamentals/power-bi-overview)

### Integration layer

This layer enables interaction with systems outside this IoT solution by using machine-to-machine or service-to-service communications APIs.

Relevant Microsoft technologies include:

[Azure API management](/azure/api-management/)

## Cross-cutting activities

These activities enable you to design, build, deploy, and monitor IoT solutions. The activities help to ensure your IoT solutions meet the requirements set at design time and to adjust for changes in requirements over time. DevOps enables formerly siloed roles—development, operations, quality engineering, and security to coordinate and collaborate to produce better, more reliable, and agile products. DevOps is well-known in the software development industry but can be applied to any product or process development and operations process.

:::image type="content" source="media/devops-lifecycle.jpg" alt-text="Diagram that shows how DevOps continuously delivers value." border="false":::

Teams who adopt a DevOps culture along with DevOps practices and tools gain the ability to better respond to customer needs, increase confidence in the applications and products they build, and achieve business goals faster.

- **Development and deployment** activity includes the design, build, test, and deployment of the IoT solution and its components. The activity covers all layers including hardware, firmware, services, and reports.
- **Management and operations** activity identifies the current health state of the IoT system across the different layers.

> [!NOTE]
> The correct execution of cross-cutting activities determines the level of success in creating and running an IoT solution that is and keeps on being well-architected and that meets your changing requirements and constraints. It's important to clearly identify your maturity on these activities and take measures to ensure execution on the required quality level.

Relevant Microsoft technologies include:

- [Visual Studio](https://visualstudio.microsoft.com/)
- [Azure DevOps](https://azure.microsoft.com/overview/what-is-devops/)
- [Secure Development Lifecycle](https://www.microsoft.com/securityengineering/sdl)
- [Azure Monitor](/azure/azure-monitor/overview)
- [Azure Arc](/azure/azure-arc/overview)
- [Azure Defender for IoT](/azure/defender-for-iot/)
- [Azure Sentinel](/azure/sentinel/overview)

## Pillars

The Azure Well-Architected Framework is a set of guiding tenets that you can use to improve the quality of an IoT workload. The framework consists of five pillars of architectural excellence. The following pillar sections describe the details related to the IoT workload:

- **Reliability** ensures your application can meet the commitments you make to your customers. Architecting resiliency into your application framework ensures your workloads are available and can recover from failures at any scale. How you handle heterogeneity, scale, intermittent connectivity, and hybridity have a major impact on your IoT workload reliability experience.

- **Security** is an important aspect of any architecture. It provides confidentiality, integrity, and availability assurances against deliberate attacks and abuse of your valuable data and systems. How you handle heterogeneity and hybridity have a major impact on your IoT workload security experience.

- **Cost Optimization** provides principles for balancing business goals with budget justification to create a cost-effective workload while avoiding capital-intensive solutions. Cost optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies, across the different IoT workload layers.

- **Operational Excellence** covers the operations processes that keep an application running in production. How you handle heterogeneity, scale, intermittent connectivity, and hybridity have a major impact on your operational IoT workload excellence experience.

- **Performance Efficiency** is the ability of your workload to scale in an efficient manner to meet the demands placed on it by users, external systems, and processes. How you handle heterogeneity, scale, intermittent connectivity, and hybridity have a major impact on your IoT workload performance efficiency experience.

## Next steps

> [!div class="nextstepaction"]
> [Cost optimization in your IoT workload](./iot-cost-optimization.md)

## Resources

- [Azure IoT reference architecture](/azure/architecture/reference-architectures/iot)
- [Azure IoT documentation](/azure/?product=iot)
