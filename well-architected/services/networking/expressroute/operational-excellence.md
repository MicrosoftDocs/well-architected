---
title: Operational excellence and ExpressRoute
description: Focuses on the ExpressRoute service used in the Networking solution to provide best-practice, design considerations, and configuration recommendations related to Operational excellence.
author: v-stacywray
ms.author: robbymillsap
ms.date: 01/28/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - express-route
categories:
  - networking
  - management-and-governance
---

# Operational excellence and ExpressRoute

With [ExpressRoute](/azure/expressroute/), you can create private connections between Microsoft datacenters and infrastructure that's on your premises or in a colocation facility.

Key features include:

- [Layer 3 connectivity](/azure/expressroute/expressroute-introduction#layer-3-connectivity)
- [Redundancy](/azure/expressroute/expressroute-introduction#redundancy)
- [Connectivity to Microsoft cloud services](/azure/expressroute/expressroute-introduction#connectivity-to-microsoft-cloud-services)
- [Connectivity to all regions within a geopolitical region](/azure/expressroute/expressroute-introduction#connectivity-to-all-regions-within-a-geopolitical-region)

For more information on key benefits and features, reference [ExpressRoute FAQ](/azure/expressroute/expressroute-faqs).

To understand how ExpressRoute supports operational excellence, reference the following topics:

- [Monitoring Azure ExpressRoute](/azure/expressroute/monitor-expressroute)
- [Configure Connection Monitor for ExpressRoute](/azure/expressroute/how-to-configure-connection-monitor)
- [ExpressRoute monitoring, metrics, and alerts](/azure/expressroute/expressroute-monitoring-metrics-alerts)

## Design considerations

ExpressRoute includes the following design considerations:

- ExpressRoute Direct allows you to create multiple ExpressRoute circuits at no extra cost, up to the ExpressRoute Direct port capacity (`10G` or `100G`). Direct also allows you to connect directly to ExpressRoute routers.
- For the `100 Gbps` Stock Keeping Unit (SKU), the minimum circuit bandwidth is `5 Gbps`.
- For the `10 Gbps` SKU, the minimum circuit bandwidth is `1 Gbps`.
- [ExpressRoute Global Reach](/azure/expressroute/expressroute-global-reach) (where available) allows customers to connect on-premises locations together using ExpressRoute circuits to transit traffic over the Microsoft backbone network.
- ExpressRoute Global Reach is available in many [ExpressRoute peering locations](/azure/expressroute/expressroute-global-reach#availability).
- When multiple virtual networks are connected to the same ExpressRoute circuit, they'll become part of the same routing domain. All virtual networks will share the bandwidth.
- Private Link can be used to establish connectivity to Platform as a Service (PaaS) services over ExpressRoute with private peering.
- ExpressRoute provides dedicated private connectivity to Microsoft services such as Azure and Office 365 from on-premises locations.

## Checklist

**Have you configured ExpressRoute with operational excellence in mind?**

> [!div class="checklist"]
> - Use Virtual Private Network (VPN) Gateways to connect branches or remote locations to Azure.
> - Enable FastPath to bypass the ExpressRoute Gateway from the data path.
> - Ensure the right SKU is used for the ExpressRoute and VPN Gateways based on bandwidth and performance requirements.
> - When multiple ExpressRoute circuits are used, [optimize routing](/azure/expressroute/expressroute-optimize-routing) by using Border Gateway Protocol (BGP) local preference and AS Path prepending.
> - Use dual ExpressRoute circuits from different peering locations when connecting an on-premises location to virtual networks in Azure.
> - Use ExpressRoute Global Reach to connect large offices, regional headquarters, or datacenters that are connected to Azure through ExpressRoute.
> - Deploy a Zone Redundant ExpressRoute Gateway in the supported Azure regions.
> - For scenarios that require bandwidth higher than `10 Gbps` or dedicated `10/100 Gbps` ports, use ExpressRoute Direct.
> - Don't explicitly use ExpressRoute circuits from a single peering location because it creates a single point of failure, making the customer susceptible to peering location outages.
> - Use ExpressRoute as the primary connectivity channel for connecting an on-premises network to Microsoft Azure.
> - Use different ExpressRoute circuits to ensure isolated routing domains and to mitigate noisy neighbor risks.
> - Proactively monitor ExpressRoute circuits using Network Performance Monitor.

## Configuration recommendations

Consider the following recommendations for operational excellence when configuring ExpressRoute:

|Recommendation|Description|
|--------------|-----------|
|Use Virtual Private Network (VPN) Gateways to connect branches or remote locations to Azure.|For higher resiliency, deploy Zone-Redundant Gateways (where available).|
|Enable FastPath to bypass the ExpressRoute Gateway from the data path.|When very low latency is required, or throughput from on-premises to Azure must be greater than `10 Gbps`.|
|Use dual ExpressRoute circuits from different peering locations when connecting an on-premises location to virtual networks in Azure.|This setup will ensure redundant paths to Azure, removing single points of failure between on-premises and Azure.|
|Use ExpressRoute as the primary connectivity channel for connecting an on-premises network to Microsoft Azure.|VPNs can be used as a source of backup connectivity to enhance connectivity resiliency.|
|Use different ExpressRoute circuits to ensure isolated routing domains and to mitigate noisy neighbor risks.|When traffic isolation or dedicated bandwidth is required, such as for separating production and non-production environments.|

## Next step

> [!div class="nextstepaction"]
> [Reliability and Azure Load Balancer](reliability.md)
