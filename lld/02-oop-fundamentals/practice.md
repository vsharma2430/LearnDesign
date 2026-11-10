# OOP Fundamentals — Practice

Work through the exercises in order. For each design, write down the invariant or contract before choosing classes. Use pseudocode or your preferred language. The [lessons](lessons.md) explain the concepts and include worked examples; this page focuses on applying them.

## 1. Model a money value

Design `Money(amount, currency)` for a shopping cart.

- Decide which data is immutable and what makes two values equal.
- Define what `plus` does when currencies match and when they do not.
- List at least two edge cases to consider (for example, fractional units, overflow, or currency representation).
- Explain why conversion should not happen silently.

**Deliverable:** a small class/record sketch and 2–3 example assertions.

## 2. Protect an account's invariants

Model a bank account with an ID and a balance.

- Identify which property gives the account its identity.
- State at least two invariants.
- Define `deposit` and `withdraw`, including invalid-input behavior.
- Explain why a public balance setter allows callers to bypass the rules.
- Decide whether the account is an entity or a value object, and why.

**Deliverable:** constructor and operation signatures, invariants, and one valid and one rejected example for each operation.

## 3. Design a notification contract

Sketch a `Notifier` capability with email and SMS implementations.

- Choose a method signature and state what the caller can rely on.
- Identify one behavior that belongs in the contract and two details that belong in an implementation.
- Show a service that uses a `Notifier` without knowing which implementation it received.
- Explain how you would report delivery failure rather than pretending delivery succeeded.

**Deliverable:** interface, two implementation sketches, and one client method.

## 4. Choose an abstraction for shapes

Suppose a program only needs to calculate area for circles and rectangles.

- Would `Shape` be an interface, an abstract class, or neither? Explain the simplest fit.
- What new requirement might justify shared state or implementation in an abstract class?
- What requirement might justify an interface instead?
- Give an example of a shape subtype that would be a poor substitute for the proposed contract, if any.

**Deliverable:** a brief decision and a small type sketch.

## 5. Own a resource safely

Design a class that reads from a file and is responsible for closing it.

- Who owns the file handle?
- How does a caller know when the resource is open and closed?
- What happens if reading throws an error?
- Explain why relying on a finalizer or garbage collector is insufficient.

**Deliverable:** a usage example with guaranteed cleanup and a short lifecycle description.

## 6. Define order state transitions

An order can be `DRAFT`, `PAID`, `SHIPPED`, or `CANCELLED`.

- Write a transition table: which transitions are allowed and which are rejected?
- Implement or describe `pay`, `ship`, and `cancel`.
- Decide how repeated calls behave (for example, a second `pay`).
- Explain what needs consideration if the state is stored in a database and future software may add states.

**Deliverable:** transition table and operation pseudocode.

## 7. Separate the domain model from a DTO

An API accepts a withdrawal request and returns an account summary.

- Sketch a request DTO and response DTO.
- Identify which validation belongs at the request boundary and which invariant belongs in the account domain object.
- Explain why the DTO should not be the only protection against overdrafts.
- Describe the mapping between the domain object and the response.

**Deliverable:** DTO sketches, one domain operation, and a short explanation of responsibilities.

## 8. Rich or anemic?

For each system below, decide whether a simple data-centric model or domain behavior near state is a better fit. Explain the tradeoff.

1. An internal tool that imports CSV rows and displays them without business rules.
2. A ticketing system where a seat must not be sold twice and cancellation has policy rules.
3. A read-only report that aggregates already-validated records.

**Deliverable:** a decision for each, plus one risk of choosing the other approach.

## 9. Composition or inheritance?

A report service can send notifications through email or SMS. A developer proposes `EmailReportService` and `SmsReportService`, both inheriting from `ReportService`.

- Is the relationship an “is-a” relationship?
- Sketch the composition-based alternative.
- Explain how the alternative makes adding a third delivery mechanism easier (or identify a tradeoff).

**Deliverable:** before-and-after class sketches.

## Review checklist

Before considering your designs complete, check:

- Are all important invariants explicit and enforced at the right boundary?
- Can a caller accidentally create or leave an invalid object?
- Are equality and identity appropriate for the kind of object?
- Does every abstraction have a purpose, such as a contract or variation point?
- Can every subtype honor the contract of its base type?
- Are resource ownership and cleanup unambiguous?
- Did you keep simple data simple rather than adding classes without a need?
