---
title: Cost optimization and Network Virtual Appliances (NVA)
description: Focuses on the Network Virtual Appliances service used in the Networking solution to provide design considerations related to Cost optimization.
author: martinekuan
ms.author: martinek
ms.date: 01/26/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - network-virtual-appliances
categories:
  - networking
  - management-and-governance
---

# Cost optimization and Network Virtual Appliances (NVA)

Network Virtual Appliances (NVA) are typically used to control the flow of traffic between network segments classified with different security levels, for example between a perimeter network (also known as DMZ, demilitarized zone, and screened subnet) and the public internet.

Examples of NVAs include:

- Network firewalls
- Layer-4 reverse-proxies
- Internet Protocol Security (IPsec) Virtual Private Network (VPN) endpoints
- Web-based reverse-proxies
- Internet proxies
- Layer-7 load balancers

For more information about Network Virtual Appliances, reference [Deploy highly available NVAs](/azure/architecture/reference-architectures/dmz/nva-ha?tabs=cli).

## Design considerations

When deploying a Network Virtual Appliance (NVA), keep in mind the following design considerations:

- There's a difference between using a third-party app (NVA) and using an Azure native service (Firewall or Application Gateway).
- With managed Platform as a Service (PaaS) services such as Azure Firewall or Application Gateway, Microsoft handles the management of the service and the underlying infrastructure. Using NVAs, which usually have to be deployed on Virtual Machines or Infrastructure as a Service (IaaS), the customer has to handle the management operations (such as patching and updating) of that Virtual Machine and the appliance on top. Managing third-party services also involves using specific vendor tools making integration difficult.

## Next step

> [!div class="nextstepaction"]
> [Operational excellence and Network Virtual Appliances (NVA)](operational-excellence.md)
