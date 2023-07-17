---
title: Networking resources provisioning
description: Learn about cost strategies for networking design choices for various Azure services, with links to reference architectures.
author: PageWriter-MSFT
ms.author: martinek
ms.date: 04/19/2023
ms.topic: conceptual
ms.custom:
  - article
  - internal-intro
---

# Networking resources provisioning

For design considerations, see [Networking cost estimates](provision-networking.md).

## Azure Front Door

Outbound data transfers, inbound data transfers, and routing rules all affect Azure Front Door billing. Pricing information doesn't include the cost of accessing data from the backend services and transferring it to Azure Front Door. Those costs are billed based on data transfer charges described in [Bandwidth pricing](https://azure.microsoft.com/pricing/details/bandwidth/).

Another consideration is Web Application Firewall (WAF) settings. Adding policies drives up the cost.

For more information, see [Azure Front Door pricing](https://azure.microsoft.com/pricing/details/frontdoor/).

### Azure Front Door reference architecture

[Highly available multi-region web application](/azure/architecture/reference-architectures/app-service-web-app/multi-region) uses Azure Front Door to route incoming requests to the primary region. If the application that runs the primary region becomes unavailable, Azure Front Door fails over to the secondary region.

## Azure Application Gateway

There are two main pricing models:

- Fixed price

  You're charged from the time that you provision the gateway and it becomes available. You're charged for the amount of data that the gateway processes. For more information, see [Application Gateway pricing](https://azure.microsoft.com/pricing/details/application-gateway/).

- Consumption price

  This model applies to v2 SKUs that offer more features such as autoscaling, Azure Kubernetes Service Ingress Controller, and zone redundancy. You're charged based on the consumed capacity units. The capacity units measure the compute resources, persistent connections, and throughput. Consumption price is charged in addition to the fixed price.

For more information, see:

- [Scaling Application Gateway v2 and WAF v2](/azure/application-gateway/application-gateway-autoscaling-zone-redundant)
- [Application Gateway pricing](https://azure.microsoft.com/pricing/details/application-gateway/)

### Application Gateway reference architecture

- [Microservices architecture on Azure Kubernetes Service](/azure/architecture/reference-architectures/containers/aks-microservices/aks-microservices) uses Application Gateway as the ingress controller.

- [Securely managed web applications](/azure/architecture/example-scenario/apps/fully-managed-secure-apps) uses Application Gateway as a web traffic load balancer operating at Layer 7 that manages traffic to the web application. Web Application Firewall is enabled to enhance security.

## Azure ExpressRoute

There are two main pricing models:

- Metered Data plan

  There are two pricing tiers: *Standard* and *Premium*, which is priced higher. The tier pricing is based on the circuit bandwidth.

  If you don't need to access the services globally, choose Standard. With this tier, you can connect to regions within the same zone at no extra cost. Outbound cross-zonal traffic incurs more cost.

- Unlimited Data plan

  All inbound and outbound data transfer is included in the flat rate. There are two pricing tiers: *Standard* and *Premium*, which is priced higher.

Calculate your usage and choose a billing plan. If you exceed about 68% of utilization, we recommend the *Unlimited Data plan*.

For more information, see [Azure ExpressRoute pricing](https://azure.microsoft.com/pricing/details/expressroute).

### ExpressRoute reference architecture

[Connect an on-premises network to Azure using ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute-vpn-failover) connects an Azure virtual network and an on-premises network connected using with VPN gateway failover.

## Azure Firewall

Azure Firewall usage is charged at a fixed rate per deployment hour. There's extra cost for the amount of data transferred.

There aren't extra costs for a firewall deployed in an availability zone. There are extra costs for inbound and outbound data transfers associated with availability zones.

When compared to network virtual appliances (NVAs), with Azure Firewall you can save up to 30-50%. For more information, see [Azure Firewall and network virtual appliances](https://azure.microsoft.com/blog/azure-firewall-and-network-virtual-appliances).

### Azure Firewall reference architecture

- [Hub-spoke network topology in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)
- [Deploy highly available NVAs](/azure/architecture/reference-architectures/dmz/nva-ha)

## Azure Load Balancer

The Load Balancer service distributes inbound traffic according to the configured rules.

There are two tiers: *Basic* and *Standard*. The *Basic* tier is free.

For the *Standard* tier, you're charged only for the number of configured load-balancing and outbound rules. Inbound NAT rules are free. There's no hourly charge for the load balancer when no rules are configured.

For more information, see [Azure Load Balancer pricing](https://azure.microsoft.com/pricing/details/load-balancer/).

### Load Balancer reference architecture

- [Connect an on-premises network to Azure using ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute-vpn-failover): Multiple subnets are connected through Azure load balancers.

- [SAP S/4HANA in Linux on Azure](/azure/architecture/reference-architectures/sap/sap-s4hana): Distribute traffic to virtual machines in the application-tier subnet.

- [Configure ExpressRoute and Site-to-Site coexisting connections](/azure/expressroute/expressroute-howto-coexist-resource-manager): Network traffic from the VPN gateway is routed to the cloud application through an internal load balancer. The load balancer resides in the front-end subnet of the application.

## Azure VPN Gateway

When provisioning a VPN Gateway resource, choose between two gateway types:

- VPN Gateway sends encrypted traffic across the public internet. Site-to-site, point-to-site, and virtual network-to-virtual network connections all use a VPN gateway.
- ExpressRoute gateway sends network traffic on a private connection. Azure ExpressRoute uses this configuration.

For VPN gateway, select *Route-based* or *Policy-based* depending on your VPN device and the kind of VPN connection you want to create. A route-based gateway allows point-to-site, inter-virtual network, or multiple site-to-site connections. Policy-based only allows one site-to-site tunnel. Point-to-site isn't supported. So, route-based VPN is more expensive.

You need to choose the SKU for Route-based VPN:

- For developer and test workloads, use *Basic*.
- For production workloads, an appropriate *Generation1* or *Generation2* SKU.

Each SKU has a range and pricing depends on the type of VPN gateway. Each type offers different levels of bandwidth, site-to-site, and point-to-site tunnel options. Some of those types also offer availability zones, which are more expensive. If you need higher bandwidth, consider Azure ExpressRoute.

VPN Gateway can be the cost driver in a workload because charges are based on the amount of time that the gateway is provisioned and available.

All inbound traffic is free. All outbound traffic is charged as per the bandwidth of the VPN type. Bandwidth also varies depending on the billing zone.

For more information, see:

- [Hybrid connectivity](./provision-networking.md#hybrid-connectivity)
- [VPN Gateway pricing](https://azure.microsoft.com/pricing/details/vpn-gateway/)
- [Traffic across billing zones and regions](./design-regions.md#traffic-across-billing-zones-and-regions)
- [Bandwidth pricing](https://azure.microsoft.com/pricing/details/bandwidth/)

### VPN Gateway reference architecture

- [Configure ExpressRoute and Site-to-Site coexisting connections](/azure/expressroute/expressroute-howto-coexist-resource-manager) connects the virtual network to the on-premises network through a VPN device.

## Azure Traffic Manager

Azure Traffic Manager uses DNS to route and load balanced traffic to service endpoints in different Azure regions. An important use case is disaster recovery. In a workload, you can use Azure Traffic Manager to route incoming requests to the primary region. If that region becomes unavailable, Azure Traffic Manager fails over to the secondary region. There are other features that can make the application highly responsive and available. Those features cost money.

- Determine the best web app to handle request based on geographic location.
- Configure caching to reduce the response time.

Azure Traffic Manager isn't charged for bandwidth consumption. Billing is based on the number of DNS queries received, with a discount for services receiving more than 1 billion monthly queries. You're also charged for each monitored endpoint.

### Azure Traffic Manager reference architecture

[Multi-region N-tier application](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server) uses Azure Traffic Manager to route incoming requests to the primary region. If that region becomes unavailable, Azure Traffic Manager fails over to the secondary region. For more information, see the section [Traffic Manager configuration](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server#traffic-manager-configuration).

### DNS query charges

Azure Traffic Manager uses DNS to direct clients to specific services.

DNS queries that reach Azure Traffic Manager are charged in million query units.

Not all DNS queries reach Azure Traffic Manager. Recursive DNS servers run by enterprises and ISPs first attempt to resolve the query by using cached DNS responses. Those servers query Azure Traffic Manager at a regular interval to get updated DNS entries. That interval value or time to live (TTL) is configurable in seconds.

TTL can affect cost. A longer TTL value increases the amount of caching and reduces DNS query charges. Conversely, shorter a TTL value results in more queries.

Increased caching also affects how often the endpoint status refreshes. For example, the user failover times, for an endpoint failure, becomes longer.

### Health monitoring charges

When Azure Traffic Manager receives a DNS request, it chooses an available endpoint based on configured state and health of the endpoint. To do this, Azure Traffic Manager continually monitors the health of each service endpoint.

The number of monitored endpoints are charged. You can add endpoints for services hosted in Azure and then add on endpoints for services hosted on-premises or with a different hosting provider. The external endpoints are more expensive, but health checks can provide high-availability applications that are resilient to endpoint failure, including Azure region failures.

### Real User Measurement charges

Real User Measurements evaluates network latency from the client applications to Azure regions. That influences Azure Traffic Manager to select the best Azure region in which the application is hosted. The number of measurements sent to Azure Traffic Manager is billed.

### Traffic View charges

By using Traffic View, you can get insight into the traffic patterns where you have endpoints. The charges are based on the number of data points used to create the insights presented.

## Azure Virtual Network

Azure Virtual Network is free. You can create up to 50 virtual networks across all regions within a subscription. Here are a few considerations:

- Inbound and outbound data transfers are charged per the billing zone. Traffic that moves across regions and billing zones is more expensive. For more information, see:

  - [Traffic across zones](./design-regions.md#traffic-across-billing-zones-and-regions)
  - [Bandwidth pricing](https://azure.microsoft.com/pricing/details/bandwidth/)

- Virtual Network peering has an extra cost. Peering within the same region is cheaper than peering between regions or Global regions. Inbound and outbound traffic is charged at both ends of the peered networks. For more information, see [Peering](./provision-networking.md#peering).

- Managed services don't always need a virtual network. The cost of networking is included in the service cost.
