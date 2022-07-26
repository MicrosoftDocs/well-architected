---
title: Reliability in an IoT workload
description: See guidance and recommendations that apply to the reliability pillar in an IoT workload.
author: dominicbetts
ms.author: dobett
ms.date: 07/26/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---

# Reliability in your IoT workload

IoT workloads, like other workloads, have the potential to malfunction. The key reliability considerations for IoT workloads are how quickly you can detect changes and how quickly you can resume operations after an outage.

IoT applications are often distributed at massive scale, and might operate over unreliable networks without persistent access or visibility into end-to-end data flows. Because of these features, you should design your IoT architecture with availability and resiliency in mind.

Building a reliable IoT solution requires careful consideration of devices, cloud, and how they interact. The choices you make for device hardware, connectivity and protocols, and cloud services affect your solution's reliability requirements and capabilities.

A resilient IoT environment should consider the following factors:

- Resilient workload-specific architectures that build in redundancy.
- Cross-region redundancies.
- Service levels indicators (SLIs) and service-level objectives (SLOs) defined around observability for your use case.
- Architecture modifications to improve service-level agreements (SLAs).
- Integrated auditing, monitoring, and alerting with a notification framework, beyond what cloud services provide.
- Critical support.

Follow these principles as you design your IoT solution for reliability:

- Design devices for resiliency.
- Establish safe practices for updates.
- Establish observability across your IoT solution.
- Implement high availability and disaster recovery (HA/DR) in critical components.
- Plan for capacity.

## Azure Well-Architected Framework Reliability pillar for IoT workloads

As described in the [Azure WAF – Reliability pillar](/azure/architecture/framework/resiliency/principles), desired reliability is subjective. Make sure you understand the business requirements for your solution.

To assess your IoT workload based on the principles described in the Microsoft Azure Well-Architected Framework, complete the IoT workload questionnaires in the [Azure Well-Architected Review assessment](/assessments). After you complete the assessment, this guide helps you address the key reliability recommendations identified for your solution.

## Reliability in IoT architecture layers

To design for reliability in an IoT solution, consider the foundational layers of your IoT architecture. To achieve overall solution reliability, each layer should have acceptable levels of reliability.

:::image type="content" source="media/architecture-layers.svg" alt-text="Diagram that shows the layers and cross-cutting activities in an IoT architecture." border="false":::

### Device and gateway layer

Devices and gateways come in many forms. As part of your overall IoT solution, design your devices to satisfy the uptime and availability requirements of your end-to-end solution. Typically, devices and gateways perform the following functions:

- Data collection
- Supervisory control
- Edge analytics

Data collection means the device is connected to sensors or subscribes to telemetry from downstream systems and then pushes this data to the cloud. The solution design should ensure reliable device management and reliable communications from the device to the cloud.

Devices that provide supervisory control not only collect data to send to the cloud, but also take actions based in that data. Actions send data back to the machines or environment that the device is authorized to perform supervisory actions on. The reliability of the application running on a device that has supervisory control is crucial.

#### Device design

Design and select IoT devices to function reliably in the expected operating conditions and over their expected lifetime. A reliable device should perform according to its hardware and software specifications, and any failure should be detected and managed through mitigation, repair, or replacement. Design for reliability, but also plan for failures.

#### Device lifecycle

Limited sevice lifetimes affect solution reliability. Assess the consequences of device failure on the solution, and define a device lifecycle strategy in accordance with the solution requirements.

Device failure impact assessment includes severity (for example single points of failures), probability (for example MTBF), detectability (see FMEA practice), and an acceptable downtime period.

The acceptable operational downtime determines the speed and extent to which devices should be maintained. For a solution lifecycle, the availability or longevity of the supply of devices and parts is an important aspect. The more modular the design, the easier it is to swap out parts of the system, especially if some parts become obsolete earlier than others. Alternative or multi-sourcing of component and module supply chains are critical for reliable solutions.

#### Environmental requirements

The conditions in which a device operates affect its reliability. Define your environmental requirements, and use devices with appropriate feature specifications. These specifications include parameters such as operating temperature range, humidity, ingress protection (IP) rating, electromagnetic interference (EMI) immunity, and shock and vibration immunity.

