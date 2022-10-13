---
title: Security in an IoT workload
description: Includes guidance and recommendations that apply to the security pillar in an IoT workload.
author: dominicbetts
ms.author: dobett
ms.date: 04/08/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---

# Security in your IoT workload

The Internet of Things (IoT) provides vast opportunities to gain insights into the complex relationships of people, systems, and data. Those opportunities also come with the challenge of securing diverse and heterogeneous device-based solutions that may have little or no direct interaction.

The security guidance of Microsoft Azure Well-Architected Framework for IoT provides recommendations to help improve the security of your IoT solution. It will help you identify key considerations for secure solution design and provides guidance on how to implement capabilities that can reduce risk to your IoT solution.

There are multiple personas such as *IoT device builders*, *IoT application developers*, and *IoT solution operators* involved in an IoT solution. They all are required to ensure security in the full lifecycle of an IoT solution.

There are key differences between IoT solutions in traditional IT solutions and operational technology (OT) solutions, such as having devices that are on-premises being used to monitor and control physical devices. These OT devices add different security challenges such as, tampering, packing sniffing, the need for out of band management, and over-the-air (OTA) updates.

When you design an IoT solution, it's important to understand the potential threats to that solution, and add defense in depth as you design and architect it. It's important to design the solution from the start with security in mind because understanding how an attacker might be able to compromise a system helps make sure appropriate mitigations are in place from the start.

Security starts with a threat model. [Threat modeling](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx) offers the greatest value when you incorporate it into the design phase. When you're designing, you have the greatest flexibility to make changes to eliminate threats. To learn more, see [Internet of Things (IoT) security architecture](/azure/iot-fundamentals/iot-security-architecture).

