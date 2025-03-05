---
title: Architecture Best Practices for Azure IoT Hub
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for IoT Hub.
author: asergaz
ms.author: sergaz
ms.topic: conceptual
ms.date: 02/18/2025
ms.service: azure-waf
ms.subservice: waf-service-guide
ai-usage: ai-assisted
products:
 - azure-iot-hub
azure.category:
 - iot
---

# Architecture best practices for Azure IoT Hub

Azure IoT Hub is a managed cloud-based service that serves as a central message hub for communication between an IoT application and its connected devices. You can reliably connect almost any device and its back-end solutions to an IoT hub at scale.

This article assumes that as an architect, you've reviewed the [technologies and services that you can use to create IoT solutions](/azure/iot/iot-services-and-technologies) and chose [IoT Hub](/azure/iot-hub/iot-concepts-and-iot-hub) as the Azure IoT platform service for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](/azure/well-architected/pillars).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope. 
>
> Also included are recommendations for the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations that are available for IoT Hub and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or to optimize your existing environments.

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:  

- Azure IoT Hub
- Azure IoT Hub Device Provisioning Service (DPS)
- Azure Device Update for IoT Hub
- Azure IoT Edge
- Azure Digital Twins
- Azure Sphere
- Microsoft Defender for IoT

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures.**

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the performance of IoT Hub. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Design devices for resiliency.** Design your devices to satisfy the uptime and availability requirements of your end-to-end solution. Ensure that your IoT device can operate efficiently with intermittent connectivity to the cloud.
>
> - **Design for business requirements.** Consider the cost implications of introducing architectural modifications to meet service-level agreements. For example, to increase reliability and high availability, you might implement cross-region redundancies and an autoscale system. Carefully consider the tradeoffs.
>
> - **Implement safe, simple update procedures.** Create a strategy for operators to manage devices in your enterprise IoT solution. IoT operators require simple and reliable update tools and practices.
>
> - **Observe application health.** Define service-level indicators (SLIs) and service-level objectives (SLOs) based on observability. Add processes for auditing, monitoring, and alerting in addition to the processes included in IoT Hub.
>
> - **Implement high availability and disaster recovery for critical components.** Plan for resilient hardware and software components that increase redundancy, including cross-region redundancies.
>
> - **Plan for capacity.** Plan for service quotas and throttles, and account for latency that occurs between detection and action. Establish benchmarks at the production scale to support uninterrupted data flow.

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Calculate your required IoT Hub capacity, such as the number of messages per day and other limits. If your workload has fluctuating capacity requirements, implement an autoscaling mechanism so that it increases and decreases capacity based on demand. For more information, see [Choose the right IoT Hub tier and size for your solution](/azure/iot-hub/iot-hub-scaling). | Dynamic scaling helps optimize resource usage. It helps ensure that your solution remains responsive and reliable without overprovisioning resources. |
| Add a device reconnection strategy to IoT Hub to design resilient applications. For more information, see [Manage device reconnections](/azure/iot/concepts-manage-device-reconnections). | IoT devices often rely on intermittent or unstable network connections. A reconnection strategy enables your workload to recover without user intervention, which increases availability. |
| Evaluate the tradeoffs of different [high availability and disaster recovery options](/azure/iot-hub/iot-hub-ha-dr) in IoT Hub. Depending on your IoT solution's uptime goals, determine the options that best suit your business objectives. Choose between [Microsoft-initiated failover](/azure/iot-hub/iot-hub-ha-dr#microsoft-initiated-failover), [manual failover](/azure/iot-hub/iot-hub-ha-dr#manual-failover), and [cross-region high availability](/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha).|  A failover strategy helps the workload quickly switch to a backup instance, which minimizes the impact on your users and business operations.|
| Use [DPS](/azure/iot-dps/about-iot-dps) to provision your devices and assign them to an IoT hub. | DPS is a helper service that enables zero-touch, just-in-time provisioning without requiring human intervention. DPS enables the provisioning of millions of devices in a highly secure and scalable manner. |
| Use [Device Update for IoT Hub](/azure/iot-hub-device-update) to manage over-the-air updates for your IoT devices. Ensure that your update strategy includes gradual rollout, resilient A/B updates, detailed management, and reporting tools. | This approach helps ensure safe, secure, and reliable updates for IoT devices, which reduce downtime and improve operational efficiency. It helps maintain device compliance and quickly identifies and resolves update failures, which enhances overall system reliability. |
| Implement DevOps practices to manage IoT solutions, including continuous integration and continuous deployment (CI/CD), monitoring, and automated updates. <br><br> - Use [DevOps to build and release your IoT Edge applications](/azure/iot-edge/how-to-continuous-integration-continuous-deployment). <br> - Use [Azure Monitor to monitor and get alerts from the data that IoT Hub collects](/azure/iot-hub/monitor-iot-hub). <br> - Use [automatic device management in IoT Hub to automate the management of devices at scale](/azure/iot-hub/iot-hub-automatic-device-management). | DevOps practices enhance the reliability and efficiency of IoT solutions. They enable rapid deployment, continuous monitoring, and automated updates. These features reduce downtime, improve system performance, and deploy updates and changes safely and consistently. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of IoT Hub.