#### Operational profile

The operational behavior of devices affects the performance stress applied to devices, and therefore their reliability. Define operational profiles (estimating device behavior throughout its lifetime), and assess device reliability accordingly. Such profiles include operation modes (for example wireless transmission on, low-power modes) and environmental condition (such as temperature) over device lifetime.  

In normal operating conditions, the device and software should run safely inside the specified performance profiles and avoid running at the limit of the device capabilities. A device should be able to service and process all external sensors and data processing required by the solution.

#### Regulations and standards

For specific industries, devices are subject to applicable regulations and standards. Regulations and standards should be defined, and devices should meet any compliance and conformity requirements. Regulations include certification and marking (for example FCC, CE). Standards include industry/application (for example ATEX and MILspec) and safety (for example IEC 61508) conformance.

### Device management and modeling layer

The cloud service provides each device with an identity and manages devices at scale. As described in the [Azure WAF – Reliability pillar](/azure/architecture/framework/resiliency/principles), the cloud services in your IoT solution must implement reliability principles to provide high availability for your overall IoT solution. The cloud is often the final data ingress point for all messages flowing from the devices. IoT solutions must provide reliability for the cloud services required for the IoT devices to integrate and transmit data.

Device connectivity conditions, including upstream to the cloud and downstream local network, should be part of the solution design and reliability. Assess the potential effect of connectivity interruption or interference, and define a connectivity strategy accordingly. The connectivity strategy should include robustness, for example fallback capability and disconnection management, and include buffering backup to mitigate cloud dependency for critical/safety functions.

The following practices focus on IoT connectivity to the cloud, error handling, and monitoring:

#### Design for connectivity

An IoT solution should enable the flow of information between intermittently connected devices and the cloud-based solutions. Ensure that your IoT device can operate efficiently with intermittent connectivity to the cloud. Best practices include:

- Implement retry and backoff logic in device software.
- Synchronize device state with the cloud.
- Ensure that devices can store data on the device if your solution can't tolerate data loss.
- Use data sampling and simulations to baseline the network capacity and storage requirements.

#### Implement connectivity

The Azure IoT device SDKs provide a set of client libraries that you can use on devices or gateways to simplify connectivity with Azure IoT services. You can use the SDKs to implement an IoT device client that:

- Connects to the cloud.
- Provides a consistent client development experience across different platforms.
- Simplifies common connectivity tasks by abstracting details of the underlying protocols and message processing patterns, such as exponential backoff with jitter and retry logic.

#### Monitor connectivity

Connectivity issues for IoT devices can be difficult to troubleshoot because of the many possible points of failure. Application logic, physical networks, protocols, hardware, Azure IoT Hub, and other cloud services can all have problems. The ability to detect and pinpoint the source of an issue is critical. However, an IoT solution at scale could have thousands of devices, so it's not practical to manually check individual devices. Azure Monitor and Event Grid are tools that can help you to diagnose connectivity issues in IoT Hub.

#### Connectivity resources

- [Manage connectivity and reliable messaging by using Azure IoT Hub device SDKs](/azure/iot-hub/iot-hub-reliability-features-in-sdks)
- [Monitor, diagnose, and troubleshoot Azure IoT Hub disconnects](/azure/iot-hub/iot-hub-troubleshoot-connectivity)
- [Retry general guidance - Best practices for cloud applications](/azure/architecture/best-practices/transient-faults)
- [Error handling for resilient apps - Azure Architecture Center](/azure/architecture/framework/resiliency/app-design-error-handling)
- [Circuit Breaker pattern - Cloud Design Patterns](/azure/architecture/patterns/circuit-breaker)
- [Compensating Transaction pattern - Cloud Design Patterns](/azure/architecture/patterns/compensating-transaction)
- [Throttling pattern - Cloud Design Patterns](/azure/architecture/patterns/throttling)

### Ingestion and communication layer

This layer handles service quotas and limits, capacity, throttling, and autoscale.

#### Design for capacity

- Plan for service quotas

  As with all platform services, IoT Hub and DPS have quotas and throttles enforced on certain operations, enabling Azure to deliver predictable service levels and cost for its services. Quotas and throttles are tied to the service tier and number of units you deploy so that you can design your solution with the right number of resources. Review quotas and throttles in advance and design your IoT Hub and DPS resources accordingly.