Securing IoT solutions with a [Zero Trust security model](https://www.microsoft.com/security/business/zero-trust) starts with non-IoT specific requirements—specifically ensuring you've implemented the basics to securing identities, their devices, and limit their access. These include explicitly verifying users, having visibility into the devices they're bringing on to the network, and being able to make dynamic access decisions using real-time risk detections. This helps limit the potential impact of users gaining unauthorized access to IoT services and data in the cloud or on-premises, which can lead to both mass information disclosure (such as leaked production data of a factory) and potential elevation of privilege for command and control of cyber-physical systems (such as stopping a factory production line). Factories and OT environments are often easy targets for malware and security breaches due to equipment that can be more than 40 years old, physically vulnerable, and isolated from server level security. For an end-to-end perspective, review the [Azure Well-Architected Framework's security pillar](/azure/architecture/framework/security/overview), which includes guiding principles that are critical to other aspects of your solution.

Guidance for creating zero trust architecture (ZTA) has also been provided by NIST in its [Zero Trust Architecture (nist.gov)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-207.pdf) document. This document provides general deployment models and uses cases where zero trust could improve an enterprise's overall information technology security posture.

## Prerequisites

To assess your IoT workload based on the principles described in the Microsoft Azure Well-Architected Framework, complete the IoT workload questionnaires in the Azure Well-Architected Review assessment:

[Azure Well-Architected Review](/assessments/)

After you complete the assessment, this guide helps you address the key security recommendations identified for your solution.

## Principles

The [Azure Well-Architected Framework for IoT overview](iot-overview.md) refers to the foundational layers in an IoT solution. These layers are shown in the diagram below. Security functions cut across these layers and should follow specific principles and recommendations.

When you extend the overall Well-Architected Framework security pillar, there are principles that relate specifically to IoT. The [Zero Trust Cybersecurity for the Internet of Things](https://aka.ms/iot-zt-paper) whitepaper describes how to apply a Zero Trust approach to your IoT solutions based on customer experiences and Microsoft's own environment.

The key security principles that inform a strong security posture that apply to an IoT solution are:

- **Strong identity** to authenticate devices. Register devices, issue renewable credentials, employ strong authentication for personnel using techniques such as multi-factor authentication or password-less authentication, and use a hardware root of trust to ensure you can trust its identity before making decisions.

- **Least privileged access** to mitigate the impact of a device being compromised. Implement device and workload access control to limit any impact from authenticated identities that may have been compromised or running unapproved workloads.

- **Device health** to gate access or flag devices for remediation. Check security configuration, assess vulnerabilities and insecure passwords, and monitor for active threats and anomalous behavioral alerts to build ongoing risk profiles.

- **Continual updates** to keep devices healthy. Utilize a centralized configuration and compliance management solution and a robust update mechanism to ensure devices are up to date and in a healthy state.

- **Security monitoring and response** to detect and respond to emerging threats. Employ proactive monitoring to rapidly identify unauthorized or compromised devices.

An IoT architecture consists of a set of foundational layers. Layers are realized by using specific technologies, and the IoT Well-Architected Framework highlights options for designing and realizing each layer. There are also cross-cutting layers that enable the design, building, and running of IoT solutions:

:::image type="content" source="media/architecture-layers.svg" alt-text="Diagram that shows the layers and cross-cutting activities in the I o T architecture." border="false":::

The following sections address the layer specifics for the security pillar.

## Design

As part of the threat modeling exercise, you should divide a typical IoT architecture into several components/zones. This article focuses on the IoT aspects of security, but you should take a holistic approach to security. The IoT security guidance focuses on these two zones:

- **Device and field gateway zones**: The immediate physical space around the device and gateway where physical access and/or peer-to-peer digital access is feasible. Many industrial companies use the Purdue model included in the ISA 95 standard to ensure their process control networks protect both the limited bandwidth of the network and the ability to offer real time deterministic behavior. In more recent years, with cyber security events being on the climb from internal and external parties security teams look at the Purdue model as an extra layer of the defense in depth methodology.

- **Cloud gateway and services zone**: Any software component or module running in the cloud that is interfacing with devices and/or gateways for data collection and analysis, as well as for command and control.

Zones are broad way to segment a solution. Each zone often has its own data and authentication and authorization requirements. Zones can also be used to isolate damage and restrict the impact of low trust zones on higher trust zones.

All elements in the architecture are subject to various threats that can be classified according to one of the [six STRIDE categories](/archive/blogs/larryosterman/threat-modeling-again-stride): *spoofing*, *tampering*, *repudiation*, *information disclosure*, *denial of service*, and *elevation of privilege*.

Follow the [SDL](https://www.microsoft.com/sdl) process when you design and build these services.

Adopt DevOps methodologies also focus on security. To learn more, see [Enable DevSecOps with Azure and GitHub](/devops/devsecops/enable-devsecops-azure-github).

### Strong Identity

Strong device identity is delivered through tightly integrated capabilities of IoT devices and services, including:

- A hardware root of trust
- Strong authentication using techniques such as certificates, multi-factor auth, or password-less authentication
- Renewable credentials
- Organizational IoT device registry

When possible, use a hardware *root of trust* (RoT) with the following attributes:

- Secure storage of the credentials proving the identity in a dedicated, tamper-resistant hardware.
- Immutable *onboarding identity* tied to the physical device.
- Unique per-device renewable *operational credentials* for regular device access.

The onboarding identity represents the physical device, and is inseparable from it. It can’t be changed for the device's lifetime, and is typically created and installed during manufacturing.

Given its immutability and lifetime, only use the device onboarding identity to onboard devices into IoT solutions. After onboarding, provision and use a renewable operational identity and credentials for authentication and authorization to the IoT application. Making this identity renewable enables you to manage access and revocation of the devices for operational access. You can apply policy driven gates - such as attestation of device integrity and health - at renewal time.

Protect the supply chain of the hardware RoT, or any other hardware components of an IoT device, to ensure that attacks on supply chain don't compromise the integrity of such devices. This also ensures devices are built to security specifications and design that conform to required compliance regimes.

**Password-less authentication**, usually using standard x509 certificates to prove a device's identity, offers greater protection than secrets such as passwords and symmetric tokens shared between both parties.

Certificates are a strong, standardized mechanism that provides renewable, password-less authentication. Provision operational certificates from a trusted PKI and use a renewal lifetime appropriate for the security posture of their business use, management overhead, and cost. Make renewal automatic to minimize any potential access disruption due to manual rotation, and use standard up to date cryptography techniques. For example, renew through CSR instead of transmitting a private key. Any access granted to a device should be granted based on its operational identity. Support credential revocation (such as CRL when using x509 certificates) to enable immediate removal of device access. For example, to respond to compromise or theft.

Some legacy or resource constrained IoT devices aren't manufactured with or capable of utilizing a strong identity, password-less authentication, or renewable credentials. For these devices, use IoT gateways as guardians to locally interface with these less-capable devices, bridging them to access IoT services with strong identity patterns. This enables Zero Trust adoption today, while transitioning to use more capable devices over time.

If you can't use a hardware root of trust - for example, virtual machines, containers, or any service that embeds an IoT client - use whatever capabilities are available. Common implementations use virtual machines or containers, which don't have hardware root of trust support, but can use password-less authentication and renewable credentials. Defense in depth lets the solution provide redundancies where possible and fills in gaps where necessary. Virtual machines and containers might run from an area with more physical security, such as a data center, as compared to an IoT device in the field.

Use a centralized **Organizational IoT device registry** for your organization's IoT devices to manage their lifecycle and audit device access. This approach is similar to the way you secure the user identities of an organization's workforce to achieve Zero Trust security. Use a cloud-based identity registry to handle the scale, management, and security of an IoT solution. IoT device registry information is used to onboard devices into an IoT solution by verifying that the device identity and credentials are known and authorized. After a device is onboarded, the device registry contains the core properties of the device, including its operational identity and the renewable credentials used to authenticate for everyday use.

You can use IoT device registry data to view the inventory of an organization's IoT devices (including health, patch, and security state), and to query and group devices for scaled operation, management, workload deployment, and access control.

In cases where devices don't connect to Azure services for IoT, use network sensors to detect and inventory unmanaged IoT devices in an organization's network for awareness and monitoring.

### Least-privileged access

Least-privileged access control helps to limit any impact from authenticated identities that may have been compromised or that are running unapproved workloads. For IoT scenarios, this means granting access to solution operators, devices, and their workloads using:

- Device and workload access control, to provide access control to the scoped workloads running on the device.
- Access only from secure locations and systems, granting just-in-time access, and implementing strong authentication mechanisms such as multi-factor auth, password-less authentication.
- Conditional access, to conditionally grant access based on the device's context. Examples include:
  - Location (IP address, GPS location)
  - Uniqueness (access from one location at a time)
  - Time of the day
  - Network traffic patterns
  
Services can also use the device's context to conditionally deploy workloads.

Configure the access management of the IoT cloud gateway to only grant appropriate access permissions based on the functionality required by the backend.

Limit access points to IoT devices and cloud applications by ensuring the ports are kept to minimum access. Build mechanisms to prevent and detect physical tempering of devices.

An IoT solution is used by a person. It's prudent to ensure that the user access is managed through an appropriate access control model such as role based or attribute based access control. To layer least-privileged access for IoT devices and design for defense in depth, use network segmentation to group IoT devices in addition to end point protection, mitigating the impact of a potential compromise. Network micro-segmentation enables isolation of less-capable devices at the network layer, either behind a gateway or on a discrete network segment. Put in place a holistic firewall rule strategy to ensure devices access the network when required for the solution to operate, and blocking access when not allowed. More mature organizations can also implement micro-segmentation policies at multiple layers of the [Purdue model](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture).

### Device health

Following the Zero Trust principle, use device health as a key factor to determine the risk profile (such as trust level) of a device. Use this risk profile as an access gate to ensure only healthy devices can access IoT applications and services, or to identify devices in questionable health for remediation.

According to industry standards, the evaluation of the device health should include:

- Security configuration assessment and attestation - is the device configured securely?
- Vulnerability assessment - is the device running software that is out of date, or software that has known vulnerabilities?
- Insecure credential assessment - is the device using secure credentials (such as certificates) and protocols (such as TLS 1.2+)?
- Active threats and threat alerts.
- Anomalous behavioral alerts such as network patterns and usage deviation.

### Continual updates

The other side of enabling your organization to control device access based on health is the need to proactively maintain production devices in a working, healthy target state.

The capabilities to proactively support healthy devices include:

- Centralized configuration and compliance management, to apply policies and to securely distribute and update certificates.
- Deployable updates to update the full set of software on devices, firmware, drivers, base OS and host applications, and cloud-deployed workloads. The update mechanism should have remote deployment capabilities and should be resilient to changes in environment, operating conditions, and authentication mechanism (for example, a certificate change because of expiry or revocation). The update mechanism should support update rollout verification, and ideally be integrated with pervasive security monitoring to enable scheduled updates for security. You should be able to defer deployments so as to not interfere with the business continuity, but be eventually completed within a well-defined time interval after a vulnerability is detected. Devices that haven't been updated, should be flagged as unhealthy.

### Security monitoring and response

As a defense in depth strategy, monitoring adds an extra layer of protection for managed greenfield devices while also providing a compensating control for legacy unmanaged brownfield devices that don't support agents and can't easily be patched or configured remotely. You need to decide the levels of logging, types of activities that you need to monitor, and the responses required for alerts. Logs should be stored securely and not contain any security details.

As [recommended by CISA](https://us-cert.cisa.gov/ncas/alerts/aa20-205a), security monitoring includes:

- Generating an as-is asset inventory and network map of all IoT/OT devices.
- Identifying all communication protocols used across IoT/OT networks.
- Cataloging all external connections to and from IoT/OT networks.
- Identifying vulnerabilities in IoT/OT devices and using a risk-based approach to mitigate them.
- Implementing a vigilant monitoring program with anomaly detection to detect malicious cyber tactics such as *living off the land* techniques within IoT/OT systems. The program should monitor for unauthorized changes to controllers, unusual behavior from devices,  and audit access and authorization attempts.

Most IoT attacks follow a familiar *kill chain* pattern in which adversaries establish an initial foothold, elevate their privileges, and move laterally across the network. Often, they use privileged credentials to bypass barriers such as next generation firewalls established to enforce network segmentation across subnets. Rapidly detecting and responding to these multistage attacks requires a bird's-eye view across IT, IoT, and OT networks, combined with automation, machine learning, and threat intelligence. Collect signals from the entire environment: all users, devices, applications, and infrastructure, both on-premises, and in multiple clouds. Analyze the signals in centralized security information and event management (SIEM) and extended detection and response (XDR) platforms, where security operations center (SOC) analysts can hunt for and uncover previously unknown threats. Finally, security orchestration and automated response (SOAR) platforms are essential for responding to incidents rapidly and mitigating attacks before they have material impact on your organization. This is accomplished by defining playbooks that are automatically executed when specific incidents are detected. For example, you can automatically block or quarantine compromised devices so they're unable to infect other systems.

The IoT solution needs to be able to perform monitoring and remediation at scale for all its connected devices.

**Data protection**: Data that's ingested into the IoT solution should be protected with the guidance in the overall Azure Well-Architecture. For IoT solutions, these principles extend to the devices too. It's critical to ensure that communication from the device to the cloud is secure and encrypted using the latest Transport Layer Security (TLS) standards. If data is stored on devices (data at rest), use standard encryption algorithms to encrypt the data.

Make sure devices are well protected physically. Turn-off or disable any features that aren't needed on the device, such as physical ports (USB, UART) and connectivity (WIFI, BT). Perform physical removal or covering/blocking when necessary.

When possible, use a firewall on the device to restrict the network access.

## Implement

### Evaluate and deploy a Zero Trust security model

- To learn more about Microsoft's approach, see [Zero Trust](https://www.microsoft.com/security/business/zero-trust).
- Use the Zero Trust Assessment to analyze the gaps in your current protection for identity, endpoints, apps, network, infrastructure and data.
- Use the recommended solutions from the assessment to prioritize your Zero Trust implementation, and move forward with guidance from the Microsoft Zero Trust Deployment Center.
- To help prioritize IoT Zero Trust investments, you can use IIC's [IoT Security Maturity Model](https://www.iiconsortium.org/smm.htm) to help assess the security risks for your business.

### Apply Zero Trust to your existing IoT infrastructure

[Microsoft Defender for IoT](/azure/defender-for-iot/) is an agentless solution that continuously monitors network traffic using IoT-aware behavioral analytics to immediately identify unauthorized or compromised IoT devices. Deploy Microsoft Defender for IoT to:

- Inventory all IoT devices
- Assess all IoT devices for vulnerabilities and provide risk based mitigation recommendations
- Continuously monitor devices for anomalous or unauthorized behavior.

Additionally, Microsoft Defender for IoT is tightly integrated with [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/), a cloud-based SIEM/SOAR platform that supports third-party SOC solutions such as Splunk, IBM QRadar, and ServiceNow.

Explore adding [Azure Sphere guardian modules](/azure-sphere/hardware/guardian-modules) to your critical brownfield devices to enable them to connect to IoT services with Zero Trust capabilities including strong identity, end-to-end encryption, and regular security updates.

Network design and configuration provide other opportunities to build defense in depth by segmenting IoT devices based on their traffic patterns and risk exposure. This minimizes the potential impact of compromised devices and ability of adversaries to pivot to higher-value assets. Network segmentation is typically accomplished using next-generation firewalls.

### Use Zero Trust as criteria to select IoT services

Use **IoT services** that offer the following key zero-trust capabilities:

- Enable full support for Zero Trust user access control via Zero Trust. For example, strong user identities, multi-factor authentication, and conditional user access.
- Include integration with user access control systems for least-privileged access and conditional controls.
- Provide a central device registry for full device inventory and device management.
- Perform mutual authentication, offering renewable device credentials with strong identity verification.
- Enforce least-privileged device access control with conditional access to ensure only devices fulfilling criteria can connect, such as only healthy devices or devices from known locations.
- Support OTA updates to keep devices healthy.
- Enable security monitoring of both the IoT services themselves, and of the range of connected IoT devices.
- Monitor and control access to all public end points and implement authentication and authorization to any calls made to these end points.

Microsoft Azure offers several IoT services that provide Zero Trust capabilities:

- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/), supports an operational registry for organizational IoT devices. Accepts device operational certificates to enable strong identity. Devices can be disabled centrally from Azure IoT Hub to prevent unauthorized connection. Azure IoT Hub supports provisioning of module identities in support of IoT Edge workloads.

- [Azure IoT Hub Device Provisioning Service (DPS)](/azure/iot-dps/about-iot-dps), provides a central device registry for organizational devices to register for onboarding at scale. DPS accepts device certificates to enable onboarding with strong device identity and renewable credentials, registering devices in IoT Hub for their daily operation.

- [Azure Device Update (ADU) for IoT Hub](/azure/iot-hub-device-update/understand-device-update), enables you to deploy OTA updates for your IoT devices. It provides a cloud-hosted solution to connect virtually any device. ADU supports a broad range of IoT operating systems, including Linux and [Azure RTOS](https://azure.microsoft.com/services/rtos/).

- [Microsoft Defender for IoT](/azure/defender-for-iot/) is an agentless, network layer security platform delivering continuous asset discovery, vulnerability management, and threat detection for IoT and OT devices, including proprietary, embedded OT devices as well as legacy Windows systems commonly found in OT environments.

- Microsoft Defender for IoT interoperates with [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) to provide a bird's-eye view of security across your entire enterprise. By collecting data at cloud scale—across all users, devices, applications, and infrastructure, including firewall, NAC, and network switch devices, Sentinel can quickly spot anomalous behaviors indicating potential compromise of IoT/OT devices.

### Use Zero Trust as criteria to select IoT devices

**IoT devices** supporting Zero Trust should:

- Contain a hardware root of trust that it can use to provide a strong device identity.
- Use renewable credentials for regular operation and access.
- Enforce least-privileged access control to local device resources such as cameras, storage, and sensors.
- Emit proper device health signals to services to enable enforcement of conditional access.
- Provide update agents and corresponding software updates for the usable lifetime of the device to ensure security updates can be applied, along with device management capabilities to enable cloud-driven device configuration and automated security response.
- Run security agents that integrate with security monitoring, detection, and response systems.
- Minimize physical attack footprint. For example, no USB ports.

Microsoft Azure offers several products that can be used to support the implementation of IoT devices:

- [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) provides an edge runtime connection to IoT Hub and other services:
  - Supports use of certificates as strong device identities.
  - Supports the PKCS\#11 standard that enables support for device manufacturing identities - and other secrets for operational identities - stored on a TPM or HSM.
- The [Azure IoT platform SDKS](/azure/iot-hub/iot-hub-devguide-sdks) are a set of device client libraries, developer guides, samples, and documentation. Device SDKs implement various security features, such as encryption, authentication, to assist you in developing a robust and secure device application.
- [Azure IoT Hub support for virtual networks](/azure/iot-hub/virtual-network-support) enable you to restrict connectivity to IoT Hub through a VNet that you operate. This network isolation prevents connectivity exposure to the public internet. It can help to prevent exfiltration attacks from sensitive on-premises networks.
- [Azure RTOS](/azure/rtos/) provides a real-time operating system as collection of C-language libraries that you can deploy on a wide range of embedded IoT platforms. Azure RTOS includes a complete TCP/IP stack with TLS 1.2 and 1.3 and basic X.509 capabilities. However, Azure RTOS along with the Azure IoT Embedded SDK is designed to integrate with Azure IoT Hub, DPS, and Microsoft Defender and can utilize some of the same security mechanisms as larger, more expensive IoT devices. With features such as X.509 mutual authentication and support for modern TLS cipher suites such as ECDHE and AES-GCM, customers can build Azure RTOS applications knowing that the basics of secure network communication are covered. In addition to the integration with Azure IoT services, Azure RTOS provides support for Zero Trust design on microcontroller platforms that support hardware security features such as ARM TrustZone (a memory protection/partitioning architecture), secure element devices such as the STSAFE-A110 from ST Microelectronics, and industry standards such as the ARM Platform Security Architecture (PSA), which combines hardware and firmware to provide a standardized set of security features including secure boot, cryptography, attestation, and more.
- [Windows 10 IoT Enterprise](https://www.microsoft.com/WindowsForBusiness/windows-iot) has significant security features that can be used to help ensure security across three key pillars of the IoT security spectrum:

  - Protect data. Securing data means always protecting, including at rest, during code execution, and in motion. This is done by using BitLocker Drive Encryption, Secure Boot, Windows Defender Application Control, Windows Defender Exploit Guard, secure Universal Windows Platform (UWP) applications, Unified Write Filter, a secure communication stack, and security credential management.

  - Monitor and detect. Device Health Attestation (DHA) lets you start with a trusted device and maintain trust over time.

  - Update and manage. You can use Device Update Center and Windows Server Update Services to apply the latest security patches. If you determine that a device might be exposed to a threat, you can remediate that threat by using Azure IoT Hub device management features, Microsoft Intune or third-party mobile device management solutions, and Microsoft System Center Configuration Manager.

Along with the edge platforms above, Microsoft provides a program to certify different device capabilities:

- The [Azure Certified Device program](/azure/certification/overview) empowers device partners to easily differentiate and promote devices, and enables solution builders and end customers to find IoT devices built with features that enable a zero-trust solution.

- [Edge Secured-core program (preview)](/azure/certification/program-requirements-edge-secured-core), which validates whether devices meet more security requirements around device identity, secure boot, operating system hardening, device updates, data protection, and vulnerability disclosures. The Edge Secured-core program requirements have been distilled from various industry requirements and security engineering points of view. The Edge Secured-core program enables Azure services such as the Azure Attestation service to make conditional decisions that are based on the posture of the device, thus enabling the Zero Trust model. Some of the highlights consist of requiring the device to include a hardware root of trust and provide secure boot and firmware protection. Attributes such as these can be measured by the attestation service and used by downstream services to conditionally grant access to sensitive resources.

Microsoft has several solutions available that provide hardware built specifically for IoT scenarios fully integrated with Azure services:

- [Azure Sphere](/azure-sphere/) is a fully managed integrated hardware, OS, and cloud platform solution for medium and low-power IoT devices that meets all seven properties of highly secured devices. Azure Sphere has several features that can help an organization implement Zero Trust. Devices are designed for explicit verification and implement certificate-based Device Attestation and Authentication (DAA), which will automatically renew trust. In addition to supporting the Zero Trust principle of explicit verification, Azure Sphere implements least-privileged access, where applications are denied access by default to all peripheral and connectivity options. This even extends to network connectivity, where permitted web domains must be included in the software manifest or the application isn't able to connect outside of the device. Azure Sphere is built around assumed breach. Protections are layered with defense in depth throughout the OS design, and a secure-world partition—running in Arm TrustZone on Azure Sphere devices—helps segment breaches to the OS from access to Pluton or hardware resources. Azure Sphere can be applied as a guardian module to secure other devices, including existing brownfield systems that weren't designed for trusted connectivity. In this scenario, an Azure Sphere guardian module is deployed with an application designed to interface with an existing product through an interface such as ethernet, serial, or BLE, that doesn't necessarily have direct internet connectivity.

- [Azure Percept](https://aka.ms/azurepercept) is an end-to-end edge AI platform that includes hardware accelerators integrated with Azure AI and IoT services, pre-built AI models, and solution management to help you start your proof of concept in minutes. Azure Percept devices are designed with a hardware root of trust to help protect inference data (in transit and at rest), AI models and privacy-sensitive sensors such as cameras and microphones. It enables device authentication and authorization for Azure Percept Studio services. To learn more, see [Azure Percept security](/azure/azure-percept/overview-percept-security).

## Resources

- [Azure Architecture Center - Azure Architecture Center](https://www.microsoft.com/security/blog/2021/05/05/how-to-apply-a-zero-trust-approach-to-your-iot-solutions/)
- [How to apply a Zero Trust approach to your IoT solutions - Microsoft Security Blog](https://www.microsoft.com/security/blog/2021/05/05/how-to-apply-a-zero-trust-approach-to-your-iot-solutions/)
- [Zero trust whitepaper (microsoft.com)](https://azure.microsoft.com/resources/zero-trust-cybersecurity-for-the-internet-of-things/)
- [IoT Security Architecture](/azure/iot-fundamentals/iot-security-architecture)

## Next steps

> [!div class="nextstepaction"]
> [Overview of an IoT workload](iot-overview.md)


