# Practice Questions and Answers

This file focuses on the most common API design interview questions. The goal is not to memorize answers but to reason clearly about trade-offs.

## 1) What is an API and why is it important?

Answer:

An API is a contract that allows one system to interact with another without exposing internal implementation details. It defines endpoints, request and response schemas, authentication, error handling, and behavioral expectations.

APIs matter because they decouple systems, enable teams to evolve independently, and make integration safer and more predictable. In modern systems, APIs are not just for external clients — they are also the backbone of internal service-to-service communication.

---

## 2) Why do REST APIs usually use nouns in URLs instead of verbs?

Answer:

REST is resource-oriented. URLs represent resources such as `/users`, `/orders`, or `/products`, while HTTP methods describe the action.

This makes APIs easier to reason about:
- GET /users gets users
- POST /users creates a user
- PATCH /users/123 updates a user

Using verbs in URLs like `/createUser` or `/deleteOrder` makes the API less standardized and less predictable. It also weakens the relationship between the request and the underlying resource model.

---

## 3) What is the difference between REST and GraphQL?

Answer:

REST is resource-oriented and typically exposes multiple endpoints. GraphQL exposes a single query entry point where the client requests exactly the fields it needs.

REST is often easier to cache and is a good choice when the data model is naturally hierarchical and resource-based. GraphQL is useful when clients require flexible, nested data from many sources and want to avoid over-fetching.

The trade-off is that GraphQL adds complexity in query validation, pagination, authorization, and performance optimization. A strong answer explains that neither is universally better; the workload and product constraints matter.

---

## 4) When would you choose gRPC over REST?

Answer:

I would choose gRPC when the communication is internal, latency-sensitive, and strongly typed. It is a good fit for service-to-service calls, streaming workloads, and systems where payload size matters.

Example use cases:
- inventory service to pricing service
- auth checks in a high-throughput backend
- streaming updates from a telemetry pipeline

REST is often better for public-facing, browser-friendly, and human-debuggable APIs. gRPC is more efficient, but it is less ergonomic for ad hoc manual testing and browser clients.

---

## 5) How do you decide between JSON and Protobuf for a service contract?

Answer:

JSON is preferred when readability, debugging, and broad ecosystem compatibility matter. It is widely used for public APIs, browser clients, and external integrations.

Protobuf is better when performance and compactness matter, especially for internal microservices or latency-sensitive systems. It supports fast serialization and a strict schema contract.

The trade-off is that Protobuf is less human-readable, and you need generated clients and codegen tooling. In an interview, a strong answer explains the difference between developer ergonomics and production efficiency.

---

## 6) What is idempotency and why is it critical for APIs?

Answer:

Idempotency means that repeating the same request has the same final effect as performing it once. For example, if a client retries a payment request after a timeout, the system should not create a second charge.

This is critical in APIs that trigger side effects such as payment, order placement, inventory reservation, or refund generation. A common implementation is to use an idempotency key, store the result, and return the original response on retries.

Without idempotency, service timeouts and client retries can create duplicate or inconsistent side effects.

---

## 7) What are the common API versioning strategies?

Answer:

Common strategies are:
- URI versioning: /v1/users, /v2/users
- header-based versioning: Accept: application/vnd.company.v2+json
- content negotiation: different media types or custom headers

URI versioning is explicit and easy to debug. Header-based versioning keeps URLs cleaner but is less obvious to consumers. The best strategy depends on how often the API changes and how much compatibility you need.

A good design also prefers backward-compatible changes over major version bumps. Adding optional fields is usually safer than removing or renaming fields.

---

## 8) What are the main differences between offset and cursor pagination?

Answer:

Offset pagination uses a page number and page size, such as ?page=2&limit=50. It is simple, but it is inefficient for very large datasets because offset calculations get slower and can become inconsistent under concurrent writes.

Cursor pagination uses an opaque cursor token that points to the next page. This is typically better for large and highly dynamic datasets because it is more stable and scalable.

As a rule, offset pagination is fine for simpler data access, while cursor pagination is usually better when the dataset can be large or changing.

---

## 9) What is request validation and why is it necessary?

Answer:

Request validation ensures that the input is in a valid, safe, and semantically acceptable form before the business logic processes it.

This includes:
- required vs optional fields
- type checks
- length and format validation
- enum validation
- permission checks
- domain constraints such as ordering dates or discount rules

Validation should happen on the server side, even when client-side validation exists. Client validation is useful for UX, but it cannot be trusted for security or correctness.

---

## 10) How should a CRUD API be designed beyond basic create/read/update/delete?

Answer:

Simple CRUD is the foundation, but real APIs need business-aware behaviors. For example, a delete may be a soft delete instead of a hard delete if data retention or audit trails matter.

Other design questions include:
- should update be full replacement or partial update?
- what do we do on conflict or concurrent modification?
- does the API need extra workflow endpoints such as "cancel order" or "archive product"?

A strong API model respects domain behavior and avoids treating the database schema as the only source of truth. The API should reflect meaningful lifecycle states instead of just raw collections.

---

## 11) Why is OpenAPI documentation important?

Answer:

OpenAPI provides a machine-readable specification for the API. It helps define routes, request bodies, response models, authentication, retries, and error responses.

This is valuable because it:
- enables code generation
- improves developer onboarding
- helps with contract testing
- reduces ambiguity between teams

Good API docs are not an afterthought. They are part of the API contract and reduce support cost and integration failures.

---

## 12) What is API composition and when is it useful?

Answer:

API composition is the practice of aggregating data from multiple services into a single response that matches the client’s needs.

This is useful when a client needs related information from several domains, such as the user profile, cart, pricing, and shipping status all at once. A BFF or gateway can combine those calls to shield the client from service fragmentation.

The trade-off is increased latency and complexity. If composition is done poorly, it can create a central bottleneck. Good design balances client ergonomics with service independence.

---

## 13) What is a service contract?

Answer:

A service contract is the explicit agreement between a producer and a consumer. It includes the request and response schema, authentication expectations, semantics, error handling rules, and compatibility constraints.

Service contracts are important because they help teams evolve systems without creating hidden dependencies. They also clarify which behaviors are safe to change and which changes are breaking.

In modern distributed systems, contracts are as important as code because they define how different services depend on each other.

---

## 14) What is the biggest mistake in API design?

Answer:

The biggest mistake is treating APIs as a quick technical implementation rather than a product and contract. A poor API often lacks clear boundaries, has weak validation, poor error semantics, no versioning strategy, and no attention to backward compatibility.

A great API is easy to use, honest about failure, resilient under retry, and designed with future change in mind. That is what makes it sustainable in production.

---

## Quick revision checklist

Before an interview, be ready to explain:
- what an API is and why it matters
- the difference between REST, GraphQL, and gRPC
- when to use JSON vs binary formats
- how to version APIs safely
- why idempotency matters
- what routing and validation do
- how pagination and filtering work in practice
- why OpenAPI and service contracts matter
- how API composition changes trade-offs