- Plan redundant capacity

  When you're planning your thresholds and alerts, consider the latency between the detection and the action taken so that the system and operators have enough time to respond to change requests. Otherwise, you may detect a need to increase the number of units, but the system fails - for example by losing messages - before the increase takes effect.

- Establish benchmarks at production scale

  Due to the distributed nature of IoT solutions, the number of devices, and the volume of data, it's important to establish scale benchmarks for the overall solution. As the number of devices or volumes of data increase, the cloud gateway must scale to support uninterrupted data flow. These benchmarks help to plan for capacity risks. Use the [IoT device telemetry simulator](/samples/azure-samples/iot-telemetry-simulator/azure-iot-device-telemetry-simulator/) to simulate production scale volumes.

- Autoscale to adjust to quotas dynamically

  A benefit of using PaaS services is the ability to scale up and down with a little effort according to your needs. To provide the lowest cost and operational effort, consider implementing an automated system to scale your resources up and down with the varying needs of your solution.

- Monitor quotas and throttling

  To ensure solution reliability, you need to continuously monitor resource usage against quotas and throttles to detect increases in resource usage that indicate the need to scale. Depending on your business requirements, you may implement something as simple as continuously monitoring resource usage and alerting the operator when thresholds are met, or an automated system to autoscale.

#### Capacity and scaling resources

