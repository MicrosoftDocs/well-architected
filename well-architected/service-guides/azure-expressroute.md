---
title: Architecture Best Practices for Azure ExpressRoute
description: Provides architectural best practices for using Azure ExpressRoute.
author: duongau
ms.author: duau
ms.topic: concept-article
ms.date: 08/17/2025
products: 
  - azure-expressroute
azure.category:
  - networking
---

# Architecture best practices for Azure ExpressRoute

Azure ExpressRoute enables you to create private connections between Azure data-centers and infrastructure that's on your premises or in a colocation facility. ExpressRoute connections don't go over the public internet, offering more security, faster speeds, and lower latencies than typical internet connections. 

This article assumes that you have working knowledge of Azure ExpressRoute and are well versed with all of its features. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](/azure/well-architected/pillars).

### Technology scope

This review focuses on the interrelated decisions for the following Azure resources:  

- Azure ExpressRoute

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

Highly reliable network connectivity depends on designing for multiple concerns, including resiliency and recoverability together with understanding the availability commitments from Azure. Resiliency minimizes the impact of failures by keeping traffic flowing within defined SLOs during circuit, gateway, or path issues. Recoverability focuses on restoring connectivity after larger site or regional disruptions within agreed RTO/RPO targets. Design each explicitly based on business needs and application requirements.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the features and capabilities of Azure ExpressRoute. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Build redundancy, strengthen resiliency:** Eliminate single points of failure as much as practical. Plan for redundancy in the network design by configuring multiple ExpressRoute circuits, diverse paths, and multiple peering locations closest to your on-premises locations.
> Define availability and recovery targets for the components, the flows, and the overall solution. Visualize the targets to negotiate, gain consensus, set expectations, and drive actions to achieve the ideal state.
>
> - **Anticipate potential failures:** Plan mitigation strategies for potential failures. The following table shows examples of failure mode analysis.
>
>   | Failure | Mitigation |
>   |---|---|
>   | Circuit disruption, such as hardware failure at a peering location. | Have a secondary circuit for redundancy while service is restored.  Monitor the health of the circuit to identify when there might be issues. |
>   | Gateway disruption in the Azure virtual networks. | Have your gateway deployed in multiple availability zones, and with Active/Active configuration so that the disruption of the gateway in a single zone does not prevent connectivity. |
>
> - **Plan for site resiliency:** Planning for site resiliency is crucial to ensure high availability. ExpressRoute offers three architectures of site resiliency: *Standard*, *High*, and *Maximum*. Standard resiliency provides basic protection against link failures, but does not provide protection against site failures. High resiliency offers enhanced protection with additional failover mechanisms, and Maximum resiliency ensures the highest level of protection with multiple redundant systems and failover mechanisms.
>
> - **Plan for regions and availability zones:** Plan for multiple region and availability zones closest to your on-premises locations to provide resiliency and high availability.
>
> - **Plan for ExpressRoute circuit or ExpressRoute Direct:** During the initial planning phase, you want to decide whether you want to configure an ExpressRoute circuit or an ExpressRoute Direct connection.
>
> - **Choose the right circuit SKU:** ExpressRoute circuit SKUs provide redundancy through the use of geographic expansion. ExpressRoute have three SKUs: Local, Standard, and Premium.
>
>    | SKU          | Geographic Expansion                                           | Redundancy                        | Use Case                                                      |
>    |--------------|----------------------------------------------------------------|-----------------------------------|---------------------------------------------------------------|
>    | Local        | Access to Azure regions within or near the same metro area.     | Localized redundancy              | High-speed, low-latency connections within a metro area.       |
>    | Standard     | Access to all Azure regions within a specific geopolitical area.| Regional redundancy               | Broader regional connectivity needs.                           |
>    | Premium      | Global connectivity to any Azure region worldwide.              | Global redundancy                 | Extensive geographic reach for global operations.              |
>
> - **Plan for Active-Active connectivity:** ExpressRoute dedicated circuits provide availability when an active-active connectivity is configured between on-premises and Azure. This configuration provides higher availability of your ExpressRoute connection.
>
> - **Plan for geo-redundant circuits:** Configure ExpressRoute circuits in more than one peering location to ensure that there are multiple, geographically diverse paths between on-premises networks and Azure. This reduces the risk of a single point of failure causing a network outage, thereby increasing the reliability and availability of the connection.
>
> - **Configure ExpressRoute Global Reach:** As an ExpressRoute circuit Premium SKU feature, ExpressRoute Global Reach allows you to link your on-premises networks across different geographical locations directly through the Azure backbone network. By connecting your on-premises networks to multiple Azure regions, Global Reach provides an additional layer of redundancy. If one Azure region becomes unavailable, you can quickly reroute traffic to another region without relying on the public internet, maintaining secure and reliable connectivity.
>
> - **Configure site-to-site VPN as a backup to ExpressRoute private peering:** This configuration provides an additional layer of redundancy and ensures that your network remains operational even if the ExpressRoute connection experiences an outage.
>
> - **Plan for Virtual Network Gateways:** When selecting and configuring your ExpressRoute Virtual Network Gateway for resiliency, consider the following best practices:
>
>    - Select the right ExpressRoute Virtual Network Gateway SKU to reflect the correct performance and throughput for your business.
>
>    - Configure ExpressRoute Gateways in different regions for regional redundancy & resiliency.
>
>    - Configure zone-redundant Virtual Network Gateway within Availability Zones for zone redundancy and resiliency.
>
>    - Deploy scalable Virtual Network Gateways that allow auto-scale based on your required throughput.
>  
> - **Plan for service providers:** Choose different service providers for each circuit to ensure diverse paths. This diversity in service providers minimizes the risk of network downtime due to a single provider's outage.
>
> - **Conduct reliability testing:** Test the network design for resiliency to ensure that the network can withstand failures. Testing can be achieved by using Azure Connectivity Toolkit to test performance across your ExpressRoute circuit to understand bandwidth capacity and latency of your network connection. Confirm failover mechanisms are working as expected.
>
> - **Configure monitoring for ExpressRoute circuits and ExpressRoute Virtual Network Gateway health:** Configure monitoring and alerts for ExpressRoute circuit and ExpressRoute Virtual Network Gateway health based on various metrics available.
>
> - **Use health indicators to identify disruptions:** Configure monitoring and alerts for ExpressRoute circuit and ExpressRoute Virtual Network Gateway health based on various metrics available.

