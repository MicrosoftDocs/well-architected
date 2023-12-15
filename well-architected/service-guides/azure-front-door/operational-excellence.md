---
title: Operational excellence and Azure Front Door
description: Focuses on the Azure Front Door service used in the Networking solution to provide best-practice and configuration recommendations related to Operational excellence.
author: martinekuan
ms.author: martinek
ms.date: 01/25/2022
ms.topic: conceptual
products:
  - azure-front-door
categories:
  - networking
  - management-and-governance
---

# Operational excellence and Azure Front Door

A scalable and secure entry point, [Azure Front Door](/azure/frontdoor/) provides fast delivery of your global web applications. Front Door uses the Microsoft global edge network to create fast, secure, and widely scalable web applications.

Key features include:

- Accelerated application performance by using split TCP-based anycast protocol.
- Intelligent health probe monitoring for backend resources.
- URL-path based routing for requests.
- Enables hosting of multiple websites for efficient application infrastructure.
- Cookie-based session affinity.

For more key features and information, reference [Why use Azure Front Door?](/azure/frontdoor/front-door-overview#why-use-azure-front-door)

To understand how Azure Front Door supports operational excellence, reference the following topics:

- [How Front Door determines backend health](/azure/frontdoor/front-door-health-probes#how-front-door-determines-backend-health)
- [Monitoring metrics and logs in Azure Front Door](/azure/frontdoor/front-door-diagnostics)
- [Front Door Standard/Premium (Preview) Logging](/azure/frontdoor/standard-premium/how-to-logs)

## Checklist

**Have you configured Azure Front Door with operational excellence in mind?**

> [!div class="checklist"]
> - Use Web Application Firewall (WAF) policies in Front Door. Lock down Application Gateway to receive traffic only from Azure Front Door when using Azure Front Door and Application Gateway to protect `HTTP/S` applications.
> - Use Azure Front Door Web Application Firewall (WAF) policies to provide global protection across Azure regions for inbound `HTTP/S` connections to a *Landing Zone*.
> - Create a rule to block access to the health endpoint from the internet.
> - Ensure that the connection to the back-end is re-encrypted.

## Configuration recommendations

Consider the following recommendation to optimize operational excellence when configuring Azure Front Door:

|Recommendation|Description|
|--------------|-----------|
|Use Web Application Firewall (WAF) policies in Front Door. Lock down Application Gateway to receive traffic only from Azure Front Door when using Azure Front Door and Application Gateway to protect `HTTP/S` applications.|Certain scenarios can force a customer to implement rules specifically on AppGateway: For example, if ModSec Core Rule Set (CRS) `2.2.9`, CRS `3.0`, or CRS `3.1` rules are required, rules can be only implemented on AppGatway. Rate-limiting and geo-filtering are available only on Azure Front Door, not on AppGateway. Instructions on how to lock down traffic can be found at [Frequently asked questions for Azure Front Door](/azure/frontdoor/front-door-faq#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door)|
|Ensure that the connection to the back-end is re-encrypted.|Front Door doesn't support SSL passthrough. Front Door must hold the certificate to terminate the encrypted inbound connection.|

## Next step

> [!div class="nextstepaction"]
> [Reliability and Network Virtual Appliances](../network-virtual-appliances/reliability.md)
