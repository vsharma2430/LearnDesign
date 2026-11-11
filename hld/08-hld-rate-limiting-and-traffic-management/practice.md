# Practice Questions and Answers

Use these questions to check whether you can select and explain traffic controls from requirements, rather than choosing an algorithm by name alone.

## 1) How is a quota different from a rate limit?

**Answer:**

A quota bounds cumulative usage over a longer policy period, such as a monthly allowance. A rate limit bounds how quickly work arrives over a shorter interval. A tenant can remain below 20 requests per second and still exhaust a monthly quota, or be below its monthly quota while exceeding a per-second burst limit. A complete API policy may enforce both.

## 2) How would you define a usage-based API quota?

**Answer:**

Specify the principal (for example, account rather than API key), metered unit, period, reset semantics, endpoint scope, overage behavior, and required enforcement accuracy. If operations have different costs, use documented weights rather than counting every request equally. Keep a low-latency admission counter separate from a durable usage ledger used for billing and reconciliation, and make reservations and retries idempotent.

## 3) What does a token bucket allow that a fixed-window limiter does not control well?

**Answer:**

A token bucket allows a bounded burst up to its capacity while constraining the sustained rate to its refill rate. A fixed-window limiter can allow almost twice its configured limit around a window boundary—for example, a full minute's allowance just before the reset and another immediately after it. Token bucket parameters should be selected from the intended burst size and sustained capacity.

## 4) A service receives 150 requests per second and requests take 400 ms on average. Approximately how many are in flight?

**Answer:**

By Little's Law, average in-flight work is arrival rate multiplied by average time in system: `150 × 0.4 = 60` requests. This is only an average estimate. Capacity should include headroom for bursts and tail latency, and should account for how much CPU, memory, or downstream capacity each request consumes.

## 5) When should a service use a concurrency limit instead of a request-rate limit?

**Answer:**

Use a concurrency limit when active requests hold scarce resources or have highly variable durations, such as database connections, report workers, or external provider calls. A rate limit constrains arrivals but may still allow slow work to accumulate. Most systems use both: a rate budget for fairness or abuse protection and a concurrency cap for resource safety.

## 6) How do fair queuing and priority scheduling differ?

**Answer:**

Fair queuing allocates service among tenants or classes to prevent one source from monopolizing capacity. Priority scheduling serves urgent work ahead of less urgent work. Priority can starve lower classes, so use reserved capacity or aging; fairness can be weighted by tenant tier or estimated work cost. Priority labels must be assigned by trusted policy, not accepted directly from clients.

## 7) When is it safe to queue excess work?

**Answer:**

Queue when the work is deferrable, short bursts are expected, and the request can still be useful when processed. Bound the queue by count and age, consider the caller's deadline, and define durable acceptance, retries, deduplication, and cancellation. Reject rather than queue work that will become stale or cannot finish within its useful lifetime.

## 8) How does admission control differ from rate limiting?

**Answer:**

Rate limiting enforces a policy budget for a principal over time. Admission control decides whether the system has enough safe capacity to accept work now, using signals such as available worker slots, queue age, dependency latency, or saturation. A customer may have remaining quota while the service temporarily rejects work because a dependency is overloaded.

## 9) Why can a local per-instance counter violate a global quota?

**Answer:**

Each instance only sees the requests routed to it. If eight instances each allow 100 calls per minute for the same tenant, the tenant may receive close to 800 calls per minute. Shared atomic counters improve global accuracy but add latency and a dependency. Leased or hierarchical budgets reduce per-request coordination while bounding aggregate overshoot.

## 10) Should a rate limiter fail open or fail closed when its counter store is unavailable?

**Answer:**

It depends on the consequence of overshoot. A best-effort abuse limit may use a bounded local fallback or temporary fail-open mode to preserve availability. A hard paid quota or expensive scarce operation may require fail-closed behavior or preallocated local budget leases. The policy should be chosen and observed explicitly; it must not emerge accidentally from a timeout default.

## 11) What should an API return when the client exceeds its quota?

**Answer:**

Usually `429 Too Many Requests`, with a documented reset or `Retry-After` hint when useful. Clients should use bounded exponential backoff with jitter and avoid infinite retries. If the rejection is caused by system-wide saturation rather than the client's policy, return an overload response such as `503` so the client can distinguish the causes.

## 12) How do you know if a traffic policy is working?

**Answer:**

Monitor allowed and rejected work, queue depth and age, in-flight requests, per-class latency, downstream saturation, retry rates, and the latency/error rate of any shared policy store. Compare those signals by bounded dimensions such as route template and customer tier. Alert on unexpected rejection spikes and avoid high-cardinality labels containing raw user IDs or API keys.

## Quick revision checklist

- Separate long-term quotas from short-term rate limits.
- Match the limiter to the scarce resource: rate, concurrency, queue, or downstream pace.
- Bound both work volume and waiting time.
- Define fairness and priority behavior under contention.
- Make distributed counter operations atomic enough for the required guarantee.
- Specify failure behavior and client retry semantics.
