---
title: SAP workload security
description: SAP workload best practices for security
author: stephen-sumner
ms.author: ssumner
ms.date: 11/11/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom: SAP
---

# SAP workload security

Azure provides all the tools needed to secure your SAP workload. SAP data contains sensitive data about your organization's technical footprint. You can secure your SAP architecture with secure authentication methods, hardened networking, and encryption are essential.

SAP on Azure is delivered in the infrastructure as a service (IaaS) cloud model. Microsoft builds security protections into the service at the levels of the physical data center, physical network, physical host, and hypervisor. For areas above the hypervisor, such as the guest operating system for SAP, you need to carefully evaluate the services and technologies that you use for proper security control of your architecture. For more information, see [Azure security documentation](/azure/security/).

Below are our security recommendations.

## Enhance identity management

Consider using Azure Active Directory (Azure AD) to authenticate and authorize users. Azure AD is a fully managed identity and access management service. You can use Azure AD to create domains that exist purely on Azure, or to integrate with your on-premises Active Directory identities. Azure AD also integrates with Microsoft 365, Dynamics CRM Online, and many Software-as-a-Service (SaaS) applications from partners.

For consumer-facing applications, Azure AD B2C lets users authenticate with their existing social accounts (such as Facebook, Google, or LinkedIn). It also lets users create a new user account that is managed by Azure AD. If you want to integrate an on-premises Active Directory environment with an Azure network, several approaches are possible depending on your requirements. For more information, see [identity management architectures](/azure/architecture/reference-architectures/identity/).

For authentication, you can take advantage of Azure Active Directory (Azure AD) with SAML to sign on to your SAP NetWeaver or HANA. You can also use single sign-on (SSO) for other SAP services like SAP Fiori Launchpad, SAP Cloud Platform, and SuccessFactors.

For more information, see:

- [SAP HANA SSO](/azure/active-directory/saas-apps/saphana-tutorial)
- [SAP NetWeaver SSO](/azure/active-directory/saas-apps/sap-netweaver-tutorial)
- [SAP Fiori SSO](/azure/active-directory/saas-apps/sap-fiori-tutorial)
- [SAP Cloud Platform SSO](/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial)
- [SuccessFactors SSO](/azure/active-directory/saas-apps/successfactors-tutorial)
- [Azure Active Directory overview](/azure/active-directory/)

## Use role-based access control (RBAC)

It’s important to control access to the SAP workload resources that you deploy. Every Azure subscription has a trust relationship with an Azure AD tenant. Use Azure role-based access control (Azure RBAC) to grant users within your organization access the SAP application. Grant access by assigning Azure roles to users or groups at a certain scope. The scope can be a subscription, a resource group, or a single resource. The scope depends on the user and how you’ve grouped your SAP workload resources.

For more information, see [Azure AD trust relationship](/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory) and [Azure RBAC](/azure/role-based-access-control/overview).

## Enforce network and application security

Network and application security controls are baseline security measures for every SAP workload. Their importance bears repeating to enforce the idea that the SAP network and application requires rigorous security review and baseline controls to protect.  

**(1) Network security** - Network security groups (NSGs) allow you to filter network traffic to and from your SAP workload. You can define NSG rules to allow or deny access to your SAP application. You can allow access to the SAP application ports from on-premises IP addresses ranges and denying public internet access.

**(2) Application security** - In general, the security best practices for application development also apply in the cloud. These include things like protecting against cross-site request forgery, thwarting cross-site scripting (XSS) attacks, and preventing SQL injection attacks. Application security groups (ASGs) should be used to make it easier to configure the network security of a workload. The ASG can be used in security rules instead of explicit IPs for VMs, and the VMs are then assigned to ASG. This configuration supports the reuse of the same policy over different application landscapes.

Cloud applications often use managed services that have access keys. Never check access keys into source control. Instead, store application secrets in Azure Key Vault. For more information, see [Azure application security groups](/azure/virtual-network/application-security-groups).

For more information, see [network security groups](/azure/virtual-network/network-security-groups-overview) and [application security groups](/azure/virtual-network/application-security-groups).

## Encrypt data

We recommend server-side encryption (SSE) using customer managed keys (CMK) along with host-based encryption with a CMK to encrypt operating system (OS) and data disks at rest.  You should use Key Vault to safeguard cryptographic keys and secrets like passwords, and store them securely in hardware security modules (HSMs). This approach promotes the integrity of the operating system and helps ensure that database backups are also encrypted. We also recommend you encrypt your SAP databases. Azure Key Vault supports database encryption for SQL Server from the DBMS and other storage and database services including [Azure Storage](/azure/storage/common/storage-service-encryption), [Azure SQL](/azure/azure-sql/database/always-encrypted-azure-key-vault-configure), [Azure Synapse Analytics](/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-encryption-tde), and [Azure Cosmos DB](/azure/cosmos-db/database-security?tabs=sql-api).

For general information, see:

- [Key Vault](/azure/key-vault/general/basic-concepts)
- [Microsoft Defender](/azure/defender-for-cloud/defender-for-cloud-introduction)
- [Trusted Cloud eBook](https://azure.microsoft.com/explore/trusted-cloud/)

## Next Step

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
