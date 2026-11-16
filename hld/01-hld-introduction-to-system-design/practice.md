# Practice Questions: Introduction to System Design

These questions are designed to match the topics covered in this module and reflect the type of prompts used in real system design interviews.

---

## Q1. If you were asked to design a URL shortener, where would you start?

Answer:
Start by clarifying the problem and scope. A strong interview answer begins with questions like:
- What is the main user action: create a short URL or redirect to the original URL?
- How many URLs are created per day?
- Is the system read-heavy or write-heavy?
- Are there latency requirements for redirects?
- Do we need analytics, expiration, or abuse prevention?

After that, you would define the core components: API layer, database, hashing strategy, caching layer, rate limiting, and analytics pipeline.

---

## Q2. Why is requirement clarification so important in a real system design interview?

Answer:
Because without a clear requirement, you can design the wrong system. Interviewers want to see whether you ask the right questions before choosing architecture.

For example, if the app is a global read-heavy service, you may prioritize CDN caching and read replicas. If it is a write-heavy transactional system, you may focus on consistency, partitioning, and strong database design.

This is a core part of evaluating whether you can reason like a real engineer.

---

## Q3. What is the difference between HLD and LLD in an interview setting?

Answer:
HLD is the big-picture design: services, APIs, storage, traffic flow, and major trade-offs. LLD is the detailed design of a component: data structures, schemas, algorithms, interfaces, and validation logic.

A real interview may ask:
- "Design a ride-sharing app" → HLD
- "How would the matching engine work?" → LLD

The interviewer is checking whether you can move from architecture to implementation depth without losing the big picture.

---

## Q4. How do interviewers evaluate whether a candidate understands system design?

Answer:
They usually evaluate a candidate across several dimensions:
- requirement clarification
- structured thinking
- realistic assumptions
- scalability and reliability awareness
- bottleneck analysis
- trade-off discussion
- communication quality

A good candidate does not just draw boxes; they justify the choices, explain constraints, and show that they understand what matters in production systems.

---

## Q5. What is a common real-world system design question around scalability?

Answer:
A common question is: "Design a social media feed or news feed that supports millions of users."

The key points to discuss are:
- read-heavy vs write-heavy traffic
- cache design for hot content
- database sharding or partitioning
- feed generation strategy
- async processing for notifications or ranking
- rate limits and failover plans

This question tests whether you can think beyond a single database and a single server.

---

## Q6. How would you explain trade-offs in a real system design interview?

Answer:
You should clearly explain what you are optimizing for and what you are sacrificing.

Examples:
- Strong consistency may reduce availability during failures.
- High availability may require more replicas and more operational complexity.
- Caching improves read latency but adds invalidation and consistency challenges.
- Microservices improve modularity but increase latency and operational overhead.

Interviewers want to see that you can reason about trade-offs rather than blindly choosing the latest pattern.

---

## Q7. What is a realistic interview question related to reliability and failure handling?

Answer:
A common question is: "How would you design a payment system so it remains reliable even when downstream services fail?"

A strong answer would include:
- idempotency for payment requests
- retries with backoff
- timeouts and circuit breakers
- queues for async processing
- database transactions for core steps
- monitoring and alerting for failures
- graceful degradation when dependencies are unavailable

This checks whether you think about failures as part of the design, not as an afterthought.

---

## Q8. What is a typical interview question involving storage choice?

Answer:
A common question is: "Should this system use SQL or NoSQL, and why?"

A good answer explains the data access pattern:
- SQL fits structured data, strong transactions, and relational queries
- NoSQL fits high write throughput, flexible schemas, and large-scale unstructured data

For example:
- a banking app → SQL is often preferred
- a user activity feed or event log → NoSQL or append-heavy storage may be better

This is a realistic design discussion because real systems are chosen based on use case, not preference.

---

## Q9. What is the most common mistake in system design interviews?

Answer:
The most common mistake is jumping into a complex architecture before clarifying the problem. Candidates often start with microservices, distributed systems, or advanced patterns without understanding the actual workload.

Interviewers usually prefer a simpler, correct design that can be evolved over time than a highly complex but unsupported architecture.

---

## Q10. How should you answer when asked to design a system in a live interview?

Answer:
Use a structured flow:
1. Clarify scope and assumptions
2. Estimate scale and constraints
3. Define the high-level architecture
4. Identify key bottlenecks
5. Discuss critical components in more detail
6. Explain trade-offs and alternatives
7. Conclude with the most important design decisions

This is the same pattern used in almost all real-world system design interviews and reflects how experienced engineers reason about production systems.
