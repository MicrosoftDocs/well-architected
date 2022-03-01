---
title: Operational excellence and Azure Load Balancer
description: Focuses on the Azure Load Balancer service used in the Networking solution to provide best-practice and configuration recommendations related to Operational excellence.
author: v-stacywray
ms.author: robbymillsap
ms.date: 01/28/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - azure-load-balancer
categories:
  - networking
  - management-and-governance
---

# Operational excellence and Azure Load Balancer

*Load balancing* refers to evenly distributing load (incoming network traffic) across a group of backend resources or servers. With [Azure Load Balancer](/azure/load-balancer/), load-balance traffic to and from virtual machines and cloud resources, and in cross-premises virtual networks.

You can scale your applications and create highly available services with Azure Load Balancer. It supports both inbound and outbound scenarios. Load balancer provides low latency and high throughput.

Key benefits include:

- Load balance internal and external traffic to Azure virtual machines.
- Increase availability by distributing resources within and across zones.
- Configure outbound connectivity for Azure virtual machines.
- Use health probes to monitor load-balanced resources.

For more information, reference [Why use Azure Load Balancer?](/azure/load-balancer/load-balancer-overview#why-use-azure-load-balancer)

To understand how Azure Load Balancer supports operational excellence, reference the following topics:

- [Load Balancer health probes](/azure/load-balancer/load-balancer-custom-probe-overview)
- [Standard load balancer diagnostics with metrics, alerts, and resource health](/azure/load-balancer/load-balancer-standard-diagnostics)
- [Using Insights to monitor and configure your Azure Load Balancer](/azure/load-balancer/load-balancer-insights)

## Checklist

**Have you configured Azure Load Balancer with operational excellence in mind?**

> [!div class="checklist"]
> - For production workloads, use the Standard Stock Keeping Units (SKU).

## Configuration recommendations

Consider the following recommendation for operational excellence when configuring an Azure Load Balancer:

|Recommendation|Description|
|--------------|-----------|
|For production workloads, use the Standard Stock Keeping Units (SKU).|Basic load balancers don't have a Service Level Agreement (SLA). The Standard SKU [supports Availability Zones](/azure/load-balancer/load-balancer-standard-availability-zones).|

## Next step

> [!div class="nextstepaction"]
> [Reliability and Traffic Manager](../traffic-manager/reliability.md)