### Configuration recommendations

| Recommendation | Benefit |
|--------|----|
| Anticipate and mitigate potential failures when you [design and architect Azure ExpressRoute](/azure/expressroute/design-architecture-for-resiliency). | Anticipating failures leads to the design of a more robust and resilient network architecture that can withstand various failure scenarios. |
| [Plan for site resiliency](/azure/expressroute/design-architecture-for-resiliency). For [Maximum](/azure/expressroute/design-architecture-for-resiliency#maximum-resiliency) or [High](/azure/expressroute/design-architecture-for-resiliency#high-resiliency) resiliency, plan to have multiple paths between the on-premises edge and the peering locations (provider/Microsoft edge locations). For Maximum Resiliency, [configure multiple circuits to different peering locations](/azure/expressroute/designing-for-high-availability-with-expressroute#first-mile-physical-layer-design-considerations). For High Resiliency, configure a circuit between multiple peering locations within the same metropolitan area (also referred to as ExpressRoute Metro) from the on-premises network. | By having multiple paths between the on-premises edge and the peering locations, the network can continue to operate even if one path fails. This redundancy is crucial for maintaining continuous connectivity and minimizing downtime. |
| Plan for multiple [region and availability zones](/azure/reliability/overview#regions-and-availability-zones).  | Availability zones are physically separate locations within a region, providing fault isolation. This means that failures in one zone don't affect the others, enhancing overall system reliability. |
| Plan for [ExpressRoute circuit](/azure/expressroute/expressroute-circuit-peerings#circuits) or [ExpressRoute Direct](/azure/expressroute/expressroute-erdirect-about). During the initial planning phase, you want to decide whether you want to configure an ExpressRoute circuit or an ExpressRoute Direct connection. You also need to identify the bandwidth requirement and the SKU type requirement for your business needs. | An ExpressRoute circuit allows a private dedicated connection into Azure with the help of a connectivity provider. ExpressRoute Direct allows you to extend on-premises network directly into the Microsoft network at a peering location. |
| Choose the right circuit SKU for redundancy by using geographic expansion. The [Local, Standard, and Premium SKUs](/azure/expressroute/expressroute-faqs#expressroute-sku-scope-access) offer different levels of connectivity, access, and performance capabilities. Premium SKU provides the highest level of redundancy with global connectivity to any Azure region worldwide. | Choosing the right circuit SKU ensures that you have the appropriate level of redundancy and connectivity for your workloads.  |
| [Plan for Active-Active connectivity](/azure/expressroute/designing-for-high-availability-with-expressroute#active-active-connections). To improve high availability, redundancy, and resiliency, we recommend operating both connections of an ExpressRoute circuit in active-active mode. Additionally, configure [Bi-Directional Forwarding Detection (BFD)](/azure/expressroute/expressroute-bfd) over both private and Microsoft Peering for faster failover during a link failure. | Active-active mode mode provides higher availability of your ExpressRoute connections. BFD provides rapid detection of link failures, enabling quicker failover to backup paths. This minimizes downtime and ensures continuous connectivity. |
| Plan for [geo-redundant circuits](/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering).  | There are scenarios where an ExpressRoute peering location or an entire regional service might experience degradation. Geo-redundancy enhances disaster recovery and high availability by ensuring that there are multiple, geographically diverse paths between on-premises networks and Azure. This reduces the risk of a single point of failure causing a network outage, thereby increasing the reliability and availability of the connection. |
| With [ExpressRoute Global Reach](/azure/expressroute/expressroute-global-reach) you can link ExpressRoute circuits together to make a private network between your on-premises networks. [Configure ExpressRoute Global Reach](/azure/expressroute/expressroute-howto-set-global-reach) on your ExpressRoute circuit Premium SKU.  | ExpressRoute Global Reach provides an additional layer of redundancy by linking your on-premises networks across different geographical locations directly through the Azure backbone network. This ensures that your network remains connected and operational even if one Azure region becomes unavailable. |
| Choose different [ExpressRoute service providers](/azure/expressroute/expressroute-locations-providers) for each circuit. | Diversity in service providers minimizes the risk of network downtime due to a single provider's outage. By choosing different service providers for each circuit, you can ensure that your network remains operational even if one provider experiences an outage. This redundancy is essential for maintaining continuous connectivity and minimizing downtime.|
| Configure [Site-to-Site VPN over Microsoft peering](/azure/expressroute/site-to-site-vpn-over-microsoft-peering) as a backup to ExpressRoute private peering. Site-to-site VPN provides an additional layer of redundancy and ensures that your network remains operational even if the ExpressRoute connection experiences an outage. |  By configuring a site-to-site VPN as a backup to ExpressRoute private peering, you can maintain continuous connectivity and minimize downtime. |
| Planning for [zone-redundant Virtual Network Gateways](/azure/vpn-gateway/about-zone-redundant-vnet-gateways). Select the right [ExpressRoute Virtual Network Gateway SKU](/azure/expressroute/expressroute-about-virtual-network-gateways#gwsku) to reflect the correct performance and throughput for your business. Consider deploying a [scalable virtual network gateway](/azure/expressroute/expressroute-about-virtual-network-gateways#expressroute-scalable-gateway-preview) that allows you to achieve 40-Gbps connectivity and will auto-scale based on your required throughput.  Deploy ExpressRoute virtual network gateways that are [zone-redundant](/azure/expressroute/expressroute-about-virtual-network-gateways#zrgw) for maximum resiliency and redundancy across Availability Zones.| Choosing the appropriate SKU ensures that the gateway can handle the required performance and throughput for your business needs. A scalable virtual network gateway autoscales based on required throughput, allowing the network to adapt to changing demands. This flexibility helps maintain performance during peak usage times and prevents overloading. Additionally, deploying zone-redundant virtual network gateways ensures that the network remains operational even if one availability zone experiences an outage, enhancing overall reliability and resiliency.   |
| Conduct [reliability testing](/azure/expressroute/evaluate-circuit-resiliency) with the [Azure Connectivity Toolkit](/azure/expressroute/expressroute-troubleshooting-network-performance#azurect---the-azure-connectivity-toolkit) to ensure that the network design is resilient and can withstand failures. | Reliability testing helps identify potential issues and weaknesses in the network design, allowing you to address them proactively. By conducting reliability testing, you can ensure that the network is robust and resilient, minimizing downtime and ensuring continuous connectivity. |
| Configure [monitoring and alerts](/azure/expressroute/monitor-expressroute) for ExpressRoute [circuits](/azure/expressroute/monitor-expressroute-reference#supported-metrics-for-microsoftnetworkexpressroutecircuits), [peering](/azure/expressroute/monitor-expressroute-reference#supported-metrics-for-microsoftnetworkexpressroutecircuitspeerings), [ports](/azure/expressroute/monitor-expressroute-reference#supported-metrics-for-microsoftnetworkexpressrouteports), and [Virtual Network Gateway](/azure/expressroute/monitor-expressroute-reference#supported-metrics-for-microsoftnetworkexpressroutegateways) resource health based on various available metrics. This helps in proactively managing and maintaining the health of your network. Use [Network Insights](/azure/expressroute/expressroute-network-insights) for ExpressRoute to visualize topological maps and health dashboards, providing a clear view of your configurations and their status. | By setting up monitoring and alerts based on various metrics, you can proactively detect and address issues such as increased latency, traffic drops, or circuit downtimes before they impact your services. |
| [Configure service health](/azure/expressroute/maintenance-alerts) to notify you about planned and unplanned maintenance. Configuring service health notifies you about changes made to your ExpressRoute circuits. | With Service Health, you can view planned and past maintenance in the Azure portal along with configuring alerts and notifications that best suits your needs. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](../security/principles.md) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure ExpressRoute.

### Workload design checklist

Start your design strategy based on the [**design review checklist for Security**](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Leverage Azure Security Baseline for ExpressRoute:** The Microsoft cloud security benchmark provides recommendations on how you can secure your cloud solutions on Azure.
>
> - **Implement Azure Role-Based Access Control (RBAC):** Use Azure RBAC to configure roles to limit user accounts that can add, update, or delete peering configurations on an ExpressRoute circuit or change ExpressRoute resources.
>
> - **Configure ExpressRoute encryption:** Encrypt data in transit over ExpressRoute circuits to ensure that data transmitted between on-premises networks and Azure virtual networks is secure and protected from unauthorized access. ExpressRoute supports the following encryption options:
>
>    -  **Media Access Control Security (MACsec)** Applied to *ExpressRoute Direct resources*, MACsec enhances ExpressRoute security by providing link-layer encryption and integrity protection, ensuring that data transmitted over ExpressRoute circuits is secure, tamper-proof, and compliant with regulatory requirements.
>
>    -  **Internet Protocol Security (IPsec)** Applied to *ExpressRoute private peering*, IPsec provides secure communication between on-premises networks and Azure virtual networks by encrypting data in transit. By configuring IPsec, you can ensure that data transmitted over ExpressRoute circuits is secure and protected from unauthorized access.
>
> - **Configure MD5 hash on ExpressRoute circuit:** During configuration of private peering or Microsoft peering, apply an MD5 hash to secure messages between the on-premises router and the MSEE routers.
>
> - **Configure Activity log to send logs to archive:** Activity logs are essential for auditing, compliance, incident response, operational visibility, and policy enforcement for ExpressRoute. Configure Activity log to send logs to an archive for long-term retention and analysis.
>

### Configuration recommendations

| Recommendation | Benefit |
|--------|----|
| Leverage [Azure Security Baseline for ExpressRoute](/security/benchmark/azure/baselines/expressroute-security-baseline). This security baseline applies guidance from the Microsoft Cloud Security Benchmark version 1.0 to ExpressRoute. | The content is organized by the security controls defined in the benchmark and includes related guidance specific to ExpressRoute. |
| Implement [Azure Role-Based Access Control (RBAC)](/azure/role-based-access-control/overview) to control who can manage ExpressRoute resources such as ExpressRoute circuits and gateways. | By providing granular access management to resources, you can maintain an inventory of administrative accounts with access to ExpressRoute resources and ensure that only authorized users can perform specific actions. |
| Configure [MACsec for ExpressRoute Direct ports](/azure/expressroute/expressroute-about-encryption). | MACsec (Media Access Control security) enhances security by encrypting data, ensuring data integrity, protecting vulnerable protocols. It secures protocols that are typically not protected on Ethernet links, such as ARP, DHCP, and LACP, thereby preventing potential security threats targeting these protocols. |
| Encrypt traffic using [IPsec (Internet Protocol Security) for ExpressRoute private peering](/azure/expressroute/site-to-site-vpn-over-microsoft-peering) or configure a tunnel using [private peering](/azure/vpn-gateway/site-to-site-vpn-private-peering?toc=%2Fazure%2Fexpressroute%2Ftoc.json). | IPsec encrypts data at the network layer (Layer 3) and enhances security by providing encryption, authentication, integrity protection, and compliance. This ensures that data transmitted over ExpressRoute circuits is secure and protected from unauthorized access and tampering. |
| Configure MD5 hash on ExpressRoute circuit during [configuration of private peering or Microsoft peering](/azure/expressroute/expressroute-howto-routing-portal-resource-manager#prerequisites) to secure messages between the on-premises route and the MSEE routers. | By generating an MD5 hash of the data before transmission and comparing it with the hash generated after reception, you can ensure that the data hasn't been tampered with during transit. |
| Configure [activity logs](/azure/expressroute/monitor-expressroute#azure-activity-log) and send logs an to archive. Data retention is only 90 days and required to be stored in Log Analytics, Event Hubs or a storage account for archive. For more information about Activity logs in ExpressRoute, see [Monitor Azure ExpressRoute](/azure/expressroute/monitor-expressroute). | Activity logs provide insights into operations that were performed at the subscription level for ExpressRoute resources. With Activity logs, you can determine who and when an operation was performed at the control plane.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The Cost Optimization design principles provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to your web apps and the environment in which they run.

### Workload design checklist

Start your design strategy based on the design review checklist for [Cost Optimization](../cost-optimization/checklist.md) for investments and fine tune the design so that the workload is aligned with the budget allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Familiarize yourself with ExpressRoute pricing:** As part of your cost model exercise, estimate the cost of ExpressRoute. Ensure that the options are adequately sized to meet the capacity demand and deliver expected performance without wasting resources.
>
> - **Determine circuit SKU and bandwidth required:** Base your selection of ExpressRoute circuit and virtual network gateway SKU and bandwidth on the capacity demand and performance requirements of your workload.
>
> - **Determine the ExpressRoute virtual network gateway size:** Choose the right size for your ExpressRoute virtual network gateway based on the capacity demand and performance requirements of your workload.
>
> - **Monitor cost and create budget alerts:** Monitor the cost of your ExpressRoute circuit and create alerts for spending anomalies and overspending risks.
>
> - **Deprovision and delete unused ExpressRoute circuits:** Azure Advisor can detect ExpressRoute circuits that have been deployed for a significant time but have a provider status of *Not Provisioned*.

### Configuration recommendations

| Recommendation | Benefit |
|--------|----|
| Familiarize yourself with [ExpressRoute pricing](https://azure.microsoft.com/pricing/details/expressroute/). Use the [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) to estimate the cost. ExpressRoute Direct has a monthly port fee that includes the circuit fee for Local and Standard SKU ExpressRoute circuits. For Premium SKU circuits, there's an additional circuit fee. Outbound data transfer is charged per GB used, depending on the zone number of the peering location. The outbound data charge applies only to Standard and Premium SKUs. For more information, see [plan and manage costs for Azure ExpressRoute](/azure/expressroute/plan-manage-cost). | Understanding ExpressRoute pricing enables better cost management, informed decision-making, avoidance of unexpected charges and maximization of value. |
| Determine [circuit SKU and bandwidth](/azure/expressroute/expressroute-circuit-peerings) required. The way you're charged for your ExpressRoute usage varies between the three different SKU types. With the Local SKU, you're automatically charged with an Unlimited data plan. With the Standard and Premium SKUs, you can choose between a Metered or an Unlimited data plan. All ingress data is free of charge, except when using the Global Reach add-on, which incurs additional costs for data transfer between different geographical locations. It's important to review and [resize your ExpressRoute circuit](/azure/expressroute/about-upgrade-circuit-bandwidth).  | It's important to understand which SKU types and data plan works best for your workload to best optimize cost and budget. |
| Determine the size of the  [ExpressRoute Virtual Network Gateway](/azure/expressroute/expressroute-about-virtual-network-gateways#gwsku). ExpressRoute virtual network gateways are used to pass traffic into a virtual network over private peering. Select the appropriate gateway SKU on your on-premises to Azure workload. Understand ExpressRoute Gateway pricing based on region and type. ExpressRoute Gateways are charged at an hourly rate plus the cost of an ExpressRoute circuit. Configure [scalable ExpressRoute gateways](/azure/expressroute/expressroute-about-virtual-network-gateways#expressroute-scalable-gateway-preview) to set minimum and maximum scale units for the gateway, which auto-scales based on active bandwidth or flow count. See [ExpressRoute pricing](https://azure.microsoft.com/pricing/details/expressroute/) and select ExpressRoute Gateways to see rates for different gateway SKUs. | This benefits you by enabling right-sizing of resources, providing flexibility to scale, optimizing performance, and supporting proactive cost management. This approach ensures that you're using resources efficiently and cost-effectively.|
| [Monitor costs and create budget alerts](/azure/expressroute/plan-manage-cost#monitor-costs). Monitor the cost of your ExpressRoute circuit and create alerts for spending anomalies and overspending risks. | Monitoring and alerts provide you with tools to control spending, enhance financial planning, ensure accountability, and optimize resource usage. |
| [Deprovision and delete unused ExpressRoute circuits](/azure/expressroute/expressroute-howto-circuit-portal-resource-manager#delete). Azure Advisor can detect ExpressRoute circuits that have been deployed for a significant time but have a provider status of *Not Provisioned*. | ExpressRoute circuits are charged from the moment they're created. To reduce unnecessary cost, deprovision the circuit with the service provider and delete the ExpressRoute circuit from your subscription. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational excellence design principles](../devops/principles.md) provide a high-level design strategy for achieving those goals towards the operational requirements of the workload.

### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to ExpressRoute.

> [!div class="checklist"]
>
> - **Choose the closest peering locations:** Choose the closest peering locations to your on-premises network to reduce latency and costs.
>
> - **Configure connection monitoring between your on-premises and Azure network:** Use Connection Monitor to monitor connectivity between your on-premises resources and Azure over the ExpressRoute private peering and Microsoft peering connection.
>
> - **Configure dynamic routing for your Microsoft peering enabled ExpressRoute circuit:** Dynamic routing for ExpressRoute leverages BGP to provide automatic route updates, optimal path selection, scalability, and interoperability for your network.
>
> - **Configure Service Health for receiving notification:** Configure Service Health notifications to alert you when planned and upcoming maintenance is happening to all ExpressRoute circuits in your subscription. For more information on how to integrate with the overall health model for your workload, see [Health modeling for workloads](/azure/well-architected/cross-cutting-guides/health-modeling).
>
> - **Configure Traffic Collector for ExpressRoute:** ExpressRoute Traffic Collector enables the sampling of network flows over your ExpressRoute circuits.
>
> - **Collect, analyze, and visualize metrics and logs:**  Collect metrics and logs as part of the overall [monitoring strategy](/azure/well-architected/operational-excellence/observability) of your solution. Set alerts to proactively notify you when a certain threshold is met. Review metrics and dashboards available through ExpressRoute Insights to view details of your peering components all in a single place.

### Configuration recommendations

| Recommendation | Benefit |
|--------|----|
| Choose the [closest peering locations](/azure/expressroute/expressroute-locations-providers) to your on-premises network to reduce latency and costs.  | By choosing the closest peering location to your on-premises network, you can reduce latency and costs, ensuring optimal performance and cost-effectiveness. |
| Configure [Connection Monitor](/azure/expressroute/how-to-configure-connection-monitor) between your on-premises and Azure network. | Connection Monitor can detect networking issues by identifying where along the network path the problem is and help you quickly resolve configuration or hardware failures. Connection Monitor is part of Azure Monitor logs. |
| Configure [dynamic routing](/azure/expressroute/expressroute-routing#dynamic-route-exchange) your Microsoft peering enabled ExpressRoute circuit. | Dynamic routing allows for more efficient and flexible routing, ensuring optimal path selection and automatic updates to routing tables in response to network changes. |
| Configure [Service Health notifications](/azure/expressroute/maintenance-alerts) to alert you when planned and upcoming maintenance is scheduled for all ExpressRoute circuits in your subscription. Service Health also displays past maintenance events along with Root Cause Analysis (RCA) if unplanned maintenance event occurs. | Service Health notifications provide timely alerts about planned and unplanned maintenance, outages, and early warnings about potential issues. This allows you to stay informed about the status of your ExpressRoute circuits. |
| Configure [Traffic Collector for ExpressRoute](/azure/expressroute/how-to-configure-traffic-collector) | ExpressRoute Traffic Collector enables the sampling of network flows over your ExpressRoute circuits. It supports both Private peering and Microsoft peering, providing near real-time visibility into network throughput and performance. |
| Review metrics with Network Insights. [ExpressRoute Insights with Network Insights](/azure/expressroute/expressroute-network-insights) allow you to review and analyze ExpressRoute circuits, gateways, connections metrics and health dashboards. ExpressRoute Insights also provide a topology view of your ExpressRoute connections where you can view details of your peering components all in a single place. | Network Insights offers a centralized platform to monitor various metrics across ExpressRoute circuits, gateways, and connections, providing a comprehensive view of network health and performance. |
| Review ExpressRoute resource metrics. Use [Azure Monitor](/azure/monitoring-and-diagnostics/monitoring-overview) to collect metrics and create alerts based on your configuration. | Metrics are collected for ExpressRoute circuits, ExpressRoute gateways, ExpressRoute gateway connections, and ExpressRoute Direct. These metrics are useful for diagnosing connectivity problems and understanding the performance of your ExpressRoute connection. |
| Use [auto-assigned Public IP address](/azure/expressroute/expressroute-about-virtual-network-gateways#auto-assigned-public-ip) for ExpressRoute gateway deployment to simplify infrastructure provisioning. ExpressRoute Virtual Network Gateways can automatically receive Public IP addresses during deployment without requiring explicit IP assignment, eliminating manual Public IP assignment requirements across multiple virtual networks and streamlining deployment processes. | Auto-assigned Public IPs reduce configuration complexity and potential deployment failures, eliminate the risk of IP conflicts, ensure consistent gateway provisioning across environments, and reduce operational complexity and deployment time by removing the need to pre-provision IP resources. |
| Review ExpressRoute [metrics and create alerts](/azure/expressroute/expressroute-monitoring-metrics-alerts). ExpressRoute uses Azure Monitor to collect metrics and create alerts based on your configuration. Follow the [recommendations for designing and creating a monitoring system](/azure/well-architected/operational-excellence/observability) to implement your monitoring strategy for ExpressRoute and your workloads.|  Metrics are collected for ExpressRoute circuits, ExpressRoute gateways, ExpressRoute gateway connections, and ExpressRoute Direct. These metrics are useful for diagnosing connectivity problems and understanding the performance of your ExpressRoute connection. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.
The [Performance Efficiency design principles](/azure/well-architected/scalability/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

Start your design strategy based on the design review checklist for Performance Efficiency for defining a baseline based on key performance indicators for Azure ExpressRoute.
Plan for scaling the network to meet the demands of your workloads. Failure to plan for scaling correctly may result in downtime or extra costs. Ensure proper scaling when it comes to both circuit planning and gateway planning.

> [!div class="checklist"]
>
> - **Test ExpressRoute gateway performance to meet work load requirements:** Use the Azure Connectivity Toolkit to test performance across your ExpressRoute circuit to understand bandwidth capacity and latency of your network connection.
>
> - **Plan for scaling:** Based on your scalability requirements, choose the right ExpressRoute circuit SKU and also the Virtual Network Gateway SKUs. Each SKU offers different features and limits. Take into consideration the performance, feature, and routing needs of your network. For additional scalability guidance for your solution, see [Recommendations for optimizing scaling and partitioning](/azure/well-architected/performance-efficiency/scale-partition#configure-scaling).
>
> - **Monitor the performance of ExpressRoute resources:**  Collect and analyze the performance telemetry in accordance with the [WAF Recommendations for collecting performance data](/azure/well-architected/performance-efficiency/collect-performance-data#key-design-strategies). Validate that it meets your performance targets and set up alerts to proactively notify you when a certain threshold is met.

### Configuration recommendations

| Recommendation | Benefit |
|--------|----|
| Test ExpressRoute gateway performances to meet work load requirements with the [Azure Connectivity Toolkit](/azure/expressroute/expressroute-troubleshooting-network-performance#azurect---the-azure-connectivity-toolkit). Schedule bandwidth-intensive operations such as backups and performance testing at times of low production traffic. | The toolkit provides user-friendly tools and interfaces that simplify the process of configuring and managing network connections to Azure. The toolkit includes tools to optimize network performance, ensuring efficient and reliable connectivity to Azure services. |
| Plan for scaling of ExpressRoute circuits. [Upgrade your ExpressRoute circuit bandwidth](/azure/expressroute/expressroute-howto-circuit-portal-resource-manager#upgrade) to meet your production workload requirements. Circuit bandwidth is shared between all virtual networks connected to the ExpressRoute circuit. Depending on your workload, one or more virtual networks can use up all the bandwidth on the circuit. For more information, see [ExpressRoute limits](/azure/expressroute/expressroute-faqs#limits).  | Upgrading the bandwidth ensures that the network can handle increasing data volumes and more users without compromising performance. |
| Plan for scaling of ExpressRoute Virtual Network Gateway. [Upgrade your ExpressRoute Virtual Network Gateway SKU](/azure/expressroute/expressroute-about-virtual-network-gateways) to meet your production workload requirements. | Upgrading to a larger gateway SKU provides higher throughput capabilities, allowing more data to be transferred between on-premises networks and Azure more quickly. A larger gateway can manage more simultaneous connections and higher volumes of traffic, reducing the likelihood of network congestion and bottlenecks. |
| Configure [Scalable Gateways](/azure/expressroute/expressroute-about-virtual-network-gateways#expressroute-scalable-gateway-preview) to automatically scale for performance. | Scalable Gateways allows you to scale up and down automatically with your gateway instances to accommodate performance needs. ErGwScale SKU also enables you to achieve 40-Gbps connectivity to virtual machines and Private Endpoints within the virtual network. |
| Enable [ExpressRoute FastPath](/azure/expressroute/about-fastpath) for higher throughput on your virtual network gateway.  | This feature improves the data path performance between your on-premises network and your virtual network resources by bypassing the gateway. As business needs grow, FastPath provides the necessary bandwidth and performance to support increasing data volumes and more users without compromising performance. Enabling FastPath ensures that the network can handle future expansions and new applications, providing long-term performance efficiency. |
| Monitor [Monitor ExpressRoute circuit, port, and gateway metrics](/azure/expressroute/monitor-expressroute). Configure alerts for ExpressRoute metrics to proactively notify you when a certain threshold is met. [ExpressRoute circuit metrics](/azure/expressroute/monitor-expressroute-reference#supported-metrics-for-microsoftnetworkexpressroutecircuits) supports metrics such as Arp Availability, BitsInPerSecond, DroppedInBitsPerSecond. [ExpressRoute port metrics](/azure/expressroute/monitor-expressroute-reference#supported-metrics-for-microsoftnetworkexpressrouteports) supports metrics such as AdminState, BitsInPerSecond, and FastPathRoutesCount. [ExpressRoute Gateway metrics](/azure/expressroute/monitor-expressroute-reference#supported-metrics-for-microsoftnetworkexpressroutegateways) supports metrics such as Bits In Per Second, Active Flows, and Count Of Routes Advertised to Peer. </br> Monitor performance targets with [Connection Monitor](/azure/network-watcher/connection-monitor-overview). | ExpressRoute circuit, port, and gateway metrics are useful to understand anomalies that can happen with your ExpressRoute connection such as outages and maintenance happening to your ExpressRoute circuits. Connection Monitor can detect networking issues by identifying where along the network path the problem is and help you quickly resolve configuration or hardware failures. |

## Azure Policy

Azure Policy doesn't provide any built-in policies for ExpressRoute, but custom policies can be created to help govern how ExpressRoute circuits should match your desired end state, such as SKU choice, peering type, peering configurations and so on.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [Implement Cloud Adoption Framework enterprise-scale landing zones in Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/implementation).

## Next steps

Consider the following articles as resources that demonstrate the recommendations highlighted in this article.

- We recommend starting with the Cloud Adoption Framework Ready methodology's guidance
  - [Connect to Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/connectivity-to-azure) and [Architect for hybrid connectivity](/azure/architecture/reference-architectures/hybrid-networking/expressroute) with Azure ExpressRoute.
  - [Traditional Azure network topology](/azure/cloud-adoption-framework/ready/azure-best-practices/traditional-azure-networking-topology)
  - [Virtual WAN network topology (Microsoft-managed)](/azure/cloud-adoption-framework/ready/azure-best-practices/virtual-wan-network-topology)

- Use the following product documentation to build your implementation expertise:
  - Configure an [ExpressRoute circuit](/azure/expressroute/expressroute-howto-circuit-portal-resource-manager) or [ExpressRoute Direct port](/azure/expressroute/how-to-expressroute-direct-portal) to establish communication between your on-premises network and Azure.
  - [Azure ExpressRoute product documentation](/azure/expressroute/expressroute-introduction). 
  - [Architect ExpressRoute for Resiliency](/azure/expressroute/design-architecture-for-resiliency) to ensure your setup is robust and reliable.
  - For low-code application architectures, review the guidance on [Enabling ExpressRoute for Power Platform](/power-platform/guidance/expressroute/overview) when planning and configuring ExpressRoute for use with Microsoft Power Platform.

<!-- Updated: August 17, 2025 for Azure Update 498361 -->
