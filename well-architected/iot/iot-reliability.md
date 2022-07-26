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

IoT workloads, like all workloads, have the potential to malfunction. The key reliability considerations for IoT workloads are how quickly you can detect changes and how quickly you can resume operations after an outage.

IoT applications are often distributed at massive scale, and might operate over unreliable networks without persistent access or visibility into end-to-end data flows. Because of these qualities, you should design your IoT architecture with availability and resiliency in mind.

Building a reliable IoT solution requires careful consideration of devices, cloud services, and how they interact. The choices you make for device hardware, connectivity and protocols, and cloud services affect your solution's reliability requirements and capabilities.

A resilient IoT environment should include the following characteristics:

- Resilient workload-specific architectures that build in redundancy.
- Cross-region redundancies.
- Service levels indicators (SLIs) and service-level objectives (SLOs) defined around observability.
- Architecture modifications to meet service-level agreements (SLAs).
- Integrated auditing, monitoring, and alerting beyond what cloud services natively provide.
- Critical maintenance and support pathways.

Follow these principles as you design your IoT solution for reliability:

- Design devices for resiliency.
- Establish safe practices for updates.
- Establish observability across your IoT solution.
- Implement high availability and disaster recovery (HA/DR) for critical components.
- Plan for capacity.

## Well-architected IoT workload reliability

As described in the Azure Well-Architected Framework [Reliability design principles](/azure/architecture/framework/resiliency/principles), reliability is a subjective concept. The reliability of an application must reflect its business requirements. Make sure you understand the business requirements for your solution.

To assess your IoT workload against the Well-Architected Framework Reliability pillar, complete the IoT workload Reliability questions in the [Azure Well-Architected Review assessment](/assessments/?mode=pre-assessment&id=azure-architecture-review). After the assessment identifies key reliability recommendations for your solution, use the current article to help address the recommendations.

## Reliability in IoT architecture layers

To design for reliability in an IoT solution, consider the foundational layers of your IoT architecture. To achieve overall solution reliability, each layer should have acceptable levels of reliability.

:::image type="content" source="media/architecture-layers.svg" alt-text="Diagram that shows the layers and cross-cutting activities in an IoT architecture." border="false":::

### Device and gateway layer

As part of your overall IoT solution, design your devices to satisfy the uptime and availability requirements of your end-to-end solution. Devices and gateways come in many forms. Typically, IoT devices and gateways do data collection, supervisory control, and edge analytics.

Data collection means connecting the device to sensors or subscribing it to telemetry from downstream systems, which the device then pushes to the cloud. The solution design should ensure reliable device management and reliable communications from the device to the cloud.

Devices that provide supervisory control not only collect data to send to the cloud, but also take actions based in that data. Actions send data back to the machines or environment that the device can take supervisory actions on. The reliability of the application running on a supervisory control device is crucial.

#### Device design

Design and select IoT devices to function reliably in the expected operating conditions over their expected lifetimes. A reliable device should perform according to its hardware and software specifications, and any failure should be detected and managed through mitigation, repair, or replacement. Design for reliability, but also plan for failures.

#### Device lifecycle

Limited service lifetimes affect solution reliability. Assess the consequences of device failure on the solution, and define a device lifecycle strategy according to solution requirements.

Device failure impact assessment includes:

- Severity, such as single points of failures (SPOFs).
- Probability, such as mean time between failures (MTBF).
- Detectability, such as failure mode and effects analysis (FMEA).
- Acceptable downtime period.

The acceptable operational downtime determines the speed and extent of device maintenance. For a solution lifecycle, the availability or longevity of the device and part supply is an important consideration.

The more modular the design, the easier it is to swap out parts of the system, especially if some parts become obsolete earlier than others. Alternative or multi-sourcing of component and module supply chains are critical for reliable solutions.

#### Environmental requirements

The conditions in which a device operates affect its reliability. Define your environmental requirements, and use devices with appropriate feature specifications. These specifications include parameters such as operating temperature range, humidity, ingress protection (IP) rating, electromagnetic interference (EMI) immunity, and shock and vibration immunity.

