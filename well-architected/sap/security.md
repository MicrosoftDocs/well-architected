---
title: SAP workload security
description: SAP workload best practices for security
author: stephen-sumner
ms.author: ssumner
ms.date: 11/14/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom: SAP
---

# SAP workload security

Azure provides all the tools needed to secure your SAP workload. SAP data contains sensitive data about your organization's technical & business footprint. Protecting your SAP architecture with secure authentication methods, hardened networking, and encryption is essential.

SAP on Azure is delivered in the infrastructure as a service (IaaS) cloud model. Microsoft builds security protections into the service at the levels of the physical data center, physical network, physical host, and hypervisor. But you are responsible for areas above the hypervisor, such as the guest operating system for SAP. We recommend you regularly evaluate the services and technologies used to ensure your security posture evolves with the threat landscape. Below are security recommendations for consideration.

## Leverage identity management

Identity management is a framework to enforce the policies that control access to critical resources. Identity management manages the access to SAP workload within or outside its Azure virtual network. There are three identity management use cases to consider for your SAP workload, and the identity management solution differs for each.

**(1) Operating system** - Organizations can improve the security of Windows and Linux virtual machines in Azure by integrating with Azure Active Directory (Azure AD) authentication. Azure AD is a fully managed identity and access management service. Azure AD can authenticate and authorize end user’s access to the SAP operating system. You can use Azure AD to create domains that exist purely on Azure, or it integrate with your on-premises Active Directory identities. Azure AD also integrates with Microsoft 365, Dynamics CRM Online, and many Software-as-a-Service (SaaS) application from partners.

For more information, see:

- [Azure Active Directory Single sign-on (SSO) integration with SAP NetWeaver]( /azure/active-directory/saas-apps/sap-netweaver-tutorial)
- [Log in to a Linux virtual machine in Azure by using Azure AD and OpenSSH]( /azure/active-directory/devices/howto-vm-sign-in-azure-ad-linux)
- [Log in to a Windows virtual machine in Azure by using Azure AD]( /azure/active-directory/devices/howto-vm-sign-in-azure-ad-windows)

**(2) SAP application** – You can access the SAP application with the SAP frontend software (SAP GUI) or a browser with HTTP/S. We recommend configuring single sign-on (SSO) using Azure Active Directory or Active Directory Federation Services (AD FS). SSO allows end users to connect to SAP applications via browser where possible.

**(3) SAP PaaS and SaaS application** - We recommend consulting the SAP Identity Authentication Service for SAP Analytics Cloud, SuccessFactors, and SAP Business Technology Platform. You can also integrate services from the SAP Business Technology Platform with Microsoft Graph using Azure AD and the SAP Identity Authentication Service. For more information, see:

