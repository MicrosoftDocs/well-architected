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


# Operational excellence in an IoT workload

Given the complexity of non-functional or operational needs such as security, reliability, performance, and cost of IoT devices, perhaps the single most important factor to scaling an IoT solution and driving sustainable business value for an organization is the ability of an organization to have excellent IoT device operational capabilities. Operational excellence in an IoT workload is about enabling an organization for full visibility and control over the hardware and software components of an IoT solution, ensuring the best experience for users. Operational excellence includes making the IoT solution design, development, provisioning, monitoring, support, and maintenance practices more agile and valuable to business without increasing operational risk. The principles of operational excellence in an IoT workload are a set of considerations that help achieve superior operational practices. These principles are described in a following section.

As you consider operational excellence in an IoT workload, the shared responsibility model in cloud and hybrid scenarios also applies. This model is best described in the context of [security considerations](/azure/security/fundamentals/shared-responsibility). An organization's IoT operation comprises three different stacks of technologies: cloud, communication network, and IoT devices. Given the scale and diversity of devices in IoT solutions and the use of different types of communication networks at geographically distributed locations where devices are installed, there's a significant shift in IoT solutions in the cloud shared responsibility model. It leaves operational ownership of key elements of an IoT solution with the organization. While Microsoft has made it easy in many ways for organizations to operate these elements on their own or using third parties, the organization owns the operational responsibility for these elements.

The IoT Well-Architected Framework operational excellence guide focuses on the operational excellence aspects of the IoT devices and Azure services that uniquely address the core requirements of an IoT solution. The key factor in IoT operational excellence is the ability of an organization to plan, provision, configure, monitor, and retire IoT devices. The core Azure IoT services discussed in this guide provide an array of capabilities for an organization to accomplish these tasks effectively and efficiently and enable an organization to have excellent IoT device operational capabilities.

The communication network technology stack of an organization's IoT operation is typically handled by the organization's network operations team partnering with the organization's telecommunication operator. It's recommended that the organization coordinate with their telecommunication operator to set up and operate the wired and wireless communication network components of their IoT solutions and operations.

:::image type="content" source="media/architecture-layers.png" alt-text="Diagram that shows the layers and cross-cutting activities in the IoT architecture." border="false":::

## Prerequisites

To assess your IoT workload using the tenets found in the Microsoft Azure Well-Architected Framework, complete the Well-Architected Framework IoT operational excellence assessment. <!--TODO: Add link -->

After taking the assessment, this guide helps you address the key areas that surfaced for your solution.

## Principles

The following principles apply to the operational excellence pillar in an IoT workload:

### Solution management and provisioning

Following the best practices around this principle ensures that the IoT solution can successfully manage the automated provisioning of devices, can be integrated with other backend systems, and is designed to be used by different types of personas including solution developers, solution administrators, and operators.

### Capacity planning and scaling

Starting small and paying only for the amount of cloud services capacity needed at any point along the lifecycle of an IoT solution in production is a benefit for organizations. This requires the capability of planning for these capacity changes and scaling efficiently.

### Configuration management

Any successful enterprise IoT solution requires a strategy to handle establishing and updating a device or fleet of device's configuration. A device's configuration includes device properties, connection settings, relationships, and firmware. IoT operators require simple and reliable tools that enable them to update a device or fleet of device's configuration at any point during the device's lifetime.

### Monitoring and alerting

Use IoT solution logging, monitoring, and alerting systems to determine whether the solution is functioning as expected and to help troubleshoot what is wrong with the solution.

### Automation and DevOps

An IoT device is fundamentally a small computer with specialized hardware and software. IoT devices are often constrained on hardware such as memory. Automation and DevOps are essential to how the software is loaded, updated, and secured on IoT devices to ensure they operate effectively. To mitigate the effects of vulnerabilities or malfunctions in IoT devices, follow best practices to ensure the software is always up to date. IoT device software, and sometimes hardware, must be updated frequently. DevOps and configuration management becomes an essential part of device monitoring.

## Design for solution management and provisioning

Some key design decisions to consider include:

- Select IoT hardware that meets the business and technical requirements of the solution and define appropriate testing procedures to ensure operational reliability. The Azure Device Catalog is an ideal site to find IoT devices and other hardware to support greenfield or brownfield deployments. Devices in the catalog have been certified to seamlessly integrate with the Azure IoT platform.

- Define a remote device provisioning strategy. The Azure Device Provisioning Service is an IoT Hub helper service that enables a zero-touch, just-in-time provisioning of IoT devices in the field without requiring human intervention.

