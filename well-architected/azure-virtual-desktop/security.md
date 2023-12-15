---
title: Security and identity and access management (IAM) considerations for Azure Virtual Desktop workloads
description: Examine Azure Virtual Desktop security considerations. See how to protect session hosts, encrypt data, and implement other security measures.
author: BenMartinBaur
ms.author: bebaur
ms.date: 10/12/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avd
---

# Security and identity and access management (IAM) considerations for Azure Virtual Desktop workloads

This article discusses the security and IAM design area of an Azure Virtual Desktop workload. [Azure Virtual Desktop](/azure/virtual-desktop/) is a managed service that provides a Microsoft control plane for your virtual desktop infrastructure. Azure Virtual Desktop uses [Azure role-based access control (RBAC)](/azure/role-based-access-control/overview) to control identities and to manage access. As a workload owner, you can also apply other [Zero Trust](/security/zero-trust/azure-infrastructure-avd) principles that are appropriate for your organizational requirements. Examples include the *verify explicitly* principle and the *least-privileged access* principle.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected Framework Azure Virtual Desktop workload](./index.yml) series. If you aren't familiar with this series, we recommend that you start with [What is an Azure Virtual Desktop workload?](./overview.md#what-is-an-azure-virtual-desktop-workload).

## Use RBAC

*Impact: Security, Operational Excellence*

