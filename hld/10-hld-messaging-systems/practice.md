# Messaging Systems: Practice Questions and Answers

Use these questions to check that you can connect broker behavior to the business guarantee. In a strong answer, distinguish transport delivery from successful business processing.

## 1) What makes Kafka a good fit for a system-design problem?

**Answer:**

Kafka is a good fit for high-throughput event streams where partitioned ordering, retention, replay, and independent consumer groups are valuable. Producers append to topic partitions; consumers track offsets and can replay within retention. It is less naturally suited to arbitrary per-message delays or task semantics that depend on visibility leases. A useful answer also names partition-key design, replication/acknowledgment policy, consumer lag, schema evolution, and the fact that business effects may be repeated.

## 2) How do Kafka partitions and consumer groups affect ordering and parallelism?

**Answer:**

A partition is the ordering unit. A stable key can route related records to the same partition, preserving their append order for a consumer group. Members of one group divide partitions, so parallelism is bounded by partition count; separate groups can independently read the same records. This does not guarantee end-to-end business order if producers race, records are retried, or a downstream side effect completes out of order.

## 3) Compare RabbitMQ, SQS, SNS, and Pulsar.

**Answer:**

RabbitMQ is a flexible exchange-and-queue broker suited to routing and task dispatch. SQS is a managed queue, commonly used for background work with low broker-operations burden; standard and FIFO queues have different semantics. SNS is a managed pub-sub topic for fan-out, often paired with SQS queues for durable per-subscriber processing. Pulsar is a distributed pub-sub platform with persistent topics and multiple subscription modes, fitting large or multi-tenant streaming deployments where its operational model is justified. Confirm service-specific ordering, retention, payload, retry, and quota behavior before selecting.

## 4) A producer receives a publisher confirm. Can it tell the user that the work is finished?

**Answer:**

No. A publisher confirm generally reports broker acceptance according to configured routing and durability behavior, not consumer completion. The API can report “accepted” if that is the contract, ideally with an operation ID and status lookup. To report “completed,” it needs a separate result or workflow state from the consumer.

## 5) When should a consumer acknowledge a message?

**Answer:**

After it has completed the required durable work, or after the work has been safely handed to another durable boundary. Acknowledging before the effect can lose work if the consumer crashes. Acknowledging after the effect can cause redelivery if the process crashes before the ack, so the handler should be idempotent and use a stable message ID.

## 6) What is the difference between at-most-once and at-least-once delivery?

**Answer:**

At-most-once avoids retry-induced duplicates but can lose a message when delivery or processing fails. At-least-once retries after uncertain failure and can produce duplicates. Durable business processing commonly uses at-least-once delivery plus an inbox, idempotency key, or naturally idempotent update. “Exactly once” must name the exact transactional boundary and systems it excludes.

## 7) A workflow needs messages ordered per account, but accounts can be processed in parallel. How would you design it?

**Answer:**

Partition or group by a stable account ID and process each account's lane serially, while processing different lanes concurrently. Include a version or sequence number and reject, defer, or reconcile gaps and stale events. Define what happens when one account's message repeatedly fails, because strict order can block later work for that account.

## 8) How do you choose a message retention period?

**Answer:**

Choose a period that covers the longest expected consumer outage, recovery, and replay window, plus operational margin. Monitor oldest unprocessed age and retention headroom. Separately satisfy business audit and privacy requirements: broker retention is not automatically an archive, and longer retention can increase cost and prolong storage of personal data.

## 9) How would you implement a message that should execute at a future time?

**Answer:**

Use a native scheduled-message capability if its supported delay and delivery semantics match the requirement. Otherwise, use a durable scheduler or due-time index that publishes when work becomes eligible, with a stable message ID and idempotent consumer. Define cancellation, rescheduling, clock-skew tolerance, retry behavior, and cleanup. Do not confuse delayed delivery with a visibility timeout or consumer sleep.

## 10) What are the benefits and costs of batching?

**Answer:**

Batching reduces request and network overhead, improving throughput. It can add latency while waiting to fill a batch, increase memory pressure, and complicate partial failures. Bound the maximum size and wait time, track per-record outcomes, and retry only failed items when the broker/provider supports partial results. Avoid delaying latency-sensitive traffic just to fill a batch.