#### Operational profile

The operational behavior of devices affects the performance stress applied to devices, and therefore their reliability. Define operational profiles that estimate behavior over device lifetime, and assess device reliability accordingly. Such profiles include operation modes, such as wireless transmission or low-power modes, and environmental conditions, such as temperature, over the device lifetime.

In normal operating conditions, the device and software should run safely within the specified performance profiles, and avoid running at the limit of the device capabilities. A device should be able to service and process all external sensors and data processing that a solution requires.

#### Regulations and standards

Devices for specific industries are subject to applicable regulations and standards. Define any regulations and standards, and make sure devices meet any compliance and conformity requirements. Regulations include certification and marking, such as FCC or CE. Standards include industry or agency applications, such as ATEX and MIL-SPEC, and safety conformance, for example IEC 61508.

### Device management and modeling layer

Cloud services provide each device with an identity, and manage devices at scale. As described in the [Reliability design principles](/azure/architecture/framework/resiliency/principles), the cloud services in your IoT solution must implement reliability principles to provide high availability for your overall IoT solution.

The cloud is often the final data ingress point for all messages flowing from the devices. In IoT solutions, the cloud services must provide reliability for the IoT devices to integrate and transmit data.

Device connectivity conditions, including upstream to the cloud and downstream to local networks, should be part of IoT solution reliability design. Assess the potential effect of connectivity interruption or interference, and define a connectivity strategy accordingly. The connectivity strategy should include robustness, for example fallback capability and disconnection management, and include buffering backup to mitigate cloud dependency for critical or safety functions.

The following practices focus on IoT solution design, error handling, and monitoring related to connectivity:

#### Design for connectivity

An IoT solution should enable the flow of information between intermittently connected devices and cloud-based services. Make sure your IoT devices can operate efficiently with intermittent connectivity to the cloud.

Best practices include the following recommendations:

- Implement retry and backoff logic in device software.
- Synchronize device state with the cloud.
- Make sure you can store data on devices if your solution can't tolerate data loss.
- Use data sampling and simulations to measure network capacity and storage requirement baselines.

#### Implement connectivity

The Azure IoT device SDKs provide client libraries that you can use on devices or gateways to simplify connectivity with Azure IoT services. You can use the SDKs to implement IoT device clients that:

- Connect to the cloud.
- Provide a consistent client development experience across different platforms.
- Simplify common connectivity tasks by abstracting details of the underlying protocols and message processing patterns, such as exponential backoff with jitter and retry logic.

#### Monitor connectivity

Connectivity issues for IoT devices can be difficult to troubleshoot because of the many possible points of failure. Application logic, physical networks, protocols, hardware, Azure IoT Hub, and other cloud services can all have problems.

The ability to detect and pinpoint the source of an issue is critical. However, an IoT solution at scale could have thousands of devices, so it's not practical to manually check individual devices. Azure Monitor and Azure Event Grid can help you diagnose connectivity issues in IoT Hub.

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

#### Design for redundant capacity

When planning thresholds and alerts, consider the latency between detection and action taken, so the system and operators have enough time to respond to change requests. Otherwise, for example, you might detect a need to increase the number of units, but the system fails by losing messages before the increase can take effect.

#### Plan for service quotas

As with all platform services, IoT Hub and the IoT Hub Device Provisioning Service (DPS) enforce quotas and throttles on certain operations, so Azure can deliver predictable service levels and costs for its services. Quotas and throttles are tied to the service tier and number of units you deploy, so you can design your solution with the right number of resources. Review quotas and throttles in advance, and design your IoT Hub and DPS resources accordingly.

#### Establish benchmarks at production scale

As the number of devices or volume of data increase, the cloud gateway must scale to support uninterrupted data flow. Due to the distributed nature of IoT solutions, the number of devices, and the volume of data, it's important to establish scale benchmarks for the overall solution. These benchmarks help to plan for capacity risks. Use the [Azure IoT Device Telemetry Simulator](/samples/azure-samples/iot-telemetry-simulator/azure-iot-device-telemetry-simulator) to simulate production scale volumes.

