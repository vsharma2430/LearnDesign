# SOLID Principles — Mock Interview Q&A

This file is designed to sound like a practical engineering interview. The goal is not to memorize slogans but to explain trade-offs and choose the smallest boundary that removes real risk.

## 1. What is SRP in plain English?

### Answer

SRP says that a class should have one coherent reason to change. The idea is not that a class must have only one method or one field. It is that the responsibilities should be aligned around one axis of change.

A class that handles both database access and HTML rendering is a common violation. If the database table changes, the rendering logic should not need to change. If the report format changes, the storage layer should not be dragged into the same edit.

The strongest SRP answer in interviews is often: “I group behavior by why it changes.” That is the core idea.

## 2. How do you explain the difference between SRP and “one class, one method”?

### Answer

Those are different ideas. SRP is not a code-size rule. A class can have many methods and still be SRP-compliant if all methods support the same responsibility.

For example, a `UserService` may have methods to create, update, and delete users. Those are all part of the same responsibility: managing the user lifecycle. It is still valid if the reason to change is the user domain behavior itself.

The issue arises when the same class mixes unrelated responsibilities, such as user validation, database persistence, email notification, and audit logging, all of which change for different reasons.

## 3. What is OCP, and why is it often misunderstood?

### Answer

The Open/Closed Principle says a system should be open for extension but closed for modification. In practice, we want to add new behavior without changing the stable code path that already works.

The misunderstanding is that “closed for modification” means never editing existing code. It does not. It means we should isolate the extension point and keep the stable logic stable.

A classic example is a shipping calculator with a `switch` statement. If we add a new shipping type by editing the switch, we are forcing the same core logic to change. A better design is to plug in a strategy or registry so the new type is added in one place, without changing the central decision logic.

The right question is: “Where is genuine variation, and what seam will absorb it?”

## 4. When is a `switch` still the correct design?

### Answer

A `switch` is not automatically wrong. It is fine when the variation is small, stable, and not likely to expand frequently.

For example, if a system has exactly two payment modes and no new modes are expected, a direct conditional is simpler than introducing a strategy pattern. Over-engineering is a real design cost.

A switch becomes a problem when it grows into a knowledge-heavy decision tree with many variations and frequent edits. At that point, the design is no longer simple; it is just a central place for lots of change.

A strong answer is: “I prefer the smallest seam that supports the real expected variation.”

## 5. Why does LSP matter so much?

### Answer

LSP matters because it tells us whether a subtype can safely substitute for its base type without surprising the caller. If a subclass violates the base contract, the design becomes brittle.

For example, if `MutablePlaylist` promises that `add` inserts a song, but a `ReadOnlyPlaylist` subtype throws, then the subtype is not substitutable. The caller expects the parent contract and gets a behavior that violates the promise.

This is not just theoretical. It breaks assumptions in validation, testing, and program flow. A system that depends on substitutability becomes fragile if subclasses silently weaken guarantees or strengthen preconditions.

A good engineering rule is: a subtype should not make life harder for any client using the base abstraction.

## 6. How would you explain ISP to a junior developer?

### Answer

ISP says clients should not depend on methods they do not use. If you force a class to implement methods that do not make sense for it, the interface is too broad.

Imagine a `Worker` interface with `print`, `scan`, and `fax`. A basic printer cannot meaningfully implement `scan` and `fax`, but it is still forced to do so. That is a bad contract because it leaks irrelevant behavior.

The better design is to split the contract into smaller interfaces, each representing a capability. Then a device implements only the interfaces it actually supports.

This reduces accidental coupling and makes the code easier to understand and test.

## 7. What is the DIP, and how is it different from “program to an interface”?

### Answer

DIP says high-level policy should not depend on low-level implementation details. Both should depend on abstractions that represent the needed capabilities.

The difference from simply “add an interface” is that the abstraction has to be meaningful. If a business service depends on a generic `DatabaseClient` interface that exposes every possible operation, that is often just a leaky abstraction.

A better abstraction is a narrow port that models what the policy actually needs, for example `UserStore` or `PaymentGateway`.

This gives us two benefits:

- the policy becomes easier to test with fake or in-memory implementations
- the infrastructure can change without rewriting the business logic

## 8. Why are SOLID principles not just academic rules?

### Answer

They are practical tools for reducing change cost and accidental complexity. They help you ask the right questions before designing code.

For example:

- SRP asks: “What is the reason this code changes?”
- OCP asks: “How can I add new behavior without rewriting the central path?”
- LSP asks: “Will this subclass still behave like the contract I promised?”
- ISP asks: “Am I forcing unrelated clients to depend on irrelevant methods?”
- DIP asks: “Am I depending on policy or on infrastructure?”

In interviews, senior candidates show they understand these questions as design trade-offs, not slogans. They explain why a boundary helps and when that boundary becomes over-engineering.

## 9. Can you give a concise interview summary of SOLID?

### Answer

A good short summary is:

- SRP: one reason to change
- OCP: extend without rewriting the stable path
- LSP: subtype must honor the base contract
- ISP: no forced irrelevant methods
- DIP: policy depends on abstractions, not concrete infrastructure

The real design mindset is: keep the system easy to reason about, easy to change, and honest about its contracts. SOLID helps with that, but only when applied to real change pressure rather than as a checklist of dogmas.

## 10. How do you defend a design in an interview when someone challenges a SOLID abstraction?

### Answer

I explain the problem in terms of maintenance cost and actual change pressure.

For example:

- “I split this because the database access is a different axis of change from the reporting format.”
- “I used a strategy here because there are multiple shipping modes and the core logic should not be rewritten each time.”
- “I did not introduce a generic abstraction because there is only one real implementation and no proven variation yet.”

Strong design answers always tie the abstraction to a real cost reduction. If the principle adds more complexity than it removes, that is a signal to keep the design simpler.
