---
title: Reliability and Network Virtual Appliances (NVA)
description: Focuses on the Network Virtual Appliances service used in the Networking solution to provide best-practice and configuration recommendations related to Reliability.
author: martinekuan
ms.author: martinek
ms.date: 01/26/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - azure-virtual-network
categories:
  - networking
  - management-and-governance
---

# Reliability and Network Virtual Appliances (NVA)

Network Virtual Appliances (NVA) are typically used to control the flow of traffic between network segments classified with different security levels, for example between a perimeter network (also known as DMZ, demilitarized zone, and screened subnet) and the public internet.

Examples of NVAs include:

- Network firewalls
- Layer-4 reverse-proxies
- Internet Protocol Security (IPsec) Virtual Private Network (VPN) endpoints
- Web-based reverse-proxies
- Internet proxies
- Layer-7 load balancers

For more information about Network Virtual Appliances, reference [Deploy highly available NVAs](/azure/architecture/reference-architectures/dmz/nva-ha?tabs=cli).

To understand how NVAs support a reliable workload, reference the following topics:

- [Scenario: Route traffic through an NVA](/azure/virtual-wan/scenario-route-through-nva)
- [Scenario: Route traffic through NVAs by using custom settings](/azure/virtual-wan/scenario-route-through-nvas-custom)
- [Use L7 load balancers](/azure/architecture/reference-architectures/dmz/nva-ha?tabs=cli#using-l7-load-balancers)

## Checklist

**Have you configured your Network Virtual Appliances (NVA) with reliability in mind?**

> [!div class="checklist"]
> - NVAs should be deployed within a *Landing Zone* or *solution-level* Virtual Network.
> - For Virtual Wide Area Network (VWAN) topologies, deploy the NVAs to a separate Virtual Network (such as, NVA VNet). Connect the NVA to the regional Virtual WAN Hub and to the *Landing Zones* that require access to NVAs.
> - For non-Virtual Wide Are Network (WAN) topologies, deploy the third-party NVAs in the central Hub Virtual Network (VNet).

## Configuration recommendations

Consider the following recommendations to optimize reliability when configuring your Network Virtual Appliances (NVA):

|Recommendation|Description|
|--------------|-----------|
|NVAs should be deployed within a *Landing Zone* or *solution-level* Virtual Network.|If third-party NVAs are required for inbound `HTTP/S` connections, deploy NVAs together with the applications that they're protecting and exposing to the internet.|
|For Virtual Wide Area Network (VWAN) topologies, deploy the NVAs to a separate Virtual Network (such as, NVA VNet). Connect the NVA to the regional Virtual WAN Hub and to the *Landing Zones* that require access to NVAs.|If third-party NVAs are required for east-west or south-north traffic protection and filtering, reference [Scenario: Route traffic through an NVA](/azure/virtual-wan/scenario-route-through-nva).|
|For non-Virtual Wide Area Network (WAN) topologies, deploy the third-party NVAs in the central Hub Virtual Network (VNet).|If third-party NVAs are required for east-west or south-north traffic protection and filtering, deploy the third-party NVAs in the central Hub Virtual Network.|

## Next step

> [!div class="nextstepaction"]
> [Cost optimization and Network Virtual Appliances (NVA)](cost-optimization.md)
