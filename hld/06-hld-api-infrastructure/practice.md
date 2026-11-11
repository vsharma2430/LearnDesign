# Practice Questions and Answers

Use these questions to check whether you can explain API infrastructure decisions, not just name components. For each answer, state the assumptions, the failure behavior, and the trade-offs.

## 1) What responsibilities belong in an API gateway?

**Answer:**

A gateway is a shared entry point for routing and cross-cutting API policies. It commonly terminates TLS, selects an upstream, applies request-size and timeout limits, enforces coarse-grained authentication and traffic policies, and emits access telemetry. It can also support protocol translation or controlled rollout.

Business rules and data-level authorization should remain in the owning service. Otherwise the gateway becomes a second business-logic implementation, a deployment bottleneck, and a single place where domain changes must be coordinated.

## 2) How is an API gateway different from a reverse proxy, ingress controller, and service mesh?

**Answer:**

A reverse proxy forwards requests to upstreams and may provide load balancing or TLS. An API gateway adds API-oriented policy and lifecycle capabilities. A Kubernetes ingress controller routes external traffic into a cluster and may be sufficient as the gateway for some systems. A service mesh focuses on service-to-service traffic, identity, and telemetry inside the environment. Products may implement multiple roles, so compare capabilities and operational boundaries rather than relying only on names.

## 3) Which rate-limiting algorithm would you choose for an API that permits short bursts?

**Answer:**

A token bucket is a good starting point: tokens refill at a sustained rate, and the bucket capacity sets the maximum burst. A client can spend accumulated tokens quickly but cannot exceed the long-term refill rate indefinitely. I would compare it with a leaky-bucket queue if the goal is to smooth work sent to a downstream system rather than allow an immediate burst.

The policy must define identity, endpoint cost, bucket size, refill rate, distributed enforcement, and what happens when the counter store is unavailable.

## 4) Why can a fixed-window limiter allow a burst larger than the configured limit?

**Answer:**

Suppose the limit is 100 requests per minute. A client can send 100 requests just before the minute boundary and 100 immediately after it. Each fixed window is within policy, but nearly 200 requests arrive in a very short interval. A sliding-window counter or token bucket reduces this boundary effect. A sliding-window log is more precise but stores more state.

## 5) How would you enforce a per-tenant limit across many gateway replicas?

**Answer:**

First decide how strict the policy must be. A shared atomic counter store provides a consistent view but adds latency and another dependency. Local counters are fast but multiply the effective limit across replicas. Hierarchical budgets can allocate a tenant's global budget to regions or gateway instances, then enforce locally with bounded overshoot.

For each approach, I would capacity-test the counter path, define key expiration and hot-key behavior, and document fail-open or fail-closed behavior. If a counter outage should not take down all reads, a bounded local fallback may be appropriate for a best-effort limit; contractual hard quotas may need stricter enforcement.

## 6) Explain rate limiting versus throttling with an example.

**Answer:**

Rate limiting says how much a principal is allowed to do over a period, such as 500 requests per minute per account. Throttling controls processing pace or admission based on available capacity, such as limiting a report service to 50 concurrent jobs when its database is near saturation.

A rate limit can reject a request even when the service is healthy. A throttle can slow or reject a request even when the tenant has quota remaining. In practice, throttling may queue work, reduce concurrency, or shed lower-priority traffic.

## 7) What should a client do after receiving HTTP 429?

**Answer:**

It should honor `Retry-After` when provided; otherwise it should use bounded exponential backoff with jitter and respect its own request deadline. It should not retry immediately or retry forever. The server should document the limit scope and any useful response headers without revealing other tenants' usage.

## 8) How do you choose between rejecting and queuing excess work?

**Answer:**

Reject when the caller can retry safely, the operation is interactive, or queued work is unlikely to finish before its deadline. Queue when short bursts are normal, the operation is asynchronous, and the queue has explicit size, age, and priority bounds.

An unbounded queue converts overload into rising latency and memory use. A queue should reject stale work, expose queue delay, and have a policy for retries and duplicate submissions.

## 9) When is request hedging useful?

**Answer:**

It is useful for safe, usually read-only requests when latency variation among independent replicas creates a long tail. After a measured delay, the caller sends a second attempt and returns the first acceptable result. It is not a general retry mechanism and does not fix a shared downstream bottleneck.

Hedging adds parallel work, so it needs a concurrency or rate budget, cancellation, a shared deadline, idempotent semantics, and monitoring. It should usually be reduced or disabled during saturation.

## 10) Why can hedging make an outage worse?

**Answer:**

If all replicas are slow because they share an overloaded database, hedging doubles the requests reaching that database without creating a faster replica. The extra load can increase queueing, timeouts, retries, and resource exhaustion. A safe design hedges only when measured conditions indicate the extra work is affordable and monitors both latency and duplicate load.

## 11) Is it safe to hedge a payment request?

**Answer:**

Not by default. A payment is a side effect, and racing two attempts can create duplicate charges. The system needs an idempotency key with durable deduplication at the operation boundary and clear behavior from any external payment provider. Even then, I would first consider whether hedging a mutation is necessary; bounded retries or status reconciliation may be safer.

## 12) What metrics would you use to assess an API gateway and its traffic policies?

**Answer:**

Track request volume, status codes, latency percentiles, upstream connection failures, timeout rates, active connections, route-level saturation, and policy rejections. For rate limiting, track allowed/rejected counts and counter-store latency/errors. For hedging, track hedge rate, extra attempts, cancellation success, and which attempt won.

Use bounded metric labels such as route template and tenant tier, not raw user IDs or API keys. Correlate logs and traces with request IDs while excluding credentials and sensitive payloads.

## 13) A gateway authenticates requests. Why must the service still authorize them?

**Answer:**

Authentication establishes who the caller is; authorization decides whether that identity can perform a specific operation on a specific resource. The gateway may enforce coarse access rules, but the service owns the resource and business context. It should validate trusted identity context and enforce object- and action-level permissions itself.

## 14) Compare common gateway options at a high level.

**Answer:**

| Option | Choose it when | Be careful about |
|---|---|---|
| Managed cloud API gateway | Cloud integration and reduced proxy operations are priorities | Provider limits, cost, portability, and feature differences |
| Envoy-based gateway | Extensibility and a programmable data plane are important | Control-plane and configuration complexity |
| NGINX-based gateway | A mature, efficient reverse-proxy foundation fits the requirements | API management capabilities may require additional components |
| Kong Gateway | Plugin-driven API management and consumer controls are useful | Plugin lifecycle and control-plane operations |
| Kubernetes ingress controller | Cluster-level HTTP routing meets the immediate need | It may not cover external API lifecycle, quota, or product requirements |

Validate the actual deployed version and edition; these categories do not guarantee identical feature sets.

## Quick revision checklist

- Explain gateway responsibilities and boundaries with services, ingress, and a service mesh.
- Select a rate algorithm based on burst, accuracy, cost, and latency needs.
- Describe what happens when the distributed counter store fails.
- Distinguish a customer quota from capacity-driven throttling.
- Explain a client-safe 429 response and retry behavior.
- State the safety conditions and load budget for hedged requests.
