---
title: Azure SQL Database and security
description: Focuses on the Azure SQL Database service to provide best-practice, configuration recommendations, and design considerations related to Service Performance.
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.date: 06/13/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - azure-sql-database
categories:
  - data
  - management-and-governance
---

# Azure SQL Database and security

[Azure SQL Database](/azure/azure-sql/database/sql-database-paas-overview) is a fully managed platform as a service (PaaS) database engine that handles most of the database management functions without user involvement. Management functions include:

- Upgrades
- Patches
- Backups
- Monitoring

The following sections include a design checklist and recommended design options specific to Azure SQL Database security.

## Design checklist

**Have you designed your workload and configured Azure SQL Database with security in mind?**
***

> [!div class="checklist"]
> - Understand [logical servers](/azure/azure-sql/database/logical-servers) and how you can administer logins for multiple databases when appropriate.
> - Enable [Azure AD authentication with Azure SQL](/azure/azure-sql/database/authentication-aad-configure). Azure AD authentication enables simplified permission management and centralized identity management.
> - Azure SQL logical servers should have [an Azure Active Directory administrator provisioned](/azure/azure-sql/database/authentication-aad-configure#azure-ad-admin-with-a-server-in-sql-database). 
> - Verify contact information email address in your Azure Subscription for service administrator and co-administrators is reaching the correct parties inside your enterprise. You don't want to miss or ignore important security notifications from Azure!
> - Review the [Azure SQL Database connectivity architecture](/azure/azure-sql/database/connectivity-architecture). Choose the `Redirect` or `Proxy` [connection policy](/azure/azure-sql/database/connectivity-architecture#connection-policy) as appropriate.
> - Review [Azure SQL Database firewall rules](/azure/azure-sql/database/firewall-configure).
> - Use [virtual network rules](/azure/azure-sql/database/vnet-service-endpoint-rule-overview) to control communication from particular subnets in virtual networks.
> - If using the Azure Firewall, [configure Azure Firewall application rules with SQL FQDNs](/azure/firewall/sql-fqdn-filtering).

## Recommendations

| Recommendation | Benefit |
|--------|----|
| Review [the minimum TLS version](/azure/azure-sql/database/connectivity-settings#minimal-tls-version). | Determine whether you have legacy applications that require older TLS or unencrypted connections. After you enforce a version of TLS, it's not possible to revert to the default. Review and configure the minimum TLS version for SQL Database connections via the Azure portal. If not, set the latest TLS version to the minimum. |
| Ledger | Consider designing database tables based on the [Ledger](/sql/relational-databases/security/ledger/ledger-overview) to provide auditing, tamper-evidence, and trust of all data changes. |
| Always Encrypted | Consider designing application access based around [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) to protect sensitive data inside applications by delegating data access to encryption keys. |
| Private endpoints and private link | [Private endpoint connections](/azure/private-link/private-endpoint-overview) enforce secure communication by enabling private connectivity to Azure SQL Database. You can use a private endpoint to secure connections and deny public network access by default. [Azure Private Link for Azure SQL Database](/azure/azure-sql/database/private-endpoint-overview) is a type of private endpoint recommended for Azure SQL Database. |
| Automated vulnerability assessments | Monitor for [vulnerability assessment](/azure/azure-sql/database/sql-vulnerability-assessment) scan results and recommendations for how to remediate database vulnerabilities. |
| Advanced Threat Protection | Detect anomalous activities indicating unusual and potentially harmful attempts to access or exploit databases with [Advanced Threat Protection for Azure SQL Database](/azure/azure-sql/database/threat-detection-configure). Advanced Threat Protection integrates its alerts with [Microsoft Defender for Cloud](https://azure.microsoft.com/services/security-center/). |
| Auditing | Track database events with [Auditing for Azure SQL Database](/azure/azure-sql/database/auditing-overview).|
| Managed identities | Consider configuring [a user-assigned managed identity (UMI)](/azure/azure-sql/database/authentication-azure-ad-user-assigned-managed-identity). [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities) eliminate the need to manage credentials in code. |
| Azure AD-only authentication | Consider disabling SQL-based authentication and [allowing only on Azure AD authentication](/azure/azure-sql/database/authentication-aad-configure). |


## Policy definitions

Review the [Azure security baseline for Azure SQL Database](/security/benchmark/azure/baselines/sql-database-security-baseline) and [Azure Policy built-in definitions](/azure/azure-sql/database/policy-reference).

All built-in policy definitions related to Azure SQL are listed in [Built-in policies - <category>](/azure/governance/policy/samples/built-in-policies#sql).


## Next steps

- [Tutorial: Secure a database in Azure SQL Database](/azure/azure-sql/database/secure-database-tutorial).

> [!div class="nextstepaction"]
> [Azure SQL Database and cost optimization](./cost-optimization.md)
