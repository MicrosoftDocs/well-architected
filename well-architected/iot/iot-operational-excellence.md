---
title: Operational excellence in an IoT workload
description: Includes guidance and recommendations that apply to the Operational Excellence pillar in an IoT workload.
author: dominicbetts
ms.author: dobett
ms.date: 09/13/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---


# Operational excellence in your IoT workload

The key factor in IoT operational excellence is an organization's ability to plan, provision, configure, monitor, and retire IoT devices. This guide focuses on the operational aspects of IoT devices and services that uniquely address the core requirements of an IoT solution.

Given the complexity of an IoT solution's security, reliability, performance, and cost requirements, an organization's operational capabilities are an important factor in driving sustainable business value. Operational excellence in an IoT workload requires full visibility and control over all hardware and software components of the solution. Design, development, provisioning, monitoring, support, and maintenance practices must be agile and deliver business value without increasing operational risk.

The cloud and hybrid [shared responsibility model](/azure/security/fundamentals/shared-responsibility) also applies to operational excellence in IoT workloads. IoT workloads comprise three technology stacks: cloud, communication network, and IoT devices. In IoT solutions, device diversity and scale, different types of communication networks, and geographically distributed locations significantly shift the shared responsibility model away from the cloud provider. Cloud services can make it easier for organizations to operate IoT devices and networks themselves or by using third parties, but the organizations themselves own the operational responsibility for these key elements of IoT workloads.

Operational excellent ensures that your IoT solution can successfully:

- Support different roles including developers, administrators, and operators.
- Use automation for management and monitoring.
- Manage all device lifecycle stages for the lifetime of the solution.
- Scale efficiently to meet changes in demand, such as new device deployments or higher ingestion throughput.
- Integrate with other backend systems.

## Assess operational excellence in your IoT workload

To assess your IoT workload against the Well-Architected Framework Operational Excellence pillar, complete the operational excellence questions for IoT workloads in the [Azure Well-Architected Review assessment](/assessments/?mode=pre-assessment&id=azure-architecture-review). After the assessment identifies key operational excellence recommendations for your IoT solution, use the following content to help implement the recommendations.

## Operational excellence in IoT architecture layers

