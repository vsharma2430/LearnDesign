# OOP Fundamentals — Practice

These questions are intentionally different from the lesson examples. For each question, write the invariant or contract first, then choose the model that best fits. After each question, include a full answer with reasoning and example pseudocode.

## 1. Model a temperature value

Design a `Temperature(value, unit)` value object for a climate-control system.

- What is the object identity of a temperature value?
- Which fields should be immutable?
- What should `toCelsius` or `equals` do?
- What are at least two edge cases you must guard against?
- Why should unit conversion not happen implicitly inside equality?

**Deliverable:** a value-object sketch and 3 example assertions.

### Answer

A temperature is a value object, not an entity. Two temperatures with the same numeric value and unit represent the same concept, even if they are created at different times or in different code paths. So the identity is not a database ID or object pointer; it is the combination of `value` and `unit`.

The important invariant is that a temperature must always represent a valid numeric amount in a known unit. The object should be immutable: once created, the value and unit do not change. If the system needs a different unit, it should create a new object, not mutate the existing one.

```text
class Temperature {
  readonly value: Decimal
  readonly unit: Unit

  constructor(value: Decimal, unit: Unit) {
    require(value is finite)
    require(unit in {C, F, K})
    this.value = value
    this.unit = unit
  }

  equals(other: Temperature): boolean {
    return this.unit == other.unit
      && this.value == other.value
  }

  toCelsius(): Temperature {
    if (this.unit == C) return this
    if (this.unit == F) return new Temperature((this.value - 32) * 5 / 9, C)
    if (this.unit == K) return new Temperature(this.value - 273.15, C)
  }
}
```

This design makes equality straightforward: `Temperature(20, C)` is equal only to `Temperature(20, C)`, not to `Temperature(68, F)`, even though they represent the same physical temperature. The conversion must be explicit because equality and arithmetic are unit-sensitive. Implicit conversion would hide required decisions around precision and interpretation.

Edge cases to consider:
- Absolute zero and negative temperatures in Celsius/Kelvin.
- Floating-point precision in calculations like `(F - 32) * 5 / 9`.
- Unit normalization: `C`, `c`, or `°C` should be normalized to a canonical representation.
- Overflow or extremely large values in climate simulation code.

Example assertions:

```text
assert Temperature(20, C) == Temperature(20, C)
assert Temperature(68, F).toCelsius() == Temperature(20, C)
assert Temperature(20, C) != Temperature(68, F)
```

The key idea is that conversions are explicit operations, while equality is precise and unit-aware. That keeps the model honest and prevents accidental mixing of incompatible measurements.

## 2. Protect a customer account's invariants

Model a `CustomerAccount` with an email, account number, and balance.

- Which property identifies the account?
- State at least three invariants that must always hold.
- What should `deposit` and `withdraw` do when the amount is invalid or the balance is insufficient?
- Why is a public `balance` field dangerous?
- Is this object an entity or a value object? Explain.

**Deliverable:** constructor, invariant list, and valid/rejected examples for each operation.

### Answer

The identity of the account is the account number, not the balance and not the email. Two different accounts could both have the same balance, and a user could change their email without changing the account itself. The account number provides stable identity over time and represents the same business entity across updates.

At least these invariants should hold:
- `accountNumber` must be non-empty and unique.
- `email` must be syntactically valid.
- `balance` must never be negative.
- `withdraw` may only proceed when the amount is positive and `amount <= balance`.
- `deposit` may only proceed when the amount is positive.

The object should hide its state behind operations rather than exposing raw fields.

```text
class CustomerAccount {
  readonly accountNumber: String
  readonly email: String
  private balance: Money

  constructor(accountNumber: String, email: String, openingBalance: Money) {
    require(accountNumber is not blank)
    require(email is valid)
    require(openingBalance >= 0)
    this.accountNumber = accountNumber
    this.email = email
    this.balance = openingBalance
  }

  deposit(amount: Money): void {
    if (amount <= 0) throw InvalidAmount
    if (amount.currency != this.balance.currency) throw InvalidCurrency
    this.balance = this.balance + amount
  }

  withdraw(amount: Money): void {
    if (amount <= 0) throw InvalidAmount
    if (amount.currency != this.balance.currency) throw InvalidCurrency
    if (this.balance < amount) throw InsufficientFunds
    this.balance = this.balance - amount
  }
}
```

