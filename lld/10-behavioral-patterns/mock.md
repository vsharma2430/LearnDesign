# Behavioral Patterns — Mock Interview Q&A

Focus on collaboration, ownership, transition rules, and failure behavior rather than only naming a pattern.

## 1. How would you choose between Strategy and State?

**Answer:** Strategy represents a choice among interchangeable algorithms, usually selected by a caller or configuration. State represents behavior that changes as an object's lifecycle changes, with transitions determining the active state. A pricing algorithm selected per request suggests Strategy; a media player whose valid operations depend on whether it is stopped or paused suggests State. Some systems use both, but each should have a distinct responsibility.

## 2. What are the most important Observer design decisions?

**Answer:** I would define subscription lifetime, notification timing, ordering, payload, and failure isolation. Synchronous observers can add latency and propagate failures into the publisher; asynchronous observers need queueing and delivery rules. In-process Observer does not provide durability. The publisher should not retain listeners indefinitely, so subscriptions need a clear cleanup mechanism.

## 3. What makes a Command more than a method call?

**Answer:** A Command represents a request as data or an object, allowing it to be queued, logged, scheduled, retried, or undone independently of the requester. That structure is useful only when one of those capabilities is needed. It also requires explicit execution, state, failure, and idempotency semantics. For immediate simple behavior, calling a method directly is clearer.

## 4. How do you design a Chain of Responsibility?

**Answer:** I define the handler contract, ordering, continuation signal, terminal behavior, and distinction between "not applicable" and failure. I then compose the chain in one visible place and test both normal progression and short-circuit paths. If every handler always runs, a pipeline may be a more accurate name; if exactly one handles, termination should be explicit.

## 5. When should State be implemented with classes instead of an enum?

**Answer:** I start with an explicit transition table and consider an enum plus transition function. State classes help when each state has meaningfully different behavior, valid operations, or side effects, and the logic is becoming hard to maintain in conditionals. I avoid creating classes for trivial states and ensure transitions cannot silently create invalid combinations.

## 6. Template Method versus Strategy?

**Answer:** Template Method uses inheritance: a base class fixes an algorithm's sequence and subclasses override selected steps. Strategy uses composition: a context delegates a varying algorithm to a replaceable collaborator. I prefer Strategy when behavior needs independent runtime selection or multiple variation dimensions. Template Method can fit when a stable sequence and subclass hooks are genuinely part of the type design.

## 7. How do you make an Iterator safe under collection changes?

**Answer:** There is no universal policy. I choose fail-fast, snapshot, or concurrent iteration based on consistency and performance requirements, document it, and test it. Fail-fast detects misuse but is not concurrency control; snapshots provide stable reads at a copying cost; concurrent iterators need precise guarantees and may not see every update.

## 8. How do you prevent a Mediator from becoming a God object?

**Answer:** I keep the mediator responsible for a cohesive interaction, such as routing chat messages, and leave participant policy and persistence to their owners. I monitor whether unrelated workflows are accumulating in it. If distinct collaborations have different lifetimes or rules, I split them into narrower coordinators or use events where appropriate.

## 9. When is Visitor a poor fit?

**Answer:** Visitor is a poor fit when the element type set changes frequently, because every visitor may need to add a method for each new type. It can also expose internal details and add dispatch complexity. It is strongest when types are stable and new operations are common; otherwise polymorphism or simpler functions can be easier to extend.

## 10. Command versus Memento for undo?

**Answer:** Command history records operations and can reverse them if each command can capture enough information to undo safely. Memento captures object state and can restore it, often with simpler restoration logic but higher memory costs. I consider operation reversibility, snapshot size, consistency, and external side effects. A command cannot reliably undo an irreversible external action just by reversing local state.

## 11. How would you handle an observer that fails?

**Answer:** The publisher contract should specify whether observer failures are isolated, aggregated, retried, or propagated. For independent subscribers, I would commonly record a failure and continue notifying others, but the correct rule depends on whether all listeners are required for the operation to succeed. I would not silently swallow exceptions or let accidental iteration order define the result.

## 12. Which behavioral pattern fits an extensible request pipeline?

**Answer:** Chain of Responsibility or middleware fits when each stage can inspect, transform, reject, or pass along a request. I would define ordering and short-circuit semantics and keep stages composable. If every stage must run in a fixed sequence, a pipeline abstraction may be clearer; if one policy varies, Strategy may be a separate concern.
