---
title: Security considerations for Azure Virtual Desktop workloads
description: Examine Azure Virtual Desktop security considerations. See how to protect session hosts, encrypt data, and implement other security measures.
author: BenMartinBaur
ms.author: bebaur
ms.date: 09/15/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avd
---

# Security considerations for Azure Virtual Desktop workloads

This article discusses the security and identity and access management (IAM) design area of an Azure Virtual Desktop workload. [Virtual Desktop](/azure/virtual-desktop/) is a managed service that provides a Microsoft control plane for your virtual desktop infrastructure. Virtual Desktop uses [Azure role-based access control (RBAC)](/azure/role-based-access-control/overview) to control identities and to manage access. As a workload owner, you can also apply other [Zero Trust](/security/zero-trust/azure-infrastructure-avd) principles that are appropriate for your organizational requirements. Examples include the *verify explicitly* principle and the *least-privileged access* principle.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected Framework Azure Virtual Desktop workload](./index.yml) series. If you aren't familiar with this series, we recommend that you start with [What is an Azure Virtual Desktop workload?](./overview.md#what-is-a-virtual-desktop-workload).

## Use RBAC

*Impact: Security, Operational Excellence*

RBAC supports [separation of duties](/azure/well-architected/security/design-identity-role-definitions) for the various teams and individuals who manage the deployment of Virtual Desktop. As part of your landing zone design, you need to decide who assumes the various roles. You then need to create a security group for each role to simplify adding and removing users to and from roles.

Virtual Desktop provides custom Azure roles that are designed for each functional area. For information about how these roles are configured, see [Built-in roles for Azure Virtual Desktop](/azure/virtual-desktop/rbac). You can also create and define [Azure built-in roles](/azure/role-based-access-control/built-in-roles) as part of the Cloud Adoption Framework for Azure deployment. You might need to combine RBAC roles that are specific to Virtual Desktop with other Azure RBAC roles. This approach provides the complete set of permissions that users need for Virtual Desktop and for other Azure services like virtual machines (VMs) and networking.

##### Assessment question

Do you use RBAC and security groups to separate duties and limit access to Virtual Desktop resources?

##### Recommendations

- Define roles for the teams and individuals who manage Virtual Desktop deployments.
- Define Azure built-in roles to separate management responsibilities for host pools, application groups, and workspaces.
- Create a security group for each role.

## Use Microsoft Entra ID Conditional Access policies

*Impact: Security*

Virtual Desktop has many built-in security controls. Virtual Desktop is also fully integrated and supported by Microsoft Entra ID Conditional Access. The Conditional Access feature is a [Zero Trust policy engine](/security/zero-trust/deploy/identity) from Microsoft that takes signals from various sources into account when enforcing policy decisions.

##### Assessment question

Which built-in security features do you use to help protect your Virtual Desktop environment?

##### Recommendations

- Require multifactor authentication. To improve the security of your entire deployment, require multifactor authentication for all users and admins in Virtual Desktop. To learn more, see [Enforce Microsoft Entra ID multifactor authentication for Azure Virtual Desktop using Conditional Access](/azure/virtual-desktop/set-up-mfa).
- Turn on Conditional Access. When you use Conditional Access, you can manage risks before you grant users access to your Virtual Desktop environment. In the process of deciding which users to grant access to, you should also consider who each user is, how they sign in, and which device they're using.

## Enhance session host security

*Impact: Security*

Virtual Desktop uses Remote Desktop Protocol (RDP) for communication between the terminal server, or session hosts, and the end-user client.

RDP is a multichannel protocol that can allow and deny separate virtual channels that carry the following information:

- Presentation data
- Serial device communications
- Licensing information
- Highly encrypted data, such as keyboard and mouse activity

To improve security, you can configure your connection's RDP properties centrally in Virtual Desktop.

##### Assessment question

What measures do you take to improve the security of your session hosts?

##### Recommendations

- Control device redirection by redirecting drives, printers, and USB devices to the user's local device in a remote desktop session. We recommend that you first evaluate your security requirements and check whether you should disable this feature.
- Restrict Windows Explorer access by hiding local and remote drive mappings. This strategy prevents users from discovering sensitive information about system configurations and users.
- Avoid direct RDP access to session hosts in your environment. If you need direct RDP access for administrative purposes or troubleshooting, enable just-in-time access to limit the potential attack surface on the session host.
- Grant users limited permissions when they access local and remote file systems. You can restrict permissions by using access control lists that are built by applying the principle of least privilege. This way, users can only access what they need and can't change or delete critical resources.
- Prevent unwanted software from running on session hosts. You can enable AppLocker for extra security on session hosts. This feature helps ensure that only the apps that you specify can run on the host.

## Design considerations for central platform, identity, and networking teams

*Impact: Security*

[Identity](/azure/virtual-desktop/authentication) is a foundational design principle for Virtual Desktop. Identity is also a key design area that you should treat as a first-class concern within your architectural process.

### Identity design for Virtual Desktop

Virtual Desktop supports different types of identities for accessing corporate resources and applications. As a workload owner, you can select from various types of identity providers according to your business and organizational needs. Review the identity design areas in this section to assess what's best for your workload.

| Identity design | Summary |
| --- | --- |
| Active Directory Domain Services (AD DS) identity | Users must be discoverable through Microsoft Entra ID to access Virtual Desktop. As a result, user identities that exist only in AD DS aren't supported. Standalone Active Directory deployments with Active Directory Federation Services (AD FS) also aren't supported. |
| Hybrid identity | Virtual Desktop supports [hybrid identities](/azure/active-directory/hybrid/whatis-hybrid-identity) through Microsoft Entra ID, including identities that are federated by using AD FS. You can manage these user identities in AD DS and sync them to Microsoft Entra ID by using [Microsoft Entra Connect](/azure/active-directory/hybrid/connect/whatis-azure-ad-connect). You can also use Microsoft Entra ID to manage these identities and sync them to [AD DS](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). |
| Cloud-only identity | Virtual Desktop supports cloud-only identities when you use [VMs that are joined by using Microsoft Entra ID](/azure/virtual-desktop/azure-ad-joined-session-hosts). These users are created and managed directly in Microsoft Entra ID. |

