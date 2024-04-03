---
title: Oracle on Azure IaaS design area compute and storage - Application design
description: Review the design area for Oracle Applications of the Azure Well-Architected Framework. See how to apply these principles to Oracle on Azure IaaS workloads.
author: jessiehaessler
ms.author: jhaessler
ms.date: 03/14/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-oracle
---

# Application design

Migrating Oracle applications to the cloud can be complex. In particular, understanding of supported and unsupported functionalities from version to version is important to avoid difficulties in, or even failure of the migration.  Moreover companies don't just want to lift and shift, but modernize the architecture and bind to functional and non-functional requirements. These requirements should be examined alongside key cloud application design patterns to ensure aspirations are fully achieved.

Examples of popular Oracle applications are Siebel, E-Business Suite, JD Edwards, and Peoplesoft. All such applications have strong dependencies between the application tier and the database tier. Separating the two tiers across different cloud vendors introduces latencies that can lead to poor user experience. A proper technical assessment should always be carried out before making a decision on hosting the two tiers. 

For each application, it is also important to note the application design considerations provided by the application vendor and the characteristics of the Azure services used for each design. The Azure cloud offers many features and capabilities that, if utilized properly, can lead to a performant, reliable, secure and highly available solution.

For more specific architecture guidance, please review [Oracle Applications on Azure](/azure/virtual-machines/workloads/oracle/deploy-application-oracle-database-azure).

## Assessment questions
For each of the following application design areas, what are the considerations that you take into account when deploying Oracle applications in the Azure cloud? 

- Network and Security
- Web and Application tiers
- Database tier
- Backup and Data Protection
- Disaster recovery

## Recommendations

- Network & Security 
   - Consider configuring single sign-on (SSO) using Microsoft Entra ID (formerly Azure Active Directory AAD). SSO allows end users to connect to the Oracle applications via browser. For more information, see [Microsoft Entra ID documentation](/azure/active-directory/manage-apps/add-application-portal-setup-sso).
   - Consider using a private connection to the cloud installation. Azure provides capabilities for private connectivity such as [Express Route and Site-to-Site VPN](/azure/expressroute/expressroute-connect-azure-to-public-cloud).
   - In case a user accesses the application from the internet, consider an [Application Gateway](/azure/application-gateway/overview). Azure Application Gateway provides two built-in functionalities. It operates as a Web Application Firewall, and also has a built-in Layer 7 Load Balancer. It is only supported for access on Port 443 (https).
   - Another option to secure your network is the [Azure Firewall](/azure/firewall/overview). This component defends the web services against common exploits and vulnerabilities. It keeps the Oracle Application highly available and helps you meet compliance requirements.
   - Consider setting up [network security groups](/azure/virtual-network/network-security-groups-overview) at a subnet level to ensure only traffic on specific ports and IP addresses is permitted.
   - If SSH or RDP access is required, a [Bastion](/azure/bastion/bastion-overview) host as a jump box can provide extra security for an in-depth mature security posture.

- Web and Application Tiers
   - Deploy your application on VMs in Azure [Availability Set](/azure/virtual-machines/availability-set-overview) to improve overall availability.
   - If you require auto-scaling of your application then consider using [Azure VM Scale Sets](/azure/virtual-machine-scale-sets/overview).
   - Placing VMs within a single availability zone will bring them physically closer together. However, as the Azure footprint grows, a single availability zone may span multiple physical data centers, which may result in a network latency impacting your application. To get VMs as close as possible, achieving the lowest possible latency, you can deploy them within a [proximity placement group](/azure/virtual-machine-scale-sets/proximity-placement-groups).

- Database Tier 
   - Consider deploying the database tier in as a primary server replicated to a secondary server using Oracle Data Guard. 
   - If you deploy the primary and secondary servers in one region using two zones, consider making use of Data Guard synchronous replication configuration after verifying the network latency between the zones in the region.
   - If you deploy the primary and secondary servers in two regions then consider using Data Guard asynchronous replication configuration.
   - If you require a zero data-loss replication strategy then consider using the asynchronous replication configuration.
   - Data Guard is only one of the options you can leverage. Other examples could be: Striim, Attunity, Goldengate or Active Data Guard.

- Backup and Data Protection
   - Consider using the [Azure Backup service](/azure/backup/backup-azure-vms-introduction) to backup your application and database VMs.
   - Consider placing your backups in a different region to provide extra protection against regional failures.
   - Consider backing up the data base using storage components that have built-in replication capabilities.

- Disaster Recovery 
   - Build a reliable architecture as suggested in the examples for a [failover with passive standby](/azure/well-architected/oracle-iaas/optimize-business-continuity-disaster-recovery#example-1-create-a-fail-over-for-business-critical-oracle-applications-in-a-two-availability-zone-deployment-with-passive-standby) and [a failover with active standby](/azure/well-architected/oracle-iaas/optimize-business-continuity-disaster-recovery#example-2-create-a-fail-over-for-business-critical-applications-in-a-two-availability-zone-deployment-with-active-standby).
   - Consider using Azure built-in DR solutions such as [Azure Site Recovery](/azure/site-recovery/site-recovery-overview).


## Next Steps

> [!div class="nextstepaction"]
> [Design Principles](./design-principles.md) 