RBAC supports [separation of duties](/azure/well-architected/security/identity-access#provide-role-based-authorization) for the various teams and individuals who manage the deployment of Azure Virtual Desktop. As part of your landing zone design, you need to decide who assumes the various roles. You then need to create a security group for each role to simplify adding and removing users to and from roles.

Azure Virtual Desktop provides custom Azure roles that are designed for each functional area. For information about how these roles are configured, see [Built-in roles for Azure Virtual Desktop](/azure/virtual-desktop/rbac). You can also create and define [Azure custom roles](/azure/role-based-access-control/custom-roles) as part of the Cloud Adoption Framework for Azure deployment. You might need to combine RBAC roles that are specific to Azure Virtual Desktop with other Azure RBAC roles. This approach provides the complete set of permissions that users need for Azure Virtual Desktop and for other Azure services like virtual machines (VMs) and networking.

##### Recommendations

- Define roles for the teams and individuals who manage Azure Virtual Desktop deployments.
- Define Azure built-in roles to separate management responsibilities for host pools, application groups, and workspaces.
- Create a security group for each role.

## Enhance the security of your session hosts

*Impact: Security*

Azure Virtual Desktop uses Remote Desktop Protocol (RDP) for communication between the terminal server, or session hosts, and the end-user client.

RDP is a multichannel protocol that can allow and deny separate virtual channels that carry the following information:

- Presentation data
- Serial device communications
- Licensing information
- Highly encrypted data, such as keyboard and mouse activity

To improve security, you can configure your connection's RDP properties centrally in Azure Virtual Desktop.

##### Recommendations

- Restrict Windows Explorer access by hiding local and remote drive mappings. This strategy prevents users from discovering sensitive information about system configurations and users.
- Prevent unwanted software from running on session hosts. You can enable AppLocker for extra security on session hosts. This feature helps ensure that only the apps that you specify can run on the host.
- Use screen capture protection and watermarking to help prevent sensitive information from being captured on client endpoints. When you turn on screen capture protection, remote content is automatically blocked or hidden in screenshots and screen sharing. The Remote Desktop client also hides content from malicious software that captures the screen.
- Use Microsoft Defender Antivirus to help protect your VMs. For more information, see [Configure Microsoft Defender Antivirus on a remote desktop or virtual desktop infrastructure environment](/microsoft-365/security/defender-endpoint/deployment-vdi-microsoft-defender-antivirus).
- Turn on Windows Defender Application Control. Define policies for your drivers and applications, whether or not you trust them.
- Sign out users when they're inactive to preserve resources and prevent unauthorized access. For more information, see [Establish maximum inactive time and disconnection policies](/azure/virtual-desktop/security-guide#establish-maximum-inactive-time-and-disconnection-policies).
- Turn on Microsoft Defender for Cloud for cloud security posture management (CSPM). For more information, see [Onboard non-persistent virtual desktop infrastructure (VDI) devices in Microsoft 365 Defender](/microsoft-365/security/defender-endpoint/configure-endpoints-vdi).

## Design considerations for central platform, identity, and networking teams

*Impact: Security*

[Identity](/azure/virtual-desktop/authentication) is a foundational design principle for Azure Virtual Desktop. Identity is also a key design area that you should treat as a first-class concern within your architectural process.

### Identity design for Azure Virtual Desktop

Azure Virtual Desktop supports different types of identities for accessing corporate resources and applications. As a workload owner, you can select from various types of identity providers according to your business and organizational needs. Review the identity design areas in this section to assess what's best for your workload.

| Identity design | Summary |
| --- | --- |
| Active Directory Domain Services (AD DS) identity | Users must be discoverable through Microsoft Entra ID to access Azure Virtual Desktop. As a result, user identities that exist only in AD DS aren't supported. Standalone Active Directory deployments with Active Directory Federation Services (AD FS) also aren't supported. |
| Hybrid identity | Azure Virtual Desktop supports [hybrid identities](/azure/active-directory/hybrid/whatis-hybrid-identity) through Microsoft Entra ID, including identities that are federated by using AD FS. You can manage these user identities in AD DS and sync them to Microsoft Entra ID by using [Microsoft Entra Connect](/azure/active-directory/hybrid/connect/whatis-azure-ad-connect). You can also use Microsoft Entra ID to manage these identities and sync them to [AD DS](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). |
| Cloud-only identity | Azure Virtual Desktop supports cloud-only identities when you use [VMs that are joined by using Microsoft Entra ID](/azure/virtual-desktop/azure-ad-joined-session-hosts). These users are created and managed directly in Microsoft Entra ID. |

> [!IMPORTANT]
> Azure Virtual Desktop doesn't support business-to-business accounts, Microsoft accounts, or [external identities](/azure/active-directory/external-identities/).

For more information about selecting and implementing an identity and authentication strategy, see [Supported identities and authentication methods](/azure/virtual-desktop/authentication).

##### Recommendations

- Create a dedicated user account with least privileges. When you deploy session hosts, use this account to join the session hosts to a Microsoft Entra Domain Services or AD DS domain.
- Require multifactor authentication. To improve the security of your entire deployment, enforce multifactor authentication for all users and admins in Azure Virtual Desktop. To learn more, see [Enforce Microsoft Entra ID multifactor authentication for Azure Virtual Desktop using Conditional Access](/azure/virtual-desktop/set-up-mfa).
- Turn on Microsoft Entra ID Conditional Access. When you use Conditional Access, you can manage risks before you grant users access to your Azure Virtual Desktop environment. In the process of deciding which users to grant access to, you should also consider who each user is, how they sign in, and which device they're using.

### Secure network design for Azure Virtual Desktop

Without network security measures in place, attackers can gain access to your assets. To protect your resources, it's important to place controls on network traffic. Proper network security controls can help you detect and stop attackers who gain entry into your cloud deployments.

##### Recommendations

- Use a hub-spoke architecture. It's critical to differentiate between shared services and Azure Virtual Desktop application services. A hub-spoke architecture is a good approach to security. You should keep workload-specific resources in their own virtual network that's separate from shared services in the hub. Examples of shared services include management and Domain Name System (DNS) services.
- Use network security groups. You can use network security groups to filter network traffic to and from your Azure Virtual Desktop workload. Service tags and network security group rules provide a way for you to allow or deny access to your Azure Virtual Desktop application. For instance, you can allow access to the Azure Virtual Desktop application ports from on-premises IP address ranges, and you can deny access from the public internet. For more information, see [Network security groups](/azure/virtual-network/network-security-groups-overview). In order to deploy Azure Virtual Desktop and make it available to your users, you must allow specific URLs that your session host VMs can access anytime. For a list of these URLs, see [Required URLs for Azure Virtual Desktop](/azure/virtual-desktop/safe-url-list?tabs=azure).
- Isolate your host pools by placing each host pool in a separate virtual network. Use network security groups with the URLs that Azure Virtual Desktop requires for each subnet.
- Enforce network and application security. Network and application security controls are baseline security measures for every Azure Virtual Desktop workload. The Azure Virtual Desktop session host network and application require rigorous security review and baseline controls.
- Avoid direct RDP access to session hosts in your environment by disabling or blocking the RDP port. If you need direct RDP access for administrative purposes or troubleshooting, use Azure Bastion to connect to session hosts.
- Use Azure Private Link with Azure Virtual Desktop to [keep traffic within the Microsoft network and help improve security](/azure/private-link/private-link-overview). When you create a [private endpoint](/azure/private-link/private-endpoint-overview), traffic between your virtual network and the service remains on the Microsoft network. You no longer need to expose your service to the public internet. You can also use a virtual private network (VPN) or Azure ExpressRoute so that users with a Remote Desktop client can connect to your virtual network.
- Use Azure Firewall to help protect Azure Virtual Desktop. Azure Virtual Desktop session hosts run in your virtual network and are subject to the virtual network security controls. If your applications or users need outbound internet access, we recommended that you use Azure Firewall to help protect them and lock down your environment.

## Encrypt data in transit

*Impact: Security*

Encryption in transit applies to the state of data that's moving from one location to another. You can encrypt data in transit in several ways, depending on the nature of the connection. For more information, see [Encryption of data in transit](/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit).

Azure Virtual Desktop uses Transport Layer Security (TLS) version 1.2 for all connections that are initiated from clients and session hosts to the Azure Virtual Desktop infrastructure components. Azure Virtual Desktop uses the same [TLS 1.2 ciphers as Azure Front Door](/azure/frontdoor/end-to-end-tls#supported-cipher-suites). It's important to make sure that client computers and session hosts can use these ciphers. For reverse connect transport, the client and session host connect to the Azure Virtual Desktop gateway. The client and session host then establish a Transmission Control Protocol (TCP) connection. Next, the client and session host validate the Azure Virtual Desktop gateway certificate. RDP is used to establish the base transport. RDP then establishes a nested TLS connection between the client and session host by using the session host certificates.

For more information about network connectivity, see [Understanding Azure Virtual Desktop network connectivity](/azure/virtual-desktop/network-connectivity).

##### Recommendations

- Understand how Azure Virtual Desktop encrypts data in transit.
- Make sure that client computers and your session hosts can use the TLS 1.2 ciphers that Azure Front Door uses.

## Use confidential computing for encrypting data in use

*Impact: Security, Performance Efficiency*

Use confidential computing to protect data in use when you operate in regulated industries such as government, financial services, and healthcare institutes.

You can use confidential VMs for Azure Virtual Desktop. Confidential VMs increase data privacy and security by protecting data in use. The Azure [DCasv5 and ECasv5](/azure/confidential-computing/confidential-vm-overview) confidential VM series provide a hardware-based trusted execution environment (TEE). This environment features Advanced Micro Devices (AMD) Secure Encrypted Virtualization-Secure Nested Paging (SEV-SNP) security capabilities. These features harden guest protections to deny the hypervisor and other host management code access to VM memory and state. They also help protect against operator access, and they encrypt data in use.

Confidential VMs provide support for versions 22H1, 22H2, and future versions of Windows 11. Confidential VM support for Windows 10 is planned. Confidential operating system disk encryption is available for confidential VMs. Also, integrity monitoring is available during Azure Virtual Desktop host pool provisioning for confidential VMs.

For more information, see the following resources:

- [What is confidential computing?](/azure/confidential-computing/overview)
- [Azure confidential computing virtual machines](/azure/virtual-desktop/security-guide#azure-confidential-computing-virtual-machines)

##### Recommendations

- Use confidential computing to protect data in use.
- Use the Azure DCasv5 and ECasv5 confidential VM series to build a hardware-based TEE.

## Related Azure Virtual Desktop security resources

- [Azure security baseline for Azure Virtual Desktop](/security/benchmark/azure/baselines/azure-virtual-desktop-security-baseline)
- [Security best practices](/azure/virtual-desktop/security-guide)

## Next steps

Now that you've looked at best practices for securing Azure Virtual Desktop, investigate operational management procedures for achieving business excellence.

> [!div class="nextstepaction"]
> [Operational procedures](./operations.md)

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./assessment.md)