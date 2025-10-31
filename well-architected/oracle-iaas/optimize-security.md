---
title: Optimize security for your Oracle workload
description: Learn how to optimize security for your Oracle workload by using the defense-in-depth approach to employ a combination of various layer security measures and create a robust security posture.  
author: jessiehaessler
ms.author: jhaessler 
ms.date: 04/23/2024
ms.topic: concept-article
ms.service: azure-waf
ms.subservice: waf-workload-oracle
---

# Optimize security for your Oracle workload

Security is crucial for any architecture. Azure offers a comprehensive range of tools to effectively secure your Oracle workload. This article describes security recommendations for the Azure control plane related to Oracle application workloads that are deployed on virtual machines (VMs) on Azure. For more information about security features in Oracle Database, see [Oracle Database security guide](https://docs.oracle.com/en/database/oracle/oracle-database/19/dbseg/introduction-to-oracle-database-security.html#GUID-41040F53-D7A6-48FA-A92A-0C23118BC8A0).

Most databases store sensitive data. Security measures only at the database level aren't enough to secure the entire architecture in which to land these workloads. Defense in depth is a comprehensive approach to security in which you implement several layers of defense mechanisms to protect data. You don't rely on a single security measure at a specific level, such as network security mechanisms. Use the defense-in-depth strategy to employ a combination of various layer security measures to create a robust security posture.

You can architect a defense-in-depth approach for Oracle workloads by using a strong authentication and authorization framework, hardened network security, and encryption of data at rest and data in transit. You can deploy Oracle workloads as an infrastructure as a service (IaaS) cloud model on Azure. Revisit the [shared responsibility matrix](/azure/security/fundamentals/shared-responsibility) to better understand the specific tasks and responsibilities assigned to the cloud provider and the customer.

You should periodically assess the services and technologies you employ to ensure that your security measures align with the changing threat landscape.

## Use centralized identity management

Identity management is a fundamental framework that governs access to important resources. Identity management becomes critical when you work with various personnel, such as temporary interns, part-time employees, or full-time employees. These individuals require different levels of access that you need to monitor, maintain, and promptly revoke as necessary.

Your organization can improve the security of Windows and Linux VMs in Azure by integrating with Microsoft Entra ID, which is a fully managed identity and access management service.

## Deploy workloads on Windows or Linux operating systems

You can use Microsoft Entra ID with single sign-on (SSO) to access Oracle applications and deploy Oracle databases on [Linux operating systems](/entra/identity/devices/howto-vm-sign-in-azure-ad-linux) and [Windows operating systems](/entra/identity/devices/howto-vm-sign-in-azure-ad-windows). Integrate your operating system with Microsoft Entra ID to enhance its security posture.

Improve the security of your Oracle workloads on Azure IaaS by ensuring that you harden your operating system to eliminate vulnerabilities that attackers might exploit to harm your Oracle database.

For more information about how to improve Oracle Database security, see [Security guidelines for Oracle workloads on Azure Virtual Machines landing zone accelerator](/azure/cloud-adoption-framework/scenarios/oracle-iaas/oracle-security-guideline-landing-zone).

### Recommendations

- Use Secure Shell (SSH) key pairs for Linux account access instead of passwords.

- Disable password-protected Linux accounts and enable them only on request for a short period.

- Disable sign-in access for privileged Linux accounts, such as root and oracle accounts, which allows sign-in access only to personalized accounts.

- Use the `sudo` command to grant access to privileged Linux accounts, such as root and oracle accounts, from personalized accounts instead of a direct sign in.

- Ensure that you capture Linux audit trail logs and `sudo` access logs into Azure Monitor Logs by using the Linux syslog utility.

- Apply security patches and operating system patches and updates regularly from trusted sources only.

- Implement restrictions to limit access to the operating system.

- Restrict unauthorized access to servers.

- Control server access at the network level to enhance overall security.

- Consider using the Linux firewall daemon as an extra layer of protection in addition to Azure network security groups (NSGs).

- Ensure that you configure the Linux firewall daemon to automatically run at startup.

- Scan network listening ports to determine potential access points. Use the Linux `netstat –l` command to list those ports. Make sure either Azure NSGs or the Linux firewall daemon controls access to those ports.

- Set up aliases for potentially destructive Linux commands, such as `rm` and `mv`, to force them to run in interactive mode so that you're prompted at least once before an irreversible command is run. Advanced users know how to remove the aliases if needed.

- Configure the Oracle database unified system logs to use the Linux syslog utility to send copies of the Oracle audit logs to Azure Monitor Logs.

## Design your network topology

Network topology is the fundamental component of a layered security approach for Oracle workloads on Azure.

Place all cloud services in a single virtual network, and use Azure NSGs to monitor and filter the traffic. Add a firewall to secure the incoming traffic. Ensure that you dedicate and securely separate the subnet where you deploy the database from the internet and the on-premises network. Assess users who internally and externally access the database to help ensure that your network topology is robust and secure.

For more information about network topology, see [Network topology and connectivity for Oracle on Azure Virtual Machines landing zone accelerator](/azure/cloud-adoption-framework/scenarios/oracle-iaas/oracle-network-topology).

### Recommendations

- Use [Azure NSGs](/azure/virtual-network/network-security-groups-overview) to filter network traffic between Azure resources in an Azure virtual network and to filter traffic between on-premises networks and Azure.

- Use Azure Firewall or a network virtual appliance (NVA) to secure your environment.

- Secure the VM on which the Oracle Database workload resides against unauthorized access by using Azure-provided features such as [Microsoft Defender for Cloud just-in-time (JIT) access](/azure/defender-for-cloud/just-in-time-access-overview) and [Azure Bastion](/azure/bastion/bastion-overview) features.

- Use SSH port forwarding for X Windows System and Virtual Network Computing (VNC) utilities to tunnel connections through SSH. For more information, see [an example that opens a VNC client and tests a deployment](https://docs.oracle.com/en/learn/ol-install-vnc/#open-a-vnc-client-and-test-your-deployment).

- Direct all traffic through a hub virtual network by placing VMs in a dedicated subnet that's isolated from the internet and the on-premises network.

## Use encryption to secure data

Encrypt data at rest when it's written to storage to protect the data. When you encrypt data, unauthorized users can't expose or alter it. Only authorized and authenticated users can view or modify the data. Microsoft Azure offers various data storage solutions, including file, disk, and blob storage, to meet different needs. These storage solutions have encryption features to secure data at rest.

Encrypt data in transit to protect data that moves from one location to another, usually across a network connection. You can use various methods to encrypt data in transit depending on the nature of the connection. Azure offers many mechanisms for keeping data in transit private as it moves from one location to another.

### Recommendations

- Understand how Microsoft [encrypts data at rest](/azure/security/fundamentals/encryption-overview#encryption-of-data-at-rest).

- Consider the features of [Oracle Advanced Security](https://docs.oracle.com/en/database/oracle/oracle-database/19/asoag/introduction-to-oracle-advanced-security.html#GUID-5D7343A0-4934-444F-97A1-5F189385A5DE), which include transparent data encryption (TDE) and data redaction.

- Manage keys with Oracle Key Vault. If you implement Oracle TDE as an extra encryption layer, note that Oracle doesn't support Azure key management solutions, such as Azure Key Vault, or other cloud providers' key management solutions. The default Oracle Wallet location is in the file system of the Oracle database VM. However, you can use Oracle Key Vault as a key management solution on Azure. For more information, see [Provisioning Oracle Key Vault in Azure](https://docs.oracle.com/en/database/oracle/key-vault/21.6/okvag/using_okv_as_oci_vm_compute_instance.html#GUID-E8154AEB-2964-4698-AE6E-64A108C06D11).

- Understand how Microsoft [encrypts data in transit](/azure//security/fundamentals/encryption-overview#encryption-of-data-in-transit).

- Consider using the Oracle Native Network Encryption and Data Integrity feature. For more information, see [Configuring Oracle Database Native Network Encryption and Data Integrity](https://docs.oracle.com/en/database/oracle/oracle-database/19/dbseg/configuring-network-data-encryption-and-integrity.html#GUID-7F12066A-2BA1-476C-809B-BB95A3F727CF).

## Integrate Oracle Database audit trails

Application log monitoring is essential for detecting security threats at the application level. Azure Sentinel is a cloud-native security information and event management (SIEM) solution that can be used to monitor the security events of your Oracle workload.

For more information, see [Oracle Database audit connector for Microsoft Sentinel](/azure/sentinel/data-connectors/oracle-database-audit).

### Recommendations

- Use the Microsoft Sentinel solution for Oracle Database workloads. The Oracle Database audit connector uses an industry-standard syslog interface to retrieve Oracle Database audit records and ingest them into Azure Monitor Logs.

- Use Azure Sentinel to review audit records of applications, the Azure infrastructure, and guest operating systems.

## Next step

> [!div class="nextstepaction"]
> [Monitor workloads](monitor-workloads.md)