### Design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Use a strong identity to authenticate devices and users.** Implement practices to enhance security. For example, you should:
>    - Have a hardware root of trust for trusted identity.
>    - Register devices.
>    - Issue renewable credentials.
>    - Use passwordless or multi-factor authentication (MFA).
>
> - **Automate and use least-privileged access control.** Limit the impact of compromised devices or identities, or unapproved workloads.
>
> - **Evaluate your device health.** Evaluate your device health to control access or identify devices for remediation. Check security configurations, assess vulnerabilities and insecure passwords, monitor for threats and anomalies, and build ongoing risk profiles.
>
> - **Implement device updates.** Implement continuous updates to keep devices healthy. Use a centralized configuration and compliance management solution and a robust update mechanism to ensure that devices are up-to-date and healthy.
>
> - **Monitor system security, and plan incident response.** Proactively monitor for unauthorized or compromised devices, and respond to emerging threats.
>
> - **Ensure highly secure connections.** Ensure that all data that's transmitted between the IoT device and the IoT cloud services is confidential and tamperproof.

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Apply Zero Trust criteria for devices. Devices that connect to IoT Hub should: <br> - Contain a [hardware security module](/azure/iot-dps/concepts-tpm-attestation) for strong identity. <br> - Use [renewable credentials](/azure/iot-dps/how-to-roll-certificates). <br> - Enforce least-privileged access control. <br> - Emit proper health signals for conditional access. <br> - Alert operators to [revoke a device from IoT Hub when compromised](/azure/iot-dps/how-to-revoke-device-access-portal).<br><br> Your devices should include: <br> - Update agents for security updates. <br> - Device management capabilities for cloud-driven configuration and automated security response. <br> - Only necessary features enabled to minimize the physical attack footprint. <br> - [Data protection at rest via standard encryption algorithms](/azure/iot-hub/iot-hub-tls-support). | Zero Trust criteria for devices that connect to IoT Hub enhances security and reliability.<br><br> Hardware security modules, renewable credentials, and least-privileged access control minimize unauthorized access and compromised devices.<br><br> Health signals for conditional access and update agents help ensure that devices remain secure and compliant. Cloud-driven device management and automated security response, along with security agents, strengthen the security posture.<br><br> A small physical attack footprint and data protection at rest safeguard sensitive information and help maintain system integrity.  |
| Use X.509 certificates to authenticate your devices to IoT Hub. For more information, see [Authenticate identities with X.509 certificates](/azure/iot-hub/authenticate-authorize-x509). | X.509-based authentication in production environments provides greater security compared to security tokens. |
| Layer least-privileged access for IoT devices. Use [network segmentation](/security/benchmark/azure/baselines/iot-hub-security-baseline#network-security) to group IoT devices. This practice mitigates the impact of a potential compromise.<br><br> For example, you can connect IoT devices to an "IoT network" for devices, such as printers, VoIP phones, and smart TVs. Keep this IoT network separate from other organizational resources that your workforce accesses.<br><br>Use network micro-segmentation to logically separate dedicated operations technology environments from the corporate information technology network. To create this separation, use a perimeter network architecture that includes firewalls. Mature organizations can also implement micro-segmentation policies at multiple layers of the Purdue model, typically by using next-generation firewalls. | Network segmentation groups IoT devices, which can mitigate the impact of a compromise. <br><br> Network micro-segmentation isolates less-capable devices at the network layer, either behind a gateway or on a discrete network segment.  |
| Use [Microsoft Defender for IoT](/azure/defender-for-iot/organizations/overview) as the frontline of defense to help protect your resources in Azure. <br><br> Defender for IoT is an agentless, network layer security platform that delivers continuous asset discovery, vulnerability management, and threat detection for IoT devices. | Defender for IoT provides continuous monitoring and threat detection to quickly identify unauthorized or compromised devices. It enhances the overall security and resilience of the IoT network. |
| Use IoT Hub with [Azure Sphere](/azure-sphere) as a guardian module to help secure other devices, including existing legacy systems that you can't rely on for trusted connectivity. <br><br>An Azure Sphere guardian module deploys with an application and interfaces with existing devices through Ethernet, serial, or Bluetooth Low Energy (BLE). The devices don't necessarily have direct internet connectivity. |The Azure Sphere guardian module provides a way to implement highly secure connectivity in existing devices without exposing those devices to the internet. This protection includes encrypted data transmission, highly secure OS and application updates, and authentication to ensure communication only with trusted hosts. |
| Use [IoT Edge gateways](/azure/iot-edge/iot-edge-as-gateway) to enforce strong identity patterns for less-capable devices. <br><br> IoT Edge provides an edge runtime connection to IoT Hub and supports certificates as strong device identities. IoT Edge supports the PKCS#11 standard for device manufacturing identities and other secrets on a Trusted Platform Module (TPM) or hardware security module. | IoT Edge gateways help enforce strong identity patterns so that only authenticated and authorized devices can communicate within the network. This feature enhances the security and integrity of your IoT ecosystem.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to IoT Hub and its environment.

### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Develop cost-management discipline.** To understand the total cost of ownership (TCO), account for both functional and nonfunctional costs in your plan.
>
> - **Use industry-standard strategies and approaches.** For IoT-specific industries like manufacturing, use [industry-standard strategies and approaches](/azure/digital-twins/concepts-ontologies) to build structures, IoT systems, smart cities, or energy grids. This approach helps optimize costs.
>
> - **Understand the cost to protect your IoT solution.** IoT covers the entire process, from hardware to cloud services, so you incur security-related expenses at every stage. For example, you can incur costs for microprocessor units (MPUs), devices, telecommunication, cloud technology, and operation technology.
>
> - **Design for rate optimization.** Define implementation plans for each [IoT solution-wide concern](/azure/iot/iot-introduction#solution-wide-concerns) to optimize costs.
>
> - **Monitor and optimize over time.** Continuously monitor and [optimize IoT Hub costs](/azure/iot-hub/iot-hub-devguide-pricing). Do ongoing cost optimization activities after you implement your solution.
>
> - **Estimate realistic costs.** Select the [appropriate IoT Hub tier and size](/azure/iot-hub/iot-hub-scaling) for the development and operational phases of your IoT solution.
>
> - **Evaluate one-time costs versus recurring costs.** Consider the cost benefits of one-time costs versus recurring costs. For example, hacking techniques continuously evolve, so you might use a reliable commercial operating system and module such as Azure Sphere. For a one-time payment, such services provide ongoing monthly device security patches.
>
> - **Optimize resource usage.** Implement [autoscaling mechanisms](/azure/iot-hub/iot-hub-scaling#auto-scale) to adjust the IoT Hub capacity based on demand. This approach helps ensure efficient resource usage and cost control.
>
> - **Use reserved capacity.** Evaluate the [size and frequency of message payloads](/azure/iot-hub/iot-hub-devguide-pricing#charges-per-operation) to ensure that your IoT Hub is appropriately sized and prepared to scale.
>
> - **Implement cost alerts and budgets.** Set up cost alerts and budgets in Microsoft Cost Management to effectively track and control your spending. Use cost management APIs for [IoT Hub](/rest/api/iothub) and [DPS](/rest/api/iot-dps).

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Understand [IoT Hub quotas and throttling limits](/azure/iot-hub/iot-hub-devguide-quotas-throttling) and how they impact the TCO when your solution runs at scale in production.<br><br>Your cost model should account for devices, infrastructure, operations, and monitoring at scale in production. | Each service has its own set of quotas and throttling limits. Understand these limits in IoT Hub to optimize costs. |
| Understand the device ecosystem and the various roles involved, such as hardware manufacturers, app developers, and operators for planning. Devices can range from small 8-bit MCUs to advanced x86 CPUs, like those found in desktop computers. For more information, see [Best practices for device configuration within an IoT solution](/azure/iot-hub/iot-hub-configuration-best-practices).<br><br> The hardware that you choose depends on many variables. Understand the [key hardware differentiators](/azure/iot/concepts-iot-device-types#key-hardware-differentiators) in an IoT device to reduce costs.| Understanding the range of devices, and the key hardware differentiators, facilitates allocating resources more effectively. This ensures that you are using the right devices for the right tasks, leading to better performance and cost efficiency.|
| Use [IoT Plug and Play](/azure/iot/overview-iot-plug-and-play) to reduce hardware development and on-boarding time. Adopt a [Digital Twin Definition Language (DTDL) industry ontology](/azure/digital-twins/concepts-ontologies-adopt), which is a model that a device uses to advertise its capabilities to an IoT Plug and Play-enabled application. | Solution builders can use IoT Plug and Play to integrate IoT devices with IoT Hub without manual configuration. |
| Use [Device Update for IoT Hub](/azure/iot-hub-device-update/understand-device-update) to deliver over-the-air updates and remotely manage your devices. | A solution to remotely update the device's firmware or software helps reduce long-term manual labor costs. |
| Use connectors for Azure services, such as Power Apps, Power Automate, and Azure Logic Apps. <br><br>IoT Hub and Azure Event Grid together [provide connectors](/connectors/azureeventgrid/) to implement your requirements within Logic Apps and Power Automate. | Connectors enable seamless integration between IoT devices and various Azure services, which enables automated workflows and enhanced data processing capabilities.|
| Use [device simulators](/azure/iot-hub/tutorial-connectivity) and set up a load testing environment to test the solution at production scale and understand the solution cost.<br><br>Simulation code samples like the [Azure IoT Device Telemetry Simulator](/samples/azure-samples/iot-telemetry-simulator/azure-iot-device-telemetry-simulator/) help you test and estimate costs at scale with various parameters.| Device simulators can help save costs at scale to model, test, and simulate outcomes for a scenario. |
| To reduce transmission sizes and costs: <br> - [Choose the right protocol](/azure/iot-hub/iot-hub-devguide-protocols) for your IoT devices. <br> - Compress telemetry at the edge. <br> - [Batch messages](/azure/iot-hub/iot-hub-devguide-file-upload) on the device. <br> - Choose between keeping connections alive or reconnecting when devices wake up. <br> - Use the [device twin feature](/azure/iot-hub/monitor-device-connection-state) to exchange status information asynchronously if cost is critical. <br><br> For battery-powered IoT devices, you can choose between keeping connections alive or reconnecting when the devices wake up. Use keep-alive messages, or heartbeat messages, to check the device status, but consider the extra network costs of transmission. <br><br> Reconnecting uses about 6 KB of data to establish a TLS connection, authenticate the device, and retrieve a device twin. But it conserves battery capacity if the device wakes up only once or twice a day. | An optimal protocol for your scenario enables devices to reduce transmission sizes and costs in the transport layer. |
| Understand how to use hot, warm, and cold path analytics for IoT data, and apply the [lambda architecture](/azure/architecture/databases/guide/big-data-architectures#lambda-architecture). Use the built-in [message routing](/azure/iot-hub/iot-hub-devguide-messages-d2c) feature in IoT Hub. | IoT solutions can store large amounts of data. Storage costs make up a large part of the overall solution cost. Choose an appropriate processing and storage plan based on your business scenario. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management.**

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to IoT Hub.

> [!div class="checklist"]
>
> - **Embrace continuous operations and scaling.** Ensure that the IoT solution can:
>    - Successfully manage automated device provisioning.
>    - Integrate with other back-end systems.
>    - Support different roles, such as solution developers, solution administrators, and operators.
>    - Adapt and scale efficiently to changes on demand, such as newly deployed IoT devices or higher ingestion throughput.
>
> - **Optimize build and release processes.** A successful enterprise IoT solution requires a strategy to establish and update a device or a fleet of device's configuration. A device's configuration includes device properties, connection settings, relationships, and firmware. IoT operators require simple and reliable tools to update the configuration of a device or fleet of devices at any point during the device's lifetime.
>
> - **Understand operational health.** Use IoT solution logging, monitoring, and alerting systems to determine if the solution is functioning as expected and to help troubleshoot problems throughout the lifecycle of the solution.
>
> - **Use automation and DevOps.** An IoT device is essentially a small computer that has specialized hardware and software. IoT devices are often constrained in hardware. For example, they might have limited memory or compute capacity. Automation and DevOps help ensure the proper upload and deployment of OS and software to IoT devices and gateways, which minimize operational downtime. Use automation and DevOps to monitor and manage the lifecycle of IoT devices.

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Use [IoT Hub automatic device management](/azure/iot-hub/iot-hub-automatic-device-management) or [IoT Edge automatic deployments](/azure/iot-edge/module-deployment-monitoring) to implement continuous updates to existing or new devices and to IoT Edge device configurations, such as properties, application specific settings, or relationships.<br><br> To update an existing device or IoT Edge device configuration based on a one-time or recurring schedule, use [IoT Hub scheduled jobs](/azure/iot-hub/iot-hub-devguide-jobs).<br><br>To update existing device or IoT Edge device firmware, application, or package updates over-the-air, use [Device Update for IoT Hub](/azure/iot-hub-device-update/understand-device-update).<br><br> Have a manual update method for IoT devices. Because of root certificate changes or connectivity problems, you might need to manually update devices by physically connecting to a local computer or by using a local connectivity protocol, such as BLE. | IoT Hub automatic device management and IoT Edge automatic deployments provide a highly efficient, secure, and reliable way to automate configuration deployments for a fleet or specific group of devices. To ensure that the devices have the specified configurations, the services continuously monitor new and existing targeted devices and their configurations based on tags.<br><br> IoT Hub scheduled jobs provide an optimal way to update configurations for a fleet or specific group of devices at a scheduled time.<br><br> Device Update for IoT Hub efficiently updates a fleet or specific group of devices. |
| Configure the ingestion and other back-end layers of the IoT cloud solution to handle expected and unexpected capacity needs efficiently. If your solution is linked to a connected product, ensure that it can handle fluctuations in expected load within IoT Hub and the associated back-end layers.|  Marketing initiatives, such as sales or promotions, and seasonal events, such as holidays, can introduce peaks in load. To ensure that your solution can scale to handle the peaks, test load variations for expected and unexpected events. |
| Build a centralized management UI by using the REST APIs exposed in [IoT Hub REST APIs](/rest/api/iothub/) to help operation teams manage device fleets. | A centralized device management solution streamlines the administration, monitoring, and operation of IoT devices, which helps ensure efficient lifecycle management and consistent configuration across the IoT solution. An integrated UI also helps operation teams manage device fleets effectively, which reduces operational complexity and improves overall system reliability.|
| Use a centralized identity provider, such as [Microsoft Entra ID](/entra/fundamentals/whatis), to create [managed identities](/entra/identity/managed-identities-azure-resources/overview). Only allow the appropriate users in those roles to perform management or operation activities, such as creating and provisioning new devices, sending commands to hardware in the field, deploying updates, and modifying user permissions. | Microsoft Entra ID for authentication provides improved security and ease of use compared to traditional security tokens. You can use Azure role-based access control (RBAC) in Microsoft Entra ID to control access to IoT Hub.|
| In an IoT Hub-based solution, you can use Microsoft Entra ID to authenticate requests to IoT Hub service APIs, such as creating device identities or invoking direct methods. You can develop a custom management UI for solution operators and administrators. This UI authenticates users against Microsoft Entra ID and sends API requests to the IoT solution back end on their behalf. | Custom management UIs improve security by incorporating RBAC and other security measures. They also enhance user experience by providing intuitive interfaces that simplify complex tasks. |
| Use IoT Hub logging, monitoring, and alerting systems to determine if the solution is functioning as expected and to help troubleshoot and mitigate problems. For more information about the metrics and logs that IoT Hub creates, see [IoT Hub monitoring data reference](/azure/iot-hub/monitor-iot-hub-reference). | Monitoring and logging help determine if devices or systems are experiencing errors, correctly configured, generating accurate data, and meeting defined SLOs. |
| Use CI/CD DevOps principles and processes to boost productivity and create a seamless rapid development cycle. Adopt DevOps for your IoT Edge applications by using the built-in IoT Edge tasks in Azure Pipelines. For more information, see [CI/CD to IoT Edge devices](/azure/iot-edge/how-to-continuous-integration-continuous-deployment). | DevOps tools and processes in IoT Hub and IoT Edge help automate the edge software lifecycle. |
| Define a process to reprovision and deprovision IoT devices. For more information. see [IoT Hub device reprovisioning concepts](/azure/iot-dps/concepts-device-reprovision). The IoT device lifecycle includes defining the procedures to reprovision existing devices to other locations or purposes and securely deprovisioning them when necessary. | A reprovision and deprovision process for IoT devices helps you manage their lifecycle. These processes help maintain continuity of operations when you migrate device state information and configurations between IoT hubs. This practice helps ensure that devices continue to function correctly after the move.|
| Test the [failover and failback of IoT Hub](/azure/iot-hub/tutorial-manual-failover) to help ensure high availability. Document recovery steps for [Microsoft-initiated failover](/azure/iot-hub/iot-hub-ha-dr#microsoft-initiated-failover) and [manual failover](/azure/iot-hub/iot-hub-ha-dr#manual-failover).<br><br> Codify and automate the steps needed to recover or fail over the application to a secondary Azure region during failures. This practice helps ensure that you can effectively respond to outages and minimize impact. Similarly, codify and automate the steps to failback the application to the primary region after the problem is addressed. |Failover and failback procedure tests help ensure that your IoT solution can handle failures and maintain high availability, which minimizes the impact of failures.<br><br>Documented recovery steps for both Microsoft-initiated and manual failovers provide a clear and structured approach to handle failover scenarios, which ensures that team members are aware of the procedures and can implement them efficiently.|
| Use an infrastructure as code (IaC) language, such as Bicep or Azure Resource Manager templates (ARM templates), to define and store all [IoT Hub, DPS, and back-end infrastructure configurations](/azure/iot-hub/create-hub#other-tools-for-managing-iot-hubs). Define your entire infrastructure as IaC so that you can automatically deploy your resources consistently across environments.<br><br> Use identity and access management (IAM) or governance tooling, such as Azure RBAC or Azure Policy, to control write access to your infrastructure.<br><br> Restrict configuration setting or infrastructure updates to an automated pipeline. Use this approach so that you can see what changes occur in your environments and when.| A repeatable and predictable process to provision and configure your IoT solution resources, such as IoT Hub or DPS, helps reduce errors and downtime. <br><br>DevOps tools, such as Azure DevOps or GitHub, can help track configuration, IaC, and firmware versions. Version tracking helps you identify the versions of firmware, configuration, and IaC in each environment.|
| Create test environments that use the same firmware, configuration settings, and IaC as your production environments. To easily create these environments, use IaC and automate your processes as much as possible. | Matching test and production environments help simplify the development and testing of new features and hot fixes.|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for IoT Hub.

> [!div class="checklist"]
>
> - **Design for horizontal scaling.** An IoT solution can start with a few hundred devices or messages and grow to millions of devices and messages per minute. IoT Hub and related cloud services can easily handle increased loads, but IoT devices and gateways add complexity. You can design and deploy IoT devices before you finalize the solution. Industries like industrial IoT have devices that last several years, often decades. Replacing devices to update capacity can incur significant costs, so you must plan ahead.
>
> - **Test performance early.** Test early and test often to quickly identify problems. Understand variables that can introduce complexity, such as sensors, devices, and gateways in geographically different locations with different characteristics, speed, and reliability of communication. Plan for this complexity in your testing. Test for failure scenarios like network disconnection. Do stress and load testing of all device, edge, and cloud components in your IoT Hub and related cloud services.
> 
> - **Continuously monitor for performance in production.** To monitor different types of devices in multiple geographical regions, use a distributed monitoring solution. Balance memory and performance costs against the amount of information that you monitor and send to the cloud. Tune transmission for diagnostic scenarios, and monitor at multiple levels and layers. Expose gateway metrics for industrial or gateway-enabled solutions.

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Optimize hardware capabilities. Hardware upgrades and replacements can increase cost and time. Size IoT devices for your required capacity and functionality in advance. <br><br> To optimize existing hardware capabilities, use efficient languages and frameworks like [Embedded C](https://wikipedia.org/wiki/Embedded_C) and [Rust Embedded](https://rust-embedded.org). When you develop for constrained devices, or when most of the security and communication stack is available on the device, use the [Azure IoT Embedded C SDK](/azure/iot-hub/iot-hub-device-embedded-c-sdk). | Compute and input-output intensive tasks that run on specific hardware can significantly enhance performance. For example, machine learning algorithms that run on local GPUs can offload processing from the central processor, which creates faster and more efficient computations.<br><br>Efficient languages and frameworks can improve performance and reduce resource consumption.<br><br>The Azure IoT device SDK for C efficiently manages all necessary components so that you can connect to the cloud gateway. The Azure IoT device SDKs handle required message translation, error handling, and retry mechanisms for a resilient connection, which optimizes the use of existing hardware capabilities. |
| Consider running some workloads at the edge, depending on system constraints such as network throughput or latency. | Workloads at the edge reduce latency and improve responsiveness because they process data closer to where it's generated. This approach minimizes the need for constant cloud connectivity, which benefits scenarios that have intermittent or limited network access. It also helps offload processing from the cloud, which reduces bandwidth usage and associated costs. |
| Don't connect all devices at once, for example after a regional power outage. | When you do a retry, use truncated exponential backoff that introduces jitter. This approach spreads out the reconnection attempts over time, which helps prevent network congestion and server overload. It creates a more stable and reliable reconnection process, which reduces the risk of other outages or performance degradation. Properly managed reconnection attempts can help maintain the overall health and performance of your IoT Hub. |
| Optimize offline scenarios. Provide devices with enough information and context to work without a cloud connection and to store data locally so they can recover from disconnections and reboots. Use [device twins and module twins](/azure/iot-hub/iot-hub-portal-csharp-module-twin-getstarted) to asynchronously sync state information between devices and the cloud for devices that have intermittent connections to IoT Hub.<br><br>Set a time to live (TTL) on the data to ensure that expired data is removed automatically. This practice reduces the need for manual intervention.<br><br>If edge device storage reaches capacity, use a cache eviction strategy, such as a first-in last-out, last-in first-out, or priority-based strategy, to help manage storage efficiently. Consider using a separate disk or disk controller to store data so that the device runtime or application can continue to work even if it has low storage.| A device that can store data locally, including logs and cached telemetry according to priority, when not connected helps maintain functionality during disconnections. Discard less-important data when the device isn't connected to reduce local storage requirements and synchronization time when the device reconnects.<br><br>A separate disk or disk controller to store data helps ensure that storage limitations don't interrupt critical operations. |
| Optimize messaging efficiency. IoT Hub calculates daily quota message counts based on a [4-KB message size](/azure/iot-hub/iot-hub-devguide-quotas-throttling). Sending smaller messages leaves some capacity unused. To optimize usage, aim for message sizes close to 4 KB. To reduce the total number of messages, group smaller device-to-cloud messages into larger messages. But be aware of the introduced latency when combining messages. <br><br> To implement application-level batching, combine multiple smaller messages at the downstream device and send larger messages to the edge gateway. <br><br> Use direct methods for request-reply interactions that can succeed or fail immediately, after a user-specified time-out. Use this approach for scenarios where the course of action differs depending on whether the device responded.<br><br> Use device twins for device state information, including metadata and configurations. IoT Hub maintains a device twin for each device that you connect. | The number and size of device-to-cloud messages is an important parameter for IoT solution performance efficiency. IoT Hub defines message limits per tier, which affects the solution performance and cost. <br><br> Batching helps limit the message overhead and reduce writes to local edge disk storage. |
| Understand messaging quotas and throttling. The IoT Hub tier sets limits for each cloud gateway unit. The messaging quota defines the sustained throughput and sustained send rates for the tier. IoT Hub can handle loads that exceed these quotas for short durations to resiliently handle bursts or load overshoots. <br><br> Another important limit is the hourly or daily service load or throttle limit. Throttle limits protect an IoT hub from excessive load over extended periods. | Understand messaging quotas and throttling to help ensure that your IoT solution operates within the defined limits. This practice prevents overload and maintains performance. Manage load effectively so that you can handle bursts and load overshoots without affecting overall system stability.<br><br>This approach helps maintain a reliable and efficient IoT solution and prevents potential service disruptions because of excessive load. |
| Optimize message processing. Optimize the format that you use to send data to the cloud. Weigh the benefits of optimizing data formats and reducing cloud processing against the bandwidth costs. Consider using [IoT Hub message enrichment](/azure/iot-hub/iot-hub-message-enrichments-overview) to add context to device messages. <br><br> Do time-critical event processing on ingested data as it arrives, instead of storing unprocessed data and requiring complex queries to acquire the data. For time-critical event processing, consider the effects of late data arrival and windowing. Evaluate your approach based on the use case, such as handling critical alarms versus enriching messages.<br><br> [Select the Basic or Standard IoT Hub tier](/azure/iot-hub/iot-hub-scaling) based on your solution requirements. Understand features that the Basic tier doesn't support.<br><br> Consider using Event Grid for publish-subscribe event routing. For more information, see [React to IoT Hub events by using Event Grid to trigger actions](/azure/iot-hub/iot-hub-event-grid) and [Compare message routing and Event Grid for IoT Hub](/azure/iot-hub/iot-hub-event-grid-routing-comparison). | Before storage, you might need to translate, process, or enrich messages from a device or gateway with more information. This step can be time consuming, so you should evaluate the effect on performance.<br><br>Some recommendations conflict, such as using compression for optimizing data transfer versus avoiding cloud processing in decrypting messages. Balance and evaluate these recommendations against other architectural pillars and solution requirements. |
| Use [IoT Edge priority queues](/azure/iot-edge/module-composition#priority-and-time-to-live) to prioritize important data that you send to IoT Hub. IoT Edge buffers messages when there's no connectivity. After the connection is restored, it sends all buffered messages in priority order first, followed by new messages.<br><br> Use [IoT Hub message routing](/azure/iot-hub/iot-hub-devguide-messages-d2c) to separate routes for different data priorities depending on the use case. IoT Hub message routing adds latency.<br><br> Save and send low-priority data at longer intervals, or use batch or [file uploads](/azure/iot-hub/how-to-file-upload). Malware detection on uploaded files increases latency.<br><br> Separate messages based on time constraints. For example, send messages to IoT Hub directly when there's a time constraint, and use file upload via IoT Hub or batch data transfer like Azure Data Factory if there's no time constraint. You can use the [Azure Blob Storage in IoT Edge module](/azure/iot-edge/how-to-deploy-blob) for file upload. | Some data that devices send to the cloud might be more important than other data. Classify and handle the data based on priority to improve performance efficiency. For example, a thermostat sensor sends temperature, humidity, and other telemetry, but also sends an alert when the temperature deviates from a defined range. The system classifies the alarm message as high priority and handles it differently than the temperature telemetry. |
| Use DPS to set up a connection to an IoT hub during provisioning, when the IoT Hub connection isn't available anymore, or during a device reboot.<br><br>Use the [evenly weighted distribution policy](/azure/iot-dps/how-to-use-allocation-policies) to adjust the weight for provisioning based on the use case. This approach optimizes resource allocation. | DPS helps ensure that devices are provisioned securely and efficiently. Provisioning devices over a period of time or in smaller batches helps balance the DPS load and quota, which ensures a smooth onboarding process.<br><br>Allocating devices to IoT Hub in different regions based on latency improves performance and reduces connection times. Implementing a caching strategy for the DPS connection string reduces reconnect operations, which enhances overall system efficiency. |
| Optimize edge versus cloud processing. Use local compute to run real-time and near real-time workloads, or small, optimized, low-latency processing that has time constraints, on devices or at the edge. For larger workloads, or workloads that require extra external data or compute dependencies, use cloud resources.<br><br>For example, you might run a machine learning algorithm at the edge to count people in a video stream, and send an event that contains the count to the cloud. This approach provides quick local processing and efficient cloud-based trend analysis. <br><br> Use the [Azure Stream Analytics IoT Edge module](/azure/stream-analytics/stream-analytics-edge) to run analytics workloads, such as anomaly detection, at the edge. This practice helps label events with detected anomalies before sending them to the cloud, which improves overall system efficiency.<br><br> Understand the overhead of an edge workload that includes many connected downstream devices. The edge node must forward or process all messages and cache all the data if there's intermittent cloud connectivity. <br><br> To evaluate the performance effect on your solution, test your solution with the expected maximum of downstream devices and messages for each edge node. Also understand the performance effect that message translation or enrichment can have on edge, IoT Hub, or cloud event processing. Plan accordingly to maintain system efficiency. | Running real-time and near real-time workloads at the edge provides low-latency processing and immediate response times, which is crucial for time-sensitive applications. <br><br> Testing with the expected maximum of downstream devices and messages ensures that the edge node can handle the load and maintain performance. |
| To optimize performance efficiency for high-volume cloud data, use built-in service integration between IoT Hub and data destinations, like Azure Data Lake Storage and Azure Data Explorer. These services are optimized for high-performance throughput.<br><br>Use the Event Hubs SDK to develop custom ingestion from an IoT hub. The SDK includes an event processor that can rebalance devices and hosts. Use the appropriate number of IoT Hub partitions and consumer groups for the number of simultaneous data readers and required throughput.<br><br>Separate the storage needed for data ingestion and event processing from the storage needed for reporting and integration. Use the data storage that fits your needs based on required throughput, size, retention period, data volume, CRUD requirements, and regional replication. Storage examples include Data Lake Storage, Azure Data Explorer, Azure SQL, and Azure Cosmos DB. For more information, see [Select an Azure data store for your application](/azure/architecture/guide/technology-choices/data-store-decision-tree). | Optimized performance efficiency for high-volume cloud data ensures that your IoT solution can handle large amounts of data efficiently. Built-in service integration and the Event Hubs SDK help streamline data ingestion and processing.<br><br>Proper configuration of IoT Hub partitions and consumer groups and separate storage for different purposes help improve data management. Appropriate data storage based on specific needs helps maintain the performance, scalability, and reliability of your IoT solution. |
| Separate the IoT solution ingestion pipeline from integration processing. Make sure complex queries or loads don't affect data ingestion performance in IoT Hub. Use well-defined and versioned APIs for access to IoT Hub information regarding device twins, module twins, jobs, and message routing. Prevent end users from creating user-defined queries against IoT Hub storage. Consider using separate data stores for integration and reporting. | Separation of the IoT solution ingestion pipeline and integration processing ensures that complex queries or loads from the integration layer don't affect data ingestion performance. This approach maintains the efficiency and reliability of IoT Hub. |
| Use Azure Monitor to collect IoT Hub metrics and send alerts for critical metrics. Set up Azure Monitor alerts based on your scale limits, such as device-to-cloud messages sent per second. Set the alert to a percentage of the limit, such as 75%, to notify you in advance. Set up Azure Monitor alerts for logs and metrics, such as the number of throttling errors. Set Azure Service Health service alerts to trigger notifications when the IoT Hub status changes. | IoT Hub metrics and alerts for critical metrics ensure proactive monitoring and management of your IoT solution. Alerts based on scale limits and a percentage of those limits help notify you before you reach scalability limits so that you can make timely adjustments. Alerts for logs and metrics, such as throttling errors, help you identify and address problems promptly. Alerts when the IoT Hub status changes provide awareness of potential disruptions and enable you to take necessary actions to maintain the reliability and performance of your IoT solution. |

## Azure policies

Azure provides an extensive set of built-in policies related to IoT Hub and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- IoT Hub has local authentication methods disabled for service APIs.
- Public network access is disabled for your IoT Hub device provisioning instance so that it's not accessible over the public internet. 
- IoT Hub can only be accessed from a private endpoint.
- Resource logs in IoT Hub are enabled. These logs recreate activity trails that can help you investigate after a security incident or network compromise.

For comprehensive governance, review the [Azure Policy built-in definitions for Internet of Things](/azure/governance/policy/samples/built-in-policies#internet-of-things) and other policies that might affect the security of the IoT solution.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Advisor recommendations are aligned with Well-Architected Framework pillars.

For more information, see the recommendations in [Azure Advisor](/azure/advisor).

## Next step

[IoT Hub documentation](/azure/iot-hub/)