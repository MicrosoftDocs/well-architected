---
title: Operational excellence in an IoT workload
description: Includes guidance and recommendations that apply to the Operational Excellence pillar in an IoT workload.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---


# Operational excellence in your IoT workload

Given the complexity of non-functional or operational needs such as security, reliability, performance, and cost of IoT devices, perhaps the single most important factor to scaling an IoT solution and driving sustainable business value for an organization is the ability of an organization to have excellent IoT device operational capabilities. Operational excellence in an IoT workload is about enabling an organization for full visibility and control over the hardware and software components of an IoT solution, ensuring the best experience for users. Operational excellence includes making the IoT solution design, development, provisioning, monitoring, support, and maintenance practices more agile and valuable to business without increasing operational risk. The principles of operational excellence in an IoT workload are a set of considerations that help achieve superior operational practices. These principles are described in a following section.

As you consider operational excellence in an IoT workload, the shared responsibility model in cloud and hybrid scenarios also applies. This model is best described in the context of [security considerations](/azure/security/fundamentals/shared-responsibility). An organization's IoT operation comprises three different stacks of technologies: cloud, communication network, and IoT devices. Given the scale and diversity of devices in IoT solutions and the use of different types of communication networks at geographically distributed locations where devices are installed, there's a significant shift in IoT solutions in the cloud shared responsibility model. It leaves operational ownership of key elements of an IoT solution with the organization. While Microsoft has made it easy in many ways for organizations to operate these elements on their own or using third parties, the organization owns the operational responsibility for these elements.

The IoT Well-Architected Framework operational excellence guide focuses on the operational excellence aspects of the IoT devices and Azure services that uniquely address the core requirements of an IoT solution. The key factor in IoT operational excellence is the ability of an organization to plan, provision, configure, monitor, and retire IoT devices. The core Azure IoT services discussed in this guide provide an array of capabilities for an organization to accomplish these tasks effectively and efficiently and enable an organization to have excellent IoT device operational capabilities.

The communication network technology stack of an organization's IoT operation is typically handled by the organization's network operations team partnering with the organization's telecommunication operator. It's recommended that the organization coordinate with their telecommunication operator to set up and operate the wired and wireless communication network components of their IoT solutions and operations.

An IoT architecture consists of a set of foundational layers. Layers are realized by using specific technologies, and the IoT Well-Architected Framework highlights options for designing and realizing each layer. There are also cross-cutting layers that enable the design, building, and running of IoT solutions:

:::image type="content" source="media/architecture-layers.svg" alt-text="Diagram that shows the layers and cross-cutting activities in the I o T architecture." border="false":::

## Prerequisites

To assess your IoT workload based on the principles described in the Microsoft Azure Well-Architected Framework, complete the assessment:

<!-- TODO: add link to assessment -->

After you complete the assessment, this guide helps you address the key operational excellence recommendations identified for your solution.

## Principles

The following principles apply to the operational excellence pillar in an IoT workload:

**Solution operation and scaling:** Follow the best practices around this principle to ensure that the IoT solution can successfully manage the automated provisioning of devices, can be integrated with other backend systems, is designed for use by different types of personas including solution developers, solution administrators, and operators, and adapts/scales efficiently to any changes in demand such as new IoT devices being deployed or higher ingestion throughput.

**Device management:** Any successful enterprise IoT solution requires a strategy to establish and update a device or fleet of device's configuration. A device's configuration includes device properties, connection settings, relationships, and firmware. IoT operators require simple and reliable tools that enable them to update a device or fleet of device's configuration at any point during the device's lifetime.

**Monitoring and alerting:** Use IoT solution logging, monitoring, and alerting systems to determine whether the solution is functioning as expected and to help troubleshoot what is wrong throughout the lifecycle of the solution.

**Automation and DevOps:** An IoT device is fundamentally a small computer with specialized hardware and software. IoT devices are often constrained in hardware, for example having limited memory or compute capacity. Automation and DevOps are essential to ensure that OS and software for IoT devices and gateways are properly uploaded and deployed to minimize any operational downtime. Automation and DevOps becomes an essential aspect for monitoring and managing the lifecycle of IoT devices.

