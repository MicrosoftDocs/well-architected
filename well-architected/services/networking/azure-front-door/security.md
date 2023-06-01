---
title: Security and Azure Front Door
description: Focuses on the Azure Front Door service used in the Networking solution to provide best-practice and configuration recommendations related to Security.
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

# Security and Azure Front Door

A scalable and secure entry point, [Azure Front Door](/azure/frontdoor/) provides fast delivery of your global web applications. Front Door uses the Microsoft global edge network to create fast, secure, and widely scalable web applications.

Key features include:

- Accelerated application performance by using split TCP-based anycast protocol.
- Intelligent health probe monitoring for backend resources.
- URL-path based routing for requests.
- Enables hosting of multiple websites for efficient application infrastructure.
- Cookie-based session affinity.

For more key features and information, reference [Why use Azure Front Door?](/azure/frontdoor/front-door-overview#why-use-azure-front-door)

To understand how Azure Front Door creates a more secure workload, reference the following topics:

- [Security baseline for Azure Front Door](/security/benchmark/azure/baselines/front-door-security-baseline?toc=/azure/frontdoor/TOC.json)
- [DDoS protection on Front Door](/azure/frontdoor/front-door-ddos)
- [End-to-end Transport Layer Security (TLS) with Azure Front Door](/azure/frontdoor/concept-end-to-end-tls)

## Checklist

**Have you configured Azure Front Door with security in mind?**

> [!div class="checklist"]
> - Consider using geo-filtering in Azure Front Door.

## Configuration recommendations

Consider the following recommendation to optimize security when configuring Azure Front Door:

|Recommendation|Description|
|--------------|-----------|
|Consider using geo-filtering in Azure Front Door.|The Front Door service responds to user requests regardless of the location of the user making the request.|

## Next step

> [!div class="nextstepaction"]
> [Operational excellence and Azure Front Door](operational-excellence.md)