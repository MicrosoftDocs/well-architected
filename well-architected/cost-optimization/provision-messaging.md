---
title: Azure Messaging cost estimates
description: Learn about the cost strategies for messaging services, including Service Bus cost, Event Grid cost, and Event Hubs.
author: martinekuan
ms.author: martinek
ms.date: 04/19/2023
ms.topic: conceptual
ms.custom:
  - article
  - internal-intro
---

# Azure messaging cost estimates

The messaging services in this article have no up-front cost or termination fees. You pay only for what you use. In some cases, combining two messaging services to increase the efficiency of your messaging system is advantageous. For for more information, see [Crossover scenarios](/azure/architecture/guide/technology-choices/messaging#crossover-scenarios).

Cost is based on the number of operations or throughput units you use, depending on the message service. If you use the wrong messaging service, you may incur higher costs. Before choosing a service, first, determine the intent and requirements of the messages. Then consider the tradeoffs between cost, operations units, and throughput units. For tradeoff examples, see [Technology choices for a message broker](/azure/architecture/guide/technology-choices/messaging#technology-choices-for-a-message-broker).

Use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) for help with creating various cost scenarios.

## Azure Service Bus cost

Connect on-premises and cloud-based applications and services to implement highly secure messaging workflows by using Azure Service Bus. Cost is based on messaging operations and the number of connections. The basic tier is the cheapest. If you want more operations and features, choose the standard tier or the premium tier. For example, the [Azure Service Bus](/azure/architecture/reference-architectures/enterprise-integration/queues-events#service-bus) premium tier runs in dedicated resources to provide higher throughput and more consistent performance.

For pricing details, see [Azure Service Bus pricing](https://azure.microsoft.com/pricing/details/service-bus/).

## Event Grid cost

Use [Event Grid](/azure/architecture/reference-architectures/serverless/cloud-automation#event-grid) to simplify event-based app development and manage events routing from any source to any destination. Event Grid can route a massive number of events, per second, per region. Cost is based on the number of operations you run. Examples of operations you can run are event ingress, subscription delivery attempts, management calls, and filtering by subject suffix.

For pricing details, see [Event Grid pricing](https://azure.microsoft.com/pricing/details/event-grid/).

## Event Hubs

Use Event Hubs to stream millions of events per second from any source, build dynamic data pipelines, and immediately respond to business challenges. Cost is based on throughput units. A key difference between Event Grid and Event Hubs is in the way event data is made available to subscribers. For more information, see [Pull model](/azure/architecture/guide/technology-choices/messaging#pull-model-1).

For frequently asked questions on pricing, see [Event Hubs FAQs](/azure/event-hubs/event-hubs-faq#pricing)
For details on Event Hubs pricing, see [Event Hubs pricing](https://azure.microsoft.com/pricing/details/event-hubs/).
