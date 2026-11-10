# Concurrency and Thread Safety — Practice

1. Two threads increment a shared counter. Explain the lost-update race and choose a lock or atomic operation.
2. A producer can outpace a consumer indefinitely. Design bounded-queue behavior and state what happens when the queue is full.
3. Two tasks acquire locks A then B and B then A. Show the deadlock and a consistent lock-order solution.
4. Choose between a mutex and semaphore for limiting access to three concurrent connections.
5. Compare token bucket and leaky bucket for an API that permits short bursts but limits sustained traffic.
6. A read-heavy cache uses a read-write lock. What performance and starvation questions should be measured?
7. Explain why a thread-safe map does not necessarily make `if absent then insert` atomic.
