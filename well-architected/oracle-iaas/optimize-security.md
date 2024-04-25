---
title: Optimize security for your Oracle workload
description: Learn how to optimize security for your Oracle workload by using the defense-in-depth approach.  
author: jessiehaessler
ms.author: jhaessler 
ms.date: 04/23/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-oracle
---

# Optimize security for your Oracle workload

Security is crucial for any architecture. Azure offers a comprehensive range of tools to effectively secure your Oracle workload. This article describes security recommendations for the Azure control plane related to Oracle application workloads that are deployed on Azure Virtual Machines (VMs). For more information about security measures within Oracle Database products, see [Oracle Database security guide](https://docs.oracle.com/en/database/oracle/oracle-database/19/dbseg/introduction-to-oracle-database-security.html#GUID-41040F53-D7A6-48FA-A92A-0C23118BC8A0).

Most databases store sensitive data. Implementing security only at the database level isn't enough to secure architecture in which to land these workloads. Defense in depth is a comprehensive approach to security that implements several layers of defense mechanisms to protect data. Instead of relying on a single security measure at a specific level, such as focusing only on network security mechanisms, the defense-in-depth strategy employs a combination of different layer security measures to create a robust security posture.

You can architect a defense-in-depth approach for Oracle workloads by using a strong authentication and authorization framework, hardened network security, and encryption of data at rest and data in transit. Oracle workloads can be deployed as an infrastructure as a service (IaaS) cloud model on Azure. Revisit the shared responsibility matrix to better understand the specific tasks and responsibilities assigned to the cloud provider and the customer. For more information about the shared responsibility model, see [Shared responsibility in the cloud](/azure//security/fundamentals/shared-responsibility).

You should periodically assess the services and technologies you employ to ensure that your security measures align with the changing threat landscape.

## Use centralized identity management

Identity management is a fundamental framework that governs access to important resources. Identity management becomes critical when you work with different types of personnel, such as temporary interns, part-time employees, or full-time employees. These individuals require different levels of access that need to be monitored, maintained, and promptly revoked as necessary.

Organizations can improve the security of Windows and Linux VMs in Azure by integrating with Microsoft Entra ID (Azure AD), which is a fully managed identity and access management service.

### Recommendations

- Use Microsoft Entra ID integration with single sign-on (SSO) to access Oracle applications. Refer to the documentation for each Oracle application to follow the supported SSO strategy.
    - [Sign in to a Linux VM in Azure by using Microsoft Entra ID and OpenSSH](/azure//active-directory/devices/howto-vm-sign-in-azure-ad-linux).
    - [Sign in to a Windows VM in Azure by using Microsoft Entra ID](/azure//active-directory/devices/howto-vm-sign-in-azure-ad-windows).

## Deploy workloads on Windows or Linux operating systems

You can deploy Oracle databases on [Linux operating systems](/entra/identity/devices/howto-vm-sign-in-azure-ad-linux) and [Windows operating systems](/entra/identity/devices/howto-vm-sign-in-azure-ad-windows). Integrate your operating system with Azure Active Directory to enhance its security posture.

Organizations can improve the security of their Oracle workloads on Azure IaaS by ensuring that their operating system is hardened to eliminate vulnerabilities that could be exploited to attack the Oracle Database.

For more information on how to improve Oracle Database security, see [Security guidelines for Oracle workloads on Azure Virtual Machines landing zone accelerator](/azure/cloud-adoption-framework/scenarios/oracle-iaas/oracle-security-guideline-landing-zone).

### Recommendations

- Use SSH key-pairs for Linux account access instead of passwords.
- Disable password-protected Linux accounts and enable only on request for a short period.
- Disable sign-in access for privileged Linux accounts (that is, root, oracle, and others), which allows sign-in access only to personalized accounts.
- Use "sudo" to grant access to privileged Linux accounts (that is, root, oracle, and others) from personalized accounts instead of direct sign-in.
- Ensure that Linux audit trail logs and "sudo" access logs are captured into Azure Monitor Logs by using Linux SYSLOG utility.
- Apply security patches and operating system patches and updates regularly from trusted sources only.
- Implement restrictions to limit access to the operating system.
- Restrict unauthorized access to servers.
- Control server access at the network level to enhance overall security.
- Consider using the Linux firewall daemon as an extra layer of protection in addition to Azure network security groups (NSGs).
- Ensure that the Linux firewall daemon is configured to start automatically at boot time.
- Scan network ports being listened upon (that is, Linux command netstat –l) to determine potential access points. Make sure either Azure NSGs or the Linux firewall daemon control access to those ports.
- Alias potentially destructive Linux commands (such as rm and mv) to force them to interactive mode so that you're prompted at least once before an irreversible command is executed. Advanced users know how to unalias.
- Set the Oracle Database unified system logs to send copies of the Oracle audit logs to Azure Monitor Logs by using the Linux SYSLOG utility.

## Use a network topology

Network topology is the fundamental component of a layered security approach for Oracle workloads on Azure.

All cloud services should reside in one single virtual network and the traffic monitored and filtered by Azure NSGs. Secure the incoming traffic by using a firewall. The subnet where the database is deployed should be dedicated and securely separated from the internet and on-premises network. Assess users who internally and externally access the database to help you build a robust and secure network topology.

For more information about network topology, see [Network topology and connectivity for Oracle on Azure Virtual Machines landing zone accelerator](/azure/cloud-adoption-framework/scenarios/oracle-iaas/oracle-network-topology).

### Recommendations

- Use [Azure network security groups](/azure//virtual-network/network-security-groups-overview) to filter network traffic between Azure resources in an Azure virtual network and to filter traffic between on-premises networks to and from Azure.
- Secure your environment by using Azure Firewall or any of the market-available network virtual appliances (NVAs).
- Secure the VM on which the Oracle Database workload resides against unauthorized access by using Azure-provided features such as [Microsoft Defender for Cloud's just-in-time (JIT) access](/azure//defender-for-cloud/just-in-time-access-overview?tabs=defender-for-container-arch-aks) and [Azure Bastion](/azure//bastion/bastion-overview).
- Use SSH port forwarding to tunnel the connections through SSH for X-Windows and VNC utilities. For more information, see [Configure the firewall rules](https://docs.oracle.com/en/learn/install-vnc-oracle-linux/#open-a-vnc-client-and-test-your-deployment).
- Ensure that all VMs that directly support the Oracle Database reside in a dedicated subnet securely separated from the internet and on-premises network by directing all traffic through the hub virtual network.

## Secure data using encryption

**Encryption of data at rest**: Protecting data when it's written to storage and at rest is necessary. To prevent data from being exposed or altered when storage media are removed or accessed, data should be encrypted so that only authorized and authenticated users can view or modify it. Microsoft Azure offers various data storage solutions to meet different needs, including file, disk, and blob. These storage solutions have encryption features to secure data at rest.
**Encryption of data in transit**: Data in transit applies to the state of data moving from one location to another, usually across a network connection. Data in transit can be encrypted in several ways, which depends on the nature of the connection. Azure offers many mechanisms for keeping data in transit private as it moves from one location to another.

### Recommendations

- Understand how Microsoft [encrypts data at rest](/azure//security/fundamentals/encryption-overview#encryption-of-data-at-rest).
- Consider the features offered by the [Oracle Advanced Security option](https://docs.oracle.com/en/database/oracle/oracle-database/19/asoag/introduction-to-oracle-advanced-security.html#GUID-5D7343A0-4934-444F-97A1-5F189385A5DE), which include Transparent Data Encryption (TDE) and Data Redaction.
- Manage keys with Oracle Key Vault. If you choose to implement Oracle TDE as another encryption layer, note that Oracle doesn't support the native key management solutions provided by Azure or other cloud providers, such as Azure Key Vault. The primary option for the Oracle Wallet location is the default location within the filesystem of the Oracle Database VM. However, you can utilize Oracle Key Vault as a key management solution on Azure. For more information, see the documentation [Provisioning Oracle Key Vault in Azure](https://docs.oracle.com/en/database/oracle/key-vault/21.6/okvag/using_okv_as_oci_vm_compute_instance.html#GUID-E8154AEB-2964-4698-AE6E-64A108C06D11).
- Understand how Microsoft [encrypts data in transit](/azure//security/fundamentals/encryption-overview#encryption-of-data-in-transit).
- Consider using Oracle’s Native Network Encryption and Data Integrity feature. For more information, see [Configuring Oracle Database Native Network Encryption and Data Integrity](https://docs.oracle.com/en/database/oracle/oracle-database/19/dbseg/configuring-network-data-encryption-and-integrity.html#GUID-7F12066A-2BA1-476C-809B-BB95A3F727CF).

## Integrate Oracle Database audit trails

Application log monitoring is essential for detecting security threats at the application level. Azure Sentinel is a cloud-native security information and event management (SIEM) solution that can be used to monitor the security events of your Oracle workload.

For more information, see [Oracle Database Audit data connectors](/azure/sentinel/data-connectors/oracle-database-audit).

### Recommendations

- Use the Microsoft Sentinel Solution for Oracle Database workloads. The Oracle Database Audit data connector retrieves and ingests all Oracle Database audit records into Azure Monitor Logs by using an industry-standard SYSLOG interface.
- Use Azure Sentinel to review applications audit trails alongside the Azure infrastructure and the guest operating system audit records.  

## Next step

> For more information on how to monitor and manage your applications and services, see [Monitor workloads](monitor-workloads.md)
