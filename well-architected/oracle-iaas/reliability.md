---
title: Oracle workload reliability
description: Oracle workload best practices for reliability
author: ckittel
ms.author: kegorman
ms.date: 12/29/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---

# Oracle workload reliability

In the cloud, we acknowledge that failures happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component. Use the following information to minimize down time and ensure that recommended practices for high availability are built into Azure and Oracle.

When discussing reliability with Oracle in Azure, it’s important to take into consideration not just the database, the connected tiers on separate VMs, virtual network subnets and disaster recovery if there's failures. Oracle on Azure IaaS can achieve these design considerations and have recommendations for each item in the checklist.

## Conduct a reliability assessment

Availability is crucial to Oracle Workloads. To discover the reliability status of your workload, you should answer some questions that help to identify areas of weaknesses. This is crucial for the architecture design and configurations.

The assessment provides specific recommendations to focus on. You can start the [WAF assessment](/assessments/azure-architecture-review) when prompted.

## Create architecture reliability

Knowing the Architecture of your Oracle on-premises application is crucial. In some cases specific versions of applications have a slight difference in the application tiers, which are crucial for your migration phase. Next to it you should establish a map pointing out any dependency. A dependency will most definitely occur between the application and database tier. The same applies to back up and your disaster recovery strategy.

Make sure to match up to the on-premises application architecture to reach high availability. It's most important for Siebel.

### Create a Fail-Over in a multiple availability zone deployment and second region deployments for disaster recovery

Business-critical Oracle Applications require failure prevention and therefore holistic architecture. One of these business-critical applications can be Oracle E-Business Suite.

As a given Tier 1 example for Oracle E-Business Suite in a multiple availability zone deployment and second region deployments for disaster recovery.

- First establish a multiple availability zone deployment with separated VNet with subnets. The Application tier uses Azure Site Recovery with a passive secondary virtual machine in availability zone three from the availability zone 1 primary.
- Use two Oracle Observers as a primary in availability zone one and a secondary in availability zone two. The observers monitor and direct the whole traffic to the primary database. Whereas the primary database is deployed in availability zone one. Oracle Data Guard performs the redo sync to availability zone two and can be configured for maximum availability. Data Guard can be established as synchronous or asynchronous. Within one region a synchronous configuration can be used for reaching a lower latency as in async mode.  

A second Data Guard standby configuration in the secondary region is established for disaster recovery purposes and is configured for maximum protection. Thereby backups of the database are performed by Azure Backup Volume Snapshot on Premium Files to the secondary region.

If a primary goes down, Observer(s) will reroute the traffic to the secondary DB2 as it comes out of standby, becomes primary and takes over all functionality for environment and sequence to fail over to secondary region standby if regional outage in first region.

:::image type="content" source="./images/oracle-architecture-01.jpg" alt-text="Diagram of a Tier 1 (Business Critical) for Oracle E-business Suite in a Multiple Availability Zone deployment and Second Region deployment for Disaster Recovery." lightbox="./images/oracle-architecture-01.jpg" border="false":::

### Create a failover for business critical Oracle applications in a two availability zone deployment with manual failover

The web server tier, application tier and database tier reside in its own virtual network subnet.

[Azure Site Recovery](/azure/site-recovery/site-recovery-overview) or the manual clone utility can be established to duplicate the passive secondary in AZ2. The primary will be set up in AZ1 whereas the database uses Data Guard to replicate it to an active Standby in AZ2.

A Failover would require manual intervention from the customer to fail over in case of a failure of AZ1. Backups use Active Data Guard standby in AZ2 and backup to Azure Premium files in AZ2 to remove any additional IO pressure to the primary database.

:::image type="content" source="./images/oracle-architecture-02.jpg" alt-text="Diagram of a Tier 2 (Production) Two-Availability Zone Deployment, Manual Failover. " lightbox="./images/oracle-architecture-02.jpg":::

## Checklist for Reliability

The reliability Checklist includes the Recovery Point Objective (RPO), Recovery Time Objective (RTO) and any business SLAs for the environment. The checklist should include all of the components for the environment, not just the database and may have different tiers of HA for different resources depending on their requirement for the business and system.