- Determine the roles that are responsible for developing, managing, and operating the IoT solution at scale. Determine the users in the organization to be assigned to those roles.

- Configure and test reliable integration with other Azure and third party services that support the backend and frontend services of the IoT application. A typical IoT solution is composed of multiple components such as ingestion, routing, data storage, and data processing. It's important to verify that all these components in the data flow are functional.

- Develop and design a management UI interface that to administer, monitor and operate the life cycle of IoT devices and to manage the overall configuration of the IoT solution. Recommended options are toeEither use Azure IoT Central's built-in management interface or build a custom UI interface.

### Implement

#### Select and test IoT Hardware

The [Azure Device Catalog](https://devicecatalog.azure.com/) site lets IoT solution developers find hardware from a list of certified partners and select the IoT devices that meet the specific requirements of the IoT solution being implemented. For a greenfield project, there's more flexibility regarding the types of devices that can be used, the required firmware/connectivity features and the technical specification for the IoT devices. For brownfield projects, there are typically more restrictions when you select hardware (because hardware has already been deployed in the field) but you might still need to look for other type of hardware such as protocol/identity translation devices or connectivity gateways - such as Bluetooth to MQTT gateway - depending on the specific scenario. For either case, the Azure Device Catalog has search and filter capabilities that you can use to find IoT hardware that's been certified by the IoT device certification program and are compatible with Azure IoT services.

An important feature to look for when selecting hardware is Azure Plug-and-Play and the Digital Twins Definition Language (DTDL) compatibility. These features ensure the device integrates seamlessly with services such as Azure IoT Central and Azure Digital Twins. For Azure IoT Edge scenarios, it's also important to find devices in the catalog that have the [Edge Managed](/azure/certification/program-requirements-edge-managed) certification that guarantees the device can run the Azure IoT Edge runtime and enables the deployment and management of IoT Edge Modules to support local processing and analytical workloads.

#### Provision and manage the lifecycle of IoT devices

For remote provisioning of IoT devices, [Azure Device Provisioning Service (DPS)](/azure/iot-dps/about-iot-dps) enables connecting and configuring remote devices to an IoT Hub. DPS enables zero-touch provisioning without hardcoding information at the factory and it enables load-balancing of devices across multiple IoT Hubs. Although DPS supports symmetric key attestation, in a production environment, you should use either the X.509 certificate or TPM attestation mechanisms. If you use X.509 certificates, the root certificate (or an intermediate certificate that has been signed by the root certificate) should be deployed to DPS to allow devices in the field to properly authenticate to the service and assigned to their corresponding IoT hub.

Part of an IoT solution lifecycle includes reprovisioning devices in the field and/or moving them between IoT hubs. DPS enables the configuration of [reprovisioning policies](/azure/iot-dps/how-to-reprovision) that determine the expected behavior when an IoT device submits a new provisioning request. Devices should be programmed to send a provisioning request on reboots and should implement a method to manually trigger a provisioning on demand. This ensures that every time the device boots, it always contacts DPS before getting redirected to the appropriate IoT hub.

#### Determine the list of roles and users for the IoT solution

A key decision to make early in the IoT solution design phase is to determine the list of roles that implement and manage the solution along with the corresponding responsibilities. Ideally, the solution should trust a centralized identity provider, such as [Azure AD](/azure/active-directory/fundamentals/active-directory-whatis), and only let the appropriate users in those roles perform any management and/or operation activities such as creating and provisioning new devices, sending commands to hardware in the field, deploying updates, and modify user permissions. If the solution uses IoT Central, there are built-in roles that you can assign to existing users in Azure AD eliminating the need to develop a customized identity service. In a solution backed by IoT Hub, you can use Azure AD to authenticate requests to Azure IoT Hub service APIs, such as creating device identities or invoking direct methods. You can develop a custom management UI interface for solution operators and administrators can be developed that authenticates users against Azure AD and executes the API requests to the IoT solution backend on behalf of those users.

#### Integration with back-end systems

A successful IoT implementation requires the proper integration of IoT services such as IoT Hub and DPS with other backend and frontend services, both Azure or third party. Although the configuration of those other services is out of the scope of this document, it's important to document and have a good understanding of the entire data flow of the IoT solution and to have testing procedures in place to ensure the different parts of the solution are working as expected and meeting the technical and operational requirements of the organization. For example, DPS supports custom allocation policies by using custom code and Azure Functions and it is, therefore, important to confirm that the Azure Function allows traffic coming from DPS and IoT Hub. Another example is the integration between IoT Hub and backend services to enable features such as message routing and file upload. IoT Hub needs to properly authenticate to those Azure services and you should use managed identities to eliminate the need for developers to manage those credentials manually. Integration of Azure IoT services with other services should be tested periodically to ensure that the IoT solution is performing as expected and meeting the operational SLAs defined by the business. The [Azure Architecture Center](/azure/architecture/) contains many IoT reference architectures that outline and describe the proper and secure integration of the Azure IoT platform with other services.

#### Design a management interface

Solution operators and administrators need an interface to interact with the IoT solution. If an IoT solution is based on IoT Central, an easy-to-use management interface is already built into the application enabling operators and administrators to complete tasks such as provisioning new devices, adding/removing users, sending commands to IoT devices, and managing device updates. If the UI management interface provided by IoT Central doesn't meet the requirements of the solution or the solution is based on PaaS services such IoT Hub, then you can build a custom management UI that uses the service REST APIs exposed by IoT Hub and IoT Central.

### Resources

- [Azure Device Catalog](https://devicecatalog.azure.com/)
- [Azure Device Provisioning Service](/azure/iot-dps/about-iot-dps)
- [DPS X.509 Attestation](/azure/iot-dps/concepts-x509-attestation)
- [Manage Users and Roles in IoT Central](/azure/iot-central/core/howto-manage-users-roles)
- [Azure IoT Edge Managed Certification Requirements](/azure/certification/program-requirements-edge-managed)
- IoT Hub Support for Managed Identities
- [Controlling access to Azure IoT Hub by using Azure AD](/azure/iot-hub/iot-hub-dev-guide-azure-ad-rbac)
- [Azure IoT Reference Architecture](/azure/architecture/reference-architectures/iot)

## Design for capacity planning and scaling

Azure offers many options to meet capacity requirements as your business grows. Capacity planning and scaling for your IoT solution varies depending on choice to build an IoT Central based solution or an IoT Hub solution.

When you implement an IoT solution by using the application-platform-as-a-service (aPaaS) IoT Central, little needs to be done. IoT Central automatically manages multiple instances of its underlying services to scale your IoT Central applications and make them highly available. However, since IoT Central only stores 30 days of data, you're likely to export data to other services. These other services are where you should focus your time on making sure the solution can handle expected and unexpected capacity needs.

With an IoT Hub based solution, the solution owner has responsibility for scaling up to handle growth in the number of messages being ingested and to scale out to handle regional demands for the solution.

You should plan for resources at the edge. Whether you're managing RTOS based devices or larger compute devices with IoT Edge, there are many factors to take into account to make sure the sizing of compute and memory are adequate for the use-cases.

### Implement

This section focuses on IoT Hub due to the managed nature of IoT Central. However, when you consider scaling you should include the resources that handle the data IoT Central exports.

If your solution is tied to a connected product, the business must communicate any fluctuation in expected load. Marketing initiatives and seasonal events can affect load. Test variations of load prior to events, including unexpected ones, to ensure that your IoT solution can scale. Ensure that all regions can adequately scale to support total load, should one region fail and devices reprovision to another region.

### Resources

- [Azure IoT Hub scaling](/azure/iot-hub/iot-hub-scaling)
- [Azure IoT Hub high availability and disaster recovery](/azure/iot-hub/iot-hub-ha-dr)
- [Plan for capacity - Microsoft Azure Well-Architected Framework](/azure/architecture/framework/scalability/design-capacity)

## Design for configuration management

An IoT solution's scale and specific use of a device's configuration influences the design of a configuration management strategy. It's important to automate this strategy as much as possible and ensure a device or fleet of device's configuration is set and updated efficiently as well.

A configuration management strategy should support:

- Gradual update rollout through device grouping
- Resilient updates to support testing and rollbacks
- Automatic updates for existing or new devices
- Update status reports and alerts
- Performant updates that can scale

### Implement

Example services that support these requirements for configuration management are Azure IoT Hub Automatic Device Management, Azure IoT Edge Automatic Deployment, Azure IoT Hub Scheduled Jobs, and Device Update for IoT Hub.

For continuously updates to existing or new device configuration, such as properties, application specific settings, and/or relationships, use either Azure IoT Hub Automatic Device Management or Azure IoT Edge Automatic Deployment. Both services enable an efficient, secure, and reliable way to automate configuration deployments for either a fleet or specific group of devices. Both services continuously monitor all new and existing targeted devices, based on tags, and their configuration to ensure these devices always have a specified configuration. The key difference between these services is that Automatic Device Management only applies to non-Azure IoT Edge devices and Azures IoT Edge Automatic Deployment only applies to Azure IoT Edge devices.

For updating an existing device or IoT Edge device configuration, such as properties, application specific settings, and/or relationships, based on a schedule, whether this is onetime or recurring, use Azure IoT Hub scheduled jobs. This service enables an efficient, secure, and reliable way to provide a configuration update for either a fleet or specific group of devices at a scheduled time.

For updating an existing device or IoT Edge device configuration, such as firmware, application, or package updates, use Device Update for IoT Hub. This service enables a safe, secure, and reliable way to provide firmware, application, or package updates over-the-air (OTA) to either a fleet or specific group of devices.

### Resources

- [Azure IoT Hub Automatic Device Management](/azure/iot-hub/iot-hub-automatic-device-management#create-a-configuration)
- [Azure IoT Edge Automatic Deployment](/azure/iot-edge/module-deployment-monitoring)
- [Azure IoT Hub Scheduled Jobs](/azure/iot-hub/iot-hub-devguide-jobs#jobs-to-update-device-twin-properties)
- [Device Update for IoT Hub](/azure/iot-hub-device-update/understand-device-update)

## Design for monitoring and alerts

Monitoring and logging systems help to answer the following operational questions:

- Are devices or systems in an error condition?
- Are devices or systems correctly configured?
- Are devices or systems generating accurate data?
- Are systems meeting the expectations of both the business and end customers?

If the answer is no, these systems surface relevant information for operations teams to help mitigate problems. IoT solutions logging and monitoring systems are often more complicated than those of standard line-of-business applications. The complexity arises from the fact that IoT solutions span:

- Physical sensors interacting with an environment.
- Applications on the intelligent edge performing activities such as data shaping and protocol translation.
- Infrastructure components such as on-premise gateways, firewalls, and switches.
- Ingestion and messaging services.
- Persistence mechanisms.
- Insight and reporting applications.
- Subsystems that operate and scale independently in the cloud.

The following simplified logging and monitoring architecture shows examples of typical IoT solution components and how they use some recommended technologies.

:::image type="content" source="media/logging-monitoring.jpg" alt-text="Diagram that shows example logging and monitoring systems." border="false":::

### Implement

When you have critical applications and business processes that rely on Azure resources, you should monitor those resources for their availability and performance.

The [Azure Monitor](https://azure.microsoft.com/services/monitor/#features) service supports these requirements. This service can:

- Detect and diagnose issues across applications and dependencies with [Application Insights](/azure/azure-monitor/app/app-insights-overview).
- Correlate infrastructure issues with [VM insights](/azure/azure-monitor/vm/vminsights-overview) and [Container insights](/azure/azure-monitor/containers/container-insights-overview).
- Drill into your monitoring data with [Log Analytics](/azure/azure-monitor/logs/log-query-overview) for troubleshooting and deep diagnostics.
- Support operations at scale with [smart alerts](/azure/azure-monitor/alerts/alerts-smartgroups-overview) and [automated actions](/azure/azure-monitor/alerts/alerts-action-rules?tabs=portal).
- Create visualizations with Azure [dashboards](/azure/azure-monitor/visualize/tutorial-logs-dashboards) and [workbooks](/azure/azure-monitor/visualize/workbooks-overview).
- Collect data from [monitored resources](/azure/azure-monitor/monitor-reference) using Azure [Monitor Metrics](/azure/azure-monitor/essentials/data-platform-metrics).

### Monitor

#### Monitor updates

As with any deployment/update, you should monitor the state of deployment and device. In the Device Update for IoT Hub service, compliance measures how many devices have installed the highest version compatible update. A device is compliant if it has installed the highest version available compatible update.

#### Monitor configuration management

As with any deployment and/or update, you should have monitoring and alerting on the status of a device configuration deployment and/or update in place. Each Azure IoT configuration service, mentioned previously, collects and stores logs and metrics in Azure Monitor. You can use this data to create alerts in Azure Monitor for sending notifications when a configuration deployment and/or update has been created, completed, or failed.

If the monitoring data provided by each of the Azure IoT configuration services isn't enough, the Azure IoT Hub service APIs offer a more granular view. See the following **Resources** section.

#### Monitor automation and DevOps

DPS, Azure IoT hub configurations and IoT Edge provide continuous metrics and status updates that are the key inputs to monitor the state of CI/CD or automation script output. You can collect and analyze these metrics in a Log Analytics workspace and then define alerts.

### Resources

Azure IoT Central:

- [Create and manage rules in your Azure IoT Central application](/azure/iot-central/core/tutorial-create-telemetry-rules)
- [Monitor device connectivity using the Azure IoT Central Explorer](/azure/iot-central/core/howto-monitor-devices-azure-cli)
- [Azure IoT Central application management guide](/azure/iot-central/core/overview-iot-central-admin#monitor-application-health)

Azure IoT Hub:

- [Monitoring Azure IoT Hub](/azure/iot-hub/monitor-iot-hub)
- [Monitoring Azure IoT Hub data reference](/azure/iot-hub/monitor-iot-hub-reference)
- [Azure IoT Hub Automatic Device Management Monitoring](/azure/iot-hub/iot-hub-automatic-device-management-cli#monitor-a-configuration)
- [Add correlation IDs to IoT messages w/distributed tracing (pre)](/azure/iot-hub/iot-hub-distributed-tracing)
- [Check Azure IoT Hub service and resource health](/azure/iot-hub/iot-hub-azure-service-health-integration)
- [Order device connection events from Azure IoT Hub w/Azure Cosmos DB](/azure/iot-hub/iot-hub-how-to-order-connection-state-events)
- [Manage connectivity and reliable messaging by using Azure IoT Hub device SDKs](/azure/iot-hub/iot-hub-reliability-features-in-sdks)

Azure Device Provisioning Service:

- [Diagnose and troubleshoot disconnects with Azure IoT Hub DPS](/azure/iot-dps/how-to-troubleshoot-dps)
- [Tutorial - Set up and use metrics and logs with an Azure IoT hub](/azure/iot-hub/tutorial-use-metrics-and-diags)

Azure IoT Edge:

- [Collect and transport metrics - Azure IoT Edge](/azure/iot-edge/how-to-collect-and-transport-metrics)
- [Explore curated visualizations - Azure IoT Edge](/azure/iot-edge/how-to-explore-curated-visualizations)
- [Get notified about issues using alerts - Azure IoT Edge](/azure/iot-edge/how-to-create-alerts)
- [Azure IoT Edge Deployment Monitoring](/azure/iot-edge/how-to-monitor-iot-edge-deployments#monitor-a-deployment-with-azure-cli)

Azure Monitor:

[Monitor, diagnose, and troubleshoot Azure IoT Hub disconnects](/azure/iot-hub/iot-hub-troubleshoot-connectivity)

## Design for automation and DevOps

Three key considerations for DevOps in IoT solutions are:

- Automated integration testing, security testing, and deployment. DevOps systems require checking security about the information emitting from sensors. These entry points are easily compromised, for example someone could hack your thermostat and transfer misleading data. The security testing tools of DevOps should scan the IoT application to make sure that the exposure is reduced.

- Continuous integration ensures that IoT applications that belong to DevOps are considered as a holistic system. All the constituent parts depend upon one another, even components that aren't under direct control such as sensors that are embedded in machines. This differs from a traditional DevOps environment where all the constituent parts are under direct control.

- Deployment is the most significant component when it comes to DevOps for IoT. Deployment processes are complicated because parts of the application reside in the cloud and parts are remote devices. For example, some application updates are made on the cloud platform, and firmware updates are made to devices or remote sensors.

Other aspects to consider include:

- **Spreading approach:** Thousands of devices are connected to single cloud endpoint such as Azure IoT Hub. Any change in configuration or software must be spread across all the devices.

- **Cross functional aspect:** Device vendors and cross-functional solution developers work together to develop and deploy the IoT solution. Embrace cross-functional teams to deliver continuously for the solution.

- **Software defined solutions:** System functionality is defined by the program or software installed on the hardware. Therefore, to change system functionality, update software instead of making electrical and mechanical changes.

- **Productivity and rapid development cycle:** A process that boosts the development cycle without affecting the quality through smarter, faster, and better ways that can also reduce the cost of production.

- **Evolving business and deployment model:** The core purposes of IoT solutions are to create possibilities for different business models and pilot validation, deployment, and enhancements. DevOps provides a way to consistently deliver fresh software updates to meet the solution and business requirements.

- **Intelligence at the edge:** Connected IoT Edge devices have a lifecycle that extends beyond deploy, break and fix, and retire. Connectivity creates the opportunity to continuously add incremental innovation across the system lifecycle. DevOps puts organizations in the best position to capitalize on that opportunity.

### Implement

When you implement automation and DevOps in IoT systems, follow the device lifecycle and specific automation and DevOps requirements in each phase. The following tables describe three phases of the device lifecycle.

**Beginning of life**:

| Expectations                            | Platform feature available with code snippets                                                                                         | Automation and DevOps - aspect                                           |
|-----------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------|
| Device registration (non-DPS)           | [Bulk Device updates](/azure/iot-hub/iot-hub-bulk-identity-mgmt)                                                                      | Automated script to register device in bulk and help to provision device |
| Device provision                        | [DPS Service: Prepare the configuration required to provide Zero touch device provisioning](/azure/iot-dps/concepts-roles-operations) | Automated script to provision, reprovision, and revoke                   |
| Device certificate and token management | [Generate SAS token or public key at device for authentication and channel security](/azure/iot-hub/iot-hub-dev-guide-sas?tabs=node)  | Automated script generates SAS token or X.509 public key for devices      |
| Device certificate lifecycle management | CA certificate lifecycle management (Preview feature with DPS and DigiCert)                                                           | Automated script to trigger or monitor certificate lifecycle             |
| Device initial configurations           | [Device Twins and device modules](/azure/iot-hub/iot-hub-automatic-device-management)                                                 | Automated script to set new devices with unified configurations          |

**Middle of life:**

| Expectations                                        | Platform feature available with code snippets                                                                                                                                                       | Automation and DevOps - aspect                                                                                                                        |
|-----------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| Continuous device configuration management at scale | [Device Twins and device modules](/azure/iot-hub/iot-hub-automatic-device-management)                                                                                                               | Automated script to ensure continuous deployment of configuration change to device twins and modules. CI/CD Pipeline to deliver changes continuously. |
| Create a CI/CD pipeline for IoT Edge modules        | [IoT Edge CI/CD](/azure/iot-edge/how-to-continuous-integration-continuous-deployment-classic)                                                                                                       | DevOps CI/CD pipeline. Approach: Normal or layered deployment.                                                                                        |
|  Reprovision device                                  | [DPS device reprovision](/azure/iot-dps/how-to-reprovision)                                                                                                                                         | Automated script to reprovision                                                                                                                       |
| SAS key generation if expired or require changes    | [Generate SAS token or public key at device for authentication and channel security](/azure/iot-hub/iot-hub-dev-guide-sas)                                                                          | Automated script generates SAS token or X.509 public key for devices                                                                                   |
| Log and device diagnostics                          | [Analyze the device logs, Azure IoT hub supported device heartbeats and diagnostics](/azure/azure-monitor/visualize/workbooks-overview), Azure IoT Hub workbook blade with pre-configured workbooks | Automated workbooks with scripts. Use the predefined workbooks associated with Azure IoT hub.                                                    |
| Azure IoT Edge monitoring diagnostics                     | [Collect and automate Azure IoT Edge device logs and metrics](/azure/iot-edge/how-to-collect-and-transport-metrics)                                                                                       | Ensure CI/CD deployment of metrics collector module for all the devices and automated workbooks with scripts.                                         |
| OTA device updates                                  | [Device updates](/azure/iot-hub-device-update/)                                                                                                                                                     | Automated script and CI/CD pipeline for OTA                                                                                                           |

**End of Life:**

| Expectations                                  | Platform feature available with code snippets                                         | Automation and DevOps - aspect                                            |
|-----------------------------------------------|---------------------------------------------------------------------------------------|---------------------------------------------------------------------------|
| Unenroll devices                              | [Revoke device access](/azure/iot-dps/how-to-revoke-device-access-portal)             | Automated script to unenroll devices                                      |
| Remove any configuration specific to a device | [Device Twins and device modules](/azure/iot-hub/iot-hub-automatic-device-management) | CI/CD Pipeline to delete changes immediately after a device is unenrolled |
| Device replacement                            | Follow beginning of life steps                                                        |

### Resources

- [Monitor IoT Edge with workbooks](/azure/iot-edge/tutorial-monitor-with-workbooks)
- [Use IoT Hub metrics](/azure/iot-hub/tutorial-use-metrics-and-diags)

## Next Steps

<!-- TODO: update this link -->
IoT Well-Architected Framework for IoT - reliability.
