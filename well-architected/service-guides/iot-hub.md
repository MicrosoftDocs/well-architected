---
title: Well-Architected Framework perspective on Azure IoT Hub
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for IoT Hub.
author: asergaz
ms.author: sergaz
ms.topic: conceptual
ms.date: 01/02/2025
ms.service: waf
ms.subservice: waf-service-guide
ms.subservice: well-architected
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

Azure IoT Hub is a managed service hosted in the cloud that acts as a central message hub for communication between an IoT application and its attached devices. You can connect millions of devices and their backend solutions reliably and securely. Almost any device can be connected to an IoT hub.

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
- [Azure IoT Edge](/azure/iot-edge/about-iot-edge?view=iotedge-1.5)
- [Azure Digital Twins](/azure/digital-twins/overview)
- [Azure Sphere](/azure-sphere/product-overview/what-is-azure-sphere?view=azure-sphere-integrated)
- [Microsoft Defender for IoT](/azure/iot/iot-overview-security)

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

#### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the performance of IoT Hub. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Design devices for resiliency**: Design your devices to satisfy the uptime and availability requirements of your end-to-end solution. Ensure that your IoT device can operate efficiently with intermittent connectivity to the cloud.
> - **Design for business requirements**: Cost implications are inevitable when introducing architectural modifications to meet service-level agreements (SLAs). For example, to have greater reliability and high availability you can implement cross-region redundancies and an automated system to autoscale. This trade-off should be carefully considered.
> - **Safe, simple update procedures**: An enterprise IoT solution should provide a strategy for how operators manage devices. IoT operators require simple and reliable update tools and practices.
> - **Observe application health**: Define service-level indicators (SLIs) and service-level objectives (SLOs) based on observability. Add processes for auditing, monitoring, and alerting beyond what IoT Hub provides.
> - **High availability and disaster recovery (HA/DR) for critical components**: Resilient hardware and software components that build in redundancy, including cross-region redundancies. Determine which of the options outlined in [IoT Hub high availability and disaster recovery](/azure/iot-hub/iot-hub-ha-dr) article best suit your business objectives.
> - **Plan for capacity**: Plan for service quotas and throttles, latency between the detection-action, and establish benchmarks at production scale to support uninterrupted data flow.

