---
title: API Management and reliability
description: Focuses on the API Management service used in the Networking solution to provide best-practice and configuration recommendations related to Reliability.
author: martinekuan
ms.author: martinek
ms.date: 01/24/2022
ms.topic: conceptual
products:
  - azure-api-management
categories:
  - networking
  - management-and-governance
---

# API Management and reliability

Learn how to use [API Management](/azure/api-management/) to publish APIs to external, partner, and employee developers securely and at scale. This networking service is a hybrid, multicloud management platform for APIs across all environments.

Components include:

- [API gateway](/azure/api-management/api-management-key-concepts#api-gateway)
- [Management plane](/azure/api-management/api-management-key-concepts#management-plane)
- [Developer portal](/azure/api-management/api-management-key-concepts#developer-portal)

For more information, reference [About API Management](/azure/api-management/api-management-key-concepts).

To understand how API Management can increase reliability for your workload, reference the following topics:

- [Availability zone support for Azure API Management](/azure/api-management/zone-redundancy)
- [How to deploy an Azure API Management service instance to multiple Azure regions](/azure/api-management/api-management-howto-deploy-multi-region)
- [How to implement disaster recovery using service backup and restore in Azure API Management](/azure/api-management/api-management-howto-disaster-recovery-backup-restore)

## Checklist

**Have you configured API Management with reliability in mind?**

> [!div class="checklist"]
> - [Secure the communication](/azure/api-management/api-management-faq#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services) between API Management and your backend.
> - Ensure that each party has its own credential when exposing APIs to third parties.
> - Ensure you set quotas and rate limits when exposing APIs to third parties.
> - Evaluate the need for response caching.
> - Plan a backup and restore process for your API Management instance.
> - Configure multiple Azure regions in your API Management service.
> - Implement a strategy to ensure availability during an outage or disaster affecting an Azure region.

## Configuration recommendations

Consider the following recommendations to optimize reliability when configuring your API Management service:

|Recommendation|Description|
|--------------|-----------|
|Ensure you set quotas and rate limits when exposing APIs to third parties.|Protect backend services and reduce the load placed on an API Management scale unit. Rate limiting policies can be applied at Global, Product, API, and Operation levels to provide rate limit customization applied to API consumers.|
|Evaluate the need for response caching.|Response caching can reduce API latency and bandwidth consumption. Response caching reduces the load placed on the backend APIs leading to improved performance, user experience, and reduced solution cost.|
|Plan a backup and restore process for your API Management instance.|Consider taking regular backups of your API Management service so that you can easily restore it in another region. Your recovery time objective may require that a standby is deployed in a secondary region. It is a good practice to take regular backups to recreate the service due to unforeseen loss or misconfiguration of the service. Regular backups allow you to replicate changes between your primary and standby instances.|
|Configure multiple Azure regions in your API Management service.|Configure your API Management service with multiple regions to provide high-availability support in case an Azure region experiences downtime or a disaster scenario. Configuring multiple regions also reduces API call latency because calls can be routed to the nearest region.|
|Implement a strategy to ensure availability during an outage or disaster affecting an Azure region.|Consider using Azure Traffic Manager, Azure Front Door, or Azure DNS to enable access to multiple regional deployments of API Management. Using these services ensures you can still serve requests due to an outage or disaster. Requirements include syncing configurations between these individual Standard instances.|

## Next step

> [!div class="nextstepaction"]
> [API Management and cost optimization](cost-optimization.md)