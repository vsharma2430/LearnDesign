# Concurrency and Thread Safety — Lesson Notes

**Concurrency** is overlapping progress among tasks; **parallelism** is simultaneous execution. A **process** has its own address space and resources; **threads** within a process share memory. A **thread pool/executor** reuses workers and bounds task scheduling, but queue size and rejection behavior still matter.

**Thread safety** means concurrent use preserves the object's invariants. **Synchronization** coordinates access. A **lock/mutex** grants exclusive access; a **semaphore** controls a count of concurrent permits. **Read-write locks** permit concurrent readers and exclusive writers, but can introduce starvation and overhead.

**Deadlock** is a cycle of blocked waits; consistent lock ordering and narrow critical sections help prevent it. **Livelock** is active but unproductive retrying; **starvation** is indefinite lack of progress. Timeouts and fairness policies may help but have costs.

An **immutable object** is naturally safer to share. **Concurrent collections** provide specified thread-safe operations, but compound operations may still need atomicity. **Atomic operations** and **compare-and-swap (CAS)** update state conditionally; retry loops and memory ordering require care. **Memory visibility** means one thread's writes becoming observable by another; use language-defined synchronization and happens-before rules, not assumptions about timing.

The **producer-consumer** pattern transfers work through a bounded or unbounded queue; bounded queues provide backpressure. A **thread-safe singleton** must safely publish its instance. A **rate limiter** controls admission: token bucket allows bursts up to capacity while replenishing tokens; leaky bucket smooths output at a steadier rate. Define clock behavior, scope, and overload response.
