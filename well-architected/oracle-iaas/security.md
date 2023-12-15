---
title: Oracle workload security
description: Oracle workload best practices for security
author: ckittel
ms.author: chkittel
ms.date: 12/29/2022
ms.topic: conceptual
---

# Oracle workload security

Security is one of the most important aspects of any architecture. Azure provides all the tools needed to secure your Oracle workload. Oracle Applications can contain sensible data. Peoplesoft as an HR system is one of the examples that the whole architecture must be secured properly. This can be achieved through secured authentication methods, hardened networking, and encryption.

Oracle on Azure is delivered in the infrastructure as a service (IaaS) cloud model. We recommend you regularly evaluate the services and technologies used to ensure your security posture evolves with the threat landscape. Below are security recommendations for consideration. We recommend you review the [security design principles](../../well-architected/security/security-principles.md).

## Use identity management

Identity management is a framework that controls access to critical resources. Especially within Oracle applications lifecycle management is crucial. Part-time workers joining only during summer season, interns joining companies, or full-time employees. Many of these are in need of different accesses, which need to be checked and maintained, but also removed as soon as they leave the company. There are two identity management use cases to consider for your Oracle workload, and the identity management solution differs for each.

**(1) Operating system** - Operating system - Organizations can improve the security of Windows and Linux virtual machines in Azure by integrating with Microsoft Entra ID. Microsoft Entra ID is a fully managed identity and access management service. For more information, see:

- [Sign in to a Windows virtual machine in Azure by using Microsoft Entra ID]( /azure/active-directory/devices/howto-vm-sign-in-azure-ad-windows)
- [Sign in to a Linux virtual machine in Azure by using Microsoft Entra ID and OpenSSH]( /azure/active-directory/devices/howto-vm-sign-in-azure-ad-linux)
- [Sign in to a Windows virtual machine in Azure by using Microsoft Entra ID]( /azure/active-directory/devices/howto-vm-sign-in-azure-ad-windows)

**(2) Oracle application** – Oracle Applications usually require a SSH (Port 22) and/or HTTP(S) (Port 443) access. We recommend configuring single sign-on (SSO) using Microsoft Entra ID. SSO allows end users to connect to the Oracle applications via browser. For more information, see [Microsoft Entra ID](/azure/active-directory/).

The table below provides a summary of the recommended SSO method for the given Oracle solution.

| Oracle solution | SSO methods |
| --- | --- |
|Siebel|Security Assertion Markup Language (SAML). <br>From version IP18.1 and onwards:<br>OAuth |
|Peoplesoft |From Version 8.53 and later: <br>- Kerberos <br>From Version 8.53 and later:<br>- SAML<br>- OAuth2.0 <br> For more information, see [Datawiza SSO for Microsoft Entra ID](/azure/active-directory/manage-apps/datawiza-azure-ad-sso-oracle-peoplesoft). |
|Hyperion |SAML 2.0|
|E-business suite (EBS) |SAML|
|JD Edwards (JDE) |SAML.<br>For more information, see [Datawiza JDE](/azure/active-directory/manage-apps/datawiza-azure-ad-sso-oracle-jde). |

Microsoft also offers customers coming from on-premises application to use an application proxy. The application proxy allows SAML authentication and can be used for all Oracle applications providing you the opportunity to establish single sign-on for external users. For more information, see:

- [Microsoft Entra application proxy](/azure/active-directory/app-proxy/what-is-application-proxy)
- [Tutorial](/azure/active-directory/saas-apps/ssogen-tutorial) on setting up Microsoft Entra SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE

## Use role-based access control (RBAC)

Role-based access control is a method to grant certain accesses to certain individuals. Azure RBAC is an authorization system build on the [Azure Resource Manager](/azure/azure-resource-manager/management/overview). It provides a management layer to you that enables you to create, update, and delete resources according to the need of certain roles and individuals. For more information, see [Azure role-based access control](/azure/role-based-access-control/overview).

## Enforce network and application security

Network and application security is crucial. Especially in regard to potential latencies, but also internet facing applications. Network security should be the baseline of every architecture.

**(1) Azure Network Design** - The first security option every customer has is a private connection to the cloud installation. In this case it's an MPLS (any-to-any networks) connection that is [ExpressRoute](/azure/expressroute/expressroute-introduction). We recommend keeping the workload specific resources in one virtual network.  

