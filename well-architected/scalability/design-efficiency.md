---
title: Design applications for efficiency
description: Review design options for application efficiency, such as asynchronous programming, queued and batched requests, data compression, and session affinity.
author: martinekuan
ms.author: martinek
ms.date: 05/01/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-pillar-efficiency
ms.custom:
  - How are you designing your applications for efficiency?
  - article
---

# Design applications for efficiency

Making choices that improve performance efficiency is critical to application design. This article describes the most important aspects of designing efficient applications. For more performance efficiency design related guidance, see [Design for performance](design-apps.md).

## Reduce response time with asynchronous programming

The time for a caller to receive a response can range from milliseconds to minutes. During that time, the thread is held by the process until the response comes back or an exception occurs. Holding the thread is inefficient, because no other requests can be processed during the response wait time. An example of multiple requests in flight being inefficient is a bank account where only one resource can operate on the request at a time. Another example is when connection pools can't be shared, so all the requests need separate connections to complete.

Asynchronous processing is an alternative approach that enables a remote service to be executed without waiting and blocking resources on the client. Asynchronous programming is a critical pattern for enabling performance efficiency, and is available in most modern programming languages and platforms.

There are many ways to inject asynchronous programming into an application design. For APIs and services that work across the internet, consider using the [asynchronous request-reply pattern](/azure/architecture/patterns/async-request-reply). In code, remote calls can be asynchronously executed by using built-in language constructs like `async`/`await` in .NET C#. For more information, see [Asynchronous programming with async and await](/dotnet/csharp/async).

The .NET platform has other built-in support for asynchronous programming. For more information, see [Task-based asynchronous pattern](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap) and [Event-based asynchronous pattern](/dotnet/standard/asynchronous-programming-patterns/event-based-asynchronous-pattern-eap).

## Process faster by queuing and batching requests

Similar to asynchronous programming, queuing is a scalable mechanism to hand off processing work to a service. Highly scalable queuing services are natively supported in Azure. The queue is a storage buffer located between the caller and the processing service. The queue takes requests, stores them in a buffer, and queues the requests to provide services for the reliable delivery and management of the queued data.

A queue is often the best way to hand off work to a processing service. The processing service receives work by listening on the queue and dequeuing messages. If items to be processed enter too quickly, the queuing service keeps them in the queue until the processing service has available resources and asks for a new message.

The processing service can use dynamic [Azure Functions](/azure/azure-functions/functions-overview) to easily autoscale on demand as the queue builds up to meet the intake pressure. Developing processor logic with Azure Functions to run task logic from a queue is a common, scalable, and cost effective way to use queuing between a client and a processor.

Azure provides some native first-party queueing services. These services include Azure Queue Storage, a simple queuing service based on Azure Storage, and Azure Service Bus, a message broker service that supports transactions and reduced latency. Many third-party options are also available through [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace).

To learn more about queue-based load leveling, see [Queue-based load leveling pattern](/azure/architecture/patterns/queue-based-load-leveling). Also see [Storage queues and Service Bus queues compared and contrasted](/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted).

## Optimize with data compression

A well-known optimization best practice is to use a compression strategy to compress and bundle web pages or API responses. Compression shrinks the data returned from a page or API back to the browser or client app. Compressing the data returned to clients optimizes network traffic and accelerates the application.

[Azure Front Door](/azure/frontdoor/front-door-caching#file-compression) can do file compression, and .NET has built-in framework support for this technique with *gzip* compression. For more information, see [Response compression in ASP.NET Core](/aspnet/core/performance/response-compression?preserve-view=true&view=aspnetcore-3.1).

## Improve performance with session affinity

If your application is stateful, and data or state is stored locally in the instance of the application, enabling session affinity might improve application performance. When session affinity is enabled, subsequent requests to the application are directed to the same server that processed the first request. If session affinity isn't enabled, subsequent requests are directed to the next available server, depending on the load balancing rules.

Session affinity allows the instance to have some persistent or cached data or context, which can speed subsequent requests. However, if your application doesn't store large amounts of state or cached data in memory, session affinity might decrease your throughput. One host could get overloaded with requests, while others are dormant.

> [!TIP]
> For information about best practices for stateless services when an application is migrated from old Azure Cloud Services to Azure Service Fabric, see [Migrate an Azure Cloud Services application to Azure Service Fabric](/azure/architecture/service-fabric/migrate-from-cloud-services).

## Run background jobs to meet integration needs

Many types of applications require background tasks that run independently of the user interface. Examples include batch jobs, intensive processing tasks, and long-running processes such as workflows. Background jobs can be run without requiring user interaction. The application can start the job and continue to process interactive requests from users. For more information, see [Background jobs](/azure/architecture/best-practices/background-jobs).

Background tasks must perform adequately to ensure that they don't block the application or cause inconsistencies due to delayed operation when the system is under load. Performance can be improved by scaling the compute instances that host the background tasks. For a list of considerations, see [Background jobs scaling and performance considerations](/azure/architecture/best-practices/background-jobs#scaling-and-performance-considerations).

[Logic Apps](/azure/logic-apps/logic-apps-overview) is a serverless pay-per-use consumption service that enables a set of ready-to-use out-of-the-box connectors and a long-running workflow engine to quickly meet cloud-native integration needs. Logic Apps is flexible enough to support scenarios like running tasks or jobs, advanced scheduling, and triggering.

Logic Apps also has advanced hosting options to allow it to run within enterprise restricted cloud environments. Logic Apps can be combined with and complement other Azure services, or can be used independently.

Like all serverless services, Logic Apps doesn't require VM instances to be purchased, enabled, or scaled up and down. Logic Apps scales automatically on serverless platform as a service (PaaS)-provided instances, and you pay only for usage.

## Next steps

> [!div class="nextstepaction"]
> [Design for scaling](design-scale.md)
