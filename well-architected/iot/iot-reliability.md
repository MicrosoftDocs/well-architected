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

# Well-Architected Framework for IoT - Reliability

Designing for reliability in an IoT solution should take into consideration the [foundational layers in the IoT architecture](iot-overview.md). To achieve overall solution reliability, each layer should have acceptable levels of reliability.

**Device and gateway layer**: Devices and gateways come in many forms and shape. Typically, devices and gateways perform the following functions:

- Data collection
- Supervisory control

Data collection means the device is connected to sensors or subscribes to telemetry from downstream systems and then pushes this data to the cloud. The solution design should ensure reliable device management and reliable communications from the device to the cloud.

Devices that provide supervisory control not only collect data to send to the cloud, but also take actions based in that data. Actions send data back to the machines or environment that the device is authorized to perform supervisory actions on. The reliability of the application running on a device that has supervisory control is crucial.

**Transport layer**: To connect to the cloud service for data, control and management, devices need access to a network. Depending on the type of IoT solution, connectivity reliability may be in your hands or the hands of the network service provider. Networks may have intermittent connectivity issues and in this case the device needs to manage its behavior accordingly.

**Device management and modeling layer**: The cloud service provides the device with an identity and manages the devices at scale. The cloud is often the final data ingress point for all messages flowing from the devices. IoT solutions must provide reliability for the cloud services required for the IoT devices to integrate and transmit data. As described in the [Azure WAF – Reliability pillar](/azure/architecture/framework/resiliency/principles), the cloud services used in your IoT solution must implement reliability principles to provide high availability for your overall IoT solution.

Building a reliable IoT solution requires careful consideration for devices, cloud, and how they interact together. The choices made for device hardware, connectivity and protocols, and cloud service selection affect the reliability needs of your solution.

As described in the Azure WAF – Reliability pillar, desired reliability is subjective. Ensure you understand the business requirements for your solution.

## Prerequisites

To assess your IoT workload using the tenets in the Microsoft Azure Well-Architected Framework, complete the assessment.
<!-- TODO: add link to assessment -->

After you complete the assessment, this guide helps you address the key areas that surfaced for your solution.

## Principles

The following principles should be followed as you design your IoT solution for reliability:

- Design devices for resiliency
- Establish safe practices for updates
- Establish observability across your IoT solution
- Implement HA/DR in critical components
- Plan for capacity
- Data and reliability

:::image type="content" source="media/architecture-layers.svg" alt-text="Diagram that shows the layers and cross-cutting activities in the IoT architecture." border="false":::

## Design devices for resiliency

As part of your overall IoT solution, design your devices to satisfy the uptime and availability requirements of your end-to-end solution. The following practices focus on connectivity to the cloud service, error handling, and monitoring:

### Design

Ensure that your IoT device can operate efficiently with intermittent connectivity to the cloud. An IoT solution should enable the flow of information between intermittently connected devices and the cloud-based solutions. Best practices include:

- Implement retry mechanisms in device software.
- Synchronize device state with the cloud.
- Devices have the capability to store data on the device if data loss can’t be tolerated.
- Use data sampling to reflect the network capacity and storage requirements.

### Implement

The Azure IoT device SDKs provide a set of client libraries that you can use on devices or gateways to simplify connectivity with the Azure IoT services. You can use the SDKs to implement an IoT client that facilitates the connectivity to the cloud, provides a consistent client development experience across different platforms, and that simplifies common connectivity tasks by abstracting details of the underlying protocols and message processing patterns such as exponential backoff with jitter and retry logic.

### Monitor

Connectivity issues for IoT devices can be difficult to troubleshoot because there are many possible points of failure. Application logic, physical networks, protocols, hardware, IoT Hub, and other cloud services can all cause problems. The ability to detect and pinpoint the source of an issue is critical. However, an IoT solution at scale could have thousands of devices, so it's not practical to check individual devices manually. Azure Monitor and Event Grid are valuable tools that can help to diagnose connectivity issues in IoT Hub.

### Resources