- [Understand Azure IoT Hub quotas and throttling](/azure/iot-hub/iot-hub-devguide-quotas-throttling)
- [Overview of the Microsoft Azure IoT Hub Device Provisioning Service](/azure/iot-dps/about-iot-dps#quotas-and-limits)
- [Auto-scale your Azure IoT Hub - Code Samples](/samples/azure-samples/iot-hub-dotnet-autoscale/iot-hub-dotnet-autoscale)
- [Azure IoT Device Telemetry Simulator - Code Samples](/samples/azure-samples/iot-telemetry-simulator/azure-iot-device-telemetry-simulator)
- [Understanding the IP address of your IoT hub](/azure/iot-hub/iot-hub-understand-ip-address)
- [Device configuration best practices for Azure IoT Hub](/azure/iot-hub/iot-hub-configuration-best-practices)
- [Azure IoT Hub scaling](/azure/iot-hub/iot-hub-scaling)
- [Azure IoT Central quotas and limits](/azure/iot-central/core/concepts-quotas-limits)
- [Azure IoT Hub scaling](/azure/iot-hub/iot-hub-scaling)
- [Operational Throttles \| Understand Azure IoT Hub quotas and throttling](/azure/iot-hub/iot-hub-devguide-quotas-throttling#operation-throttles)
- [Other Limits | Understand Azure IoT Hub quotas and throttling](/azure/iot-hub/iot-hub-devguide-quotas-throttling#other-limits)

### Transport layer

To connect to the cloud service for data, control, and management, devices need access to a network. Depending on the type of IoT solution, connectivity reliability is in your hands or the hands of the network service provider. Networks may have intermittent connectivity issues and in this case the device needs to manage its behavior accordingly.

## DevOps layer

An enterprise IoT solution should provide a strategy for how operators manage the system. The cross-cutting DevOps management and operations layer covers updates, observability and monitoring, and HA/DR implementation.

### Updates

The device side of IoT solutions presents new challenges compared to cloud-based solutions. For example, devices need to be continually updated for vulnerabilities and application changes. Due to the distributed nature of IoT solutions, it's important to adopt safe and secure policies for deploying updates. IoT operators require simple and reliable tools and practices that enable safe and reliable device updates.

A device update solution must support:

- Gradual update rollout through device grouping and scheduling controls.
- Support for resilient A/B device updates for seamless rollback.
- Detailed update management and reporting tools.
- Network optimization based on available bandwidth.

[Device Update for IoT Hub](/azure/iot-hub-device-update) is a service that enables safe, secure, and reliable over-the-air (OTA) IoT device updates. Device Update for IoT Hub can group devices and specify which devices should receive an update. Operators can view the status of update deployments and make sure each device successfully applies required updates.

When an update fails, Device Update for IoT Hub lets operators identify the devices that failed to apply the update and see failure details. The ability to identify which devices failed to update results in fewer manual hours trying to pinpoint the source of a failure.

Operators need to monitor the state of device deployments and updates. In Device Update for IoT Hub, compliance measures how many devices have installed the highest version compatible update. A device is compliant if it has installed the highest version available update that is compatible for it.

### Observability and monitoring

Monitor every component of your IoT solution and define alerts and procedures to manage overall reliability. All Azure IoT services publish metrics that describe the health and availability of the service. Also consider the metrics that you need on the device side to establish end-to-end observability for reliability. Use these metrics as part of your overall solution reliability monitoring.

IoT application monitoring and diagnostics are crucial for availability and resiliency. If something fails, you need to know that it failed, when it failed, and why.By monitoring the operation of an IoT application and devices relative to a healthy state, you can detect and fix reliability issues.

Before you can mitigate issues that affect IoT application reliability, you must be able to capture logs and signals that help you detect issues in the end-to-end operation of the solution. Use IoT solution logging and monitoring systems to determine whether the solution is functioning as expected and to help troubleshoot issues with its components.

The following actions establish observability on an IoT solution:

- Establish a mechanism to collect and analyze performance metrics and alerts.
- Configure devices, cloud services, and applications to collect and connect with Azure Monitor.
- Use a real-time dashboard and alerts to monitor Azure backend services.
- Define roles and responsibilities to monitor and act on events and alerts.
- Implement continuous monitoring.

#### Azure Monitor

[Azure Monitor](/azure/azure-monitor/essentials/data-platform-metrics) is the recommended monitoring and visualization solution for Azure IoT solutions. Devices, services, and applications, regardless of deployment location, can push log messages directly or through built-in connectors into Azure Monitor.

Configure devices, cloud services, and applications to collect and connect to Azure Monitor. Azure Monitor provides custom log parsing to facilitate the decomposition of events and records into individual fields for indexing and search.

Enable remote monitoring of IoT Edge devices using [Azure Monitor and built-in metrics integration](/azure/iot-edge/how-to-collect-and-transport-metrics). To enable this capability on your devices, add the metrics collector module to your deployment and configure it to collect and transport module metrics to Azure Monitor.

If your solution uses Azure IoT Central, you can use the metrics IoT Central provides to assess the health of connected devices and the health of your active data exports. IoT Central applications enable metrics by default, and you access them from the Azure portal. The Azure Monitor data platform also exposes and provides several ways to interact with these metrics.

[IoT Hub provides usage metrics](/azure/iot-hub/monitor-iot-hub), such as the number of messages used and the number of devices connected. You can monitor data generated by Azure IoT Hub and relay it to Azure Monitor for analysis and to alert other services.

If the IoT solution requires a custom application such as Azure App Service, Azure Kubernetes Service, or Azure Functions, use [Application Insights](/azure/azure-monitor/app/cloudservices) for monitoring and analysis. Application Insights is a feature of Azure Monitor that provides extensible application performance management and monitoring for live web apps. Use Application Insights to:

- Automatically detect performance anomalies.
- Help diagnose issues by using powerful analytics tools.
- See what users actually do with apps.
- Help continuously improve app performance and usability.

Enable a real-time dashboard and alerts to monitor the Azure backend services. Azure Monitor alerts proactively notify you about specific conditions in your monitoring data. Alerts let you identify and address issues in your system before your customers encounter them. You can set alerts on metrics, logs, and the activity log.

Define roles and responsibilities to monitor and act on events and alerts. For more information, see [Roles, responsibilities, and permissions - Microsoft Azure Well-Architected Framework](/azure/architecture/framework/security/design-identity-role-definitions).

#### Continuous monitoring

*Continuous integration and continuous deployment (CI/CD)* is a DevOps concept that helps you deliver software more quickly and reliably and provide continuous value to your users. *Continuous monitoring (CM)* is a follow-up concept that lets you incorporate monitoring across each phase of your DevOps cycles. CM continuously ensures the health, performance, and reliability of your apps and infrastructure as they flow through development, production, and release to customers. For more information, see:

- [Seven best practices for continuous monitoring with Azure Monitor](https://azure.microsoft.com/blog/7-best-practices-for-continuous-monitoring-with-azure-monitor)
- [Continuous integration and continuous deployment to Azure IoT Edge devices](/azure/iot-edge/how-to-continuous-integration-continuous-deployment-classic)

#### Monitoring resources

- [Monitoring Azure IoT Hub](/azure/iot-hub/monitor-iot-hub)
- [Monitoring Azure IoT Hub data reference](/azure/iot-hub/monitor-iot-hub-reference)
- [Trace Azure IoT device-to-cloud messages with distributed tracing](/azure/iot-hub/iot-hub-distributed-tracing)
- [Check Azure IoT Hub service and resource health](/azure/iot-hub/iot-hub-azure-service-health-integration)
- [Collect and transport metrics - Azure IoT Edge](/azure/iot-edge/how-to-collect-and-transport-metrics)
- [Monitoring data reference](/azure/iot-hub/monitor-iot-hub-reference)
- [Enable message tracking](/azure/iot-hub/iot-hub-distributed-tracing)
- [Check Azure IoT Hub service and resource health](/azure/iot-hub/iot-hub-azure-service-health-integration)

### Implement HA/DR in critical components

As you build out your IoT solution, your SLA guides you to implement HA/DR in critical components. You must design to meet the SLA for failure recovery across the IoT solution stack. There are multiple approaches, from redundancy across the IoT solution stack to redundancy for specific layers. Cost is also a major consideration to weigh against the benefits of meeting SLAs.

Azure IoT services have defined uptime and availability targets. Review the SLAs for Azure IoT services that are part of your solution, and define your uptime goals. For example, Azure IoT Hub has an SLA of 99.9%, which translates into 1 minute and 36 seconds of potential downtime per day to plan for. The Azure IoT SDK provides built-in, configurable logic to handle retries and backoff.

Consider breaking the uptime goals of your solution into two categories: Device management and operational, for data ingestion. For example, if a device is successfully sending data to an IoT hub, but the device management services are unavailable, how does that affect your solution? For more information, see [Azure IoT Hub SDK reliability features](/azure/iot-hub/iot-hub-reliability-features-in-sdks).

Consider using redundant hardware options for sensors, power, and storage. Redundant hardware enables devices to function if a critical feature isn't available. Hardware also needs to account for connectivity issues. For example, use a store and forward approach for data when connectivity isn't available. Azure IoT Edge has this feature built in.

Your device must also be able to handle cloud outages. Azure region pairing provides an HA/DR strategy for IoT Hub that meets many SLA requirements. If region pairing isn't enough, consider implementating a secondary IoT Hub. You can also use Azure Device Provisioning Service (DPS) to avoid hardcoded IoT Hub configurations on your devices. If your primary IoT hub goes down, DPS can assign your devices to a different hub.

For devices that are expected to be online most of the time, consider implementing a heartbeat message pattern. This pattern uses a custom IoT Hub route that's processed by Azure Stream Analytics, Azure Logic Apps, or Azure Functions to determine if a heartbeat has failed. You can use the heartbeat to define Azure Monitor alerts that take actions as needed.

Azure Monitor lets you monitor the state of your IoT Hub environment, ensure it's running properly, and verify that your devices aren't being throttled or experiencing connection issues.

#### HA/DR resources

- [IoT Hub SDK reliability features](/azure/iot-hub/iot-hub-reliability-features-in-sdks)
- [IoT Hub HA/DR](/azure/iot-hub/iot-hub-ha-dr)
- [Apply IoT Hub Intra-region HA/DR](/azure/iot-hub/iot-hub-ha-dr#intra-region-ha)
- [Apply IoT Hub Cross-region HA/DR](/azure/iot-hub/iot-hub-ha-dr#cross-region-dr)
- [How to clone an Azure IoT hub](/azure/iot-hub/iot-hub-how-to-clone)
- [Test apps for availability and resiliency - Azure Architecture Center](/azure/architecture/framework/resiliency/testing)

## Next steps

> [!div class="nextstepaction"]
> [Security in your IoT workload](iot-security.md)
