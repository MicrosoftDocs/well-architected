---
title: Azure Well-Architected Framework perspective on Azure ExpressRoute
description: Provides architectural best practices for using Azure ExpressRoute.
author: duongau
ms.author: duau
ms.topic: conceptual
ms.service: azure-waf
ms.subservice: waf-service-guide 
ms.date: 06/07/2022
products: 
  - azure-expressroute
azure.category:
  - networking
---

# Azure Well-Architected Framework perspective on Azure ExpressRoute

Azure ExpressRoute is a service that enables you to create private connections between Azure data-centers and infrastructure that's on your premises or in a co-location facility. ExpressRoute connections don't go over the public internet, offering more reliability, faster speeds, and lower latencies than typical internet connections. ExpressRoute connections can offer higher security, reliability, and speeds with lower latencies than typical internet connections.

This article assumes that you have working knowledge of Azure ExpressRoute and are well versed with all of its features. For more information, see [Azure ExpressRoute](/azure/expressroute/expressroute-introduction). The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](/azure/well-architected/pillars).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural areas of
> concern along with design strategies localized to the technology scope.
>
> Also included are *recommendations* on the technology capabilities that can
> help materialize those strategies. The recommendations don't represent an
> exhaustive list of all configurations available for Azure ExpressRoute and its
> dependencies. Instead, they list the key recommendations mapped to the design
> perspectives. Use the recommendations to build your proof-of-concept or
> optimize your existing environments.
>
> Foundational architecture that demonstrates the key recommendations:
> [Implement Cloud Adoption Framework enterprise-scale landing zones in Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/implementation).

### Technology scope

This review focuses on the interrelated decisions for the following Azure resources:  

