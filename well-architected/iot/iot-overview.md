---
title: Overview of IoT workloads
description: Includes guidance and recommendations that apply to each of the five pillars in an IoT workload.
author: dominicbetts
ms.author: dobett
ms.date: 07/15/2022
ms.topic: overview
ms.service: architecture-center
ms.subservice: well-architected
ms.custom:
  - e2e-iot
---

# Overview of IoT workloads

Internet of Things (IoT) is a collection of managed and platform services across edge and cloud that connect, monitor, and control IoT assets. The IoT workload for the Microsoft Azure Well-Architected Framework helps you meet  architectural challenges to design, build, and run IoT solutions according to your requirements and constraints.

The IoT Well-Architected Framework addresses the three components of IoT systems:

- *Things*, or the physical objects, industrial equipment, devices, and sensors that connect to the cloud persistently or intermittently.
- *Insights*, information that the things collect that humans or AI analyze and turn into actionable knowledge.
- *Actions*, the responses of people or systems to insights, which connect to business outcomes, systems, and tools.

IoT architectures use a connected components or connected operations pattern. Each pattern interacts with a set of guiding IoT principles that drive planning and decision making. Both patterns use a layered approach that identifies the logical elements of the solution. This article discusses the IoT architectural patterns, IoT guiding principles, and architecture layers as they relate to the IoT Well-Architectured Framework.

## Architectural approaches

Most IoT solutions follow one of two patterns: *connected products* or *connected operations*. Each pattern has specific requirements and constraints in a well-architected framework.

### Connected products

The connected products pattern focuses on the hot path. End users and customers can manage and interact with products and telemetry by using real-time applications. This pattern is applicable for smart device builders, who build devices for consumers and businesses in a wide range of locations and settings. Example devices include smart coffee machines, smart TVs, and smart production machines. In these IoT solutions, the product builder provides connected smart services to the product users.

### Connected operations​

The connected operations pattern focuses on the warm or cold path, with edge devices and alerts. These solutions analyze data from multiple sources, and build machine learning models to gather operational insights. This pattern applies to enterprises and smart service providers that connect existing machines and devices. Examples include smart factories and smart buildings. In these IoT solutions, service builders deliver smart services that provide insight into the effectiveness and efficiency of connected environment.

## Pillars and principles

The IoT Well-Architected Framework provides specific IoT principles for each of the five Azure Well-Architected Framework pillars: *Cost optimization*, *operational excellence*, *performance efficiency*, *reliability*, and *security*. These pillars and principles help create clarity on considerations to ensure your workloads meet your requirements across architectural layers.

## Pillars

The Azure Well-Architected Framework consists of five pillars of architectural excellence. You can use the guiding tenets of the framework to improve the quality of an IoT workload. The following list describes how the pillars relate to IoT workloads:

- **Cost optimization** balances business goals with budget justification to create a cost-effective workload while avoiding capital-intensive solutions. Cost optimization looks at ways to reduce unnecessary expenses and improve operational efficiency across IoT workload layers.

- **Reliability** ensures that applications meet the availability commitments you make to your customers. Architecting resiliency into an application framework ensures that workloads are available and can recover from failures at any scale. Heterogeneity, scale, connectivity, and hybridity have a major impact on IoT workload reliability.

- **Security** is an important aspect of any architecture to provide confidentiality, integrity, and availability assurances against deliberate attacks and abuse of data and systems. Heterogeneity and hybridity have a major impact on IoT workload security.

- **Operational excellence** covers the processes that keep an application running in production. Heterogeneity, scale, connectivity, and hybridity have a major impact on operational IoT workload excellence.

- **Performance efficiency** is the ability of a workload to scale efficiently to meet the demands placed on it by users, external systems, and processes. Heterogeneity, scalability, connectivity, and hybridity have a major impact on IoT workload performance efficiency.

## IoT principles

The following high-level guiding principles based on the preceding pillars facilitate good IoT solution design in the cloud and at the edge:

### Heterogeneity

IoT solutions must accommodate various scenarios, environments, devices, processing patterns, and standards. The solutions should be able to handle different types of hardware and software. Identifying the level of heterogeneity for each layer at design time enables the creation of a well-architected IoT solution.

