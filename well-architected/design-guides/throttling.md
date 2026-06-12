---
title: Design throttling to improve resilience
description: Learn how to design throttling as an architectural capability that preserves reliability, fairness, and performance under normal cloud overload conditions.
author: krnese
ms.author: pgupt
ms.date: 06/11/2026
ms.topic: concept-article
ms.update-cycle: 1095-days  
---

# Design throttling to improve resilience

Throttling is a design pattern that controls how much load your system can accept at a given time. In distributed systems, traffic spikes, retry storms, fan-out patterns, and shared downstream dependencies all create conditions where uncontrolled demand eventually exhausts capacity. When a component reaches capacity, the throttling mechanism helps reject or delay additional requests until the system is ready again. Otherwise, latency can increase, retries amplify load, and failures can spread through the system.

A common architectural approach is to apply throttling only at the gateway to protect ingress. An isolated throttling mechanism may protect one component while shifting overload to other parts of your application, increasing latency and degrading user experience.

In  well-architected design, apply the pattern across the system rather than only at the edge. Consider where your service shares capacity across multiple callers, exposes resources that can be exhausted, or participates in a call chain where overload in one component can cascade to others. All those points are potentially good candidates for throttling. Effective throttling requires coordinated response across architecture components. You should treat overload as a normal operating condition: expect it, monitor it, and actively control how your system responds when capacity limits are reached.

Throttling buys time for reactive responses during events like scaling operations, on-call investigation, circuit breakers. Static limits calibrated last quarter may no longer reflect today's traffic patterns.

:::image type="content" source="./images/throttling/throttling-effect.png" alt-text="Graph showing how throughput, response time, and resource utilization change as concurrent load increases. In the low-utilization zone, response time is constant and throughput grows. In the high-utilization zone, throughput peaks and resource utilization is high. In the server-unresponsive zone, throughput falls and response time explodes." lightbox="./images/throttling/throttling-effect.png":::

The intent of this article is to guide you through throttling best practices. It assumes that you understand the [Throttling pattern](/azure/architecture/patterns/throttling). 

