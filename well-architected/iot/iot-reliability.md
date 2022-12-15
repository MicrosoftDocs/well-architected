---
title: Reliability in your IoT workload
description: See guidance and recommendations that apply to the reliability pillar in a well-architected IoT workload.
author: asergaz
ms.author: sergaz
ms.date: 12/14/2022
ms.topic: overview
ms.service: architecture-center
ms.subservice: well-architected
ms.custom:
  - engagement-fy23
products:
  - azure-iot
categories:
  - iot
---

# Reliability in your IoT workload

IoT workloads, like all workloads, have the potential to malfunction. The key reliability considerations for Well-Architected IoT solutions are how quickly you can detect changes and how quickly you can resume operations.

IoT applications are often distributed at massive scale, and might operate over unreliable networks without persistent access or visibility into end-to-end data flows. Because of these factors, you should design your IoT architecture with availability and resiliency in mind.

Building a reliable IoT solution requires careful consideration of devices, cloud services, and how they interact. The choices you make for device hardware, connectivity and protocols, and cloud services affect your solution's reliability requirements and capabilities.

## Assess reliability in your IoT workload

To assess your IoT workload through the lenses of the Well-Architected Framework Reliability pillar, complete the reliability questions for IoT workloads in the [Azure Well-Architected Review](/assessments/azure-architecture-review/). After the assessment identifies key reliability recommendations for your IoT solution, use the following content to help implement the recommendations.

## Design principles

