---
title: Well-Architected Framework perspective on Azure IoT Hub
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for IoT Hub.
author: asergaz
ms.author: sergaz
ms.topic: conceptual
ms.date: 01/02/2025
ms.service: azure-waf
ms.subservice: waf-service-guide
ai-usage: ai-assisted
products:
 - azure-iot-hub
azure.category:
 - iot
---

<!-- TODO: 
1- Add Card back in index.yml 
2- Remove Assessment: https://learn.microsoft.com/en-us/assessments/azure-architecture-review
-->

# Well-Architected Framework perspective on Azure IoT Hub

Azure IoT Hub is a managed service hosted in the cloud that acts as a central message hub for communication between an IoT application and its attached devices. You can connect millions of devices and their back-end solutions reliably and securely. Almost any device can be connected to an IoT hub.

This article assumes that as an architect, you've reviewed the [technologies and services you can use to create IoT solutions](/azure/iot/iot-services-and-technologies?toc=https%3A%2F%2Flearn.microsoft.com%2Fen-us%2Fazure%2Farchitecture%2Ftoc.json&bc=https%3A%2F%2Flearn.microsoft.com%2Fen-us%2Fazure%2Farchitecture%2Fbread%2Ftoc.json) and chose [IoT Hub](/azure/iot-hub/iot-concepts-and-iot-hub) as the Azure IoT platform service for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](/azure/well-architected/pillars).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope.
>
> Also included are recommendations for the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations that are available for IoT Hub and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or to optimize your existing environments.

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:  

- [Azure IoT Hub](/azure/iot-hub/iot-concepts-and-iot-hub)
- [Azure IoT Hub Device Provisioning Service (DPS)](/azure/iot-dps/about-iot-dps)
- [Azure Device Update for IoT Hub](/azure/iot-hub-device-update/understand-device-update)
- [Azure IoT Edge](/azure/iot-edge/about-iot-edge)
- [Azure Digital Twins](/azure/digital-twins/overview)
- [Azure Sphere](/azure-sphere/product-overview/what-is-azure-sphere)
- [Microsoft Defender for IoT](/azure/iot/iot-overview-security)

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

#### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the performance of IoT Hub. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Design devices for resiliency**: Design your devices to satisfy the uptime and availability requirements of your end-to-end solution. Ensure that your IoT device can operate efficiently with intermittent connectivity to the cloud.
>
> - **Design for business requirements**: Cost implications are inevitable when introducing architectural modifications to meet service-level agreements (SLAs). For example, to have greater reliability and high availability you can implement cross-region redundancies and an automated system to autoscale. This trade-off should be carefully considered.
>
> - **Safe, simple update procedures**: An enterprise IoT solution should provide a strategy for how operators manage devices. IoT operators require simple and reliable update tools and practices.
>
> - **Observe application health**: Define service-level indicators (SLIs) and service-level objectives (SLOs) based on observability. Add processes for auditing, monitoring, and alerting beyond what IoT Hub provides.
>
> - **High availability and disaster recovery (HA/DR) for critical components**: Resilient hardware and software components that build in redundancy, including cross-region redundancies. Determine which of the options outlined in [IoT Hub high availability and disaster recovery](/azure/iot-hub/iot-hub-ha-dr) article best suit your business objectives.
>
> - **Plan for capacity**: Plan for service quotas and throttles, latency between the detection-action, and establish benchmarks at production scale to support uninterrupted data flow.

