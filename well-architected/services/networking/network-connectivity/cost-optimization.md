---
title: Cost optimization and Network Connectivity
description: Focuses on the Network Connectivity service used in the Networking solution to provide best-practice, design considerations, and configuration recommendations related to Cost optimization.
author: martinekuan
ms.author: martinek
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

# Cost optimization and Network connectivity

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

Network connectivity includes the following design considerations related to cost optimization:

- Running cost of services: The services are metered. Pay for service itself and consumption on service.
- VNet Peering cost: Consider the consequences of putting all resources in a single VNet to save costs. It also prevents the infrastructure from growing. The VNet can eventually reach a point where new resources don't fit anymore.
- For two peered VNets using a private endpoint: Only the private endpoint access is billed and not the VNet peering cost.
- Azure Firewall is also metered: Pay for the instance and for usage. The same applies to load balancers.

## Checklist

**Have you configured Network connectivity with cost optimization in mind?**

> [!div class="checklist"]
> - Select SKU for service so that it does the job required, which allows the customer to grow as the workload evolves.
> - For the Load balancer, select two SKUs: Basic (free) and Standard (paid).
> - For App Gateway, select Basic or V2.
> - For Gateways, limit throughput and performance.
> - Select DDoS Standard.

## Configuration recommendations

Consider the following recommendation for cost optimization when configuring Network connectivity:

|Recommendation|Description|
|--------------|-----------|
|For the Load balancer, select two SKUs: Basic (free) and Standard (paid).|Microsoft recommends Standard because it has richer capabilities, such as: <br>- Outbound rules <br>- Granular network security configuration <br>- Monitoring <br> Standard provides a Service Level Agreement (SLA) and can be deployed in Availability Zones. Capabilities in Basic are limited.|
|Select DDoS Standard.|Depending on the workload and usage patterns, Standard can provide useful protection. Otherwise, you can use Basic for small customers.|

## Next step

> [!div class="nextstepaction"]
> [Operational excellence and Network connectivity](operational-excellence.md)