A public `balance` field is dangerous because any caller can set it to any arbitrary value and bypass the rules. That includes negative balances or impossible values. Once state is directly mutable, the class no longer protects its own invariants. The entire purpose of encapsulation is to prevent this.

This is an entity, not a value object. A value object is usually defined by its data and can be replaced without affecting identity. An account, however, is a stable business object whose identity persists while its state changes over time. The same user may deposit or withdraw money, but the account remains the same entity.

Valid and rejected examples:

- Valid: `deposit(Money(100, USD))` from a `USD` account.
- Rejected: `deposit(Money(-5, USD))` because amount must be positive.
- Valid: `withdraw(Money(20, USD))` if current balance is at least 20.
- Rejected: `withdraw(Money(10000, USD))` when balance is lower.

This is a good example of how object design should ensure that invalid transitions are impossible at the boundary of the object itself.

## 3. Design a logging contract

Sketch a `Logger` capability with file and database implementations.

- Choose a method signature and state what the caller can rely on.
- Which behaviors belong in the contract, and which belong in implementations?
- Show a service that depends on `Logger` without knowing what it receives.
- Explain how failures should be reported without pretending the write succeeded.

**Deliverable:** interface, two implementation sketches, and one client method.

### Answer

A clean contract is:

```text
interface Logger {
  log(event: LogEntry): Result<Void>
}
```

The caller can rely on a single contract: if the result is success, the event was accepted according to the logger abstraction; if it is a failure, the caller receives an explicit reason. The caller should not have to know whether the logger writes to a file, a database, Kafka, or an external platform.

What belongs in the contract:
- The ability to log an event.
- The result type or error behavior.
- The promise that the logger handles the message in a consistent way.

What belongs in an implementation:
- Opening a file handle.
- Creating a database connection.
- Retry policy, buffering, and flush timing.
- Serialization format and storage details.

Example implementations:

```text
class FileLogger implements Logger {
  log(entry: LogEntry): Result<Void> {
    try {
      appendToFile(entry.toJson())
      return Ok()
    } catch (e) {
      return Err("file write failed: " + e)
    }
  }
}

class DatabaseLogger implements Logger {
  log(entry: LogEntry): Result<Void> {
    try {
      insertIntoDb(entry)
      return Ok()
    } catch (e) {
      return Err("db insert failed: " + e)
    }
  }
}
```

Client code:

```text
class OrderService {
  constructor(private logger: Logger) {}

  placeOrder(order: Order): void {
    // do the order work
    result = this.logger.log(LogEntry("order placed", order.id))
    if (result.isError()) {
      recordAuditFailure(result.error)
    }
  }
}
```

This is polymorphism: the service depends on the abstraction, not a concrete implementation. That makes testing easy because the service can receive a fake logger in unit tests.

Failure reporting should not pretend the log succeeded. If file storage fails, the logger should report a failure result or exception. In high-reliability systems, it may also emit a separate “log delivery failed” event, but callers should never infer success from lack of an exception if the contract does not guarantee it.

## 4. Choose a common abstraction for document types

Suppose a program needs to support PDF, Word, and text documents, but for now it only needs a `preview()` operation.

- Would `Document` be an interface, an abstract class, or neither? Explain the simplest fit.
- What new requirement might justify a shared abstract base instead?
- What requirement might justify an interface even when the types are related?
- Give one example of a type that would be a poor substitute for the contract.

**Deliverable:** a brief decision and a small type sketch.

### Answer

The simplest fit is an interface:

```text
interface Document {
  preview(): String
}
```

The program only cares that each document can produce a preview. There is no need for a common implementation or shared state. If different document types require only a common ability, an interface is the least complicated arrangement.

