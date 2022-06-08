---
title: Microsoft Azure Well-Architected Framework review - Azure ExpressRoute
description: Provides architectural best practices for using Azure ExpressRoute.
author: duongau
ms.author: duau
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.date: 06/07/2022
---

# Azure Well-Architected Framework review - Azure ExpressRoute

This article provides architectural best practice for Azure ExpressRoute. The guidance is based on the five pillars of the architecture excellence:

* [Reliability](#reliability)
* [Security](#security)
* [Cost optimization](#cost-optimization)
* [Operational excellence](#operational-excellence)
* [Performance efficiency](#performance-efficiency)

We assume that you have working knowledge of Azure ExpressRoute and are well versed with all of its features. For more information, see [Azure ExpressRoute](/azure/expressroute/expressroute-introduction).

## Prerequisites

For context, consider reviewing a reference architecture that reflects these considerations in its design. We recommend that you start with Cloud Adoption Framework Ready methodology's guidance [Connect to Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/connectivity-to-azure) and [Architect for hybrid connectivity](/azure/architecture/reference-architectures/hybrid-networking/expressroute) with Azure ExpressRoute. For low-code application architectures, we recommend reviewing [Enabling ExpressRoute for Power Platform](/power-platform/guidance/expressroute/overview) when planning and configuring ExpressRoute for use with Microsoft Power Platform.

## Reliability

In the cloud, we acknowledge that failures happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component. Use the following information to minimize down time to and from Azure when establishing connectivity using Azure ExpressRoute.

When discussing about reliability with Azure ExpressRoute it's important to taking into consideration bandwidth usage, physical layout of the network, and disaster recovery if there's failures. Azure ExpressRoute is capable of achieving these design considerations and have recommendations for each item in the checklist.

In the **design checklist** and **list of recommendations** below, information is presented in order for you to design a highly available network between your Azure environment and on-premises network.

### Design checklist

As you make design choices for Azure ExpressRoute, review the [design principles](/azure/architecture/framework/resiliency/principles) for adding reliability to the architecture.

> [!div class="checklist"]
> - Select between ExpressRoute circuit or ExpressRoute Direct for business requirements.
> -	Configure a diverse physical layer network to the service provider.
> -	Configure ExpressRoute circuits with different service provider to have diverse routing paths.
> - Configure Active-Active ExpressRoute connections between on-premises and Azure.
> - Set up availability zone aware ExpressRoute Virtual Network Gateways.
> -	Configure ExpressRoute circuits in a different location than the on-premises network.
> - Configure ExpressRoute Virtual Network Gateways in different regions.
> - Configure site-to-site VPN as a backup to ExpressRoute private peering.
> -	Set up monitoring for ExpressRoute circuit and ExpressRoute Virtual Network Gateway health.
> - Configure service health to receive ExpressRoute circuit maintenance notification.

### Recommendations

Explore the following table of recommendations to optimize your ExpressRoute configuration for Reliability.

| Recommendation | Benefit |
|--------|----|
| Plan for ExpressRoute circuit or ExpressRoute Direct | During the initial planning phase, you want to decide whether you want to configure an ExpressRoute circuit or an ExpressRoute Direct connection. An ExpressRoute circuit allows a private dedicated connection into Azure with the help of a connectivity provider. ExpressRoute Direct allows you to extend on-premises network directly into the Microsoft network at a peering location. You also need to identify the bandwidth requirement and the SKU type requirement for your business needs. |
| Physical layer diversity | For better resiliency, plan to have multiple paths between the on-premises edge and the peering locations (provider/Microsoft edge locations). This configuration can be achieved by going through different service provider or through a different location from the on-premises network. |
| Plan for geo-redundant circuits| To plan for disaster recovery, set up ExpressRoute circuits in more than one peering locations. You can create circuits in peering locations in the same metro or different metro and choose to work with different service providers for diverse paths through each circuit. |
| Plan for Active-Active connectivity | ExpressRoute dedicated circuits guarantee `99.95%` availability when an active-active connectivity is configured between on-premises and Azure. This mode provides higher availability of your Expressroute connection. It's also recommended to configure BFD for faster failover if there's a link failure on a connection. |
| Planning for Virtual Network Gateways | Create availability zone aware Virtual Network Gateway for higher resiliency and plan for Virtual Network Gateways in different region for disaster recovery and high availability. Also think about configuring a site-to-site VPN connection as a backup to private peering if there's complete ExpressRoute circuit failure. |
| Monitor circuits and gateway health | Set up monitoring and alerts for ExpressRoute circuits and Virtual Network Gateway health based on various metrics available. |
| Enable service health | ExpressRoute uses service health to notify about planned and unplanned maintenance. Configuring service health will notify you about changes made to your ExpressRoute circuits. |

For more suggestions, see [Principles of the reliability pillar](/azure/architecture/framework/resiliency/principles).

Azure Advisor provides many recommendations for ExpressRoute circuits as they relate to reliability. For example, Azure Advisor can detect:

* ExpressRoute gateways in which only a single ExpressRoute circuit is deployed, instead of multiple. Multiple ExpressRoute circuits are recommended for add resiliency for the peering location.
* ExpressRoute circuits that aren't being observed by Connection Monitor, as end-to-end monitoring of your ExpressRoute circuit is critical for reliability insights.
* Network topologies involving multiple peering locations that would benefit from ExpressRoute Global Reach to improve disaster recovery designs for on-premises connectivity to account for unplanned connectivity loss.

### Azure Policy

Azure Policy doesn't provide any built-in policies for ExpressRoute, but custom policies can be created to help govern how ExpressRoute circuits should match your desired end state, such as SKU choice, peering type, peering configurations and so on.

## Security

Security is one of the most important aspects of any architecture. ExpressRoute provides features to employ both the principle of least privilege and defense-in-defense. We recommend you review the [Security design principles](../../security/security-principles.md).

### Design checklist

> [!div class="checklist"]
> - Configure Activity log to send logs to archive.
> - Maintain an inventory of administrative accounts with access to ExpressRoute resources.
> - Configure MD5 hash on ExpressRoute circuit.
> - Configure MACSec for ExpressRoute Direct resources.
> - Encrypt traffic over private peering and Microsoft peering for virtual network traffic.

### Recommendations

Explore the following table of recommendations to optimize your ExpressRoute configuration for security.

| Recommendation | Benefit |
|--------|----|
| Configure Activity log to send logs to archive | Activity logs provide insights into operations that were performed at the subscription level for ExpressRoute resources. With Activity logs, you can determine who and when an operation was performed at the control plane. Data retention is only 90 days and required to be stored in Log Analytics, Event Hubs or a storage account for archive. |
| Maintain inventory of administrative accounts | Use Azure RBAC to configure roles to limit user accounts that can add, update, or delete peering configuration on an ExpressRoute circuit.  |
| Configure MD5 hash on ExpressRoute circuit | During configuration of private peering or Microsoft peering, apply an MD5 hash to secure messages between the on-premises route and the MSEE routers.  |
| Configure MACSec for ExpressRoute Direct resources | Media Access Control security is a point-to-point security at the data link layer. ExpressRoute Direct supports configuring MACSec to prevent security threats to protocols such as ARP, DHCP, LACP not normally secured on the Ethernet link. For more information on how to configure MACSec, see [MACSec for ExpressRoute Direct ports](/azure/expressroute/expressroute-about-encryption).   |
| Encrypt traffic using IPsec | Configure a Site-to-site VPN tunnel over your ExpressRoute circuit to encrypt data transferring between your on-premises network and Azure virtual network. You can configure a tunnel using [private peering](/azure/vpn-gateway/site-to-site-vpn-private-peering?toc=%2Fazure%2Fexpressroute%2Ftoc.json) or using [Microsoft peering](/azure/expressroute/site-to-site-vpn-over-microsoft-peering).  |

For more suggestions, see [Principles of the security pillar](/azure/architecture/framework/security/security-principles).

There are no Azure Advisor related recommendations for this category.

### Azure Policy

Azure Policy doesn't provide any built-in policies for ExpressRoute, but custom policies can be created to help govern how ExpressRoute circuits should match your desired end state, such as SKU choice, peering type, peering configurations and so on.

## Cost optimization

Cost optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies. We recommend you review the [Cost optimization design principle](../../cost/principles.md) and [Plan and manage costs for Azure ExpressRoute](/azure/expressroute/plan-manage-cost).

### Design checklist

> [!div class="checklist"]
> - Familiarize yourself with ExpressRoute pricing.
> - Determine the ExpressRoute circuit SKU and bandwidth required.
> - Determine the ExpressRoute virtual network gateway size required.
> - Monitor cost and create budget alerts.
> - Deprovision ExpressRoute circuits no longer in use.

### Recommendations

Explore the following table of recommendations to optimize your ExpressRoute configuration for Cost optimization.

| Recommendation | Benefit |
|--------|----|
| Familiarize yourself with ExpressRoute pricing | For information about ExpressRoute pricing, see [Understand pricing for Azure ExpressRoute](https://azure.microsoft.com/en-us/pricing/details/expressroute/). You can also use the [Pricing calculator](https://azure.microsoft.com/pricing/calculator/).<br><br>Ensure that the options are adequately sized to meet the capacity demand and deliver expected performance without wasting resources.  |
| Determine SKU and bandwidth required | The way you're charged for your ExpressRoute usage varies between the three different SKU types. With Local SKU, you're automatically charged with an Unlimited data plan. With Standard and Premium SKU, you can select between a Metered or an Unlimited data plan. All ingress data are free of charge except when using the Global Reach add-on. It's important to understand which SKU types and data plan works best for your workload to best optimize cost and budget. For more information resizing ExpressRoute circuit, see [upgrading ExpressRoute circuit bandwidth](/azure/expressroute/about-upgrade-circuit-bandwidth). |
| Determine the ExpressRoute virtual network gateway size | ExpressRoute virtual network gateways are used to pass traffic into a virtual network over private peering. [Review the performance and scale](/azure/expressroute/expressroute-about-virtual-network-gateways) needs of your preferred Virtual Network Gateway SKU. Select the appropriate gateway SKU on your on-premises to Azure workload.  |
| Monitor cost and create budget alerts | Monitor the cost of your ExpressRoute circuit and create alerts for spending anomalies and overspending risks. For more information, see [Monitoring ExpressRoute costs](/azure/expressroute/plan-manage-cost#monitor-costs). |
| Deprovision and delete ExpressRoute circuits no longer in use. | ExpressRoute circuits are charged from the moment they're created. To reduce unnecessary cost, deprovision the circuit with the service provider and delete the ExpressRoute circuit from your subscription. For steps on how to remove an ExpressRoute circuit, see [Deprovisioning an ExpressRoute circuit](/azure/expressroute/expressroute-howto-circuit-portal-resource-manager#delete). |

For more suggestions, see [Principles of the cost optimization pillar](/azure/architecture/framework/cost/overview).

Azure Advisor can detect ExpressRoute circuits that have been deployed for a significant time but have a provider status of *Not Provisioned*. Circuits in this state aren't operational; and removing the unused resource will reduce unnecessary costs.

### Azure Policy

Azure Policy doesn't provide any built-in policies for ExpressRoute, but custom policies can be created to help govern how ExpressRoute circuits should match your desired end state, such as SKU choice, peering type, peering configurations and so on.


## Operational excellence

Monitoring and diagnostics are crucial. Not only can you measure performance statistics but also use metrics troubleshoot and remediate issues quickly. We recommend you review the [Operational excellence design principles](../../devops/principles.md).

### Design checklist

> [!div class="checklist"]
> - Configure connection monitoring between your on-premises and Azure network.
> - Configure Service Health for receiving notification.
> - Review metrics and dashboards available through ExpressRoute Insights using Network Insights.
> - Review ExpressRoute resource metrics.

### Recommendations

Explore the following table of recommendations to optimize your ExpressRoute configuration for Operational excellence.

| Recommendation | Benefit |
|--------|----|
| Configure connection monitoring | [Connection monitoring](/azure/expressroute/how-to-configure-connection-monitor) allows you to monitor connectivity between your on-premises resources and Azure over the ExpressRoute private peering and Microsoft peering connection. Connection monitor can detect networking issues by identifying where along the network path the problem is and help you quickly resolve configuration or hardware failures. |
| Configure Service Health | Set up [Service Health notifications](/azure/expressroute/maintenance-alerts) to alert when planned and upcoming maintenance is happening to all ExpressRoute circuits in your subscription. Service Health also displays past maintenance along with RCA if an unplanned maintenance were to occurred. |
| Review metrics with Network Insights | [ExpressRoute Insights with Network Insights](/azure/expressroute/expressroute-network-insights) allow you to review and analyze ExpressRoute circuits, gateways, connections metrics and health dashboards. ExpressRoute Insights also provide a topology view of your ExpressRoute connections where you can view details of your peering components all in a single place.<br><br>Metrics available:<br>- Availability<br>- Throughput<br>- Gateway metrics |
| Review ExpressRoute resource metrics | ExpressRoute uses Azure Monitor to collect [metrics and create alerts](/azure/expressroute/expressroute-monitoring-metrics-alerts) base on your configuration. Metrics are collected for ExpressRoute circuits, ExpressRoute gateways, ExpressRoute gateway connections, and ExpressRoute Direct. These metrics are useful for diagnosing connectivity problems and understanding the performance of your ExpressRoute connection. |

For more suggestions, see [Principles of the operational excellence pillar](/azure/architecture/framework/devops/principles).

There are no Azure Advisor related recommendations for this category.

### Azure Policy

Azure Policy doesn't provide any built-in policies for ExpressRoute, but custom policies can be created to help govern how ExpressRoute circuits should match your desired end state, such as SKU choice, peering type, peering configurations and so on.


## Performance efficiency

Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. We recommend you review the [Performance efficiency principles](../../scalability/principles.md).

### Design checklist


> [!div class="checklist"]
> - Test ExpressRoute gateway performance to meet work load requirements.
> - Increase the size of the ExpressRoute gateway.
> - Upgrade the ExpressRoute circuit bandwidth.
> - Enable ExpressRoute FastPath for higher throughput.
> - Monitor the ExpressRoute circuit and gateway metrics.

### Recommendations

Explore the following table of recommendations to optimize your ExpressRoute configuration for performance efficiency.

| Recommendation | Benefit |
|--------|----|
| Test ExpressRoute gateway performance to meet work load requirements. | Use [Azure Connectivity Toolkit](/azure/expressroute/expressroute-troubleshooting-network-performance#azurect---the-azure-connectivity-toolkit) to test performance across your ExpressRoute circuit to understand bandwidth capacity and latency of your network connection. |
| Increase the size of the ExpressRoute gateway. | Upgrade to a higher [gateway SKU](/azure/expressroute/expressroute-about-virtual-network-gateways) for improved throughput performance between on-premises and Azure environment. |
| Upgrade ExpressRoute circuit bandwidth | Upgrade your [circuit bandwidth](/azure/expressroute/about-upgrade-circuit-bandwidth) to meet your work load requirements. Circuit bandwidth is shared between all virtual networks connected to the ExpressRoute circuit. Depending on your work load, one or more virtual networks can use up all the bandwidth on the circuit.  |
| Enable ExpressRoute FastPath for higher throughput | If you're using an Ultra performance or an ErGW3AZ virtual network gateway, you can enable [FastPath](/azure/expressroute/about-fastpath) to improve the data path performance between your on-premises network and Azure virtual network. |
| Monitor ExpressRoute circuit and gateway metrics | Set up alerts base on [ExpressRoute metrics](/azure/expressroute/monitor-expressroute) to proactively notify you when a certain threshold is met. These metrics are useful to understand anomalies that can happen with your ExpressRoute connection such as outages and maintenance happening to your ExpressRoute circuits. |

For more suggestions, see [Principles of the performance efficiency pillar](/azure/architecture/framework/scalability/principles).

Azure Advisor will offer a recommendation to upgrade your ExpressRoute circuit bandwidth to accommodate usage when your circuit has recently been consuming over 90% of your procured bandwidth. If your traffic exceeds your allocated bandwidth, you’ll experience dropped packets, which can lead to significant performance or reliability impact.

### Azure Policy

Azure Policy doesn't provide any built-in policies for ExpressRoute, but custom policies can be created to help govern how ExpressRoute circuits should match your desired end state, such as SKU choice, peering type, peering configurations and so on.

## Additional resources

### Cloud Adoption Framework guidance

* [Traditional Azure network topology](/azure/cloud-adoption-framework/ready/azure-best-practices/traditional-azure-networking-topology)
* [Virtual WAN network topology (Microsoft-managed)](/azure/cloud-adoption-framework/ready/azure-best-practices/traditional-azure-networking-topology)

## Next steps

Configure an [ExpressRoute circuit](/azure/expressroute/expressroute-howto-circuit-portal-resource-manager) or [ExpressRoute Direct port](/azure/expressroute/how-to-expressroute-direct-portal) to establish communication between your on-premises network and Azure.
