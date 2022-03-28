---
title: Reliability in an IoT workload
description: Includes guidance and recommendations that apply to the reliability pillar in an IoT workload.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---

# Reliability in your IoT workload

Everything has the potential to break and IoT workloads are no exception. Because of this, you should design your architecture with availability and resiliency in mind. The key considerations are how quickly you can detect change and how quickly you can resume operations. IoT applications are unique as they're distributed at massive scale, operate over unreliable networks with no persistent access or visibility into the end-to-end data flows.

Your environment should consider resilient architectures, cross-region redundancies, service levels indicators (SLI), service-level objectives (SLO), service-level agreements (SLAs), and critical support. The existing environment should include auditing, monitoring, and alerting by using integrated monitoring and a notification framework.

On top of these environmental controls, the workload team should consider:

- Defining SLI and SLO around observability for your specific use case.
- Making architecture modifications to improve service level SLAs.
- Building redundancy into the workload-specific architecture.
- Adding processes for monitoring and notification beyond what’s provided by the cloud operations teams

Designing for reliability in an IoT solution should take into consideration the foundational layers in the IoT architecture. To achieve overall solution reliability, each layer should have acceptable levels of reliability.

:::image type="content" source="media/architecture-layers.svg" alt-text="Diagram that shows the layers and cross-cutting activities in the I o T architecture." border="false":::

**Device and gateway layer**: Devices and gateways come in many forms and shape. Typically, devices and gateways perform the following functions:

- Data collection
- Supervisory control
- Edge analytics

Data collection means the device is connected to sensors or subscribes to telemetry from downstream systems and then pushes this data to the cloud. The solution design should ensure reliable device management and reliable communications from the device to the cloud.

Devices that provide supervisory control not only collect data to send to the cloud, but also take actions based in that data. Actions send data back to the machines or environment that the device is authorized to perform supervisory actions on. The reliability of the application running on a device that has supervisory control is crucial.

**Transport layer**: To connect to the cloud service for data, control and management, devices need access to a network. Depending on the type of IoT solution, connectivity reliability may be in your hands or the hands of the network service provider. Networks may have intermittent connectivity issues and in this case the device needs to manage its behavior accordingly.

**Device management and modeling layer**: The cloud service provides the device with an identity and manages the devices at scale. The cloud is often the final data ingress point for all messages flowing from the devices. IoT solutions must provide reliability for the cloud services required for the IoT devices to integrate and transmit data. As described in the [Azure WAF – Reliability pillar](/azure/architecture/framework/resiliency/principles), the cloud services used in your IoT solution must implement reliability principles to provide high availability for your overall IoT solution.

Building a reliable IoT solution requires careful consideration for devices, cloud, and how they interact together. The choices made for device hardware, connectivity and protocols, and cloud service selection affect the reliability needs of your solution.

As described in the [Azure WAF – Reliability pillar](/azure/architecture/framework/resiliency/principles), desired reliability is subjective. Ensure you understand the business requirements for your solution.

## Prerequisites

To assess your IoT workload based on the principles described in the Microsoft Azure Well-Architected Framework, complete the assessment:

<!-- TODO: add link to assessment -->

After you complete the assessment, this guide helps you address the key reliability recommendations identified for your solution.

## Principles

The following principles should be followed as you design your IoT solution for reliability:

- Design devices for resiliency
- Establish safe practices for updates
- Establish observability across your IoT solution
- Implement HA/DR in critical components
- Plan for capacity

## Device and gateway layer

### Design devices for resiliency

As part of your overall IoT solution, design your devices to satisfy the uptime and availability requirements of your end-to-end solution. The following practices focus on connectivity to the cloud service, error handling, and monitoring:

#### Design

Ensure that your IoT device can operate efficiently with intermittent connectivity to the cloud. An IoT solution should enable the flow of information between intermittently connected devices and the cloud-based solutions. Best practices include:

- Implement retry and backoff logic in device software.
- Synchronize device state with the cloud.
- Devices have the capability to store data on the device if data loss can’t be tolerated.
- Use data sampling and simulations to baseline the network capacity and storage requirements.

#### Implement

