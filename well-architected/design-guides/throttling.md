---
title: Implement a throttling design pattern
description: Learn how to design throttling as an architectural capability that preserves reliability, fairness, and performance under normal cloud overload conditions.
author: 
ms.author: pgupt, krnese
ms.date: 03/18/2026
ms.topic: concept-article
ms.update-cycle: 1095-days  
---

# Improve your service availability under load

Throttling is a design pattern that controls how much load your system can accept at a given time. In distributed systems, traffic spikes, retry storms, fan-out patterns, and shared downstream dependencies all create conditions where uncontrolled demand eventually exhausts capacity. When a component reaches capacity, the throttling mechanism helps reject or delay additional requests until the system is ready again. Otherwise, latency can increase, retries amplify load, and failures can spread through the system.

A common architectural approach is to apply throttling only at the gateway to protect ingress. An isolated throttling mechanism may protect one component while shifting overload to other parts of your application, increasing latency and degrading customer experience.

In  well-architected design, apply the pattern across the system rather than only at the edge. Consider where your service shares capacity across multiple callers, exposes resources that can be exhausted, or participates in a call chain where overload in one component can cascade to others. All those points are potentially good candidates for throttling.  Effective throttling requires coordinated behavior across retries, queues, backoff policies, load shedding, and downstream dependencies. You should treat overload as a normal operating condition: expect it, monitor it, and actively control how your system responds when capacity limits are reached.

Throttling buys time for reactive responses during events like scaling operations, on-call investigation, circuit breakers. Static limits calibrated last quarter may no longer reflect today's traffic patterns.

![alt text](./_images/throttling/image.png)

This article guides you through throttling best practices. Start with the fundamentals first, because every workload follows the same core principles. The principles are presented in tabs, and you should review all of them before you tune limits in your design.

After you understand the principles, use the best-practice represented as TPs as implementation guidance. Each TP is a discrete guidance unit and appears in numeric order.

The guide also presents levels of adoption. Use the lower levels to implement baseline implementation and optimize as your workload matures over time.


## Types of throttling

Each throttling type protects a different part of your architecture. Place each one at the boundary where it can directly control load. The table maps the types to the architectural layers used in the practices below.

| Type | What it controls | Where it lives | Relevant practices |
|---|---|---|---|
| **Ingress throttling** | Request rate and volume entering your system | API gateway, public and private entry points | TP-1, TP-2 |
| **Internal throttling** | Traffic between your sub-components — databases, caches, queues, and shared services | Service-to-service calls inside your boundary | TP-6 |
| **Egress throttling** | Traffic your system sends to downstream dependencies | Client SDKs, fan-out points, outbound call paths | TP-7 |
| **State-aware throttling** | Limits that adjust based on real-time system health, operation cost, or queue depth | Throttle policy engine, admission control layer | TP-12, TP-13, TP-14 |

Most workloads need all four types working together. If you only throttle ingress, you protect the front door but push overload deeper into your application. Internal and egress controls stop that spread. State-aware throttling lets your system adjust as conditions change instead of running with stale fixed limits.

## Throttling fundamentals

No matter which throttling types your workload uses, you still need the same core fundamentals. If you're new to throttling, read these principles in order so you understand *why* each practice exists before you implement *how*. If you're reviewing an existing design, use the design questions under each principle as a checklist to find gaps.

| Terminology | Description |
|---|---|
| **Boundary** | Boundary is the component group your throttling protects, usually represented by an entry point. |
| **Dimension** | Dimension is a name/value pair from request metadata that a throttling rule evaluates. |
| **Upstream** | Upstream is the caller that sends requests to your service. |
| **Downstream** | Downstream is the dependency your service calls. |

### [**Principle 1**](#tab/level1)

![Goal icon](../_images/goal.svg) **Design for overload as a normal operating mode**

In distributed systems, overload is expected, not exceptional. Throttling is the control loop that protects shared dependencies and preserves your SLOs under load. Design your system assuming traffic surges, retries, and cascading effects will happen.

#### Design Questions

Use these when reviewing an existing design or assessing where your system is exposed to overload risk.

* What really saturates this system and impacts SLOs - Requests, concurrency, CPU, IO, queue depth, or downstream quotas?
* Where does load enter, amplify (fan-out), or cascade?
* For each entrypoint, what are the explicit boundaries of impact (gateway, shard, dependency)?
* What is the appropriate overload signaling mechanism between components?
* When (not if) overload happens, how will the system heal and recover?

#### Design Recommendations

* Define throttling boundaries (i.e., where throttling occurs) and throttling dimensions (i.e., what limits are enforced) at design time and ensure that these are aligned with saturation vectors.
* Enforce limits at every entry point, including low-priority and read-only APIs
* Control concurrency at fan-out points
* Are components of this system ready to respect protocol-specific signals of overload
* Treat throttling as a lifecycle: measure → enforce → learn → adapt.