An abstract class may become appropriate if every document type shares substantial logic, such as metadata handling, caching, or validation. For example, all documents might have a `title`, `owner`, `lastModified`, and a method for `renderHeader()`. In that case an abstract base can centralize behavior while each subclass provides the content-specific preview logic.

An interface may still be the better fit if the types are not a true inheritance hierarchy, or if the capability must be available to unrelated types. For example, a `SearchResultCard` or a UI preview component might implement `preview()` without being a document in the domain. In such a case, an interface describes a capability rather than a family relationship.

A poor substitute would be a `PdfPreviewService` passed where a `Document` is expected. It would not naturally satisfy the contract because it is not itself a document; it is a helper that knows how to render one. That kind of fake substitution creates confusion and breaks the semantics of the abstraction.

The design principle is simple: use inheritance when there is a true subtype relationship, and use an interface when you need a contract for capability sharing.

## 5. Own a file resource safely

Design a class that opens a configuration file, reads it, and is responsible for closing it.

- Who owns the file handle?
- How does the caller know whether the resource is open or closed?
- What happens if the read raises an exception?
- Why is garbage collection not a reliable cleanup mechanism?

**Deliverable:** a usage example with guaranteed cleanup and a short lifecycle description.

### Answer

The `ConfigReader` object owns the file handle. The caller does not own the OS resource directly; they interact with the object, and the object is responsible for opening and closing the file. This is the central ownership rule: the object that acquired the resource must release it.

The caller can tell the resource is open or closed by looking at the object state or by using a construct that guarantees cleanup automatically, such as a `try/finally` block or `using` block. The class may expose `isOpen()` or may simply define that a file is held only while a read is being performed.

```text
class ConfigReader {
  private handle: FileHandle | null = null

  open(path: String): void {
    this.handle = openFile(path)
  }

  read(): String {
    if (this.handle == null) throw IllegalState
    try {
      return this.handle.readAll()
    } finally {
      this.close()
    }
  }

  close(): void {
    if (this.handle != null) {
      this.handle.close()
      this.handle = null
    }
  }
}
```

Usage example:

```text
reader = new ConfigReader()
reader.open("app.config")
try {
  content = reader.read()
} finally {
  reader.close()
}
```

If reading throws an exception, `finally` still runs and closes the file. Without that guarantee, a failed read could leave a file descriptor hanging open until much later, which wastes resources and may eventually cause the process to fail.

Garbage collection is not sufficient because resource cleanup is time-dependent, not just memory-dependent. The timing of finalizers is not guaranteed, and different languages handle memory management differently. A resource should be closed as soon as the owner is done with it, not “whenever the runtime feels like collecting.” This is why explicit cleanup and lifecycle management are essential.

## 6. Define a ride state machine

A ride can be `REQUESTED`, `CONFIRMED`, `IN_PROGRESS`, `COMPLETED`, or `CANCELLED`.

- Write a transition table showing allowed and rejected transitions.
- Implement or describe `confirm`, `start`, `complete`, and `cancel`.
- How should repeated calls behave?
- What must be considered if the state is stored in a database and future states are added?

**Deliverable:** state table and operation pseudocode.

### Answer

A robust state machine says which operations are legal for each state. Here is a compact transition table:

| Current state | confirm | start | complete | cancel |
|---|---|---|---|---|
| REQUESTED | allowed → CONFIRMED | rejected | rejected | allowed → CANCELLED |
| CONFIRMED | rejected | allowed → IN_PROGRESS | rejected | allowed → CANCELLED |
| IN_PROGRESS | rejected | rejected | allowed → COMPLETED | rejected |
| COMPLETED | rejected | rejected | rejected | rejected |
| CANCELLED | rejected | rejected | rejected | rejected |

This means the ride moves forward in a valid order. Once a ride is complete or cancelled, it should not be mutated again.

Pseudocode:

```text
enum RideState { REQUESTED, CONFIRMED, IN_PROGRESS, COMPLETED, CANCELLED }

class Ride {
  private state: RideState = REQUESTED

  confirm(): void {
    if (state != REQUESTED) throw InvalidState
    state = CONFIRMED
  }

  start(): void {
    if (state != CONFIRMED) throw InvalidState
    state = IN_PROGRESS
  }

  complete(): void {
    if (state != IN_PROGRESS) throw InvalidState
    state = COMPLETED
  }

  cancel(): void {
    if (state == COMPLETED || state == CANCELLED) throw InvalidState
    state = CANCELLED
  }
}
```

Repeated calls must not silently “redo” the same transition. If a second `confirm()` is attempted on an already confirmed ride, the code should reject it with a state error. Whether that error is thrown or returned as a result depends on the app style, but the behavior should be explicit and predictable.

If the state is stored in a database, there are additional concerns:
- A race condition: two requests might simultaneously try to start the same ride.
- Transaction boundaries: state changes must be atomic with payment/status updates.
- Future extensibility: a hardcoded enum can become brittle as new states like `PAUSED` or `DISPUTED` are added.
- Auditing: you may want a history of state changes with timestamps and reasons.

This is exactly why domain state machines are useful: they make invalid transitions impossible in the model and easy to reason about in code.

## 7. Separate a request DTO from a domain model

An API accepts a `withdraw` request and returns an account summary. Design the boundary carefully.

- Sketch the request DTO and response DTO.
- Which validation belongs at the boundary and which belongs in the domain model?
- Why is the DTO not enough to prevent overdrafts?
- Describe how the domain object maps to the response object.

**Deliverable:** DTO sketches, one domain operation, and a short design explanation.

### Answer

The request DTO should be a plain data container for transport and validation. It should not contain business logic. Example:

```text
class WithdrawRequestDto {
  accountId: String
  amount: Decimal
  currency: String
}

class AccountSummaryDto {
  accountId: String
  balance: Decimal
  currency: String
  status: String
}
```

The request boundary is responsible for validating shape and syntax, such as:
- required fields are present
- `amount` is numeric
- `currency` is supported
- `accountId` is well-formed
- `amount` is positive

The domain model is responsible for actual business invariants, such as:
- account balance can never go negative
- withdrawal must be allowed for the given currency
- only valid operations on this account are permitted

```text
class BankAccount {
  readonly id: String
  private balance: Money

  withdraw(amount: Money): void {
    if (amount <= 0) throw InvalidAmount
    if (amount.currency != this.balance.currency) throw InvalidCurrency
    if (this.balance < amount) throw InsufficientFunds
    this.balance = this.balance - amount
  }
}
```

The DTO is not enough to prevent an overdraft because it is only input data. A malicious client or a bug in the controller could pass a valid-looking request that contradicts the domain rules. The real invariant must live in the domain model, because that is the component that owns account state and business rules.

The mapping between the domain object and the response is explicit:

```text
function toSummary(account: BankAccount): AccountSummaryDto {
  return new AccountSummaryDto(
    account.id,
    account.balance.amount,
    account.balance.currency,
    "active"
  )
}
```

This separation keeps responsibilities clean: DTOs represent data crossing boundaries, and the domain object owns behavior and invariants. A controller can validate a request and then ask the domain object to perform the actual withdrawal.

## 8. Rich or anemic?

Decide whether each of the following systems is better modeled as a simple data-centric object or a rich domain model close to the state.

1. A spreadsheet import tool that loads rows and displays them in a table.
2. A concert ticketing system where a seat must not be sold twice and cancellation has policy rules.
3. A reporting service that reads already-validated records and aggregates them for display.

For each item, explain the tradeoff and one risk of choosing the other approach.

**Deliverable:** a decision for each and a tradeoff explanation.

### Answer

1. Spreadsheet import tool: simple data-centric model is better.

Reason: if the tool’s job is to accept CSV rows, show them, and maybe let the user inspect them, there may be no meaningful business logic to enforce. A plain object with fields is enough. It avoids creating a complex domain model around a low-risk process.

Tradeoff: the model stays easy to understand and fast to build. The downside is that if the system later grows business rules, the data model may become too weak and a richer domain layer may need to be introduced.

