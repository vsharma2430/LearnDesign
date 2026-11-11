# Communication Patterns: Real-World Interview Q&A

These scenarios are designed to practice turning product requirements into communication contracts. A strong answer clarifies guarantees, names failure modes, and explains how the system recovers.

## 1) Design live order tracking for a food-delivery app

**Interviewer:** Customers should see courier location updates, and couriers send a new location every few seconds. How would you communicate these updates?

**Candidate:**

I would separate location ingestion from customer delivery. Courier devices submit authenticated, rate-limited location updates to an ingestion API. The service validates the courier's active assignment and writes the latest location, with a timestamp and version, to a fast location store. It publishes the accepted update to a real-time distribution layer that routes it only to customers authorized to view that order.

For browsers, I would begin with SSE if customers only receive updates; if the same channel needs frequent bidirectional interaction, I would use WebSockets. The initial order state and history remain available through HTTP. The live stream carries replaceable location state, so an older update can be coalesced or dropped when a client is slow. On reconnect, the customer fetches a fresh snapshot and resumes live updates.

I would measure update age and p95 end-to-end latency, connection count, gateway buffer size, reconnect rate, and fan-out lag. I would not treat the stream as the source of truth: the latest location is stored independently, and order status transitions are durable. Privacy authorization must be checked on subscription and assignment changes must revoke access promptly.

## 2) A payment API must charge a card and update an order

**Interviewer:** The payment provider sometimes times out. How do you prevent double charging while keeping the order service reliable?

**Candidate:**

First I would define whether the API is synchronous from the customer's perspective. I would create a payment attempt with a stable idempotency key and persist its state before calling the provider. The key must be reused for retries of the same logical attempt, not regenerated per network request.

A timeout is an unknown outcome, not proof the charge failed. I would query or reconcile with the provider using that key before starting a new attempt. The payment result and an order-domain event would be committed through a transactional outbox, then delivered asynchronously to the order service. The order consumer would deduplicate by event ID and apply a valid state transition.

I would expose pending, succeeded, and failed states rather than returning success on mere acceptance. Retries would be bounded and provider-specific, and operators would have a reconciliation path for ambiguous cases. I would avoid claiming end-to-end exactly-once: the design aims for one business outcome through idempotency and reconciliation.

## 3) A customer sends order events to a webhook URL

**Interviewer:** Receivers may be down for hours. What does your delivery system look like?

**Candidate:**

I would persist the event and delivery record before attempting the webhook. Workers would lease delivery attempts, sign the exact request body, include a stable event ID and timestamp, and use bounded timeouts. Transient errors receive exponential backoff with jitter; permanent client errors are recorded and surfaced rather than retried forever. A delivery expires according to a documented retention window, with a manual replay path.

The receiver should acknowledge after verifying and durably recording or enqueueing the event, not after all downstream work. Duplicate delivery is expected, so the receiver deduplicates on the event ID. I would expose delivery attempts, next retry time, last response category, and replay tools to the customer.

The destination configuration is security sensitive. I would block internal and link-local destinations, validate redirects and DNS resolution to reduce SSRF risk, and handle DNS changes safely. Secrets should rotate, and request payloads should contain only necessary data.

## 4) Build notifications for millions of users

**Interviewer:** A product event may trigger email, push, and in-app notification. How would you scale it?

**Candidate:**

The business transaction writes a notification intent or outbox row atomically with the state change. A relay publishes events to a durable broker. Separate channel workers can scale independently because provider rates, payloads, and retry behavior differ. A preference and consent check happens before each delivery, with templates localized and versioned.

I would partition or shard by a stable recipient or event key where ordering matters, while avoiding a single global order. Workers use idempotency keys and record per-channel outcomes. Transient provider failures retry with backoff; permanent address or token failures are suppressed. In-app notifications can be stored in a durable user inbox, while email/push provider acceptance is tracked separately from actual delivery.

Backpressure is controlled by queue-age objectives and provider rate limits. During a major outage, non-urgent campaigns can be throttled while high-priority transactional messages retain reserved capacity. Monitoring includes event-to-enqueue delay, queue age, provider acceptance and bounce rates, duplicate suppression, and per-channel failure rates.