The Azure IoT device SDKs provide a set of client libraries that you can use on devices or gateways to simplify connectivity with the Azure IoT services. You can use the SDKs to implement an IoT client that facilitates the connectivity to the cloud, provides a consistent client development experience across different platforms, and that simplifies common connectivity tasks by abstracting details of the underlying protocols and message processing patterns such as exponential backoff with jitter and retry logic.

#### Monitor

Connectivity issues for IoT devices can be difficult to troubleshoot because there are many possible points of failure. Application logic, physical networks, protocols, hardware, IoT Hub, and other cloud services can all cause problems. The ability to detect and pinpoint the source of an issue is critical. However, an IoT solution at scale could have thousands of devices, so it's not practical to check individual devices manually. Azure Monitor and Event Grid are valuable tools that can help to diagnose connectivity issues in IoT Hub.

#### Resources

- [Manage connectivity and reliable messaging by using Azure IoT Hub device SDKs](/azure/iot-hub/iot-hub-reliability-features-in-sdks)
- [Monitor, diagnose, and troubleshoot Azure IoT Hub disconnects](/azure/iot-hub/iot-hub-troubleshoot-connectivity)
- [Retry general guidance - Best practices for cloud applications](/azure/architecture/best-practices/transient-faults)
- [Error handling for resilient apps - Azure Architecture Center](/azure/architecture/framework/resiliency/app-design-error-handling)
- [Circuit Breaker pattern - Cloud Design Patterns](/azure/architecture/patterns/circuit-breaker)
- [Compensating Transaction pattern - Cloud Design Patterns](/azure/architecture/patterns/compensating-transaction)
- [Throttling pattern - Cloud Design Patterns](/azure/architecture/patterns/throttling)

### Establish safe practices for updates

An enterprise IoT solution should provide a strategy for how operators handle the ongoing management of their devices. IoT operators require simple and reliable tools and applications that enable reliable device updates.

#### Design

Considering the distributed nature of IoT solutions it’s important to adopt safe and secure policies for updating and deploying applications. Unlike cloud-based solutions, the device side of IoT solutions brings new challenges. For example, devices must be continually updated for vulnerabilities and application changes.

A device update solution must support:

- Gradual update rollout through device grouping and update scheduling controls.
- Support for resilient device updates (A/B) to deliver seamless rollback.
- Detailed update management and reporting tools.
- Optimization for network based on available bandwidth.

#### Implement

[Device Update for IoT Hub](/azure/iot-hub-device-update/) is a service that enables safe, secure, and reliable IoT device updates over-the-air (OTA). Device Update for IoT Hub includes the ability to group devices and specify which devices should receive an update. Operators can view the status of update deployments and make sure each device successfully applies required updates.

When an update failure happens, Device Update for IoT Hub lets operators identify the devices that failed to apply the update and see failure details. The ability to identify which devices failed to update means fewer manual hours trying to pinpoint the source of a failure.

#### Monitor

Operators need to monitor the state of device deployments and updates. In Device Update for IoT Hub, compliance measures how many devices have installed the highest version compatible update. A device is compliant if it has installed the highest version available update that is compatible for it.

### Considerations for IoT devices

#### Design

Design and select IoT Devices to function reliably in the expected operation conditions and over the expected lifetime.

A reliable device should perform according to its hardware and software specifications and any failure should be properly detected and managed through mitigation, repair, or  replacement.

*"Design for reliability, but also plan for failures."* 

#### Device lifecycle

Device lifetime is limited and affects the solution reliability. Assess the consequences of device failure on the solution, and define a device lifecycle strategy in accordance with the solution requirements.  

Device failure impact assessment includes severity (for example single points of failures), probability (for example MTBF), detectability (see FMEA practice), and an acceptable downtime period.  

The acceptable operational downtime determines the speed and extent to which devices should be maintained. For a solution lifecycle, the availability or longevity of supply of devices and parts is an important aspect. The more modular the design, the easier it is to swap out parts of the system, especially if some parts become obsolete earlier than others. Alternative or multi-sourcing of component and module supply chains are critical for highly reliable solutions.  

#### Environmental requirements

The conditions in which a device will operate, affect its reliability. Define your environmental requirements, and use devices with appropriate feature specifications. These specifications include parameters such as operating temperature range, humidity, ingress protection rating (IPxx), relevant and EMI immunity, and shock and vibration immunity.

#### Operational profile

