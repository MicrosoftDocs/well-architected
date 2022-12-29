---
title: Oracle workload security
description: Oracle workload best practices for security
author: ckittel
ms.author: kegorman
ms.date: 12/29/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---

# Oracle workload security

Security is one of the most important aspects of any architecture. Azure provides all the tools needed to secure your Oracle workload. Oracle Applications can contain sensible data. Peoplesoft as an HR system is one of the examples that the whole architecture must be secured properly. This can be achieved through secured authentication methods, hardened networking, and encryption.

Oracle on Azure is delivered in the infrastructure as a service (IaaS) cloud model. We recommend you regularly evaluate the services and technologies used to ensure your security posture evolves with the threat landscape. Below are security recommendations for consideration. We recommend you review the [security design principles](../../well-architected/security/security-principles.md).

## Use identity management

Identity management is a framework that controls access to critical resources. Especially within Oracle applications lifecycle management is crucial. Part-time workers joining only during summer season, interns joining companies, or full-time employees. Many of these are in need of different accesses, which need to be checked and maintained, but also removed as soon as they leave the company. There are two identity management use cases to consider for your Oracle workload, and the identity management solution differs for each.

**(1) Operating system** - Operating system - Organizations can improve the security of Windows and Linux virtual machines in Azure by integrating with Azure Active Directory (Azure AD). Azure AD is a fully managed identity and access management service. For more information, see:

- [Sign in to a Windows virtual machine in Azure by using Azure AD]( /azure/active-directory/devices/howto-vm-sign-in-azure-ad-windows)
- [Sign in to a Linux virtual machine in Azure by using Azure AD and OpenSSH]( /azure/active-directory/devices/howto-vm-sign-in-azure-ad-linux)
- [Sign in to a Windows virtual machine in Azure by using Azure AD]( /azure/active-directory/devices/howto-vm-sign-in-azure-ad-windows)

**(2) Oracle application** – Oracle Applications usually require a SSH (Port 22) and/or http(s) (Port 443) access. We recommend configuring single sign-on (SSO) using Azure Active Directory). SSO allows end users to connect to the Oracle applications via browser. For more information, see Azure Active Directory [documentation.](/azure/active-directory/)

The table below provides a summary of the recommended SSO method for the given Oracle solution.

| Oracle solution | SSO method |
| --- | --- |
|Siebel|Security Assertion Markup Language (SAML). From version **IP18.1** and onwards: **OAuth** |
|Peoplesoft |From Version **8.53** and later: **Kerberos**. From Version **8.53** and later: **SAML**, **OAuth2.0**. For more information, see [Datawiza.](/azure/active-directory/manage-apps/datawiza-azure-ad-sso-oracle-peoplesoft)  |
|Hyperion |**SAML 2.0**|
|E-business suite (EBS) |**SAML**|
|JD Edwards (JDE) |**SAML**. For more information, see [Datawiza JDE.](/azure/active-directory/manage-apps/datawiza-azure-ad-sso-oracle-jde)|

Microsoft also offers customers coming from on-premises application to use an application proxy. The application proxy allows SAML authentication and can be used for all Oracle applications providing you the opportunity to establish a Single-Sign-On for external users. For more information, see:
- [Azure AD Application Proxy](/azure/active-directory/app-proxy/what-is-application-proxy) documentation.
- [Tutorial](/azure/active-directory/saas-apps/ssogen-tutorial) on setting up Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE.

## Use role-based access control (RBAC)

Role Based Access Control is a method to grant certain accesses to certain individuals. Azure RBAC is an authorization system build on the [Azure Resource Manager](/azure/azure-resource-manager/management/overview). It provides a management layer to you that enables you to create, update, and delete resources according to the need of certain roles and individuals. For more information, see [Azure RBAC documentation.](/azure/role-based-access-control/overview)

## Enforce network and application security

Network and application security is crucial. Especially in regard to potential latencies, but also internet facing applications. Network security should be the baseline of every architecture.

**(1) Azure Network Design** - The first security option every customer has is a private connection to the cloud installation. In this case it's an MPLS (any-to-any networks) connection that is [ExpressRoute](/azure/expressroute/expressroute-introduction). We recommend keeping the workload specific resources in one virtual network.  

For Oracle-native setups, you should use Oracle Cloud Connector and Oracle Private Link for Azure as part of the hub-spoke setup. These technologies support the Oracle extension and innovation architecture for the Oracle Business Technology Platform (BTP). Azure native integrations fully integrated with Azure virtual networks and APIs and don’t require these components.

**(2) Virtual network security** - You can use Network Security Groups (NSG) to filter network traffic between Azure resources in your Azure virtual network. The NSG rules can be defined to allow or deny access to your Oracle application. It can also be dropped down to ports from on-premises IP address ranges. For more information, see [network security groups](/azure/virtual-network/network-security-groups-overview).

**(3) Application security** - This is one of the most complex parts when it comes to Oracle applications. Determine where are users accessing the application and if they're only coming from the corporate network or also from the Internet. Take a look on the operating systems currently used whether Linux or Windows. Linux systems usually require access from SSH whereas Windows require an RDP protocol. Most customers desire to properly secure these protocols by installing a Bastion host in front. It's highly based on the customer requirements and also on where the application will be accessed.

Next to this it's also recommended to secure these accesses through a Web application Firewall. In case everyone logs in from the corporate network, this is only based on your security requirements and optional.  

In case a user accesses the application from the internet, consider an Application Gateway. Azure Application Gateway provides two built-in functionalities. At first it operates as a Web Application Firewall, but also has a built-in Layer7 Load Balancer. It is only supported for access at Port 443 (http/s). For more information, see [Azure Application Gateway](/azure/application-gateway/overview)

Another possibility to configure a mature security posture are Application Security Groups (ASG). An ASG is an easier possibility to establish network security of the dedicated workload. For more information, see [Azure application security groups.](/azure/virtual-network/application-security-groups)

## Recommendations
Explore the following table of recommendations to optimize your Oracle on Azure IaaS environment for security:

| Recommendation | Benefit |
| --- | --- |
| Send STIG security alerts from Oracle Cloud Control to Activity Logs  | [Oracle Cloud Control STIG](https://docs.oracle.com/en/enterprise-manager/cloud-control/enterprise-manager-cloud-control/13.4/emdbc/security-technical-implementation-guidelines-stig-rules-enhanced-oracle.html) alerts and logs can be imported into [Log Analytics](/azure/azure-monitor/agents/data-sources-custom-logs) to grant a single pane of glass for Cloud Management, while retaining Oracle tools for database specialists. |
| Use Listener Services | Follow recommended practices for the [Oracle Net Listener](https://docs.oracle.com/en/database/oracle/oracle-database/19/netag/configuring-and-administering-oracle-net-listener.html) to enforce local operating system authentication and manage incoming client connection requests to the database server. |
| Consider using the DBSAT to evaluate security state of database | Oracle provides a stand-alone command line tool called the [Oracle Database Security Assessment Tool (DBSAT)](https://docs.oracle.com/en/database/oracle/security-assessment-tool/index.html) to assess and check regulatory compliance, including relevant configuration and configuration information. |
| Virtual Machine Security in Azure | Follow the recommended practices for [Virtual Machine Security in Azure](/azure/security/fundamentals/virtual-machines-overview), including the use of compute, data security, ports and network. |

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