In connected products architectures, heterogeneity is affected by the variety of environments where you can deploy the smart product, such as network and type of user.

In connected operations architectures, heterogeneity is affected by the varieties of machines and devices that need to be supported. Heterogeneity focuses on support for different operational technology (OT) protocols and connectivity.

### Security

IoT solutions must consider security and privacy measures across all layers. Measures include device and user identity, authentication and authorization, data protection for data at rest and data in transit, and strategies for data attestation.

In connected products architectures, security is affected by the heterogeneity and distribution of environments, and the limited control over the use of the product. According to [STRIDE](/azure/security/develop/threat-modeling-tool-threats), the highest risk to the devices is from tampering, and to the services is from denial of service attacks if devices are hijacked.

In connected operations architectures, security is affected by the security requirements for the deployment environment. Security focuses on specific OT environment requirements and deployment models, such as [ISA95](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa95) and Purdue, and the integration with a cloud-based IoT platform. Based on [STRIDE](/azure/security/develop/threat-modeling-tool-threats), the highest risks for connected operations solutions are spoofing, tampering, information disclosure, and elevation of privilege.

### Scalability

IoT solutions must be able to support hyper-scalability, with millions of connected devices and events. IoT solutions must enable proof of concept and pilot projects that start with a few devices and events and then scale-out to hyper-scale dimensions. IoT solutions must ingest large amounts of data at high frequency. Considering the scalability of each layer is essential to the success of the IoT solution.

In connected products architectures, scalability depends on the number of devices. In most cases, each device has a limited set of data and interactions, controlled by the device builder. The scale comes from the number of devices deployed.

In connected operations architectures, scalability depends on the number of messages and events that need to be processed. In general, the number of machines and devices is limited, but OT machines and devices typically send large numbers of messages and events.

### Flexibility

IoT solutions must be built upon a principle of *composability*. Composability enables you to combine several building blocks and use various first-party or third-party technologies for the individual conceptual components. A well-architected IoT solution has extension points that enable integration with existing devices, systems, and applications. A high-scale, event-driven architecture with brokered communication is part of the backbone, with loosely coupled composition of services and processing modules.

In connected products architectures, flexibility is affected by changing enduser requirements. The solution must let you easily change device behavior and enduser services in the cloud, and provide new services.

In connected operations architectures, flexibility is affected by the addition of different types of machines and devices. Solutions should be able to easily connect legacy and proprietary protocols.

### Serviceability

IoT solutions must consider the ease of maintaining and repairing components, devices, and other system elements. Early detection of potential problems is critical. Ideally, a well-architected IoT solution can correct problems automatically before serious trouble occurs, and maintenance and repair operations should cause as little downtime or disruption as possible.

In connected products architectures, serviceability is affected by the wide distribution of devices and the ability to monitor, manage, and update the devices within enduser context and control, without direct access to that environment.

In connected operations architectures, serviceability is affected by the given context, controls, and procedures of the OT environment, which depends on already available and used systems and protocols for serviceability.

### Connectivity

IoT solutions must be able to handle extended periods of offline and low-bandwidth or intermittent connectivity. To support connectivity, create metrics to track devices that aren't communicating regularly.

In connected products architectures, connectivity is affected by unexpected extended periods of offline and low-bandwidth connectivity. Connected products run in uncontrolled consumer environments, so connectivity is unknown and hard to sustain.

In connected operations architectures, connectivity is affected by the deployment model of the OT environment. Typically, connectivity is well known and managed in OT scenarios, including intermittent connectivity.

### Hybridity

IoT solutions must address hybrid complexity, running on different hardware and platforms across on-premises, edge, and multi-cloud environments. Managing these disparate IoT workload architectures, ensuring uncompromised security, and enabling developer agility are critical to success.

In connected products architectures, hybridity is affected by the wide distribution of devices. As the builder of the IoT solution, you have control over the hardware and runtime platform. Hybridity for connected products must focus on the heterogeneity of the deployment environments.

In connected operations architectures, hybridity is affected by the distribution data and processing logic. Based on the scale and latency requirements, you must determine where data is processed and how fast the digital feedback must be.

## Architecture layers

An IoT architecture consists of a set of foundational layers. Layers are supported by specific technologies, and the IoT Well-Architected Framework highlights options for designing and creating each layer.

