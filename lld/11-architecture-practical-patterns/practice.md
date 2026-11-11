# Architectural and Practical Patterns — Practice Q&A

For each answer, trace ownership and dependencies explicitly. Names can vary by framework; responsibilities should not.

## 1. Trace order creation

**Question:** Trace an HTTP request that creates an order through controller, handler, domain, repository, and response. What should each layer own?

**Answer:** The controller parses transport input and maps the result to HTTP. Middleware handles cross-cutting request concerns and supplies validated identity/context. The handler coordinates the create-order use case. Domain objects enforce order invariants and domain operations. A repository port describes how the application loads or saves the aggregate; infrastructure implements it using storage. Persistence and domain failures are mapped to stable application/transport outcomes. The controller should not duplicate domain rules, and the repository should not decide business eligibility.

## 2. Refine a generic repository

**Question:** A repository exposes `saveEverything(anything)`. Refine its contract around a specific aggregate and use case.

**Answer:** Name the aggregate and intent, for example `OrderRepository.load(orderId)` and `save(order)`, with explicit not-found and concurrency behavior. If a use case needs a specialized projection or query, add a focused method with meaningful inputs and output rather than exposing arbitrary database queries. Decide whether writes are immediate or staged in a Unit of Work.

## 3. Compose specifications

**Question:** Compose specifications for an eligible discount. How do you keep rule evaluation understandable and testable?

**Answer:** Model named predicates such as `CustomerIsActive` and `OrderMeetsMinimum`, then combine them with an explicit AND if composition is useful. Test each rule independently and test the combined boundary cases. Avoid building a generic expression language for one simple rule. If the rule is translated to a database query, verify null, rounding, and time semantics match in-memory evaluation.

## 4. Authentication and tracing

**Question:** Add authentication and tracing middleware. Which runs first, and how are short-circuits and exceptions represented?

**Answer:** Tracing often wraps authentication so successful and rejected requests both receive trace information, while avoiding logging secrets. Authentication may short-circuit with an explicit unauthenticated/forbidden result. Exceptions should flow to a defined error-mapping boundary and preserve observability. Ordering depends on what each stage needs, so it should be configured and tested rather than accidental.

## 5. Unit of Work across repositories

**Question:** A transaction spans two repositories. Explain where Unit of Work belongs and what consistency assumptions must be explicit.

**Answer:** The application use-case boundary can own the Unit of Work lifetime, coordinating repository changes and committing once the operation succeeds. Repository implementations participate in the same underlying transaction where supported. Define rollback, commit failure, isolation, and concurrent update behavior. A Unit of Work over one database does not make a remote API call atomic; cross-system workflows need a separate consistency design.

## 6. MVC or MVVM

**Question:** Choose MVC or MVVM for a form with derived validation state. Identify the ViewModel's responsibilities.

**Answer:** MVVM is a reasonable fit when the form has substantial derived presentation state or data binding. The ViewModel can expose input state, validation messages, enabled/loading state, and actions while delegating business validation to the application/domain. The view renders and forwards interaction. For a small form with little state, a simpler MVC/controller arrangement may be easier.

## 7. Request context contents

**Question:** Which values belong in request context, and which should be ordinary arguments?

**Answer:** Request-scoped cancellation, deadline, identity, correlation, or trace metadata are reasonable context values. Business inputs such as order contents or chosen pricing rules should be explicit arguments so they are typed and visible. Context should not become an unbounded global bag or outlive the request.

## 8. Where does a business rule go?

**Question:** A discount rule is duplicated in a controller and an entity. Where should it live?

**Answer:** Remove the duplicate and place the invariant with the domain concept or a domain policy/service that owns the rule. The controller should adapt the request, not calculate eligibility independently. The application use case can coordinate the rule with other operations. Tests should exercise the rule once at its owner and verify the use case invokes it appropriately.

## 9. Repository and transaction boundaries

**Question:** Should every repository `save` commit immediately?

**Answer:** It depends on the consistency boundary. Immediate commit can be suitable for an isolated write, but a use case changing multiple records may need a transaction that stages repository operations and commits once. A Unit of Work can express that boundary. The design should avoid accidental partial commits and make failure/rollback behavior observable.

## 10. Dependency direction

**Question:** How can application code depend on persistence without depending on a concrete database adapter?

**Answer:** Define a repository or port contract in a stable application/domain-facing module and implement it in infrastructure. The application invokes the contract; the composition root wires the adapter. Runtime calls reach persistence, but source dependencies point toward the stable contract. Avoid letting database-specific query types leak through the port unless that is an intentional trade-off.