#### Relevant Best Practices
* [TP-1](#tp-1-customize-protections-at-your-gateway)
* [TP-2](#tp-2-implement-frontendgatewayingress-throttling)
* [TP-3](#tp-3-create-backpressure-return-429s-with-retry-after-header-to-your-callers)

### [**Principle 2**](#tab/level2)

![Goal icon](../_images/goal.svg) **Design how your dependencies respond to overload to prevent cascading failures**

Account for the upstream (systems that call the service), downstream (systems called by the service) and shared components. Having a well design interaction mechanism that can handle overload conditions improves overall system stability. Poor signaling amplifies retries and cascades failures.

#### Design Questions

Use these when assessing how well your system's components signal and respond to overload across boundaries.

* What are the interaction patterns and protocols used between components, and do they have appropriate signaling mechanisms?
* Are internal components and downstream systems protected from overload?
* Are shared components or highly leveraged components protected from overload?
* Are all components able to signal they are overloaded? Are back-pressure signals in place to aid recovery?
* Are we teaching callers how to behave correctly under load, or forcing blind retry loops?

#### Design Recommendations

* Choose well-understood architectural patterns and use standardized protocols that support overload signaling.
* Ensure components can protect themselves via back-pressure and overload signals are respected
* Ensure overload signals from downstream services are not hidden or made opaque
* Ensure component interactions allow recovery to happen by pausing or other compensating mechanisms
* Return information to customers that allows them to retry intelligently

#### Relevant Best Practices
* [TP-5](#tp-5-respect-backpressure-when-your-dependencies-return-429-slow-down-your-rate-and-respect-retry-after-headers)
* [TP-6](#tp-6-implement-further-internal-controls-guard-internal-components-3-in-figure-2-above)
* [TP-7](#tp-7-implement-egress-bulkhead-when-calling-dependencies-dont-overwhelm-services-you-depend-on-especially-at-fan-out-points-4-in-figure-2-above)
* [TP-8](#tp-8-a-cache-does-not-replace-a-throttle-avoid-accidentally-even-relying-on-caching-as-replacement-for-throttling)
* [TP-9](#tp-9-keep-throttling-accounting-out-of-critical-path)
* [TP-10](#tp-10-pay-particular-attention-to-horizontalcross-cutting-infrastructure)

### [**Principle 3**](#tab/level3)

![Goal icon](../_images/goal.svg) **Design throttling and boundaries around business requirements, not infrastructure convenience**

Throttling impacts user experience. Limits must reflect what's critical to the businesses and the tolerated blast radius, not internal topology convenience. Hard stops waste available capacity and increase friction. 

#### Design Questions

Use these to evaluate whether your throttling design respects customer experience, not just infrastructure health.

* Who feels the limit when it triggers? Is the blast-radius wide or narrow? Is this intentional?
* Are throttling boundaries and dimensions aligned with customer expectations and mental models?
* Can we enable customers to make progress if system capacity and state allow it?
* Do customers consider API responses to be opaque rejections without clear actionable information?

#### Design Recommendations

* Be aware of "logical" transaction boundaries as experienced by the customer
* Allow temporary elasticity even if it is temporarily unfair, provided it does not compromise stability
* Where possible, factor in the cost of servicing the request and build awareness of the overall system state/available capacity into throttling decisions
* Build in the ability to offer brown outs, where systems trade off completeness in favor of availability

#### Relevant Best Practices
* [TP-2](#tp-2-implement-frontendgatewayingress-throttling)
* [TP-3](#tp-3-create-backpressure-return-429s-with-retry-after-header-to-your-callers)
* [TP-12](#tp-12-implement-state-aware-throttling-implement-throttling-that-reacts-to-system-state)
* [TP-13](#tp-13-honor-transactional-boundaries)
* [TP-14](#tp-14-implement-random-early-drop)

### [**Principle 4**](#tab/level4)

![Goal icon](../_images/goal.svg) **Treat throttling with a continuous evolution mindset**

Throttling effectiveness decays as traffic patterns evolve. Static limits become incorrect over time. Regular measurement, testing, and refinement processes keep throttling boundaries and dimensions aligned with reality.

#### Design Questions

Use these to evaluate whether your team treats throttling as a living operational discipline rather than a one-time configuration.

1. Is throttling treated as a point in time exercise? Or is it treated as a lifecycle and evidence-driven configuration flywheel: measure → enforce → learn → adapt?
2. Are overload events practiced and rehearsed?
3. Is telemetry rich enough to diagnose saturation quickly?
4. When was the last time we validated that our limits still reflect reality?

#### Design Recommendations

1. Treat throttling configuration as adaptive and evidence driven. Regularly review top-N traffic and saturation/utilization patterns that feed processes (automated or manual) for refreshing throttling limits.
2. Validate limits through controlled testing and low-limit exercises for your entire API surface.
3. Develop and practice DRI responses to overload events, prioritizing recovery time over all other considerations.

#### Relevant Best Practices
* [TP-4](#tp-4-operational-excellence-part-1-are-your-dris-able-to-handle-load-events-operationally)
* [TP-11](#tp-11-operational-excellence-part-2-reassess-tune-and-validate-periodically)

---

![alt text](./_images/throttling/image-1.png)

> **Token-Aware QoS Control Loop**
> In AI workloads, the primary saturation vector shifts from request rate to token throughput — capacity becomes proportional to prompt and response size rather than request count. The Azure OpenAI tabs throughout this section apply the principles above through that lens.

## [TP-1] Customize protections at your gateway

TBD - rewrite in an arm agnostic way

## [TP-2] Implement Frontend/gateway/ingress throttling

Implement throttling at all points where traffic enters the your system (#2 in Figure 2 above), **including** any private entry points used by internal services. This ensures that it’s not possible to ever bypass the limits for the system. As noted in TP-1, throttling at gateways is usually not sufficient.

### Design your Limits 

Once the entry points have been identified, design your limits to achieve the following:

* Protect users or workloads from each other (a.k.a., *noisy neighbors*). We call these **User Limits**
* Protect the service and its infrastructure from *all* users. We call these **Service Limits** 

For each API, you must have atleast one User and one service limit. Obviously, in the case where users send too much traffic, we want User limits to kick in before Service Limits. But Service limits may be triggered without any user limit being triggered. This can happen when the system undergoes temporary events like zone outage, and traffic shifts to overload remaining partitions. 

<!---
from an appropriate hierarchy relevant to your service, with at least 2 limits for each API endpoint: One that protects users from each other (a.k.a., User Limits) and limits that protect the service from all users (a.k.a., Service Limits). 

* **User Limits**: These focus on the impact of throttling on the noisy . Subscription and/or (subscription + workload) identifier is recommended.
* **Service Limits**: These protect the service, irrespective of impact on customers.
--->

The mental model here (visualized in Figure 3) is that each request has a set of dimensions (region = X, identity = Y, etc.) and throttling policies define a series of admittance gates (logical AND) for those dimensions.

![alt text](./_images/throttling/image-2.png)

The following table shows a sample hierarchy of common request dimensions:

| Limit dimension | Type | Protects | Comment |
|---|---|---|---|
| Workload Identity | User | Misbehaving workload from each other | Strongly recommended  |
| Server side Resource Id | User | A cohesive set of workloads needing the same resources from each other | Usually results in desirable behavior. Resource groups can work well here. |
| Subscription | User | A bigger set of workloads from each other | Usually results in desirable behavior. |
| Tenant | User | A misbehaving set of users of your service from other users | Usually results in desirable behavior. |
| Operation Name | Service | An API that is under load from taking down other APIs of your service | Strongly recommended |
| Service/Global | Service | Your infrastructure from terminal overload | Not recommended. This type of limit has a very large blast radius and can cause unnecessary outages when they get stale. Use *Operation Name* based limits instead. |
| Service Node | Service | Protects the node from being overwhelmed | Not recommended. While very effective in the face of uniform traffic distribution, these create a perception of randomness when node-level traffic is "lumpy" or node population is heterogenous. Use *Operation Name* based limits instead. |

<!---
User and Service limits form a spectrum where capacity allocation (and therefore QoS) gets incrementally worse and further away from your promise to the customer. You should have at least one of each type but the more the better.
-->

Architectural constructs that are internal to the service (i.e., not visible to customers, e.g., partitions/shards) are NOT good dimensions to place user limits on. Since customers cannot predict/control them, they have no sensible way to respond. Further, error messages returned by the service can reveal internal structural information and may have security implications; proceed carefully. Placing service limits on these constructs is a great idea but exceeding such limits should be communicated as HTTP 503 (Service Unavailable) or equivalent, with clear retry semantics. See [TP-3] for further discussion.

If your system is elastic, do not rely on local throttling (i.e., throttling that counts traffic on each node) alone – scale-up/down operations and organic growth will admit more work than you originally designed for. This decay, over time, can overwhelm constrained backend components when you least expect it. If you do decide to use local-only throttling, ensure you pair this with operational discipline to frequently review limit utilization.

### Blocking Traffic

Implement the ability to completely block specific customers and specific API operations without requiring service deployments and restarts; See TP-4 for specific requirements. 

### [General](#tab/tp2-general)

Apply the limit hierarchy in the table above. Workload Identity and Operation Name are the minimum recommended dimensions.

### [Azure OpenAI](#tab/tp2-aoai)

Azure OpenAI enforces both requests per minute (RPM) and tokens per minute (TPM). In practice, quota is allocated in TPM, and the corresponding RPM is derived from that capacity based on model-specific ratios. Design your admission control to account for both dimensions — a request can be within RPM limits and still be rejected if TPM capacity is exhausted.

Apply limits by workload tier, operation type, and deployment/model at the entry point to your service:

| Dimension | Type | Example |
|---|---|---|
| Workload identity (caller tier) | User | Separate interactive, batch, and background clients by identity |
| Operation type | Service | Chat completions vs. embeddings have very different token costs — enforce separately |
| Deployment / model | Service | Azure OpenAI quota is scoped per subscription, region, model, and deployment type, then assigned to deployments from that pool |

Note that **token cost at admission is unknown** — you don't know how many completion tokens the model will generate when you accept the request. Use prompt token count and a configured max-output cap as a proxy for admission decisions and track actual token usage from the `usage` field in the response to calibrate limits over time.

---

## [TP-3] Create Backpressure: Return 429s with Retry-After header to your callers

When a user or service limit is breached, this is a temporary event, and signaling this to your upstream callers via **HTTP 429 (Too Many Requests)** or **HTTP 503 (Service Unavailable)** allows them to respond intelligently. Use **429** for **User limits** and **503** for **Service limits**.

Without this signal, they may take the failure as a signal to retry right away, leading to increased traffic (which in turn creates more throttling and failures). They may return HTTP 500 to their callers or put long running operations in a [DLQ](https://en.wikipedia.org/wiki/Dead_letter_queue). All of these will lead to unpredictable customer experience and/or increased live site calls.

#### Signaling via 429

When a <ins>user limit</ins> is exceeded, signal your upstream to slow down via HTTP return code **429**. This code indicates that the caller is exceeding their contract with your service.

As part of the 429 return code, your service must return the following header at a minimum: 


| Header | Data Type | Discussion |
|---|---|---|
| `Retry-After` | Integer; Time in seconds | Caller should wait for at least this long in seconds; While [RFC 9110](https://www.rfc-editor.org/rfc/rfc9110.html?__prettifying=true#section-10.2.3) allows a timestamp in the form of HTTP Date, we recommend to avoid time stamps and return a duration. In terms of ISO‑8601, this can be thought of as *PT**n**S*. For `n = 0`, the client is free to retry right away.<br /><br />Sub-second durations are not supported since they don't have wide applicability in kinds of systems discussed in this document. If you have a use-case, please contact the authors. |

In addition to the above, it is strongly recommended your service returns the following headers:

| Header                | Data Type     | Discussion                                                   |
| --------------------- | ------------- | ------------------------------------------------------------ |
| `RateLimit-Policy`    | String        | The set of policies that apply to this request. <br><br>Example: ` RateLimit-Policy: "default";q=100;w=10`. This says that there is a throttling policy called `default`, which allocates `100 requests` over a `10 second` window. See [IETF Rate Limit Headers](https://www.ietf.org/archive/id/draft-ietf-httpapi-ratelimit-headers-10.html#name-ratelimit-policy-field) draft for full specification. |
| `RateLimit`           | String        | The capacity units that remain and when capacity is expected to be available next. <br />Example: `RateLimit: "default";r=0;t=30`. This says the throttling policy called `default` has `0` units of capacity remaining and the server expects to have more units available in `30 seconds`. See [IETF Rate Limit Headers](https://www.ietf.org/archive/id/draft-ietf-httpapi-ratelimit-headers-10.html#name-ratelimit-field) draft for full specification. |
| `x-ms-ratelimit-used` | Number; Count | Capacity units that will be used by this call. Will be included in the RateLimit header (WIP) |

If in current use, it is recommended to move away (in a backward compatible manner) from the following headers in favor of the ones above.

| Header                      | Data Type          | Discussion                 |
| --------------------------- | ------------------ | -------------------------- |
| `x-ms-ratelimit-retryafter` | HTTP Date, Integer | MS proprietary, not needed |
| `x-ms-ratelimit-used`       | Number; Count      | MS proprietary, not needed |
| `x-ms-ratelimit-remaining`  | Number; Count      | MS proprietary, not needed |

Your throttling solution should return these to your application when a limit is breached. Using standardized headers will ensure compatibility with industry standard middleware being used on the client side.

#### Signaling via 503

When a <ins>service limit</ins> is exceeded, indicate this to your upstream callers by HTTP return code **503**. This code indicates that the service is in a temporarily constrained mode, unrelated to the behavior of the recipient of the 503.

Along with 503, you can optionally include the headers above, **only if** you would like callers to retry. If you are permanently rejecting their traffic and do not wish them to retry, **do not** include any headers. Unlike 429, you can return 503 for reasons other than service limits violation.

<!--

### Signaling for gRPC
Similar standardization TBD.
-->

#### Computing Retry-After

In order to be effective in creating back pressure, you will need to compute the Retry-After interval to return to your clients. If your throttling solution does not provide a pre-computed retry-after value, you can compute this as follows:

$$\text{Retry-After}=\text{Now}+\text{Throttling Window Length }(W)+\text{Randomized Jitter}$$

Note that jitter should be randomized in the interval *(-W, +small multiple of W)*, to prevent creating repeated waves of calls. Traffic priority, Timeout lengths, retry-attempt header (See TP-5) and typical API call durations can also play a role in additionally modifying jitter. At the root of it, we are trying to predict when you will have capacity available to service this request. Avoid sending back absolute time in Retry-After, so that clock synchronization differences between client and server, and time-zone parsing don’t come into play.

See [TP-12, 13, 14] for further discussion of advanced cases.

# [General](#tab/tp3-general)

Return `429` for user limit breaches with `Retry-After` so callers back off and retry intelligently.

# [Azure OpenAI](#tab/tp3-aoai)

For provisioned deployments, Azure OpenAI documents `retry-after` and `retry-after-ms` on 429 responses. Surface these upstream faithfully — don't absorb a 429 and return a generic 500, because that strips retry guidance from a recoverable condition and turns it into a retry storm.

For streaming requests, treat interrupted streams as incomplete responses and design the client for safe retry or graceful degradation. If you rely on streamed token usage, the final usage chunk may not arrive when the stream is interrupted, so your accounting path should tolerate missing final usage and fall back to estimates or platform metrics.

---

## [TP-4] Operational Excellence (Part 1): Are your DRIs able to handle load events operationally?

**Rationale:** Throttling implementations fail for a variety of reasons, many of which don’t have anything to do with misbehaving customers. Until software achieves a higher level of maturity, DRIs need to be able to mitigate throttling events quickly using a “Observe, decide, act” loop.

Observability and DRI tooling are the two pillars of this best practice and being able to at quickly with a minimal blast radius is the measure of success. The following are key capabilities that must exist in your team:

1. **Observe:** Current throttling limits and their saturation  
2. **Decide:** Can you identify Top-N traffic for each API, aligned with the limits in place  
3. **Act:** Block traffic with lowest collateral damage  
4. **Safety:** DRIs act quickly AND safely  
5. **Prepare and Train:** Do you have training for DRIs? Does operational review include load management as an area of focus?

# [General](#tab/tp4-general)

DRI tooling must show current limit saturation and the top-N callers per API so you can act with minimal blast radius.

# [Azure OpenAI](#tab/tp4-aoai)

For Azure OpenAI workloads, DRI tooling must surface token consumption alongside request counts. A dashboard that shows only RPM gives an incomplete picture — a single batch job sending long prompts can saturate TPM while RPM looks healthy.

Key operational signals to monitor:
- TPM and RPM utilization per deployment, broken down by caller identity and operation type
- Token cost distribution (p50/p95/p99 tokens per request) to detect prompt inflation
- Cache hit rate if a semantic cache is in use — a sudden drop in cache hits multiplies token consumption directly

Ensure your DRI runbooks include steps to throttle or block a specific caller identity without redeploying the service. Model upgrades (for example, moving from GPT-4o to GPT-4.1) change throughput characteristics entirely — include a post-upgrade limit validation step in your model deployment runbook.

---

## [TP-5] Respect Backpressure: When your dependencies return 429, slow down your rate and respect Retry-After headers

**Rationale:** Since 429 signals a temporary condition, respecting backpressure by reducing traffic sent downstream will lead to better overall stability. While this is a temporary state, it can last for a “long” (i.e. multiples of request lifetimes) time. As a result, respecting backpressure requires some careful planning. 

First, you should evaluate if you want to fail the request and have your upstream retry or retry yourself. If you choose the latter, you should evaluate if you should **enqueue** work (durably or in-memory) that must be retried. Keeping a thread active for the duration of the retry is not recommended because these in-flight requests can cause head-of-line blocking - a phenomenon where requests that are being retried hold up requests that could be be processed immediately. This makes it harder when **declaring bankruptcy**, (i.e., resetting the retry queue) since this now requires a service restart, which delays recovery.

Next, conduct retries in an orderly fashion. Do not retry the request until the period indicated in the retry header has expired. When you retry, annotate the request with a `retry-attempt` header showing how many times this request has been retried. Retry count should be limited to a low number, pre-negotiated with your dependencies. Remember that servers are free to return 425 (Too early) and may escalate to 403 (Forbidden) if violated. If there are no retry headers for 429, use exponential backoff, i.e., it’s always ok to (bounded) retry a 429. But **do not retry a 503** unless it has the retry headers.

Finally, use the circuit breaker pattern to fail fast – do not send traffic to a dependency if it has already signaled that throttling is still going on. When dependency recovers, draining the enqueued work all at once should be avoided, since this can lead to sudden inrush of traffic for the downstream system. A gradual reintroduction of traffic allows downstream systems to ramp up. Even with circuit breakers, as reintroduction begins, use the `retry-attempt` header as noted above. If max retry count is reached, reject the request by sending a HTTP 503 upstream, with appropriate headers/meta-data. Ensure that the libraries and SDKs you use to call your dependencies can support these mechanisms.

# [General](#tab/tp5-general)

Respect the `Retry-After` header, enqueue rather than block threads for retries, and use circuit breakers to fail fast during sustained throttle windows.

# [Azure OpenAI](#tab/tp5-aoai)

Azure OpenAI's `retry-after-ms` header gives millisecond precision. Use it — don't substitute a fixed backoff when a precise value is available.

For multi-tier workloads (interactive, batch, background), differentiate retry strategy by tier:

- **Interactive:** Fail fast. Retry once after `retry-after-ms`; if still throttled, return a degraded but useful response to the user rather than holding the request open.
- **Batch:** Enqueue the failed chunk durably and resume after the retry window. Don't hold threads.
- **Background:** Accept a longer delay. Use exponential backoff with jitter bounded within the retry window. Annotate retries with `retry-attempt` on each attempt.

Apply the circuit breaker only to the deployment that is throttling. If you have multiple Azure OpenAI deployments (for example, PTU for interactive and PAYG for batch), circuit-breaking the PTU deployment should not prevent background work from reaching the PAYG deployment.

Not all Azure OpenAI error responses are retryable, and your application logic must explicitly distinguish between them:

| Response | Retryable | Action |
|---|---|---|
| **429** with `retry-after-ms` | Yes | Back off for the indicated duration; always honor the `retry-after-ms` header rather than using fixed delays |
| **408** (PTU queue timeout) | Yes | The request timed out while waiting in the deployment queue — a capacity/backpressure signal. Retry with backoff or route to a spillover deployment |
| **400** with `code: content_filter` | No | The prompt or completion violated a content policy. Retrying the same content will deterministically fail. Log, surface to the caller, and stop retrying |
| **5xx** (service errors) | Yes | Transient service-side failure. Retry with exponential backoff and bounded attempts |

Treating a `content_filter` 400 as a transient failure is a common and costly anti-pattern. Blind retries on rejected content:

- Consume token and throughput capacity
- Increase queue depth on PTU deployments
- Amplify latency for unrelated requests
- Can indirectly contribute to 408 timeouts under load

The `innerError.code` value `ResponsibleAIPolicyViolation` is the authoritative programmatic signal to identify these cases and prevent retries.

In high-throughput systems, correct classification of retryable vs. non-retryable errors is not just an optimization — it's required to maintain system stability under load.

---

## [TP-6] Implement further internal controls: Guard internal components (#3 in Figure 2 above)

**Rationale:** While throttling at the entry-points serves as admission control, throttling the traffic between sub-components such as databases, caches, queues, etc. is also very important.

Throttling in this layer allows each sub-component to protect itself. The mindset is to assume other sub-components are hostile and make no assumptions about their behavior under load. This creates logical “cells” inside the system that are (relatively) isolated from one another and protected against bugs in other components. In particular, focus on components that are **shared** between different types of work your service does. Protecting these "cells" will significantly improve your recovery time.

## [TP-7] Implement egress bulkhead when calling dependencies: Don't overwhelm services you depend on, especially at fan-out points. (#4 in Figure 2 above)

**Rationale:** Egress bulkheads are necessary to limit maximum concurrent in-flight calls. While each dependency should protect itself, such protections may not always be in place.

Traffic to dependencies traverses Layer 3 & 4 systems (often shared, like load balancers and switches) before reaching Layer 7 where throttling may occur—so it can create congestion before application-level throttling kicks in.

Also, at small timescales, requests don’t arrive uniformly within the throttling aggregation window. A short surge might be ok for your system but could overwhelm downstream infrastructure (queues fill, sockets exhaust, downstream destabilizes).

Egress bulkheads help by flattening and reducing variance of traffic between you and you dependencies, increasing overall stability. Ensure the client/SDK supports egress bulkheads.

Watch out for fan-out points: places where one incoming unit-of-work turns into multiple requests (batch calls, queue processors, workflows, bulk DB updates). Bulkheads here require attention to call ordering, expiration, and recovery.

# [General](#tab/tp7-general)

Egress bulkheads limit concurrent in-flight calls to dependencies. Apply them at fan-out points where one unit of work becomes many downstream requests.

# [Azure OpenAI](#tab/tp7-aoai)

Document processing is the most common fan-out source in AI workloads: a single document or conversation thread is split into chunks and each chunk becomes a separate completion or embedding call. Without a bulkhead, one large document submission can exhaust the TPM budget for an entire deployment in seconds.

Apply the bulkhead at the chunking layer, not just at the API gateway:

- Cap maximum concurrent in-flight completion calls per document or job, not just per caller
- Limit the token rate of the chunking pipeline to a fraction of the total TPM budget, reserving headroom for interactive requests
- For embedding fan-out, use batched embedding calls where possible rather than one call per chunk — this reduces RPM consumption significantly at similar TPM cost

Be aware that streaming completions hold a socket open for the duration of token generation. High concurrency of streaming requests can exhaust connection pools before TPM limits are reached.

---

## [TP-8] A cache does not replace a throttle: Avoid (accidentally even) relying on caching as replacement for throttling.

**Rationale:** Some services implement a cache to improve resilience and/or latency, which can reduce steady-state load. This can appear to work but may fail when caller traffic creates cache misses, leading to unplanned traffic from a capacity standpoint.

Cache if you need to, but have throttling and bulkheads in place for the worst case. This also ensures cache-busting DoS attacks will not be effective. Also note cold-start cache hydration: restarting a partition can create temporary load on working partitions. Caches bring benefits but also architectural complexity.

<!--
**Assessment Questions:**
1. Identify caches in your system and find cache hit rate
2. What happens when cache hit rate drops? What is the multiplier for traffic growth?
3. How is the cache hydrated before being put into service? How many caches can be hydrated simultaneously?

**Questionnaire:**
- **Does the service rely on caching to reduce load without assuming it provides protection?**
- **Are throttling limits safe even at worst-case (near-zero) cache hit rates?**
- **Is cold-start cache hydration behavior accounted for in throttling limits?**
- Have we tested cache-busting scenarios?
-->

# [General](#tab/tp8-general)

Cache to improve latency and steady-state load, but set throttling limits that hold at worst-case cache miss rates, not average-case.

# [Azure OpenAI](#tab/tp8-aoai)

Semantic caching — returning a cached response when an incoming prompt is sufficiently similar to a previously answered one — can significantly reduce TPM consumption in steady state. However:

- Cache hit rates collapse when query diversity is high (long-tail questions, personalized prompts, or low similarity thresholds)
- A newly deployed model invalidates the entire cache, creating a cold-start period where every request misses
- Cache-busting is trivially easy: rephrasing a question slightly defeats similarity matching

Set your TPM limits to handle the full uncached request volume. Treat the cache as a cost optimization, not a capacity guarantee. This also prevents a cache miss spike from becoming an outage.

---

## [TP-9] Keep Throttling accounting out of critical path

**Rationale:** Reporting usage and retrieving decisions **asynchronously** and checking these decisions **synchronously** is more resilient than alternative implementations. If not using ATS, consider dedicating a thread in your application that does throttling accounting. The application’s critical paths submit usage to this thread and check the decisions retrieved by this thread.

<!--
**Assessment Questions:**
1. If your throttling implementation is down or slow, what is the impact on your system availability?
2. **How soon can you recover from an issue with your throttling mechanism and what is your recovery process?**

**Questionnaire:**
- Is throttling accounting performed asynchronously from request execution?
- **Does throttling failure degrade gracefully (not block all traffic)?**
- Is there monitoring for throttling decision latency or failure?

**Paved Path Solution:** This is built into ATS Client, but additional investments are needed.
-->

# [General](#tab/tp9-general)

Perform throttling accounting asynchronously so that a slow or unavailable throttle store doesn't block the request critical path.

# [Azure OpenAI](#tab/tp9-aoai)

Token accounting in Azure OpenAI workloads has a timing asymmetry: you can estimate prompt tokens at admission time, but completion token count is only known after the response completes (or after the stream closes for streaming responses). This means exact TPM accounting is always retrospective.

Design your accounting layer accordingly:

- At request admission, reserve an estimated token budget based on prompt length and a configured max-output cap
- After response completion, reconcile actual token usage from the `usage.total_tokens` field in the response and adjust the running counter. For streaming requests, enable `include_usage` where available so the final streamed chunk carries token statistics for the full request. If the stream is interrupted, that final usage chunk may not arrive — your accounting path should tolerate missing final usage and fall back to estimates or platform metrics.
- Keep this accounting in a sidecar or background thread — don't block the response path waiting for the reconciliation to commit
- If the accounting service is unavailable, fail open with a conservative estimate rather than blocking all traffic; alert and restore normal accounting as quickly as possible

---
## [TP-10] Pay particular attention to "horizontal/cross-cutting infrastructure"

**Rationale:** Cross-cutting dependencies like DNS zone updates, upstream ACRs, Yum repos etc., often group traffic differently for throttling purposes than your service. Very often, such dependencies do not have any business logic, and are thought be infinitely scalable and forgotten. However, a single misbehaving consumer can push the cross-cutting infrastructure into the red-zone and cause throttling for many unrelated users. As a result, be careful that:

* Your service does not overwhelm such a service; apply TP-7 to avoid broader-than-necessary impact.
* Have a plan for when such a cross-cutting dependency is not available — caching of "last known good" responses and pre-decided brownouts can be effective techniques to handle throttling

Be suspicious of dependencies that don’t throttle at all—they may silently reach limit and return 500s to all users without warning.

## [TP-11] Operational Excellence (part 2): Reassess, tune and validate periodically

**Rationale:** Periodically revisit configuration to ensure it reflects current usage patterns at all points in your system. Ideally done as part of weekly live site review using a dedicated dashboard showing current limits and real-time + historic utilization (high/low watermarks). Automation is encouraged. Ensure incident review includes evaluating current usage patterns.

**Validation Techniques:** Periodic testing can be conducted by setting low limits in throttling policies and/or using Chaos Studio to ensure 429 handling behavior and DRI triage capability has not regressed. Other testing:
1. Recreate last N outages (N decided by service owner)
2. Load test least-used APIs
3. Load test most expensive APIs

At a minimum, coupling at least 2 of these with zonal outage drills is strongly recommended.

Creating replica environments with mocked dependencies and AI-driven traffic generators can validate non-functional goals (performance, resiliency), and is recommended long-term.

# [General](#tab/tp11-general)

Periodically review limits against current usage patterns. Static limits decay as traffic evolves. Validate through controlled low-limit testing.

# [Azure OpenAI](#tab/tp11-aoai)

AI workloads have an additional decay trigger that classic REST services don't: **model upgrades**. Moving from one model version to another (for example, GPT-4o to GPT-4.1) can significantly change throughput behavior, cost per token, and output length distribution in ways that make previously calibrated limits incorrect. Because Azure OpenAI metrics are tracked by model and model version, include post-upgrade validation in your runbooks and compare throughput, token usage, and latency before and after each model version change.

Build model upgrades into your operational review process:

- Re-run low-limit chaos tests after each model deployment, not just after infrastructure changes
- Update token cost estimates in your accounting layer to reflect the new model's output length distribution
- Track prompt and completion token counts per model version in your observability tooling so you can compare before and after

Also plan for quota changes: Azure OpenAI quota allocations per region and per model change as Microsoft adds capacity. Validate after quota changes in both directions to ensure limits are still calibrated correctly.

---

## [TP-12] Implement state-aware throttling: Implement throttling that reacts to system state

**Rationale:** Throttling that reacts to system state reallocates scarce system capacity in alignment with business goals. Techniques include:

- **Dynamic Throttling:** Modulate nominal cost based on operation type (read vs write) and/or current state (queue length, memory).
- **Bankruptcy:** For asynchronous systems, re-evaluate queued work because cost/state may have changed; be able to drop enqueued work (declare bankruptcy). Deadlines and LIFO prioritization can help.
- **Brownouts:** Pre-decided degraded modes when health metrics exceed thresholds (disable low-priority operations, return shorter results, etc.).
- **Bursting and Borrowing:** Allow short-term consumption above baseline; borrowing uses spare capacity from others; both require careful fairness controls.

<!--
**Questionnaire:**
- **Does the service differentiate throttling cost by operation type (e.g., Read vs Write vs Update)?**
- **Does the service dynamically adjust throttling based on real-time health (CPU, memory, queue depth, latency)?**
- **When severely overloaded, does it intentionally shed load (declare bankruptcy) to protect system health?**
- **Can it automatically enter brownout mode under severe stress?**
- **Does it support controlled bursting and borrowing beyond baseline limits?**
-->

# [General](#tab/tp12-general)

Throttling that reacts to real-time system state — rather than fixed limits — allocates scarce capacity in alignment with business priorities.

# [Azure OpenAI](#tab/tp12-aoai)

Azure OpenAI workloads have strong, natural cost differentiation that makes state-aware throttling especially valuable:

- **Dynamic cost by operation:** A chat completion with a 4,000-token context costs roughly 40× more TPM than a short classification prompt. Assign different token-cost weights to operation types and throttle against the weighted budget, not a flat request count.
- **Brownout mode:** Under sustained TPM pressure, reduce the `max_tokens` parameter on interactive requests (for example, cap at 512 instead of 4,096). Users receive shorter but timely responses rather than a hard rejection. Apply brownouts to the interactive tier first; shed batch and background work entirely before degrading interactive quality further.
- **Bankruptcy for batch queues:** An overnight summarization job assembles a queue of documents before processing starts. If TPM pressure is high when the job runs, re-evaluate the queue: drop stale items (documents already superseded by newer versions), deprioritize low-value summaries, and process only what's needed before the next business day starts. Holding the full queue and retrying everything amplifies load; declaring partial bankruptcy reduces it.
- **Bursting with PAYG overflow:** Where applicable, design your workload to redirect overflow traffic to a PAYG deployment when your primary PTU deployment approaches capacity. PTU gives predictable throughput at fixed cost; PAYG can absorb short bursts at higher per-token cost. Set a spend ceiling and time limit on PAYG overflow to prevent sustained cost escalation during prolonged burst events.

---

## [TP-13] Honor Transactional boundaries

**Rationale:** For service APIs with transactional semantics, ensure throttling can prioritize in-flight transactions over starting new ones. Throttling sub-operations of a transactional API may cause transaction failure/rollback, wasting resources and degrading customer experience. This could be implemented via special transaction identifiers sent along with the API call but there may be more natural identifiers available in your use case. This requires careful design and application modification.

# [General](#tab/tp13-general)

Prioritize completing in-flight transactional operations over admitting new ones to avoid wasting partially consumed resources.

# [Azure OpenAI](#tab/tp13-aoai)

A multi-turn conversation is a logical transaction in AI workloads. By the time the user's message arrives, the system has already retrieved context, assembled a prompt, and consumed tokens for retrieval. Throttling the completion call at this point wastes all of that upstream work and delivers a worse experience than either completing the turn or gracefully brownout-capping the response length.

Track conversation identifiers through your throttling layer and apply these rules:
- Prioritize completion calls that are part of an in-progress conversation over new conversation starts when TPM is constrained
- If you must throttle mid-conversation, prefer reducing `max_tokens` (brownout, see TP-12) over returning a 429 that forces the client to replay the full request
- For agent tasks that span multiple model calls (plan-then-act patterns), treat the full task as a transaction: don't admit a new task if you can't guarantee enough TPM headroom to complete it

---

## [TP-14] Implement Random Early Drop

**Rationale:** As aggregate usage approaches limits (yellow zone in Figure 1), start rejecting a carefully chosen fraction of traffic. A positive `x-ms-ratelimit-remaining` header signals to callers their traffic was in this category and the system is nearing limits. Benefits: (1) prevent system reaching red zone; (2) validate upstream partners remain ready to respect constraints. 

# [General](#tab/tp14-general)

Start shedding a fraction of traffic early as aggregate usage approaches limits, before hitting the hard ceiling.

# [Azure OpenAI](#tab/tp14-aoai)

The key difference from the general case is that the early drop signal should be token-based, not request-based. An RPM-only early drop is ineffective — ten short prompts may consume less TPM than one long one.

Use your own token accounting layer (see TP-9) as the primary early drop signal. As your tracked TPM consumption approaches the deployment quota:

- Begin probabilistically rejecting new **batch and background** requests with a 429 and a short `Retry-After`
- Continue serving interactive requests at full rate until a lower threshold is reached
- This preserves the best possible experience for interactive users during load surges, without waiting for Azure OpenAI to return a hard 429

Calibrate your high-watermark thresholds using Azure Monitor's token usage metrics — Processed Prompt Tokens and Processed Inference Tokens — rather than derived request counts.

---

## Summary

The principles and practices in this guide form a control loop: work is admitted in accordance with capacity, overload signals flow back to callers, and limits are continuously calibrated against real patterns. In AI-native systems, throttling is no longer just about protecting infrastructure — it's the primary mechanism for allocating intelligence under constraint.

