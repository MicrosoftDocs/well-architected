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

For more information, see [Azure security documentation](/azure/security/)

## Leverage identity management

For more information, see [identity management architectures](/azure/architecture/reference-architectures/identity/).

For more information, see:

- [SAP HANA SSO](/azure/active-directory/saas-apps/saphana-tutorial)
- [SAP NetWeaver SSO](/azure/active-directory/saas-apps/sap-netweaver-tutorial)
- [SAP Fiori SSO](/azure/active-directory/saas-apps/sap-fiori-tutorial)
- [SAP Cloud Platform SSO](/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial)
- [SuccessFactors SSO](/azure/active-directory/saas-apps/successfactors-tutorial)
- [Azure Active Directory overview](/azure/active-directory/)



## Use role-based access control (RBAC)

For more information, see:

- [Azure AD trust relationship](/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory)
- [Azure RBAC](/azure/role-based-access-control/overview)
- [Network security groups](/azure/virtual-network/network-security-groups-overview)


## Enforce application security

For more information, see [application security groups](/azure/virtual-network/application-security-groups).

## Understand data sovereignty

For more information, see [Azure NetApp Files](/azure/azure-netapp-files/azure-netapp-files-introduction).

## Encrypt data

- [Azure Storage](/azure/storage/common/storage-service-encryption)
- [Azure SQL](/azure/azure-sql/database/always-encrypted-azure-key-vault-configure)
- [Azure Synapse Analytics](/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-encryption-tde)
- [Azure Cosmos DB](/azure/cosmos-db/database-security?tabs=sql-api)

For general information, see:

- [Key Vault](/azure/key-vault/general/basic-concepts)
- [Microsoft Defender](/azure/defender-for-cloud/defender-for-cloud-introduction)

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