An IoT architecture consists of a set of foundational layers. Layers use specific technologies, and the [IoT Well-Architected Framework](iot-overview.md#iot-architecture-layers) highlights options for designing and implementing each layer. There are also cross-cutting activities that enable the design, building, and running of IoT and other solutions.

The IoT core layers: device and gateway, device management and modeling, and ingestion and communication, identify IoT-specific solutions. The other layers and cross-cutting activities are also common to, and often shared with, other workloads. DevOps cross-cutting activities are especially important to support the operational excellence pillar.

:::image type="content" source="media/architecture-layers.svg" alt-text="Diagram that shows the layers and cross-cutting activities in the IoT architecture." border="false":::

## Device and gateway layer

Select IoT hardware that meets the business and technical requirements of the solution. Define appropriate testing procedures to ensure the operational reliability of your IoT devices and gateways.

For a greenfield project that uses new hardware, you have more flexibility in device types, required firmware and connectivity features, and technical specification. You might need to select devices that comply with certain regional certification requirements and regulations such as CE, FCC, UL, PCI, or FDA.

For brownfield projects that already have hardware deployed, there are typically more restrictions on hardware. Depending on your scenario, you might need to look for other types of hardware, such as protocol or identity translation devices, or connectivity gateways such as Bluetooth to MQTT gateway.

The [Azure Device Catalog](https://devicecatalog.azure.com) helps you find and select hardware from certified partners that's been certified by the IoT device certification program and is compatible with Azure IoT services. The Device Catalog has search and filter capabilities you can use to find IoT hardware that meets your solution requirements.

An important feature to look for in Azure IoT-certified hardware is Azure Plug-and-Play and Digital Twins Definition Language (DTDL) compatibility. These features ensure that devices integrate seamlessly with services such as Azure IoT Central and Azure Digital Twins. For Azure IoT Edge scenarios, it's important to find catalog devices that have the [IoT Edge Managed](/azure/certification/program-requirements-edge-managed) certification. This certification guarantees the device can run the IoT Edge runtime, and enables deployment and management of IoT Edge modules that support edge processing and analytics workloads.

Device components and spares must be available to cover maintenance and support contracts for the lifetime of the solution. Ensure a timely and secure equipment supply at the start of the project, as this requirement can be expensive to introduce later. Use a trusted vendor chain and consider dual or multi supply sources.

## Ingestion and communication layer

The organization's network operations team typically partners with the telecommunication operator to handle the communication network technology stack of an IoT workload. Coordinate with your telecommunication operator to set up and operate the wired and wireless communication network components of your IoT solutions and operations.

### Capacity scaling

Configure the ingestion and other backend layers of the IoT cloud solution to be able to scale to handle expected and unexpected capacity needs. Azure offers several options to meet capacity requirements as your business grows. Capacity planning and scaling for your IoT solution varies depending on whether you build an IoT Central or Azure IoT Hub-based solution.

The IoT Central application platform as a service (aPaaS) automatically manages multiple instances of its underlying services to scale your IoT Central applications and make them highly available. However, IoT Central stores only 30 days of data, and because most IoT solutions export data to other services, you should focus on making sure those other services can handle expected and unexpected capacity needs.

With an IoT Hub-based solution, it's your responsibility to scale up to handle growth in the number of messages being ingested and to scale out to handle regional demands. Understanding the number of messages that will be sent to the IoT hub and the sustained throughput is critical to selecting the correct IoT Hub tier to support the predicted demand.

If you're approaching the message limit for IoT Hub, your system should automatically scale IoT Hub up to the next unit of capacity. Any backend services in the IoT solution, such as Azure Stream Analytics, Azure Cosmos DB, and Azure Data Explorer must support scalability to ensure there are no bottlenecks anywhere in the solution's data flow.

If your solution is tied to a connected product, you must communicate any fluctuation in expected load. Load can be impacted by marketing initiatives such as sales or promotions, or by seasonal events such as holidays. You should test load variations prior to events, including unexpected events, to ensure that your IoT solution can scale.

You should also plan for edge device capacity needs and requirements. Whether you're managing RTOS-based devices or larger compute devices with IoT Edge, make sure compute and memory sizing are adequate for your specific use cases.

For more information about capacity scaling, see the following articles:

- [Azure IoT Edge requirements](/azure/iot-edge/support)
- [DPS X.509 attestation](/azure/iot-dps/concepts-x509-attestation)
- [Azure IoT Edge managed certification requirements](/azure/certification/program-requirements-edge-managed)
- [Azure IoT Hub scaling](/azure/iot-hub/iot-hub-scaling)
- [Control access to Azure IoT Hub by using Azure Active Directory](/azure/iot-hub/iot-hub-dev-guide-azure-ad-rbac)
- [Manage users and roles in IoT Central](/azure/iot-central/core/howto-manage-users-roles)
- [Plan for capacity - Microsoft Azure Well-Architected Framework](/azure/architecture/framework/scalability/design-capacity)

## Device management and modeling layer

Implement a centralized device management solution to administer, monitor, and operate the lifecycle of IoT devices, and to manage the overall configuration of the IoT solution. Consider an integrated UI to assist operation teams with device fleet management.

### Design a management interface

Solution operators and administrators need an interface to interact with the IoT solution. IoT Central has a built-in, easy-to-use management interface that lets operators and administrators provision devices, add or remove users, send commands to IoT devices, and manage device updates. If the IoT Central management UI doesn't meet your requirements, or the solution is based on platform-as-a-service (PaaS) services like IoT Hub, you can build a custom management UI by using the REST APIs that IoT Hub and IoT Central expose.

### Device configuration and update management

Establish a strategy to update device or device fleet configuration. A device's configuration includes device properties, firmware, connection settings, and relationships. IoT operators need simple and reliable tools that let them update a device or device fleet's configuration at any point during the device's lifetime.

An IoT solution's scale and specific use of a device's configuration influences the design of a configuration management strategy. It's important to automate this strategy as much as possible, and to ensure that the configuration can be set and updated efficiently.

A configuration management strategy should support:

- Inventory of IoT devices and IoT Edge devices deployed in the field.
- Gradual update rollout through device grouping.
- Resilient updates to support testing and rollbacks.
- Automatic updates for existing or new devices.
- Updated status reports and alerts.

Azure services that support these configuration management requirements include IoT Hub automatic device management, IoT Edge automatic deployments, IoT Hub scheduled jobs, and Device Update for IoT Hub.

For continuous updates to existing or new device and IoT Edge device configurations, such as properties, application specific settings, or relationships, use either IoT Hub automatic device management or IoT Edge automatic deployments. Both services are an efficient, secure, and reliable way to automate configuration deployments for either a fleet or specific group of devices. Both services continuously monitor all new and existing targeted devices and their configuration based on tags, to ensure these devices always have a specified configuration. The key difference between these services is that automatic device management applies only to non-Azure IoT Edge devices, and IoT Edge automatic deployments applies only to Azure IoT Edge devices.

To update an existing device or IoT Edge device configuration based on a one-time or recurring schedule, use Azure IoT Hub scheduled jobs. This service is an efficient, secure, and reliable way to provide a configuration update for either a fleet or specific group of devices at a scheduled time.

To update existing device or IoT Edge device firmware, application, or package updates over-the-air (OTA), use [Device Update for IoT Hub](/azure/iot-hub-device-update/understand-device-update). This service is a safe, secure, and reliable way to update a fleet or specific group of devices.

It's a good idea to include a manual update method for IoT devices. Because of root certificate changes to IoT Hub or DPS, or connectivity issues, you might need to update devices using a manual method such as physically connecting to a local computer or using a local connectivity protocol such as Bluetooth.

For more information about device management, see:

- [Azure IoT Hub Automatic Device Management](/azure/iot-hub/iot-hub-automatic-device-management)
- [Azure IoT Edge automatic deployments](/azure/iot-edge/module-deployment-monitoring)
- [Azure IoT Hub scheduled jobs](/azure/iot-hub/iot-hub-devguide-jobs#jobs-to-update-device-twin-properties)
- [Device Update for IoT Hub](/azure/iot-hub-device-update/understand-device-update)

### Device provisioning

Define a remote device provisioning strategy to enable zero-touch, just-in-time provisioning of IoT devices in the field without requiring human intervention.

For remote provisioning of IoT devices, [Azure IoT Hub Device Provisioning Service (DPS)](/azure/iot-dps/about-iot-dps) enables connecting and configuring remote devices to IoT Hub. DPS enables zero-touch provisioning without hard-coding information at the factory, and enables load-balancing of devices across multiple IoT hubs. Although DPS supports symmetric key attestation, in a production environment you should use either the X.509 certificate or TPM attestation mechanisms. If you use X.509 certificates, you should deploy the root certificate or an intermediate certificate signed by the root certificate to DPS, to allow devices in the field to properly authenticate to the service and be assigned to their correct IoT hub.

Part of an IoT solution lifecycle includes reprovisioning devices in the field or moving them between IoT hubs. DPS enables the configuration of [reprovisioning policies](/azure/iot-dps/how-to-reprovision) that determine the expected behavior when an IoT device submits a new provisioning request. Devices should be programmed to send a provisioning request on reboot, and should implement a method to manually trigger provisioning on demand. This mechanism ensures that every time the device starts up, it contacts DPS to get redirected to the appropriate IoT hub.

## Integration layer

A typical IoT solution is composed of multiple components such as ingestion, routing, data storage, and data processing. It's important to document and have a good understanding of the entire data flow of the IoT solution. Have testing procedures in place to ensure the different parts of the solution work as expected and meet the technical and operational requirements of the organization. Implement automation to identify device capabilities at scale as they connect to your IoT solution and to easily integrate with back-end services.

Configure and test reliable integration with other Azure and third-party services that support the back-end and front-end services of the IoT application. A successful IoT implementation requires integrating IoT services such as IoT Hub and DPS with other Azure and third-party services.

For example, DPS supports custom allocation policies by using custom code and Azure Functions, so it's important to confirm that the Azure Function allows traffic coming from DPS and IoT Hub. Another example is the integration between IoT Hub and back-end services to enable features such as message routing and file upload. IoT Hub needs to properly authenticate to those Azure services. You should use managed identities to eliminate the need to manage those credentials manually.

## DevOps layer

DevOps includes role and user management, metrics collection, monitoring, and automation.

### Role and user management

A key decision to make early in the IoT solution design phase is to define the roles that implement and manage the solution, along with the corresponding responsibilities. Determine the roles that are responsible for developing, managing, and operating the IoT solution at scale, and the users to be assigned to those roles.

Ideally, the solution should trust a centralized identity provider, such as [Azure Active Directory (Azure AD)](/azure/active-directory/fundamentals/active-directory-whatis), and only let the appropriate users in those roles perform management or operation activities such as creating and provisioning new devices, sending commands to hardware in the field, deploying updates, and modifying user permissions.

IoT Central has built-in roles you can assign to existing users in Azure AD, eliminating the need to develop a customized identity service. In an IoT Hub-based solution, you can use Azure AD to authenticate requests to IoT Hub service APIs, such as creating device identities or invoking direct methods. You can develop a custom management UI for solution operators and administrators that authenticates users against Azure AD and executes API requests to the IoT solution back end on behalf of those users.

### IoT Edge Metrics Collector

Azure IoT provides the [IoT Edge Metrics Collector](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.metrics-collector?tab=overview) ready-to-use IoT Edge module in the IoT Edge Module Marketplace. Add this module to an IoT Edge deployment to collect metrics and send them to Azure Monitor. The open-source module code is a multi-architecture Docker container image that supports Linux x64, ARM32, and ARM64 version 1809.

The Metrics Collector module can collect logs from all the modules that can emit metrics by using the [Prometheus data model](https://prometheus.io/docs/concepts/data_model/). While [built-in metrics](/azure/iot-edge/how-to-access-built-in-metrics) enable broad workload visibility by default, you can also use custom modules to emit scenario-specific metrics that enhance the monitoring solution.

There are two options to send metrics from the Metrics Collector module to the cloud:

- Send the metrics to Log Analytics. The collected metrics are ingested into the specified Log Analytics workspace using a fixed, native table called `InsightsMetrics`.

- Send the metrics to IoT Hub. You can configure the collector module to send the collected metrics as UTF-8 encoded JSON device-to-cloud messages through the *edgeHub* module. This option unlocks monitoring of locked-down IoT Edge devices that are only allowed external access to the IoT Hub endpoint.

The `AllowedMetrics` and `BlockedMetrics` configuration options take space- or comma-separated lists of metric selectors. A metric is matched to the list and included or excluded if it matches one or more metrics in either list.

You can visually explore metrics collected from IoT Edge devices by using Azure Monitor workbooks. Curated workbooks use built-in metrics from the IoT Edge runtime that are ingested into a Log Analytics workspace. These views don't need any metrics instrumentation from the workload modules.

The Azure portal provides curated monitoring workbooks for IoT Edge devices as public templates. To access the workbooks, from your **IoT Hub** or **IoT Central** page in the Azure portal, navigate to the **Workbooks** page in the **Monitoring** section.

:::image type="content" source="media/edge-monitoring-workbook.gif" alt-text="An animation that shows the IoT Edge monitoring workbook in the Azure portal.":::

### Monitoring

Use IoT solution logging, monitoring, and alerting systems to determine whether the solution is functioning as expected and to help troubleshoot and mitigate problems. Monitoring and logging help determine whether devices or systems are in an error condition, are correctly configured, are generating accurate data, and are meeting defined service level objectives.

IoT logging and monitoring systems can be more complicated than in standard line-of-business applications. The complexity arises because IoT solutions often span:

- Physical sensors that interact with an environment.
- Applications on the edge doing activities like data shaping and protocol translation.
- Infrastructure components such as on-premise gateways, firewalls, and switches.
- Ingestion and messaging services.
- Persistence mechanisms.
- Insight and reporting applications.
- Subsystems that operate and scale independently in the cloud.

The following simplified logging and monitoring architecture shows examples of typical IoT solution components and how they use recommended technologies.

:::image type="content" source="media/logging-monitoring.jpg" alt-text="Diagram that shows example logging and monitoring systems." border="false" lightbox="media/logging-monitoring.jpg":::

If your critical applications and business processes rely on Azure resources, you should monitor those resources for availability and performance. You can use [Azure Monitor](https://azure.microsoft.com/services/monitor) to carry out the following monitoring activities:

- Detect and diagnose issues across applications and dependencies with [Application Insights](/azure/azure-monitor/app/app-insights-overview).
- Correlate infrastructure issues with [VM insights](/azure/azure-monitor/vm/vminsights-overview) and [Container insights](/azure/azure-monitor/containers/container-insights-overview).
- Drill into your monitoring data with [Log Analytics](/azure/azure-monitor/logs/log-query-overview) for troubleshooting and deep diagnostics.
- Support operations at scale with [smart alerts](/azure/azure-monitor/alerts/alerts-smartgroups-overview) and [automated actions](/azure/azure-monitor/alerts/alerts-action-rules).
- Create visualizations with Azure [dashboards](/azure/azure-monitor/visualize/tutorial-logs-dashboards) and [workbooks](/azure/azure-monitor/visualize/workbooks-overview).
- Collect data from [monitored resources](/azure/azure-monitor/monitor-reference) using [Azure Monitor Metrics](/azure/azure-monitor/essentials/data-platform-metrics).

#### Monitor IoT Hub

The **Overview** page in the Azure portal for each IoT hub includes charts that provide some usage metrics, such as the number of messages used and the number of devices connected to the hub. The information on the **Overview** page is useful, but represents only a small amount of the monitoring data available for an IoT hub. Some monitoring data is collected automatically and is available for analysis as soon as you create your IoT hub. You can configure other types of data collection. To learn more about the metrics and logs that IoT Hub creates, see [Monitoring Azure IoT Hub data reference](/azure/iot-hub/monitor-iot-hub-reference).

Azure IoT Hub collects the same types of monitoring data as other Azure resources, as described in [Monitoring data from Azure resources](/azure/azure-monitor/essentials/monitor-azure-resource#monitoring-data).

#### Monitor updates

As with any deployment or update, you should monitor the state of deployments and devices. Device Update for IoT Hub monitors compliance by measuring how many devices have installed the highest version compatible update. A device is compliant if it has installed the highest version available compatible update.

#### Monitor configuration

As with any deployment or update, you should monitor and alert on the status of a device configuration deployment or update in place. Each Azure IoT configuration service collects and stores logs and metrics in Azure Monitor. You can use this data to create Azure Monitor alerts to send notifications when a configuration deployment or update is created, completed, or failed.

If the monitoring data provided by each of the Azure IoT configuration services isn't enough, the Azure IoT Hub service APIs offer a more granular view.

#### Monitor automation and DevOps

DPS, IoT Hub, and IoT Edge provide continuous metrics and status updates that are key inputs to monitor continuous integration/continuous deployment (CI/CD) status or automation script output. You can collect and analyze these metrics in a Log Analytics workspace and then define alerts.

- [Monitor device connectivity using the Azure CLI IoT extension](/azure/iot-central/core/howto-monitor-devices-azure-cli)
- [Monitor, diagnose, and troubleshoot Azure IoT Hub device connectivity](/azure/iot-hub/iot-hub-troubleshoot-connectivity)
- [Monitor Azure IoT Hub](/azure/iot-hub/monitor-iot-hub)
- [Automatic IoT device and module management using the Azure CLI](/azure/iot-hub/iot-hub-automatic-device-management-cli)
- [Check Azure IoT Hub service and resource health](/azure/iot-hub/iot-hub-azure-service-health-integration)
- [Tutorial: Set up and use metrics and logs with an Azure IoT hub](/azure/iot-hub/tutorial-use-metrics-and-diags)
- [Collect and transport metrics](/azure/iot-edge/how-to-collect-and-transport-metrics)

### Automation

An IoT device is fundamentally a small computer with specialized hardware and software. IoT devices are often hardware-constrained, for example have limited memory or compute capacity. Automation and DevOps ensure that IoT device and gateway software is properly uploaded and deployed to minimize operational downtime. Automation and DevOps are an essential aspect of monitoring and managing the lifecycle of IoT devices.

The key benefit of a mature DevOps implementation is agility, the ability to quickly sense and respond to changes in business needs and seamlessly support the full lifecycle of developing, deploying, and operating a solution. Automated testing, integration, and deployment enables DevOps for application software development. Deploy application software changes in an infrastructure-as-code (IaC) environment to automate and manage the ongoing operation of deployed software.

Agility, DevOps, and automation are important in the development, deployment, and operations of IoT solutions.

- Automate the IoT application software lifecycle, from development through testing to deployment to IT operations.
- Deploy IoT application software on devices by using IoT Edge. Use Azure DevOps tools and processes in IoT Hub and IoT Edge to automate the software lifecycle of an edge solutions.
- Operationally manage IoT devices by providing operators with tools that let them collaborate and get visibility, insights, and control to maintain and operate a reliable IoT solution.

Consider the following requirements when you design automation and DevOps for IoT:

- Thousands of devices can be connected to a single cloud endpoint such as IoT Hub. Any change in configuration or software must be spread across all the devices.
- Device vendors and cross-functional solution developers should work together to develop and deploy IoT solutions. Embrace cross-functional teams to deliver continuously for solutions.
- Software programs installed on hardware define system functionality. To change system functionality, update software instead of making hardware changes or local interventions.
- Use CI/CD DevOps principles and processes to boost productivity and create a seamless rapid development cycle.
- Evolve business and deployment models to create possibilities for different business models and pilot validation, deployment, and enhancements. DevOps provides a way to consistently deliver fresh software updates to meet solution and business requirements.
- Connected IoT Edge devices have a lifecycle that extends beyond deploy, break and fix, and retire. Connectivity creates the opportunity to continuously add incremental innovation across the system lifecycle. DevOps puts organizations in the best position to capitalize on opportunity.

When you implement automation and DevOps in IoT systems, follow device lifecycle and specific automation and DevOps requirements in each phase. The following tables describe platform features that support three phases of the device lifecycle.

**Beginning of life**

| Expectations                            | Platform feature available with code snippets                                                                                         |
|-----------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|
| Non-DPS device registration             | [Bulk device updates](/azure/iot-hub/iot-hub-bulk-identity-mgmt)                                                                      |
| Device provisioning                     | [DPS configuration required to provide zero touch device provisioning](/azure/iot-dps/concepts-roles-operations) |
| Device certificate and token management | [Control access to IoT Hub using Shared Access Signatures (SAS)](/azure/iot-hub/iot-hub-dev-guide-sas)  |
| Device certificate lifecycle management | [CA certificate lifecycle management with DPS and DigiCert](/azure/key-vault/certificates/how-to-integrate-certificate-authority)     |
| Device initial configurations           | [Device twins and device modules](/azure/iot-hub/iot-hub-automatic-device-management)                                                 |

**Midlife**

| Expectations                            | Platform feature available with code snippets                                                                                                                                                       |
|-----------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Continuous device configuration management at scale | [Device twins and device modules](/azure/iot-hub/iot-hub-automatic-device-management)|
| CI/CD pipeline for IoT Edge modules                 | [Continuous integration and continuous deployment (CI/CD) to Azure IoT Edge devices](/azure/iot-edge/how-to-continuous-integration-continuous-deployment-classic)|
| Device reprovisioning                               | [DPS device reprovisioning](/azure/iot-dps/how-to-reprovision)|
| SAS key generation for changes or expiration   | [Control access to IoT Hub using Shared Access Signatures (SAS)](/azure/iot-hub/iot-hub-dev-guide-sas)|
| Log and device diagnostics                          | Pre-configured [Azure workbooks](/azure/azure-monitor/visualize/workbooks-overview) for IoT Hub |
| Azure IoT Edge monitoring diagnostics               | [Collect and transport IoT Edge device logs and metrics](/azure/iot-edge/how-to-collect-and-transport-metrics)|
| OTA device updates                                  | [Device Update for IoT Hub](/azure/iot-hub-device-update)|

**End of life**

| Expectations                                  | Platform feature available with code snippets                                         |
|-----------------------------------------------|---------------------------------------------------------------------------------------|
| Unenroll devices                              | [Disenroll a device from DPS](/azure/iot-dps/how-to-revoke-device-access-portal)             |
| Remove device-specific configuration          | [Device twins and device modules](/azure/iot-hub/iot-hub-automatic-device-management) |
| Device replacement                            | Same as beginning of life                                                        |

## Next steps

> [!div class="nextstepaction"]
> [Performance efficiency in your IoT workload](iot-performance.md)