The operational behavior of devices affects the performance stress applied to devices, and therefore their reliability. Define operational profiles (estimating device behavior throughout its lifetime), and assess device reliability accordingly. Such profiles include operation modes (for example wireless transmission on, low-power modes) and environmental condition (such as temperature) over device lifetime.  

In normal operating conditions, the device and software should run safely inside the specified performance profiles and avoid running at the edge of the device capabilities. A device should be able to service and process all external sensors and data processing required by the solution.  

#### Regulations and standards

For specific industries, devices are subject to applicable regulations and standards. Regulations and standards should be defined, and devices should meet compliance and conformity accordingly. Regulations include certification and marking (for example FCC, CE). Standards include industry/application (for example ATEX and MILspec) and safety (for example IEC 61508) conformance.

#### Connectivity

Device connectivity conditions, including upstream to the cloud and downstream local network, should be part of the solution design and reliability. Assess the potential effect of connectivity interruption or interference, and define a connectivity strategy accordingly. The connectivity strategy should include robustness, for example fallback capability and disconnection management, and include buffering back up to mitigate cloud dependency for critical/safety functions.

## Management and operations layer (DevOps)

### Establish observability across your IoT solution

Monitor every component of your IoT Solution and define alerts and procedures to manage the overall reliability. All Azure IoT services publish metrics that describe the health and availability of the service. In addition to the cloud service metrics, you should also consider the metrics that need to be established on the device side to establish end-to-end observability for reliability. Consider using these metrics as part of your overall solution reliability monitoring.

By monitoring the operation of an IoT application and devices relative to a healthy state, you can detect and fix reliability issues. Monitoring and diagnostics of an IoT Application are crucial for availability and resiliency. If something fails, you need to know that it failed, when it failed, and why it failed:

#### Design

Before you mitigate issues that affect the reliability of an IoT application, you must be able to capture logs and signals that help you detect issues in the end-to-end operation of the solution.

Use IoT solution logging and monitoring systems to determine whether the solution is functioning as expected and to help troubleshoot what is wrong with its components.

Complete the following actions to establish observability on an IoT solution:

- Establish a mechanism to collect and analyze performance metrics and alerts related to your IoT solution.
- Configure devices, cloud services, and applications to collect and connect with Azure Monitor.
- Enable a real-time dashboard and alerts to monitor the Azure backend services.
- Define roles and responsibilities to monitor and act on events and alerts.
- Implement continuous monitoring.

#### Implement

Establish a mechanism to collect and analyze performance metrics and alerts related to the IoT solution.

[Azure Monitor](/azure/azure-monitor/essentials/data-platform-metrics) is the recommended monitoring and visualization solution for Azure IoT solutions. Devices, services, and applications, regardless of deployment location, can push log messages directly or via built-in connectors into Azure Monitor. Azure Monitor provides custom log parsing to facilitate the decomposition of events and records into individual fields for indexing and search.

**Configure devices, cloud services, and applications to collect and connect with Azure Monitor.**

Enable remote monitoring of IoT Edge devices using [Azure Monitor and built-in metrics integration](/azure/iot-edge/how-to-collect-and-transport-metrics). To enable this capability on your devices, add the metrics-collector module to your deployment and configure it to collect and transport module metrics to Azure Monitor.

If your solution uses IoT Central, you can use the set of metrics provided by IoT Central to assess the health of devices connected to your IoT Central application and the health of your running data exports. Metrics are enabled by default for your IoT Central application, and you access them from the Azure portal. In addition, the Azure Monitor data platform exposes these metrics and provides several ways for you to interact with them.

IoT Hub provides usage metrics, such as the number of messages used, and the number of devices connected. You should monitor data generated by Azure IoT Hub and relay it to Azure Monitor for analysis and to alert other services and devices. [Monitoring Azure IoT Hub](/azure/iot-hub/monitor-iot-hub)

If the IoT solution requires a custom application such as App Service, Azure Kubernetes Services, Azure Functions, use [Application Insights](/azure/azure-monitor/app/cloudservices) for monitoring and analysis. Application Insights is a feature of Azure Monitor that provides extensible application performance management (APM) and monitoring for live web apps. Developers and DevOps professionals can use Application Insights to:

