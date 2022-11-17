---
title: Operational excellence and Azure Virtual Network
description: Focuses on the Azure Virtual Network service used in the Networking solution to provide best-practice, design considerations, and configuration recommendations related to Operational excellence.
author: v-stacywray
ms.author: martinekuan
ms.date: 01/27/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - azure-virtual-network
categories:
  - networking
  - management-and-governance
---

# Operational excellence and Azure Virtual Network

A fundamental building block for your private network, [Azure Virtual Network](/azure/virtual-network/) enables Azure resources to securely communicate with each other, the internet, and on-premises networks.

Key features of Azure Virtual Network include:

- [Communication with Azure resources](/azure/virtual-network/virtual-networks-overview#communicate-between-azure-resources)
- [Communication with the internet](/azure/virtual-network/virtual-networks-overview#communicate-with-the-internet)
- [Communication with on-premises resources](/azure/virtual-network/virtual-networks-overview#communicate-with-on-premises-resources)
- [Network traffic filtering](/azure/virtual-network/virtual-networks-overview#filter-network-traffic)

For more information, reference [What is Azure Virtual Network?](/azure/virtual-network/virtual-networks-overview)

To understand how Azure Virtual Network supports operational excellence, reference the following topics:

- [Monitoring Azure Virtual Network](/azure/virtual-network/monitor-virtual-network)
- [Monitoring Azure Virtual Network data reference](/azure/virtual-network/monitor-virtual-network-reference#resource-logs)
- [Azure Virtual Network concepts and best practices](/azure/virtual-network/concepts-and-best-practices)

## Design considerations

The Virtual Network (VNet) includes the following design considerations for operational excellence:

- Overlapping IP address spaces across on-premises and Azure regions creates major contention challenges.
- While a Virtual Network address space can be added after creation, this process requires an outage if the Virtual Network is already connected to another Virtual Network through peering. An outage is necessary because the Virtual Network peering is deleted and re-created.
- Resizing of peered Virtual Networks is in [public preview](https://azure.microsoft.com/blog/how-to-resize-azure-virtual-networks-that-are-peered-now-in-preview/) (August 20, 2021).
- Some Azure services do require [dedicated subnets](/azure/virtual-network/virtual-network-for-azure-services#services-that-can-be-deployed-into-a-virtual-network), such as:
  - Azure Firewall
  - Azure Bastion
  - Virtual Network Gateway
- Subnets can be delegated to certain services to create instances of that service within the subnet.
- Azure reserves five IP addresses within each subnet, which should be factored in when sizing Virtual Networks and encompassed subnets.

## Checklist

**Have you configured Azure Virtual Network with operational excellence in mind?**

> [!div class="checklist"]
> - Use Azure DDoS Standard Protection Plans to protect all public endpoints hosted within customer Virtual Networks.
> - Enterprise customers must plan for IP addressing in Azure to ensure there's no overlapping IP address space across considered on-premises locations and Azure regions.
> - Use IP addresses from the address allocation for private internets (Request for Comment (RFC) 1918).
> - For environments with limited private IP addresses (RFC 1918) availability, consider using IPv6.
> - Don't create unnecessarily large Virtual Networks (for example: `/16`) to ensure there's no unnecessary waste of IP address space.
> - Don't create Virtual Networks without planning the required address space in advance.
> - Don't use public IP addresses for Virtual Networks, especially if the public IP addresses don't belong to the customer.
> - Use VNet Service Endpoints to secure access to Azure Platform as a Service (PaaS) services from within a customer VNet.
> - To address data exfiltration concerns with Service Endpoints, use Network Virtual Appliance (NVA) filtering and VNet Service Endpoint Policies for Azure Storage.
> - Don't implement forced tunneling to enable communication from Azure to Azure resources.
> - Access Azure PaaS services from on-premises through ExpressRoute Private Peering.
> - To access Azure PaaS services from on-premises networks when VNet injection or Private Link aren't available, use ExpressRoute with Microsoft Peering when there are no data exfiltration concerns.
> - Don't replicate on-premises perimeter network (also known as DMZ, demilitarized zone, and screened subnet) concepts and architectures into Azure.
> - Ensure the communication between Azure PaaS services that have been injected into a Virtual Network is locked down within the Virtual Network using user-defined routes (UDRs) and network security groups (NSGs).
> - Don't use VNet Service Endpoints when there are data exfiltration concerns, unless NVA filtering is used.
> - Don't enable VNet Service Endpoints by default on all subnets.

## Configuration recommendations

Consider the following recommendations for operational excellence when configuring an Azure Virtual Network:

|Recommendation|Description|
|--------------|-----------|
|Don't create Virtual Networks without planning the required address space in advance.|Adding address space will cause an outage once a Virtual Network is connected through Virtual Network peering.|
|Use VNet Service Endpoints to secure access to Azure Platform as a Service (PaaS) services from within a customer VNet.|Only when Private Link isn't available and when there are no data exfiltration concerns.|
|Access Azure PaaS services from on-premises through ExpressRoute Private Peering.|Use either VNet injection for dedicated Azure services or Azure Private Link for available shared Azure services.|
|To access Azure PaaS services from on-premises networks when VNet injection or Private Link aren't available, use ExpressRoute with Microsoft Peering when there are no data exfiltration concerns.|Avoids transit over the public internet.|
|Don't replicate on-premises perimeter network (also known as DMZ, demilitarized zone, and screened subnet) concepts and architectures into Azure.|Customers can get similar security capabilities in Azure as on-premises, but the implementation and architecture will need to be adapted to the cloud.|
|Ensure the communication between Azure PaaS services that have been injected into a Virtual Network is locked down within the Virtual Network using user-defined routes (UDRs) and network security groups (NSGs).|Azure PaaS services that have been injected into a Virtual Network still perform management plane operations using public IP addresses.|

## Next step

> [!div class="nextstepaction"]
> [Reliability and ExpressRoute](reliability.md)