Start with the [throttling tenets](#throttling-tenets), because every workload follows certain fundamental approaches. The tenets are presented in tabs, and you should review all of them before you tune limits in your design.

After you understand the tenets, evaluate where you are in your adoption journey by reviewing the [Adoption phases](#adoption-phases). Use the lower levels to implement baseline implementation and optimize as your workload matures over time.

Remainder of the guide is a reference of throttling practices codified as TPs. Each TP is a discrete guidance unit and appears in numeric order.

## Types of throttling

Each throttling type protects a different part of your architecture. Place each one at the boundary where it can directly control load. 

![Architecture diagram showing throttling points in a system. A client calls a gateway, which routes into a system stamp containing an API layer and internal components. The internal components call external dependencies and storage. An ATS (Adaptive Throttling Service) receives usage signals from each layer, numbered 1 through 4, corresponding to ingress, internal, and egress throttling boundaries.](./images/throttling/throttling-points.png)

The table maps the types to the architectural layers used in the practices below.

| Type | What it controls | Where it lives | Relevant practices |
|---|---|---|---|
| **Ingress throttling** | Request rate and volume entering your system | API gateway, public and private entry points | [TP-1](#tp-1-customize-protections-at-your-gateway), [TP-2](#tp-2-implement-throttling-at-multiple-points) |
| **Internal throttling** | Traffic between your sub-components like databases, caches, queues, and shared services | Service-to-service calls inside your boundary | [TP-6](#tp-6-protect-internal-components) |
| **Egress throttling** | Traffic your system sends to downstream dependencies | Client SDKs, fan-out points, outbound call paths | [TP-7](#tp-7-implement-egress-bulkhead-when-calling-dependencies) |
| **State-aware throttling** | Limits that adjust based on real-time system health, operation cost, or queue depth | Throttle policy engine, admission control layer | [TP-12](#tp-12-implement-state-aware-throttling), [TP-13](#tp-13-honor-transactional-boundaries), [TP-14](#tp-14-implement-random-early-drop) |

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Throttling depends on cooperation between the server and its clients. To be effective, the server must enforce clear, predictable throttling policies and communicate them consistently. Clients, in turn, must understand and respect those limits by implementing appropriate retry, backoff, and rate-control mechanisms. Mitigate this risk by having well-defined contracts that reward good behavior and discourage abuse or excessive consumption.

Most workloads need all four types working together. If you only throttle ingress, you protect the front door but push overload deeper into your application. Internal and egress controls stop that spread. State-aware throttling lets your system adjust as conditions change instead of running with stale fixed limits.

## Throttling tenets

No matter which throttling types your workload uses, you still need the same core fundamentals. If you're new to throttling, read these tenets in order so you understand *why* each practice exists before you implement *how*. If you're reviewing an existing design, use the design questions under each principle as a checklist to find gaps.

| Terminology | Description |
|---|---|
| **Boundary** | Boundary is the component group your throttling protects, usually represented by an entry point. |
| **Dimension** | Dimension is a name/value pair from request metadata that a throttling rule evaluates. |
| **Upstream** | Upstream is the caller that sends requests to your service. |
| **Downstream** | Downstream is the dependency your service calls. |

### [**Tenet 1**](#tab/level1)

![Goal icon](../_images/goal.svg) **Design for overload as a normal operating mode**

In distributed systems, overload is expected, not exceptional. Throttling is the control loop that protects shared dependencies and preserves your SLOs under load. Design your system assuming traffic surges, retries, and cascading effects will happen.

#### Design considerations

Use these when reviewing an existing design or assessing where your system is exposed to overload risk.

* What saturates this system and impacts SLOs: requests, concurrency, CPU, I/O, queue depth, or downstream quotas?
* Where does load enter, amplify (fan-out), or cascade?
* For each entry point, what are the explicit boundaries of impact (gateway, shard, dependency)?
* What is the appropriate overload signaling mechanism between components?
* When (not if) overload happens, how will the system heal and recover?

#### Design recommendations

* Define throttling boundaries (where throttling occurs) and throttling dimensions (what limits are enforced) at design time, and ensure they align with saturation vectors.
* Enforce limits at every entry point, including low-priority and read-only APIs.
* Control concurrency at fan-out points.
* Ensure components in this system are ready to respect protocol-specific overload signals.
* Treat throttling as a lifecycle: measure → enforce → learn → adapt.

> [!CAUTION]
> Throttling is not a substitute for DoS/DDoS protection. Continue investing in mechanisms to detect, mitigate, and block malicious traffic designed to overwhelm the system.

#### Relevant best practices
* [TP-1](#tp-1-customize-protections-at-your-gateway)
* [TP-2](#tp-2-implement-throttling-at-multiple-points)
* [TP-3](#tp-3-create-backpressure)

### [**Tenet 2**](#tab/level2)

![Goal icon](../_images/goal.svg) **Design how your dependencies respond to overload to prevent cascading failures**

Account for upstream systems (systems that call the service), downstream systems (systems called by the service), and shared components. A well-designed interaction mechanism for overload conditions improves overall system stability. Poor signaling amplifies retries and cascades failures.

#### Design considerations

Use these when assessing how well your system's components signal and respond to overload across boundaries.

* What are the interaction patterns and protocols used between components, and do they have appropriate signaling mechanisms?
* Are internal components and downstream systems protected from overload?
* Are shared components or highly leveraged components protected from overload?
* Can all components signal when they are overloaded, and are back-pressure signals in place to aid recovery?
* Are we giving callers clear guidance for load conditions, or unintentionally encouraging blind retry loops?

#### Design recommendations

* Choose well-understood architectural patterns and use standardized protocols that support overload signaling.
* Ensure components can protect themselves through back-pressure, and ensure they respect overload signals.
* Ensure overload signals from downstream services aren't hidden or made opaque.
* Ensure component interactions allow recovery through pausing or other compensating mechanisms.
* Return information to users that allows them to retry intelligently.


> :::image type="icon" source="../_images/risk.svg"::: **Risk**: If throttling is implemented with an oversimplified design - for example, defaulting to rejecting traffic on failure - it can inadvertently block legitimate requests during partial outages, degradation scenarios, or configuration errors. To mitigate this risk, the system should be thoroughly tested and validated end-to-end under failure conditions before production.


#### Relevant best practices
* [TP-5](#tp-5-respect-backpressure)
* [TP-6](#tp-6-protect-internal-components)
* [TP-7](#tp-7-implement-egress-bulkhead-when-calling-dependencies)
* [TP-8](#tp-8-dont-use-caching-to-replace-throttling)
* [TP-9](#tp-9-keep-throttling-accounting-out-of-critical-path)
* [TP-10](#tp-10-pay-attention-to-cross-cutting-infrastructure)

### [**Tenet 3**](#tab/level3)

![Goal icon](../_images/goal.svg) **Design throttling and boundaries around business requirements, not infrastructure convenience**

Throttling affects the user experience. Set limits based on what’s critical to the business and the tolerated impact, not on internal topology convenience. Hard stops waste available capacity and increase friction.

#### Design considerations

Use these to evaluate whether your throttling design respects user experience, not just infrastructure health.

* Who feels the limit when it triggers? Is the impact wide or narrow? Is this impact intentional?
* Are throttling boundaries and dimensions aligned with user expectations and mental models?
* Can we enable users to make progress if system capacity and state allow it?
* Do users experience API responses as opaque rejections without clear, actionable information?

#### Design recommendations

* Be aware of "logical" transaction boundaries as experienced by the user.
* Allow temporary elasticity, even if it is temporarily unfair, provided it doesn't compromise stability.
* Where possible, factor in the cost of servicing the request and incorporate awareness of overall system state and available capacity into throttling decisions.
* Build in the ability to offer brownouts, where systems trade off completeness in favor of availability.


#### Relevant best practices
* [TP-2](#tp-2-implement-throttling-at-multiple-points)
* [TP-3](#tp-3-create-backpressure)
* [TP-12](#tp-12-implement-state-aware-throttling)
* [TP-13](#tp-13-honor-transactional-boundaries)
* [TP-14](#tp-14-implement-random-early-drop)

### [**Tenet 4**](#tab/level4)

![Goal icon](../_images/goal.svg) **Treat throttling with a continuous evolution mindset**

Throttling effectiveness decays as traffic patterns evolve. Static limits become incorrect over time. Regular measurement, testing, and refinement processes keep throttling boundaries and dimensions aligned with reality.

#### Design considerations

Use these to evaluate whether your team treats throttling as a living operational discipline rather than a one-time configuration.

- Does your team treat throttling as a point-in-time exercise, or as a lifecycle and evidence-driven configuration flywheel: measure → enforce → learn → adapt?
- Are overload events practiced and rehearsed?
- Is telemetry rich enough to diagnose saturation quickly?
- When was the last time we validated that our limits still reflect reality?

#### Design recommendations

- Treat throttling configuration as adaptive and evidence-driven. Regularly review top-N traffic and saturation and utilization patterns that feed processes (automated or manual) for refreshing throttling limits.
- Validate limits through controlled testing and low-limit exercises for your entire API surface.
- Develop and practice DRI responses to overload events, prioritizing recovery time over all other considerations.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Throttling helps protect system stability and control resource consumption, but it comes with ongoing operational costs. It requires additional code, configuration, monitoring, and infrastructure that must be maintained and continuously tuned as traffic patterns and system requirements evolve. Therefore, disciplined operations, rigorous testing, and effective change management are non-negotiable prerequisites for ensuring that throttling delivers its intended value over time.


#### Relevant best practices
* [TP-4](#tp-4-build-operations-to-handle-load-events)
* [TP-11](#tp-11-reassess-tune-and-validate-periodically)

---

## Adoption phases

| Maturity Level | Best Practices | Benefit |
|---:|---|---|
| 1 | [TP-1](#tp-1-customize-protections-at-your-gateway): Customize protections are your gateway<br>[TP-2](#tp-2-implement-throttling-at-multiple-points): Implement Frontend throttling<br>[TP-3](#tp-3-create-backpressure): Create Backpressure<br>[TP-4](#tp-4-build-operations-to-handle-load-events): Operational excellence (Part 1) | Protect the service that you own; have operational response |
| 2 | [TP-5](#tp-5-respect-backpressure): Respect backpressure<br>[TP-6](#tp-6-protect-internal-components): Internal controls<br>[TP-7](#tp-7-implement-egress-bulkhead-when-calling-dependencies): Egress bulkhead | Protect a downstream dependency and your service |
| 3 | [TP-8](#tp-8-dont-use-caching-to-replace-throttling): Cache does not replace a throttle.<br>[TP-9](#tp-9-keep-throttling-accounting-out-of-critical-path): Keep Throttling out of critical path.<br>[TP-10](#tp-10-pay-attention-to-cross-cutting-infrastructure): Protect Horizontal Infrastructure<br>[TP-11](#tp-11-reassess-tune-and-validate-periodically): Operational Excellence (Part 2) | Your service avoids problematic architectural patterns; You have operational posture and discipline to withstand failures |
| 4 | [TP-12](#tp-12-implement-state-aware-throttling): State-aware throttling<br>[TP-13](#tp-13-honor-transactional-boundaries): Honor transactional boundaries<br>[TP-14](#tp-14-implement-random-early-drop): Continuous validation via Random Early Drop | Your service has granular control on QoS; Client and service auto-validate response readiness |

> [!IMPORTANT]
> 
> In AI workloads, the primary saturation vector shifts from request rate to token throughput. Capacity becomes proportional to prompt and response size rather than request count. The Azure OpenAI tabs throughout this section apply the tenets above through that lens.

## [TP-1] Customize protections at your gateway

Start load control at the first Layer 3 gateway component you control (see item 1 in [image showing throttling points in a system](./images/throttling/throttling-points.png)). Your gateway capabilities and position in the request path determine how sophisticated these controls can be. Even simple checks, such as source IP or DNS-based filtering, can protect backend services during traffic surges.

Gateways are also a good place for coarse-grained load blocking during unexpected traffic spikes. Blocking at this layer can reduce the number of backend systems destabilized during an event and help your system recover faster.

Keep gateway checks simple and inexpensive. Sophisticated controls at this point slow all requests. Choose gateway products that balance response speed with policy complexity. Also remember that these Layer 3 controls are coarse-grained. When they trigger, the blast radius is often wider than expected. Continue to [TP-2](#tp-2-implement-throttling-at-multiple-points) to implement more comprehensive controls. 

## [TP-2] Implement throttling at multiple points

Implement throttling at all points where Layer 7 load enters your system (see item 2 in [image showing throttling points in a system](./images/throttling/throttling-points.png)), **including** private entry points used by internal services. This design ensures callers can't bypass system limits. As noted in [TP-1](#tp-1-customize-protections-at-your-gateway), gateway throttling alone is usually not sufficient.

### Design your limits

After you identify the entry points, design your limits to achieve the following goals:

* Protect users or workloads from each other (also known as *noisy neighbors*). These limits are **User Limits**.
* Protect the service and its infrastructure from *all* users. These limits are **Service Limits**.

For each API, define at least one user limit and one service limit. Ideally, user limits trigger before service limits when an individual caller sends too much traffic. However, service limits can trigger even when no single user limit is breached. This situation can happen during temporary events such as zone outages, when traffic shifts and overloads the remaining partitions.

The mental model <!-- (see [image showing request dimensions](./images/throttling/throttling-request-dimensions.png))--> is that each request carries a set of dimensions (region = X, identity = Y, and so on), and throttling policies define a series of admission gates (logical AND) across those dimensions.

![Diagram showing concentric ellipses representing a request admission hierarchy. A request from a user principal passes through progressively broader limit gates: identity-based limits, Azure Resource limit, subscription-based limit, tenant-based limit, and operation-name-based limit, before being admitted.](./images/throttling/throttling-request-dimensions.png)

The following table shows a sample hierarchy of common request dimensions:

| Limit dimension | Type | Protects | Comment |
|---|---|---|---|
| Workload Identity | User | Noisy or overactive workloads from one another | Strongly recommended  |
| Server-side Resource ID | User | A cohesive set of workloads that need the same resources, protected from each other | Usually results in desirable behavior. Resource groups can work well here. |
| Subscription | User | A bigger set of workloads from each other | Usually results in desirable behavior. |
| Tenant | User | A misbehaving set of users of your service from other users | Usually results in desirable behavior. |
| Operation Name | Service | An API that is under load from taking down other APIs of your service | Strongly recommended |
| Service/Global | Service | Your infrastructure from terminal overload | Not recommended. This type of limit has a very large blast radius and can cause unnecessary outages when they get stale. Use *Operation Name* based limits instead. |
| Service Node | Service | Protects the node from being overwhelmed | Not recommended. While very effective with uniform traffic distribution, these limits can create a perception of randomness when node-level traffic is "lumpy" or the node population is heterogeneous. Use *Operation Name* based limits instead. |

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Targeted throttling improves fairness and overall user experience by limiting only the users who exceed their agreed-upon usage contracts, rather than degrading service for the entire user population. However, this approach introduces additional complexity, requiring clear contract definitions, per-user usage tracking, and the ongoing management and enforcement of differentiated rate limits across users.

Architectural constructs that are internal to the service (that is, not visible to users, such as partitions or shards) aren't good dimensions for user limits. Because users can't predict or control these constructs, they have no sensible way to respond. Also, error messages can reveal internal structural details and create security concerns, so proceed carefully. Service limits on these constructs can still be valuable, but when those limits are exceeded, communicate the condition as HTTP 503 (Service Unavailable), or an equivalent, with clear retry semantics. For more discussion, see [TP-3](#tp-3-create-backpressure).

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: There's a risk associated with doing local throttling if your system is elastic. This means throttling that counts traffic on each node. Scale-up/down operations and organic growth will admit more work than you originally designed for. This decay, over time, can overwhelm constrained backend components when you least expect it. If you do decide to use local-only throttling, ensure you pair this with operational discipline to frequently review limit utilization.

### Blocking Traffic

Implement the ability to block specific users or API operations without requiring service deployments or restarts. For specific requirements, see [TP-4](#tp-4-build-operations-to-handle-load-events).

# [RESTful services](#tab/tp2-general)

Apply the limit hierarchy in the table above. Workload Identity and Operation Name are the minimum recommended dimensions.

# [Azure OpenAI](#tab/tp2-aoai)

Azure OpenAI enforces both requests per minute (RPM) and tokens per minute (TPM). In practice, quota is allocated in TPM, and the corresponding RPM is derived from that capacity based on model-specific ratios. Design your admission control to account for both dimensions: a request can be within RPM limits and still be rejected if TPM capacity is exhausted.

Apply limits by workload tier, operation type, and deployment/model at the entry point to your service:

| Dimension | Type | Example |
|---|---|---|
| Workload identity (caller tier) | User | Separate interactive, batch, and background clients by identity |
| Operation type | Service | Chat completions vs. embeddings have very different token costs, enforced separately |
| Deployment / model | Service | Azure OpenAI quota is scoped per subscription, region, model, and deployment type, then assigned to deployments from that pool |

Token cost at admission is unknown. You don't know how many completion tokens the model will generate when you accept a request. Use prompt token count and a configured max-output cap as proxies for admission decisions. Track actual token usage from the `usage` field in the response to calibrate limits over time.

---

## [TP-3] Create backpressure

When a user or service limit is breached, the condition is typically temporary. Signal this condition to upstream callers by returning **HTTP 429 (Too Many Requests)** or **HTTP 503 (Service Unavailable)** so they can respond intelligently. Use **429** for **user limits** and **503** for **service limits**.

> [!NOTE]
> Use HTTP 429 for user-limit enforcement and HTTP 503 for service-level constraints. Include retry guidance only when retry is safe and intended.

Without this signal, callers might retry immediately, increasing traffic and causing more throttling and failures. They might return HTTP 500 to their own callers or move long-running operations into a [DLQ](https://en.wikipedia.org/wiki/Dead_letter_queue). These outcomes create unpredictable user experience and increase live site calls.

#### Signaling via 429

When a _user limit_ is exceeded, signal upstream callers to slow down by returning HTTP **429**. This code indicates that the caller exceeded its contract with your service.

At a minimum, include the following header with a 429 response:


| Header | Data Type | Discussion |
|---|---|---|
| `Retry-After` | Integer; time in seconds | The caller should wait at least this many seconds. Although RFC 9110 allows a timestamp in HTTP-date format, return a duration instead of a timestamp. In ISO 8601 terms, this duration can be thought of as *PT**n**S*. For `n = 0`, the client can retry immediately.<br /><br />Sub-second durations aren't supported because they don't have wide applicability in the kinds of systems discussed in this document. If you have a use case, contact the authors. |

In addition to the header above, return the following headers:

| Header                | Data Type     | Discussion                                                   |
| --------------------- | ------------- | ------------------------------------------------------------ |
| `RateLimit-Policy`    | String        | The set of policies that apply to this request. <br><br>Example: `RateLimit-Policy: "default";q=100;w=10`. This header indicates a throttling policy named `default` that allocates `100 requests` over a `10`-second window. See the [IETF Rate Limit Headers](https://www.ietf.org/archive/id/draft-ietf-httpapi-ratelimit-headers-10.html#name-ratelimit-policy-field) draft for the full specification. |
| `RateLimit`           | String        | The remaining capacity units and when capacity is expected to be available next. <br />Example: `RateLimit: "default";r=0;t=30`. This header indicates the `default` policy has `0` units remaining and the server expects more units to be available in `30` seconds. See the [IETF Rate Limit Headers](https://www.ietf.org/archive/id/draft-ietf-httpapi-ratelimit-headers-10.html#name-ratelimit-field) draft for the full specification. |
| `x-ms-ratelimit-used` | Number; Count | Capacity units that will be used by this call. Will be included in the RateLimit header (WIP) |

Your throttling solution should return these values to your application when a limit is breached. Using standardized headers ensures compatibility with industry-standard client middleware.

#### Signaling via 503

When a _service limit_ is exceeded, indicate this condition to upstream callers by returning HTTP **503**. This code indicates the service is temporarily constrained, unrelated to the behavior of the recipient.

With 503, you can optionally include the headers described earlier, **only if** you want callers to retry. If you're permanently rejecting traffic and don't want retries, **don't** include retry guidance headers. Unlike 429, 503 can also be returned for reasons other than service limit violations.

#### Computing Retry-After

To create effective backpressure, compute the Retry-After interval returned to clients. If your throttling solution doesn't provide a precomputed value, compute it as follows:

```
Retry-After =  Now + Throttling Window Length(W) + Randomized Jitter
```

Jitter should be randomized in the interval *(-W, +small multiple of W)* to prevent repeated waves of calls. Traffic priority, timeout lengths, the retry-attempt header (see [TP-5](#tp-5-respect-backpressure)), and typical API call durations can also influence jitter. The goal is to predict when capacity is likely to be available to service the request. Avoid returning an absolute time in Retry-After so clock skew and time-zone parsing differences don't create additional failures.

See [TP-12](#tp-12-implement-state-aware-throttling), [TP-13](#tp-13-honor-transactional-boundaries), and [TP-14](#tp-14-implement-random-early-drop) for further discussion of advanced cases.

# [RESTful services](#tab/tp3-general)

Return `429` for user limit breaches with `Retry-After` so callers back off and retry intelligently.

# [Azure OpenAI](#tab/tp3-aoai)

For provisioned deployments, Azure OpenAI documents `retry-after` and `retry-after-ms` on 429 responses. Surface these upstream with intention. Don't absorb a 429 and return a generic 500, because that strips retry guidance from a recoverable condition and turns it into a retry storm.

For streaming requests, treat interrupted streams as incomplete responses and design the client for safe retry or graceful degradation. If you rely on streamed token usage, the final usage chunk may not arrive when the stream is interrupted, so your accounting path should tolerate missing final usage and fall back to estimates or platform metrics.

---

## [TP-4] Build operations to handle load events

Throttling implementations fail for many reasons. Until software reaches a higher level of maturity, DRIs must be able to mitigate throttling events quickly by using an "observe, decide, act" loop.

Observability and DRI tooling are the two pillars of this best practice. Success is measured by how quickly DRIs can act with minimal blast radius. Your team must have the following capabilities:

1. **Observe:** Current throttling limits and their saturation.  
1. **Decide:** Identify top-N traffic for each API, aligned to existing limits.  
1. **Act:** Block traffic with the lowest collateral damage.  
1. **Safety:** DRIs act quickly and safely.  
5. **Prepare and Train:** Do you have training for DRIs? Does operational review include load management as an area of focus?

# [RESTful services](#tab/tp4-general)

DRI tooling must show current limit saturation and the top-N callers per API so you can act with minimal blast radius.

# [Azure OpenAI](#tab/tp4-aoai)

For Azure OpenAI workloads, DRI tooling must surface token consumption alongside request counts. A dashboard that shows only RPM gives an incomplete picture. For example, a single batch job sending long prompts can saturate TPM while RPM looks healthy.

Key operational signals to monitor:
- TPM and RPM utilization per deployment, broken down by caller identity and operation type
- Token cost distribution (p50/p95/p99 tokens per request) to detect prompt inflation
- Cache hit rate if a semantic cache is in use. There might be a sudden drop in cache hits multiplies token consumption directly

Ensure your DRI runbooks include steps to throttle or block a specific caller identity without redeploying the service. Model upgrades (for example, moving from GPT-4o to GPT-4.1) can change throughput characteristics. Include a post-upgrade limit validation step in your model deployment runbook.

---

## [TP-5] Respect backpressure

Because 429 signals a temporary condition, respecting backpressure by reducing downstream traffic improves overall stability. Although this state is temporary, it can last for a long time (that is, multiple request lifetimes). As a result, effective backpressure handling requires careful planning.

First, decide whether to fail the request and let your upstream caller retry, or retry it yourself. If you choose to retry it yourself, evaluate whether retriable work should be **enqueued** (durably or in memory). Keeping a thread active for the duration of a retry isn't recommended because in-flight retries can cause head-of-line blocking, where retried requests hold up requests that could be processed immediately. This also makes **declaring bankruptcy** (that is, resetting the retry queue) harder, because it might require a service restart and delay recovery.

Next, perform retries in an orderly fashion. Don't retry until the period in the retry header has elapsed. On each retry, annotate the request with a `retry-attempt` header that indicates how many times the request has been retried. Keep retry count low and pre-negotiated with your dependencies. Servers are free to return 425 (Too Early) and can escalate to 403 (Forbidden) if those expectations are violated. If a 429 response doesn't include retry headers, use exponential backoff; bounded retries for 429 are acceptable. But **do not retry a 503** unless it includes retry headers.

Finally, use the circuit breaker pattern to fail fast. Don't send traffic to a dependency that already signaled ongoing throttling. When that dependency recovers, avoid draining enqueued work all at once, because it can create a sudden inrush of downstream traffic. Gradually reintroduce traffic so downstream systems can ramp up. During reintroduction, continue using the `retry-attempt` header as noted above. If the maximum retry count is reached, reject the request by sending an HTTP 503 upstream with appropriate headers and metadata. Ensure the libraries and SDKs you use to call dependencies support these mechanisms.

# [RESTful services](#tab/tp5-general)

Respect the `Retry-After` header, enqueue rather than block threads for retries, and use circuit breakers to fail fast during sustained throttle windows.

# [Azure OpenAI](#tab/tp5-aoai)

Azure OpenAI's `retry-after-ms` header gives millisecond precision. Don't substitute a fixed backoff when a precise value is available.

For multi-tier workloads (interactive, batch, background), differentiate retry strategy by tier:

- **Interactive:** Fail fast. Retry once after `retry-after-ms`; if still throttled, return a degraded but useful response to the user rather than holding the request open.
- **Batch:** Enqueue the failed chunk durably and resume after the retry window. Don't hold threads.
- **Background:** Accept a longer delay. Use exponential backoff with jitter bounded within the retry window. Annotate retries with `retry-attempt` on each attempt.

Apply the circuit breaker only to the deployment that is throttling. If you have multiple Azure OpenAI deployments (for example, PTU for interactive and PAYG for batch), circuit-breaking the PTU deployment should not prevent background work from reaching the PAYG deployment.

Not all Azure OpenAI error responses are retryable, and your application logic must explicitly distinguish between them:

| Response | Retryable | Action |
|---|---|---|
| **429** with `retry-after-ms` | Yes | Back off for the indicated duration; always honor the `retry-after-ms` header rather than using fixed delays |
| **408** (PTU queue timeout) | Yes | The request timed out while waiting in the deployment queue. This indicates a capacity/backpressure signal. Retry with backoff or route to a spillover deployment |
| **400** with `code: content_filter` | No | The prompt or completion violated a content policy. Retrying the same content will deterministically fail. Log, surface to the caller, and stop retrying |
| **5xx** (service errors) | Yes | Transient service-side failure. Retry with exponential backoff and bounded attempts |

Treating a `content_filter` 400 as a transient failure is a common and costly anti-pattern. Blind retries on rejected content:

- Consume token and throughput capacity
- Increase queue depth on PTU deployments
- Amplify latency for unrelated requests
- Can indirectly contribute to 408 timeouts under load

The `innerError.code` value `ResponsibleAIPolicyViolation` is the authoritative programmatic signal to identify these cases and prevent retries.

In high-throughput systems, correct the classification of retryable vs. non-retryable errors. This is important to maintain system stability under load.

---

## [TP-6] Protect internal components

While entry-point throttling provides admission control, throttling traffic between subcomponents such as databases, caches, and queues is also essential (see item 3 in [image showing throttling points in a system](./images/throttling/throttling-points.png)).

Throttling in this layer allows each subcomponent to protect itself. Assume breach from other subcomponents, and make no assumptions about their behavior under load. This approach creates logical "cells" inside the system that are relatively isolated from one another and protected from bugs in other components. In particular, focus on components that are **shared** across different types of work your service performs. Protecting these cells can significantly improve recovery time.

## [TP-7] Implement egress bulkhead when calling dependencies

Egress bulkheads are necessary to limit the maximum number of concurrent in-flight calls (see item 4 in [image showing throttling points in a system](./images/throttling/throttling-points.png)). While each dependency should protect itself, those protections might not always be in place.

Traffic to dependencies traverses Layers 3 and 4 systems (often shared, such as load balancers and switches) before reaching Layer 7, where throttling might occur. As a result, congestion can happen before application-level throttling kicks in.

At small timescales, requests don't arrive uniformly within the throttling aggregation window. A short surge might be acceptable for your system but can still overwhelm downstream infrastructure (queues fill, sockets exhaust, and downstream systems destabilize).

Egress bulkheads help by flattening traffic and reducing variance between your service and your dependencies, which increases overall stability. Ensure your client SDK supports egress bulkheads.

Watch out for fan-out points: places where one incoming unit-of-work turns into multiple requests (batch calls, queue processors, workflows, bulk DB updates). Bulkheads here require attention to call ordering, expiration, and recovery.

# [RESTful services](#tab/tp7-general)

Egress bulkheads limit concurrent in-flight calls to dependencies. Apply them at fan-out points where one unit of work becomes many downstream requests.

# [Azure OpenAI](#tab/tp7-aoai)

Document processing is the most common fan-out source in AI workloads: a single document or conversation thread is split into chunks and each chunk becomes a separate completion or embedding call. Without a bulkhead, one large document submission can exhaust the TPM budget for an entire deployment in seconds.

Apply the bulkhead at the chunking layer, not just at the API gateway:

- Cap maximum concurrent in-flight completion calls per document or job, not just per caller
- Limit the token rate of the chunking pipeline to a fraction of the total TPM budget, reserving headroom for interactive requests
- For embedding fan-out, use batched embedding calls where possible rather than one call per chunk. This can reduce RPM consumption significantly at similar TPM cost

Be aware that streaming completions hold a socket open for the duration of token generation. High concurrency of streaming requests can exhaust connection pools before TPM limits are reached.

---

## [TP-8] Don't use caching to replace throttling

Some services implement a cache to improve resilience or latency, which can reduce steady-state load. This approach can appear to work, but it can fail when caller traffic creates cache misses and drives unplanned load from a capacity perspective.

Use caching when it helps, but keep throttling and bulkheads in place for worst-case miss scenarios. This approach also helps reduce the impact of cache-busting DoS attacks. Also account for cold-start cache hydration: restarting a partition can create temporary load on working partitions. Caches provide real benefits, but they also add architectural complexity.

# [RESTful services](#tab/tp8-general)

Cache to improve latency and steady-state load, but set throttling limits that hold at worst-case cache miss rates, not average-case.

# [Azure OpenAI](#tab/tp8-aoai)

Semantic caching returns a cached response when an incoming prompt is sufficiently similar to a previously answered one. This technique can significantly reduce TPM consumption in steady state. However:

- Cache hit rates collapse when query diversity is high (long-tail questions, personalized prompts, or low similarity thresholds)
- A newly deployed model invalidates the entire cache, creating a cold-start period where every request misses
- Cache-busting is trivially easy: rephrasing a question slightly defeats similarity matching

Set your TPM limits to handle the full uncached request volume. Treat the cache as a cost optimization, not a capacity guarantee. This also prevents a cache miss spike from becoming an outage.

---

## [TP-9] Keep throttling accounting out of critical path

Regardless of the throttling service you use, report usage and retrieve decisions **asynchronously**. This approach improves resilience because the system can still function if the throttling service fails. Consider dedicating a thread in your application to perform throttling accounting and maintain enforcement decisions that request-processing threads can read quickly. The application's critical paths can submit usage to this thread and **synchronously** check the decisions it provides.

# [RESTful services](#tab/tp9-general)

Perform throttling accounting asynchronously so that a slow or unavailable throttle store doesn't block the request critical path.

# [Azure OpenAI](#tab/tp9-aoai)

Token accounting in Azure OpenAI workloads has a timing asymmetry: you can estimate prompt tokens at admission time, but completion token count is only known after the response completes (or after the stream closes for streaming responses). This means exact TPM accounting is always retrospective.

> [!NOTE]
> Token accounting is partially retrospective: completion token usage is known only after response completion (or stream close). Design admission and reconciliation paths accordingly.

Design your accounting layer accordingly:

- At request admission, reserve an estimated token budget based on prompt length and a configured max-output cap
- After response completion, reconcile actual token usage from the `usage.total_tokens` field in the response and adjust the running counter. For streaming requests, enable `include_usage` where available so the final streamed chunk carries token statistics for the full request. If the stream is interrupted, that final usage chunk may not arrive. Your accounting path should tolerate missing final usage and fall back to estimates or platform metrics.
- Keep this accounting in a sidecar or background thread. Don't block the response path waiting for the reconciliation to commit
- If the accounting service is unavailable, fail open with a conservative estimate rather than blocking all traffic; alert and restore normal accounting as quickly as possible

---
## [TP-10] Pay attention to cross-cutting infrastructure

Cross-cutting dependencies such as DNS zone updates, often group traffic for throttling differently than your service does. These dependencies often have little or no business logic and are assumed to be infinitely scalable, then forgotten. However, a single consumer can push this shared infrastructure into the red zone and cause throttling for many unrelated users. As a result, make sure that:

* Your service does not overwhelm such a service; apply [TP-7](#tp-7-implement-egress-bulkhead-when-calling-dependencies) to avoid broader-than-necessary impact.
* You have a plan for when a cross-cutting dependency is unavailable. Caching "last known good" responses and using pre-decided brownouts can be effective techniques.

Be suspicious of dependencies that don't throttle at all. They might silently hit limits and return 500s to all users without warning.

## [TP-11] Reassess, tune, and validate periodically

Periodically revisit configuration to ensure it reflects current usage patterns across your system. Ideally, do this as part of a weekly live-site review, using a dedicated dashboard that shows current limits and both real-time and historical utilization (high and low watermarks). Use automation. Ensure incident reviews include this usage-pattern analysis.

**Validation techniques:** Run periodic testing by setting low limits in throttling policies or using Chaos Studio to ensure 429 handling behavior and DRI triage capability haven't regressed. Other testing includes:
1. Recreate the last N outages (N decided by the service owner).
2. Load test least-used APIs
3. Load test most expensive APIs

At a minimum, strongly consider coupling at least two of these tests with zonal outage drills.

Creating replica environments with mocked dependencies and AI-driven traffic generators can validate non-functional goals (performance and resiliency) and is recommended as a long-term investment.

# [RESTful services](#tab/tp11-general)

Periodically review limits against current usage patterns. Static limits decay as traffic evolves. Validate through controlled low-limit testing.

# [Azure OpenAI](#tab/tp11-aoai)

AI workloads have an extra decay trigger that classic REST services don't have: **model upgrades**. Moving from one model version to another (for example, GPT-4o to GPT-4.1) can significantly change throughput characteristics, cost per token, and output length distribution. These changes make previously calibrated limits incorrect. Because Azure OpenAI metrics are tracked by model and model version, include post-upgrade validation in your runbooks. Compare throughput, token usage, and latency before and after each model version change.

Build model upgrades into your operational review process:

- Re-run low-limit chaos tests after each model deployment, not just after infrastructure changes
- Update token cost estimates in your accounting layer to reflect the new model's output length distribution
- Track prompt and completion token counts per model version in your observability tooling so you can compare before and after

Also plan for quota changes. Azure OpenAI quota allocations per region and model change as Microsoft adds capacity. Validate after quota changes in both directions to ensure limits remain correctly calibrated.

---

## [TP-12] Implement state-aware throttling

Throttling that reacts to system state reallocates scarce capacity in alignment with business goals. Techniques include:

- **Dynamic throttling:** Modulate nominal cost based on operation type (read vs. write) or current state (queue length, memory).
- **Bankruptcy:** For asynchronous systems, re-evaluate queued work when cost or state changes. Be able to drop enqueued work (declare bankruptcy). Deadlines and LIFO prioritization can help.
- **Brownouts:** Pre-decided degraded modes when health metrics exceed thresholds (disable low-priority operations, return shorter results, and so on).
- **Bursting and borrowing:** Allow short-term consumption above baseline. Borrowing uses spare capacity from others. Both require careful fairness controls.

# [RESTful services](#tab/tp12-general)

Throttling that reacts to real-time system state, rather than fixed limits, allocates scarce capacity in alignment with business priorities.

# [Azure OpenAI](#tab/tp12-aoai)

Azure OpenAI workloads have strong, natural cost differentiation that makes state-aware throttling especially valuable:

> [!TIP]
> Under pressure, prioritize preserving interactive user progress first, then degrade gracefully (brownout), and shed low-priority/background work before hard-failing all traffic.

- **Dynamic cost by operation:** A chat completion with a 4,000-token context costs roughly 40× more TPM than a short classification prompt. Assign different token-cost weights to operation types and throttle against the weighted budget, not a flat request count.
- **Brownout mode:** Under sustained TPM pressure, reduce the `max_tokens` parameter on interactive requests (for example, cap at 512 instead of 4,096). Users receive shorter but timely responses rather than a hard rejection. Apply brownouts to the interactive tier first; shed batch and background work entirely before degrading interactive quality further.
- **Bankruptcy for batch queues:** An overnight summarization job assembles a queue of documents before processing starts. If TPM pressure is high when the job runs, re-evaluate the queue: drop stale items (documents already superseded by newer versions), deprioritize low-value summaries, and process only what's needed before the next business day starts. Holding the full queue and retrying everything amplifies load; declaring partial bankruptcy reduces it.
- **Bursting with PAYG overflow:** Where applicable, design your workload to redirect overflow traffic to a PAYG deployment when your primary PTU deployment approaches capacity. PTU gives predictable throughput at fixed cost; PAYG can absorb short bursts at higher per-token cost. Set a spend ceiling and time limit on PAYG overflow to prevent sustained cost escalation during prolonged burst events.

---

## [TP-13] Honor transactional boundaries

For service APIs with transactional semantics, ensure throttling can prioritize in-flight transactions over starting new ones. Throttling sub-operations of a transactional API can cause transaction failure or rollback, which wastes resources and degrades user experience. You can implement this behavior through special transaction identifiers passed with the API call, although more natural identifiers might exist in your use case. This approach requires careful design and application changes.

# [RESTful services](#tab/tp13-general)

Prioritize completing in-flight transactional operations over admitting new ones to avoid wasting partially consumed resources.

# [Azure OpenAI](#tab/tp13-aoai)

A multi-turn conversation is a logical transaction in AI workloads. By the time the user's message arrives, the system has already retrieved context, assembled a prompt, and consumed tokens for retrieval. Throttling the completion call at this point wastes all of that upstream work and delivers a worse experience than either completing the turn or gracefully brownout-capping the response length.

Track conversation identifiers through your throttling layer, and apply these rules:
- Prioritize completion calls that are part of an in-progress conversation over new conversation starts when TPM is constrained.
- If you must throttle mid-conversation, prefer reducing `max_tokens` (brownout; see [TP-12](#tp-12-implement-state-aware-throttling)) over returning a 429 that forces the client to replay the full request.
- For agent tasks that span multiple model calls (plan-then-act patterns), treat the full task as a transaction: don't admit a new task unless you can guarantee enough TPM headroom to complete it.

---

## [TP-14] Implement random early drop

As aggregate usage approaches high utilization limits (see [Image showing resource utilization change with load](./images/throttling/throttling-effect.png)), start rejecting a carefully chosen fraction of traffic. A positive value for remaining tokens in the `RateLimit` header signals to callers that their traffic is in this category and that the system is nearing limits. Benefits: (1) prevent the system from reaching the red zone, and (2) validate that upstream partners remain ready to respect constraints.

# [RESTful services](#tab/tp14-general)

Start shedding a fraction of traffic early as aggregate usage approaches limits, before hitting the hard ceiling.

# [Azure OpenAI](#tab/tp14-aoai)

The key difference from the general case is that the early drop signal should be token-based, not request-based. An RPM-only early drop is ineffective. For example, ten short prompts may consume less TPM than one long one.

Use your own token accounting layer (see [TP-9](#tp-9-keep-throttling-accounting-out-of-critical-path)) as the primary early-drop signal. As tracked TPM consumption approaches the deployment quota:

- Begin probabilistically rejecting new **batch and background** requests with a 429 and a short `Retry-After`.
- Continue serving interactive requests at full rate until a lower threshold is reached.
- This approach preserves the best possible experience for interactive users during load surges without waiting for Azure OpenAI to return a hard 429.

Calibrate your high-watermark thresholds using Azure Monitor's token usage metrics. Use Processed Prompt Tokens and Processed Inference Tokens,  rather than derived request counts.

---

## Related links

- [Throttling design pattern](/azure/architecture/patterns/throttling)

## Community links
- [https://en.wikipedia.org/wiki/Dead_letter_queue](https://en.wikipedia.org/wiki/Dead_letter_queue)
- [RFC 9110 HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110.html?__prettifying=true#section-10.2.3)