- [Manage connectivity and reliable messaging by using Azure IoT Hub device SDKs](/azure/iot-hub/iot-hub-reliability-features-in-sdks)
- [Monitor, diagnose, and troubleshoot Azure IoT Hub disconnects](/azure/iot-hub/iot-hub-troubleshoot-connectivity)
- [Retry general guidance - Best practices for cloud applications](/azure/architecture/best-practices/transient-faults)
- [Error handling for resilient apps - Azure Architecture Center](/azure/architecture/framework/resiliency/app-design-error-handling)
- [Circuit Breaker pattern - Cloud Design Patterns](/azure/architecture/patterns/circuit-breaker)
- [Compensating Transaction pattern - Cloud Design Patterns](/azure/architecture/patterns/compensating-transaction)
- [Throttling pattern - Cloud Design Patterns](/azure/architecture/patterns/throttling)

## Establish safe practices for updates

An enterprise IoT solution should provide a strategy for how operators handle the ongoing management of their devices. IoT operators require simple and reliable tools and applications that enable reliable device updates.

### Design

Considering the distributed nature of IoT solutions it’s important to adopt safe and secure policies for updating and deploying applications. Unlike cloud-based solutions, the device side of IoT solutions brings new challenges. For example, devices must be continually updated for vulnerabilities and application changes.

A device update solution must support:

- Gradual update rollout through device grouping and update scheduling controls.
- Support for resilient device updates (A/B) to deliver seamless rollback.
- Detailed update management and reporting tools.
- Optimization for network based on available bandwidth.

### Implement

Device Update for IoT Hub is a service that enables safe, secure, and reliable IoT device updates over-the-air (OTA). Device Update for IoT Hub includes the ability to group devices and specify which devices should receive an update. Operators can view the status of update deployments and make sure each device successfully applies required updates.

When an update failure happens, Device Update for IoT Hub lets operators identify the devices that failed to apply the update and see failure details. The ability to identify which devices failed to update means fewer manual hours trying to pinpoint the source of a failure.

### Monitor

Operators need to monitor the state of device deployments and updates. In Device Update for IoT Hub, compliance measures how many devices have installed the highest version compatible update. A device is compliant if it has installed the highest version available update that is compatible for it.

## Establish observability across your IoT solution

Monitor every component of your IoT Solution and define alerts and procedures to manage the overall reliability. All Azure IoT services publish metrics that describe the health and availability of the service. In addition to the cloud service metrics, you should also consider the metrics that need to be established on the device side to establish end-to-end observability for reliability. Consider using these metrics as part of your overall solution reliability monitoring.

By monitoring the operation of an IoT application and devices relative to a healthy state, you can detect and fix reliability issues. Monitoring and diagnostics of an IoT Application are crucial for availability and resiliency. If something fails, you need to know that it failed, when it failed, and why it failed:

### Design

Before you mitigate issues that affect the reliability of an IoT application, you must be able to capture logs and signals that help you detect issues in the end-to-end operation of the solution.

Use IoT solution logging and monitoring systems to determine whether the solution is functioning as expected and to help troubleshoot what is wrong with its components.

Complete the following actions to establish observability on an IoT solution:

- Establish a mechanism to collect and analyze performance metrics and alerts related to your IoT solution.
- Configure devices, cloud services, and applications to collect and connect with Azure Monitor.
- Enable a real-time dashboard and alerts to monitor the Azure backend services.
- Define roles and responsibilities to monitor and act on events and alerts.
- Implement continuous monitoring.

### Implement

**Establish a mechanism to collect and analyze performance metrics and alerts related to the IoT solution.**

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

**Continuous Monitoring**

Continuous Integration and Continuous Deployment (CI/CD) is a DevOps concept that can help you deliver software faster and more reliably to provide continuous value to your users. Continuous Monitoring (CM) is a new follow-up concept where you can incorporate monitoring across each phase of your DevOps and IT Ops cycles. This ensures the health, performance, and reliability of your apps and infrastructure continuously as it flows through the developer, production, and customers.

