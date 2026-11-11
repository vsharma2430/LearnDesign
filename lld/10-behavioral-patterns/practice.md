# Behavioral Patterns — Practice Q&A

Try to identify the owner of each decision and state any ordering, lifecycle, or failure assumptions.

## 1. Discount policies

**Question:** A checkout can calculate discounts using one of several policies. Use Strategy and identify who selects the policy.

**Answer:** Define a discount-policy contract and implementations for the supported algorithms. A checkout operation delegates the calculation to the selected policy. The application or domain decision point that knows the customer's eligibility or configuration should select the strategy; the calculation itself should not secretly select a policy using unrelated global state. If there is only one permanent policy, a direct calculation is simpler.

## 2. Undo: Command or Memento?

**Question:** A document editor supports undo. Compare Command history with Memento snapshots.

**Answer:** Command history records operations and can be compact when commands have clear inverse information; it is useful when undoing discrete actions. Memento stores snapshots and can restore state directly, but snapshots may be large and need consistency/versioning rules. A hybrid can use commands for common actions and snapshots as checkpoints. Choose based on state size, operation reversibility, and undo semantics.

## 3. Validation pipeline

**Question:** A request passes through authentication, validation, and logging. Model the order using Chain of Responsibility and define failure behavior.

**Answer:** Compose an ordered chain, for example request logging/tracing, authentication, then validation before the handler, depending on which information logging is allowed to record. Authentication denial and validation failure should terminate with explicit errors; a handler's decision not to handle must be distinct from an exception. Ensure the chain has a defined terminal handler and that failure responses are observable.

## 4. Media player state

**Question:** A media player has stopped, playing, and paused states. List valid events and transitions before choosing a State implementation.

**Answer:** Possible transitions are stopped + play → playing; playing + pause → paused; paused + resume → playing; playing/paused + stop → stopped. Define invalid events such as resume while stopped and their behavior. A simple enum and transition function may be enough; use State objects if each state has substantially different behavior or grows independently.

## 5. Chat room mediator

**Question:** A chat room coordinates participants. Explain how Mediator can reduce coupling and how it can become too central.

**Answer:** Participants send messages to the room, which routes them to recipients or groups. Participants no longer need direct references to one another. The room becomes too central if it accumulates unrelated user policy, persistence, presence, moderation, and transport responsibilities. Keep coordination cohesive and split distinct concerns when the mediator becomes difficult to reason about.

## 6. Visitor and expression tree

**Question:** A stable expression tree needs multiple operations such as printing and evaluation. Is Visitor a fit? What if new expression types are frequent?

**Answer:** Visitor can fit when expression types are stable and new operations are common: each visitor implements the operation for every element type. If new expression types are frequent, every visitor needs updating and Visitor becomes costly; polymorphic methods or another dispatch approach may be better. The decision depends on which axis is expected to change.

## 7. Observer lifecycle

**Question:** A long-lived publisher notifies UI listeners. What resource and error concerns should the design address?

**Answer:** Subscriptions need a disposal or unsubscribe path so the publisher does not retain listeners after their view is gone. Define synchronous/asynchronous delivery, ordering, and what happens if one listener throws. One failing observer should not accidentally prevent all later observers unless that is the explicit contract. If delivery must be durable, in-process Observer is insufficient.

## 8. Retry commands

**Question:** A queued payment command fails with a timeout. Can it be retried safely?

**Answer:** Not without knowing whether the payment may already have succeeded. The command needs idempotency keys, deduplication, or a status-reconciliation step before retrying. It should classify retryable failures, bound attempts, and preserve the original request identity. A timeout is an uncertain outcome, not proof that no side effect occurred.

## 9. Iterator mutation

**Question:** A collection is modified while a consumer iterates it. What policies can the collection choose?

**Answer:** It can reject mutation with fail-fast behavior, let the iterator traverse a snapshot, or explicitly support concurrent mutation with defined consistency guarantees. The choice should reflect whether readers need a stable view and the cost of copying or synchronization. Document and test the behavior rather than letting it depend on incidental implementation details.

## 10. Choose a behavioral pattern

**Question:** Several objects must react to a successful order placement, but the order service should not know each consumer. Which pattern could help, and what is not solved?

**Answer:** Observer or an event-publishing mechanism can decouple the order service from subscribers such as notifications and analytics. In-process Observer does not guarantee durable delivery, ordering across restarts, or exactly-once processing. If those guarantees are required, use a durable event or messaging design and define delivery/idempotency semantics.
