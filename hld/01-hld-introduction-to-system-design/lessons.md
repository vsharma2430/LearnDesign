# Introduction to System Design

### Key topics
- What is System Design?
- HLD vs LLD vs Machine Coding
- How System Design is evaluated in interviews

---

## 1) What is System Design?

System design is the process of defining a software system’s architecture, components, interfaces, data flow, and operational constraints so it can satisfy both business and technical requirements at scale.

In simple terms: software development asks, "How do we build this feature?" System design asks, "How do we build a system that can handle growth, failures, and real-world traffic without breaking?"

### Why system design matters

A feature can work in a demo and still fail in production if the system is not designed for reality. Real systems must handle:
- more users
- more requests
- more data
- more failure modes
- more teams working in parallel

Common production problems include:
- traffic spikes
- database bottlenecks
- retries and timeouts
- consistency issues
- lack of observability
- poor scale-out architecture

### Core goals of a good system design

A strong system balances several goals:

- Functional correctness: Does it do the required job?
- Scalability: Can it handle growth in users, requests, or data?
- Reliability: Does it keep working when parts fail?
- Availability: Can it serve users continuously with minimal downtime?
- Performance: Is the system fast enough for the user experience?
- Maintainability: Can engineers evolve the system without chaos?
- Security: Is it protected against common attacks and misuse?
- Cost-efficiency: Can it work without excessive infrastructure cost?

### Example: a simple e-commerce checkout system

Designing an online shopping platform is not just about a checkout button. You need to think about:
- customer requests reaching the app
- authentication and session handling
- inventory checks
- payment processing
- order creation
- notifications and analytics
- retries when payment services fail

This is system design in action.

### The big idea: design for uncertainty

Production systems face unpredictable traffic and failures. A good design does not assume everything works perfectly. It assumes:
- servers crash
- networks fail
- databases slow down
- queues back up
- users retry requests

That is why system design focuses on resilient, scalable, and observable systems rather than only “working code.”

### Mermaid diagram: high-level system design flow

```mermaid
flowchart LR
    U[Users] --> A[Application Layer]
    A --> S[Services / Business Logic]
    S --> D[(Database)]
    S --> C[Cache]
    S --> Q[Message Queue]
    Q --> W[Workers / Async Jobs]
    A --> M[Monitoring & Alerts]
    S --> L[Load Balancer]
    L --> A
```

### Real-world takeaway

System design is the bridge between a product idea and a production-ready architecture. It helps teams answer questions like:
- How many users can this handle?
- What breaks when traffic increases?
- How do we make the system resilient?
- How do we store and retrieve data efficiently?
- What trade-offs are we making to optimize for speed, consistency, or cost?

### Practice angle

When asked in interviews, do not begin with a large architecture diagram. Start with:
1. Clarify requirements
2. Define constraints and assumptions
3. Estimate scale
4. Define major components
5. Discuss bottlenecks and trade-offs

---

## 2) HLD vs LLD vs Machine Coding

These three are related but very different.

### High-Level Design (HLD)

HLD focuses on the overall architecture of the system.

It answers questions such as:
- What are the major components?
- How do they communicate?
- Which services or modules exist?
- Where do we use databases, caches, queues, or CDNs?
- What are the important trade-offs?

This is usually what system design interviews test.

#### Example HLD concerns
- Monolithic vs microservices
- Database choice (SQL vs NoSQL)
- Replication and sharding
- Load balancing and API gateways
- Caching strategy
- Event-driven architecture
- Observability and failure handling

### Low-Level Design (LLD)

LLD focuses on the internal logic and implementation details of a single component or module.

It answers questions like:
- What are the classes, interfaces, and methods?
- How are data models structured?
- What algorithm is used?
- How does a service handle requests internally?
- What are the edge cases and validations?

Example: designing the internal class structure of a payment service or a URL shortener service.

### Machine Coding

Machine coding is coding-focused problem solving, usually done in a coding environment.

This is not system design; it usually tests:
- algorithm knowledge
- coding fluency
- data structure use
- correctness and optimization
- handling edge cases

Examples include:
- implementing LRU cache
- solving top-k frequent elements
- designing a parser or traversal
- implementing a queue with constraints

### Comparison

| Area | Focus | Typical Questions | Output |
|---|---|---|---|
| HLD | Architecture and system-level decisions | How should the system be structured? | High-level diagrams and components |
| LLD | Module internals and logic | How will this component work? | Classes, APIs, schemas, algorithms |
| Machine Coding | Code implementation | Write code to solve a problem | Executable working code |

### Difference in practical terms

Imagine you are asked to design a ride-sharing app.

- HLD: Which services exist? API gateway, user service, ride service, payment service, notifications, matching engine, map service?
- LLD: How does the matching engine select nearby drivers? What fields are in the Ride table? How are driver availability states stored?
- Machine coding: Write the algorithm for nearest driver search or implement a priority queue for trip matching.

### Mermaid diagram: layered view

