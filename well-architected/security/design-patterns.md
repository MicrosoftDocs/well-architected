---
title: Cloud design patterns that support security
description: Learn about industry patterns that support security and can help you address common challenges in cloud workloads.  
author: ckittel
ms.author: chkittel
ms.date: 11/15/2023
ms.topic: conceptual
---

# Cloud design patterns that support security

When you design workload architectures, you should use industry patterns that address common challenges. Patterns can help you make intentional tradeoffs within workloads and optimize for your desired outcome. These patterns are backed by real-world experience, are designed for cloud scale and operating models, and are inherently vendor agnostic. Using well-known patterns as a way to standardize your workload design is a component of operational excellence.

Many design patterns directly support one or more architecture pillars. Design patterns that support the security pillar prioritize concepts like segmentation and isolation, strong authorization, uniform application security, and modern protocols.

## Design patterns for security

The following table summarizes cloud design patterns that support the goals of security.

|Pattern|Summary|
|-|-|
|[Ambassador](/azure/architecture/patterns/ambassador)|Encapsulates and manages network communications by offloading cross-cutting tasks that are related to network communication. The resulting helper services initiate communication on behalf of the client. This mediation point provides an opportunity to augment security on network communications.|
|[Backends for Frontends](/azure/architecture/patterns/backends-for-frontends)|Individualizes the service layer of a workload by creating separate services that are exclusive to a specific frontend interface. Because of this separation, the security and authorization in the service layer that support one client can be tailored to the functionality provided by that client, potentially reducing the surface area of an API and lateral movement among different backends that might expose different capabilities.|
|[Bulkhead](/azure/architecture/patterns/bulkhead)|Introduces intentional and complete segmentation between components to isolate the blast radius of malfunctions. You can also use this strategy to contain security incidents to the compromised bulkhead.|
|[Claim Check](/azure/architecture/patterns/claim-check)|Separates data from the messaging flow, providing a way to separately retrieve the data related to a message. This pattern supports keeping sensitive data out of message bodies, instead keeping it managed in a secured data store. This configuration enables you to establish stricter authorization to support access to the sensitive data from services that are expected to use the data, but remove visibility from ancillary services like queue monitoring solutions.|
|[Federated Identity](/azure/architecture/patterns/federated-identity)|Delegates trust to an identity provider that's external to the workload for managing users and providing authentication for your application. By externalizing user management and authentication, you can get evolved capabilities for identity-based threat detection and prevention without needing to implement these capabilities in your workload. External identity providers use modern interoperable authentication protocols.|
|[Gatekeeper](/azure/architecture/patterns/gatekeeper)|Offloads request processing that's specifically for security and access control enforcement before and after forwarding the request to a backend node. Adding a gateway into the request flow enables you to centralize security functionality like web application firewalls, DDoS protection, bot detection, request manipulation, authentication initiation, and authorization checks.|
|[Gateway Aggregation](/azure/architecture/patterns/gateway-aggregation)|Simplifies client interactions with your workload by aggregating calls to multiple backend services in a single request. This topology often reduces the number of touch points a client has with a system, which reduces the public surface area and authentication points. The aggregated backends can stay fully network-isolated from clients.|
|[Gateway Offloading](/azure/architecture/patterns/gateway-offloading)|Offloads request processing to a gateway device before and after forwarding the request to a backend node. Adding a gateway into the request flow enables you to centralize security functionality like web application firewalls and TLS connections with clients. Any offloaded functionality that's platform-provided already offers enhanced security.|
|[Publisher/Subscriber](/azure/architecture/patterns/publisher-subscriber)|Decouples components in an architecture by replacing direct client-to-service communication with communication via an intermediate message broker or event bus. This replacement introduces an important security segmentation boundary that enables queue subscribers to be network-isolated from the publisher.|
|[Sidecar](/azure/architecture/patterns/sidecar)|Extends the functionality of an application by encapsulating non-primary or cross-cutting tasks in a companion process that exists alongside the main application. By encapsulating these tasks and deploying them out-of-process, you can reduce the surface area of sensitive processes to only the code that's needed to accomplish the task. You can also use sidecars to add cross-cutting security controls to an application component that's not natively designed with that functionality.|
|[Throttling](/azure/architecture/patterns/throttling)|Imposes limits on the rate or throughput of incoming requests to a resource or component. You can design the limits to help prevent resource exhaustion that could result from automated abuse of the system.|
|[Valet Key](/azure/architecture/patterns/valet-key)|Grants security-restricted access to a resource without using an intermediary resource to proxy the access. This pattern enables a client to directly access a resource without needing long-lasting or standing credentials. All access requests start with an auditable transaction. The granted access is then limited in both scope and duration. This pattern also makes it easier to revoke the granted access.|

## Next steps

Review the cloud design patterns that support the other Azure Well-Architected Framework pillars:

- [Cloud design patterns that support reliability](../reliability/design-patterns.md)
- [Cloud design patterns that support cost optimization](../cost-optimization/design-patterns.md)
- [Cloud design patterns that support operational excellence](../operational-excellence/design-patterns.md)
- [Cloud design patterns that support performance efficiency](../performance-efficiency/design-patterns.md)