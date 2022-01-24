---
title: Application Gateway v2 and reliability
description: Focuses on the Application Gateway v2 service used in the Networking solution to provide best-practice, configuration recommendations, and design considerations related to Reliability.
author: v-stacywray
ms.author: robbymillsap
ms.date: 01/20/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - application-gateway-v2
categories:
  - networking
  - management-and-governance
---

# Application Gateway v2 and reliability

[Application Gateway v2](/azure/application-gateway/) is a web traffic load balancer that enables you to manage traffic to your web applications and is available under a Standard_v2 SKU. The v2 SKU offers performance enhancements and adds support for critical new features such as:

- Autoscaling
- Zone redundancy
- Support for static VIPs

For more information, reference [What is Azure Application Gateway v2?](/azure/application-gateway/overview-v2)

To understand how Application Gateway v2 supports a reliable workload, reference the following topics:

- [Application Gateway high traffic support](/azure/application-gateway/high-traffic-support)
- [Autoscaling and High Availability](/azure/application-gateway/application-gateway-autoscaling-zone-redundant#autoscaling-and-high-availability)

## Checklist

**Have you configured Application Gateway v2 with reliability in mind?**

> [!div class="checklist"]
> - Deploy the instances in a [zone-aware configuration](/azure/application-gateway/application-gateway-autoscaling-zone-redundant), where available.
> - Use Application Gateway with Web Application Firewall (WAF) within an application Virtual Network for protecting inbound `HTTP/S` traffic from the internet.
> - In new deployments, use Application Gateway v2 unless there is a compelling reason to use v1.
> - Deploy at least two AppGateway v2 instances to increase availability.
> - When using Azure Front Door and Application Gateway to protect `HTTP/S` applications, use WAF policies in Front Door and lock down Application Gateway to receive traffic only from Azure Front Door.

## Configuration recommendations

Consider the following recommendation to optimize reliability when configuring Application Gateway v2:

|Recommendation|Description|
|--------------|-----------|
|When using Azure Front Door and Application Gateway to protect `HTTP/S` applications, use WAF policies in Front Door and lock down Application Gateway to receive traffic only from Azure Front Door.|Certain scenarios can force a customer to implement rules specifically on AppGateway: For example, if ModSec CRS `2.2.9`, CRS `3.0` or CRS `3.1` rules are required, these rules can be only implemented on AppGateway. Conversely, rate-limiting and geo-filtering are available only on Azure Front Door, not on AppGateway.|

## Next step

> [!div class="nextstepaction"]
> [Application Gateway v2 and operational excellence](operational-excellence.md)
