---
title: Reliability and Azure Front Door
description: Focuses on the Azure Front Door service used in the Networking solution to provide best-practice and configuration recommendations related to Reliability.
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

# Reliability and Azure Front Door

A scalable and secure entry point, [Azure Front Door](/azure/frontdoor/) provides fast delivery of your global web applications. Front Door uses the Microsoft global edge network to create fast, secure, and widely scalable web applications.

Key features include:

- Accelerated application performance by using split TCP-based anycast protocol.
- Intelligent health probe monitoring for backend resources.
- URL-path based routing for requests.
- Enables hosting of multiple websites for efficient application infrastructure.
- Cookie-based session affinity.

For more key features and information, reference [Why use Azure Front Door?](/azure/frontdoor/front-door-overview#why-use-azure-front-door)

To understand how Azure Front Door creates a more reliable workload, reference the following topics:

- [Selecting the Front Door environment for traffic routing (Anycast)](/azure/frontdoor/front-door-routing-architecture#selecting-the-front-door-environment-for-traffic-routing-anycast)
- [Front Door routing methods](/azure/frontdoor/front-door-routing-methods)

## Checklist

**Have you configured Azure Front Door with reliability in mind?**

> [!div class="checklist"]
> - Use Azure Front Door Web Application Firewall (WAF) policies in Front Door. Lock down Application Gateway to receive traffic only from Azure Front Door when using Azure Front Door and Application Gateway to protect `HTTP/S` applications.
> - Use WAF policies to provide global protection across Azure regions for inbound `HTTP/S` connections to a *landing zone*.
> - Create a rule to block access to the health endpoint from the internet.
> - Ensure that the connection to the back-end is re-encrypted.
> - Evaluate the four traffic routing configurations in Azure Front Door.

## Configuration recommendations

Consider the following recommendation to optimize reliability when configuring Azure Front Door:

|Recommendation|Description|
|--------------|-----------|
|Use WAF policies in Front Door. Lock down Application Gateway to receive traffic only from Azure Front Door when using Azure Front Door before Application Gateway to protect `HTTP/S` applications.|Certain scenarios can force a customer to implement rules specifically on Application Gateway. For example, if ModSec Core Rule Set (CRS) `2.2.9`, CRS `3.0`, or CRS `3.1` rules are required, rules can be only implemented on Application Gateway WAF. Rate-limiting and geo-filtering custom WAF rules are available only on Azure Front Door WAF, not on Application Gateway WAF. Instructions to lock down traffic on Azure Front Door can be found at [Frequently asked questions for Azure Front Door](/azure/frontdoor/front-door-faq#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-).|
|Ensure that the connection to the back-end is re-encrypted.|Front Door doesn't support SSL passthrough. Front Door must hold the certificate to terminate the encrypted inbound connection. For more information, see [End-to-end TLS encryption](/azure/frontdoor/end-to-end-tls?pivots=front-door-standard-premium#end-to-end-tls-encryption). |
|Evaluate the four traffic routing methods in Azure Front Door.|The Front Door service supports various traffic-routing methods to determine how to route your `HTTP/HTTPS` traffic to the various service endpoints. For more information on traffic routing methods, see [Front Door routing methods](/azure/frontdoor/routing-methods).|

## Next step

> [!div class="nextstepaction"]
> [Security and Azure Front Door](security.md)