*Core layers* identify an IoT solution. *Common layers* aren't specific to the IoT workload. The different layers each have specific pillar-related requirements and implementations. Each IoT Well-Architected Framework pillar addresses different layer specifics.

The IoT Well-architected Framework focuses on the *core layers*, and identifies the specific impact of the IoT workload on the *common layers*. There are also *cross-cutting layers* that support designing, building, and running IoT solutions.

:::image type="content" source="media/architecture-layers.svg" alt-text="Diagram that shows the layers and cross-cutting activities in the IoT architecture." border="false":::

The following sections describe the layers in detail:

### Core layers and services

**Device and gateway**, **Device management and modeling**, and **Ingestion and communication** are the *core layers* of an IoT workload. The IoT core layers and services identify whether a solution is an IoT solution. The IoT Well-Architected Framework focuses primarily on these layers.

To realize these layers, Microsoft provides IoT specific services such as Azure IoT Hub, Azure IoT Edge, IoT Hub Device Provisioning Service (DPS), and IoT Central.

#### Device and gateway layer

This layer represents the physical or virtual device and gateway hardware deployed at the edge or on-premises. Elements in this layer include the operating systems that manage the processes on the devices and gateways, and the device and gateway firmware, which is the software and instructions programmed onto devices and gateways. This layer is responsible for:

- Sensing and acting on other peripheral devices and sensors
- Processing and transferring IoT data
- Communicating with the IoT cloud platform
- Base level device security, encryption, and trust root
- Device level software and processing management

Common use cases include reading sensor values from a device, processing and transferring data to the cloud, and enabling local communication.

Relevant Microsoft technologies include:

- [Azure IoT Edge](/azure/iot-edge/about-iot-edge)
- [Azure IoT device SDKs](/azure/iot-develop/about-iot-sdks)
- [Azure RTOS](/en-us/azure/rtos/overview-rtos)
- [Azure Defender for IoT](/azure/defender-for-iot/)
- [Azure Sphere](/azure-sphere/)
- [Windows for IoT](/en-us/windows/iot/)

#### Ingestion and communication layer

This layer aggregates and brokers communications between the device and gateway layer and the IoT cloud solution. This layer enables:

- Support for bi-directional communication with devices and gateways.
- Aggregation and combination of communications from different devices and gateways and routing communications to a specific device, gateway, or service.
- Bridging and transformation between different protocols. For example, to offer cloud or edge side services that are mediated into an MQTT message going to the device or gateway.

Relevant Microsoft technologies include:

- [Azure IoT Hub](/azure/iot-hub/iot-concepts-and-iot-hub)
- [Azure IoT Central](/azure/iot-central/core/overview-iot-central)

#### Device management and modeling layer

This layer maintains the list of devices and gateway identities, their state, and their capabilities. This layer also enables the creation of device type models and relationships between devices.

Relevant Microsoft technologies include:

- [IoT Hub Device Twins](/azure/iot-hub/iot-hub-devguide-device-twins)
- [IoT Central device templates](/azure/iot-central/core/concepts-device-templates)
- [IoT Hub DPS](/azure/iot-dps/about-iot-dps)
- [Azure Digital Twins](/azure/digital-twins/)
- [Azure IoT Plug and Play](/azure/iot-develop/overview-iot-plug-and-play)

### Common layers and services

Other workloads, such as Data & AI and modern applications, also use the common layers. The top-level Microsoft Azure Well-Architected Framework addresses the generic elements of these common layers, and other workloads address other requirements. The following sections touch on the IoT-related impact on requirements, and include links to other guidance.

#### Transport layer

This layer represents the way devices, gateways, and services connect and communicate, the protocols they use, and how they move or route events from one to the other, both on-premises and in the cloud.

Relevant Microsoft technologies include:

