---
title: Service Bus and operational excellence
description: Focuses on the Service Bus service used in the Messaging solution to provide best-practice, configuration recommendations, and design considerations related to Operational excellence.
author: v-stacywray
ms.author: robbymillsap
ms.date: 01/18/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - service-bus
categories:
  - messaging
  - management-and-governance
---

# Service Bus and operational excellence

[Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview) is a fully managed enterprise message broker with message queues and publish-subscribe topics. This service stores messages in a *broker* (for example, a *queue*) until the consuming party is ready to receive the messages.

Benefits include:

- Load-balancing work across competing workers.
- Safely routing and transferring data and control across service, and application boundaries.
- Coordinating transactional work that requires a high-degree of reliability.

For more information about using Service Bus, reference [Azure Service Bus Messaging documentation](/azure/service-bus-messaging/) to learn how to set up messaging that connects applications and services across on-premises and cloud environments.

To understand how Service Bus promotes operational excellence, reference the following topics:

- [Handling outages and disasters](/azure/service-bus-messaging/service-bus-outages-disasters)
- [Throttling operations on Azure Service Bus](/azure/service-bus-messaging/service-bus-throttling)

The following sections are specific to Azure Service Bus and operational excellence:

- Design considerations
- Configuration checklist
- Recommended configuration options
- Source artifacts

## Design considerations