## 11) Explain the transactional outbox and its limitation.

**Answer:**

The business row and an outbox event are written in one database transaction. A relay publishes committed rows and marks them sent, closing the database/broker dual-write gap. The relay can crash after publish but before marking, causing duplicate publication; therefore, downstream consumers still need idempotency. The outbox guarantees a durable handoff, not exactly-once end-to-end processing.

## 12) How does an inbox prevent duplicate business effects?

**Answer:**

The consumer inserts a unique message ID into an inbox table and applies the local business update in the same database transaction. A redelivery conflicts on the unique key and is acknowledged without repeating the effect. Retain the record for at least as long as messages may be replayed. An external API call is not made atomic by the inbox; use provider idempotency, another outbox, or reconciliation.

## 13) What schema changes are usually safest for independently deployed producers and consumers?

**Answer:**

Additive optional fields with defaults are often safer than changing or removing existing fields, but compatibility depends on serialization format and registry policy. Define backward, forward, full, and transitive compatibility in terms of actual reader/writer deployments. Validate schemas in CI, test old and new versions together, preserve field meaning, and plan deprecation before removal.

## 14) How do fan-out and fan-in differ?

**Answer:**

Fan-out distributes a message or event to multiple independent destinations, each with its own progress and failure handling. Fan-in correlates inputs or responses from multiple producers and combines them. Fan-in needs a correlation ID, expected-participant or completion policy, duplicate handling, deadline, and behavior for partial or late results. Neither pattern by itself creates a distributed transaction.

## 15) Describe a scatter-gather request and its main latency risk.

**Answer:**

Scatter-gather sends requests to several services and aggregates their responses. If all responses are required, tail latency is controlled by the slowest participant. Set an overall deadline and decide whether to wait for all, a quorum, or return partial results. Make retries idempotent, cap concurrency, and ignore or separately record results that arrive after the aggregator has completed.

## 16) What is a routing slip, and when might an orchestrator be preferable?

**Answer:**

A routing slip carries a sequence of steps that a message visits, advancing to the next service after each step. It is useful for a route that varies but remains a simple, known sequence. Persist progress, make each step retry-safe, and validate destinations. For long-running workflows with branching, human approvals, timers, or compensation, a workflow orchestrator often makes state and recovery clearer.

## 17) Why use the claim check pattern?

**Answer:**

It stores a large payload in object storage and puts a compact, authorized reference in the message. This avoids broker size limits and repeated large payload transfer. Upload the object before publishing the reference, include a checksum/version, protect access, retain the object long enough for replay, and clean it up if publication fails. A reference should not be a public, guessable URL.

## 18) How is a command different from an event?

**Answer:**

A command requests an action from an owner, such as `ReserveInventory`; the owner may accept or reject it. An event records a fact that has already occurred, such as `InventoryReserved`, and can be consumed by many interested systems. Name and model them distinctly so consumers do not mistake a request for a completed outcome.

## 19) What belongs in a message envelope?

**Answer:**

Common metadata includes a stable message ID, message type, schema version, event/publication timestamps, correlation and causation IDs, producer or tenant identity where relevant, and trace context. Keep it separate from domain payload, validate untrusted fields, and do not include credentials or unnecessary personal data. Metadata should support deduplication, compatibility, observability, and routing without duplicating the entire business database.

## 20) Queue age is rising, but queue depth is stable. What could be happening?

**Answer:**

The system may be processing fewer or slower messages while new traffic roughly matches completed traffic, leaving old messages stuck in a tail. A poison message, blocked ordered partition, slow dependency, or uneven key distribution can cause this. Inspect per-partition/group lag, oldest-message age, retry rates, worker saturation, and downstream latency. Queue depth alone does not show whether deadlines are being met.

## Quick revision checklist

- State what publication and consumer acknowledgments guarantee.
- Name the ordering key and ordering scope.
- Explain how duplicates, retries, and partial failures are handled.
- Distinguish queues, pub-sub topics, and retained logs.
- Bound retention, delays, batches, buffers, and in-flight work.
- Explain replay, schema evolution, overload, and operator recovery.