## 5) Design a service that publishes database changes to search and analytics

**Interviewer:** Search must update quickly, but analytics can lag. Would you make database writes call both systems?

**Candidate:**

No. Synchronous dual writes make user requests depend on both systems and create partial-success problems. I would use CDC from the database transaction log into a durable stream, then independent search and analytics consumers with their own offsets and retry policies. The search consumer can have a tighter lag objective; analytics can prioritize throughput and batching.

For initial setup or recovery, the CDC pipeline needs a consistent snapshot followed by log changes from a well-defined position. Events need primary keys, operation type, source position/version, and enough information to handle deletes. Consumers should be idempotent and reject stale versions. I would alert on connector and consumer lag and protect database log retention from being exhausted.

If downstream consumers need business meaning rather than row mutations, I would publish domain events through an outbox instead of exposing database schemas as the contract. CDC and domain events can coexist for different purposes.

## 6) A real-time collaboration product has slow and disconnected clients

**Interviewer:** Users edit shared documents concurrently. How do you communicate edits and recover from a disconnect?

**Candidate:**

I would use a bidirectional connection such as WebSockets for low-latency edit operations, but persist operations or document snapshots independently. Each operation has an identity, document ID, author, and version/sequence metadata. The collaboration model—OT or a CRDT—defines how concurrent edits converge; transport ordering alone does not solve concurrent editing.

The server validates authorization and operation shape, applies or coordinates the operation, persists it, then broadcasts it. Clients acknowledge a version and reconnect with their last durable cursor. The server replays missing operations when retained or sends a current snapshot plus subsequent changes. Duplicate submissions are deduplicated by operation ID.

I would cap per-client buffers and operation sizes, apply backpressure, and disconnect clients that cannot keep up. Metrics include document-version lag, reconnect/resync frequency, operation rejection, persistence latency, and gateway memory. For very large documents, snapshots and compaction bound replay cost.

## 7) A queue backlog grows during a downstream outage

**Interviewer:** Consumers are retrying and the queue is growing. Should you add more workers?

**Candidate:**

Not until I know whether the consumer or its downstream dependency is the bottleneck. More workers may multiply load against an already failing dependency. I would inspect queue age, arrival and completion rates, retry volume, dependency saturation, and in-flight work.

I would stop immediate retry loops, use bounded exponential backoff, and apply a circuit breaker or pause consumption if the dependency is unhealthy. Admission control can reject or defer new low-priority work, and priority queues or reserved capacity may protect critical work. The queue must have a retention and capacity policy; if work cannot finish before its deadline, accepting it is misleading.

Messages that exhaust attempts go to a DLQ with context and alerts. After the dependency recovers, consumers resume at a controlled rate. I would only scale worker count once downstream capacity and processing cost support it.

## 8) Pick a transport for a live sports score app

**Interviewer:** Scores should update quickly, clients are mostly read-only, and missing an update must not leave the displayed score permanently wrong.

**Candidate:**

I would use SSE as the browser push channel if only the server sends updates, with HTTP for initial state and explicit refresh. Each event includes a sequence or event ID. The server retains enough recent updates to support resume; if the cursor is too old or invalid, the client fetches a full score snapshot and reconnects. This makes the live channel an optimization rather than the only source of truth.

A durable event stream or database stores authoritative score changes, and a fan-out layer distributes them to connected clients. Updates can be coalesced by match if a client is behind, because the latest score supersedes intermediate display states, while audit history remains durable. I would compare SSE with WebSockets if client interactivity, mobile platform constraints, or gateway economics make bidirectional connections more appropriate.

I would define a latency objective, such as p95 from accepted score event to browser delivery, and monitor stream disconnects, replay gaps, cursor resets, and stale-score duration. On reconnect, jitter prevents a stampede.

## Interviewer follow-up checklist

- What is the durable acceptance point, and what does a successful response promise?
- Which events can be duplicated, reordered, delayed, or lost?
- How does a client or consumer recover after a disconnect or crash?
- What queue, buffer, or retry limit protects the system under overload?
- Which operations require idempotency or reconciliation?
- What metrics prove the user-visible latency and reliability objectives are met?