```mermaid
flowchart TB
    A[Business Requirement] --> B[HLD]
    B --> C[System Components]
    C --> D[LLD]
    D --> E[Implementation / Machine Coding]

    B --> F[Scalability, APIs, Storage, Traffic]
    D --> G[Classes, Functions, Data Structures]
    E --> H[Working Code]
```

### Why this distinction matters in interviews

Interviewers often expect you to move from the big picture to details in a logical order:
1. Start with requirements and constraints
2. Propose the architecture (HLD)
3. Go deeper into bottlenecks or critical components (LLD)
4. If necessary, talk about algorithmic or coding-level optimization

Many candidates get stuck because they mix up the layers and jump into code too early or over-design the system without clarifying the problem.

---

## 3) How System Design is evaluated in interviews

System design interviews are not just about “drawing boxes.” They test clarity of thought, communication, technical judgment, and the ability to make trade-offs under pressure.

### What interviewers look for

A strong system design answer usually shows:

- requirement clarification: asking the right questions early
- structured thinking: breaking a complex problem into manageable parts
- trade-off reasoning: explaining why one choice is better than another
- scalability awareness: thinking beyond a single server
- storage judgment: choosing the right database or access pattern
- failure awareness: discussing what happens when parts break
- communication: explaining the design clearly and in order

### Typical evaluation rubric

A good interviewer may score you on several dimensions:

1. Problem framing
   - Can you clarify scope and assumptions?
   - Do you identify core use cases and constraints?

2. Architecture quality
   - Is the design logical and modular?
   - Are key components separated appropriately?

3. Scalability and performance
   - Do you consider traffic growth, read/write patterns, and latency?

4. Data modeling and storage
   - Do you choose storage based on access patterns?
   - Are you aware of SQL vs NoSQL trade-offs?

5. Reliability and resilience
   - Do you mention retries, timeouts, circuit breakers, queues, replication, backups, and failover?

6. Trade-off discussion
   - Do you explain what you are optimizing for and what you are sacrificing?

7. Communication and structure
   - Can you explain the design in a crisp, step-by-step way?

### Common interview flow

Most system design interviews follow a similar pattern:

1. Requirement clarification
   - What is the product?
   - Who are the users?
   - What are the expected scale and constraints?

2. High-level architecture
   - Define major services, components, and APIs

3. Back-of-the-envelope estimation
   - Estimate traffic, storage, bandwidth, and throughput

4. Deep dive on critical modules
   - Choose the most important component and design it in detail

5. Bottleneck analysis
   - Identify the main constraints and how to solve them

6. Trade-offs and improvements
   - Explain alternatives and why they are or are not chosen

### Example of good interviewer expectations

If the problem is “Design a URL shortener,” an interviewer may expect discussion on:
- API endpoints for URL creation and redirect
- database schema for URL mappings
- hashing and ID generation
- caching frequently accessed links
- rate limiting and abuse prevention
- analytics and link expiration policies

They are not asking for a perfect production architecture. They want to see whether you can reason under constraints and make sensible trade-offs.

### The most important mindset

A strong candidate does not try to impress with buzzwords. They focus on:
- clarity
- prioritization
- trade-offs
- reasoning

Weak answers often sound impressive but fail on fundamentals. Common mistakes include:
- jumping directly to microservices for a tiny app
- ignoring bottlenecks and single points of failure
- using overly complex patterns without need
- not discussing read/write behavior
- forgetting latency and backups

### Mermaid diagram: interview evaluation flow

```mermaid
flowchart TD
    A[Clarify Requirements] --> B[Estimate Scale]
    B --> C[Propose HLD]
    C --> D[Identify Bottlenecks]
    D --> E[Discuss Trade-offs]
    E --> F[Final Architecture]
    F --> G[Evaluation: clarity, scalability, resilience, communication]
```

### Interview success formula

A strong system design answer usually combines:
- requirements + constraints
- clean architecture
- realistic assumptions
- concrete trade-offs
- clear explanation

In short, system design interviews are designed to test whether you can think like a real software architect: not just code, but design for scale, reliability, and business value.

---

For additional interview-style practice questions, refer to [practice.md](./practice.md).

---

## Quick revision checklist

Before you finish a system design answer, check whether you have discussed:
- functional requirements
- non-functional requirements
- traffic assumptions
- storage model
- API boundaries
- bottlenecks
- scaling strategy
- failure handling
- trade-offs
- monitoring and operations

If you can clearly explain these, you are already thinking like a system designer.

---

## Summary

System design is not about memorizing random architecture diagrams. It is about learning how to reason about complexity, constraints, and scale in a structured way.

For this module, the key message is simple:
- Understand what a system is and why it needs design
- Know the difference between HLD, LLD, and machine coding
- Learn how interviews evaluate architecture reasoning

This foundation will make the rest of the HLD modules much easier to understand.

For a realistic mock interview walkthrough, refer to [mock.md](./mock.md).
