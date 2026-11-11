# Architectural and Practical Patterns — Mock Interview Q&A

Strong answers trace responsibilities and dependencies, then explain the cost and consistency guarantees of the design.

## 1. Walk through a request in a layered application.

**Answer:** The transport layer parses and authenticates the incoming request and maps it to an application command. Middleware handles cross-cutting concerns such as tracing and request limits. A use-case handler coordinates the operation, invokes domain behavior, and uses ports such as repositories. Infrastructure adapters implement those ports against databases or external services. The result is mapped back to the transport response. I would keep protocol concerns at the edge and invariants with their domain owner.

## 2. How do MVC and MVVM differ?

**Answer:** MVC separates model, view, and controller, with the controller coordinating user input and model operations. MVVM adds a ViewModel that presents UI-ready state and actions, often supporting data binding and isolatable UI logic. Frameworks implement both differently, so I would specify the actual update flow and avoid moving domain policy into either controller or ViewModel.

## 3. What is the benefit and risk of Layered Architecture?

**Answer:** Layers clarify responsibilities and can isolate transport, application workflows, domain policy, and infrastructure. Dependency inversion lets stable policy depend on contracts rather than concrete adapters. The risk is ceremonial forwarding layers or leaked details that make the architecture harder without adding isolation. I would keep each layer only when it owns a distinct responsibility or boundary.

## 4. What should a Repository abstract?

**Answer:** A repository should express application- or domain-oriented retrieval and persistence needs while hiding storage-specific mechanics. It should have focused methods and explicit not-found, concurrency, and transaction semantics. It should not become a generic dumping ground or pretend the storage technology has no effect if its behavior is visible in the contract.

## 5. When is Specification useful?

**Answer:** Specification is useful for a named, reusable, composable rule that needs independent testing or application in multiple places. I would keep predicates readable and deterministic, and ensure query translation matches in-memory behavior. For a one-off simple boolean condition, direct code is often clearer than a specification framework.

## 6. What makes Middleware order important?

**Answer:** Middleware wraps later stages, so earlier stages often observe both request and response. Tracing may need to wrap authentication to include denied requests; error handling may need to wrap all stages; authentication must precede protected work. Short-circuit and exception behavior also affect which stages execute. I would document and test the order and avoid logging sensitive data.

## 7. What belongs in request context?

**Answer:** Request-scoped identity, cancellation, deadline, correlation, and tracing metadata can belong in context. Ordinary business inputs should be explicit typed parameters, not hidden in a context bag. Context should propagate to downstream work and should not be retained past request lifetime. Ambient framework contexts require the same lifecycle discipline.

## 8. How do Controller, Handler, and Service differ?

**Answer:** A controller adapts transport to application calls and maps results to protocol responses. A handler often implements one use case and coordinates domain behavior and ports. An application service may group or coordinate related use cases, while a domain service owns domain behavior that does not fit one entity. The labels are less important than explicit responsibility and avoiding duplicated policy.

## 9. Where should business logic live?

**Answer:** Invariants belong with the domain model or a domain policy that owns them; use-case coordination belongs in the application layer. The BLL is a broad term for these rules and workflows, not necessarily one class. Presentation should not duplicate business rules, and the same invariant should not drift across the domain and application layers.

## 10. What does Unit of Work guarantee?

**Answer:** A Unit of Work groups persistence changes within a defined commit boundary, often one database transaction. It can provide atomicity for participating writes, subject to the database's transaction semantics. It does not make external network calls atomic, guarantee conflict-free concurrent updates, or remove the need for rollback and failure handling. Those constraints must be explicit.

## 11. How would you handle an order write and an external payment call?

**Answer:** A database transaction cannot generally atomically commit with a remote payment provider. I would avoid holding a database transaction open across an unreliable network call. The design needs an explicit workflow, such as a payment state machine with idempotent provider requests and an outbox or saga for reliable coordination. I would define recovery for uncertain outcomes and compensating actions where possible.

## 12. How do you prevent abstraction layers from becoming ceremony?

**Answer:** I ask what change, dependency, or policy boundary each layer isolates. If a class only forwards calls and provides no stable contract, orchestration, validation, or useful test seam, it may not be needed. I preserve layers that separate volatile infrastructure from business policy or give a meaningful use-case boundary, and keep the module structure proportional to the system.