The following sections address the layer specifics of these principles for the operational security pillar:

## Solution operation and scaling

### Design

Some key design decisions to consider include:

- Select IoT hardware that meets the business and technical requirements of the solution and define appropriate testing procedures to ensure the operational reliability of your IoT devices and gateways.

- Implement an automated way to identify device capabilities at scale as they connect to your IoT solution to easily integrate them with your backend services.

- Define a remote device provisioning strategy to enable zero-touch, just-in-time provisioning of IoT devices in the field without requiring human intervention.

- Determine the roles that are responsible for developing, managing, and operating the IoT solution at scale. Determine the users in the organization to be assigned to those roles.

- Implement a centralized device management solution to administer, monitor, and operate the lifecycle of IoT devices and to manage the overall configuration of the IoT solution. Consider an integrated UI to assist operation teams to management the device fleet.

- Configure and test reliable integration with other Azure and third party services that support the backend and frontend services of the IoT application. A typical IoT solution is composed of multiple components such as ingestion, routing, data storage, and data processing. It's important to verify that all these components in the data flow are functional.

- Configure the ingestion and other backend layers of the IoT solution in the cloud to scale to be able to handle expected and unexpected capacity needs as demand for the service grows.

### Implement

#### Select and test IoT hardware

The [Azure Device Catalog](https://devicecatalog.azure.com/) site lets IoT solution developers find hardware from a list of certified partners and select the IoT devices that meet the specific requirements of the IoT solution being implemented. For example, during product selection or development, you may need to identify devices that comply with certain regional certification requirements and regulations such as CE, FCC, UL, PCI, and FDA.

For a greenfield project, there's more flexibility regarding the types of devices that can be used, the required firmware/connectivity features and the technical specification for the IoT devices. For brownfield projects, there are typically more restrictions when you select hardware (because hardware has already been deployed in the field) but you might still need to look for other type of hardware such as protocol/identity translation devices or connectivity gateways - such as Bluetooth to MQTT gateway - depending on the specific scenario. For either case, the Azure Device Catalog has search and filter capabilities that you can use to find IoT hardware that's been certified by the IoT device certification program and are compatible with Azure IoT services.

An important feature to look for when selecting hardware is Azure Plug-and-Play and the Digital Twins Definition Language (DTDL) compatibility. These features ensure the device integrates seamlessly with services such as Azure IoT Central and Azure Digital Twins. For Azure IoT Edge scenarios, it's also important to find devices in the catalog that have the [IoT Edge Managed](/azure/certification/program-requirements-edge-managed) certification that guarantees the device can run the Azure IoT Edge runtime and enables the deployment and management of IoT Edge Modules to support local processing and analytical workloads.

When you select devices or components for your solution, take care to ensure a timely and secure supply of equipment. Consider dual or multi supply sources and a trusted vendor chain. The availability of spares to cover the solution lifetime and maintenance or support contracts should also be included, as this step is sometimes forgotten at the start of a project and can be expensive to introduce later.

#### Provision and manage the lifecycle of IoT devices

For remote provisioning of IoT devices, [Azure Device Provisioning Service (DPS)](/azure/iot-dps/about-iot-dps) enables connecting and configuring remote devices to an IoT Hub. DPS enables zero-touch provisioning without hardcoding information at the factory and it enables load-balancing of devices across multiple IoT Hubs. Although DPS supports symmetric key attestation, in a production environment you should use either the X.509 certificate or TPM attestation mechanisms. If you use X.509 certificates, the root certificate (or an intermediate certificate that has been signed by the root certificate) should be deployed to DPS to allow devices in the field to properly authenticate to the service and assigned to their corresponding IoT hub.

Part of an IoT solution lifecycle includes reprovisioning devices in the field and/or moving them between IoT hubs. DPS enables the configuration of [reprovisioning policies](/azure/iot-dps/how-to-reprovision) that determine the expected behavior when an IoT device submits a new provisioning request. Devices should be programmed to send a provisioning request on reboots and should implement a method to manually trigger a provisioning on demand. This ensures that every time the device boots, it always contacts DPS before getting redirected to the appropriate IoT hub.

#### Determine the list of roles and users for the IoT solution

A key decision to make early in the IoT solution design phase is to determine the list of roles that implement and manage the solution along with the corresponding responsibilities. Ideally, the solution should trust a centralized identity provider, such as [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis), and only let the appropriate users in those roles perform any management and/or operation activities such as creating and provisioning new devices, sending commands to hardware in the field, deploying updates, and modifying user permissions. If the solution uses IoT Central, there are built-in roles that you can assign to existing users in Azure Active Directory eliminating the need to develop a customized identity service. In a solution backed by IoT Hub, you can use Azure Active Directory to authenticate requests to Azure IoT Hub service APIs, such as creating device identities or invoking direct methods. You can develop a custom management UI interface for solution operators and administrators can be developed that authenticates users against Azure Active Directory and executes the API requests to the IoT solution backend on behalf of those users.

#### Integration with backend systems

A successful IoT implementation requires the proper integration of IoT services such as IoT Hub and DPS with other backend and frontend services, both Azure or third party. Although the configuration of those other services is out of the scope of this document, it's important to document and have a good understanding of the entire data flow of the IoT solution and to have testing procedures in place to ensure the different parts of the solution are working as expected and meeting the technical and operational requirements of the organization. For example, DPS supports custom allocation policies by using custom code and Azure Functions, therefore it's important to confirm that the Azure Function allows traffic coming from DPS and IoT Hub. Another example is the integration between IoT Hub and backend services to enable features such as message routing and file upload. IoT Hub needs to properly authenticate to those Azure services and you should use managed identities to eliminate the need for developers to manage those credentials manually. Integration of Azure IoT services with other services should be tested periodically to ensure that the IoT solution is performing as expected and meeting the operational SLAs defined by the business. The [Azure Architecture Center](/azure/architecture/) contains many IoT reference architectures that outline and describe the proper and secure integration of the Azure IoT platform with other services.

#### Design a management interface

Solution operators and administrators need an interface to interact with the IoT solution. If an IoT solution is based on IoT Central, an easy-to-use management interface is already built into the application enabling operators and administrators to complete tasks such as provisioning new devices, adding/removing users, sending commands to IoT devices, and managing device updates. If the UI management interface provided by IoT Central doesn't meet the requirements of the solution or the solution is based on PaaS services such IoT Hub, then you can build a custom management UI that uses the service REST APIs exposed by IoT Hub and IoT Central.

#### Capacity scaling

Azure offers many options to meet capacity requirements as your business grows. Capacity planning and scaling for your IoT solution varies depending on whether you choose to build an IoT Central or IoT Hub based solution.

When you implement an IoT solution by using the application-platform-as-a-service (aPaaS) IoT Central, little needs to be done. IoT Central automatically manages multiple instances of its underlying services to scale your IoT Central applications and make them highly available. However, since IoT Central only stores 30 days of data, you're likely to export data to other services. These other services are where you should focus your time, making sure the solution can handle expected and unexpected capacity needs.

With an IoT Hub based solution, it's your responsibility to scale up to handle growth in the number of messages being ingested and to scale out to handle regional demands for the solution. Understanding the number of messages that will be sent to the IoT hub and the sustained throughput is critical to select the correct IoT Hub tier to support the predicted demand. If you're approaching the message limit for your IoT Hub, you can follow these steps to automatically scale the IoT Hub up to the next unit of capacity. In addition to properly configuring IoT Hub to scale as demand changes, it's important to design any backend services in the IoT solution (such as Stream Analytics, Cosmos DB, and Azure Data Explorer) with scalability in mind to ensure there are no bottlenecks anywhere in the solution's data flow.

If your solution is tied to a connected product, the business must communicate any fluctuation in expected load. Load can be impacted by marketing initiatives, such as sales or promotions; or, by seasonal events, such as holidays. You should test variations of load prior to events, including unexpected ones, to ensure that your IoT solution can scale.

Also, you should plan for the capacity needs/requirements of the IoT Edge devices. Whether you're managing RTOS based devices or larger compute devices with IoT Edge, there are many factors that need to be taken into account to make sure the sizing of compute and memory are adequate for the specific use-cases.

### Resources

- [Azure IoT Hub scaling](/azure/iot-hub/iot-hub-scaling)
- [Azure IoT Edge Requirements](/azure/iot-edge/support)
- [DPS X.509 Attestation](/azure/iot-dps/concepts-x509-attestation)
- [Manage Users and Roles in IoT Central](/azure/iot-central/core/howto-manage-users-roles)
- [Azure IoT Edge Managed Certification Requirements](/azure/certification/program-requirements-edge-managed)
- [Controlling access to Azure IoT Hub by using Azure Active Directory](/azure/iot-hub/iot-hub-dev-guide-azure-ad-rbac)
- [Plan for capacity - Microsoft Azure Well-Architected Framework](/azure/architecture/framework/scalability/design-capacity)

## Device management

### Design

An IoT solution's scale and specific use of a device's configuration influences the design of a configuration management strategy. It's important to automate this strategy as much as possible and ensure a device or fleet of device's configuration is set and updated efficiently as well.

A configuration management strategy should support:

- Inventory of IoT devices and IoT Edge devices deployed in the field
- Gradual update rollout through device grouping
- Resilient updates to support testing and rollbacks
- Automatic updates for existing or new devices
- Update status reports and alerts

### Implement

Example services that support these requirements for configuration management are Azure IoT Hub Automatic Device Management, Azure IoT Edge Automatic Deployment, Azure IoT Hub Scheduled Jobs, and Device Update for IoT Hub.

For continuous updates to existing or new device and IoT Edge device configurations, such as properties, application specific settings, and/or relationships, use either Azure IoT Hub Automatic Device Management or Azure IoT Edge Automatic Deployment. Both services enable an efficient, secure, and reliable way to automate configuration deployments for either a fleet or specific group of devices. Both services continuously monitor all new and existing targeted devices, based on tags, and their configuration to ensure these devices always have a specified configuration. The key difference between these services is that Automatic Device Management only applies to non-Azure IoT Edge devices and Azures IoT Edge Automatic Deployment only applies to Azure IoT Edge devices.

To update an existing device or IoT Edge device configuration, such as properties, application specific settings, and/or relationships, based on a schedule, whether this update is onetime or recurring, use Azure IoT Hub scheduled jobs. This service enables an efficient, secure, and reliable way to provide a configuration update for either a fleet or specific group of devices at a scheduled time.

To update an existing device or IoT Edge device configuration, such as firmware, application, or package updates, use [Device Update for IoT Hub](/azure/iot-hub-device-update/understand-device-update). This service enables a safe, secure, and reliable way to provide firmware, application, or package updates over-the-air (OTA) to either a fleet or specific group of devices. It's a good idea to include a manual update method when developing IoT devices. Because of root certificate changes to IoT Hub or DPS, or connectivity issues, you may need to update devices using a manual method such as physically connecting to a local computer or using a local connectivity protocol such as Bluetooth.

### Resources

- [Azure IoT Hub Automatic Device Management](/azure/iot-hub/iot-hub-automatic-device-management#create-a-configuration)
- [Azure IoT Edge Automatic Deployment](/azure/iot-edge/module-deployment-monitoring)
- [Azure IoT Hub Scheduled Jobs](/azure/iot-hub/iot-hub-devguide-jobs#jobs-to-update-device-twin-properties)
- [Device Update for IoT Hub](/azure/iot-hub-device-update/understand-device-update)

## Monitoring and alerting

### Design

Monitoring and logging systems help to answer the following operational questions:

- Are devices or systems in an error condition?
- Are devices or systems correctly configured?
- Are devices or systems generating accurate data?
- Are systems meeting the defined service level objectives?

Monitoring and logging systems can help answer these questions. If the answer is no, these systems surface relevant information for operations teams to help mitigate problems. IoT solutions logging and monitoring systems are often more complicated than those of standard line-of-business applications. The complexity arises from the fact that IoT solutions span:

- Physical sensors interacting with an environment.
- Applications on the intelligent edge performing activities such as data shaping and protocol translation.
- Infrastructure components such as on-premise gateways, firewalls, and switches.
- Ingestion and messaging services.
- Persistence mechanisms.
- Insight and reporting applications.
- Subsystems that operate and scale independently in the cloud.

The following simplified logging and monitoring architecture shows examples of typical IoT solution components and how they use some recommended technologies.

:::image type="content" source="media/logging-monitoring.jpg" alt-text="Diagram that shows example logging and monitoring systems." border="false" lightbox="media/logging-monitoring.jpg":::

### Implement

When you have critical applications and business processes that rely on Azure resources, you should monitor those resources for their availability and performance.

The [Azure Monitor](https://azure.microsoft.com/services/monitor/#features) service supports these requirements. This service can:

- Detect and diagnose issues across applications and dependencies with [Application Insights](/azure/azure-monitor/app/app-insights-overview).
- Correlate infrastructure issues with [VM insights](/azure/azure-monitor/vm/vminsights-overview) and [Container insights](/azure/azure-monitor/containers/container-insights-overview).
- Drill into your monitoring data with [Log Analytics](/azure/azure-monitor/logs/log-query-overview) for troubleshooting and deep diagnostics.
- Support operations at scale with [smart alerts](/azure/azure-monitor/alerts/alerts-smartgroups-overview) and [automated actions](/azure/azure-monitor/alerts/alerts-action-rules?tabs=portal).
- Create visualizations with Azure [dashboards](/azure/azure-monitor/visualize/tutorial-logs-dashboards) and [workbooks](/azure/azure-monitor/visualize/workbooks-overview).
- Collect data from [monitored resources](/azure/azure-monitor/monitor-reference) using Azure [Monitor Metrics](/azure/azure-monitor/essentials/data-platform-metrics).

### Monitor Azure IoT Hub

The **Overview** page in the Azure portal for each IoT hub includes charts that provide some usage metrics, such as the number of messages used and the number of devices connected to the IoT Hub. The information on the **Overview** page is useful but represents only a small amount of the monitoring data available for an IoT hub. Some monitoring data is collected automatically and is available for analysis as soon as you create your IoT hub. You can configure other types of data collection.

Azure IoT Hub collects the same types of monitoring data as other Azure resources as described in [Monitoring data from Azure resources](/azure/azure-monitor/essentials/monitor-azure-resource#monitoring-data).

To learn more about the metrics and logs that IoT Hub creates, see [Monitoring Azure IoT Hub data reference](/azure/iot-hub/monitor-iot-hub-reference).

### IoT Edge metrics collector

Microsoft provides a ready to use module called *Metrics Collector*. Add this first-party module to an IoT Edge deployment to collect module metrics and send them to Azure Monitor. The module code is open source and is provided as a multi-arch Docker container image that supports Linux x64, ARM32, ARM64 (version 1809). It's available in the [IoT Edge Module Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.metrics-collector?tab=overview).

Module features include:

- Collect logs from all the modules that can emit metrics using the [Prometheus data model](https://prometheus.io/docs/concepts/data_model/). While [built-in metrics](/azure/iot-edge/how-to-access-built-in-metrics) enable broad workload visibility by default, you can also use custom modules to emit scenario-specific metrics that enhance the monitoring solution.

- There are two options to send metrics from the metrics collector module to the cloud:

  - Send the metrics to Log Analytics. The collected metrics are ingested into the specified Log Analytics workspace using a fixed, native table called InsightsMetrics.

  - Send the metrics to IoT Hub. The collector module can be configured to send the collected metrics as UTF-8 encoded JSON device-to-cloud messages through the *edgeHub* module. This option unlocks monitoring of locked-down IoT Edge devices that are only allowed external access to the IoT Hub endpoint.

- The *AllowedMetrics* and *BlockedMetrics* configuration options take space or comma separated lists of metric selectors. A metric is matched to the list and included or excluded if it matches one or more metrics in either list.

You can visually explore metrics collected from IoT Edge devices using Azure Monitor workbooks. Curated monitoring workbooks for IoT Edge devices are provided as public templates:

- For devices connected to IoT Hub, from the **IoT Hub** page in the Azure portal, navigate to the **Workbooks** page in the **Monitoring** section.

- For devices connected to IoT Central, from the IoT Central page in the Azure portal, navigate to the **Workbooks** page in the **Monitoring** section.

Curated workbooks use built-in metrics from the IoT Edge runtime. They first need metrics to be ingested into a Log Analytics workspace. These views don't need any metrics instrumentation from the workload modules:

:::image type="content" source="media/edge-monitoring-workbook.gif" alt-text="An animation that shows the I o T Edge monitoring workbook in the Azure portal.":::

### Monitor updates

As with any deployment/update, you should monitor the state of deployment and device. In the Device Update for the IoT Hub service, compliance measures how many devices have installed the highest version compatible update. A device is compliant if it has installed the highest version available compatible update.

### Monitor configuration management

As with any deployment and/or update, you should have monitoring and alerting on the status of a device configuration deployment and/or update in place. Each Azure IoT configuration service, as mentioned previously, collects and stores logs and metrics in Azure Monitor. You can use this data to create alerts in Azure Monitor for sending notifications when a configuration deployment and/or update has been created, completed, or failed.

If the monitoring data provided by each of the Azure IoT configuration services isn't enough, the Azure IoT Hub service APIs offer a more granular view. See the following **Resources** section.

### Monitor automation and DevOps

DPS, Azure IoT hub configurations and IoT Edge provide continuous metrics and status updates that are the key inputs to monitor the state of CI/CD or automation script output. You can collect and analyze these metrics in a Log Analytics workspace and then define alerts.

### Resources

- [Monitor device connectivity using the Azure IoT Central Explorer](/azure/iot-central/core/howto-monitor-devices-azure-cli)
- [Monitor, diagnose, and troubleshoot Azure IoT Hub disconnects](/azure/iot-hub/iot-hub-troubleshoot-connectivity)
- [Monitoring Azure IoT Hub](/azure/iot-hub/monitor-iot-hub)
- [Azure IoT Hub Automatic Device Management Monitoring](/azure/iot-hub/iot-hub-automatic-device-management-cli#monitor-a-configuration)
- [Check Azure IoT Hub service and resource health](/azure/iot-hub/iot-hub-azure-service-health-integration)
- [Tutorial - Set up and use metrics and logs with an Azure IoT hub](/azure/iot-hub/tutorial-use-metrics-and-diags)
- [Collect and transport metrics - Azure IoT Edge](/azure/iot-edge/how-to-collect-and-transport-metrics)

## Automation and DevOps

The key benefit for organizations that are mature in using DevOps is agility - the ability to quickly sense and respond to changes in business needs and seamlessly drive through the full lifecycle of developing, deploying, and operating a solution. DevOps in the world of application software development is enabled by automation in testing, integration, and deployment. Finally, when application software changes are deployed in an infrastructure-as-code environment, the ongoing operation of deployed software is automated and managed.

In the world of IoT solutions, there are three areas where agility, DevOps and automation come into play in the development, deployment, and operation of an IoT solution:

- The automation of the IoT application software lifecycle from development through testing to deployment to IT operations.

- The IoT application software deployed in devices using Azure IoT Edge. Here, once again, you can use a combination of services available in Azure IoT Hub and Azure IoT Edge to use Azure DevOps tools and processes in automating software lifecycle aspects of an IoT solution at the edge.

- Addressing the challenge to operationally manage the IoT devices by providing operators with tools that let them collaborate and get the necessarily visibility, insights, and control to properly maintain and operate a reliable IoT solution.

### Design

Consider the following aspects when you design your automation and DevOps processes for IoT:

- **Spreading approach:** Thousands of devices are connected to single cloud endpoint such as Azure IoT Hub. Any change in configuration or software must be spread across all the devices.

- **Cross functional aspect:** Device vendors and cross-functional solution developers work together to develop and deploy the IoT solution. Embrace cross-functional teams to deliver continuously for the solution.

- **Software or configuration defined solutions:** System functionality is defined by the program or software installed on the hardware. Therefore, to change system functionality, update software instead of making hardware changes or local interventions.

- **Productivity and rapid development cycle:** A process that boosts the development cycle seamlessly across solution development cycle. Use CI/CD DevOps principles and processes.

- **Evolving business and deployment model:** The core purposes of IoT solutions are to create possibilities for different business models and pilot validation, deployment, and enhancements. DevOps provides a way to consistently deliver fresh software updates to meet the solution and business requirements.

- **Intelligence at the edge:** Connected IoT Edge devices have a lifecycle that extends beyond deploy, break and fix, and retire. Connectivity creates the opportunity to continuously add incremental innovation across the system lifecycle. DevOps puts organizations in the best position to capitalize on that opportunity.

### Implement

When you implement automation and DevOps in IoT systems, follow the device lifecycle and specific automation and DevOps requirements in each phase. The following tables describe three phases of the device lifecycle:

**Beginning of life**:

| Expectations                            | Platform feature available with code snippets                                                                                         |
|-----------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|
| Device registration (non-DPS)           | [Bulk Device updates](/azure/iot-hub/iot-hub-bulk-identity-mgmt)                                                                      |
| Device provision                        | [DPS Service: Prepare the configuration required to provide Zero touch device provisioning](/azure/iot-dps/concepts-roles-operations) |
| Device certificate and token management | [Generate SAS token or public key at device for authentication and channel security](/azure/iot-hub/iot-hub-dev-guide-sas?tabs=node)  |
| Device certificate lifecycle management | CA certificate lifecycle management (Preview feature with DPS and DigiCert)                                                           |
| Device initial configurations           | [Device Twins and device modules](/azure/iot-hub/iot-hub-automatic-device-management)                                                 |

**Middle of life:**

| Expectations                                        | Platform feature available with code snippets                                                                                                                                                       |
|-----------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Continuous device configuration management at scale | [Device Twins and device modules](/azure/iot-hub/iot-hub-automatic-device-management)                                                                                                               |
| Create a CI/CD pipeline for IoT Edge modules        | [IoT Edge CI/CD](/azure/iot-edge/how-to-continuous-integration-continuous-deployment-classic)                                                                                                       |
|  Reprovision device                                  | [DPS device reprovision](/azure/iot-dps/how-to-reprovision)                                                                                                                                         |
| SAS key generation if expired or require changes    | [Generate SAS token or public key at device for authentication and channel security](/azure/iot-hub/iot-hub-dev-guide-sas)                                                                          |
| Log and device diagnostics                          | [Analyze the device logs, Azure IoT hub supported device heartbeats and diagnostics](/azure/azure-monitor/visualize/workbooks-overview), Azure IoT Hub workbook blade with pre-configured workbooks |
| Azure IoT Edge monitoring diagnostics                     | [Collect and automate Azure IoT Edge device logs and metrics](/azure/iot-edge/how-to-collect-and-transport-metrics)                                                                                       |
| OTA device updates                                  | [Device updates](/azure/iot-hub-device-update/)                                                                                                                                                     |

**End of Life:**

| Expectations                                  | Platform feature available with code snippets                                         |
|-----------------------------------------------|---------------------------------------------------------------------------------------|
| Unenroll devices                              | [Revoke device access](/azure/iot-dps/how-to-revoke-device-access-portal)             |
| Remove any configuration specific to a device | [Device Twins and device modules](/azure/iot-hub/iot-hub-automatic-device-management) |
| Device replacement                            | Follow beginning of life steps                                                        |

## Next steps

> [!div class="nextstepaction"]
> [Performance efficiency in your IoT workload](iot-performance.md)