Risk of the rich model: over-engineering. You may build classes with rules that nobody actually needs, creating more code, more maintenance, and more confusion for a simple display function.

2. Concert ticketing system: rich domain model is better.

Reason: there are real invariants: a seat cannot be sold twice, cancellation policy may depend on timing, refunds may have rules, and sold seats are stateful. These are domain behaviors, not just passive fields.

Tradeoff: the model is more complex and requires careful validation, transition rules, and possibly concurrency control. But this complexity is justified because the business rules matter.

Risk of the anemic model: if you just store `seatId`, `status`, and `customerId` without behavior, any caller can bypass logic and accidentally sell the same seat twice. That is exactly the type of bug rich domain logic is designed to prevent.

3. Reporting service: simple data-centric model is usually better.

Reason: the data is already validated upstream, and the task is to aggregate or summarize it for display. There may be no transactionally crucial business rules in the report component itself.

Tradeoff: this is simpler and easier to test. The report remains a presentation/aggregation layer, not a business policy engine.

Risk of the rich model: you may duplicate logic or add unnecessary abstractions. The result is a system more complicated than the problem requires, with extra layers to maintain and more coupling to domain concepts that do not actually belong in the report.

The rule is: keep simple data simple, but give behavior to the model when the behavior enforces rules, protects invariants, or encodes policy.

## 9. Composition or inheritance?

A newsletter service can send messages via email, SMS, or push notification. A developer proposes `EmailNewsletterService` and `SmsNewsletterService`, both inheriting from `NewsletterService`.

- Is the relationship truly an “is-a” relationship?
- Sketch the composition-based alternative.
- Why does this make a third delivery mechanism easier to add?
- What tradeoff should you be aware of?

**Deliverable:** before-and-after class sketches.

### Answer

No, this is not a natural “is-a” relationship. An `EmailNewsletterService` is not “a kind of `NewsletterService`” in a way that requires inheritance to model the real variation. The real variation is not the newsletter service itself; it is the mechanism used to deliver the message. That is a dependency, not a type hierarchy.

Inheritance-based sketch:

```text
class NewsletterService {
  send(to: User, message: String): void {
    // default behavior
  }
}

class EmailNewsletterService extends NewsletterService {
  send(to: User, message: String): void {
    emailProvider.send(to.email, message)
  }
}

class SmsNewsletterService extends NewsletterService {
  send(to: User, message: String): void {
    smsProvider.send(to.phone, message)
  }
}
```

This design creates subclasses for each delivery mechanism. It works, but it is rigid and inflexible. Every new delivery mechanism requires a new subclass, and the service is harder to configure at runtime.

Composition-based sketch:

```text
interface Messenger {
  send(to: Contact, message: String): Result<Void>
}

class NewsletterService {
  constructor(private messenger: Messenger) {}

  send(to: User, message: String): void {
    this.messenger.send(to.contact, message)
  }
}

class EmailMessenger implements Messenger {
  send(...) { ... }
}

class SmsMessenger implements Messenger {
  send(...) { ... }
}

class PushMessenger implements Messenger {
  send(...) { ... }
}
```

This makes adding a third mechanism straightforward: write another `Messenger` implementation and inject it. The newsletter service remains the same; only the implementation of the dependency changes.

The tradeoff is indirection. The composition-based design adds one more object boundary, so the code is slightly less immediate than a subclass hack. But the benefit is flexibility, testability, and lower coupling. It is easier to swap implementations, fake them in tests, and add new delivery strategies without rewriting the core abstraction.

This is a classic example of using composition to model “has a” behavior rather than relying on inheritance to force a questionable subtype relationship.

## Review checklist

Before you consider your design complete, check:

- Are the invariants explicit and are they enforced at the right boundary?
- Can callers construct invalid objects accidentally?
- Is the identity of each object appropriate for its role (entity vs value object)?
- Does every abstraction have a clear purpose?
- Can every subtype truly honor the contract of its parent?
- Are resources owned and cleaned up deterministically?
- Are state transitions explicit and validated?
- Did you avoid adding classes when a simple value or function would suffice?
