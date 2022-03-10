---
title: Security in an IoT workload
description: Includes guidance and recommendations that apply to the security pillar in an IoT workload.
author: dominicbetts
ms.author: dobett
ms.date: 02/17/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---

# Security in an IoT workload

The Internet of Things (IoT) provides vast opportunities to gain insights into the complex relationships of people, systems, and data. Those opportunities also come with the challenge of securing diverse and heterogeneous device-based solutions that may have little or no direct interaction.

The security guidance of Microsoft Azure Well-Architected Framework for IoT provides recommendations to help improve the security of your IoT solution. It will help you identify key considerations for secure solution design and provides guidance on how to implement capabilities that can reduce risk to your IoT solution.

There are multiple personas such as *IoT device builders*, *IoT application developers*, and *IoT solution operators* involved in an IoT solution. They all are required to ensure security in the full lifecycle of an IoT solution. To learn more about the different people and their roles, see [Builders, developers, and operators](/azure/architecture/example-scenario/iot/builders-developers-operators).

There are key differences between IoT solutions in traditional IT solutions and operational technology (OT) solutions, such as having devices that are on-premises being used to monitor and control physical devices. These OT devices add different security challenges such as, tampering, packing sniffing, need for out of band management, and over the air updates.

When you design an IoT solution, it is important to understand the potential threats to that solution, and add defense in depth as you design and architect it. It is important to design the solution from the start with security in mind because understanding how an attacker might be able to compromise a system helps make sure appropriate mitigations are in place from the beginning.

Security starts with a threat model. [Threat modeling](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx) offers the greatest value when you incorporate it into the design phase. When you are designing, you have the greatest flexibility to make changes to eliminate threats. To learn more, see [Internet of Things (IoT) security architecture](/azure/iot-fundamentals/iot-security-architecture).