- Azure ExpressRoute

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **Building enough resilience and the ability
to recover fast from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the features and capabilities of Azure ExpressRoute. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Anticipate potential failures**: Plan mitigation strategies for potential failures. The following table shows examples of failure mode analysis.
>
>   | Failure | Mitigation |
>   |---|---|
>   | Circuit disruption, such as hardware failure at a peering location. | Have a secondary circuit for redundancy while service is restored.  Monitor the health of the circuit to identify when there might be issues. |
>   | Gateway disruption in the Azure virtual networks. | Have your gateway deployed in multiple availability zones, and with Active/Active configuration so that the disruption of the gateway in a single zone does not prevent connectivity. |
>
> - **Build redundancy, strengthen resiliency**: Eliminate single points of failure as much as practical. Plan for redundancy in the network design by configuring multiple ExpressRoute circuits, diverse paths, and multiple peering locations closest to your on-premises locations.
> Define availability and recovery targets for the components, the flows, and the overall solution. Visualize the targets to negotiate, gain consensus, set expectations, and drive actions to achieve the ideal state.
>
>    - **Site resiliency**: Planning for site resiliency is crucial to ensure high availability. ExpressRoute offers three architectures of site resiliency: Standard, High, and Maximum. [Standard resiliency](/azure/expressroute/design-architecture-for-resiliency#standard-resiliency) provides basic protection against site failures, [High resiliency](/azure/expressroute/design-architecture-for-resiliency#high-resiliency) offers enhanced protection with additional failover mechanisms, and [Maximum resiliency](/azure/expressroute/design-architecture-for-resiliency#maximum-resiliency) ensures the highest level of protection with multiple redundant systems and failover mechanisms.
>
>    - **Region and Availability zones**: Plan for multiple [region and availability zones](/azure/reliability/overview#regions-and-availability-zones) closest to your on-premises locations to provide resiliency and high availability.
>
>    - **Choose the right SKU**: ExpressRoute circuit SKUs provide redundancy through the use of geographic expansion. ExpressRoute have three SKUs, Local, Standard, and Premium.
>
>    | SKU          | Geographic Expansion                                           | Redundancy                        | Use Case                                                      |
>    |--------------|----------------------------------------------------------------|-----------------------------------|---------------------------------------------------------------|
>    | Local        | Access to Azure regions within or near the same metro area.     | Localized redundancy              | High-speed, low-latency connections within a metro area.       |
>    | Standard     | Access to all Azure regions within a specific geopolitical area.| Regional redundancy               | Broader regional connectivity needs.                           |
>    | Premium      | Global connectivity to any Azure region worldwide.              | Global redundancy                 | Extensive geographic reach for global operations.              |
>
>    - **ExpressRoute Global Reach**: As an ExpressRoute circuit Premium SKU feature, [ExpressRoute Global Reach](/azure/expressroute/expressroute-global-reach) allows you to link your on-premises networks across different geographical locations directly through the Azure backbone network. By connecting your on-premises networks to multiple Azure regions, Global Reach provides an additional layer of redundancy. If one Azure region becomes unavailable, you can quickly reroute traffic to another region without relying on the public internet, maintaining secure and reliable connectivity.
>
>    - **Service providers**: Choosing different service providers for each circuit to ensure diverse paths. This diversity in service providers minimizes the risk of network downtime due to a single provider's outage.
>
> - **Have a reliable scaling strategy**: Plan for scaling the network to meet the demands of the workloads. Scaling can be achieved by upgrading the ExpressRoute circuit bandwidth, increasing the size of the ExpressRoute Virtual Network Gateway, and enabling FastPath for higher throughput. You may face downtime or extra costs if they did not plan for scaling correctly. You should scale correctly when it comes to circuit planning and gateway planning. You should use [Azure Advisor](/azure/advisor/advisor-overview) to get monitoring suggestions for ExpressRoute resources. You should configure scalable gateways.
>
>   Plan for scaling of your organization by leveraging ExpressRoute circuits, which are dedicated connections that offer more reliability, faster speeds, and lower latencies than typical internet connections. Enhance your network's ability to scale by implementing the following strategies.
>
>    - Choosing the right ExpressRoute circuit SKU based on your scalability requirements through varied features and limits. The Local, Standard, and Premium SKUs offers different levels of connectivity, access, and performance capabilities, allowing organizations to choose the most appropriate option based on their specific needs. See the [ExpressRoute limits](/azure/expressroute/expressroute-faqs#limits) table for more information.
>
>    - Upgrading the ExpressRoute circuit bandwidth to meet the demands of your workloads. The bandwidth of an ExpressRoute circuit can be upgraded to meet the demands of your workloads. The bandwidth can be upgraded from 50 Mbps to 10 Gbps. For more information, see [Upgrade an ExpressRoute circuit](/azure/expressroute/expressroute-howto-circuit-portal-resource-manager#upgrade).
>
>    - Selecting the right ExpressRoute Virtual Network Gateway SKU for your network. Take into consideration the performance, feature, and routing needs of your network when selecting the ExpressRoute Virtual Network Gateway SKU. Consider upgrading your virtual network gateway SKU to maintain performance in your network. For more information, see [ExpressRoute Virtual Network Gateway](/azure/expressroute/expressroute-about-virtual-network-gateways).
>
>    - Monitoring the ExpressRoute circuit and gateway metrics to understand the performance of your ExpressRoute connection. Monitoring helps detect and resolve issues proactively, ensuring consistent performance and minimizing downtime. This practice supports adherence to SLAs and effective capacity planning, enhancing overall reliability. It is important to monitor the metrics to understand the performance of your ExpressRoute connection and to be prepared when it's time to adjust your scaling strategy based on re-time data and predictive analytics. For more information, see [Monitor ExpressRoute](/azure/expressroute/monitor-expressroute).
>
> - **Conduct reliability testing**: Test the network design for resiliency to ensure that the network can withstand failures. Testing can be achieved by using Azure Connectivity Toolkit to test performance across your ExpressRoute circuit to understand bandwidth capacity and latency of your network connection. Confirm failover mechanisms are working as expected. For more information, see [Evaluate circuit resiliency](/azure/expressroute/evaluate-circuit-resiliency).
> - **Disaster recovery planning**: Plan for disaster recovery to ensure that the network can recover from failures. Disaster recovery planning can be achieved by setting up ExpressRoute circuits in more than one peering location, creating circuits in peering locations in the same metro or different metro, and choosing to work with different service providers for diverse paths through each circuit.
> - **Use health indicators to identify disruptions**: Monitor the health of the ExpressRoute circuit and the ExpressRoute Virtual Network Gateway. Configure monitoring and alerts for ExpressRoute circuit and ExpressRoute Virtual Network Gateway health based on various metrics available. For more information, see [Operational Excellence](#operational-excellence).

### Recommendations

| Recommendation | Benefit |
|--------|----|
| During the initial planning phase, you want to decide whether you want to configure an [ExpressRoute circuit](/azure/expressroute/expressroute-circuit-peerings) or an [ExpressRoute Direct](/azure/expressroute/expressroute-erdirect-about) connection. |  An ExpressRoute circuit allows a private dedicated connection into Azure with the help of a connectivity provider. ExpressRoute Direct allows you to extend on-premises network directly into the Microsoft network at a peering location. You also need to identify the bandwidth requirement and the SKU type requirement for your business needs. |
| Design your architecture physical layer diversity. This can be achieved by having multiple paths between the on-premises edge and the peering locations (provider/Microsoft edge locations). | This configuration can be achieved by going through different service provider or through a different location from the on-premises network. |
| Plan for geo-redundant circuits by setting up ExpressRoute circuits in more than one peering locations. | There are scenarios where an ExpressRoute peering location or an entire regional service might experience degradation. Geo-redundancy enhances disaster recovery and high availability by ensuring that there are multiple, geographically diverse paths between on-premises networks and Azure. This reduces the risk of a single point of failure causing a network outage, thereby increasing the reliability and availability of the connection. For more information, see [Designing for disaster recovery](/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering) and [Designing for high availability](/azure/expressroute/designing-for-high-availability-with-expressroute). |
| Plan for Active-Active connectivity. | ExpressRoute dedicated circuits provide availability when an active-active connectivity is configured between on-premises and Azure. This mode provides higher availability of your ExpressRoute connection. It's also recommended to configure BFD for faster failover if there's a link failure on a connection.  |
| Planning for zone-redundant Virtual Network Gateways.| Creating [zone-redundant Virtual Network Gateways](/azure/vpn-gateway/about-zone-redundant-vnet-gateways) provide higher resiliency, support for disaster recovery, and high availability. Planning for Virtual Network Gateways in different regions can further ensure disaster recovery and high |
|[Monitor circuits and gateway health](/azure/expressroute/monitor-expressroute). | By setting up monitoring and alerts based on various metrics, you can proactively detect and address issues such as increased latency, traffic drops, or circuit downtimes before they impact your services. |
| [Enable service health](/azure/expressroute/maintenance-alerts) for notifications of planned and upcoming ExpressRoute circuit maintenance. | ExpressRoute uses service health to notify about planned and unplanned maintenance. Configuring service health notifies you about changes made to your ExpressRoute circuits. With Service Health, you can view planned and past maintenance in the Azure portal along with configuring alerts and notifications that best suits your needs. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](../security/principles.md) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure ExpressRoute.

### Design checklist

Start your design strategy based on the [**design review checklist for Security**](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review security baselines:** To get started first review the [security baseline for Azure ExpressRoute](/security/benchmark/azure/baselines/expressroute-security-baseline).
>
> - **Configure Network Security Groups (NSGs):** Configure NSGs restrict or monitor traffic by port, protocol, source IP address, or destination IP address.
>
> - **Implement Azure Role-Based Access Control (RBAC):** Use RBAC to control who can manage ExpressRoute resources such as ExpressRoute circuits and gateways. By providing granular access management to resources, you can maintain an inventory of administrative accounts with access to ExpressRoute resources and ensure that only authorized users can perform specific actions.
>
> - **Configure ExpressRoute encryption:** Encrypt data in transit over ExpressRoute circuits to ensure that data transmitted between on-premises networks and Azure virtual networks is secure and protected from unauthorized access. ExpressRoute supports the following encryption options.
>
>    -  **Media Access Control Security (MACsec)** Applied to *ExpressRoute Direct resources*, MACsec enhances ExpressRoute security by providing link-layer encryption and integrity protection, ensuring that data transmitted over ExpressRoute circuits is secure, tamper-proof, and compliant with regulatory requirements.
>
>    -  **Internet Protocol Security (IPsec)** Applied to *ExpressRoute private peering*, IPsec provides secure communication between on-premises networks and Azure virtual networks by encrypting data in transit. By configuring IPsec, you can ensure that data transmitted over ExpressRoute circuits is secure and protected from unauthorized access.
>
> - **Configure inbound and outbound route maps:** Route maps are used to control the flow of traffic between on-premises networks and Azure virtual networks. By configuring inbound and outbound route maps, you can define the path that traffic takes, ensuring that data is transmitted securely and efficiently.
>
> - **Configure Activity log to send logs to archive:** Activity logs are essential for auditing, compliance, incident response, operational visibility, and policy enforcement for ExpressRoute. Configure Activity log to send logs to an archive for long-term retention and analysis.
>
> - **Conduct security testing**: Regularly conduct security testing to identify vulnerabilities and ensure that security controls are effective. Security testing can include penetration testing, vulnerability scanning, and security assessments to identify and remediate security issues.

### Recommendations

Explore the following table of recommendations to optimize your ExpressRoute configuration for security.

| Recommendation | Benefit |
|--------|----|
| Configure Activity logs and send logs to archive | Activity logs provide insights into operations that were performed at the subscription level for ExpressRoute resources. With Activity logs, you can determine who and when an operation was performed at the control plane. Data retention is only 90 days and required to be stored in Log Analytics, Event Hubs or a storage account for archive. For more information about Activity logs in ExpressRoute, see [Monitor Azure ExpressRoute](/azure/expressroute/monitor-expressroute). |
| Configure Network Watcher Connection monitor | Connection monitor is a feature of Network Watcher that allows you to monitor connectivity between your on-premises network and Azure over the ExpressRoute private peering and Microsoft peering connection. Connection monitor can detect networking issues by identifying where along the network path the problem is and help you quickly resolve configuration or hardware failures. For more information about Connection monitor, see [Connection monitor](/azure/expressroute/how-to-configure-connection-monitor). |
| Managing inventory of administrative accounts | Use Azure RBAC to configure roles to limit user accounts that can add, update, or delete peering configuration on an ExpressRoute circuit. For more information about Azure RBAC, see [Azure RBAC](/azure/role-based-access-control/overview)  |
| Configure MD5 hash on ExpressRoute circuit | During configuration of private peering or Microsoft peering, apply an MD5 hash to secure messages between the on-premises route and the MSEE routers.  |
| Configure MACsec for ExpressRoute Direct resources | Media Access Control security is a point-to-point security at the data link layer. ExpressRoute Direct supports configuring MACSec to prevent security threats to protocols such as ARP, DHCP, LACP not normally secured on the Ethernet link. For more information on how to configure MACSec, see [MACSec for ExpressRoute Direct ports](/azure/expressroute/expressroute-about-encryption).   |
| Encrypt traffic using IPsec for ExpressRoute private peering | Configure a Site-to-site VPN tunnel over your ExpressRoute circuit to encrypt data transferring between your on-premises network and Azure virtual network. You can configure a tunnel using [private peering](/azure/vpn-gateway/site-to-site-vpn-private-peering?toc=%2Fazure%2Fexpressroute%2Ftoc.json) or using [Microsoft peering](/azure/expressroute/site-to-site-vpn-over-microsoft-peering).  |

For more suggestions, see [Principles of the security pillar](/azure/well-architected/security/security-principles).

## Cost optimization

Cost optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies. We recommend you review the [Cost optimization design principle](../cost-optimization/principles.md) and [Plan and manage costs for Azure ExpressRoute](/azure/expressroute/plan-manage-cost).

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
| Familiarize yourself with ExpressRoute pricing | For information about ExpressRoute pricing, see [Understand pricing for Azure ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/). You can also use the [Pricing calculator](https://azure.microsoft.com/pricing/calculator/).<br><br>Ensure that the options are adequately sized to meet the capacity demand and deliver expected performance without wasting resources.  |
| Determine SKU and bandwidth required | The way you're charged for your ExpressRoute usage varies between the three different SKU types. With Local SKU, you're automatically charged with an Unlimited data plan. With Standard and Premium SKU, you can select between a Metered or an Unlimited data plan. All ingress data are free of charge except when using the Global Reach add-on. It's important to understand which SKU types and data plan works best for your workload to best optimize cost and budget. For more information resizing ExpressRoute circuit, see [upgrading ExpressRoute circuit bandwidth](/azure/expressroute/about-upgrade-circuit-bandwidth). |
| Determine the ExpressRoute virtual network gateway size | ExpressRoute virtual network gateways are used to pass traffic into a virtual network over private peering. [Review the performance and scale](/azure/expressroute/expressroute-about-virtual-network-gateways) needs of your preferred Virtual Network Gateway SKU. Select the appropriate gateway SKU on your on-premises to Azure workload.  |
| Monitor cost and create budget alerts | Monitor the cost of your ExpressRoute circuit and create alerts for spending anomalies and overspending risks. For more information, see [Monitoring ExpressRoute costs](/azure/expressroute/plan-manage-cost#monitor-costs). |
| Deprovision and delete ExpressRoute circuits no longer in use. | ExpressRoute circuits are charged from the moment they're created. To reduce unnecessary cost, deprovision the circuit with the service provider and delete the ExpressRoute circuit from your subscription. For steps on how to remove an ExpressRoute circuit, see [Deprovisioning an ExpressRoute circuit](/azure/expressroute/expressroute-howto-circuit-portal-resource-manager#delete). |

For more suggestions, see [Design review checklist for Cost Optimization](../cost-optimization/checklist.md).

Azure Advisor can detect ExpressRoute circuits that have been deployed for a significant time but have a provider status of *Not Provisioned*. Circuits in this state aren't operational; and removing the unused resource will reduce unnecessary costs.

## Operational excellence

Monitoring and diagnostics are crucial. Not only can you measure performance statistics but also use metrics troubleshoot and remediate issues quickly. We recommend you review the [Operational excellence design principles](../devops/principles.md).

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
| Configure Service Health | Set up [Service Health notifications](/azure/expressroute/maintenance-alerts) to alert when planned and upcoming maintenance is happening to all ExpressRoute circuits in your subscription. Service Health also displays past maintenance along with RCA if an unplanned maintenance were to occur. |
| Review metrics with Network Insights | [ExpressRoute Insights with Network Insights](/azure/expressroute/expressroute-network-insights) allow you to review and analyze ExpressRoute circuits, gateways, connections metrics and health dashboards. ExpressRoute Insights also provide a topology view of your ExpressRoute connections where you can view details of your peering components all in a single place.<br><br>Metrics available:<br>- Availability<br>- Throughput<br>- Gateway metrics |
| Review ExpressRoute resource metrics | ExpressRoute uses Azure Monitor to collect [metrics and create alerts](/azure/expressroute/expressroute-monitoring-metrics-alerts) base on your configuration. Metrics are collected for ExpressRoute circuits, ExpressRoute gateways, ExpressRoute gateway connections, and ExpressRoute Direct. These metrics are useful for diagnosing connectivity problems and understanding the performance of your ExpressRoute connection. |

For more suggestions, see [Principles of the operational excellence pillar](/azure/well-architected/devops/principles).

## Performance efficiency

Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. We recommend you review the [Performance efficiency principles](../scalability/principles.md).

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

For more suggestions, see [Principles of the performance efficiency pillar](/azure/well-architected/scalability/principles).

Azure Advisor offers a recommendation to upgrade your ExpressRoute circuit bandwidth to accommodate usage when your circuit is consuming over 90% of your procured bandwidth. If your traffic exceeds your allocated bandwidth, you'll experience dropped packets, which can lead to significant performance or reliability impact.

## Azure Policy

Azure Policy doesn't provide any built-in policies for ExpressRoute, but custom policies can be created to help govern how ExpressRoute circuits should match your desired end state, such as SKU choice, peering type, peering configurations and so on.

## Additional resources

### Cloud Adoption Framework guidance

* [Traditional Azure network topology](/azure/cloud-adoption-framework/ready/azure-best-practices/traditional-azure-networking-topology)
* [Virtual WAN network topology (Microsoft-managed)](/azure/cloud-adoption-framework/ready/azure-best-practices/traditional-azure-networking-topology)

## Next steps

Configure an [ExpressRoute circuit](/azure/expressroute/expressroute-howto-circuit-portal-resource-manager) or [ExpressRoute Direct port](/azure/expressroute/how-to-expressroute-direct-portal) to establish communication between your on-premises network and Azure.
