# High-Level Design (HLD) Roadmap

This document covers the core topics and interview themes for High-Level Design preparation.

## 1. Introduction to System Design

### Key topics
- What is System Design?
- HLD vs LLD vs Machine Coding
- How System Design is evaluated in interviews

## 2. Core System Design Concepts

### Key topics
- Scalability
- Availability
- Reliability
- Single Point of Failure (SPOF)
- Latency vs Throughput vs Bandwidth
- Consistent Hashing
- CAP Theorem
- Consistency Models
- Back-of-the-Envelope Estimation
- Resilience
- Maintainability
- Autoscaling

## 3. Networking Fundamentals

### Key topics
- OSI Model
- IP Addressing
- TCP vs UDP
- HTTP vs HTTPS
- Domain Name System (DNS)
- TLS Handshake Overview
- Checksums
- Proxy vs Reverse Proxy
- NAT and Firewalls
- HTTP/1.1 vs HTTP/2 vs HTTP/3
- Keep-Alive Connections
- HTTP Headers and Security Headers
- CORS

## 4. Load Balancing

### Key topics
- What are Load Balancers?
- Load Balancing Algorithms
- DNS Load Balancing
- Anycast Routing
- Global vs Regional Load Balancers
- Hardware vs Software Load Balancers
- Rendezvous Hashing
- Health Checks (Active vs Passive)
- Sticky Sessions
- Load Balancer Failover
- Load Shedding

## 5. API Design

### Key topics
- What is an API?
- Data Formats (JSON, Protobuf, Avro)
- API Architectural Styles
- REST API Design
- GraphQL Deep Dive
- gRPC Deep Dive
- API Versioning Strategies
- Idempotency
- Pagination and Filtering
- Routing
- Serialization and Deserialization
- Request Validation and Transformation
- CRUD API Design Deep Dive
- OpenAPI and API Documentation Standards
- API Composition
- Service Contracts

## 6. API Infrastructure

### Key topics
- API Gateways
- Rate Limiting
- Throttling vs Rate Limiting
- Request Hedging

## 7. API Security

### Key topics
- Authentication vs Authorization
- Session vs Token Based Authentication
- JWT
- OAuth / OAuth2
- Single Sign-On (SSO)
- OpenID Connect
- API Keys
- Multi-Factor Authentication (MFA)
- Service-to-Service Authentication

## 8. Rate Limiting and Traffic Management

### Key topics
- API Quotas and Usage-Based Limits
- Burst Traffic Handling
- Concurrency Limits
- Fair Queuing and Priority Queues
- Admission Control
- Traffic Shaping
- Distributed Counters

## 9. Communication Patterns

### Key topics
- Synchronous Communication
- Asynchronous Communication
- Real-Time Communication
- Long Polling
- WebSockets
- Server-Sent Events (SSE)
- Webhooks
- WebRTC
- Message Queues
- Publish-Subscribe
- Dead Letter Queues
- Change Data Capture (CDC)
- Message Delivery Semantics
- Backpressure Handling
- Transactional Emails and Notifications

## 10. Messaging Systems

### Key topics
- Kafka for System Design
- RabbitMQ vs SQS vs SNS vs Pulsar
- Message Acknowledgments
- Message Ordering
- Message Retention
- Delayed Messages
- Message Batching
- Transactional Outbox Pattern
- Inbox Pattern
- Schema Registry and Schema Evolution
- Fan-Out and Fan-In
- Scatter-Gather and Aggregator
- Routing Slip
- Claim Check Pattern
- Command Message and Message Envelope

## 11. Real-Time Systems

### Key topics
- Presence, Online Status and Typing Indicators
- Chat Message Storage
- Offline Message Delivery
- Connection Management
- Real-Time Leaderboards
- Real-Time Collaboration
- Operational Transformation
- CRDTs (Conflict-Free Replicated Data Types)

## 12. Caching

### Key topics
- Caching Fundamentals
- What is Caching?
- Read-Through vs Write-Through Cache
- Caching Strategies
- Cache Eviction Policies
- Distributed Caching
- Content Delivery Network (CDN)
- Distributed Cache Architecture
- Cache Invalidation
- Cache Stampede
- Cache Warming
- Cache Consistency
- Negative Caching
- Hot Keys
- Multi-Level Caching
- Browser Caching
- HTTP Cache-Control
- Request Coalescing

## 13. Content Delivery and Media

### Key topics
- Signed URLs and Signed Cookies
- Image Optimization
- Video Streaming (Adaptive Bitrate, HLS, DASH)

## 14. Database Fundamentals & Storage Engines

### Key topics
- Database Types
- SQL vs NoSQL
- ACID Transactions
- Database Internals
- How Databases Guarantee Durability
- Elasticsearch and Search Systems
- Database Schema Design
- Normalization
- Keys and Constraints
- MVCC
- Optimistic vs Pessimistic Locking
- Database Migrations
- Bloom Filters
- B-Trees and B+ Trees
- LSM Trees
- Write-Ahead Logging (WAL)

## 15. Database Read & Write Scaling

### Key topics
- Indexing
- Query Optimization
- Read Replicas
- Denormalization
- Materialized Views
- Connection Pooling
- N+1 Query Problem
- Vertical Partitioning
- Sharding
- Sharding vs Partitioning
- Data Compression
- Multi-Region Databases
- Hot Partitions
- Cross-Shard Queries and Transactions

## 16. Storage Systems