Choices for Oracle on Azure should include and be reviewed in the [design principles](/azure/architecture/framework/resiliency/principles) for adding reliability to the architecture.

To assess the Utilization, extract the AWR or statspack workload data to right-size the database in Azure (lift and shift the workload, not the on-premises hardware.).

Microsoft offers different sizes of Virtual Machines matching CPU, Memory and throughput. Based on the data in the AWR or statspack you can choose the correct VM SKU for Oracle Workload to ensure reliability and sustainability of the Oracle workload.

Some explanations to the different types of Virtual Machines are:
- D-series newest version (v5 or higher) for small Oracle databases or non-production.
- E-series VM skus, newest version, (v5 or higher) whenever available for most production Oracle database workloads.
- M-series for high memory/vCPU use Oracle databases but be cognizant of IO limits for attached disk. Preference for network attached storage.

Choose a virtual machine (VM) based on the needs of vCPU, memory and IO threshold for storage attached to VM, dependent on attached managed disk or network attached storage.

Based on the AWR and statspack data you should also choose a Premium SSD for OS Disk and place Linux swapfile(s) on VM temp storage.

The appropriate storage for throughput (MBPs) for Oracle workload should be chosen.

Another solution when your workload requires a high throughput is [Azure NetApp Files (ANF)](/azure/azure-netapp-files/azure-netapp-files-introduction). ANF is a third-Party Storage Solution (Silk, Flashgrid Storage).

For any multi-tier systems using E and D series VMs, consider using [Proximity Placement Groups](/azure/virtual-machines/co-location) for more consistent performance between VMs.

Calculate the Recovery Point Object (RPO), Recovery Time Objective (RTO) and uptime SLAs for the system and ensure it matches for both the IT organization and the business.

Consider enabling host level caching on the VM and use readonly caching on premium SSD P30-P50 for Oracle Datafiles. Performances for read-heavy workloads require consistency for relational systems. Where bursting may offer some benefits, most often we rely on host-level caching for best and consistent Oracle workload performance. Read-only is chosen as Oracle desires all writes to write through to the datafiles to avoid hindering issues by caching.

Use accelerated networking for VM. To reduce the latency on the network, [Azure ExpressRoute](/azure/expressroute/expressroute-introduction) should be established.

The application health should be monitored, not just Oracle, but application and middleware tier (if existing):
- Oracle Enterprise Manager can be deployed on a separate VM to monitor, maintain, and manage Oracle environments.
- Oracle Enterprise Manager text logs can be loaded to [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview) and used with [Azure Monitor](/azure/azure-monitor/overview) for a single pane of glass monitoring.

### Recommendations for your Oracle Workload on Azure

| Recommendation | Benefit |
| --- | --- |
| Use Host-level ReadOnly Caching for Oracle datafiles | P30-P50, (up to 4095G) can offer almost double MBPs on reads for datafiles. Bursting isn’t consistent or guaranteed, but caching offers better performance and at no extra cost. |
| Use Host-Level ReadWrite caching on OS Disk | Oracle VMs can benefit with ReadWrite caching for the OS disk only. |
| Separate Redo Logs for Log Latency | Log File Sync and Log File parallel write waits in Oracle can benefit by separating log files too small ultra disk allocation. If sync waits are shown, and requirement to have two members of redo log groups, separate members to TWO ultra disks with high enough IO set to handle log latency. Don’t configure any host-level caching if only redo logs on premium disk. |

For more suggestions, see [Principles of the reliability pillar.](/azure/architecture/framework/resiliency/principles)


## Next step

>[!div class="nextstepaction"]
>[Overview](./overview.md)

>[!div class="nextstepaction"]
>[Security](./security.md)

>[!div class="nextstepaction"]
>[Cost Optimization](./cost-optimization.md)

>[!div class="nextstepaction"]
>[Operational Excellence](./operational-excellence.md)

>[!div class="nextstepaction"]
>[Performance Efficiency](./performance-efficiency.md)