The IoT workload design methodology is underpinned by [five pillars of architectural excellence](../index.md) which serve as a compass for subsequent design decisions across the [key IoT design areas](iot-overview.md#iot-design-areas). The following design principles resonate and extend the quality pillar of the Azure Well-Architected Framework - [Reliability](../resiliency/overview.md).

|Design principle|Considerations|
|---|---|
|**Design devices for resiliency**|Design your devices to satisfy the uptime and availability requirements of your end-to-end solution. Ensure that your IoT device can operate efficiently with intermittent connectivity to the cloud.|
|[**Design for business requirements**](../resiliency/principles.md#design-for-business-requirements)|Cost implications are inevitable when introducing architectural modifications to meet service-level agreements (SLAs). For example, to have greater reliability and high availability you can implement cross-region redundancies and an automated system to autoscale. This trade-off should be carefully considered.|
|**Safe, simple update procedures**|An enterprise IoT solution should provide a strategy for how operators manage devices. IoT operators require simple and reliable update tools and practices.|
|[**Observe application health**](../resiliency/principles.md#observe-application-health)|Define service-level indicators (SLIs) and service-level objectives (SLOs) based on observability. Adding processes for auditing, monitoring, and alerting beyond what cloud services provide.|
|**High availability and disaster recovery (HA/DR) for critical components.**|Resilient hardware and software components that build in redundancy, including cross-region redundancies.|
|**Plan for capacity**|Plan for service quotas and throttles, latency between the detection-action, and establish benchmarks at production scale to support uninterrupted data flow.|

## IoT architecture layers

Reliability design principles help clarify considerations to ensure your IoT workload meets requirements across the [foundational IoT architecture layers](iot-overview.md#iot-architecture-layers). To achieve overall solution reliability, each layer should have acceptable levels of reliability.

:::image type="content" source="media/architecture-layers.svg" alt-text="Diagram that shows the layers and cross-cutting activities in an IoT architecture." border="false":::

## Device and gateway layer

As part of your overall IoT solution, design your devices to satisfy your solution's end-to-end uptime and availability requirements. Devices and gateways come in many forms. IoT devices and gateways can do data collection, supervisory control, and edge analytics.

- Data collection connects devices to sensors or subscribes them to telemetry from downstream systems, and pushes collected data to the cloud. IoT solution design should ensure reliable device management and reliable communications from the device to the cloud.

- Devices that provide supervisory control not only collect data to send to the cloud, but also take actions based on that data. Devices send data back to the machines or environment to take supervisory actions. The reliability of supervisory control applications is critical.

### Device design

Design and select IoT devices to function reliably in the expected operating conditions over their expected lifetimes. A reliable device should perform according to its hardware and software specifications, and any failure should be detected and managed through mitigation, repair, or replacement. Design devices for reliability, but also plan for failures.

### Device lifecycle

Limited service lifetimes affect solution reliability. Assess the consequences of device failure on the solution, and define a device lifecycle strategy according to solution requirements.

Device failure impact assessment includes:

- Severity, such as single points of failures (SPOFs).
- Probability, such as mean time between failures (MTBF).
- Detectability, such as failure mode and effects analysis (FMEA).
- Acceptable downtime period.

The acceptable operational downtime determines the speed and extent of device maintenance. The availability or longevity of the device and part supply is an important consideration for device lifecycle.

The more modular the design, the easier it is to swap out parts of the system, especially if some parts become obsolete earlier than others. Alternative or multi-sourcing of component and module supply chains are critical for reliable solutions.

### Environmental requirements

The conditions in which a device operates affect its reliability. Define your environmental requirements, and use devices with appropriate feature specifications. These specifications include parameters such as operating temperature range, humidity, ingress protection (IP) rating, electromagnetic interference (EMI) immunity, and shock and vibration immunity.

### Operational profile

Performance stress affects the operational behavior of devices, and therefore their reliability. Define operational profiles that estimate behavior over device lifetime, and assess device reliability accordingly. Such profiles include operation modes, such as wireless transmission or low-power modes, and environmental conditions, such as temperature, over the device lifetime.

In normal operating conditions, the device and software should run safely within the specified operational profiles. Devices must be able to service and process all the external sensors and data processing that the solution requires. Avoid running at the limit of device capabilities.

### Regulations and standards

Devices for specific industries are subject to applicable regulations and standards. Define regulations and standards, and make sure devices meet compliance and conformity requirements. Regulations include certification and marking, such as FCC or CE. Standards include industry or agency applications, such as ATEX and MIL-SPEC, and safety conformance, for example IEC 61508.

## Device management and modeling layer

Cloud services provide each device with an identity, and manage devices at scale. The cloud is often the final data ingress point for all messages flowing from the devices. In IoT solutions, the cloud services must provide reliability for the IoT devices to integrate and transmit data.

Device connectivity conditions, including upstream to the cloud and downstream to local networks, should be part of IoT solution reliability design. Assess the potential effect of connectivity interruption or interference, and define a connectivity strategy accordingly.

The connectivity strategy should include robustness, for example fallback capability and disconnection management, and buffering backup to mitigate cloud dependency for critical or safety functions.

The following design, error handling, and monitoring practices relate to connectivity.

### Connectivity design

An IoT solution should enable the flow of information between intermittently connected devices and cloud-based services. Make sure your IoT devices can operate efficiently with intermittent connectivity to the cloud.

Best practices include the following recommendations:

- Implement retry and backoff logic in device software.
- Synchronize device state with the cloud.
- Make sure you can store data on devices if your solution can't tolerate data loss.
- Use data sampling and simulations to measure network capacity and storage requirement baselines.

### Connectivity implementation

The Azure IoT device SDKs provide client libraries that you can use on devices or gateways to simplify connectivity with Azure IoT services. You can use the SDKs to instrument IoT device clients that:

- Connect to the cloud.
- Provide a consistent client development experience across different platforms.
- Simplify common connectivity tasks by abstracting details of the underlying protocols and message processing patterns, such as exponential backoff with jitter and retry logic.

### Connectivity monitoring

Connectivity issues for IoT devices can be difficult to troubleshoot because of the many possible points of failure. Application logic, physical networks, protocols, hardware, Azure IoT Hub, and other cloud services can have problems.

The ability to detect and pinpoint the source of an issue is critical. However, an IoT solution at scale could have thousands of devices, so it's not practical to manually check individual devices. Azure Monitor and Azure Event Grid can help you diagnose connectivity issues in IoT Hub.

### Connectivity resources

- [Manage connectivity and reliable messaging by using Azure IoT Hub device SDKs](/azure/iot-hub/iot-hub-reliability-features-in-sdks)
- [Monitor, diagnose, and troubleshoot Azure IoT Hub device connectivity](/azure/iot-hub/iot-hub-troubleshoot-connectivity)
- [Transient fault handling](/azure/architecture/best-practices/transient-faults)
- [Error handling for resilient applications in Azure](../resiliency/app-design-error-handling.md)
- [Circuit Breaker pattern - Cloud Design Patterns](/azure/architecture/patterns/circuit-breaker)
- [Compensating Transaction pattern - Cloud Design Patterns](/azure/architecture/patterns/compensating-transaction)
- [Throttling pattern - Cloud Design Patterns](/azure/architecture/patterns/throttling)

## Ingestion and communication layer

The IoT ingestion and communication layer covers service quotas and limits, capacity, throttling, and autoscale.

### Redundant capacity design

When planning thresholds and alerts, consider the latency between detection and action taken. Make sure the system and operators have enough time to respond to change requests. Otherwise, for example, you might detect a need to increase the number of units, but the system could fail by losing messages before the increase can take effect.

### Service quota planning

As with all platform services, IoT Hub and the IoT Hub Device Provisioning Service (DPS) enforce quotas and throttles on certain operations, so Azure can deliver predictable service levels and costs for its services. Quotas and throttles are tied to the service tier and number of units you deploy, so you can design your solution with the right number of resources. Review quotas and throttles in advance, and design your IoT Hub and DPS resources accordingly.

### Production-scale benchmarks

As the number of devices or volume of data increase, the cloud gateway must scale to support uninterrupted data flow. Due to the distributed nature of IoT solutions, the number of devices, and the volume of data, it's important to establish scale benchmarks for the overall solution. These benchmarks help to plan for capacity risks. Use the [Azure IoT Device Telemetry Simulator](/samples/azure-samples/iot-telemetry-simulator/azure-iot-device-telemetry-simulator) to simulate production scale volumes.

### Autoscaling to dynamically adjust to quotas

A benefit of using platform-as-a-service (PaaS) components is the ability to scale up and down with little effort according to your needs. To provide the lowest cost and operational effort, consider implementing an automated system to scale your resources up and down with the varying needs of your solution.

### Quota and throttle management

To ensure solution reliability, continuously monitor resource usage against quotas and throttles to detect usage increases that indicate the need to scale. Depending on your business requirements, you can continuously monitor resource usage and alert the operator when thresholds are met, or implement an automated system to autoscale.

### Capacity and scaling resources

- [Understand Azure IoT Hub quotas and throttling](/azure/iot-hub/iot-hub-devguide-quotas-throttling)
- [Quotas and limits in the Azure IoT Hub Device Provisioning Service](/azure/iot-dps/about-iot-dps#quotas-and-limits)
- [Auto-scale your Azure IoT Hub - Code Samples](/samples/azure-samples/iot-hub-dotnet-autoscale/iot-hub-dotnet-autoscale)
- [Azure IoT Device Telemetry Simulator - Code Samples](/samples/azure-samples/iot-telemetry-simulator/azure-iot-device-telemetry-simulator)
- [IoT Hub IP addresses](/azure/iot-hub/iot-hub-understand-ip-address)
- [Best practices for device configuration within an IoT solution](/azure/iot-hub/iot-hub-configuration-best-practices)
- [Choose the right IoT Hub tier for your solution](/azure/iot-hub/iot-hub-scaling)
- [Azure IoT Central quotas and limits](/azure/iot-central/core/concepts-quotas-limits)
- [IoT Hub quotas and throttling: Operation throttles](/azure/iot-hub/iot-hub-devguide-quotas-throttling#operation-throttles)
- [IoT Hub quotas and throttling: Other limits](/azure/iot-hub/iot-hub-devguide-quotas-throttling#other-limits)

## Transport layer

To connect to the cloud service for data, control, and management, devices need access to a network. Depending on the type of IoT solution, connectivity reliability might be your responsibility or that of the network service provider. Networks might have intermittent connectivity issues, and devices need to manage their behavior accordingly.

## DevOps layer

An enterprise IoT solution should provide a strategy for operators to manage the system. To address reliability, IoT management and operations should use DevOps processes to handle updates, observability and monitoring, and HA/DR implementation.

### Updates

The device aspect of IoT solutions presents challenges compared to cloud-based solutions. For example, there must be a way to continually update devices to address vulnerabilities and application changes.

Due to the distributed nature of IoT solutions, it's important to adopt safe and secure policies for deploying updates. IoT operators require simple and reliable update tools and practices.

A device update solution must support:

- Gradual update rollout through device grouping and scheduling controls.
- Support for resilient A/B device updates for seamless rollback.
- Detailed update management and reporting tools.
- Network optimization based on available bandwidth.

[Device Update for IoT Hub](/azure/iot-hub-device-update) is a service that enables safe, secure, and reliable over-the-air (OTA) IoT device updates. Device Update for IoT Hub can group devices and specify which devices should receive an update. Operators can view the status of update deployments and whether each device successfully applies the required updates.

If an update fails, Device Update helps operators identify the devices that failed and see the failure details. The ability to identify which devices failed can eliminate hours of trying to manually pinpoint the failure source.

Device Update monitors the status of device deployments and updates, and reports how many devices are compliant with the highest version available compatible update.

### Observability and monitoring

To manage overall solution reliability and define alert procedures, you should monitor every component of your IoT solution. All Azure IoT services publish metrics that describe service health and availability. To establish end-to-end observability, also consider the metrics that you need on the device side. Use these metrics as part of your overall solution reliability monitoring.

IoT application monitoring and diagnostics are crucial for availability and resiliency. If something fails, you need to know that it failed, when it failed, and why. By monitoring the operation of an IoT application and devices against a healthy state, you can detect and fix reliability issues.

To mitigate issues that affect IoT application reliability, you must be able to capture logs and signals that help you detect issues in end-to-end operations. Use logging and monitoring to determine whether an IoT solution is functioning as expected and to help troubleshoot issues with solution components.

The following actions support observability for IoT solutions:

- Establish a mechanism to collect and analyze performance metrics and alerts.
- Configure devices, cloud services, and applications to collect and connect with Azure Monitor.
- Use real-time dashboards and alerts to monitor Azure backend services.
- Define roles and responsibilities for monitoring and acting on events and alerts. For more information, see [Roles, responsibilities, and permissions](../security/design-identity-role-definitions.md).
- Implement continuous monitoring.

### Azure Monitor

[Azure Monitor](/azure/azure-monitor/essentials/data-platform-metrics) is the recommended monitoring and visualization platform for Azure IoT solutions. You can configure devices, cloud services, and applications, regardless of deployment location, to push log messages directly or through built-in connectors into Azure Monitor.

- Use Azure Monitor [built-in metrics integration](/azure/iot-edge/how-to-collect-and-transport-metrics) for remote monitoring of IoT Edge devices. To enable this capability on your devices, add the IoT Edge Metrics Collector module to your deployment and configure it to collect and transport module metrics to Azure Monitor.

- With Azure Monitor, you can monitor the state of your IoT Hub environment, ensure it's running properly, and verify that your devices aren't being throttled or experiencing connection issues. IoT Hub provides [usage metrics](/azure/iot-hub/monitor-iot-hub), such as the number of messages used and the number of devices connected. You can relay this data to Azure Monitor for analysis and to alert other services.

- If your solution uses Azure IoT Central, you can use the metrics IoT Central provides to assess the health of connected devices and active data exports. IoT Central applications enable metrics by default, which you can access from the Azure portal. Azure Monitor exposes and provides several ways to interact with these metrics.

- Azure Monitor provides custom log parsing to facilitate the decomposition of events and records into individual fields for indexing and search.

- Implement real-time dashboards and Azure Monitor alerts to monitor Azure backend services. Alerts proactively notify you about specific conditions in your monitoring data, so you can identify and address issues before customers encounter them. You can set alerts on metrics, logs, and the activity log.

[Application Insights](/azure/azure-monitor/app/cloudservices) is a feature of Azure Monitor that provides extensible application performance management and monitoring for live web apps. If your IoT solution uses custom Azure App Service, Azure Kubernetes Service, or Azure Functions applications, you can use Application Insights for app monitoring and analysis.

Application Insights can:

- Automatically detect performance anomalies.
- Help diagnose issues by using powerful analytics tools.
- Show what users actually do with your apps.
- Help you continuously improve app performance and usability.

### Continuous monitoring

Continuous integration and continuous deployment (CI/CD) is a DevOps practice that delivers software more quickly and reliably to provide continuous value to users. *Continuous monitoring (CM)* is a similar concept that incorporates monitoring across all phases and components of a DevOps cycle.

CM continuously ensures the health, performance, and reliability of your apps and infrastructure as they flow through development, production, and release to customers. For more information, see:

- [Seven best practices for continuous monitoring with Azure Monitor](https://azure.microsoft.com/blog/7-best-practices-for-continuous-monitoring-with-azure-monitor)
- [Continuous integration and continuous deployment to Azure IoT Edge devices](/azure/iot-edge/how-to-continuous-integration-continuous-deployment)

### Monitoring resources

- [Monitor Azure IoT Hub](/azure/iot-hub/monitor-iot-hub)
- [Monitor Azure IoT Hub data reference](/azure/iot-hub/monitor-iot-hub-reference)
- [Trace Azure IoT device-to-cloud messages with distributed tracing](/azure/iot-hub/iot-hub-distributed-tracing)
- [Check Azure IoT Hub service and resource health](/azure/iot-hub/iot-hub-azure-service-health-integration)
- [Collect and transport metrics - Azure IoT Edge](/azure/iot-edge/how-to-collect-and-transport-metrics)

### HA/DR for critical components

As you design and build your IoT solution, you must meet the SLA for failure recovery across the solution stack. Your SLA should guide you regarding which critical system components need HA/DR. There are multiple approaches, from redundancy across the IoT solution stack to redundancy for specific layers. Cost is also a major consideration to weigh against the importance of meeting SLAs.

- Azure IoT services have defined uptime and availability targets. Review the SLAs for Azure IoT services that are part of your solution to see if they meet your uptime goals. For example, Azure IoT Hub has an SLA of 99.9%, which means you should plan for 1 minute and 36 seconds of potential downtime per day. The Azure IoT Hub SDK provides built-in, configurable logic to handle retries and backoff.

- Consider breaking your uptime goals into two categories: device management and data ingestion operations. For example, it might be critical for a device to successfully send data to an IoT hub, even if device management services are unavailable. For more information, see the [Azure IoT Hub SDK reliability features](/azure/iot-hub/iot-hub-reliability-features-in-sdks).

- Consider using redundant hardware for sensors, power, and storage. Redundant hardware enables devices to function if a critical component isn't available. Hardware can also help with connectivity issues. For example, you can use a store and forward approach for data when connectivity isn't available. Azure IoT Edge has this feature built in.

- Devices must also be able to handle cloud outages. Azure region pairing provides an HA/DR strategy for IoT Hub that meets many SLA requirements. If region pairing isn't enough, consider implementing a secondary IoT hub. You can also use DPS to avoid hardcoded IoT Hub configurations on your devices. If your primary IoT hub goes down, DPS can assign your devices to a different hub.

- Consider implementing a heartbeat message pattern for devices you expect to be online most of the time. This pattern uses a custom IoT Hub route with Azure Stream Analytics, Azure Logic Apps, or Azure Functions to determine if a heartbeat has failed. You can use the heartbeat to define Azure Monitor alerts that take actions as needed.

#### HA/DR resources

- [Manage connectivity and reliable messaging by using Azure IoT Hub device SDKs](/azure/iot-hub/iot-hub-reliability-features-in-sdks)
- [IoT Hub high availability and disaster recovery](/azure/iot-hub/iot-hub-ha-dr)
- [IoT Hub intra-region HA/DR](/azure/iot-hub/iot-hub-ha-dr#intra-region-ha)
- [IoT Hub cross-region HA/DR](/azure/iot-hub/iot-hub-ha-dr#cross-region-dr)
- [How to clone an Azure IoT hub to another region](/azure/iot-hub/iot-hub-how-to-clone)
- [Test apps for availability and resiliency](/azure/architecture/framework/resiliency/testing)

## Next steps

> [!div class="nextstepaction"]
> [Security in your IoT workload](iot-security.md)

> [!div class="nextstepaction"]
> [Cost optimization in your IoT workload](iot-cost-optimization.md)

> [!div class="nextstepaction"]
> [Operational excellence in your IoT workload](iot-operational-excellence.md)

> [!div class="nextstepaction"]
> [Performance efficiency in your IoT workload](iot-performance.md)

## Related resources

- [Azure IoT reference architecture](/azure/architecture/reference-architectures/iot)
- [Azure IoT documentation](/azure/iot)

