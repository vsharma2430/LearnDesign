# System Design Learning Repository

A structured study path for software architecture, distributed systems, and interview preparation.

This repository brings together practical notes, roadmaps, and modular exercises for both High-Level Design (HLD) and Low-Level Design (LLD). It is designed for engineers who want to strengthen architecture reasoning, design trade-offs, and object-oriented problem-solving.

## What this repo covers

- **Networking:** network foundations, IP addressing and routing, TCP/UDP, DNS, HTTP/HTTPS, modern communication protocols, and networking for system design
- **Operating systems:** processes and threads, scheduling, synchronization, deadlocks, memory management, IPC, file systems, I/O, concurrency, and Linux
- **SQL and databases:** relational design, normalization, transactions, isolation, indexing, query optimization, scaling, SQL queries, joins, and interview patterns
- **High-Level Design (HLD):** scalability, availability, caching, messaging, storage, distributed systems, architecture, reliability, and trade-offs
- **Low-Level Design (LLD):** OOP, class relationships, design principles, UML, design patterns, concurrency, error handling, and clean code
- Lessons, roadmaps, practice exercises, and interview prompts to connect fundamentals with system design

## Repository map

- [network/](./network) — numbered networking lessons, from fundamentals to interview preparation
- [topics/network.md](./topics/network.md) — networking roadmap and topic checklist
- [os/](./os) — numbered operating-systems lessons, from core concepts to Linux and interview preparation
- [topics/os.md](./topics/os.md) — operating-systems roadmap and topic checklist
- [sql/](./sql) — numbered database and SQL lessons, from relational fundamentals to query optimization
- [topics/sql.md](./topics/sql.md) — database and SQL roadmap and topic checklist
- [hld/](./hld) — HLD modules with lessons, mocks, and practice material
- [topics/hld.md](./topics/hld.md) — HLD roadmap and topic checklist
- [lld/](./lld) — LLD modules with lessons, mocks, and practice material
- [topics/lld.md](./topics/lld.md) — LLD roadmap and topic checklist

## Recommended learning flow

1. Build foundations with [networking](./network/), [operating systems](./os/), and [SQL and databases](./sql/). These tracks can be studied in parallel.
2. Follow the [HLD roadmap](./topics/hld.md) and work through the modules in [hld/](./hld/) to learn how these foundations shape system architecture.
3. Use the [LLD roadmap](./topics/lld.md) and [lld/](./lld/) to practice object-oriented modeling, patterns, and implementation-level design.
4. For each topic, explain the core idea, compare trade-offs, and work through the linked practice or mock questions.
5. Revisit topics with system-design scenarios and interview prompts to strengthen recall and communication.

## Suggested timeline

### Phase 1: Engineering foundations (Weeks 1-3)
- Study networking fundamentals, including routing, transport protocols, DNS, and HTTP
- Learn operating-system concepts such as processes, threads, memory, and synchronization
- Practice relational modeling, SQL queries, and transaction fundamentals

### Phase 2: Core system design (Weeks 4-6)
- Start HLD with system design fundamentals, networking, load balancing, APIs, and traffic management
- Connect database fundamentals to storage, caching, messaging, and search
- Practice identifying requirements, bottlenecks, and architectural trade-offs

### Phase 3: Distributed systems and architecture (Weeks 7-9)
- Study distributed systems, consistency, failure handling, availability, and disaster recovery
- Explore architectural patterns, microservices, domain-driven design, and observability
- Work through end-to-end design scenarios and explain decisions under constraints

### Phase 4: Design practice and interview readiness (Weeks 10-12)
- Complete LLD modules covering OOP, design principles, patterns, concurrency, and clean code
- Revisit SQL, OS, networking, HLD, and LLD topics where you need more practice
- Simulate interviews, refine diagrams, and prepare concise explanations

## Suggested study loops

- Fundamentals loop: concept → worked example → edge cases → interview explanation
- System design loop: concept → trade-offs → constraints → alternatives → validation
- LLD loop: requirements → entities → responsibilities → interactions → edge cases → tests
- SQL loop: model the data → write the query → check correctness → inspect performance

## Goals

- Build a broad foundation across networking, operating systems, databases, HLD, and LLD
- Understand how low-level fundamentals influence scalable system architecture
- Improve confidence for software engineering and system design interviews
- Practice reasoning over memorization
- Connect theory with real-world software design decisions

## Contributing and extending the repo

This repository is intended to grow with guided study and real-world design exercises. If you are adding new material, prefer:

- clear, structured notes
- measurable learning objectives
- practice prompts and trade-off analysis
- short examples that reinforce the concept

A well-structured repo is most useful when each topic is easy to navigate and easy to revisit.