- [Using Azure Active Directory to secure access to SAP platforms and applications](/azure/active-directory/fundamentals/scenario-azure-first-sap-identity-integration).
- [SAP Identity Authentication Service](https://help.sap.com/docs/IDENTITY_AUTHENTICATION)

A common customer scenario is deploying SAP application into Microsoft Teams is a common pattern. This solution requires SSO with Azure AD. We recommend browsing the Microsoft AppSource marketplace to see which SAP apps are available in Microsoft Teams.

We also recommend using System for Cross-Domain Identity Management (SCIM) for Identity propagation. This pattern enables optimal user life cycle. For more information, see:

- [SCIM synchronization with Azure Active Directory]( /azure/active-directory/fundamentals/sync-scim)
- [Configure SAP Cloud Platform Identity Authentication for automatic user provisioning]( /azure/active-directory/saas-apps/sap-cloud-platform-identity-authentication-provisioning-tutorial)

## Enable single sign-on (SSO)

We've mentioned SSO a few times, and we do recommend configuring SSO for your SAP workload. The table below provides the recommended SSO method for the given SAP solution.

| SAP solution | SSO method |
| --- | --- |
|SAP NetWeaver based-web applications such as Fiori, WebGui|Security Assertion Markup Language (SAML)|
|SAP GUI|Kerberos with windows active directory or AAD-Domain services or 3rd party solution|
|SAP PaaS and SaaS applications such as SAP Business Technology Platform (BTP), Analytics Cloud, Cloud Identity Services, [SuccessFactors]( /azure/active-directory/app-provisioning/sap-successfactors-integration-reference), [Cloud for Customer]( /azure/active-directory/saas-apps/sap-customer-cloud-tutorial), [Ariba](/azure/active-directory/saas-apps/ariba-tutorial)|SAML / OAuth / JSON Web Tokens (JWT) and pre-configured authentication flows with Azure AD directly or through proxying with SAP Identity Authentication Service|

For more information, see:

- [SAP HANA SSO](/azure/active-directory/saas-apps/saphana-tutorial)
- [SAP NetWeaver SSO](/azure/active-directory/saas-apps/sap-netweaver-tutorial)
- [SAP Fiori SSO](/azure/active-directory/saas-apps/sap-fiori-tutorial)
- [SAP Cloud Platform SSO](/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial)
- [SuccessFactors SSO](/azure/active-directory/saas-apps/successfactors-tutorial)
- [Azure Active Directory overview](/azure/active-directory/)

## Use role-based access control (RBAC)

It’s important to control access to the SAP workload resources that you deploy. Every Azure subscription has a trust relationship with an Azure AD tenant. We recommend you use Azure role-based access control (Azure RBAC) to grant users within your organization access the SAP application. Grant access by assigning Azure roles to users or groups at a certain scope. The scope can be a subscription, a resource group, or a single resource. The scope depends on the user and how you’ve grouped your SAP workload resources.

For more information, see [Azure AD trust relationship](/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory) and [Azure RBAC](/azure/role-based-access-control/overview).

## Enforce network and application security

Network and application security controls are baseline security measures for every SAP workload. Their importance bears repeating to enforce the idea that the SAP network and application requires rigorous security review and baseline controls to protect.  

**(1) Azure Network Design** - To secure SAP workload, it’s critical to differentiate between shared services and SAP application services. A hub-spoke architecture is a good approach to security. You must keep workload specific resources in its own virtual network separate from the shared services in hub such as management services and DNS.  

For SAP-native setups, you should use SAP Cloud Connector and SAP Private Link for Azure as part of the hub-spoke setup. These technologies support the SAP extension and innovation architecture for the SAP Business Technology Platform (BTP). Azure native integrations don’t require these components because they are fully integrated with Azure virtual networks and APIs.

**(2) Virtual network security** - Network security groups (NSGs) allow you to filter network traffic to and from your SAP workload. You can define NSG rules to allow or deny access to your SAP application. You can allow access to the SAP application ports from on-premises IP addresses ranges and denying public internet access. For more information, see [network security groups](/azure/virtual-network/network-security-groups-overview)

**(3) Application security** - In general, the security best practices for application development also apply in the cloud. These include things like protecting against cross-site request forgery, thwarting cross-site scripting (XSS) attacks, and preventing SQL injection attacks.

Application security groups (ASGs) should be used to make it easier to configure the network security of a workload. The ASG can be used in security rules instead of explicit IPs for VMs. And the VMs are then assigned to ASG. This will support the reuse of the same policy over different application landscapes, because of this abstraction layer. Cloud applications often use managed services that have access keys. Never check access keys into source control. Instead, store application secrets in Azure Key Vault. For more information, see [application security groups](/azure/virtual-network/application-security-groups).

**(4) Internet facing SAP workload** – An internet facing workload must be protected using services like Azure Firewall, Web Application Firewall, Application Gateway to create separation between endpoints.  For more information, see [inbound and outbound internet connections for SAP on Azure]( /azure/architecture/guide/sap/sap-internet-inbound-outbound).

## Encrypt data

Azure includes tools to safeguard data according to your organization's security and compliance needs. It is essential that you encrypt SAP workload data in transit and at rest.

**(1) Encryption at rest** – Encrypting data at rest is a common security requirement. Azure Storage service-side encryption (SSE) is enabled by default for all managed disks, snapshots, and images. Server-side encryption by default using service-managed keys, which is transparent to the application.

We recommend you review and understand server-side encryption (SSE) with customer managed keys (CMK). The combination allows you to encrypt data-at-rest in the operating system (OS) and data disks for available SAP OS combinations.  Azure Disk Encryption doesn’t support all SAP operating systems. The CMK should be stored in Key Vault to help ensure the integrity of the operating system. We also recommend encrypting your SAP databases. Azure Key Vault supports database encryption for SQL Server from the database management system (DBMS) as well as other storage. Below is an encryption workflow to help you visualize the process.

![Workflow for server-side encryption with a customer managed key using Azure Active Directory and Azure Key Vault](./images/sse-cmk.png)

For more information, see:

- [SSE for managed disks]( /azure/virtual-machines/disk-encryption)
- [Azure Storage SSE for data at rest]( /azure/storage/common/storage-service-encryption)
- [SSE using CMK in Azure Key Vault]( /azure/storage/common/customer-managed-keys-configure-existing-account)

When using client-side encryption, customers encrypt the data and upload the data as an encrypted blob. Key management is done by the customer. For more information, see [client-side encryption](/azure/storage/blobs/client-side-encryption).

**(2) Encryption in transit** – It applies to the state of data one move from one location to another location. Data in transit to, from, and between VMs running Windows/Linux can be encrypted in several ways, depending on the nature of the connection. For more information, see [encryption of data in transit]( /azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit).

## Collect and analyze SAP application logs

Log monitoring is an essential for detecting security threats at the application level. We recommend the Microsoft Sentinel Solution for SAP. It’s cloud-native security information and event management (SIEM) solution built for your SAP workload running on a VM. For more information, see [Microsoft Sentinel Solution for SAP](/Azure/sentinel/sap/deployment-overview).

## Next Step

For general security information, see:

- [Azure security documentation]( /azure/security/)
- [Trusted Cloud eBook](https://azure.microsoft.com/explore/trusted-cloud/)

>[!div class="nextstepaction"]
>[Overview](./overview.md)

>[!div class="nextstepaction"]
>[Reliability](./reliability.md)

>[!div class="nextstepaction"]
>[Cost Optimization](./cost-optimization.md)

>[!div class="nextstepaction"]
>[Operational Excellence](./operational-excellence.md)

>[!div class="nextstepaction"]
>[Performance Efficiency](./performance-efficiency.md)