- OT and IoT protocols, such as MQTT(S), AMQP(S), HTTPS, OPC-UA, and Modbus.
- [Azure IoT Hub routing](/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Azure IoT Edge routing](/azure/iot-edge/module-composition#declare-routes)

#### Event processing and analytics layer

This layer takes the IoT events from the ingestion and communication layer and processes and acts on the events:

- *Hot path* stream processing and analytics happens in near real-time to identify immediate insights and actions. For example, to generate alerts on temperature rises.
- *Warm path* processing and analytics identifies short-term insights and actions. For example, to predict a trend from rising temperatures.
- *Cold path* processing and analytics creates intelligent models to use in the hot or warm paths.

Relevant Microsoft technologies include:

- [Azure Stream Analytics](/azure/stream-analytics/)
- [Azure Functions](/azure/azure-functions/)
- [Azure Databricks](/azure/databricks/)
- [Azure Machine Learning](/azure/machine-learning/overview-what-is-azure-machine-learning)
- [Azure Synapse Analytics](/azure/synapse-analytics/)

#### Storage layer

This layer enables the solution to persist IoT device events and state data for some period of time. The type of storage depends on the required use for that data:

- Streaming storage decouples IoT services and communication availability. For example, message queues.
- Time series-based storage enables warm-path analysis.
- Long-term storage enables machine learning and AI model creation.

Relevant Microsoft technologies include:

- [Azure Event Hubs](/azure/event-hubs/event-hubs-about)
- [Azure Data Explorer](/azure/data-explorer/)
- [Azure Cosmos DB](/azure/cosmos-db/introduction)
- [Azure SQL Server](/azure/azure-sql/)
- [Azure Data Lake](/azure/storage/blobs/data-lake-storage-introduction)

#### Interaction and reporting layer

This layer enables endusers to have a role-based view into device state, analytics, and event processing, and to interact with the IoT platform as an enduser.

Relevant Microsoft technologies include:

- [Azure Web Apps](/azure/app-service/overview)
- [Power Apps](/powerapps/powerapps-overview)
- [Power BI](/power-bi/fundamentals/power-bi-overview)
- [Dynamics 365 Connected Field Service](/dynamics365/field-service/connected-field-service)

#### Integration layer

This layer enables interaction with systems outside this IoT solution by using machine-to-machine or service-to-service communications APIs.

Relevant Microsoft technologies include:

- [Azure Logic Apps](/azure/logic-apps/logic-apps-overview)
- [Azure Functions](/azure/azure-functions/functions-overview)
- [Azure API management](/azure/api-management/)
- [Azure Event Grid](/azure/event-grid/overview)
- [Power Automate](/power-automate/getting-started)

### Cross-cutting activities

Cross-cutting activities like DevOps enable you to design, build, deploy, and monitor IoT solutions. The activities help to ensure your IoT solutions meet the requirements set at design time and to adjust for changes in requirements over time.

DevOps enables formerly siloed roles, like development, operations, quality engineering, and security, to coordinate and collaborate to produce better, more reliable, and agile products. DevOps is well-known in software development, but can apply to any product or process development and operations.

:::image type="content" source="media/devops-lifecycle.png" alt-text="Diagram that shows how DevOps continuously delivers value." border="false":::

Teams who adopt a DevOps culture along with DevOps practices and tools gain the ability to better respond to customer needs, increase confidence in the applications and products they build, and achieve business goals faster.

Development and deployment activities include the design, build, test, and deployment of the IoT solution and its components. The activity covers all layers including hardware, firmware, services, and reports.

Management and operations activities identify the current health state of the IoT system across the different layers.

The correct execution of cross-cutting activities determines the level of success in creating and running a well-architected IoT solution that meets changing requirements and constraints. It's important to clearly assess your maturity on these activities and take measures to ensure execution at the required quality level.

Relevant Microsoft technologies include:

- [Visual Studio](https://visualstudio.microsoft.com/)
- [Azure DevOps](https://azure.microsoft.com/overview/what-is-devops/)
- [Secure Development Lifecycle](https://www.microsoft.com/securityengineering/sdl)
- [Azure Monitor](/azure/azure-monitor/overview)
- [Azure Arc](/azure/azure-arc/overview)
- [Azure Defender for IoT](/azure/defender-for-iot/)
- [Azure Sentinel](/azure/sentinel/overview)

## Next steps

> [!div class="nextstepaction"]
> [Cost optimization in your IoT workload](./iot-cost-optimization.md)

## Related resources

- [Azure IoT reference architecture](/azure/architecture/reference-architectures/iot)
- [Azure IoT documentation](/azure/iot)
