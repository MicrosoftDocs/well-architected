---
title: Reliability and Network connectivity
description: Focuses on the Network connectivity service used in the Networking solution to provide best-practice, design considerations, and configuration recommendations related to Reliability.
author: v-stacywray
ms.author: robbymillsap
ms.date: 01/26/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - network-connectivity
categories:
  - networking
  - management-and-governance
---

# Reliability and Network connectivity

Network connectivity includes three Azure models for private network connectivity:

- VNet injection
- [VNet service endpoints](/azure/virtual-network/virtual-network-service-endpoints-overview)
- [Private Link](/azure/private-link/private-endpoint-overview#private-link-resource)

VNet injection applies to services that are deployed specifically for you, such as:

- Azure Kubernetes Service (AKS) nodes
- SQL Managed Instance
- Virtual Machines

These resources connect directly to your virtual network.

Virtual Network (VNet) service endpoints provide secure and direct connectivity to Azure services. These service endpoints use an optimized route over the Azure network. Service endpoints enable private IP addresses in the VNet to reach the endpoint of an Azure service without needing a public IP address on the VNet.

Private Link provides dedicated access using private IP addresses to Azure PaaS instances, or custom services behind an Azure Load Balancer Standard.

## Design considerations

Network connectivity includes the following design considerations related to a reliable workload:

- Use Private Link, where available, for shared Azure PaaS services. Private Link is generally available for several services and is in public preview for numerous ones.
- Access Azure PaaS services from on-premises through [ExpressRoute](/azure/expressroute/) private peering.
- Use either virtual network injection for dedicated Azure services or Azure Private Link for available shared Azure services. To access Azure PaaS services from on-premises when virtual network injection or Private Link isn't available, use ExpressRoute with Microsoft peering. This method avoids transiting over the public internet.
- Use virtual network service endpoints to secure access to Azure PaaS services from within your virtual network. Use virtual network service endpoints only when Private Link isn't available and there are no concerns with unauthorized movement of data.
- Service Endpoints don't allow a PaaS service to be accessed from on-premises networks. Private Endpoints do.
- To address concerns about unauthorized movement of data with service endpoints, use network-virtual appliance (NVA) filtering. You can also use virtual network service endpoint policies for Azure Storage.
- The following native network security services are fully managed services.  Customers don't incur the operational and management costs associated with infrastructure deployments, which can become complex at scale:

  - Azure Firewall
  - Application Gateway
  - Azure Front Door

- PaaS services are typically accessed over public endpoints. The Azure platform provides capabilities to secure these endpoints or make them entirely private.
- You can also use third-party network-virtual appliances (NVAs) if the customer prefers them for situations where native services don't satisfy specific requirements.

## Checklist

**Have you configured Network connectivity with reliability in mind?**

> [!div class="checklist"]
> - Don't implement forced tunneling to enable communication from Azure to Azure resources.
> - Unless you use network virutal appliance (NVA) filtering, don't use virtual network service endpoints when there are concerns about unauthorized movement of data.
> - Don't enable virtual network service endpoints by default on all subnets.

## Next step

> [!div class="nextstepaction"]
> [Cost optimization and Network connectivity](cost-optimization.md)