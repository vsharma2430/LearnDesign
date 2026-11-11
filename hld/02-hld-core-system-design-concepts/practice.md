# Practice Questions and Answers

This set is designed to help you practice core system design concepts quickly and orally.

## 1) What is the difference between scalability and performance?

Answer:

Scalability is the ability of a system to handle increasing load without a disproportionate drop in performance. Performance refers to how fast a system responds under a given load. A system can have good performance at low traffic but poor scalability when traffic increases.

Example: a database is fast for 100 users but slow for 100,000 because the query planner or storage becomes a bottleneck.

---

## 2) How would you explain availability to a non-technical stakeholder?

Answer:

Availability is the percentage of time the system is working when users need it. If we promise 99.9% availability, we are saying the service can be down for a limited time each month. For business stakeholders, availability means revenue, trust, and user retention. They care about uptime as much as speed.

---

## 3) What is a single point of failure? Give examples.

Answer:

A single point of failure is any system component whose failure causes the whole service to stop working. Common examples include a single database instance, one API server behind no load balancer, or one message queue without redundancy.

To reduce SPOFs, use redundancy, failover, replication, multiple availability zones, and health checks.

---

## 4) Why do we care about latency, throughput, and bandwidth separately?

Answer:

Because they measure different system properties.

- Latency is the time to answer one request
- Throughput is how many requests can be served in a period
- Bandwidth is the maximum amount of data that can move over a connection

A network may have high bandwidth but still show high latency because of queueing and processing delay. A service can have high throughput but poor latency if it serializes work or does expensive retries.

---

## 5) What is consistent hashing and why is it useful?

Answer:

Consistent hashing maps both keys and servers onto a ring so that when a server is added or removed, only a relatively small portion of keys need to be redistributed. This is especially useful for caches, distributed data stores, and sharded systems.

Without consistent hashing, adding a new node could cause massive remapping and require a large amount of data movement.

---

## 6) Explain CAP theorem in simple terms.

Answer:

CAP theorem says that in a distributed system with network partitions, you cannot guarantee all three of consistency, availability, and partition tolerance at once.

- CP systems prioritize correctness and may reject requests during partitions
- AP systems prioritize availability and may serve stale data
- CA is not practical in a distributed environment with network splits

Example: a payment service usually prefers consistency, while a social feed often prefers availability.

---

## 7) What are the common consistency models?

Answer:

- Strong consistency: every read sees the latest write
- Eventual consistency: replicas converge over time
- Causal consistency: dependent operations are ordered
- Session consistency: a user sees their own recent writes
- Read-your-writes consistency: after you write, you can immediately read your own update

The right model depends on business requirements. Financial systems usually need stronger consistency than recommendation systems.

---

## 8) What is a back-of-the-envelope estimate and why is it useful?

Answer:

Back-of-the-envelope estimation is a rough capacity estimate done in the early design phase. It forces you to reason with assumptions about traffic, payload sizes, and storage needs.

Example:
- 1 million requests/day
- 1 KB payload each
- total daily data = ~1 GB
- with replication and overhead, maybe 2-3 GB/day

This helps determine whether a single database or sharded database is necessary.

---

## 9) What is resilience and how is it different from availability?

Answer:

Resilience is the system's ability to keep working despite partial failures. Availability is whether the system is up. Resilience is about graceful degradation when parts fail.

Example: a recommendation service fails, but checkout still works. Or a cache fails and the app falls back to the database without crashing.

Patterns include retries, circuit breakers, timeouts, fallback responses, and bulkheads.

---

## 10) Why is maintainability important in system design?

Answer:

A system that works today but is impossible to modify is a poor long-term investment. Maintainability includes clean architecture, observability, testing, CI/CD, and documentation.

Highly maintainable systems are easier to debug, deploy, and evolve as business requirements change.

---

## 11) What is autoscaling and what should you monitor before enabling it?

Answer:

Autoscaling automatically adjusts resources based on metrics such as CPU, memory, request rate, or queue depth. It is useful for handling bursts without over-provisioning all the time.

You should monitor:
- average and peak load
- latency sensitivity
- scale-up and scale-down thresholds
- cooldown periods
- whether the app is stateless and horizontally scalable

---

## 12) Give a short answer explaining why distributed systems are hard.

Answer:

Distributed systems are hard because failures are inevitable, network latency is unpredictable, clocks are imperfect, and consistency trade-offs are unavoidable. Good system design uses reduction of failure impact, redundancy, observability, and explicit trade-offs rather than assuming the ideal environment.

---

## Quick revision checklist

Before your interview, be ready to explain:

- what scalability means
- how availability differs from reliability
- what SPOFs are and how to remove them
- the difference between latency, throughput, and bandwidth
- when CAP theorem matters
- which consistency model fits the workload
- how to do rough capacity estimates
- what resilience patterns help under failure
- why maintainability matters
- how autoscaling works and what it depends on