#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Calculate the IoT Hub capacity you need, for example, using number of messages/day and other limits. If your workload has a fluctuating need for capacity, implement an auto-scaling mechanism so that it increases and decreases capacity with demand. For more information, see [Choose the right IoT Hub tier and size for your solution](/azure/iot-hub/iot-hub-scaling). | This approach helps in optimizing resource usage, maintaining performance, and controlling costs. By scaling the capacity up or down based on demand, you can ensure that your solution remains responsive and reliable without over-provisioning resources. |
| Design resilient applications by adding a device reconnection strategy to IoT Hub. Follow guidance in [Manage device reconnections to create resilient applications](/azure/iot/concepts-manage-device-reconnections). | It's important to have a strategy to reconnect devices. Without a reconnection strategy, you could see a negative effect on your solution's performance, availability, and cost. |
| Evaluate the trade-offs of different High Availability (HA) and Disaster Recovery (DR) options offered by IoT Hub. Depending on the uptime goals you define for your IoT solution, you should determine which of the options outlined in [IoT Hub high availability and disaster recovery](/azure/iot-hub/iot-hub-ha-dr) best suit your business objectives. | Selecting between [Microsoft-initiated failover](/azure/iot-hub/iot-hub-ha-dr#microsoft-initiated-failover), [manual failover](/azure/iot-hub/iot-hub-ha-dr#manual-failover) or [cross region HA](/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha) will help you implement a resilient IoT solution and properly estimate cost and complexity.|
| Use [DPS](/azure/iot-dps/about-iot-dps) to provision your devices and assign them to an IoT hub. | DPS is a helper service for IoT Hub that enables zero-touch, just-in-time provisioning to the right IoT hub without requiring human intervention. DPS enables the provisioning of millions of devices in a secure and scalable manner. |
| Use [Device Update for IoT Hub](/azure/iot-hub-device-update) to manage over-the-air (OTA) updates for your IoT devices. Ensure that your update strategy includes gradual rollout, resilient A/B updates, detailed management, and reporting tools. | This approach ensures safe, secure, and reliable updates for IoT devices, reducing downtime and operational inefficiencies. It helps maintain device compliance and quickly identifies and resolves update failures, enhancing overall system reliability. |
| Implement DevOps practices for managing IoT solutions, including continuous integration and continuous deployment (CI/CD), monitoring, and automated updates. Use [DevOps to build and release your IoT Edge applications](/azure/iot-edge/how-to-continuous-integration-continuous-deployment), [Azure Monitor to monitor and get alerts from the data collected by IoT Hub](/azure/iot-hub/monitor-iot-hub) and [automatic device management in IoT Hub to automate the management of devices at scale](/azure/iot-hub/iot-hub-automatic-device-management). | DevOps practices enhance the reliability and efficiency of IoT solutions by enabling rapid deployment, continuous monitoring, and automated updates. This approach reduces downtime, improves system performance, and ensures that updates and changes are deployed safely and consistently. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security/principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of your IoT Hub.

#### Design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md). Identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Use a strong identity to authenticate devices and users**: Have a hardware root of trust for trusted identity, register devices, issue renewable credentials, and use passwordless or multi-factor authentication (MFA).
> - **Automate and use least-privileged access control:** Limit the impact from compromised devices or identities, or unapproved workloads.
> - **Device health:** Evaluate device health to gate device access or flag devices for remediation. Check security configuration, assess vulnerabilities and insecure passwords, monitor for threats and anomalies, and build ongoing risk profiles.
> - **Device update:** Continuous updates to keep devices healthy. Use a centralized configuration and compliance management solution and a robust update mechanism to ensure devices are up-to-date and healthy.
> - **Monitor system security, plan incident response:** Proactively monitor for unauthorized or compromised devices and respond to emerging threats.
> - **Connection security:** Ensure all data transmitted between the IoT device and the IoT cloud services is confidential and tamper-proof.

#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Apply zero-trust criteria for devices. Devices connected to IoT Hub should contain an [hardware security module (HSM)](/azure/iot-dps/concepts-tpm-attestation) for strong identity, use [renewable credentials](/azure/iot-dps/how-to-roll-certificates), enforce least-privileged access control, emit proper health signals for conditional access and alert operators to [revoke a device from IoT Hub when compromised](azure/iot-dps/how-to-revoke-device-access-portal), provide [update agents for security updates](/azure/iot-hub-device-update/), include device management capabilities for cloud-driven configuration and automated security response, minimize physical attack footprint by disabling unnecessary features, and [protect data at rest using standard encryption algorithms](azure/iot-hub/iot-hub-tls-support). | Applying zero-trust criteria for devices connected to IoT Hub enhances security and reliability.<br><br> By using hardware security modules (HSM) for strong identity, renewable credentials, and enforcing least-privileged access control, unauthorized access and compromised devices are minimized.<br><br> Emitting health signals for conditional access and providing update agents ensure devices remain secure and compliant. Integrating cloud-driven device management and automated security response, along with running security agents, strengthens the security posture.<br><br> Minimizing the physical attack footprint and protecting data at rest with encryption safeguards sensitive information and maintains system integrity.  |
| Use X.509 certificates to authenticate your devices to IoT Hub. To learn more, see [Authenticate identities with X.509 certificates](/azure/iot-hub/authenticate-authorize-x509). | IoT Hub support both X509 certificate-based authentication and security tokens as methods for a device to authenticate. Use X509-based authentication in production environments as it provides greater security. |
| To layer least-privileged access for IoT devices, use [network segmentation](/security/benchmark/azure/baselines/iot-hub-security-baseline?toc=/azure/iot-hub/TOC.json#network-security) to group IoT devices, mitigating potential impact of a potential compromise. | Network segmentation can group IoT devices, mitigating potential impact of a compromise. A common approach is to connect IoT devices to an "IoT network" for devices such as printers, VoIP phones, and smart TVs. This IoT network is separate from other organizational resources that the workforce accesses.<br><br> Network micro-segmentation lets you isolate less-capable devices at the network layer, either behind a gateway or on a discrete network segment. For example, you can logically separate dedicated OT environments from the corporate IT network using zone (DMZ) network architecture with firewalls. More mature organizations can also implement micro-segmentation policies at multiple layers of the Purdue Model, typically using next-gen firewalls. |
| Use [Microsoft Defender for IoT](/azure/defender-for-iot/organizations/overview) as the frontline of defense to protect your resources in Azure. | Microsoft Defender for IoT is an agentless, network layer security platform that delivers continuous asset discovery, vulnerability management, and threat detection for IoT devices. Defender for IoT continuously monitors network traffic using IoT-aware behavioral analytics to identify unauthorized or compromised components. |
| Use IoT Hub with [Azure Sphere](/azure-sphere) as a guardian module to secure other devices, including existing legacy systems not designed for trusted connectivity. | Azure Sphere can be a guardian module to secure other devices, including existing legacy systems not designed for trusted connectivity. In this scenario, an Azure Sphere guardian module deploys with an application and interfaces with existing devices through Ethernet, serial, or BLE. The devices don't necessarily have direct internet connectivity. |
| Use [IoT Edge gateways](azure/iot-edge/iot-edge-as-gateway) to enforce strong identity patterns for less capable devices. | IoT Edge provides an edge runtime connection to IoT Hub, and supports certificates as strong device identities. IoT Edge supports the PKCS#11 standard for device manufacturing identities and other secrets stored on a Trusted Platform Module (TPM) or Hardware Security Module (HSM). |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to IoT Hub and its environment.

#### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Develop cost-management discipline:** Understand total cost of ownership (TCO) by accounting for both functional and nonfunctional costs when planning.
> - **Use industry-standard strategies and approaches:** For IoT specific industries, like manufacturing, building structures, IoT systems, smart cities, energy grids, use [industry-standard strategies and approaches](/azure/digital-twins/concepts-ontologies) to optimize costs.
> - **Understand the cost of protecting your IoT solution:** As IoT provides end-to-end solutions, security costs are incurred from silicon to cloud. For example, MPU, devices, telecommunication, cloud and operation technology. There are many options to implement security requirements and affect costs.
> - **Design for rate optimization:** Define implementation plans for each [IoT solution-wide concern](/azure/iot/iot-introduction#solution-wide-concerns) to optimize costs.
> - **Monitor and optimize over time:** Continuously monitor and [optimize IoT Hub costs](/azure/iot-hub/iot-hub-devguide-pricing) with ongoing cost optimization activities after you implement your solution.
> - **Estimate realistic costs:** Select the [appropriate IoT Hub tier and size](/azure/iot-hub/iot-hub-scaling) for both development and operational phases of your IoT solution.
> - **Evaluate one-time vs. recurring costs:** Consider the cost benefits of one-time costs versus recurring costs.  For example, in security where hacking techniques are always changing, it can be best to import a reliable commercial operating system and module such as Azure Sphere. For a one-time payment, such services provide ongoing monthly device security patches.
> - **Optimize resource usage:** Implement [auto-scaling mechanisms](/azure/iot-hub/iot-hub-scaling#auto-scale) to adjust IoT Hub capacity based on demand, ensuring efficient resource usage and cost control.
> - **Leverage reserved capacity:** Consider the [size and frequency of message payloads](/azure/iot-hub/iot-hub-devguide-pricing#charges-per-operation) to ensure your IoT Hub has the correct size and ready to scale.
> - **Implement cost alerts and budgets:** Set up cost alerts and budgets in Azure Cost Management to track and control your spending effectively. Cost management APIs are available for [IoT Hub](/rest/api/iothub), and [DPS](/rest/api/iot-dps).

#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Understand [IoT Hub quotas and throttling limits](/azure/iot-hub/iot-hub-devguide-quotas-throttling) and how they impact total cost of ownership (TCO) on running at scale in production.| Your cost model should include the following areas based on running at scale in production: devices, infrastructure, operations, and monitoring. Every service has its own set of quotas and throttling limits. Understanding these limits in IoT Hub is a key part of cost optimization. |
| Understand the device ecosystem and the various roles involved, such as hardware manufacturers, app developers and operators for planning. Evaluate [Best practices for device configuration within an IoT solution](/azure/iot-hub/iot-hub-configuration-best-practices), to help you understand more about device management. | There are small 8-bit MCUs all the way up to the latest x86 CPUs as found in a desktop computer. Many variables factor into the decision for which hardware to choose. Understanding the [key hardware differentiators](/azure/iot/concepts-iot-device-types#key-hardware-differentiators) in an IoT device, will help you reduce costs. |
| Use [IoT Plug and Play](/azure/iot/overview-iot-plug-and-play) to reduce hardware development and on-boarding time. Adopt a [DTDL industry ontology](/azure/digital-twins/concepts-ontologies-adopt). | IoT Plug and Play allows solution builders to integrate IoT devices with IoT Hub without any manual configuration. At the core, it's a device model that a device uses to advertise its capabilities to an IoT Plug and Play-enabled application. |
| Use [Device Update for IoT Hub](/azure/iot-hub-device-update/understand-device-update) to deliver over-the-air updates and remotely manage your devices. | Implementing a solution to remotely update the device's firmware or software is a key way to lower manual labor costs in the long run. |
| Use connectors for Azure services such as Power Apps, Power Automate and Logic Apps. | IoT Hub, together with [Azure Event Grid provides connectors](/connectors/azureeventgrid/) to implement your requirements within Logic Apps and Power Automate. |
| Use [device simulators](/azure/iot-hub/tutorial-connectivity) and set up a load testing environment, to test the solution at production scale and understand the solution cost. | Device simulators can help save costs at scale when there's a need to model, test, and simulate outcomes for any given scenario. Simulation code samples like the [Azure IoT Device Telemetry Simulator](/samples/azure-samples/iot-telemetry-simulator/azure-iot-device-telemetry-simulator/) help you test and estimate costs at scale with various parameters. |
| To reduce transmission sizes and costs: [choose the right protocol](/azure/iot-hub/iot-hub-devguide-protocols) for your IoT devices, compress telemetry at the edge, [batch messages](/azure/iot-hub/iot-hub-devguide-file-upload) on the device, choose between keeping connections alive or reconnecting when the devices wake up, use device twin to exchange status information asynchronously if cost is critical. | Choosing the right protocol for your scenario enables devices to reduce transmission sizes and costs in the transport layer.<br><br> For battery-powered IoT devices, you can choose between keeping connections alive or reconnecting when the devices wake up. Keep alive, or heartbeat messages are essential for checking device status but adds up to the network costs of transmission. In IoT Hub, messages exchanged to keep the connection open and alive are not charged. Reconnecting consumes packets around 6-KB for TLS connection, device authentication, and retrieving a device twin, but saves battery capacity if the device wakes up only once or twice per day. |
| Understand how to use hot, warm, and cold path analytics for IoT data and apply the [lambda architecture](/azure/architecture/databases/guide/big-data-architectures#lambda-architecture). Use the built-in [message routing](/azure/iot-hub/iot-hub-devguide-messages-d2c) feature in IoT Hub. | IoT solutions can store large amounts of data. Storage costs are a large part of the overall solution cost and you should choose an appropriate processing and storage plan based on your business scenario. |

<!-- Required: Operational Excellence H2 

Include the standard description for the pillar.

-->

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development
practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles)
provide a high-level design strategy for achieving those goals for the
operational requirements of the workload.

<!-- Required: Design checklist H4

In the first H4 of the pillar section, lead readers through
design principles by:

- Using standardized text that contains a link to the design
review checklist for the pillar.
- Presenting a checklist of the pillar's design review recommendations
that are relevant for your Azure offering.

For each applicable principle:

- Discuss considerations that relate to that checklist item.
- Provide links to conceptual articles in product documentation
if needed.
- Focus on areas of architectural concern for the architect, not
on specific configuration settings.

-->

#### Design checklist

> [!div class="checklist"]
>
> - [design-consideration]
> - [design-consideration]
>   ...
> - [design-consideration]

<!-- Required: Recommendations H4

In the second H4 of the pillar section, present a table of
recommendations for optimizing the configuration of your Azure
offering. The recommendations should relate to the pillar and
show how to materialize the vision of the preceding design principles.

-->

#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| ... | ... |

<!-- Required: Performance Efficiency H2

Include a standardized description of the pillar.

-->

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when
there's an increase in load** by managing capacity. The strategy includes
scaling resources, identifying and optimizing potential bottlenecks, and
optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles)
provide a high-level design strategy for achieving those capacity goals
against the expected usage.

<!--  Required: Design checklist H4

In the first H4 of the pillar section, lead readers through
design principles by:

- Using standardized text that contains a link to the design
review checklist for the pillar.
- Presenting a checklist of the pillar's design review recommendations
that are relevant for your Azure offering.

Start your design strategy based on the
[design review checklist for Performance Efficiency](../performance-efficiency/checklist.md).
Define a baseline that's based on key performance indicators for [Azure offering].

For each applicable principle:

- Discuss considerations that relate to that checklist item.
- Provide links to conceptual articles in product documentation
if needed.
- Focus on areas of architectural concern for the architect, not
on specific configuration settings.

-->

#### Design checklist

> [!div class="checklist"]
>
> - [design-consideration]
> - [design-consideration]
>   ...
> - [design-consideration]


<!-- Required: Recommendations H4

In the second H4 of the pillar section, present a table of
recommendations for optimizing the configuration of your Azure
offering. The recommendations should relate to the pillar and
show how to materialize the vision of the preceding design principles.

-->

#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| ... | ... |

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

[Add your content.]

- [policy-description]
- [policy-description]
  ...
- [policy-description]

<!-- Required: Azure Advisor recommendations H2

Provide links to Azure Advisor guidance that helps improve the
configuration of your Azure offering.

Example:

'Azure Advisor is a personalized cloud consultant that helps you follow
best practices to optimize your Azure deployments. Here are some
recommendations that can help you improve the reliability, security,
cost effectiveness, performance, and operational excellence of [Azure offering].

- [Reliability](/azure/advisor/advisor-high-availability-recommendations#[offering-or-infrastructure-area-anchor])
- [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations#[offering-or-infrastructure-area-anchor])
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations#[offering-or-infrastructure-area-anchor])
- [Performance](/azure/advisor/advisor-reference-performance-recommendations#[offering-or-infrastructure-area-anchor])
- [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations#[offering-or-infrastructure-area-anchor])'

-->

## Azure Advisor recommendations

[Add your content.]

<!-- Optional: Related resources H2

List code artifacts or any other resources that might be beneficial
to the reader, such as custom queries and tools. Separate the resources
into H3 sections with headings that indicate the purpose of the resources. 

-->

## Related resources

[Add your content.]

<!-- Required: Next step or Related content H2

Consider adding one of these H2 sections (not both):

A "Next step" section that uses 1 link in a blue box 
to point to a next, consecutive article in a sequence.

-or- 

A "Related content" section that lists links to 
1 to 3 articles the user might find helpful.

-->

## Next step -or- Related content

> [!div class="nextstepaction"]
> [Next sequential article title](link.md)

-or-

- [Related article title](link.md)
- [Related article title](link.md)
- [Related article title](link.md)

<!--

Remove all the comments in this template before you
sign off or merge to the main branch.

-->