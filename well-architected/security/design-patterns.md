---
title: Cloud design patterns that support security
description: Learn about industry patterns that support security and can help you address common challenges in cloud workloads.  
author: ckittel
ms.author: chkittel
ms.date: 11/15/2023
ms.topic: conceptual
---

# Cloud design patterns that support security

When you design workload architectures, you should use industry patterns that address common challenges. Patterns can help you make intentional tradeoffs within workloads and optimize for your desired outcome. These patterns are backed by real-world experience, are designed for cloud scale and operating models, and are inherently vendor-agnostic. Using well-known patterns as a way to standardize your workload design is a component of operational excellence.

Many design patterns directly support one or more architecture pillars. Design patterns that support the security pillar prioritize concepts like segmentation and isolation, strong authorization, uniform application security, and modern protocols.

## Design patterns for security

The following table summarizes cloud design patterns that support the goals of security.

|Pattern|Summary|
|-|-|
|[Ambassador](/azure/architecture/patterns/ambassador)|Encapsulates and manages network communications by offloading cross-cutting tasks that are related to network communication, ultimately initiating communication on behalf of its client. This mediation point provides an opportunity to augment security on network communications by, for example, implementing mTLS connections or providing network logging and monitoring.|
|[Backends for Frontends](/azure/architecture/patterns/backends-for-frontends)|Individualizes the service layer of a workload by creating separate services that are exclusive to a specific frontend interface. Because of this separation, the security and authorization in the service layer that support one client can be tailored to the functionality provided by that client, potentially reducing the surface area of an API and lateral movement among different backends that might expose different capabilities.|
|[Bulkhead](/azure/architecture/patterns/bulkhead)|Introduces intentional and complete segmentation between components to isolate the blast radius of malfunctions. You can also use this strategy to contain security incidents to the compromised bulkhead.|
|[Claim Check](/azure/architecture/patterns/claim-check)|Separates data from the messaging flow, providing a way to separately retrieve the data related to the message. This pattern supports keeping sensitive data out of message bodies, instead keeping it managed in a secured data store. This configuration enables you to establish stricter authorization to support access to the sensitive data from services that are expected to use the data, but remove visibility from ancillary services like queue monitoring solutions.|
|[Federated Identity](/azure/architecture/patterns/federated-identity)|Delegates trust to an identity provider that's external to the workload for managing users and providing authentication for your application. By externalizing user management and authentication, you can get evolved capabilities for identity-based threat detection and prevention without needing to implement these capabilities in your workload. External identity providers use modern interoperable authentication protocols.|
|[Gatekeeper](/azure/architecture/patterns/gatekeeper)|Offloads request processing that's specifically for security and access control enforcement before and after forwarding the request to a backend node. Adding a gateway into the request flow enables you to centralize security functionality like web application firewalls, DDoS protection, bot detection, request manipulation, authentication initiation, and authorization checks.|
|[Gateway Aggregation](/azure/architecture/patterns/gateway-aggregation)|Simplifies client interactions with your workload by aggregating calls to multiple backend services in a single request. This topology often reduces the number of touch points a client has with a system, reducing the public surface area and authentication points. The aggregated backends can stay fully network-isolated from clients.|
|[Gateway Offloading](/azure/architecture/patterns/gateway-offloading)|This pattern is used to offload request processing in a gateway device before and after forwarding the request to a backend node. Injecting a gateway into the request flow allows you to centralize security affordances such as web-application firewalls and establishing TLS connections with clients.  Any offloaded functionality that is platform-provided is already hardened as part of the offering.|
|[Publisher/Subscriber](/azure/architecture/patterns/publisher-subscriber)|This pattern seeks to decouple components in an architecture by replacing direct client-to-service with an intermediate message broker or event bus. This can introduce an important security segmentation boundary such that the queue subscribers can be completely network-isolated from the publisher.|
|Sidecar|This pattern is a method to extend the functionality of an application by encapsulating non-primary or cross-cutting concerns into a companion process that exists alongside of the main application. By encapsulating non-primary or cross-cutting concerns and deploying them as an out-of-process implementation, the surface area of sensitive processes can be reduced to only the code necessary to accomplish the task. Sidecars can also be used to layer in additional security controls to an application, for example performing mTLS for service-to-service calls in an application component not designed to do natively do so.|
|[Throttling](/azure/architecture/patterns/throttling)|This pattern put limits on the rate or throughput of incoming requests to resource or component. The limits can be designed to help prevent resource exhaustion that could come with automated abuse of the system.|
|Valet Key|This pattern grants security-restricted access to a resource without involving an intermediary resource to proxy the access. This pattern allows a client to directly access a resource without the need for long-lasting or standing credentials. All access requests start with an auditable transaction, and the granted access is then limited in both scope & duration and facilitates revocability of that access.|

## Next steps

Review the cloud design patterns that support the other Azure Well-Architected Framework pillars:

- [Cloud design patterns that support reliability](../reliability/design-patterns.md)
- [Cloud design patterns that support cost optimization](../cost-optimization/design-patterns.md)
- [Cloud design patterns that support operational excellence](../operational-excellence/design-patterns.md)
- [Cloud design patterns that support performance efficiency](../performance-efficiency/design-patterns.md)