- Automatically detect performance anomalies.
- Help diagnose issues by using powerful analytics tools.
- See what users actually do with apps.
- Help continuously improve app performance and usability.

**Enable real-time dashboard and alerts to monitor the Azure backend services**

Azure Monitor alerts proactively notify you when it finds specific conditions in your monitoring data. Alerts let you identify and address issues in your system before your customers notice them. You can set alerts on metrics, logs, and the activity log.

**Define roles and responsibilities to monitor and act on events and alerts**

[Roles, responsibilities, and permissions - Microsoft Azure Well-Architected Framework](/azure/architecture/framework/security/design-identity-role-definitions)

**Continuous monitoring**

Continuous Integration and Continuous Deployment (CI/CD) is a DevOps concept that can help you deliver software faster and more reliably to provide continuous value to your users. Continuous Monitoring (CM) is a new follow-up concept where you can incorporate monitoring across each phase of your DevOps and IT Ops cycles. This ensures the health, performance, and reliability of your apps and infrastructure continuously as it flows through the developer, production, and customers.

[Seven best practices for Continuous Monitoring with Azure Monitor](https://azure.microsoft.com/blog/7-best-practices-for-continuous-monitoring-with-azure-monitor/)

[Continuous integration and continuous deployment to Azure IoT Edge devices](/azure/iot-edge/how-to-continuous-integration-continuous-deployment-classic)

#### Resources

- [Monitoring Azure IoT Hub](/azure/iot-hub/monitor-iot-hub)
- [Monitoring Azure IoT Hub data reference](/azure/iot-hub/monitor-iot-hub-reference)
- [Add correlation IDs to IoT messages w/distributed tracing (pre)](/azure/iot-hub/iot-hub-distributed-tracing)
- [Check Azure IoT Hub service and resource health](/azure/iot-hub/iot-hub-azure-service-health-integration)
- [Collect and transport metrics - Azure IoT Edge](/azure/iot-edge/how-to-collect-and-transport-metrics)
- [Monitoring data reference](/azure/iot-hub/monitor-iot-hub-reference)
- [Enable message tracking](/azure/iot-hub/iot-hub-distributed-tracing)
- [Check Azure IoT Hub service and resource health](/azure/iot-hub/iot-hub-azure-service-health-integration)

### Implement HA/DR in critical components

#### Design

As you build out your IoT solution, your SLA guides you into what you need to implement in your critical components for HA/DR. You must design to meet the SLA to recover from failures across the IoT solution stack. There are multiple approaches, starting with redundancy across the IoT solution stack to enabling redundancy for specific layers of the solution stack. Cost is also a major consideration that needs to be weighed against the benefits of meeting the SLAs.

- **Define uptime goals for your IoT solution**: Azure IoT services have defined uptime and availability targets. Review the SLAs for Azure IoT services that are part of your solution and define your uptime goals. For example, Azure IoT Hub has an SLA of 99.9%. This translates into 1 minute and 36 seconds of potential downtime per day to plan for. The Azure IoT SDK provides built-in, configurable logic to handle retries and backoff.

Consider breaking the uptime goals of your solution into two categories. Operational (data ingestion) and device management. For example, if a device is sending data to an IoT hub, but the services to manage the device are unavailable, how does that affect your solution?

To learn more, see [Azure IoT Hub SDK reliability features](/azure/iot-hub/iot-hub-reliability-features-in-sdks).

#### Implement

Consider using redundant hardware options for sensors, power, and storage. This enables the device to function completely if one of the critical features isn't available. Your hardware also needs to account for issues with connectivity. For example, use a store and forward approach to data when connectivity isn't available. Azure IoT Edge has this feature built in.

Your device must also be able to handle outages in the cloud. Microsoft Azure region pairing provides an HA/DR strategy for IoT Hub. For many, this meets their SLA requirements. If this isn't enough, consider an implementation with a secondary IoT Hub enabled. You should also use Azure Device Provisioning Service (DPS) to avoid hardcoded IoT Hub configurations on your device. Should your primary IoT hub go down, DPS can assign your device to a different hub.

#### Monitor

For devices that are expected to be online most of the time, consider implementing a heartbeat message pattern using a custom route in IoT Hub that’s processed by Stream Analytics, a Logic App, or an Azure Function, to determine if a heartbeat failure has occurred. You can then use the heartbeat to define Azure Monitor alerts that take actions as needed.

Use Azure Monitor as it lets you monitor the state of your IoT Hub environment to ensure it’s running properly, and that your devices aren't being throttled or experiencing connection issues.

#### Resources

- [IoT Hub SDK reliability features](/azure/iot-hub/iot-hub-reliability-features-in-sdks)
- [IoT Hub HA/DR](/azure/iot-hub/iot-hub-ha-dr)
- [Apply IoT Hub Intra-region HA/DR](/azure/iot-hub/iot-hub-ha-dr#intra-region-ha)
- [Apply IoT Hub Cross-region HA/DR](/azure/iot-hub/iot-hub-ha-dr#cross-region-dr)
- [How to clone an Azure IoT hub](/azure/iot-hub/iot-hub-how-to-clone)
- [Test apps for availability and resiliency - Azure Architecture Center](/azure/architecture/framework/resiliency/testing)

## Ingestion and communication layer

### Plan for capacity

#### Design

**Plan for service quotas:** As with all platform services, IoT Hub and the Device Provisioning have quotas and throttles enforced on certain operations. That enables Azure to deliver predictable service levels and cost for its services. Quotas and throttles are tied to the service tier and number of units you deploy so that you can design your solution with just the right number of resources. Review quotas and throttles in advance and design your IoT Hub and DPS resources accordingly.

**Plan redundant capacity:** When you're planning your thresholds and alerts, consider the latency between the detection and action taken so that system and operators have enough time to respond to change requests. Otherwise, you may detect a need to increase the number of units, but the system fails - for example by losing messages - before the increase takes effect.

**Establish benchmarks at production scale**: Due to the distributed nature of IoT solutions and the scale of device and data, it's important to establish scale benchmarks for the overall solution. As the number of devices or quantities of data increase, the cloud gateways must scale to support uninterrupted data flow. These benchmarks help to plan for capacity risks. Use the [IoT device telemetry simulator](/samples/azure-samples/iot-telemetry-simulator/azure-iot-device-telemetry-simulator/) to simulate production scale volumes.

#### Implement

**Auto scale to adjust to quotas dynamically:** To provide the lowest cost and operational effort, consider implementing an automated system to scale your resources up and down with the varying needs of your solution. To learn more, see the following Resources section.

#### Monitor

**Monitor quotas and throttling:** A benefit of using PaaS services is the ability to scale up and down with a little effort according to your needs. To ensure solution reliability, you need to continuously monitor resource usage against quotas and throttles to detect increases in resource usage that indicate the need to scale. Depending on your business requirements, you may implement something as simple as continuously monitoring resource usage and alerting the operator when thresholds are met, or an automated system to auto scale, as described below.

#### Resources

- [Understand Azure IoT Hub quotas and throttling](/azure/iot-hub/iot-hub-devguide-quotas-throttling)
- [Overview of the Microsoft Azure IoT Hub Device Provisioning Service](/azure/iot-dps/about-iot-dps#quotas-and-limits)
- [Auto-scale your Azure IoT Hub - Code Samples](/samples/azure-samples/iot-hub-dotnet-autoscale/iot-hub-dotnet-autoscale/)
- [Azure IoT Device Telemetry Simulator - Code Samples](/samples/azure-samples/iot-telemetry-simulator/azure-iot-device-telemetry-simulator/)
- [Understanding the IP address of your IoT hub](/azure/iot-hub/iot-hub-understand-ip-address)
- [Device configuration best practices for Azure IoT Hub](/azure/iot-hub/iot-hub-configuration-best-practices)
- [Azure IoT Hub scaling](/azure/iot-hub/iot-hub-scaling)
- [Azure IoT Central quotas and limits](/azure/iot-central/core/concepts-quotas-limits)
- [Azure IoT Hub scaling](/azure/iot-hub/iot-hub-scaling)
- [Operational Throttles \| Understand Azure IoT Hub quotas and throttling](/azure/iot-hub/iot-hub-devguide-quotas-throttling#operation-throttles)
- [Other Limits \| Understand Azure IoT Hub quotas and throttling](/azure/iot-hub/iot-hub-devguide-quotas-throttling#other-limits)

## Next steps

[!div class="nextstepaction"]
[Security in your IoT workload](iot-security.md)