#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Calculate the IoT Hub capacity you need, for example, using number of messages/day and other limits. If your workload has a fluctuating need for capacity, implement an autoscaling mechanism so that it increases and decreases capacity with demand. For more information, see [Choose the right IoT Hub tier and size for your solution](/azure/iot-hub/iot-hub-scaling). | This approach helps in optimizing resource usage, maintaining performance, and controlling costs. By scaling the capacity up or down based on demand, you can ensure that your solution remains responsive and reliable without over-provisioning resources. |
| Design resilient applications by adding a device reconnection strategy to IoT Hub. Follow guidance in [Manage device reconnections to create resilient applications](/azure/iot/concepts-manage-device-reconnections). | It's important to have a strategy to reconnect devices. Without a reconnection strategy, you could see a negative effect on your solution's performance, availability, and cost. |
| Evaluate the trade-offs of different High Availability (HA) and Disaster Recovery (DR) options offered by IoT Hub. Depending on the uptime goals you define for your IoT solution, you should determine which of the options outlined in [IoT Hub high availability and disaster recovery](/azure/iot-hub/iot-hub-ha-dr) best suit your business objectives. | Selecting between [Microsoft-initiated failover](/azure/iot-hub/iot-hub-ha-dr#microsoft-initiated-failover), [manual failover](/azure/iot-hub/iot-hub-ha-dr#manual-failover) or [cross region HA](/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha) help you implement a resilient IoT solution and properly estimate cost and complexity.|
| Use [DPS](/azure/iot-dps/about-iot-dps) to provision your devices and assign them to an IoT hub. | DPS is a helper service for IoT Hub that enables zero-touch, just-in-time provisioning to the right IoT hub without requiring human intervention. DPS enables the provisioning of millions of devices in a secure and scalable manner. |
| Use [Device Update for IoT Hub](/azure/iot-hub-device-update) to manage over-the-air (OTA) updates for your IoT devices. Ensure that your update strategy includes gradual rollout, resilient A/B updates, detailed management, and reporting tools. | This approach ensures safe, secure, and reliable updates for IoT devices, reducing downtime, and operational inefficiencies. It helps maintain device compliance and quickly identifies and resolves update failures, enhancing overall system reliability. |
| Implement DevOps practices for managing IoT solutions, including continuous integration and continuous deployment (CI/CD), monitoring, and automated updates. Use [DevOps to build and release your IoT Edge applications](/azure/iot-edge/how-to-continuous-integration-continuous-deployment), [Azure Monitor to monitor and get alerts from the data collected by IoT Hub](/azure/iot-hub/monitor-iot-hub) and [automatic device management in IoT Hub to automate the management of devices at scale](/azure/iot-hub/iot-hub-automatic-device-management). | DevOps practices enhance the reliability and efficiency of IoT solutions by enabling rapid deployment, continuous monitoring, and automated updates. This approach reduces downtime, improves system performance, and ensures that updates and changes are deployed safely and consistently. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of your IoT Hub.

#### Design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md). Identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Use a strong identity to authenticate devices and users**: Have a hardware root of trust for trusted identity, register devices, issue renewable credentials, and use passwordless or multi-factor authentication (MFA).
>
> - **Automate and use least-privileged access control:** Limit the impact from compromised devices or identities, or unapproved workloads.
>
> - **Device health:** Evaluate device health to gate device access or flag devices for remediation. Check security configuration, assess vulnerabilities and insecure passwords, monitor for threats and anomalies, and build ongoing risk profiles.
>
> - **Device update:** Continuous updates to keep devices healthy. Use a centralized configuration and compliance management solution and a robust update mechanism to ensure devices are up-to-date and healthy.
>
> - **Monitor system security, plan incident response:** Proactively monitor for unauthorized or compromised devices and respond to emerging threats.
>
> - **Connection security:** Ensure all data transmitted between the IoT device and the IoT cloud services is confidential and tamper-proof.

#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Apply zero-trust criteria for devices. Devices connected to IoT Hub should contain an [hardware security module (HSM)](/azure/iot-dps/concepts-tpm-attestation) for strong identity, use [renewable credentials](/azure/iot-dps/how-to-roll-certificates), enforce least-privileged access control, emit proper health signals for conditional access and alert operators to [revoke a device from IoT Hub when compromised](/azure/iot-dps/how-to-revoke-device-access-portal), provide [update agents for security updates](/azure/iot-hub-device-update/), include device management capabilities for cloud-driven configuration and automated security response, minimize physical attack footprint by disabling unnecessary features, and [protect data at rest using standard encryption algorithms](/azure/iot-hub/iot-hub-tls-support). | Applying zero-trust criteria for devices connected to IoT Hub enhances security and reliability.<br><br> By using hardware security modules (HSM) for strong identity, renewable credentials, and enforcing least-privileged access control, unauthorized access, and compromised devices are minimized.<br><br> Emitting health signals for conditional access and providing update agents ensure devices remain secure and compliant. Integrating cloud-driven device management and automated security response, along with running security agents, strengthens the security posture.<br><br> Minimizing the physical attack footprint and protecting data at rest with encryption safeguards sensitive information and maintains system integrity.  |
| Use X.509 certificates to authenticate your devices to IoT Hub. To learn more, see [Authenticate identities with X.509 certificates](/azure/iot-hub/authenticate-authorize-x509). | IoT Hub support both X509 certificate-based authentication and security tokens as methods for a device to authenticate. Use X509-based authentication in production environments as it provides greater security. |
| To layer least-privileged access for IoT devices, use [network segmentation](/security/benchmark/azure/baselines/iot-hub-security-baseline?toc=/azure/iot-hub/TOC.json#network-security) to group IoT devices, mitigating potential impact of a potential compromise. | Network segmentation can group IoT devices, mitigating potential impact of a compromise. A common approach is to connect IoT devices to an "IoT network" for devices such as printers, VoIP phones, and smart TVs. This IoT network is separate from other organizational resources that the workforce accesses.<br><br> Network micro-segmentation lets you isolate less-capable devices at the network layer, either behind a gateway or on a discrete network segment. For example, you can logically separate dedicated Operations Technology (OT) environments from the corporate Information Technology (IT) network, using demilitarized zone (DMZ) network architecture with firewalls. More mature organizations can also implement micro-segmentation policies at multiple layers of the Purdue Model, typically using next-gen firewalls. |
| Use [Microsoft Defender for IoT](/azure/defender-for-iot/organizations/overview) as the frontline of defense to protect your resources in Azure. | Microsoft Defender for IoT is an agentless, network layer security platform that delivers continuous asset discovery, vulnerability management, and threat detection for IoT devices. Defender for IoT continuously monitors network traffic using IoT-aware behavioral analytics to identify unauthorized or compromised components. |
| Use IoT Hub with [Azure Sphere](/azure-sphere) as a guardian module to secure other devices, including existing legacy systems not designed for trusted connectivity. | Azure Sphere can be a guardian module to secure other devices, including existing legacy systems not designed for trusted connectivity. In this scenario, an Azure Sphere guardian module deploys with an application and interfaces with existing devices through Ethernet, serial, or Bluetooth Low Energy (BLE). The devices don't necessarily have direct internet connectivity. |
| Use [IoT Edge gateways](/azure/iot-edge/iot-edge-as-gateway) to enforce strong identity patterns for less capable devices. | IoT Edge provides an edge runtime connection to IoT Hub, and supports certificates as strong device identities. IoT Edge supports the PKCS#11 standard for device manufacturing identities and other secrets stored on a Trusted Platform Module (TPM) or Hardware Security Module (HSM). |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to IoT Hub and its environment.

#### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that is allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Develop cost-management discipline:** Understand total cost of ownership (TCO) by accounting for both functional and nonfunctional costs when planning.
>
> - **Use industry-standard strategies and approaches:** For IoT specific industries, like manufacturing, building structures, IoT systems, smart cities, energy grids, use [industry-standard strategies and approaches](/azure/digital-twins/concepts-ontologies) to optimize costs.
>
> - **Understand the cost of protecting your IoT solution:** As IoT provides end-to-end solutions, security costs are incurred from silicon to cloud. For example, MPU, devices, telecommunication, cloud and operation technology. There are many options to implement security requirements and affect costs.
>
> - **Design for rate optimization:** Define implementation plans for each [IoT solution-wide concern](/azure/iot/iot-introduction#solution-wide-concerns) to optimize costs.
>
> - **Monitor and optimize over time:** Continuously monitor and [optimize IoT Hub costs](/azure/iot-hub/iot-hub-devguide-pricing) with ongoing cost optimization activities after you implement your solution.
>
> - **Estimate realistic costs:** Select the [appropriate IoT Hub tier and size](/azure/iot-hub/iot-hub-scaling) for both development and operational phases of your IoT solution.
>
> - **Evaluate one-time vs. recurring costs:** Consider the cost benefits of one-time costs versus recurring costs. For example, in security where hacking techniques are always changing, it can be best to import a reliable commercial operating system and module such as Azure Sphere. For a one-time payment, such services provide ongoing monthly device security patches.
>
> - **Optimize resource usage:** Implement [auto-scaling mechanisms](/azure/iot-hub/iot-hub-scaling#auto-scale) to adjust IoT Hub capacity based on demand, ensuring efficient resource usage and cost control.
>
> - **Leverage reserved capacity:** Consider the [size and frequency of message payloads](/azure/iot-hub/iot-hub-devguide-pricing#charges-per-operation) to ensure your IoT Hub has the correct size and ready to scale.
>
> - **Implement cost alerts and budgets:** Set up cost alerts and budgets in Azure Cost Management to track and control your spending effectively. Cost management APIs are available for [IoT Hub](/rest/api/iothub), and [DPS](/rest/api/iot-dps).

#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Understand [IoT Hub quotas and throttling limits](/azure/iot-hub/iot-hub-devguide-quotas-throttling) and how they impact total cost of ownership (TCO) on running at scale in production.| Your cost model should include the following areas based on running at scale in production: devices, infrastructure, operations, and monitoring. Every service has its own set of quotas and throttling limits. Understanding these limits in IoT Hub is a key part of cost optimization. |
| Understand the device ecosystem and the various roles involved, such as hardware manufacturers, app developers, and operators for planning. Evaluate [Best practices for device configuration within an IoT solution](/azure/iot-hub/iot-hub-configuration-best-practices), to help you understand more about device management. | There are small 8-bit MCUs all the way up to the latest x86 CPUs as found in a desktop computer. Many variables factor into the decision for which hardware to choose. Understanding the [key hardware differentiators](/azure/iot/concepts-iot-device-types#key-hardware-differentiators) in an IoT device, help you reduce costs. |
| Use [IoT Plug and Play](/azure/iot/overview-iot-plug-and-play) to reduce hardware development and on-boarding time. Adopt a [DTDL industry ontology](/azure/digital-twins/concepts-ontologies-adopt). | IoT Plug and Play allows solution builders to integrate IoT devices with IoT Hub without any manual configuration. At the core, it's a device model that a device uses to advertise its capabilities to an IoT Plug and Play-enabled application. |
| Use [Device Update for IoT Hub](/azure/iot-hub-device-update/understand-device-update) to deliver over-the-air updates and remotely manage your devices. | Implementing a solution to remotely update the device's firmware or software is a key way to lower manual labor costs in the long-term. |
| Use connectors for Azure services such as Power Apps, Power Automate and Logic Apps. | IoT Hub, together with [Azure Event Grid provides connectors](/connectors/azureeventgrid/) to implement your requirements within Logic Apps and Power Automate. |
| Use [device simulators](/azure/iot-hub/tutorial-connectivity) and set up a load testing environment, to test the solution at production scale and understand the solution cost. | Device simulators can help save costs at scale when there's a need to model, test, and simulate outcomes for any given scenario. Simulation code samples like the [Azure IoT Device Telemetry Simulator](/samples/azure-samples/iot-telemetry-simulator/azure-iot-device-telemetry-simulator/) help you test and estimate costs at scale with various parameters. |
| To reduce transmission sizes and costs: [choose the right protocol](/azure/iot-hub/iot-hub-devguide-protocols) for your IoT devices, compress telemetry at the edge, [batch messages](/azure/iot-hub/iot-hub-devguide-file-upload) on the device, choose between keeping connections alive or reconnecting when the devices wake up, use device twin to exchange status information asynchronously if cost is critical. | Choosing the right protocol for your scenario enables devices to reduce transmission sizes and costs in the transport layer.<br><br> For battery-powered IoT devices, you can choose between keeping connections alive or reconnecting when the devices wake up. Keep alive, or heartbeat messages are essential for checking device status but adds up to the network costs of transmission. In IoT Hub, messages exchanged to keep the connection open and alive aren't charged. Reconnecting consumes packets around 6-KB for TLS connection, device authentication, and retrieving a device twin, but saves battery capacity if the device wakes up only once or twice per day. |
| Understand how to use hot, warm, and cold path analytics for IoT data and apply the [lambda architecture](/azure/architecture/databases/guide/big-data-architectures#lambda-architecture). Use the built-in [message routing](/azure/iot-hub/iot-hub-devguide-messages-d2c) feature in IoT Hub. | IoT solutions can store large amounts of data. Storage costs are a large part of the overall solution cost and you should choose an appropriate processing and storage plan based on your business scenario. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

#### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to IoT Hub.

> [!div class="checklist"]
>
> - **Embrace continuous operations and scaling:** Ensure that the IoT solution can successfully manage automated device provisioning, integrate with other back-end systems, support different roles such as solution developers, solution administrators, and operators, and adapt and scale efficiently to any changes on demand such as new IoT devices being deployed or higher ingestion throughput.
>
> - **Optimize build and release processes:** Any successful enterprise IoT solution requires a strategy to establish and update a device or fleet of device's configuration. A device's configuration includes device properties, connection settings, relationships, and firmware. IoT operators require simple and reliable tools that enable them to update a device or fleet of device's configuration at any point during the device's lifetime.
>
> - **Understand operational health:** Use IoT solution logging, monitoring, and alerting systems to determine whether the solution is functioning as expected and to help troubleshoot problems throughout the lifecycle of the solution.
>
> - **Use automation and DevOps:** An IoT device is fundamentally a small computer with specialized hardware and software. IoT devices are often constrained in hardware, for example having limited memory or compute capacity. Automation and DevOps are essential to ensure that OS and software for IoT devices and gateways are properly uploaded and deployed to minimize operational downtime. Automation and DevOps are essential for monitoring and managing the lifecycle of IoT devices.

#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| For continuous updates to existing or new devices and IoT Edge device configurations, such as properties, application specific settings, or relationships, use either [IoT Hub automatic device management](/azure/iot-hub/iot-hub-automatic-device-management) or [IoT Edge automatic deployments](/iot-edge/module-deployment-monitoring).<br><br> To update an existing device or IoT Edge device configuration based on a one-time or recurring schedule, use [IoT Hub scheduled jobs](/azure/iot-hub/iot-hub-devguide-jobs).<br><br>To update existing device or IoT Edge device firmware, application, or package updates over-the-air (OTA), use [Device Update for IoT Hub](/azure/iot-hub-device-update/understand-device-update).<br><br> Have a manual update method for IoT devices. Due to root certificate changes or connectivity issues, you may need to manually update devices by physically connecting to a local computer or using a local connectivity protocol such as BLE. | IoT Hub automatic device management and IoT Edge automatic deployments, offer an efficient, secure, and reliable way to automate configuration deployments for a fleet or specific group of devices. The services continuously monitor all new and existing targeted devices and their configuration based on tags, to ensure the devices always have the specified configuration.<br><br> IoT Hub scheduled jobs are an efficient, secure, and reliable way to provide a configuration update for a fleet or specific group of devices at a scheduled time.<br><br> Device Update for IoT Hub is a safe, secure, and reliable way to update a fleet or specific group of devices. |
| Configure the ingestion and other back-end layers of the IoT cloud solution to be able to scale to handle expected and unexpected capacity needs. | If your solution is tied to a connected product, you must handle fluctuations in expected load in IoT Hub and related back-end layers. Load is impacted by marketing initiatives such as sales or promotions, or by seasonal events such as holidays. You should test load variations before events, including unexpected events, to ensure that your IoT solution can scale. |
| Build a centralized management UI by using the REST APIs exposed in [IoT Hub REST APIs](/rest/api/iothub/) to assist operation teams with device fleet management. | A centralized device management solution streamlines the administration, monitoring, and operation of IoT devices, ensuring efficient lifecycle management and consistent configuration across the IoT solution. An integrated UI further assists operation teams in managing device fleets effectively, reducing operational complexity, and improving overall system reliability. |
| Use a centralized identity provider, such as [Microsoft Entra ID](/entra/fundamentals/whatis) and create [managed identities](/entra/identity/managed-identities-azure-resources/overview), and only let the appropriate users in those roles perform management or operation activities, such as creating and provisioning new devices, sending commands to hardware in the field, deploying updates, and modifying user permissions. | In an IoT Hub-based solution, you can use Microsoft Entra ID to authenticate requests to IoT Hub service APIs, such as creating device identities or invoking direct methods. You can develop a custom management UI for solution operators and administrators that authenticates users against Microsoft Entra ID and executes API requests to the IoT solution back-end on behalf of those users. |
| Use IoT Hub logging, monitoring, and alerting systems to determine whether the solution is functioning as expected and to help troubleshoot and mitigate problems. To learn more about the metrics and logs that IoT Hub creates, see [Azure IoT Hub monitoring data reference](/azure/iot-hub/monitor-iot-hub-reference). | Monitoring and logging help determine whether devices or systems are in an error condition, correctly configured, generating accurate data, and meeting defined service level objectives. |
| Use CI/CD DevOps principles and processes to boost productivity and create a seamless rapid development cycle. Adopt DevOps with your IoT Edge applications with the built-in IoT Edge tasks in Azure Pipelines. Learn more in [Continuous integration and continuous deployment to Azure IoT Edge devices](/azure/iot-edge/how-to-continuous-integration-continuous-deployment). | Use DevOps tools and processes in IoT Hub and IoT Edge to automate the edge software lifecycle. |
| Define a process to reprovision and deprovision IoT devices. Learn more in [IoT Hub Device reprovisioning concepts](/azure/iot-dps/concepts-device-reprovision). | The IoT device lifecycle includes defining the procedures to reprovision existing devices to other locations or purposes, and securely deprovisioning them when they're no longer needed. |
| Test the [failover and failback of IoT Hub](/azure/iot-hub/tutorial-manual-failover) to ensure high availability. Document recovery steps for [Microsoft-initiated failover](/azure/iot-hub/iot-hub-ha-dr#microsoft-initiated-failover) and [manual failover](/azure/iot-hub/iot-hub-ha-dr#manual-failover). | The steps required to recover or failover the application to a secondary Azure region in failure situations should be codified, preferably in an automated manner, to ensure capabilities exist to effectively respond to an outage in a way that limits impact. Similar codified steps should also exist to capture the process required to failback the application to the primary region once a failover triggering issue is addressed. |
| Define and store all [IoT Hub, DPS and back-end infrastructure configuration](/azure/iot-hub/create-hub?tabs=portal#other-tools-for-managing-iot-hubs), using an infrastructure as code (IaC) language, such as Bicep or ARM templates.<br><br> Lock down write access to your infrastructure using IAM or governance tooling, such as Azure role-based access control (RBAC) or Azure Policy. | As you provision and configure your IoT solution resources, Iot Hub or DPS, a repeatable and predictable process helps you avoid errors and downtime. Define your entire infrastructure as IaC to ensure that your resources can be deployed automatically and consistently across environments. A DevOps tool, such as Azure DevOps or GitHub, can help track configuration, infrastructure as code, and firmware versions. Version tracking helps your organization to determine what versions of firmware, configuration, and IaC are deployed to what environments.<br><br>Ensure the only way to update configuration settings or infrastructure is through an automated pipeline. This enables you to see when and what changes have occurred to your environments. |
| Create test environments that use the same firmware, configuration settings, and IaC as your production environments. You can easily create these environments by using IaC and automating your processes as much as possible. | Critical test environments have 1:1 parity with the production environment. Apply the same firmware, configuration settings, and IaC in your production and testing environments to simplify the development and testing of new functionality or hot fixes. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

#### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for IoT Hub.

> [!div class="checklist"]
>
> - **Design for horizontal scaling**: An IoT solution can start with a few hundred devices or messages and grow to millions of devices and messages per minute. You can easily scale IoT Hub and related cloud services to an increase in load, but the situation can be more complex for IoT devices and gateways. IoT devices can be designed or deployed before the solution is finalized. Industrial IoT or similar industries can measure device lifespan in decades. Updating capacity by replacing devices is costly. In these scenarios, it's especially important to plan ahead.
>
> - **Shift-left on performance testing**: Test early and test often to catch issues early. Be aware of the complexity of having sensors, devices, and gateways in geographically different locations with different characteristics, speed, and reliability of communication. Plan for this complexity in your testing, and make sure to test for failure scenarios like network disconnection. Do stress and load testing of all device, edge, and cloud components in your IoT Hub and related cloud services.
> 
> - **Continuously monitor for performance in production**: To monitor different types of devices in multiple geographical regions, use a distributed monitoring solution. Balance the amount of information monitored and sent to the cloud against memory and performance costs. Tune transmission for diagnostic scenarios, and monitor at multiple levels and layers. Expose gateway metrics for industrial or gateway-enabled solutions.

#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| **Optimize hardware capabilities**. Upgrading or replacing hardware is costly and time consuming. Size IoT devices for required capacity and functionality in advance. | Running compute and input-output intensive tasks on specific hardware can significantly enhance performance. For example, running machine learning (ML) algorithms on local graphics processing units (GPUs) can offload processing from the central processor, leading to faster and more efficient computations.<br><br>Optimizing existing hardware capabilities by using efficient languages and frameworks like [Embedded C](https://wikipedia.org/wiki/Embedded_C) and [Rust Embedded](https://rust-embedded.org) can lead to better performance and lower resource consumption. You can use the [Azure IoT Embedded C SDK](/azure/iot-hub/iot-hub-device-embedded-c-sdk) when developing for constrained devices, or when most of the security and communication stack is already available on the device.<br><br>Using the [Azure IoT device SDK for C](/azure/iot-hub/iot-hub-device-sdk-c-intro) ensures that all necessary components to connect to the cloud gateway are managed efficiently. The Azure IoT Device software development kits (SDKs) handle required message translation, error handling, and retry mechanisms needed for a resilient connection, thereby optimizing the use of existing hardware capabilities. |
| **Run workloads at the edge**. Depending on system constraints such as network throughput or latency, consider running some workloads at the edge. | Running workloads at the edge reduces latency and improves responsiveness by processing data closer to where it's generated. This approach minimizes the need for constant cloud connectivity, which is beneficial in scenarios with intermittent or limited network access. It also helps to offload processing from the cloud, reducing bandwidth usage and associated costs. |
| **Don't connect all devices at once**, for example after a regional power outage. | Use truncated exponential backoff with introduced jitter when retrying. This approach helps to prevent network congestion and server overload by spreading out the reconnection attempts over time. It ensures a more stable and reliable reconnection process, reducing the risk of further outages or performance degradation. By managing the reconnection attempts efficiently, you can maintain the overall health and performance of your IoT Hub. |
| **Optimize offline scenarios**. You can provide devices with enough information and context to work without a cloud connection and to store data locally, so they can recover from disconnections and reboots. Use [device twins and module twins](/azure/iot-hub/iot-hub-portal-csharp-module-twin-getstarted) to asynchronously sync state information between devices and the cloud, even when devices aren't currently connected to IoT Hub. | Ensuring the device is capable of storing data locally when not connected, including logs and cached telemetry according to priority, helps maintain functionality during disconnections. Setting a time to live (TTL) on the data ensures that expired data is removed automatically, reducing the need for manual intervention. Discarding less important data when the device isn't connected reduces local storage requirements and synchronization time when the device reconnects.<br><br>If edge device storage reaches capacity, using a cache eviction strategy such as first-in last-out (FIFO), last-in first-out (LIFO), or priority-based can help manage storage efficiently. Additionally, consider using a separate disk or disk controller to store data, so the device runtime or application can continue to work even when low on storage. This approach ensures that critical operations aren't interrupted due to storage limitations. |
| **Optimize messaging efficiency**. IoT Hub calculates daily quota message counts based on a [4-KB message size](/azure/iot-hub/iot-hub-devguide-quotas-throttling). Sending smaller messages leaves some capacity unused. In general, use message sizes close to the 4-KB boundary. Group smaller device-to-cloud messages into larger messages to reduce the total number of messages, but consider the introduced latency when combining messages.<br><br> Use application-level batching by combining multiple smaller messages at the downstream device and sending larger messages to the edge gateway. This batching limits the message overhead and reduces writes to local edge disk storage.<br><br> Use direct methods for request-reply interactions that can succeed or fail immediately, after a user-specified time-out. This approach is useful for scenarios where the course of action is different depending on whether the device responded.<br><br> Use device twins for device state information, including metadata and configurations. IoT Hub maintains a device twin for each device that you connect. | The number and size of device to cloud messages is an important parameter for IoT solution performance efficiency. IoT Hub define message limits per tier, which affects both solution performance and cost. |
| **Understand messaging quotas and throttling**. The IoT Hub tier sets cloud gateway per-unit limits. The messaging *quota* defines sustained throughput and sustained send rates for the tier. IoT Hub can handle loads above these quotas for short durations to resiliently handle bursts or load overshoots. Another important limit is the hourly or daily service load or *throttle* limit. Throttle limits protect an IoT hub from too much load for too long a time. | Understanding messaging quotas and throttling help ensure that your IoT solution operates within the defined limits, preventing overload and maintaining performance. By managing load effectively, you can handle bursts and load overshoots without impacting the overall system stability. This approach helps maintain a reliable and efficient IoT solution, avoiding potential service disruptions due to excessive load. |
| **Optimize message processing**. Optimize the data format used to send data to the cloud. Compare performance (and cost) of bandwidth vs. performance improvement with less cloud data processing needed. Consider using [IoT Hub message enrichment](/azure/iot-hub/iot-hub-message-enrichments-overview) to add context to device messages. <br><br> Do time-critical event processing on ingested data as it arrives, instead of storing unprocessed data and requiring complex queries to acquire the data. For time-critical event processing, consider the impacts of late arrival and windowing. Evaluate depending on use case, for example critical alarm handling versus message enrichment.<br><br> [Select the right IoT Hub tier](/azure/iot-hub/iot-hub-scaling), Basic, or Standard, based on solution requirements. Be aware of features that the Basic tier doesn't support.<br><br> Consider using Azure Event Grid for publish-subscribe event routing. For more information, see [React to IoT Hub events by using Event Grid to trigger actions](/azure/iot-hub/iot-hub-event-grid) and [Compare message routing and Event Grid for IoT Hub](/azure/iot-hub/iot-hub-event-grid-routing-comparison). | Messages from a device or gateway might need to be translated, processed, or enriched with more information before storage. This step can be time-consuming, so it's important to evaluate the effect on performance. Some recommendations conflict, such as using compression for optimizing data transfer versus avoiding cloud processing in decrypting messages. These recommendations need to be balanced and evaluated against other architectural pillars and solution requirements. |
| **Prioritize data**. Use [IoT Edge priority queues](/azure/iot-edge/module-composition#priority-and-time-to-live) to make sure important data is prioritized while sending to IoT Hub. IoT Edge buffers messages when there's no connectivity, but after the connection is restored, sends all buffered messages in priority order first, followed by new messages.<br><br> Use [IoT Hub message routing](/azure/iot-hub/iot-hub-devguide-messages-d2c) to separate routes for different data priorities depending on use case. IoT Hub message routing adds some latency.<br><br> Save and send low priority data at longer intervals, or by using batch or [file uploads](/azure/iot-hub/how-to-file-upload). Malware detection on uploaded files increases latency.<br><br> Separate messages based on time constraints. For example, send messages to IoT Hub directly when there's a time constraint, and utilize file upload via IoT Hub or batch data transfer like Azure Data Factory if there's no time constraint. You can use the [Azure Blob Storage in IoT Edge module](/azure/iot-edge/how-to-deploy-blob) for file upload. | Some data that devices send to the cloud might be more important than other data. Classifying and handling the data based on priority is a good practice for performance efficiency. For example, a thermostat sensor sends temperature, humidity, and other telemetry, but also sends an alarm when temperature is outside a defined range. The system classifies the alarm message as higher priority and handles it differently than the temperature telemetry. |
| **Provision devices with DPS**. Use DPS to set up a connection to an IoT hub during provisioning, when the IoT Hub connection isn't available anymore, or during device reboot. | Using DPS ensures that devices are provisioned securely and efficiently, even when the IoT Hub connection is unavailable or during device reboots. The [evenly weighted distribution policy](/azure/iot-dps/how-to-use-allocation-policies) allows for adjusting the weight for provisioning based on use case, optimizing resource allocation. Provisioning devices over a period of time or in smaller batches helps balance the DPS load and quota, ensuring a smooth onboarding process. Allocating devices to IoT Hubs in different regions based on latency improves performance and reduces connection times. Implementing a caching strategy for the DPS connection string reduces reconnect operations, enhancing overall system efficiency. |
| **Optimize edge versus cloud processing**. Run real-time and near real-time workloads, or small, optimized, low-latency processing with time constraints, on devices or at the edge by using local compute. Run larger workloads, or other workloads that have added or external data, or compute dependencies, in the cloud. | Running real-time and near real-time workloads at the edge ensures low-latency processing and immediate response times, which is crucial for time-sensitive applications. For example, running a machine learning algorithm at the edge to count people in a video stream and sending an event containing the count to the cloud allows for quick local processing and efficient cloud-based trend analysis. Using the [Stream Analytics Edge module](/azure/stream-analytics/stream-analytics-edge) to run analytics workloads at the edge, such as anomaly detection, helps label events sent to the cloud with detected anomalies, improving overall system efficiency.<br><br>Be aware of the overhead of an edge workload with many connected downstream devices. The edge node must forward or process all messages and handle caching all the data if there's intermittent cloud connectivity. Validate the performance impact on your solution by testing with the planned maximum of downstream devices and messages per edge node. This approach ensures that the edge node can handle the expected load and maintain performance. Additionally, be aware of the performance impact that message translation or enrichment can have on edge, IoT Hub, or cloud event processing, and plan accordingly to maintain system efficiency. |
| **Handle high-volume cloud data**. To optimize performance efficiency for high-volume cloud data, use out-of-the-box service integration between IoT Hub and data destinations like Azure Data Lake Storage and Azure Data Explorer that are already optimized for high performance throughput. Use the Event Hubs SDK to develop custom ingestion from an IoT hub with the included event processor. The event processor can rebalance devices and hosts. Use the right number of IoT Hub partitions and consumer groups for the number of simultaneous data readers and required throughput. Separate the storage needed for data ingestion and event processing from the storage needed for reporting and integration. Use the data storage that fits the needs based on required throughput, size, retention period, data volume, CRUD requirements, and regional replication. Examples are Azure Data Lake Storage, Azure Data Explorer, Azure SQL, or Azure Cosmos DB. For more information, see [Select an Azure data store for your application](/azure/architecture/guide/technology-choices/data-store-decision-tree). | Optimizing performance efficiency for high-volume cloud data ensures that your IoT solution can handle large amounts of data efficiently and effectively. Using out-of-the-box service integration and the Event Hubs SDK helps streamline data ingestion and processing. Properly configuring IoT Hub partitions and consumer groups, and separating storage for different purposes, ensures that data is managed efficiently. Selecting the appropriate data storage based on specific needs helps maintain performance, scalability, and reliability of your IoT solution. |
| **Separate the IoT solution ingestion pipeline from integration processing**. Make sure complex queries or loads don't affect data ingestion performance in IoT Hub. Use well-defined and versioned APIs for access to IoT Hub information regarding device twins, module twins, jobs, and message routing. Avoid tools for end users to create user-defined queries against IoT Hub storage. Consider using separate data stores for integration and for reporting. | Separating the IoT solution ingestion pipeline from integration processing ensures that complex queries or loads from the integration layer don't impact data ingestion performance, maintaining the efficiency and reliability of your IoT Hub. |
| **Use Azure Monitor to collect IoT Hub metrics with alerts for critical metrics**. Set up Azure Monitor alerts based on current scale limits, such as device to cloud messages sent per second. Set the alert to a percentage of the limit, such as 75%, for prenotification of upcoming scalability limits. Also set up Azure Monitor alerts for logs and metrics such as number of throttling errors. Set Azure Service Health service alerts to trigger notifications when IoT Hub status changes. | Using Azure Monitor to collect IoT Hub metrics and set up alerts for critical metrics ensures proactive monitoring and management of your IoT solution. Setting alerts based on current scale limits and a percentage of the limit helps in prenotification of upcoming scalability limits, allowing for timely adjustments. Additionally, setting up alerts for logs and metrics such as throttling errors helps in identifying and addressing issues promptly. Azure Service Health service alerts provide notifications when IoT Hub status changes, ensuring that you're aware of any potential disruptions and can take necessary actions to maintain the reliability and performance of your IoT solution. |

<!-- Required: Azure policies H2

In a list, summarize policies that customers can use for resource
governance. Describe policies in general terms instead of mentioning
them by name. After the summary list, include a statement that provides
a link to your offering's built-in policy definitions.

Example:

Azure provides an extensive set of built-in policies related to
<Azure-offering> and its dependencies. Some of the preceding
recommendations can be audited through Azure Policy. For example,
you can check whether:

For comprehensive governance, review the
[Azure Policy built-in definitions for 
[Azure offering]](/azure/governance/policy/samples/built-in-policies#[Azure-offering-anchor])
and other policies that might impact the security of the [cloud-infrastructure-area].

-->

## Azure policies

Azure provides an extensive set of built-in policies related to IoT Hub and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- IoT Hub has local authentication methods disabled for Service Apis.
- Public network access is disabled for your IoT Hub device provisioning instance so that it's not accessible over the public internet. 
- IoT Hub can only be accessed from a private endpoint.
- Resource logs in IoT Hub are enabled to recreate activity trails to use for investigation purposes; when a security incident occurs or when your network is compromised.

For comprehensive governance, review the [Azure Policy built-in definitions for Internet of Things](/azure/governance/policy/samples/built-in-policies#internet-of-things) and other policies that might impact the security of the IoT Solution.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of IoT Hub.

- [Reliability](/azure/advisor/advisor-reference-reliability-recommendations#azure-iot-hub)
- [Security](/azure/defender-for-cloud/recommendations-reference-iotb)
- [Cost Optimization](/azure/advisor/advisor-reference-cost-recommendations)
- [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations#internet-of-things)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations)

## Next step

For more information about IoT Hub, see [IoT Hub documentation](/azure/iot-hub/).