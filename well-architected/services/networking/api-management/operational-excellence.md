---
title: API Management and operational excellence
description: Focuses on the API Management service used in the Networking solution to provide best-practice and configuration recommendations related to Operational excellence.
author: v-stacywray
ms.author: robbymillsap
ms.date: 01/25/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - api-management
categories:
  - networking
  - management-and-governance
---

# API Management and operational excellence

Learn how to use [API Management](/azure/api-management/) to publish APIs to external, partner, and employee developers securely and at scale. This networking service is a hybrid, multicloud management platform for APIs across all environments.

Components include:

- [API gateway](/azure/api-management/api-management-key-concepts#api-gateway)
- [Management plane](/azure/api-management/api-management-key-concepts#management-plane)
- [Developer portal](/azure/api-management/api-management-key-concepts#developer-portal)

For more information, reference [About API Management](/azure/api-management/api-management-key-concepts).

To understand how API Management supports operational excellence, reference the following topics:

- [Managing Azure API Management using Azure Automation](/azure/api-management/automation-manage-api-management)
- [Observability in Azure API Management](/azure/api-management/observability)

## Checklist

**Have you configured API Management with operational excellence in mind?**

> [!div class="checklist"]
> - [Secure the communication](/azure/api-management/api-management-faq#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services) between API Management and your backend.
> - Ensure that each party has its own credential when exposing APIs to third parties.
> - Ensure you set quotas and rate limits when exposing APIs to third parties.
> - Understand the Microsoft REST API design and architecture guidance.
> - Enable versioning of APIs to maintain backwards compatibility while adding other features.
> - Use the API Management Versioning and Revisions features to implement API versioning.
> - Understand the API import restrictions in API Management.
> - Understand the Event logging feature.
> - Trace calls in Azure API Management to help with debugging and testing.
> - Configure logging using Azure Monitor for the API Management service.
> - Choose the right modes to access private site connections.
> - Evaluate firewall rules and IP allowlists based on the API Management public IP address.

## Configuration recommendations

Consider the following recommendations for operational excellence when configuring your API Management service:

|Recommendation|Description|
|--------------|-----------|
|Ensure you set quotas and rate limits when exposing APIs to third parties.|Protect backend services and reduce the load placed on an API Management scale unit. Rate limiting policies can be applied at Global, Product, API, and Operation levels to provide rate limit customization applied to API consumers.|
|Understand the Microsoft REST API design and architecture guidance.|Follow standards and best practices when using the REST API. Following best practices enables maximum compatibility across platforms and implementations. Review the REST API Guidelines and API Design guidance.|
|Understand the API import restrictions in API Management.|Every effort is made to ensure the API import process runs smoothly, which includes requiring no customizations. Some scenarios impose restrictions that will require modification to the import source. Applies to both REST and SOAP services. Reference [Policy Restrictions](/rest/api/policy/policy-restrictions) for the current API Import restrictions.|
|Understand the Event logging feature.|Supports event logging to an Azure event hub to perform near real-time analysis. This feature integrates with external logging, security information and event management (SIEM) solutions, or analyzing API usage in near real time.|
|Trace calls in Azure API Management to help with debugging and testing.|Tracing must be enabled on the subscription used to make the request. Tracing is enabled on a request-by-request basis using the [Ocp-Apim-Trace](/azure/api-management/api-management-howto-api-inspector) header value. API Tracing is also built into the admin portal and is enabled by default when testing APIs from the portal.|
|Configure logging using Azure Monitor for the API Management service.|Logs can be sent to a Logs Analytics workspace to enable complex querying and analysis. Metrics can be ingested for longer term analysis. All data is then surfaced using Azure Monitor. It is possible to integrate Application Insights for Application Performance Management.|
|Choose the right modes to access private site connections.|Supports Virtual Network integration in internal and external mode.|
|Evaluate firewall rules and IP allowlists based on the API Management public IP address.|A fixed public IP address is available for the lifetime of the service with the Basic, Developer, Standard, and Premium plans for API Management.|

## Next step

> [!div class="nextstepaction"]
> [Reliability and Azure Firewall](../azure-firewall/reliability.md)
