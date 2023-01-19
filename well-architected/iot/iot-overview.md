---
title: Overview of IoT workloads
description: Learn how IoT components, architecture patterns, design areas, and architecture layers relate to well-architected IoT workloads.
author: asergaz
ms.author: sergaz
ms.date: 12/14/2022
ms.topic: overview
ms.service: architecture-center
ms.subservice: well-architected
ms.custom:
  - e2e-iot
  - engagement-fy23
products:
  - azure-iot
categories:
  - iot
---

# Overview of IoT workloads

This section of the Microsoft Azure Well-Architected Framework aims to address the challenges of building IoT workloads on Azure. This article describes the IoT design areas, architecture patterns, and architecture layers in the IoT workload.

The IoT workload design methodology is underpinned by [five pillars of architectural excellence](../index.md) which serve as a compass for subsequent design decisions across the design areas described in this article. The remaining articles in this series delve into how to evaluate the design areas using IoT-specific design principles in the reliability, security, cost optimization, operational excellence and performance efficiency pillars.

> [!TIP]
> To assess your IoT workload through the lenses of reliability, security, cost optimization, operational excellence and performance efficiency, see the [Azure Well-Architected Review](/assessments/azure-architecture-review/).

## What is an IoT workload?

The term _workload_ refers to a collection of application resources that support a common business goal or the execution of a common business process, with multiple services, such as APIs and data stores, working together to deliver specific end-to-end functionality.

Internet of Things (IoT) is a collection of managed and platform services across edge and cloud environments that connect, monitor, and control physical assets.

An _IoT workload_ therefore describes the practice of designing, building, and operating IoT solutions to help meet architectural challenges according to your requirements and constraints.

The IoT workload addresses the three components of IoT systems:

- *Things*, or the physical objects, industrial equipment, devices, and sensors that connect to the cloud persistently or intermittently.
- *Insights*, information that the things collect that humans or AI analyze and turn into actionable knowledge.
- *Actions*, the responses of people or systems to insights, which connect to business outcomes, systems, and tools.

## IoT architecture patterns

Most IoT systems use either a *connected products* or *connected operations* architecture pattern. Each pattern has specific requirements and constraints in the IoT design areas.

- *Connected products* architectures focus on the *hot path*. End users manage and interact with products by using real-time applications. This pattern applies to manufacturers of smart devices for consumers and businesses in a wide range of locations and settings. Examples include smart coffee machines, smart TVs, and smart production machines. In these IoT solutions, the product builders provide connected services to the product users.

- *Connected operations​* architectures focus on the *warm or cold path* with edge devices, alerts, and cloud processing. These solutions analyze data from multiple sources, gather operational insights, build machine learning models, and initiate further device and cloud actions. The connected operations pattern applies to enterprises and smart service providers that connect pre-existing machines and devices. Examples include smart factories and smart buildings. In these IoT solutions, service builders deliver smart services that provide insights and support the effectiveness and efficiency of connected environments.

We encourage you to also read more about the [Azure IoT reference architecture](/azure/architecture/reference-architectures/iot) and about [Industry specific Azure IoT reference architectures](/azure/architecture/reference-architectures/iot/industry-iot-hub-page) to understand the base solution architecture for IoT workloads.

## Well-Architected Framework pillars in your IoT workload

The Azure Well-Architected Framework consists of five pillars of architectural excellence, which you can use to improve the quality of IoT workloads. The following articles highlight how IoT-specific design principles influence decisions across IoT design areas:

- *Reliability* ensures that applications meet availability commitments. Resiliency ensures that workloads are available and can recover from failures at any scale. [Reliability in your IoT workload](iot-reliability.md) discusses how the IoT design areas of heterogeneity, scalability, connectivity, and hybridity affect IoT reliability.

- *Security* provides confidentiality, integrity, and availability assurances against deliberate attacks and abuse of data and systems. [Security in your IoT workload](iot-security.md) describes how heterogeneity and hybridity affect IoT security.

- *Cost optimization* balances business goals with budget justification to create cost-effective workloads while avoiding capital-intensive solutions. [Cost optimization in your IoT workload](iot-cost-optimization.md) looks at ways to reduce expenses and improve operational efficiency across IoT design areas.

- *Operational excellence* covers the processes that build and run applications in production. [Operational excellence in your IoT workload](iot-operational-excellence.md) discusses how heterogeneity, scalability, connectivity, and hybridity affect IoT operations.

- *Performance efficiency* is a workload's ability to scale efficiently to meet demands. [Performance efficiency in your IoT workload](iot-performance.md) describes how heterogeneity, scalability, connectivity, and hybridity affect IoT performance.

## IoT design areas

The key IoT design areas that facilitate a good IoT solution design are:

- Heterogeneity
- Security
- Scalability
- Flexibility
- Serviceability
- Connectivity
- Hybridity

The design areas are interrelated and decisions made within one area can impact or influence decisions across the entire design. To evaluate the design areas, use the IoT-specific design principles in the five pillars of architectural excellence. These principles help clarify considerations to ensure your IoT workload meets requirements across architecture layers.

The following sections describe the IoT design areas, and how they apply to the IoT *connected products* and *connected operations* architecture patterns.

### Heterogeneity

IoT solutions must accommodate various devices, hardware, software, scenarios, environments, processing patterns, and standards. It's important to identify the necessary level of heterogeneity for each architecture layer at design time.

In connected products architectures, heterogeneity describes the varieties of machines and devices that need to be supported. Heterogeneity also describes the variety of environments where you can deploy smart product, such as networks and types of users.

In connected operations architectures, heterogeneity focuses on support for different operational technology (OT) protocols and connectivity.

### Security

IoT solutions must consider security and privacy measures across all layers. Security measures include device and user identity, authentication and authorization, data protection for data at rest and in transit, and strategies for data attestation.

In connected products architectures, limited control over product use in heterogeneous and widely distributed environments affects security. According to the Microsoft Threat Modeling Tool [STRIDE](/azure/security/develop/threat-modeling-tool-threats#stride) model, the highest risk to devices is from tampering, and the threat to services is from denial of services from hijacked devices.

In connected operations architectures, the security requirements for the deployment environment are important. Security focuses on specific OT environment requirements and deployment models, such as [ISA95](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa95) and Purdue, and integration with the cloud-based IoT platform. Based on [STRIDE](/azure/security/develop/threat-modeling-tool-threats#stride), the highest security risks for connected operations are spoofing, tampering, information disclosure, and elevation of privilege.

### Scalability

IoT solutions must be able to support *hyper-scalability*, with millions of connected devices and events ingesting large amounts of data at high frequency. IoT solutions must enable proof of concept and pilot projects that start with a few devices and events, and then scale out to hyper-scale dimensions. Considering the scalability of each architecture layer is essential to IoT solution success.

In connected products architectures, scale describes the number of devices. In most cases, each device has a limited set of data and interactions, controlled by the device builder, and scalability comes only from the number of devices deployed.

In connected operations architectures, scalability depends on the number of messages and events to process. In general, the number of machines and devices is limited, but OT machines and devices send large numbers of messages and events.

### Flexibility

IoT solutions build on the principle of *composability*, which enables combining various first-party or third-party components as building blocks. A Well-Architected IoT solution has extension points that enable integration with existing devices, systems, and applications. A high-scale, event-driven architecture with brokered communication is part of the backbone, with loosely coupled composition of services and processing modules.

In connected products architectures, changing end-user requirements define flexibility. Solutions should allow you to easily change device behavior and end-user services in the cloud, and provide new services.

In connected operations architectures, the support for different types of devices defines flexibility. Solutions should be able to easily connect legacy and proprietary protocols.

### Serviceability

IoT solutions must consider ease of maintaining and repairing components, devices, and other system elements. Early detection of potential problems is critical. Ideally, a Well-Architected IoT solution should correct problems automatically before serious trouble occurs. Maintenance and repair operations should cause as little downtime or disruption as possible.

In connected products architectures, the wide distribution of devices affects serviceability. The ability to monitor, manage, and update devices within end user context and control, without direct access to that environment, is limited.

In connected operations architectures, serviceability depends on the given context, controls, and procedures of the OT environment, which may include systems and protocols already available or in use.

### Connectivity

IoT solutions must be able to handle extended periods of offline, low-bandwidth, or intermittent connectivity. To support connectivity, you can create metrics to track devices that don't communicate regularly.

Connected products run in uncontrolled consumer environments, so connectivity is unknown and hard to sustain. Connected products architectures must be able to support unexpected extended periods of offline and low-bandwidth connectivity.

In connected operations architectures, the deployment model of the OT environment affects connectivity. Typically, the degree of connectivity, including intermittent connectivity, is known and managed in OT scenarios.

### Hybridity

IoT solutions must address hybrid complexity, running on different hardware and platforms across on-premises, edge, and multicloud environments. It's critical to manage disparate IoT workload architectures, ensure uncompromised security, and enable developer agility.

In connected products architectures, the wide distribution of devices defines hybridity. The IoT solution builder controls the hardware and runtime platform, and hybridity focuses on the diversity of the deployment environments.

In connected operations architectures, hybridity describes the data distribution and processing logic. Scale and latency requirements determine where to process data and how fast feedback must be.

## IoT architecture layers

An IoT architecture consists of a set of foundational layers. Specific technologies support the different layers, and the IoT workload highlights options for designing and creating each layer.

- *Core layers* identify IoT-specific solutions.
- *Common layers* aren't specific to IoT workloads.
- *Cross-cutting layers* support all layers in designing, building, and running solutions.

The IoT workload addresses different layer-specific requirements and implementations. The framework focuses on the *core layers*, and identifies the specific impact of the IoT workload on the *common layers*.

:::image type="content" source="media/architecture-layers.svg" alt-text="Diagram that shows the layers and cross-cutting activities in the IoT architecture." border="false":::

The following sections describe the IoT architecture layers and the Microsoft technologies that support them.

### Core layers and services

The IoT core layers and services identify whether a solution is an IoT solution. The *core layers* of an IoT workload are:

- Device and gateway
- Device management and modeling
- Ingestion and communication

The IoT workload focuses primarily on these layers. To realize these layers, Microsoft provides IoT technologies and services such as [Azure IoT Hub](/azure/iot-hub/iot-concepts-and-iot-hub), [Azure IoT device SDKs](/azure/iot-hub/iot-hub-devguide-sdks), [Azure IoT Edge](/azure/iot-edge/about-iot-edge), [IoT Hub Device Provisioning Service (DPS)](/azure/iot-dps/about-iot-dps), [Azure Digital Twins](/azure/digital-twins/overview), and [Azure Sphere](/azure-sphere/product-overview/what-is-azure-sphere).

> [!TIP]
> [Azure IoT Central](/azure/iot-central/core/overview-iot-central) is a managed application platform that you can use to quickly evaluate your IoT scenario and assess the opportunities for your business. After you've used IoT Central to evaluate your IoT scenario, you can then build your enterprise ready solution by using the power of Azure IoT platform.

#### Device and gateway layer

This layer represents the physical or virtual device and gateway hardware deployed at the edge or on premises. Elements in this layer include the operating systems that manage the processes on the devices and gateways, and the device and gateway firmware, which is the software and instructions programmed onto devices and gateways. This layer is responsible for:

- Sensing and acting on other peripheral devices and sensors.
- Processing and transferring IoT data.
- Communicating with the IoT cloud platform.
- Base level device security, encryption, and trust root.
- Device level software and processing management.

Common use cases include reading sensor values from a device, processing and transferring data to the cloud, and enabling local communication.

Relevant Microsoft technologies include:

- [Azure IoT Edge](/azure/iot-edge/about-iot-edge)
- [Azure IoT device SDKs](/azure/iot-hub/iot-hub-devguide-sdks)
- [Azure RTOS](/azure/rtos/overview-rtos)
- [Microsoft Defender for IoT](/azure/defender-for-iot)
- [Azure Sphere](/azure-sphere/product-overview/what-is-azure-sphere)
- [Windows for IoT](/windows/iot)

#### Ingestion and communication layer

This layer aggregates and brokers communications between the device and gateway layer and the IoT cloud solution. This layer enables:

- Support for bi-directional communication with devices and gateways.
- Aggregating and combining communications from different devices and gateways.
- Routing communications to a specific device, gateway, or service.
- Bridging and transforming between different protocols. For example, mediate cloud or edge services into an MQTT message going to a device or gateway.

Relevant Microsoft technologies include:

- [Azure IoT Hub](/azure/iot-hub/iot-concepts-and-iot-hub)
- [Azure IoT Central](/azure/iot-central/core/overview-iot-central)

#### Device management and modeling layer

This layer maintains the list of devices and gateway identities, their state, and their capabilities. This layer also enables the creation of device type models and relationships between devices.

Relevant Microsoft technologies include:

- [IoT Hub device twins](/azure/iot-hub/iot-hub-devguide-device-twins)
- [IoT Central device templates](/azure/iot-central/core/concepts-device-templates)
- [IoT Hub Device Provisioning Service](/azure/iot-dps/about-iot-dps)
- [Azure Digital Twins](/azure/digital-twins/overview)
- [IoT Plug and Play](/azure/iot-develop/overview-iot-plug-and-play)

### Common layers and services

Workloads other than IoT, such as Data & AI and modern applications, also use the common layers. The top-level Azure Well-Architected Framework addresses the generic elements of these common layers, and other workload frameworks address other requirements. The following sections touch on the IoT-related impact on requirements, and include links to other guidance.

#### Transport layer

This layer represents the way devices, gateways, and services connect and communicate, the protocols they use, and how they move or route events, both on premises and in the cloud.

Relevant Microsoft technologies include:

- OT and IoT protocols, such as MQTT(S), AMQP(S), HTTPS, OPC-UA, and Modbus
- [IoT Hub routing](/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [IoT Edge routes](/azure/iot-edge/module-composition#declare-routes)

#### Event processing and analytics layer

This layer processes and acts on the IoT events from the ingestion and communication layer.

- *Hot path* stream processing and analytics happen in near real-time to identify immediate insights and actions. For example, stream processing generates alerts when temperatures rise.
- *Warm path* processing and analytics identify short-term insights and actions. For example, analytics predict a trend of rising temperatures.
- *Cold path* processing and analytics create intelligent data models for the hot or warm paths to use.

Relevant Microsoft technologies include:

- [Azure Stream Analytics](/azure/stream-analytics)
- [Azure Functions](/azure/azure-functions)
- [Azure Databricks](/azure/databricks)
- [Azure Machine Learning](/azure/machine-learning/overview-what-is-azure-machine-learning)
- [Azure Synapse Analytics](/azure/synapse-analytics)

#### Storage layer

This layer persists IoT device event and state data for some period of time. The type of storage depends on the required use for the data.

- *Streaming storage*, such as message queues, decouple IoT services and communication availability.
- *Time series-based storage* enables warm-path analysis.
- *Long-term storage* supports machine learning and AI model creation.

Relevant Microsoft technologies include:

- [Azure Event Hubs](/azure/event-hubs/event-hubs-about)
- [Azure Data Explorer](/azure/data-explorer)
- [Azure Cosmos DB](/azure/cosmos-db/introduction)
- [Azure SQL](/azure/azure-sql)
- [Azure Data Lake Storage](/azure/storage/blobs/data-lake-storage-introduction)

#### Interaction and reporting layer

This layer lets end users interact with the IoT platform and have a role-based view into device state, analytics, and event processing.

Relevant Microsoft technologies include:

- [Azure App Service](/azure/app-service/overview)
- [Power Apps](/powerapps/powerapps-overview)
- [Power BI](/power-bi/fundamentals/power-bi-overview)
- [Dynamics 365 Connected Field Service](/dynamics365/field-service/connected-field-service)

#### Integration layer

This layer enables interaction with systems outside the IoT solution by using machine-to-machine or service-to-service communications APIs.

Relevant Microsoft technologies include:

- [Azure Logic Apps](/azure/logic-apps/logic-apps-overview)
- [Azure Functions](/azure/azure-functions/functions-overview)
- [Azure API Management](/azure/api-management)
- [Azure Event Grid](/azure/event-grid/overview)
- [Power Automate](/power-automate/getting-started)

### Cross-cutting activities

Cross-cutting activities like DevOps help you design, build, deploy, and monitor IoT solutions. DevOps lets formerly siloed roles, like development, operations, quality engineering, and security, coordinate and collaborate to produce better, more reliable, and agile products.

DevOps is well-known in software development, but can apply to any product or process development and operations. Teams who adopt a DevOps culture, practices, and tools can better respond to customer needs, increase confidence in the applications and products they build, and achieve business goals faster.

The following diagram shows the DevOps continuous planning, development, delivery, and operations cycle:

:::image type="content" source="media/devops-lifecycle.png" alt-text="Diagram that shows how DevOps continuously delivers value." border="false":::

- Development and deployment activities include the design, build, test, and deployment of the IoT solution and its components. The activity covers all layers and includes hardware, firmware, services, and reports.

- Management and operations activities identify the current health state of the IoT system across all layers.

Correctly executing DevOps and other cross-cutting activities can determine your success in creating and running a Well-Architected IoT solution. Cross-cutting activities help you meet the requirements set at design time and adjust for changing requirements over time. It's important to clearly assess your expertise in these activities and take measures to ensure execution at the required quality level.

Relevant Microsoft technologies include:

- [Visual Studio](https://visualstudio.microsoft.com)
- [Azure DevOps](https://azure.microsoft.com/overview/what-is-devops)
- [Microsoft Security Development Lifecycle (SDL)](https://www.microsoft.com/securityengineering/sdl)
- [Azure Monitor](/azure/azure-monitor/overview)
- [Azure Arc](/azure/azure-arc/overview)
- [Microsoft Defender for IoT](/azure/defender-for-iot)
- [Microsoft Sentinel](/azure/sentinel/overview)

## Next steps

> [!div class="nextstepaction"]
> [Reliability in your IoT workload](./iot-reliability.md)

> [!div class="nextstepaction"]
> [Security in your IoT workload](./iot-security.md)

> [!div class="nextstepaction"]
> [Cost optimization in your IoT workload](./iot-cost-optimization.md)

> [!div class="nextstepaction"]
> [Operational excellence in your IoT workload](./iot-operational-excellence.md)

> [!div class="nextstepaction"]
> [Performance efficiency in your IoT workload](./iot-performance.md)

## Related resources

- [Azure IoT reference architecture](/azure/architecture/reference-architectures/iot)
- [Azure IoT documentation](/azure/iot-fundamentals)
