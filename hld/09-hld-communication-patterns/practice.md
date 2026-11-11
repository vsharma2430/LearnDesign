# Communication Patterns: Practice Questions and Answers

Use these questions to practice selecting a communication contract from requirements. For each design, state what success means, what can fail, and how the system recovers.

## 1) When is a synchronous request a better choice than a queue?

**Answer:**

Use a synchronous request when the caller must make an immediate decision based on the result, such as checking authorization or calculating a quote. Define a deadline, propagate its remaining budget to dependencies, and bound concurrency. Use asynchronous work when completion can be deferred and the system needs to absorb bursts or tolerate temporary consumer outages.

## 2) If an API returns `202 Accepted`, what should the client infer?

**Answer:**

Only that the server accepted the request for later processing, according to the API's documented durability contract. It does not mean the requested operation completed successfully. Provide a job or operation ID, a status mechanism, expiration behavior, and a way to report terminal failure. A `202` should not be returned before the system has reached its defined durable acceptance point.

## 3) Why can a chain of synchronous microservice calls reduce availability?

**Answer:**

Each mandatory dependency adds another opportunity for a timeout or failure to make the whole request fail. Latencies also add, and retries can multiply load during an outage. Reduce call depth, set end-to-end deadlines, use fallbacks only when their semantics are safe, and move deferrable work to an asynchronous boundary.

## 4) A system needs a dashboard that updates every few seconds, but users never send data over the live channel. What would you choose?

**Answer:**

SSE is a natural fit because updates flow only from server to client and the client can use HTTP for commands. Long polling may be simpler for infrequent updates or compatibility constraints. WebSockets are justified if bidirectional messaging is needed or the product already has a managed WebSocket gateway. Verify proxy buffering, idle timeouts, browser connection limits, authorization, and replay needs.

## 5) When would WebSockets be preferable to SSE?

**Answer:**

Use WebSockets when both client and server need to send frequent messages over the same persistent channel, as in chat or collaborative editing. The application must still implement message identity, durable history, acknowledgments where required, reconnect/resume, and bounded buffers. WebSockets alone do not guarantee that a disconnected client receives missed updates.

## 6) What state must an application add on top of long polling?

**Answer:**

It needs a timeout and reconnect strategy, jitter to avoid synchronized client retries, and a cursor or sequence number to recover changes between requests. It must also bound outstanding held requests and account for load-balancer or proxy timeouts. Long polling transports events; it does not itself preserve event history.

## 7) How does a webhook sender avoid losing notifications when a receiver is offline?

**Answer:**

Persist pending deliveries, retry transient failures with bounded exponential backoff and jitter, and expose attempt status and controlled replay. Sign requests, include a stable event ID and timestamp, and document receiver timeout and response semantics. The receiver should verify the signature, deduplicate, durably enqueue if processing takes time, and acknowledge promptly.

## 8) How do STUN and TURN differ in a WebRTC design?

**Answer:**

STUN helps a peer discover its public-facing network mapping so it can attempt a direct path. TURN relays traffic when direct connectivity fails because of NAT or firewall restrictions. TURN improves connectivity but introduces bandwidth cost and additional latency. Signaling exchanges session setup information; it is a separate service from STUN/TURN media traversal.

## 9) What does a queue provide, and what does it not provide?

**Answer:**

A durable queue can buffer work and isolate producer and consumer availability, subject to its retention and durability configuration. It does not automatically make business effects exactly once, preserve global order, or ensure a consumer eventually succeeds. Those properties require explicit acknowledgment, idempotency, ordering scope, retry limits, monitoring, and recovery procedures.

## 10) How do a queue and pub-sub differ?

**Answer:**

A competing-consumer queue typically distributes each work item to one worker. Pub-sub delivers each event to multiple independent subscriptions, with consumers often load-balanced within each subscription. Real brokers may support both models, but persistence, replay, and acknowledgment differ by product and configuration. Pick based on whether one worker pool should process a task or multiple systems should react to a fact.

## 11) What should happen when a consumer repeatedly fails on one message?

**Answer:**

