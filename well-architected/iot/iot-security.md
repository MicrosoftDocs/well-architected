---
title: Security in your IoT workload
description: See guidance and recommendations that apply to the security pillar in a well-architected IoT workload.
author: dominicbetts
ms.author: dobett
ms.date: 08/08/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---

# Security in your IoT workload

IoT solutions have the challenge of securing diverse and heterogeneous device-based workloads with little or no direct interaction. The following security guidance for the [Azure Well-Architected Framework for IoT](iot-overview.md) identifies key considerations and provides design and implementation recommendations.

[IoT device builders, IoT application developers, and IoT solution operators](/azure/architecture/example-scenario/iot/builders-developers-operators) share responsibility for security during the full IoT solution lifecycle. It's important to design the solution from the start with security in mind. Understand the potential threats, and add [defense in depth](https://csrc.nist.gov/glossary/term/defense_in_depth) as you design and architect the solution.

Security planning starts with a [threat model](https://www.microsoft.com/securityengineering/sdl/threatmodeling). Understanding how an attacker might be able to compromise a system helps you ensure appropriate mitigations from the start. Threat modeling offers the greatest value when you incorporate it into the design phase. When you're designing, you have the greatest flexibility to make changes to eliminate threats.

As part of the threat modeling exercise, you can divide a typical IoT architecture into several components or zones: device, device gateway, cloud gateway, and services. Each zone can have its own authentication, authorization, and data requirements. You can use zones to isolate damage and restrict the impact of low-trust zones on higher-trust zones. For more information, see the [Internet of Things (IoT) security architecture](/azure/iot-fundamentals/iot-security-architecture#security-in-iot).

The following key principles specifically extend the Well-Architected Framework security pillar to IoT solutions:

- *Strong identity* to authenticate devices and users. Have a hardware root of trust for trusted identity, register devices, issue renewable credentials, and use passwordless or multi-factor authentication (MFA).
- *Least-privileged access control* to limit impact from compromised devices or identities or unapproved workloads.
- *Device health* to gate device access or flag devices for remediation. Check security configuration, assess vulnerabilities and insecure passwords, monitor for active threats and anomalous behavior, and build ongoing risk profiles.
- Continual *updates* to keep devices healthy. Use a centralized configuration and compliance management solution and a robust update mechanism to ensure devices are up-to-date and healthy.
- Proactive *monitoring and response* to rapidly identify unauthorized or compromised devices and respond to emerging threats.

## Zero trust security model

Instead of assuming everything behind a corporate firewall is safe, a [zero trust security model](https://www.microsoft.com/security/business/zero-trust) fully authenticates, authorizes, and encrypts every access request before granting access. Securing IoT solutions with zero trust starts with implementing basic identity, device, and access security practices. Basic practices include explicitly verifying users, reviewing devices added to the network, and using real-time risk detection to make dynamic access decisions.

These security basics help limit the potential impact of users gaining unauthorized access to cloud or on-premises IoT services and data. Unauthorized access could otherwise lead to mass information disclosure, such as leaked factory production data, or elevation of privilege for cyber-physical systems control, such as stopping a factory production line.

The following resources can help you implement a zero-trust IoT solution:

- The [Microsoft Zero Trust Assessment](https://www.microsoft.com/security/business/zero-trust/maturity-model-assessment-tool) analyzes the gaps in your current protection for identity, endpoints, apps, network, infrastructure, and data. Use the recommended solutions to prioritize your zero trust implementation, and move forward with guidance from the [Microsoft Zero Trust Guidance Center](/security/zero-trust).

- The [Zero Trust Cybersecurity for the Internet of Things](https://aka.ms/iot-zt-paper) technical paper describes how to apply a zero-trust approach to IoT solutions based on Microsoft's environment and customer experiences.

- The NIST [Zero Trust Architecture (nist.gov)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-207.pdf) document provides guidance for creating zero-trust architectures. This document provides general deployment models and use cases where zero trust could improve an enterprise's overall information technology security posture.

## Assess security in your IoT workload

To assess your IoT workload against the Well-Architected Framework Security pillar, complete the security questions for IoT workloads in the [Azure Well-Architected Review assessment](/assessments/?mode=pre-assessment&id=azure-architecture-review). After the assessment identifies key security recommendations for your IoT solution, the following sections can help you implement the recommendations.

## Security in IoT architectural patterns

Most IoT systems use either a [connected products or connected operations architectural pattern](iot-overview.md#iot-architectural-patterns). There are key security differences between these patterns. Connected operations or *operational technology (OT)* solutions often have on-premises devices that monitor and control other physical devices. These OT devices add security challenges such as tampering, packet sniffing, and the need for out-of-band management and over-the-air (OTA) updates.

Factories and OT environments can be easy targets for malware and security breaches, because equipment can be old, physically vulnerable, and isolated from server-level security. For an end-to-end perspective, review the [Azure Well-Architected Framework security pillar](/azure/architecture/framework/security/overview).

## Security in IoT architecture layers

An IoT architecture consists of a set of foundational layers. Layers are realized by using specific technologies, and the [Azure Well-Architected Framework for IoT overview](iot-overview.md) highlights options for designing and realizing each layer. There are also cross-cutting DevOps layers that support designing, building, and running IoT solutions.

:::image type="content" source="media/architecture-layers.svg" alt-text="Diagram that shows the layers and cross-cutting activities in the IoT architecture." border="false":::

Security functions cut across all layers, and should follow specific principles and recommendations. All layers are subject to various threats that can be classified according to the [STRIDE categories](/archive/blogs/larryosterman/threat-modeling-again-stride): *spoofing*, *tampering*, *repudiation*, *information disclosure*, *denial of service*, and *elevation of privilege*. Always follow the [Microsoft Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl) practices when you design and build IoT architectures.

### Device and gateway layer

This architectural layer includes the immediate physical space around the device and gateway that allow physical access or peer-to-peer digital access. Many industrial companies use the [Purdue model](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture) included in the ISA 95 standard to ensure their process control networks (PCNs) both protect their limited network bandwidth and provide real-time deterministic behavior. The Purdue model provides an extra layer of defense-in-depth methodology.

#### Strong device identity

Tightly integrated capabilities of IoT devices and services provide strong device identity. These capabilities include:

- A hardware root of trust.
- Strong authentication using certificates, MFA, or passwordless authentication.
- Renewable credentials.
- Organizational IoT device registry.

A *hardware root of trust* has the following attributes:

- Secure credential storage that proves identity in dedicated, tamper-resistant hardware.
- Immutable onboarding identity tied to the physical device.
- Unique per-device renewable operational credentials for regular device access.

The *onboarding identity* represents and is inseparable from the physical device. This identity is typically created and installed during manufacturing, and can't be changed for the device's lifetime. Given its immutability and lifetime, you should use the device onboarding identity only to onboard the device into the IoT solution.

After onboarding, provision and use a renewable *operational identity* and *credentials* for authentication and authorization to the IoT application. Making this identity renewable lets you manage access and revocation of the device for operational access. You can apply policy-driven gates, such as attestation of device integrity and health, at renewal time.

The hardware root of trust also ensures that devices are built to security specifications and conform to required compliance regimes. Protect the supply chain of the hardware root of trust, or any other hardware components of an IoT device, to ensure that supply chain attacks don't compromise device integrity.

*Passwordless authentication*, usually using standard x509 certificates to prove a device's identity, offers greater protection than secrets such as passwords and symmetric tokens shared between both parties. *Certificates* are a strong, standardized mechanism that provides renewable passwordless authentication. To manage certificates:

- Provision operational certificates from a trusted public key infrastructure (PKI).
- Use a renewal lifetime appropriate for the business use, management overhead, and cost.
- Make renewal automatic, to minimize any potential access disruption due to manual rotation.
- Use standard, up-to-date cryptography techniques. For example, renew through certificate signing requests (CSR) instead of transmitting a private key.
- Grant access to devices based on their operational identity.
- Support credential revocation, such as a certificate revocation list (CRL) when using x509 certificates, to immediately remove device access, for example in response to compromise or theft.

Some legacy or resource-constrained IoT devices can't use a strong identity, passwordless authentication, or renewable credentials. Use IoT gateways as guardians to locally interface with these less-capable devices, bridging them to access IoT services with strong identity patterns. This practice lets you adopt zero trust today, while transitioning to use more capable devices over time.

Virtual machines (VMs), containers, or any service that embeds an IoT client can't use a hardware root of trust. Use available capabilities with these components. For example, VMs or containers, which don't have hardware root of trust support, can use passwordless authentication and renewable credentials. A defense-in-depth solution provides redundancies where possible, and fills in gaps where necessary. For example, you could locate VMs and containers in an area with more physical security, such as a data center, compared to an IoT device in the field.

Use a centralized *organizational IoT device registry* to manage your organization's IoT device lifecycle and audit device access. This approach is similar to the way you secure the user identities of an organization's workforce to achieve zero-trust security. Use a cloud-based identity registry to handle the scale, management, and security of an IoT solution.

IoT device registry information onboards devices into an IoT solution by verifying that the device identity and credentials are known and authorized. After a device is onboarded, the device registry contains the core properties of the device, including its operational identity and the renewable credentials used to authenticate for everyday use.

You can use IoT device registry data to:

- View the inventory of an organization's IoT devices, including health, patch, and security state.
- Query and group devices for scaled operation, management, workload deployment, and access control.

In cases where devices don't connect to IoT Azure services, use network sensors to detect and inventory unmanaged IoT devices in an organization's network for awareness and monitoring.

#### Least-privileged access

Least-privileged access control helps limit impact from authenticated identities that might be compromised or running unapproved workloads. For IoT scenarios, grant solution operators, devices, and workloads access to the solution by using:

- Device and workload access control, for access only to scoped workloads on the device.
- Conditional access only from secure locations and systems.
- Just-in-time access.
- Strong authentication mechanisms such as MFA and passwordless authentication.
- Conditional access based on a device's context, such as IP address or GPS location, uniqueness, time of day, or network traffic patterns. Services can also use device context to conditionally deploy workloads.

To implement effective least-privileged access:

- Configure IoT cloud gateway access management to only grant appropriate access permissions for the functionality the backend requires.
- Limit access points to IoT devices and cloud applications by ensuring the ports are kept to minimum access.
- Build mechanisms to prevent and detect physical device tampering.
- Manage user access through an appropriate access control model, such as role-based or attribute-based access control.
- Layer least-privileged access for IoT devices by using network segmentation.

Network micro-segmentation enables isolation of less-capable devices at the network layer, either behind a gateway or on a discrete network segment. Use network segmentation to group IoT devices, and use endpoint protection to mitigate the impact of potential compromise.

Implement a holistic firewall rule strategy that allows devices to access the network when required, and blocks access when not allowed. To support defense in depth, mature organizations can implement micro-segmentation policies at multiple layers of the Purdue model. If necessary, use firewalls on devices to restrict network access.

#### Device health

Under the zero-trust principle, device health is a key factor to determine the risk profile, such as trust level, of a device. Use the risk profile as an access gate to ensure only healthy devices can access IoT applications and services, or to identify devices in questionable health for remediation.

According to industry standards, device health evaluation should include:

- Security configuration assessment and attestation that the device is configured securely.
- Vulnerability assessment, whether the device is running software that is out of date or has known vulnerabilities.
- Insecure credential assessment, whether the device is using secure credentials, such as certificates, and protocols, such as Transport Layer Security (TLS) 1.2+.
- Active threats and threat alerts.
- Anomalous behavioral alerts, such as network pattern and usage deviation.

#### Use zero-trust criteria to select IoT devices

Explore adding [Azure Sphere guardian modules](/azure-sphere/hardware/guardian-modules) to critical legacy devices to enable them to connect to IoT services with zero-trust capabilities, including strong identity, end-to-end encryption, and regular security updates.

To support zero trust, IoT devices should:

- Contain a hardware root of trust to provide a strong device identity.
- Use renewable credentials for regular operation and access.
- Enforce least-privileged access control to local device resources such as cameras, storage, and sensors.
- Emit proper device health signals to enable enforcement of conditional access.
- Provide update agents and corresponding software updates for the usable lifetime of the device to ensure security updates can be applied, along with device management capabilities to enable cloud-driven device configuration and automated security response.
- Run security agents that integrate with security monitoring, detection, and response systems.
- Minimize physical attack footprint, for example by eliminating USB ports.

Several Azure products and services support security for IoT devices:

- [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge) provides an edge runtime connection to IoT Hub and other Azure services. IoT Edge supports certificates as strong device identities, and supports the PKCS\#11 standard that enables support for device manufacturing identities - and other secrets for operational identities - stored on a TPM or HSM.

- The [Azure IoT platform SDKS](/azure/iot-hub/iot-hub-devguide-sdks) are a set of device client libraries, developer guides, samples, and documentation. Device SDKs implement various security features, such as encryption and authentication, to help you develop a robust and secure device application.

- [Azure RTOS](/azure/rtos) provides a real-time operating system as a collection of C-language libraries that you can deploy on a wide range of embedded IoT device platforms.

  Azure RTOS includes a complete TCP/IP stack with TLS 1.2 and 1.3 and basic X.509 capabilities. Azure RTOS and the Azure IoT Embedded SDK also integrate with Azure IoT Hub, Azure Device Provisioning Service (DPS), and Microsoft Defender, and use some of the same security mechanisms as larger IoT devices. Features such as X.509 mutual authentication and support for modern TLS cipher suites such as ECDHE and AES-GCM cover the basics of secure network communication.

  Azure RTOS also provides support for:

  - Zero-trust design on microcontroller platforms that support hardware security features, such as Arm TrustZone, a memory protection and partitioning architecture.
  - Secure element devices, such as the STSAFE-A110 from ST Microelectronics.
  - Industry standards such as the Arm Platform Security Architecture (PSA), which combines hardware and firmware to provide a standardized set of security features including secure boot, cryptography, and attestation.

- [Windows 10 IoT Enterprise](https://www.microsoft.com/WindowsForBusiness/windows-iot) has features that help ensure security across key pillars of the IoT security spectrum:

  - Protects data at rest, during code execution, and in motion. Features include BitLocker Drive Encryption, Secure Boot, Windows Defender Application Control, Windows Defender Exploit Guard, secure Universal Windows Platform (UWP) applications, Unified Write Filter, a secure communication stack, and security credential management.

  - Monitors and detects with Device Health Attestation (DHA) to let you start with a trusted device and maintain trust over time.

  - Uses Device Update Center and Windows Server Update Services to apply the latest security patches. You can remediate threats to devices by using Azure IoT Hub device management features, Microsoft Intune or third-party mobile device management solutions, and Microsoft System Center Configuration Manager.

- The [Azure Certified Device program](/azure/certification/overview) enables device partners to easily differentiate and promote devices. The program helps solution builders and customers find IoT devices built with features that enable a zero-trust solution.

- The [Edge Secured-core program (preview)](/azure/certification/program-requirements-edge-secured-core) validates whether devices meet security requirements for device identity, secure boot, operating system hardening, device updates, data protection, and vulnerability disclosures. The Edge Secured-core program requirements are distilled from various industry requirements and security engineering points of view. The Edge Secured-core program enables Azure services such as the Azure Attestation service to make conditional decisions based on device posture, thus enabling the zero-trust model. Highlights include requiring the device to include a hardware root of trust and provide secure boot and firmware protection. These attributes can be measured by the attestation service and used by downstream services to conditionally grant access to sensitive resources.

### Device management and modeling layer

This architectural layer includes software components or modules running in the cloud that interface with devices and gateways for data collection and analysis, as well as for command and control. It's critical to ensure that communication from the device to the cloud is secure and encrypted using the latest TLS standards.

Network design and configuration provide opportunities to build defense in depth by segmenting IoT devices based on their traffic patterns and risk exposure. This minimizes the potential impact of compromised devices and the ability of adversaries to pivot to higher-value assets. Network segmentation typically uses next-generation firewalls.

#### Apply zero trust to existing IoT infrastructure

[Microsoft Defender for IoT](/azure/defender-for-iot/) is an agentless solution that continuously monitors network traffic using IoT-aware behavioral analytics to immediately identify unauthorized or compromised IoT devices. Deploy Microsoft Defender for IoT to:

- Inventory all IoT devices.
- Assess all IoT devices for vulnerabilities and provide risk based mitigation recommendations.
- Continuously monitor devices for anomalous or unauthorized behavior.

Microsoft Defender for IoT is tightly integrated with [Microsoft Sentinel](https://azure.microsoft.com/services/azure-sentinel/), a cloud-based security information and event management (SIEM) and security orchestration, automation, and response (SOAR) platform. Microsoft Sentinel supports third-party security operations center (SOC) solutions such as Splunk, IBM QRadar, and ServiceNow.

#### Use zero-trust criteria to select IoT services

Use IoT services that offer the following key zero-trust capabilities:

- Enable full support for zero-trust user access control, for example strong user identities, MFA, and conditional user access.
- Include integration with user access control systems for least-privileged access and conditional controls.
- Provide a central device registry for full device inventory and device management.
- Support mutual authentication, offering renewable device credentials with strong identity verification.
- Enforce least-privileged device access control with conditional access to ensure only devices fulfilling criteria, such as only healthy devices or devices from known locations, can connect.
- Support OTA updates to keep devices healthy.
- Enable security monitoring of both IoT services and connected IoT devices.
- Monitor and control access to all public endpoints, and implement authentication and authorization for any calls made to these endpoints.

Several Azure services provide zero-trust capabilities:

- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) supports an operational registry for organizational IoT devices and accepts device operational certificates to enable strong identity. Azure IoT Hub supports provisioning of module identities to support IoT Edge workloads, and can disable devices centrally to prevent unauthorized connection. 

- [Azure IoT Hub Device Provisioning Service (DPS)](/azure/iot-dps/about-iot-dps) provides a central device registry for organizational devices to register for onboarding at scale. DPS accepts device certificates to enable onboarding with strong device identity and renewable credentials, registering devices in IoT Hub for their daily operation.

- [Azure Device Update (ADU) for IoT Hub](/azure/iot-hub-device-update/understand-device-update) lets you deploy OTA updates for your IoT devices. ADU provides a cloud-hosted solution to connect virtually any device, and supports a broad range of IoT operating systems, including Linux and [Azure RTOS](https://azure.microsoft.com/services/rtos).

- [Azure IoT Hub support for virtual networks](/azure/iot-hub/virtual-network-support) lets you restrict connectivity to IoT Hub through a virtual network that you operate. This network isolation prevents connectivity exposure to the public internet. It can help to prevent exfiltration attacks from sensitive on-premises networks.

- [Microsoft Defender for IoT](/azure/defender-for-iot/) is an agentless, network layer security platform that delivers continuous asset discovery, vulnerability management, and threat detection for IoT and OT devices. Microsoft Defender for IoT supports proprietary embedded OT devices and legacy Windows systems commonly found in OT environments.

- [Microsoft Sentinel](https://azure.microsoft.com/services/azure-sentinel) interoperates with Microsoft Defender for IoT to provide a bird's-eye view of security across your enterprise. Microsoft Sentinel collects data at cloud scale across all users, devices, applications, and infrastructure, including firewalls, network access control, and network switch devices. Microsoft Sentinel can quickly spot anomalous behaviors that indicate potential compromise of IoT or OT devices.

Several Microsoft solutions provide hardware specifically built to fully integrate with Azure services in an IoT solution:

- [Azure Sphere](/azure-sphere) is a fully managed integrated hardware, OS, and cloud platform solution for medium and low-power IoT devices that meets all seven properties of highly secured devices. Azure Sphere has several features that can help an organization implement zero trust. Devices use explicit verification and implement certificate-based Device Attestation and Authentication (DAA), which automatically renews trust. Azure Sphere also implements least-privileged access, where applications are denied access by default to all peripheral and connectivity options. For network connectivity, permitted web domains must be included in the software manifest or the application can't connect outside of the device.

  Azure Sphere is built around assumed breach. Protections are layered with defense in depth throughout the OS design. A secure world partition running in Arm TrustZone on Azure Sphere devices helps segment OS breaches from access to Pluton or hardware resources. You can apply Azure Sphere as a guardian module to secure other devices, including existing legacy systems not designed for trusted connectivity. In this scenario, an Azure Sphere guardian module deploys with an application and interfaces with existing devices through Ethernet, serial, or BLE. The devices don't necessarily have direct internet connectivity.

- [Azure Percept](https://aka.ms/azurepercept) is an end-to-end edge AI platform that can help you start your proof of concept in minutes. Azure Percept includes hardware accelerators integrated with Azure AI and IoT services, pre-built AI models, and solution management. Azure Percept devices have a hardware root of trust to help protect inference data in transit and at rest, AI models, and privacy-sensitive sensors such as cameras and microphones. Azure Percept enables device authentication and authorization for Azure Percept Studio services. For more information, see [Azure Percept security](/azure/azure-percept/overview-percept-security).

### Ingestion and communication layer

Protect data that's ingested into the IoT solution, including data on devices. If data at rest is stored on devices, use standard encryption algorithms to encrypt the data.

Make sure devices are protected physically. Turn off or disable any device features that aren't needed, such as physical USB or UART ports, or WiFi or Bluetooth connectivity. Use physical removal, covering, or blocking when necessary.

## Management and operations

An enterprise IoT solution should provide a strategy for operators to manage the system. DevOps methodologies that proactively focus on security include:

- Centralized configuration and compliance management, to apply policies and to securely distribute and update certificates.
- Deployable updates, to update the full set of software on devices, firmware, drivers, base OS and host applications, and cloud-deployed workloads.

For more information, see [Enable DevSecOps with Azure and GitHub](/devops/devsecops/enable-devsecops-azure-github).

### Continual updates

To control device access based on health, you must proactively maintain production devices in a working, healthy target state. Update mechanisms should have remote deployment capabilities and should be resilient to changes in environment, operating conditions, and authentication mechanism, such as certificate changes because of expiry or revocation.

The update mechanism should support update rollout verification, and ideally be integrated with pervasive security monitoring to enable scheduled updates for security. You should be able to defer updates to not interfere with business continuity, but eventually complete them within a well-defined time interval after you detect a vulnerability. Devices that haven't been updated should be flagged as unhealthy.

### Security monitoring and response

The IoT solution needs to be able to perform monitoring and remediation at scale for all its connected devices. As a defense-in-depth strategy, monitoring adds an extra layer of protection for managed greenfield devices, while providing a compensating control for legacy, unmanaged brownfield devices that don't support agents and can't easily be patched or configured remotely.

You need to decide the levels of logging, types of activities that you need to monitor, and the responses required for alerts. Logs should be stored securely and not contain any security details.

According to the [Cybersecurity and Infrastructure Security Agency (CISA)](https://us-cert.cisa.gov/ncas/alerts/aa20-205a), security monitoring should include:

- Generating an as-is asset inventory and network map of all IoT and OT devices.
- Identifying all communication protocols used across IoT and OT networks.
- Cataloging all external connections to and from networks.
- Identifying vulnerabilities in IoT and OT devices and using a risk-based approach to mitigate them.
- Implementing a vigilant monitoring program with anomaly detection to detect malicious cyber tactics such as *living off the land* within IoT systems. The program should monitor and audit unauthorized changes to controllers, unusual behavior from devices, and access and authorization attempts.

Most IoT attacks follow a *kill chain* pattern, where adversaries establish an initial foothold, elevate their privileges, and move laterally across the network. Often, attackers use privileged credentials to bypass barriers such as next-generation firewalls established to enforce network segmentation across subnets. Rapidly detecting and responding to these multistage attacks requires a bird's-eye view across IT, IoT, and OT networks, combined with automation, machine learning, and threat intelligence.

Collect signals from the entire environment, including all users, devices, applications, and infrastructure, both on-premises and in multiple clouds. Analyze the signals in centralized SIEM and extended detection and response (XDR) platforms, where SOC analysts can hunt for and uncover previously unknown threats. Finally, use SOAR platforms to respond to incidents rapidly and mitigate attacks before they materially impact your organization. You can define playbooks that automatically execute when specific incidents are detected. For example, you can automatically block or quarantine compromised devices so they're unable to infect other systems.

## Related resources

- [How to apply a Zero Trust approach to your IoT solutions](https://www.microsoft.com/security/blog/2021/05/05/how-to-apply-a-zero-trust-approach-to-your-iot-solutions)
- [Zero Trust Cybersecurity for the Internet of Things](https://azure.microsoft.com/resources/zero-trust-cybersecurity-for-the-internet-of-things)
- [Internet of Things (IoT) security architecture](/azure/iot-fundamentals/iot-security-architecture)
- [Industry IoT Consortium Security Maturity Model](https://www.iiconsortium.org/smm.htm)

## Next steps

> [!div class="nextstepaction"]
> [Cost optimization in your IoT workload](iot-cost-optimization.md)

> [!div class="nextstepaction"]
> [Reliability in your IoT workload](iot-reliability.md)

> [!div class="nextstepaction"]
> [Operational excellence in your IoT workload](iot-operational-excellence.md)

> [!div class="nextstepaction"]
> [Performance efficiency in your IoT workload](iot-performance.md)

> [!div class="nextstepaction"]
> [Overview of an IoT workload](iot-overview.md)

