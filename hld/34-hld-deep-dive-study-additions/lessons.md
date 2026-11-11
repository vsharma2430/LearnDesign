# Deep-Dive Study Additions

### In-depth lessons for each listed topic
- Create a one-page concept note for each topic.
- Explain the problem, architecture pattern, trade-offs, and common failure modes.
- Connect each concept to at least one production system example.
- Summarize the key decisions, constraints, and assumptions.

### Practice questions and answers
- Q: How would you design a URL shortener service?
  A: Define the problem, estimate scale, propose services for write/read paths, storage choice, rate limits, caching, sharding, and observability.
- Q: How would you design a notification system?
  A: Use message queues, fan-out patterns, retry logic, dead-letter queues, idempotency, and delivery tracking.
- Q: How do you scale a read-heavy social feed service?
  A: Add caching, CDN support, read replicas, denormalized models, timeline sharding, and asynchronous fan-out.
- Q: What trade-offs do you evaluate when choosing SQL vs NoSQL?
  A: Consider consistency, query flexibility, transaction needs, operational complexity, and cost per workload.
- Q: How do you handle a database hotspot?
  A: Identify hot keys, rebalance partitions, shard by hash or tenant, add caching, and reduce write amplification.

### Mock interview Q&A
- Q: Design a chat application with 10M daily active users.
  A: Start with traffic estimation, define APIs, choose persistent storage, message queues, real-time delivery using WebSockets, presence tracking, offline message sync, and failure handling.
- Q: Design a rate-limited API gateway.
  A: Use token buckets or leaky buckets, distributed counters, quota tracking, per-client limits, per-route policies, and circuit breakers.
- Q: Design a distributed cache layer.
  A: Explain cache topology, replication, invalidation, warming, eviction policy, hot key handling, and failover strategy.
- Q: Design an e-commerce order system.
  A: Separate order creation, payment orchestration, inventory checks, retry workflows, outbox pattern, and eventual consistency with compensating actions.
- Q: Design a real-time analytics pipeline.
  A: Use event ingestion, stream processing, bounded windows, aggregation services, storage layers, and alerting dashboards.

### Interview checklist
- Clarify scope and constraints.
- State assumptions upfront.
- Estimate traffic and data volume.
- Show a high-level architecture.
- Explain key components and responsibilities.
- Discuss read/write paths and failure modes.
- Compare alternatives and justify decisions.
- End with operational considerations and monitoring plans.