Use a bounded retry policy with backoff, then route the message to a DLQ or equivalent quarantine. Track the message ID, attempt count, error category, and age; alert on arrivals and backlog. Diagnose and fix the root cause before replaying, and rate-limit redrive so a corrected message does not overload a recovering dependency.

## 12) Explain at-most-once and at-least-once delivery.

**Answer:**

At-most-once avoids retry-induced duplicates but can lose a message when delivery or processing fails. At-least-once retries until acknowledged, subject to broker retention and configuration, and can deliver duplicates. Durable business processing commonly uses at-least-once plus idempotent consumers. Exactly-once claims must specify a narrow transactional boundary and should not be assumed to cover external side effects.

## 13) How can CDC help a search service stay synchronized with a relational database?

**Answer:**

A CDC connector reads committed changes from the database log and publishes them to a stream; a search indexer applies those changes asynchronously. Define snapshot and resume behavior, deletion representation, per-key ordering, schema evolution, and lag monitoring. CDC captures database mutations, not necessarily domain intent, so publish domain events instead when consumers need stable business-level facts.

## 14) How do you handle a producer that is faster than its consumers?

**Answer:**

Measure queue age, depth, consumer lag, and in-flight work, then apply bounded admission, producer throttling, or load shedding. Scale consumers only when downstream systems have spare capacity. Coalesce or drop only replaceable data such as stale cursor positions; durable commands must be retained or explicitly rejected. Every queue and connection buffer needs a bound and an overload policy.

## 15) Describe the transactional outbox pattern.

**Answer:**

The business transaction writes both its state change and an event row to an outbox table in the same database transaction. A relay publishes committed rows and marks them sent. A crash between publishing and marking can cause duplicate publication, so consumers deduplicate or make effects idempotent. The pattern prevents the database/broker dual-write gap but does not guarantee exactly-once delivery end to end.

## 16) Why do transactional email and push notifications usually run asynchronously?

**Answer:**

Provider latency and outages should not normally block the user's business operation. Commit the business state and notification intent together, then deliver from a worker with retry and status tracking. Model transient and permanent provider errors separately; preserve consent and preferences; and do not claim delivery merely because a provider accepted the request.

## 17) Compare live transport choices.

**Answer:**

| Choice | Direction | Useful when | Recovery responsibility |
|---|---|---|---|
| Long polling | Server response to repeated client requests | Updates are infrequent or compatibility is important | Client reconnect and server-side cursor/history |
| SSE | Server to client | Browser needs a simple one-way event stream | Event IDs and retained replay window if gaps matter |
| WebSocket | Bidirectional | Both sides send frequent live messages | Application-level IDs, replay, acknowledgments, and resume |
| WebRTC | Peer media/data, with relay fallback | Interactive audio/video or peer data | Signaling, ICE reconnection, and application session state |

These are not interchangeable guarantees: none makes business data durable by itself.

## 18) A slow WebSocket client is accumulating outbound messages. What should the server do?

**Answer:**

Bound the per-connection buffer and define a policy based on message meaning. Coalesce superseded state updates, drop explicitly disposable updates, or disconnect the client and let it resume from a durable cursor. Do not allow an unbounded buffer to turn one slow connection into memory pressure for the whole gateway.

## 19) What ordering guarantee should a messaging design promise?

**Answer:**

Promise the narrowest scope that satisfies the use case, such as order per account, entity, or partition. Global ordering limits parallelism and complicates availability. Partition related messages by a stable key, preserve sequence/version information, and define how consumers detect gaps or stale events. A broker's ordering guarantee is only one part of end-to-end ordering.

## 20) What does “real-time” mean in a system design?

**Answer:**

It means the system meets a stated latency objective for a workload, not necessarily a hard real-time deadline. Specify the target (for example, p95 update latency), measurement point, load, and failure assumptions. Include connection establishment, event fan-out, client processing, and reconnect behavior in the user-visible definition.

## Quick revision checklist

- Define what an acknowledgment or successful response actually promises.
- Separate transport from durability, replay, ordering, and business completion.
- State timeout, retry, deduplication, and idempotency behavior.
- Bound queues, in-flight work, and per-client buffers.
- Make overload and permanent-failure handling observable.
- Choose the smallest ordering and latency guarantees that meet the product need.