Securing IoT solutions with a [Zero Trust security model](https://www.microsoft.com/security/business/zero-trust) starts with non-IoT specific requirements—specifically ensuring you have implemented the basics to securing identities, their devices, and limit their access. These include explicitly verifying users, having visibility into the devices they’re bringing on to the network, and being able to make dynamic access decisions using real-time risk detections. This helps limit the potential impact of users gaining unauthorized access to IoT services and data in the cloud or on-premises, which can lead to both mass information disclosure (like leaked production data of a factory) and potential elevation of privilege for command and control of cyber-physical systems (like stopping a factory production line). Factories and OT environments are often easy targets for malware and security breaches due to equipment that can be 40+ years old, physically vulnerable, and isolated from server level security. For an end-to-end perspective, review the [Azure Well-Architected Framework’s security pillar](/azure/architecture/framework/security/overview) which includes guiding principles that are critical to other aspects of your solution.

Guidance for creating zero trust architecture (ZTA) has also been provided by NIST in its Zero Trust Architecture document[1]. This document provides general deployment models and use cases where zero trust could improve an enterprise’s overall information technology security posture.

## Principles

Extending the overall Well-Architected Framework security pillar, there are principles that relate specifically for IoT. The [Zero Trust Cybersecurity for the Internet of Things](https://aka.ms/iot-zt-paper) whitepaper describes how to apply a Zero Trust approach to your IoT solutions based on customer experiences and Microsoft’s own environment.

The key security principles that inform a strong security posture that apply to an IoT solution are:

- **Strong identity** to authenticate devices. Register devices, issue renewable credentials, employ strong authentication for personnel using techniques like multi-factor auth or password-less authentication, and use a hardware root of trust to ensure you can trust its identity before making decisions.

- **Least privileged access** to mitigate the impact of a device being compromised. Implement device and workload access control to limit any impact from authenticated identities that may have been compromised or running unapproved workloads.

- **Device health** to gate access or flag devices for remediation. Check security configuration, assess vulnerabilities and insecure passwords, and monitor for active threats and anomalous behavioral alerts to build ongoing risk profiles.

- **Continual updates** to keep devices healthy. Utilize a centralized configuration and compliance management solution and a robust update mechanism to ensure devices are up to date and in a healthy state.

- **Security monitoring and response** to detect and respond to emerging threats. Employ proactive monitoring to rapidly identify unauthorized or compromised devices.

:::image type="content" source="media/architecture-layers.png" alt-text="Diagram that shows the layers and cross-cutting activities in the IoT architecture." border="false":::

## Design

*As part of the threat modelling exercise, you should divide a typical IoT architecture into several components/zones.*

*Holistic approach to security, but we are only focusing on IoT here.*

The IoT security guidance focuses on these two zones:

- **Device and Field Gateway zones**: The immediate physical space around the device and gateway where physical access and/or peer-to-peer digital access is feasible. Many industrial companies leverage the Purdue model included in the ISA 95 standard to ensure their process control networks protect both the limited bandwidth of the network and the ability to offer real time deterministic behavior. In more recent years with cyber security events being on the climb from internal and external parties security teams look at the Purdue model as an additional layer of the defense in depth methodology.

- **Cloud Gateway and Services zone**: Any software component or module running in the cloud that is interfacing with devices and/or gateways for data collection and analysis, as well as for command and control.

Zones are broad way to segment a solution; each zone often has its own data and authentication and authorization requirements. Zones can also be used to isolation damage and restrict the impact of low trust zones on higher trust zones.

All elements in the architecture are subject to various threats which can be classified according to one of the [six STRIDE categories](/archive/blogs/larryosterman/threat-modeling-again-stride): "Spoofing", "Tampering", "Repudiation", "Information disclosure", "Denial of service", and "Elevation of privilege".

Follow [SDL](https://www.microsoft.com/sdl) process for designing and building these services.

Adopt DevOps methodologies with an increased focus on security. To learn more, see [Enable DevSecOps with Azure and GitHub](/devops/devsecops/enable-devsecops-azure-github).

### Strong Identity

Strong device identity is delivered through tightly integrated capabilities of IoT devices and services, including:

-   A hardware root of trust

-   Strong authentication using techniques like certificates, multi-factor auth, or password-less authentication

-   Renewable credentials

-   Organizational IoT device registry.

When possible, use a **Hardware root of trust** (RoT) with the following attributes:

-   secure storage of the credentials proving the identity in a dedicated, tamper-resistant hardware

-   immutable "onboarding" identity tied to the physical device

-   unique per-device renewable “operational” credentials for regular device access

The **“onboarding” identity** represents the physical device, and it is inseparable from it. It cannot be changed for the device’s lifetime (immutable), and it is usually created and installed at manufacturing time.

Given its immutability and lifetime, the device onboarding identity must only be used to onboard devices into IoT solutions, whereon a **renewable** **“operational”** **identity** and credentials are then provisioned and used for authentication and authorization to the IoT application (e.g. what the device can do). Making this renewable allows you to manage access and revocation of the devices for operational access. Policy driven gates (such as attestation of device integrity and health) can be applied at renewal time.

Protecting the supply chain of Hardware Root of Trust, or any other hardware components of an IoT device ensures that attacks on supply chain do not compromise the integrity of such devices. This also ensures devices are built as per security specifications and design conforming to required compliance regimes.

**Password-less authentication**, usually using standardized x509 certificates to prove device’s identity, offers greater protection than “secrets” such as passwords and symmetric tokens shared between both parties.

Certificates are a strong, standardized mechanism providing renewable, password-less authentication. Operational certificates must be provisioned from a trusted PKI and have a renewal lifetime appropriate for the security posture of their business use, and management overhead as well as cost. Their renewal must be automatic to minimize any potential access disruption due to manual rotation, and must use standard and latest cryptography techniques (e.g. renewal through CSR instead of transmission of private key). Any access granted to a device should be granted based on its operational identity. Credential revocation (such as CRL when using x509 certificates) must be supported to enable immediate removal of device access (for example, to respond to compromise or theft).

For IoT devices that are not manufactured with or capable of utilizing a strong identity, password-less authentication, or renewable credentials (legacy devices, constrained resources devices):

- IoT gateways can be used as "guardians" (IoT Edge gateway??) to locally interface with these less-capable devices, bridging them to access IoT services with strong identity patterns. This enables Zero Trust adoption today, while transitioning to use more capable devices over time.

If you cannot use a hardware root of trust (*virtual machines, containers, any service embedding an IoT* *client*), leverage what capabilities are available. Common implementations leverage virtual machines or containers, which don’t have hardware root of trust support, but can leverage password-less authentication and renewable credentials. Defense in depth allows the solution to provide redundancies where possible and fill in gaps where necessary. Virtual machines and containers might run from an area of more physical security, like a data center, versus an IoT device out in public.

**Organizational IoT device** **registry**: similar to securing the user identities of an organization’s workforce to achieve Zero Trust security, it is crucial to have a centralized registry for your organization’s IoT devices to manage their lifecycle and audit device access. A cloud-based identity registry is recommended for handling the scale, management, and security of IoT solutions. IoT device registry information is used to onboard devices into an IoT solution by verifying that the device identity and credentials are known and authorized to onboard to the organization. Once onboarded, the device registry contains the core properties of devices, including their operational identity and renewable credentials used to authenticate for everyday use.

IoT device registry data can be used to view the inventory of an organization’s IoT devices (including health, patch, and security state), and to query and group devices for scaled operation, management, workload deployment, and access control.

In cases where devices do not connect to Azure services for IoT, network sensors can be used to detect and inventory unmanaged IoT devices in an organization’s network for awareness and monitoring.

### Least-privileged access

Least-privileged access control helps to limit any potential impact from authenticated identities that may have been compromised or running unapproved workloads. For IoT scenarios, this means granting access to solution operators, devices, and their workloads using:

-   Device and workload access control, to provide access control to the scoped workloads running on the device

-   Allowing device and solution operators access only from secure locations and systems, granting just-in-time access, and implementing strong authentication mechanisms such as multi-factor auth, password-less authentication, etc.

-   Conditional access, to conditionally grant access based on the device’s context. Some examples are:

    -   Location (IP address, GPS location, …)

    -   Uniqueness (access from one location at a time)

    -   Time of the day

    -   Network traffic patterns

Services can also use the device’s context to conditionally deploy workloads.

IoT Hub can be integrated to other services like Azure services and custom apps. The access management to backend services should be configured to only allow appropriate access permissions based on the functionality required.

Limit access points to IoT Devices and cloud applications (e.g. IoT Hub) by ensuring the ports are kept to minimum access. Build mechanisms to prevent and detect physical tempering of devices.

An IoT solution is used by a person. It is prudent to ensure that the user access is managed through an appropriate access control model such as Role Based Access Control (RBAC) or Attribute Based Access Control (ABAC). To layer least-privileged access for IoT devices, network segmentation should be used to group IoT devices, mitigating potential blast radius of a potential compromise. Network micro-segmentation allows for isolation of less-capable devices at the network layer, either behind a gateway or on a discrete network segment. More mature organizations can also implement micro-segmentation policies at multiple layers of the [Purdue Model](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture).

IoT Edge nested in the implement section?

### Device health

Following the Zero Trust principle, device health should be used as a key factor in determining the risk profile (such as trust level) of a device. This risk profile should be used as an access gate to ensure only healthy devices can access IoT applications and services, or to identify devices in questionable health for remediation action.

According to the industry standards, the evaluation of the device health should include:

-   Security configuration assessment and attestation ("Is the device configured securely?")

-   Vulnerability assessment ("Is the device running software that is out of date, or software that has known vulnerabilities?")

-   Insecure credential assessment ("Is the device using secure credentials (such as certificates) and protocols (such as TLS 1.2+)?")

-   Active threats and threat alerts

-   Anomalous behavioral alerts e.g. network patterns, usage deviation

### Continual updates

The other side of enabling your organization to control device access based on health is the need to proactively maintain production devices in a working, healthy target state.

The capabilities to proactively support healthy devices include:

-   Centralized configuration and compliance management, to apply policies and to securely distribute and update certificates (when used)

-   Deployable updates: to update the full set of software on devices, firmware, drivers, base OS and host applications, and cloud-deployed workloads. The update mechanism should have remote deployment capabilities as well as update rollout verification, and ideally should be integrated with pervasive security monitoring to enable scheduled updates for security.

maybe CI/CD??

### Security monitoring and response

Monitoring adds an additional layer of protection for managed "greenfield" devices while also providing a compensating control for legacy unmanaged "brownfield" devices that don’t support agents and cannot easily be patched or configured remotely. You need to decide the levels of logging, types of activities that you need to monitor, and the responses required for alerts. Logs should be stored securely and not contain any security details.

As [recommended by CISA](https://us-cert.cisa.gov/ncas/alerts/aa20-205a), this includes:

-   Generating an "as-is" asset inventory and network map of all IoT/OT devices

-   Identifying all communication protocols used across IoT/OT networks

-   Cataloging all external connections to and from IoT/OT networks

-   Identifying vulnerabilities in IoT/OT devices and using a risk-based approach to mitigate them

-   Implementing a vigilant monitoring program with anomaly detection to detect malicious cyber tactics like "living off the land" techniques within IoT/OT systems, such as monitoring for unauthorized changes to controllers. Others include auditing access and authorization attempts, unusual behavior from devices etc.

Most IoT attacks follow a familiar "kill chain" pattern in which adversaries establish an initial foothold, elevate their privileges, and move laterally across the network. Often, they will leverage privileged credentials to bypass barriers such as next generation firewalls established to enforce network segmentation across subnets. Rapidly detecting and responding to these multistage attacks requires a bird’s-eye view across IT, IoT, and OT networks, combined with automation, machine learning, and threat intelligence. By collecting signals from the entire environment—across all users, devices, applications, and infrastructure, both on-premises and in multiple clouds—and analyzing them in centralized Security Information and Event Management (SIEM) and Extended Detection and Response (XDR) platforms, security operations center (SOC) analysts can hunt for and uncover previously unknown threats. Finally, Security Orchestration and Automated Response (SOAR) platforms are essential for responding to incidents rapidly and mitigating attacks before they have material impact on your organization. This is accomplished by defining playbooks that are automatically executed when specific incidents are detected. For example, you can automatically block or quarantine compromised devices so they are unable to infect other systems.

The IoT solution needs to be able to perform monitoring and remediation at scale for all the devices that it is connected to.

Data protection

Data that is ingested into the IoT solution should be protected with the guidance in the overall Azure Well-Architecture. For IoT solutions, these principles extend to the devices too. It is critical to ensure that communication from the device to the cloud communication is secure and encrypted using the latest Transport Layer Security (TLS) standards, TLS 1.2. If data is stored on devices (data at rest), standard encryption algorithms should be used.

## Implement

### Evaluate & deploy a Zero Trust security model

-   learn more about Microsoft’s approach to [Zero Trust](https://www.microsoft.com/security/business/zero-trust)

<!-- -->

-   use our Zero Trust Assessment to analyze the gaps in your current protection for identity, endpoints, apps, network, infrastructure and data.

-   use the recommended solutions from the assessment to prioritize your Zero Trust implementation, and move forward with guidance from the Microsoft Zero Trust Deployment Center.

-   To help prioritize IoT Zero Trust investments, you can use IIC’s [IoT Security Maturity Model](https://www.iiconsortium.org/smm.htm) to help assess the security risks for your business.

### Apply Zero Trust to your existing IoT infrastructure:

[Azure Defender for IoT](/azure/defender-for-iot/overview) is an agentless solution that continuously monitors network traffic using IoT-aware behavioral analytics to immediately identify unauthorized or compromised IoT devices. Deploy Azure Defender for IoT to:

-   inventory all IoT devices;

<!-- -->

-   assess them for vulnerabilities and provide risk based mitigation recommendations

-   continuously monitor devices for anomalous or unauthorized behavior.

Additionally, Azure Defender for IoT is tightly integrated with [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/), the Microsoft’s cloud-native SIEM/SOAR platform and it supports third-party SOC solutions such as Splunk, IBM QRadar, and ServiceNow.

Explore adding [Azure Sphere Guardian modules](/azure-sphere/hardware/guardian-modules) to your critical brownfield devices to enable them to connect to IoT services with Zero Trust capabilities including strong identity, end-to-end encryption and regular security updates.

Implement granular network segmentation for IoT devices based on their traffic patterns and risk exposure, to minimize the blast radius of compromised devices and ability for adversaries to pivot to higher-value assets. Network segmentation is typically accomplished using next-generation firewalls.

IoT Edge gateways?

### Use Zero Trust as criteria to select IoT services

Use **IoT services** that offer the following key zero-trust capabilities:

-   Enable full support for user access control via Zero Trust (for example, require strong user identities, multifactor authentication, conditional user access).

-   Include integration with user access control systems for least-privileged access and conditional controls.

-   Provide a central device registry for full device inventory and device management.

-   Perform mutual authentication, offering renewable device credentials with strong identity verification.

-   Enforce least-privileged device access control, and with conditional access to ensure only devices fulfilling criteria can connect (such as only healthy devices or from known locations.)

-   Support over-the-air (OTA) updates to keep devices healthy.

-   Enable security monitoring of both the IoT services themselves, and of the range of connected IoT devices.

Microsoft Azure offers several IoT services that provide Zero Trust capabilities:

-   [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/), supports an operational registry for organizational IoT devices. Accepts device operational certificates to enable strong identity. Devices can be disabled centrally from Azure IoT Hub to prevent unauthorized connection. Azure IoT Hub supports provisioning of module identities in support of IoT Edge workloads.

-   [Azure IoT Hub Device Provisioning Service (DPS)](/azure/iot-dps/about-iot-dps), provides a central device registry for organizational devices to register for onboarding at scale. DPS accepts device certificates to enable onboarding with strong device identity and renewable credentials, registering devices in IoT Hub for their daily operation.

-   [Azure Device Update for IoT Hub](/azure/iot-hub-device-update/understand-device-update), enables you to deploy over-the-air updates (OTA) for your IoT devices. It provides a cloud-hosted solution to connect virtually any device. ADU supports a broad range of IoT operating systems, including Linux and [Azure RTOS](https://azure.microsoft.com/services/rtos/) (real-time operating system) and is extensible via open source.

-   Azure Defender for IoT is an agentless, network layer security platform delivering continuous asset discovery, vulnerability management, and threat detection for IoT and OT devices, including proprietary, embedded OT devices as well as legacy Windows systems commonly found in OT environments.

-   Azure Defender for IoT interoperates with [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/), the industry’s first cloud-native SIEM/SOAR platform on a hyperscale cloud, providing a bird’s-eye view of security across your entire enterprise. By collecting data at cloud scale—across all users, devices, applications, and infrastructure, including firewall, NAC, and network switch devices—Sentinel can quickly spot anomalous behaviors indicating potential compromise of IoT/OT devices.

### Use Zero Trust as criteria to select IoT devices

**IoT devices** supporting Zero Trust should:

-   Contain a hardware root of trust that is utilized to provide a strong device identity.

-   Leverage renewable credentials for regular operation and access.

-   Enforce least-privileged access control to local device resources (such as cameras, storage, or sensors).

-   Emit proper device health signals to services to enable their enforcement of conditional access.

-   Provide updatability agents and corresponding software updates for the usable lifetime of the device to ensure security updates can be applied, along with device management capabilities to enable cloud-driven device configuration and automated security response.

-   Run security agents that integrate with security monitoring, detection, and response systems.

-   Minimize physical attack footprint. E.g. No USB ports

Microsoft Azure offers several products that can be used to support the implementation of IoT devices:

-   [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) runtime provides an edge runtime connection to IoT Hub and other services.

    -   Support for certificates to be used as strong device identities.

    -   Support for PKCS\#11 standard enables support for device manufacturing identities (and other secrets for operational identities, for example) to be stored on TPM/HSM.

-   [Azure IoT platform SDKS](/azure/iot-hub/iot-hub-devguide-sdks), The Azure IoT platform SDKS are a set of device client libraries, developer guides, samples, and documentation. Device SDKs implement a variety of security features, such as, encryption, authentication, and so on, to assist you in developing a robust and secure device application.

-   [Azure IoT Hub support for virtual networks](/azure/iot-hub/virtual-network-support) – Use virtual networks to restrict connectivity to IoT Hub (and other Azure services ) through a VNet that you operate. This network isolation prevents connectivity exposure to the public internet. It can preventing exfiltration attacks from sensitive on-premises networks.

-   Azure RTOS provides a real-time operating system (RTOS) as C-language libraries that can be deployed on a wide range of embedded/IoT platforms. Included in the offering is a complete TCP/IP stack with TLS (1.2 and 1.3) and basic X.509 capability. Given the wide range of possible configurations and platforms, the responsibility of security for Azure RTOS applications leans heavily on the customer. However, Azure RTOS (along with the Azure IoT Embedded SDK) is designed to integrate with Azure IoT Hub, DPS, and Azure Defender and can utilize some of the same security mechanisms as larger, more expensive IoT devices. With features like X.509 mutual authentication and support for modern TLS cipher suites (ECDHE, AES-GCM), customers can build Azure RTOS applications knowing that the basics of secure network communication are covered. In addition to the integration with Azure IoT services, Azure RTOS provides support for Zero Trust design on microcontroller platforms that support hardware security features like ARM TrustZone (a memory protection/partitioning architecture), secure element devices like the STSAFE-A110 from ST Microelectronics, and industry standards like the ARM Platform Security Architecture (PSA), which combines hardware and firmware to provide a standardized set of security features including secure boot, cryptography, attestation, and more. Combining a hardware root of trust from secure elements with the memory protection features of TrustZone with PSA-certified firmware, Azure RTOS customers have the ability to implement Zero Trust designs in even the smallest embedded IoT devices.

-   [Windows 10 IoT Enterprise](https://www.microsoft.com/WindowsForBusiness/windows-iot) has significant security features that can be used to help ensure security across three key pillars of the IoT security spectrum:

    -   Protect data. Securing data means protecting it at all times, including at rest, during code execution, and in motion. This is done by using BitLocker Drive Encryption, Secure Boot, Windows Defender Application Control, Windows Defender Exploit Guard, secure Universal Windows Platform (UWP) applications, Unified Write Filter, a secure communication stack, and security credential management.

    -   Monitor and detect. Device Health Attestation (DHA) lets you start with a trusted device and maintain trust over time. As the device runs. Azure Defender for IoT can help detect and protect against threats.

    -   Update and manage. You can use Device Update Center and Windows Server Update Services (WSUS) to apply the latest security patches. If you determine that a device might be exposed to a threat, you can remediate that threat by using Azure IoT Hub device management features, Microsoft Intune or third-party mobile device management (MDM) solutions, and Microsoft System Center Configuration Manager (Configuration Manager).

Along with the edge platforms above, Microsoft provides a program to certify devices, with a device catalog to indicate devices along with their certifications:

-   The [Azure Certified Device program](/azure/certification/overview) empowers device partners to easily differentiate and promote devices, and enables solution builders and end customers to find IoT devices built to work well with Azure. From intelligent cameras to connected sensors to edge infrastructure, this enhanced IoT device certification program helps device builders increase their product visibility and saves customers time in building solutions.

-   [Edge Secured-core program](/azure/certification/program-requirements-edge-secured-core), which validates whether devices meet additional security requirements around device identity, secure boot, operating system hardening, device updates, data protection, and vulnerability disclosures. The Edge Secured-core program requirements have been distilled from various industry requirements and security engineering points of view. The Edge Secured-core program enables Azure services such as the Azure Attestation service to make conditional decisions based on the posture of the device, thus enabling the Zero Trust model. Some of the highlights consist of requiring the device to include a hardware root of trust and provide secure boot and firmware protection. Attributes such as these can be measured by the attestation service and used by downstream services to conditionally grant access to sensitive resources.

Microsoft has several solutions available which provide hardware built specifically for IoT scenarios fully integrated with Azure services:

-   Azure Sphere is a fully managed integrated hardware, OS, and cloud platform solution for medium and low-power IoT devices that meets all seven properties of highly secured devices. Azure Sphere has several features that can help an organization implement Zero Trust. Devices are designed for explicit verification and implement certificate-based Device Attestation and Authentication (DAA), which will automatically renew trust. In addition to supporting the Zero Trust principle of explicit verification, Azure Sphere implements least-privileged access, where applications are denied access by default to all peripheral and connectivity options. This even extends to network connectivity, where permitted web domains must be included in the software manifest or the application is not able to connect outside of the device. Azure Sphere is built around the Zero Trust principle of assuming breach, even of its own software applications and OS. Protections are layered with defense in depth throughout the OS design, and a secure-world partition—running in Arm TrustZone on Azure Sphere devices—helps segment breaches to the OS from access to Pluton or hardware resources. By adopting the principles of Zero Trust, Azure Sphere helps enable devices that can be used by an organization to apply Zero Trust models throughout their IoT deployments. In addition to the devices themselves, Azure Sphere can be applied as a guardian module to secure other devices, including existing brownfield systems that were not designed for trusted connectivity. In this scenario, an Azure Sphere guardian module will be deployed with an application designed to interface with an existing product through an interface— like ethernet, serial, or BLE—that doesn’t necessarily have direct internet connectivity. The application then translates telemetry and controls from the product’s domain to the cloud, utilizing Azure Sphere’s security to ensure continued trust. From the cloud perspective, this device operates no differently than if it was natively built for IoT. This guardian approach can help bring existing organizations into a Zero Trust model more quickly with a high bar for security even with existing products that have limited connectivity.

-   [Azure Percept](https://aka.ms/azurepercept), a comprehensive, easy-to-use platform with added security for creating edge AI solutions. The end-to-end edge AI platform includes hardware accelerators integrated with Azure AI and IoT services, pre-built AI models, and solution management to help you start your proof of concept in minutes. Azure Percept devices are designed with a hardware root of trust to help protect inference data (in transit and at rest), AI models and privacy-sensitive sensors like cameras and microphones. It enables device authentication and authorization for Azure Percept Studio services. To learn more, see [Azure Percept security](/azure/azure-percept/overview-percept-security).

## Next Steps

To assess your workload using the tenets found in the Microsoft Azure Well-Architected Framework, see the Microsoft Azure Well-Architected Review.

## Resources

[Azure Architecture Center - Azure Architecture Center](https://www.microsoft.com/security/blog/2021/05/05/how-to-apply-a-zero-trust-approach-to-your-iot-solutions/)

[How to apply a Zero Trust approach to your IoT solutions - Microsoft Security Blog](https://www.microsoft.com/security/blog/2021/05/05/how-to-apply-a-zero-trust-approach-to-your-iot-solutions/)

[Zero trust whitepaper (microsoft.com)](https://azure.microsoft.com/resources/zero-trust-cybersecurity-for-the-internet-of-things/)

[IoT Security Architecture](/azure/iot-fundamentals/iot-security-architecture)

[Zero Trust Architecture (nist.gov)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-207.pdf)

## OTHER

From [security ground up](https://docs.microsoft.com/en-us/azure/iot-fundamentals/iot-security-ground-up):

-   [Secure device provisioning and authentication](https://docs.microsoft.com/en-us/azure/iot-fundamentals/iot-security-ground-up#secure-device-provisioning-and-authentication)

    -   The [Azure IoT Hub identity registry](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide) provides secure storage of device identities and security keys for a solution. Individual or groups of device identities can be added to an allow list, or a block list, enabling complete control over device access.

    -   Azure IoT Hub access control policies in the cloud enable activation and disabling any device identity,

-   [Secure connectivity](https://docs.microsoft.com/en-us/azure/iot-fundamentals/iot-security-ground-up%23secure-connectivity)

    -   Durability of messaging: Azure IoT Hub offers durability of messaging between cloud and devices through a system of acknowledgments in response to messages. Additional durability for messaging is achieved by caching messages in the IoT Hub for up to seven days for telemetry and two days for commands.

    -   Efficiency vs. reliability in resource-constrained environments: HTTPS for maximum efficiency, AMQP/MQTT for reliable message delivery.

    -   Scalability

    -   Industry-standard TLS to secure the communication path between devices, field and cloud gateways

    -   In order to protect devices from unsolicited inbound connections, Azure IoT Hub does not open any connection to the device. The device initiates all connections.

    -   Azure IoT Hub durably stores messages for devices and waits for the device to connect. These commands are stored for two days, enabling devices connecting sporadically, due to power or connectivity concerns, to receive these commands. Azure IoT Hub maintains a per-device queue for each device.

-   [Secure processing and storage in the cloud](https://docs.microsoft.com/en-us/azure/iot-fundamentals/iot-security-ground-up#secure-processing-and-storage-in-the-cloud)

    -   policy-based authorization model for data in the cloud, enabling easy access management (including revocation) that can be audited and reviewed (Azure Active Directory – AAD)

    -   store all the keys used by the IoT infrastructure in secure storage, with the ability to roll over in case keys need to be reprovisioned

    -   data?

Security is a collaborative effort among different personas (from [security best-practices](https://docs.microsoft.com/en-us/azure/iot-fundamentals/iot-security-best-practices%23iot-solution-operator)):

-   [IoT hardware manufacturer/integrator](https://docs.microsoft.com/en-us/azure/iot-fundamentals/iot-security-best-practices%23iot-hardware-manufacturerintegrator)

    -   Scope hardware to minimum requirements

    -   Make hardware tamper proof

    -   Build around secure hardware

    -   Make upgrades secure

-   [IoT solution developer](https://docs.microsoft.com/en-us/azure/iot-fundamentals/iot-security-best-practices#iot-solution-developer)

    -   Follow secure software development methodology

    -   Choose open-source software with care

    -   Integrate with care

-   [IoT solution deployer](https://docs.microsoft.com/en-us/azure/iot-fundamentals/iot-security-best-practices#iot-solution-deployer)

    -   Deploy hardware securely

    -   Keep authentication keys safe

-   [IoT solution operator](https://docs.microsoft.com/en-us/azure/iot-fundamentals/iot-security-best-practices%23iot-solution-operator)

    -   Keep the system up-to-date

    -   Protect against malicious activity

    -   Audit frequently

    -   Physically protect the IoT infrastructure

    -   Protect cloud credentials