For Oracle-native setups, you should use Oracle Cloud Connector and Oracle Private Link for Azure as part of the hub-spoke setup. These technologies support the Oracle extension and innovation architecture for the Oracle Business Technology Platform (BTP). Azure native integrations fully integrated with Azure virtual networks and APIs and don’t require these components.

**(2) Virtual network security** - You can use Network Security Groups (NSG) to filter network traffic between Azure resources in your Azure virtual network. The NSG rules can be defined to allow or deny access to your Oracle application. It can also be dropped down to ports from on-premises IP address ranges. For more information, see [network security groups](/azure/virtual-network/network-security-groups-overview).

**(3) Application security** - This is one of the most complex parts when it comes to Oracle applications. Determine where are users accessing the application and if they're only coming from the corporate network or also from the Internet. Take a look on the operating systems currently used whether Linux or Windows. Linux systems usually require access from SSH whereas Windows require an RDP protocol. Most customers desire to properly secure these protocols by installing a Bastion host in front. It's highly based on the customer requirements and also on where the application will be accessed.

Next to this it's also recommended to secure these accesses through a Web application Firewall. In case everyone logs in from the corporate network, this is only based on your security requirements and optional.  

In case a user accesses the application from the internet, consider an Application Gateway. Azure Application Gateway provides two built-in functionalities. At first it operates as a Web Application Firewall, but also has a built-in Layer7 Load Balancer. It is only supported for access at Port 443 (http/s). For more information, see [Azure Application Gateway](/azure/application-gateway/overview)

Another option to secure your network is the [Azure Firewall](/azure/firewall/overview). This component defends the web services against common exploits and vulnerabilities. It keeps the Oracle Application highly available and helps you meet compliance requirements.

Another possibility to configure a mature security posture are Application Security Groups (ASG). An ASG is an easier possibility to establish network security of the dedicated workload. For more information, see [Azure application security groups.](/azure/virtual-network/application-security-groups)

## Make use of Azure Policy

There are no specific built-in Azure Policy definitions for Oracle on IaaS in Azure. Azure Policy has broad coverage over the core resources that make up any Oracle solution on Azure. Primary resources are [virtual machines](/azure/virtual-machines/overview), [storage](/azure/storage/common/storage-account-overview), and [networking](/azure/networking/fundamentals/networking-overview). Always enforce architectural choices with Azure Policy to prevent accidental drift from desired state. There are policies for these resources that span all five pillars of the Well-Architected Framework. If built-in policies for your architecture’s components or configurations do not exist, you can create custom policies to cover any gaps.

Examples of key policies that are built-in would be Audit virtual machines without disaster recovery, virtual machines should use Secure Boot, and any policies that limit SKU choices to ensure you’re meeting reliability, cost optimization, and performance efficiency targets. See the related Azure Well-Architected service guide for each service for a list of recommended Azure Policies for that service.

## Recommendations
Explore the following table of recommendations to optimize your Oracle on Azure IaaS environment for security:

| Recommendation | Benefit |
| --- | --- |
| Send STIG security alerts from Oracle Cloud Control to Activity Logs  | [Oracle Cloud Control STIG](https://docs.oracle.com/en/enterprise-manager/cloud-control/enterprise-manager-cloud-control/13.4/emdbc/security-technical-implementation-guidelines-stig-rules-enhanced-oracle.html) alerts and logs can be imported into [Log Analytics](/azure/azure-monitor/agents/data-sources-custom-logs) to grant a single pane of glass for Cloud Management, while retaining Oracle tools for database specialists. |
| Use Listener Services | Follow recommended practices for the [Oracle Net Listener](https://docs.oracle.com/en/database/oracle/oracle-database/19/netag/configuring-and-administering-oracle-net-listener.html) to enforce local operating system authentication and manage incoming client connection requests to the database server. |
| Consider using the DBSAT to evaluate security state of database | Oracle provides a stand-alone command line tool called the [Oracle Database Security Assessment Tool (DBSAT)](https://docs.oracle.com/en/database/oracle/security-assessment-tool/index.html) to assess and check regulatory compliance, including relevant configuration and configuration information. |
| Virtual Machine Security in Azure | Follow the recommended practices for [Virtual Machine Security in Azure](/azure/security/fundamentals/virtual-machines-overview), including the use of compute, data security, ports and network. |

## Next Steps

>[!div class="nextstepaction"]
>[Cost Optimization](./cost-optimization.md)