#### Autoscale to adjust to quotas dynamically

A benefit of using platform-as-a-service (PaaS) components is the ability to scale up and down according to your needs with little effort. To provide the lowest cost and operational effort, consider implementing an automated system to scale your resources up and down with the varying needs of your solution.

#### Monitor quotas and throttling

To ensure solution reliability, continuously monitor resource usage against quotas and throttles to detect usage increases that indicate the need to scale. Depending on your business requirements, you might continuously monitor resource usage and alert the operator when thresholds are met, or implement an automated system to autoscale.

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

To connect to the cloud service for data, control, and management, devices need access to a network. Depending on the type of IoT solution, connectivity reliability is your responsibility or that of the network service provider. Networks might have intermittent connectivity issues, and devices need to manage their behavior accordingly.

## DevOps processes

An enterprise IoT solution should provide a strategy for operators to manage the system. For reliability, the DevOps management and operations layer covers updates, observability and monitoring, and HA/DR implementation.

### Updates

The device side of IoT solutions presents new challenges compared to cloud-based solutions. For example, devices need to be continually updated for vulnerabilities and application changes. Due to the distributed nature of IoT solutions, it's important to adopt safe and secure policies for deploying updates. IoT operators require simple and reliable tools and practices that enable safe and reliable device updates.

A device update solution must support:

- Gradual update rollout through device grouping and scheduling controls.
- Support for resilient A/B device updates for seamless rollback.
- Detailed update management and reporting tools.
- Network optimization based on available bandwidth.

[Device Update for IoT Hub](/azure/iot-hub-device-update) is a service that enables safe, secure, and reliable over-the-air (OTA) IoT device updates. Device Update for IoT Hub can group devices and specify which devices should receive an update. Operators can view the status of update deployments and make sure each device successfully applies the required updates.

When an update fails, Device Update for IoT Hub helps operators identify the devices that failed to apply the update and see the failure details. The ability to identify which devices failed results in fewer hours trying to manually pinpoint the source of a failure.

Operators need to monitor the state of device deployments and updates. Device Update for IoT Hub compliance reports how many devices have installed the highest version compatible update. A device is compliant if it has installed the highest version compatible available update.

### Observability and monitoring

To manage overall reliability, monitor every component of your IoT solution, and define alerts and procedures. All Azure IoT services publish metrics that describe service health and availability. Also consider the metrics that you need on the device side to establish end-to-end observability. Use these metrics as part of your overall solution reliability monitoring.

IoT application monitoring and diagnostics are crucial for availability and resiliency. If something fails, you need to know that it failed, when it failed, and why. By monitoring the operation of an IoT application and devices relative to a healthy state, you can detect and fix reliability issues.

Before you can mitigate issues that affect IoT application reliability, you must be able to capture logs and signals that help you detect issues in end-to-end solution operation. Use IoT solution logging and monitoring systems to determine whether the solution is functioning as expected and to help troubleshoot issues with solution components.

The following actions establish observability for an IoT solution:

- Establish a mechanism to collect and analyze performance metrics and alerts.
- Configure devices, cloud services, and applications to collect and connect with Azure Monitor.
- Use a real-time dashboard and alerts to monitor Azure backend services.
- Define roles and responsibilities to monitor and act on events and alerts.
- Implement continuous monitoring.

#### Azure Monitor

[Azure Monitor](/azure/azure-monitor/essentials/data-platform-metrics) is the recommended monitoring and visualization solution for Azure IoT solutions. Devices, services, and applications, regardless of deployment location, can push log messages directly or through built-in connectors into Azure Monitor.

Configure devices, cloud services, and applications to collect and connect to Azure Monitor. Azure Monitor provides custom log parsing to facilitate the decomposition of events and records into individual fields for indexing and search.