[Seven best practices for Continuous Monitoring with Azure Monitor](https://azure.microsoft.com/blog/7-best-practices-for-continuous-monitoring-with-azure-monitor/)

[Continuous integration and continuous deployment to Azure IoT Edge devices](/azure/iot-edge/how-to-continuous-integration-continuous-deployment-classic)

### Resources

- [Monitoring Azure IoT Hub](/azure/iot-hub/monitor-iot-hub)
- [Monitoring Azure IoT Hub data reference](/azure/iot-hub/monitor-iot-hub-reference)
- [Add correlation IDs to IoT messages w/distributed tracing (pre)](/azure/iot-hub/iot-hub-distributed-tracing)
- [Check Azure IoT Hub service and resource health](/azure/iot-hub/iot-hub-azure-service-health-integration)
- [Collect and transport metrics - Azure IoT Edge](/azure/iot-edge/how-to-collect-and-transport-metrics)
- [Monitoring data reference](/azure/iot-hub/monitor-iot-hub-reference)
- [Enable message tracking](/azure/iot-hub/iot-hub-distributed-tracing)
- [Check Azure IoT Hub service and resource health](/azure/iot-hub/iot-hub-azure-service-health-integration)

## Implement HA/DR in critical components

### Design

As you build out your IoT solution, your SLA guides you into what you need to implement in your critical components for HA/DR. You must design to meet the SLA to recover from failures across the IoT solution stack. There are multiple approaches, starting with redundancy across the IoT solution stack to enabling redundancy for specific layers of the solution stack. Cost is also a major consideration that needs to be weighed against the benefits of meeting the SLAs.

- **Define uptime goals for your IoT solution**: Azure IoT services have defined uptime and availability targets. Review the SLAs for Azure IoT services that are part of your solution and define your uptime goals. For example, Azure IoT Hub has an SLA of 99.9%. This translates into 1 minute and 36 seconds of potential downtime per day to plan for. The Azure IoT SDK provides built-in, configurable logic to handle retries and backoff.

Consider breaking the uptime goals of your solution into two categories. Operational (data ingestion) and device management. For example, if a device is sending data to an IoT hub, but the services to manage the device are unavailable, how does that affect your solution?

To learn more, see [Azure IoT Hub SDK reliability features](/azure/iot-hub/iot-hub-reliability-features-in-sdks).

### Implement

Consider using redundant hardware options for sensors, power, and storage. This enables the device to function completely if one of the critical features isn't available. Your hardware also needs to account for issues with connectivity. For example, use a store and forward approach to data when connectivity isn't available. Azure IoT Edge has this feature built in.

Your device must also be able to handle outages in the cloud. Microsoft Azure region pairing provides an HA/DR strategy for IoT Hub. For many, this meets their SLA requirements. If this isn't enough, consider an implementation with a secondary IoT Hub enabled. You should also use Azure Device Provisioning Service (DPS) to avoid hardcoded IoT Hub configurations on your device. Should your primary IoT hub go down, DPS can assign your device to a different hub.

### Monitor

For devices that are expected to be online most of the time, consider implementing a heartbeat message pattern using a custom route in IoT Hub that’s processed by Stream Analytics, a Logic App, or an Azure Function, to determine if a heartbeat failure has occurred. You can then use the heartbeat to define Azure Monitor alerts that take actions as needed.

Use Azure Monitor as it lets you monitor the state of your IoT Hub environment to ensure it’s running properly, and that your devices aren't being throttled or experiencing connection issues.

### Resources

- [IoT Hub SDK reliability features](/azure/iot-hub/iot-hub-reliability-features-in-sdks)
- [IoT Hub HA/DR](/azure/iot-hub/iot-hub-ha-dr)
- [Apply IoT Hub Intra-region HA/DR](/azure/iot-hub/iot-hub-ha-dr#intra-region-ha)
- [Apply IoT Hub Cross-region HA/DR](/azure/iot-hub/iot-hub-ha-dr#cross-region-dr)
- [How to clone an Azure IoT hub](/azure/iot-hub/iot-hub-how-to-clone)
- [Test apps for availability and resiliency - Azure Architecture Center](/azure/architecture/framework/resiliency/testing)

## Plan for capacity

### Design

**Plan for service quotas:** As with all platform services, IoT Hub and the Device Provisioning have quotas and throttles enforced on certain operations. That enables Azure to deliver predictable service levels and cost for its services. Quotas and throttles are tied to the service tier and number of units you deploy so that you can design your solution with just the right number of resources. Review quotas and throttles in advance and design your IoT Hub and DPS resources accordingly.

**Plan redundant capacity:** When you're planning your thresholds and alerts, consider the latency between the detection and action taken so that system and operators have enough time to respond to change requests. Otherwise, you may detect a need to increase the number of units, but the system fails - for example by losing messages - before the increase takes effect.

**Establish benchmarks at production scale**: Due to the distributed nature of IoT solutions and the scale of device and data, it's important to establish scale benchmarks for the overall solution. As the number of devices or quantities of data increase, the cloud gateways must scale to support uninterrupted data flow. These benchmarks help to plan for capacity risks.

### Implement

**Auto scale to adjust to quotas dynamically:** To provide the lowest cost and operational effort, consider implementing an automated system to scale your resources up and down with the varying needs of your solution. To learn more, see the following Resources section.

### Monitor

**Monitor quotas and throttling:** A benefit of using PaaS services is the ability to scale up and down with a little effort according to your needs. To ensure solution reliability, you need to continuously monitor resource usage against quotas and throttles to detect increases in resource usage that indicate the need to scale. Depending on your business requirements, you may implement something as simple as continuously monitoring resource usage and alerting the operator when thresholds are met, or an automated system to auto scale, as described below.

### Resources

- [Understand Azure IoT Hub quotas and throttling](/azure/iot-hub/iot-hub-devguide-quotas-throttling)
- [Overview of the Microsoft Azure IoT Hub Device Provisioning Service](/azure/iot-dps/about-iot-dps#quotas-and-limits)
- [Auto-scale your Azure IoT Hub - Code Samples](/samples/azure-samples/iot-hub-dotnet-autoscale/iot-hub-dotnet-autoscale/)
- [Azure IoT Device Telemetry Simulator - Code Samples](/samples/azure-samples/iot-telemetry-simulator/azure-iot-device-telemetry-simulator/)

Plan for resilient connectivity:

[Understanding the IP address of your IoT hub](/azure/iot-hub/iot-hub-understand-ip-address)

Apply configuration best practices to provide device level resiliency and improve SLAs:

[Device configuration best practices for Azure IoT Hub](/azure/iot-hub/iot-hub-configuration-best-practices)

Choose right IoT Hub tier for your solution based on benchmarks:

- [Azure IoT Hub scaling](/azure/iot-hub/iot-hub-scaling)
- [Azure IoT Central quotas and limits](/azure/iot-central/core/concepts-quotas-limits)
- [Azure IoT Hub scaling](/azure/iot-hub/iot-hub-scaling)
- [Operational Throttles \| Understand Azure IoT Hub quotas and throttling](/azure/iot-hub/iot-hub-devguide-quotas-throttling#operation-throttles)
- [Other Limits \| Understand Azure IoT Hub quotas and throttling](/azure/iot-hub/iot-hub-devguide-quotas-throttling#other-limits)
- [Device simulation for load testing](/samples/azure-samples/iot-telemetry-simulator/azure-iot-device-telemetry-simulator/)

## Data and reliability

### Design

- Plan for storage on the device
- Implement retry patterns
- Use DTDL for data definition
- Design for data loading - size and rate limits

Ensure that the content-type of the message payload is configured and optimized for an optimal message dispatch, in compliance with packet-size limits associated with the chosen message delivery mechanism. This enables the message payload to be encapsulated and normalized for optimal transmission in a consistent message format such as JSON to better serve message transmission and routing requirements.
  
- [IoT Hub messaging guide](/azure/iot-hub/iot-hub-devguide-messaging)
- [IoT Hub quotas and limits](/azure/iot-hub/iot-hub-devguide-quotas-throttling#other-limits)
- [IoT Hub file uploads](/azure/iot-hub/iot-hub-devguide-file-upload)
- [Normalize IoT data](https://www.linkedin.com/pulse/why-normalizing-data-fundamental-iot-amit-cohen/)

**Recommendation**: Submit packets no more than 256 KB for device-to-cloud messages and 64 KB for cloud-to-device messages. To learn more, see [IoT Hub quotas and limits](/azure/iot-hub/iot-hub-devguide-quotas-throttling#other-limits). Design for optimal data transmission by implementing appropriate data normalization, and meeting IoT hub per-device file upload limits.

**Applicability**: Applies to customers using the Azure IoT Hub SDKs or management APIs to wrap and transmit messages to the IoT Hub, and/or to customers following the [file upload pattern](/azure/iot-hub/iot-hub-devguide-file-upload).

### Implement

**Use the Azure IoT SDK:** IoT devices often rely on non-continuous or unstable network connections (for example, GSM or satellite). Errors can occur when devices interact with cloud-based services because of intermittent service availability and infrastructure-level or transient faults. An application that runs on a device has to manage the mechanisms for connection, reconnection, and the retry logic for sending and receiving messages. Also, the retry strategy requirements depend heavily on the device's IoT scenario, context, capabilities.

**Design for resilient data retention:** Ensure that sufficient storage capacity is available on the IoT device, or, on the storage endpoint to assure message retention. For network connectivity issues, mitigate against message loss during offline operations whilst ensuring message playback. To learn more, see [offline mode retry guidelines](https://azure.microsoft.com/blog/extended-offline-operation-with-azure-iot-edge/) and the [retry pattern](/azure/architecture/patterns/retry). In addition, a robust code design on the forwarding application should be implemented to support event and error handling to ensure that [expiration policies for offline caches](/azure/architecture/patterns/cache-aside) are observed, to mitigate against data inconsistencies, or staleness.

**Implement data retry patterns:** Ensure a suitable design is in place to manage [data consistency](/previous-versions/msp-n-p/dn589800(v=pandp.10)) and minimize inconsistencies that could result from data-transmission related transaction failures, by designing the IoT solution to use design considerations. For example, a circuit breaker to mitigate against events such as intermittent connectivity issues through the implementation and use of components that offer robust retry mechanisms and policies, to [retry](/azure/architecture/patterns/retry) failing steps.

**Others**:

- [Valet pattern](/azure/architecture/patterns/valet-key): Offload data movement or transfer of telemetry streams from the forwarding IoT application to storage by assigning appropriate application-related privileges (security token) to the application to write to storage.

- [Sharding pattern](/azure/architecture/patterns/sharding): Provision sufficient resources such as storage space, compute resource, network bandwidth to support the file export to IoT Hub using the storage API for the relevant storage endpoints.

## Appendix

**Establish controls for releases and rollbacks**: Software releases to IoT devices can be challenging considering the heterogeneous and distributed nature of IoT solutions. Ensure appropriate controls to manage releases and updates across a fleet or sub fleet or individual device as needed. Design for reproducible and automated way to release to devices.

Consider using different tags within the device twin to easily distinguish and segregate devices and modules from each other. For example: `Dev` and `Prod`, `Virtual` and `Physical`, `Birthdate`, and `Firmware Rev`.

This enables you to query and filter on devices and create repeatable patterns for using the Jobs API or Azure Device Update to push changes to devices and then track the progress of the deployment.

### Design

**Use the Azure IoT SDKs**

Azure IoT provides a set of open-source SDKs to simplify and accelerate the development of IoT solutions build with Azure IoT Hub. Using the SDKs in prototyping and production enables you to:

- **Develop a future-proof solution with minimal code:** While you can use protocol libraries to communicate with Azure IoT Hub, you'll miss upcoming advanced features of IoT Hub and spend time redeveloping code and functionality that you could get for free. The SDKs support new features from IoT Hub, so you can incorporate them with minimal code and ensure your solution is up-to-date.

- **Leverage features designed for a complete software solution and focus on your specific need:** The SDKs contain many libraries that address key problems and needs of IoT solutions such as security, device management, and reliability. You can speed up time to market by using these libraries directly and focus on developing for your specific IoT scenario.

- **Develop with your preferred language for different platform:** You can develop with C, C\#, Java, Node.js, or Python without worrying about protocol details. The SDKs provide out-of-box support for a range of platforms and the C SDK can be ported to new platforms.

- **Benefit from the flexibility of open source with support from Microsoft and community:** The SDKs are available open source on GitHub. You can modify, adapt, and contribute to the code that runs your devices and your applications.

The SDKs include multiple reliability features. For example, the retry policy for unsuccessful device-to-cloud communication, addresses the problem of intermittent and non-reliable connectivity inherent to IoT devices. The SDKs offer the industry best-practice for retry policy, exponential back-off with random jitter, and the option to customize, taking into account the battery constrains of devices.

The SDKs also implement different connectivity features such as customizable keep-alive times to efficiently maintain cloud-to-device connectivity, support connections to IoT Hub from networks behind a proxy, turn tracing on or off for the transports. To learn more, see [Azure IoT SDK for C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md):

- [Azure IoT Hub SDKs](/azure/iot-hub/iot-hub-devguide-sdks)
- [Benefits of using the Azure IoT SDKs, and pitfalls to avoid if you don't](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## Next steps

<!-- TODO: update this link -->
IoT Well-Architected Framework for IoT - reliability.