### Key topics
- Object Storage
- File Storage
- Block Storage
- Distributed File Systems
- Data Durability and Replication
- Data Archiving
- Content-Addressable Storage
- Metadata Storage
- Erasure Coding
- Data Deduplication
- Storage Tiering (Hot vs Cold)
- Data Retention and Lifecycle Policies
- Immutable Storage

## 17. Search Systems

### Key topics
- Text Analysis (Tokenization, Stemming, Stop Words)
- Relevance Scoring (TF-IDF, BM25)
- Fuzzy Search
- Autocomplete and Typeahead
- Faceted Search

## 18. System Design Tradeoffs

### Key topics
- Vertical vs Horizontal Scaling
- Concurrency vs Parallelism
- Long Polling vs WebSockets
- Stateful vs Stateless Architecture
- Strong vs Eventual Consistency
- Push vs Pull Architecture
- Cost vs Performance Tradeoffs
- Latency vs Consistency Tradeoffs

## 19. Distributed Systems Fundamentals

### Key topics
- What is a Distributed System?
- Distributed State
- Anti-Entropy, Read Repair and Hinted Handoff

## 20. Failure Handling

### Key topics
- Heartbeats
- Handling Failures in Distributed Systems
- Failure Detection Mechanisms
- Graceful Shutdown

## 21. High Availability and Disaster Recovery

### Key topics
- Active-Active vs Active-Passive
- Hot, Warm and Cold Standby
- Failover Strategies
- Disaster Recovery
- RTO and RPO
- Backup and Point-in-Time Recovery
- Chaos Engineering and Fault Injection
- Multi-AZ Deployment

## 22. Time and Ordering

### Key topics
- Clock Skew
- Vector Clocks

## 23. Coordination and Consensus

### Key topics
- Consensus Algorithms Overview
- Leader Election
- Gossip Protocol
- Distributed Locking
- Byzantine Fault Tolerance
- Split-Brain Problem

## 24. Distributed Transactions

### Key topics
- Two-Phase Commit (2PC)
- Three-Phase Commit (3PC)
- SAGA Pattern
- ACID vs BASE

## 25. Data Structures for Scale

### Key topics
- Geohash
- Quad Trees
- R-Trees
- Skip Lists
- Merkle Trees
- HyperLogLog
- Count-Min Sketch

## 26. Architectural Patterns

### Key topics
- Client-Server Architecture
- Monolithic Architecture
- Microservices Architecture
- Layered Architecture
- Serverless Architecture
- Event-Driven Architecture
- CQRS
- Event Sourcing
- Peer-to-Peer (P2P) Architecture
- Hexagonal Architecture
- The Twelve-Factor App
- Modular Monolith
- Space-Based Architecture
- Pipe-and-Filter Architecture
- Master-Worker Architecture
- Cell-Based Architecture
- Plugin Architecture
- Edge Computing

## 27. Microservices Patterns

### Key topics
- Service Discovery
- API Gateway Pattern
- Backend for Frontend (BFF)
- Sidecar Pattern
- Circuit Breaker Pattern
- Bulkhead Pattern
- Strangler Fig Pattern
- Service Mesh
- Distributed Configuration
- Configuration Management
- Service Decomposition
- Database per Service
- Ambassador Pattern
- Graceful Degradation
- Microservices Testing

## 28. Domain-Driven Design

### Key topics
- What is Domain-Driven Design?
- Bounded Contexts
- Aggregates
- Domain Events
- Anti-Corruption Layer

## 29. Multi-Tenancy

### Key topics
- Single-Tenant vs Multi-Tenant
- Tenancy Data Models
- Tenant Identification and Context
- Tenant Isolation
- Noisy Neighbor Problem
- Tenant Quotas and Rate Limits
- Tenant Configuration and Encryption
- Tenant-Aware Caching and Sharding
- Tenant Provisioning and Migration
- Tenant Data Export and Deletion
- Tenant-Level Monitoring

## 30. Big Data Processing

### Key topics
- Batch vs Stream Processing
- MapReduce
- ETL Pipelines
- Data Lakes
- Data Warehousing
- Data Lakehouse
- Lambda Architecture
- Kappa Architecture
- Streaming Engines
- Exactly-Once Processing Semantics
- OLTP vs OLAP
- Windowing and Aggregation
- Data Lineage and Data Quality
- Event Analytics
- Reservoir Sampling

## 31. Observability

### Key topics
- Three Pillars of Observability
- Logging Best Practices
- Log Aggregation
- Correlation IDs
- Metrics and Instrumentation
- Alerting and Monitoring
- Dashboards and Runbooks
- Distributed Tracing
- SLOs, SLIs, SLAs
- Golden Signals, RED and USE Methods
- Latency Percentiles and Tail Latency
- Synthetic vs Real User Monitoring

## 32. Advanced Security

### Key topics
- SSL/TLS Deep Dive
- RBAC
- Secrets Management
- SAML
- Encryption at Rest and in Transit
- Zero Trust Architecture
- Principle of Least Privilege
- Token Rotation and Revocation
- Common Web Attacks (SQL Injection, XSS, CSRF, SSRF)
- DDoS Protection and WAF
- Audit Logging
- Data Masking and PII Protection
- Threat Modeling

## 33. System Design Interview Preparation

### Key topics
- How to approach HLD interviews
- Requirement Clarification and Assumptions
- Capacity Estimation
- Bottleneck Identification
- Tradeoff Justification
- Common System Design Mistakes
- End-to-End Case Studies

## 34. Deep-Dive Study Additions

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