Enable remote monitoring of IoT Edge devices using Azure Monitor [built-in metrics integration](/azure/iot-edge/how-to-collect-and-transport-metrics). To enable this capability on your devices, add the IoT Edge Metrics Collector module to your deployment and configure it to collect and transport module metrics to Azure Monitor.

If your solution uses Azure IoT Central, you can use the metrics IoT Central provides to assess the health of connected devices and active data exports. IoT Central applications enable metrics by default, which you can access from the Azure portal. The Azure Monitor data platform also exposes and provides several ways to interact with these metrics.

IoT Hub provides [usage metrics](/azure/iot-hub/monitor-iot-hub), such as the number of messages used and the number of devices connected. You can monitor data generated by Azure IoT Hub and relay it to Azure Monitor for analysis and to alert other services.

If the IoT solution requires a custom application such as Azure App Service, Azure Kubernetes Service, or Azure Functions, use [Application Insights](/azure/azure-monitor/app/cloudservices) for monitoring and analysis. Application Insights is a feature of Azure Monitor that provides extensible application performance management and monitoring for live web apps. Use Application Insights to:

- Automatically detect performance anomalies.
- Help diagnose issues by using powerful analytics tools.
- See what users actually do with apps.
- Help continuously improve app performance and usability.

Enable a real-time dashboard and alerts to monitor Azure backend services. Azure Monitor alerts proactively notify you about specific conditions in your monitoring data, and let you identify and address issues before your customers encounter them. You can set alerts on metrics, logs, and the activity log.

Define roles and responsibilities to monitor and act on events and alerts. For more information, see the Azure Well-Architected Framework [Roles, responsibilities, and permissions](/azure/architecture/framework/security/design-identity-role-definitions).

#### Continuous monitoring

*Continuous integration and continuous deployment (CI/CD)* is a DevOps concept that helps you deliver software more quickly and reliably and provide continuous value to your users. *Continuous monitoring (CM)* is a follow-up concept that lets you incorporate monitoring across each phase of your DevOps cycle.

CM continuously ensures the health, performance, and reliability of your apps and infrastructure as they flow through development, production, and release to customers. For more information, see:

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

As you design and build your IoT solution, you must meet the SLA for failure recovery across the IoT solution stack. Your SLA guides you to implement HA/DR in critical components. There are multiple approaches, from redundancy across the IoT solution stack to redundancy for specific layers. Cost is also a major consideration to weigh against the benefits of meeting SLAs.

Azure IoT services have defined uptime and availability targets. Review the SLAs for Azure IoT services that are part of your solution, and define your uptime goals. For example, Azure IoT Hub has an SLA of 99.9%, which translates into 1 minute and 36 seconds of potential downtime per day to plan for. The Azure IoT SDK provides built-in, configurable logic to handle retries and backoff.

Consider breaking the uptime goals of your solution into two categories: Device management and operational data ingestion. For example, if a device is successfully sending data to an IoT hub, but the device management services are unavailable, how does that affect your solution? For more information, see the [Azure IoT Hub SDK reliability features](/azure/iot-hub/iot-hub-reliability-features-in-sdks).

Consider using redundant hardware options for sensors, power, and storage. Redundant hardware enables devices to function if a critical feature isn't available. Hardware also needs to account for connectivity issues. For example, use a store and forward approach for data when connectivity isn't available. Azure IoT Edge has this feature built in.

Your device must also be able to handle cloud outages. Azure region pairing provides an HA/DR strategy for IoT Hub that meets many SLA requirements. If region pairing isn't enough, consider implementing a secondary IoT hub. You can also use DPS to avoid hardcoded IoT Hub configurations on your devices. If your primary IoT hub goes down, DPS can assign your devices to a different hub.

Consider implementing a heartbeat message pattern for devices that are expected to be online most of the time. This pattern uses a custom IoT Hub route that's processed by Azure Stream Analytics, Azure Logic Apps, or Azure Functions to determine if a heartbeat has failed. You can use the heartbeat to define Azure Monitor alerts that take actions as needed.

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
