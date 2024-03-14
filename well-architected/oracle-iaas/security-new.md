---
title: Oracle workload best practices for security
description: Oracle workload best practices for security
author: jessiehaessler
ms.author: jhaessler 
ms.date: 03/14/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-oracle
---

# Oracle workload best practices for security

Ensuring security is crucial when it comes to any architecture, and Azure offers a comprehensive range of tools to effectively secure your Oracle workload. The objective of this article is to provide security recommendations for the Azure control plane related to Oracle application workloads that are deployed on Azure Virtual Machines. For detailed information and implementation guidelines regarding security measures within the Oracle Database product, see [Oracle database security guide](https://docs.oracle.com/en/database/oracle/oracle-database/19/dbseg/introduction-to-oracle-database-security.html#GUID-41040F53-D7A6-48FA-A92A-0C23118BC8A0).

Most databases store sensitive data. To have a secure architecture in which to land these workloads, implementing security only at the database level isn't sufficient. Defense in depth is a comprehensive approach to security that involves implementing multiple layers of defense mechanisms to protect data. Instead of relying on a single security measure at a specific level, such as, for example, focusing on network security mechanisms, the defense in depth strategy employs a combination of different layer security measures to create a robust security posture. Defense-in-depth approach can be architected for Oracle workloads through strong authentication and authorization framework, hardened network security and encryption of data at rest and in-transit. Oracle workloads currently can be deployed as infrastructure as a service (IaaS) cloud model on Azure. The shared responsibility matrix should be revisited to have a clear understanding of the specific tasks and responsibilities assigned to both the cloud provider and the customer. For more information about the shared responsibility model, see Shared responsibility in the [cloud](/azure//security/fundamentals/shared-responsibility).

We suggest periodically assessing the services and technologies you employ to ensure that your security measures align with the changing threat landscape.

## Use Centralized identity management

Identity management is a fundamental framework that governs access to important resources. When you work with different sorts of personnel, such as part-time employees, interns who join temporarily, or full-time employees, identity management becomes critical. These individuals require different levels of access that need to be monitored, maintained, and promptly revoked as necessary. 

Organizations can improve the security of Windows and Linux virtual machines in Azure by integrating with Microsoft Entra ID (Azure AD). Microsoft Entra ID is a fully managed identity and access management service.

### Assessment question
What is your approach to using centralized identity management to access your Oracle database and applications?

### Recommendations
- Use Microsoft Entra ID integration with SSO to access Oracle applications. Refer to the documentation for each Oracle application to follow the supported Single Sign On (SSO) strategy.
- [Sign in to a Linux virtual machine in Azure by using Microsoft Entra ID and OpenSSH](/azure//active-directory/devices/howto-vm-sign-in-azure-ad-linux).
- [Sign in to a Windows virtual machine in Azure by using Microsoft Entra ID](/azure//active-directory/devices/howto-vm-sign-in-azure-ad-windows).

## Operating system
Organizations can improve the security of their Oracle on Azure IaaS workloads by ensuring that the operating system is hardened to eliminate vulnerabilities that could be exploited to attack the Oracle database.

### Assessment question
What are the steps that you take to harden the operating system that runs your Oracle database and applications?

### Recommendations
- Use SSH key-pairs for Linux account access instead of passwords.
- Disable password-protected Linux accounts, enable only on request for a short period.
- Disable login access for privileged Linux accounts (that is, root, oracle, etc.), allowing login access to only personalized accounts.
- Instead of direct login, use “sudo” for granting access to privileged Linux accounts (that is, root, oracle, etc.) from personalized accounts.
- Ensure that Linux audit trail logs and “sudo” access logs are captured into Azure Log Analytics using Linux SYSLOG utility.
- Apply security patches and operating system patches/updates regularly from trusted sources only.
- Implement restrictions to limit access to the operating system.
- Restrict unauthorized access to servers.
- Control server access at the network level to enhance overall security.
- Consider using the Linux firewall daemon as local protection above and beyond Azure network security groups (NSGs).
- Ensure that the Linux firewall daemon is configured to start automatically at boot time.
- Periodically scan network ports being listened upon (that is, Linux command netstat –l) to determine potential access points, and be sure access to those ports are controlled by either Azure network security groups (NSGs) or the Linux firewall daemon.
- Alias potentially destructive Linux commands (such as rm and mv) to force them to interactive mode, so you're prompted at least once before an irreversible command is executed. Advanced users know how to unalias if they wish.
- Set the Oracle database unified system logs to send copies of the Oracle audit logs to Azure Log Analytics using the Linux SYSLOG utility.

## Network Security
- Using network security is the fundamental component of a layered security approach for Oracle workloads on Azure.

### Assessment question
What is your approach to securing the network that is utilized by your Oracle workload on Azure IaaS?

### Recommendations
- Use [Azure network security groups](/azure//virtual-network/network-security-groups-overview) to filter network traffic between Azure resources in an Azure virtual network, and to filter traffic between on-premises networks to/from Azure. 
- Secure the virtual machine on which the Oracle database workload resides against unauthorized access by using Azure provided features such as [Microsoft Defender for Cloud's just-in-time (JIT) access](/azure//defender-for-cloud/just-in-time-access-overview?tabs=defender-for-container-arch-aks) and [Azure Bastion](/azure//bastion/bastion-overview).
- For X-Windows and VNC utilities it is recommended to use SSH port forwarding to tunnel the connections through SSH, [see for example](https://docs.oracle.com/en/learn/install-vnc-oracle-linux/#open-a-vnc-client-and-test-your-deployment).
- For connectivity from Oracle database workloads running in Azure and workloads in Oracle cloud (OCI), private links or pipelines between applications can be created using the Azure/OCI interconnect between specific regions in Azure and OCI.

## Secure data using encryption
**Encryption at rest** - It is necessary to protect data when it's written to storage, while it is at rest. Storage media can be removed and the data within examined, and storage media can be accessed while in use and confidential information revealed, so it's important that data is encrypted so that only authorized and authenticated users can view or modify it. Microsoft Azure offers a variety of data storage solutions to meet different needs, including file, disk and blob, with encryption features to secure data at rest.
**Encryption in transit** - Applies to the state of data moving from one location to another, usually across a network connection. Data in transit can be encrypted in several ways, depending on the nature of the connection. Azure offers many mechanisms for keeping data private as it moves from one location to another.

### Assessment question
What data encryption measures do you use for your Oracle workload on Azure?

### Recommendations
- Understand how Microsoft [encrypts data at rest](/azure//security/fundamentals/encryption-overview#encryption-of-data-at-rest).
- Consider the features offered by the [Oracle Advanced Security option](https://docs.oracle.com/en/database/oracle/oracle-database/19/asoag/introduction-to-oracle-advanced-security.html#GUID-5D7343A0-4934-444F-97A1-5F189385A5DE) which include Transparent Data Encryption and Data Redaction.
- Key management - If you choose to implement Oracle Transparent Data Encryption (TDE) as another encryption layer, it's important to note that Oracle doesn't support the native key management solutions (for example, Azure Key Vault, etc.) provided by Azure or other cloud providers. The primary option for the Oracle Wallet location is the default location within the filesystem of the Oracle database Virtual Machine. However, you can utilize Oracle Key Vault as a key management solution on Azure. For detailed information, refer to the documentation on [Provisioning Oracle Key Vault in Azure](https://docs.oracle.com/en/database/oracle/key-vault/21.6/okvag/using_okv_as_oci_vm_compute_instance.html#GUID-E8154AEB-2964-4698-AE6E-64A108C06D11).
- Understand how Microsoft [encrypts data in transit](/azure//security/fundamentals/encryption-overview#encryption-of-data-in-transit).
- Consider using Oracle’s Native Network Encryption and Data Integrity feature, and for more information, see [Configuring Oracle Database Native Network Encryption and Data Integrity](https://docs.oracle.com/en/database/oracle/oracle-database/19/dbseg/configuring-network-data-encryption-and-integrity.html#GUID-7F12066A-2BA1-476C-809B-BB95A3F727CF).

## Integrating Audit Trails

Application log monitoring is essential for detecting security threats at the application level. Azure Sentinel is a cloud-native security information and event management (SIEM) solution which can be used to monitor the security events of your Oracle workload.

### Assessment question
How do you monitor and analyze the security related events of your Oracle workload?

### Recommendations
- Use the Microsoft Sentinel Solution for Oracle Database workloads. The Oracle Database Audit connector retrieves and ingests all Oracle database audit records into Azure Log Analytics using an industry-standard SYSLOG interface.
- Use Azure Sentinel to review applications audit trails alongside the Azure infrastructure and the guest OS audit records.  

## Next steps
> [!div class="nextstepaction"]
> [Monitoring](./monitoring.md)
