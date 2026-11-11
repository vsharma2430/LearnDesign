# Messaging Systems: Real-World Interview Q&A

These scenarios test whether you can turn product requirements into precise messaging guarantees. In an interview, clarify throughput, latency, retention, ordering, and failure expectations before choosing a broker.

## 1) Design order processing for an e-commerce platform

**Interviewer:** An order API must remain responsive during traffic spikes. Inventory, payment, fulfillment, and email services can be temporarily unavailable. How would you design the messaging flow?

**Candidate:**

I would first distinguish the customer-facing acceptance response from final order completion. The API validates the request, assigns an idempotency key and order ID, and commits the order plus an `OrderSubmitted` outbox row in one database transaction. It returns an operation/order status only after that durable commit.

A relay publishes the outbox event to a durable broker. I would model the process as explicit order states, such as submitted, inventory-reserved, payment-pending, paid, and fulfillment-started. Inventory and payment commands have a clear owning service; completed outcomes are published as events. Each consumer uses a stable message ID and idempotent state transitions. The order service updates status from outcomes rather than assuming that a broker acknowledgment means the work completed.

I would avoid a single global ordering requirement. If ordering matters for an order, key by order ID and validate a version/transition sequence. Retries are bounded with backoff; permanently invalid messages go to a DLQ with alerts and a controlled redrive process. The API exposes status, and operations staff can inspect and reconcile ambiguous payment outcomes. I would not claim end-to-end exactly-once: the design uses durable handoff, idempotency, and reconciliation to achieve one intended business outcome.

**Interviewer follow-up:** What if the payment provider times out after charging the card?

**Candidate:**

A timeout is an unknown result, not a confirmed failure. I would reuse the same provider idempotency key for retries, query/reconcile the provider outcome, and avoid issuing a second logical charge until the first attempt is resolved. The payment state should represent pending/unknown rather than prematurely marking failure.

## 2) Compare RabbitMQ, SQS, SNS, and Kafka for a notification platform

**Interviewer:** A product event should trigger email, push, analytics, and an in-app notification. How would you select the messaging backbone?

**Candidate:**

I would clarify whether the main requirement is work dispatch, independent fan-out, or retained replay. For simple managed work queues, SQS is a reasonable starting point, with separate queues per channel or worker class. SNS can fan an event out to durable SQS subscriptions so one channel's outage does not block the others. RabbitMQ is attractive when flexible routing and queue-level behavior are central and the team is prepared to operate the broker. Kafka is a strong fit if the platform needs a high-volume retained event log, multiple replaying consumers, or a wider stream-processing ecosystem. Pulsar may fit a large multi-tenant messaging platform where its subscription and storage model justify the added operational complexity.

I would not treat these as identical products or choose by a generic throughput claim. I would compare durability, ordering scope, replay/retention, retry and DLQ support, payload limits, regional deployment, cost, quotas, and operator skills. The business transaction writes a notification intent through an outbox; each channel has independent rate limits and delivery status. A provider accepting an email request is not the same as the recipient receiving it.

## 3) Design an audit/event stream that feeds search and analytics

**Interviewer:** A relational service is the source of truth. Search needs updates in seconds, and analytics can lag. How would you keep both consumers current?

**Candidate:**

I would avoid synchronous dual writes from the request path because one downstream outage would create partial success or increase user latency. If downstream consumers need domain meaning, I would publish versioned domain events through an outbox. If they need broad row-level change capture, I would consider log-based CDC and document that database schema becomes part of the integration contract.

A retained partitioned log such as Kafka or Pulsar can serve independent search and analytics consumer groups. I would partition by entity ID where per-entity ordering matters, and include stable IDs and versions so consumers can deduplicate and reject stale updates. Search can have a tighter lag objective; analytics can batch for throughput.

For recovery, define a consistent initial snapshot and a precise stream position from which changes continue. Handle deletes, schema changes, tombstones, consumer rebuilds, and retention expiry. Monitor connector lag, consumer lag, oldest event age, and remaining log retention. If a consumer falls beyond the replay window, rebuild from a snapshot rather than silently skipping history.

## 4) A consumer is charging customers twice after retries

**Interviewer:** The queue is at-least-once. A worker calls a payment service, times out, and then receives the message again. How do you prevent a duplicate charge?

**Candidate:**

I would give the logical payment attempt a stable operation ID and pass it as the provider idempotency key. A transport retry must reuse that ID; creating a new ID would make it a new charge. The consumer records the message and local state transition atomically using an inbox where possible. If the external provider does not offer reliable idempotency, I would reconcile by a provider reference before retrying an ambiguous attempt and provide an operator workflow.

I would acknowledge only after a durable state transition or safe handoff. The inbox cannot make the external network call atomic with the database, so the design still needs provider-side idempotency or reconciliation. I would add metrics and alerts for repeated attempts, unknown payment states, and reconciliation age.

## 5) Design delayed reminders that users can cancel

**Interviewer:** Users schedule reminders for any time over the next six months and may edit or cancel them. How would you implement this?

**Candidate:**

I would treat this as durable scheduled state, not as workers sleeping for six months or as an unbounded retry delay. A schedule table or scheduler service stores reminder ID, user, due time, version, status, and cancellation state. A due-time index or time-bucketed scheduler claims eligible items in bounded batches and publishes an event containing the reminder ID and version.

