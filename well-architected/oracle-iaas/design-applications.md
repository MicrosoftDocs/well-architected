---
title: Design Oracle applications
description: Review the design area for Oracle applications of the Azure Well-Architected Framework. See how to apply these principles to Oracle on Azure IaaS workloads.
author: jessiehaessler
ms.author: jhaessler
ms.date: 07/30/2024
ms.topic: concept-article
ms.service: azure-waf
ms.subservice: waf-workload-oracle
---

# Design Oracle applications

Migrating Oracle applications to the cloud is a complex process. You need to understand which functionalities each version of an application supports so that you can avoid problems during the migration or even avoid a failed migration. Organizations don't want to just lift and shift applications. They also want to modernize the architecture and align with functional and nonfunctional requirements. You should examine these requirements alongside key cloud application design patterns to ensure that you achieve your migration goals.

Examples of popular Oracle applications are Siebel, E-Business Suite, JD Edwards, and PeopleSoft. These applications have strong dependencies between the application tier and the database tier. Separating the two tiers across different cloud vendors introduces latencies that can lead to a poor experience for customers. You should always do a proper technical assessment before you decide how to host the two tiers.

For each application, you should note the design considerations that the application vendor provides and consider the characteristics of the Azure services that you use for each design. The Azure cloud offers many features and capabilities that can lead to a performant, reliable, secure, and highly available solution.

For more specific architecture guidance, see [Architectures for Oracle applications with a database on Azure Virtual Machines](/azure/virtual-machines/workloads/oracle/deploy-application-oracle-database-azure).

## Recommendations

Use the following recommendations to plan the migration of your Oracle applications to the cloud.

### Network and security 

- Consider configuring single sign-on (SSO) by using Microsoft Entra ID. Customers can use SSO to connect to Oracle applications via their internet browser. For more information, see [Enable SSO for an enterprise application](/azure/active-directory/manage-apps/add-application-portal-setup-sso).
- Consider using a private connection to the cloud installation. Azure provides private connectivity capabilities like [Azure ExpressRoute connections and site-to-site VPN connections](/azure/expressroute/expressroute-connect-azure-to-public-cloud).
- If a customer accesses the application from the internet, consider an [application gateway](/azure/application-gateway/overview). Azure Application Gateway provides two built-in functionalities. It operates as a web application firewall and has a built-in Layer 7 load balancer. Application Gateway only supports access on port 443 (HTTPS).
- Another option to secure your network is [Azure Firewall](/azure/firewall/overview). This component defends web services against common exploits and vulnerabilities. It keeps Oracle applications highly available and helps you meet compliance requirements.
- Consider setting up [network security groups](/azure/virtual-network/network-security-groups-overview) at a subnet level to ensure that the network permits traffic on specific ports and IP addresses only.
- If your application requires Secure Shell (SSH) protocol or Remote Desktop Protocol (RDP) access, deploy an [Azure Bastion](/azure/bastion/bastion-overview) host as a jump server to provide extra security for an in-depth, mature security posture.

### Web and application tiers

- Deploy your application on virtual machines (VMs). Group those VMs in a [flexible virtual machine scale set](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes#scale-sets-with-flexible-orchestration) to improve overall availability.
- If you need your application to autoscale, then consider using [Azure Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets/overview).
- Place VMs in a single availability zone to bring them physically closer together. However, keep in mind that as the Azure footprint grows, a single availability zone might span multiple physical datacenters. The distance between physical datacenters might cause network latency that affects your application. To get VMs as close as possible and achieve the lowest possible latency, deploy them within a [proximity placement group](/azure/virtual-machine-scale-sets/proximity-placement-groups).

### Database tier
   
- Consider deploying the database tier as a primary server that's replicated to a secondary server by using Oracle Data Guard. 
- If you use two zones to deploy the primary and secondary servers in one region, consider using the Data Guard synchronous replication configuration after you verify the network latency between the zones in the region.
- If you deploy the primary and secondary servers in two regions, then consider using the Data Guard asynchronous replication configuration.
- If you require a zero data-loss replication strategy, then consider using the asynchronous replication configuration.
- In addition to Data Guard, other integration options include Striim, Qlik, GoldenGate, or Active Data Guard.

### Backup and data protection
   
- Consider using [Azure Backup](/azure/backup/backup-azure-vms-introduction) to back up your application and database VMs.
- Consider placing your backups in a different region from your primary region to provide extra protection against regional failures.
- Consider backing up the database by using storage components that have built-in replication capabilities.

### Disaster recovery
   
- Build a reliable architecture like these examples:
   - [Create a failover with passive standby](./optimize-business-continuity-disaster-recovery.md#create-a-failover-with-passive-standby) 
   - [Create a failover with active standby](./optimize-business-continuity-disaster-recovery.md#create-a-failover-with-active-standby)
- Consider using Azure built-in disaster recovery solutions like [Azure Site Recovery](/azure/site-recovery/site-recovery-overview).

## Next step

> [!div class="nextstepaction"]
> [Design principles for an Oracle workload on Azure](review-design-principles.md)