> [!IMPORTANT]
> Virtual Desktop doesn't support business-to-business accounts, Microsoft accounts, or [external identities](/azure/active-directory/external-identities/).

For more information about selecting and implementing an identity and authentication strategy, see [Supported identities and authentication methods](/azure/virtual-desktop/authentication).

##### Assessment question

What's your IAM strategy for Virtual Desktop?

##### Recommendations

- Enforce network and application security. Network and application security controls are baseline security measures for every Virtual Desktop workload. The Virtual Desktop session host network and application require rigorous security review and baseline controls.
- Use a hub-spoke architecture. It's critical to differentiate between shared services and Virtual Desktop application services. A hub-spoke architecture is a good approach to security. You should keep workload-specific resources in their own virtual network that's separate from shared services in the hub. Examples of shared services include management and Domain Name System (DNS) services.
- Use network security groups. You can use network security groups to filter network traffic to and from your Virtual Desktop workload. Service tags and network security group rules provide a way for you to allow or deny access to your Virtual Desktop application. For instance, you can allow access to the Virtual Desktop application ports from on-premises IP address ranges, and you can deny access from the public internet. For more information, see [Network security groups](/azure/virtual-network/network-security-groups-overview). In order to deploy Virtual Desktop and make it available to your users, you must allow specific URLs that your session host VMs can access anytime. For a list of these URLs, see [Required URLs for Azure Virtual Desktop](/azure/virtual-desktop/safe-url-list?tabs=azure).
- Use Azure Private Link with Virtual Desktop to [keep traffic within the Microsoft network and help improve security](/azure/private-link/private-link-overview). When you create a [private endpoint](/azure/private-link/private-endpoint-overview), traffic between your virtual network and the service remains on the Microsoft network. You no longer need to expose your service to the public internet. You can also use a virtual private network (VPN) or Azure ExpressRoute so that users with a Remote Desktop client can connect to your virtual network.
- Use Azure Firewall to help protect Virtual Desktop. Virtual Desktop session hosts run in your virtual network and are subject to the virtual network security controls. If your applications or users need outbound internet access, we recommended that you use Azure Firewall to help protect them and lock down your environment.

## Encrypt data in transit

*Impact: Security*

Encryption in transit applies to the state of data that's moving from one location to another. You can encrypt data in transit in several ways, depending on the nature of the connection. For more information, see [Encryption of data in transit](/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit).

Virtual Desktop uses Transport Layer Security (TLS) version 1.2 for all connections that are initiated from clients and session hosts to the Virtual Desktop infrastructure components. Virtual Desktop uses the same [TLS 1.2 ciphers as Azure Front Door](/azure/frontdoor/end-to-end-tls#supported-cipher-suites). It's important to make sure that client computers and session hosts can use these ciphers. For reverse connect transport, the client and session host connect to the Virtual Desktop gateway. The client and session host then establish a Transmission Control Protocol (TCP) connection. Next, the client and session host validate the Virtual Desktop gateway certificate. RDP is used to establish the base transport. RDP then establishes a nested TLS connection between the client and session host by using the session host certificates.

For more information about network connectivity, see [Understanding Azure Virtual Desktop network connectivity](/azure/virtual-desktop/network-connectivity).

##### Assessment question

How do you encrypt data in transit?

##### Recommendations

- Understand how Virtual Desktop encrypts data in transit.
- Make sure that client computers and your session hosts can use the TLS 1.2 ciphers that Azure Front Door uses.

## Use confidential computing for encrypting data in use

*Impact: Security, Performance Efficiency*

Use confidential computing to protect data in use when you operate in regulated industries such as government, financial services, and healthcare institutes.

The use of confidential VMs for Virtual Desktop is currently in public preview. Confidential VMs increase data privacy and security by protecting data in use. The Azure [DCasv5 and ECasv5](/azure/confidential-computing/confidential-vm-overview) confidential VM series provide a hardware-based trusted execution environment (TEE). This environment features Advanced Micro Devices (AMD) Secure Encrypted Virtualization-Secure Nested Paging (SEV-SNP) security capabilities. These features harden guest protections to deny the hypervisor and other host management code access to VM memory and state. They also help protect against operator access, and they encrypt data in use.

This preview provides support for confidential VMs for version 22H2 of Windows 11. Confidential operating system disk encryption and integrity monitoring will be added to the preview at a later date. Confidential VM support for Windows 10 is planned.

For more information, see the following resources:

- [What is confidential computing?](/azure/confidential-computing/overview)
- [Azure confidential computing virtual machines](/azure/virtual-desktop/security-guide#azure-confidential-computing-virtual-machines)

##### Assessment question

How do you encrypt data in use?

##### Recommendations

- Use confidential computing to protect data in use.
- To build a hardware-based TEE, use the Azure DCasv5 and ECasv5 confidential VM series.

## Related Virtual Desktop security resources

- [Azure security baseline for Azure Virtual Desktop](/security/benchmark/azure/baselines/azure-virtual-desktop-security-baseline)
- [Security best practices](/azure/virtual-desktop/security-guide)

## Next steps

Now that you've looked at best practices for securing Virtual Desktop, investigate operational management procedures for achieving business excellence.

> [!div class="nextstepaction"]
> [Operations](./operations.md)

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./assessment.md)