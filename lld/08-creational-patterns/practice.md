# Creational Patterns — Practice Q&A

Try each prompt before reading its answer. The answers are examples of reasoning, not the only valid designs.

## 1. Game enemy creation

**Question:** A game creates one of several enemy types from level data. Compare a factory method, registry, and direct conditional.

**Answer:** For a small, closed set of enemy types, a conditional in a focused creation function is straightforward. A factory method fits if different game modes or creators need to customize product creation while sharing a workflow. A registry fits when keys and creators are configured or extended independently, but it needs duplicate-key, unknown-key, and registration-lifecycle rules. I would choose based on whether the set is closed and who owns selection, not on a preference for a pattern.

## 2. UI product families

**Question:** A UI toolkit supports matching buttons, menus, and dialogs for different platforms. Explain why Abstract Factory might help.

**Answer:** The platform is a family-level decision and the widgets need to remain compatible. One selected factory can create the button, menu, and dialog for that platform, preventing clients from accidentally mixing families. This costs a factory contract and implementations; it is less useful if each product varies independently or there are only simple constructors.

## 3. Notification builder

**Question:** Design a builder for a notification with required recipient and optional title, body, and attachment. Where do validation rules run?

**Answer:** Require the recipient when creating the builder, then expose optional setters for title, body, and attachment. The builder's `build` operation creates the notification only after checking invariants, such as a non-empty recipient and any attachment limits. The resulting notification should preserve its own invariants even if it is later deserialized or constructed through another path. A builder is justified if optional fields or validation make construction unwieldy; otherwise a named constructor may suffice.

## 4. Prototype copy semantics

**Question:** A prototype has a mutable list of children. What must copying do to avoid accidental shared mutation?

**Answer:** If the copy is meant to be independent, it must copy the list and each mutable child whose ownership belongs to the prototype. Immutable child values can be shared. Resource handles should generally not be duplicated blindly; their lifecycle needs a separate policy. A test should mutate a copied child/list and assert that the original remains unchanged.

## 5. Connection pool

**Question:** A connection pool is proposed to improve performance. Which lifecycle and measurement questions must be answered first?

**Answer:** Measure connection-creation cost and current latency/throughput to verify that creation is a bottleneck. Define maximum size, wait/timeout behavior on exhaustion, acquisition cancellation, health checks, reset-on-return, cleanup on exceptions, shutdown, and how in-use connections are handled. Also define who owns release and how leaked borrows are detected. If measured gains do not justify these guarantees and contention, avoid the pool.

## 6. Singleton concerns

**Question:** Explain one testing or concurrency concern with a global Singleton.

**Answer:** A mutable Singleton can retain state between tests, making test order affect results; explicit construction and injection provide isolation. Concurrent calls may also race on its mutable state even if the instance itself is initialized only once. Safe initialization and safe concurrent use are separate properties.

## 7. Pick the pattern

**Question:** A report can be exported to PDF or CSV, and the export workflow is otherwise the same. What creation approach would you start with?

**Answer:** I would define an exporter contract and place product selection at the report workflow's boundary. A factory method or a small injected factory may work if export type is selected at runtime. If the choice is a simple, fixed conditional, that can remain the clearest option. Abstract Factory is unnecessary unless each format involves a compatible family of products.
