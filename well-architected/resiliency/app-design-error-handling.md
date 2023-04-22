---
title: Error handling for resilient apps
description: Review best practices for handling errors in resilient Azure applications. Understand transient fault handling, request timeouts, and cascading failures.
author: martinekuan
ms.author: martinek
ms.date: 04/24/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-pillar-reliability
ms.custom:
  - How are you managing errors & failures?
  - article
---

# Error handling for resilient applications in Azure

Many things in a distributed system, such as underlying cloud infrastructure and third-party runtime dependencies, are outside your span of control and your means to test. You can be sure something will fail eventually, so you need to be prepared.

Ensuring that your application can recover from errors is critical. You can test your applications to prevent errors and failure, but you need to be prepared for when applications encounter issues or fail. Testing doesn't always catch everything, so you need to understand how to handle errors and prevent potential failure.

## Key points

- Implement retry logic to handle transient application failures and transient failures with internal or external dependencies.
- Uncover issues or failures in your application's retry logic.
- Configure request timeouts to manage intercomponent calls.
- Configure and test health probes for your load balancers and traffic managers.
- Segregate read operations from update operations across application data stores.

## Implement retry logic

A [retry pattern](/azure/architecture/patterns/retry) describes how an application handles anticipated temporary failures when it tries to connect to a service or network resource by transparently retrying an operation that has previously failed.

When you use a retry pattern, pay particular attention to [issues and considerations](/azure/architecture/patterns/retry#issues-and-considerations). Avoid overwhelming dependent services by implementing the [Circuit Breaker pattern](/azure/architecture/patterns/circuit-breaker). Review and incorporate other best practices for [transient fault handling](/azure/architecture/patterns/circuit-breaker). While calling systems that have [throttling pattern](/azure/architecture/patterns/circuit-breaker) implemented, ensure that your retries aren't counter productive.

:::image type="icon" source="../_images/github.png" border="false"::: [Samples related to this pattern](https://github.com/mspnp/samples/tree/master/Reliability/RetryPatternSample) are available on GitHub.

## Uncover retry logic issues

Track the number of transient exceptions and retries over time to uncover issues or failures in your application's retry logic. A trend of increasing exceptions over time might indicate that the service has an issue and might fail. To learn more, see [Retry guidance for Azure services](/azure/architecture/best-practices/retry-service-specific).

The [Circuit Breaker pattern](/azure/architecture/patterns/circuit-breaker) provides stability while the system recovers from a failure and minimizes the impact on performance. It can help to maintain the response time of the system by quickly rejecting a request for an operation that's likely to fail, rather than waiting for the operation to time out, or never return.

A circuit breaker might be able to test the health of a service by sending a request to an endpoint exposed by the service. The service should return information indicating its status.

:::image type="icon" source="../_images/github.png" border="false"::: This [reference implementation](https://github.com/mspnp/microservices-reference-implementation/tree/master/src/shipping/workflow) on GitHub uses [Polly](https://github.com/App-vNext/Polly/wiki/Circuit-Breaker) and [IHttpClientBuilder](/dotnet/api/microsoft.extensions.dependencyinjection.ihttpclientbuilder?view=dotnet-plat-ext-5.0&preserve-view=true) to implement the Circuit Breaker pattern.

## Configure request timeouts

For a service call or a database call, ensure that appropriate request timeouts are set. Database connection timeouts are typically set to 30 seconds. For guidance on how to troubleshoot, diagnose, and prevent SQL connection errors, see [transient errors for SQL Database](/azure/sql-database/sql-database-connectivity-issues).

Use design patterns that encapsulate robust timeout strategies like [Choreography pattern](/azure/architecture/patterns/choreography) or [Compensating Transaction pattern](/azure/architecture/patterns/compensating-transaction).

:::image type="icon" source="../_images/github.png" border="false"::: A [reference implementation](https://github.com/mspnp/microservices-reference-implementation) is available on GitHub.

## Application health probes

Configure and test health probes for your load balancers and traffic managers. Ensure that your health endpoint checks the critical parts of the system and responds appropriately.

- For [Azure Front Door](/azure/frontdoor/front-door-overview) and [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview), the health probe determines whether to fail over to another region. Your health endpoint should check any critical dependencies that are deployed within the same region.
- For [Azure Load Balancer](/azure/load-balancer/load-balancer-overview), the health probe determines whether to remove a virtual machine (VM) from rotation. The health endpoint should report the health of the VM. Don't include other tiers or external services. Otherwise, a failure that occurs outside the VM causes the load balancer to remove the VM from rotation.

:::image type="icon" source="../_images/github.png" border="false"::: [Samples related to heath probes](https://github.com/mspnp/samples/tree/master/Reliability/HealthProbesSample) are available on GitHub. The samples include:

- ARM template that deploys an Azure Load Balancer and health probes that detect the health of the sample service endpoint.
- An ASP.NET Core Web API that shows configuration of health checks at startup.

## Command and query responsibility segregation (CQRS)

Segregate read and write interfaces by implementing the [CQRS pattern](/azure/architecture/patterns/cqrs) to achieve the levels of scale and performance needed for your solution.

## Related links

- For information on transient faults, see [Troubleshoot transient connection errors](/azure/azure-sql/database/troubleshoot-common-connectivity-issues).
- For guidance on implementing health monitoring in your application, see [Health Endpoint Monitoring pattern](/azure/architecture/patterns/health-endpoint-monitoring).

Go back to the main article: [Testing for reliability](test-checklist.md)

## Next steps

> [!div class="nextstepaction"]
> [Chaos engineering](./chaos-engineering.md)