Before delivery, the worker verifies the current schedule version and cancellation state so an old queued message cannot send a canceled or rescheduled reminder. The send operation uses an idempotency key. If the scheduler crashes after publishing but before marking the record dispatched, the stable ID prevents duplicate sends. I would define time-zone and daylight-saving semantics, clock-skew tolerance, retry/backoff, retention, and provider delivery status.

I would evaluate native delayed-message support only if its duration, cancellation model, throughput, and operational behavior match the six-month requirement. Queue visibility timeout is not a long-term scheduler.

## 6) A real-time service must preserve per-account updates

**Interviewer:** Account balance events arrive concurrently from several producers. Consumers must never apply an older balance after a newer one. What is your design?

**Candidate:**

First I would identify the authoritative sequence source. If the account service assigns a monotonically increasing version while committing a balance change, the event should carry account ID and version. I would partition or group by account ID where the broker supports keyed ordering, but I would still validate versions at the consumer because multiple producers, retries, and replays can create stale delivery.

The consumer applies an update only if its version is newer than the stored version, and detects gaps if every transition must be observed. If the message represents a complete current snapshot, it may safely skip older versions; if it represents a delta such as “subtract 10,” skipping is unsafe and gaps must be replayed or reconciled. I would avoid global ordering, which would unnecessarily serialize unrelated accounts.

I would define poison-message handling carefully: pausing one account lane preserves order but creates head-of-line blocking for that account; quarantining and continuing requires a reconciliation policy. Metrics include per-partition lag, version gaps, stale-event count, and oldest account update.

## 7) A distributed search endpoint aggregates five services

**Interviewer:** The endpoint returns profile, pricing, availability, recommendations, and reviews. Some dependencies are often slow. How do you design fan-out and aggregation?

**Candidate:**

I would set an end-to-end deadline from the caller and allocate a smaller budget to each dependency. Calls fan out concurrently with bounded concurrency, request IDs, and cancellation when the overall deadline expires. The aggregator waits for required responses, or for a documented quorum/threshold, and returns partial results with explicit availability indicators for optional sections.

Retries are limited to safe, idempotent reads and must fit within the remaining deadline. I would not wait indefinitely for the slowest optional dependency. If the work may exceed a normal request budget, I would persist a long-running operation and return an operation ID rather than hold open a request. Correlation IDs let us trace each response and distinguish timeout, empty result, and service failure.

I would monitor p95/p99 end-to-end latency and per-dependency latency/error rates, partial-response rate, timeout rate, and concurrency saturation. Caching or precomputation may help for expensive stable data, but would need freshness semantics.

## 8) A team wants to put 20 MB documents directly on a broker

**Interviewer:** The broker has a message-size limit and several consumers need the document. What pattern would you propose?

**Candidate:**

I would use a claim check. The producer uploads the document to durable object storage, preferably under an immutable versioned key, verifies it, and then publishes a compact event with the reference, checksum, content type, size, and object version. Consumers fetch the object with narrowly scoped authorization and verify the checksum before processing.

The object must be uploaded before publishing the reference, and its retention must cover broker retention and replay. If publication fails, the upload can become orphaned, so I would use lifecycle cleanup or an upload state record. I would not put a public object URL or sensitive access token in the message. Access checks, encryption, tenant isolation, payload scanning where required, and auditability are part of the design.

## 9) A workflow has variable processing steps across services

**Interviewer:** A shipment can require customs validation, address correction, payment review, and carrier booking in a route that varies by destination. Would you use a routing slip?

**Candidate:**

A routing slip can work if the route is a bounded sequence of known, allowlisted steps and each step is independently retry-safe. The message carries workflow ID, route version, current step, and correlation metadata. Each service persists its completion or uses idempotency before advancing the slip, and failures have explicit retry, compensation, and dead-letter behavior. I would not let a message specify arbitrary URLs or service names.

If routes have complex branching, long waits, manual review, timers, or compensating transactions, I would prefer a workflow orchestrator with durable state and visibility into each step. The key requirement is recoverable progress, not the routing-slip pattern itself.

## 10) How would you evolve a message contract without a synchronized deployment?

**Interviewer:** The producer and several consumers deploy independently. The new event requires an additional field and changes how a price is represented.

**Candidate:**

I would separate the additive field from the semantic change. An optional field with a documented default may be backward-compatible, depending on the format and registry policy. Changing a price's unit or meaning in place is not safe even if the serialized type stays the same; I would add a new field or event version with explicit currency and units, dual-write/read during migration, and deprecate the old representation only after consumers have moved.

I would register schemas and enforce compatibility in CI, test both old-reader/new-writer and new-reader/old-writer combinations needed for the rollout, and use an expand-migrate-contract sequence. Consumers should tolerate unknown optional fields, but malformed or unsupported contract versions should be surfaced to a DLQ/alert path rather than silently interpreted.

## Interviewer follow-up checklist

- What is the durable acceptance point, and what does each acknowledgment mean?
- Is the message a command, event, or snapshot, and who owns its contract?
- What are the ordering key, retention window, and replay boundary?
- How are duplicate delivery, unknown outcomes, and partial completion handled?
- What are the retry, delayed-work, dead-letter, cancellation, and redrive policies?
- How does the system limit backlog, batch size, fan-out concurrency, and payload size?
- What metrics and operator workflows reveal lag and recover safely?
