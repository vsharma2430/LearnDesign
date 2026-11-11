# OOP Fundamentals — Mock Interview Questions

This file contains mock interview-style questions for OOP fundamentals. Each question is written to test design reasoning, invariants, and tradeoffs. Each answer is meant to sound like a strong interview explanation without being too long or too vague.

## 1. What is the difference between a class and an object?

### Answer
A class is a blueprint: it defines the structure and behavior of a kind of thing. An object is a specific instance created from that blueprint. The class defines what exists; the object holds the actual state at runtime.

Example:

```text
class Lamp {
  private boolean on

  void turnOn() { on = true }
  void turnOff() { on = false }
  boolean isOn() { return on }
}

Lamp deskLamp = new Lamp()
Lamp bedroomLamp = new Lamp()
```

Here, `Lamp` is the class. `deskLamp` and `bedroomLamp` are objects. Two different objects can both be instances of the same class but have different current states.

The important interview point: a class is not the same as an object, and state is not the same as behavior.

## 2. What is encapsulation, and why is it important?

### Answer
Encapsulation means keeping state and the rules that govern it behind a boundary. The outside world should interact with the object through its public operations rather than directly modifying internal fields.

This matters because invariants become easier to preserve. If callers can mutate internal fields directly, they can violate the object's rules.

Example:

```text
class Account {
  private int balance

  void deposit(int amount) {
    if (amount <= 0) throw new IllegalArgumentException()
    balance += amount
  }
}
```

If `balance` were public, any caller could change it to any value, including a negative number. Encapsulation prevents invalid states and keeps the object honest.

## 3. What is an invariant? Give examples.

### Answer
An invariant is a condition that must always remain true for the object to be valid.

Examples:
- A bank account balance cannot be negative.
- A temperature is always measured in a known unit.
- An order cannot be shipped before it is paid.
- A student ID must be non-empty and unique.

Invariants are important because they define the rules of the domain. The object should enforce them at construction time and in all mutation methods.

Example:

```text
class Account {
  private long balance

  Account(long openingBalance) {
    if (openingBalance < 0) throw new IllegalArgumentException()
    balance = openingBalance
  }
}
```

This ensures the object cannot be created in an invalid state.

## 4. What is the difference between a value object and an entity?

### Answer
A value object is defined by its data. Two value objects with the same data are considered equal, even if they are separate instances.

An entity is defined by its identity. Two entities with the same data are still different if they have different IDs.

Examples:
- `Money(10, USD)` is a value object.
- `Account(accountNumber = A123)` is an entity.

```text
class Money {
  final int amount
  final String currency

  boolean equals(Money other) {
    return this.amount == other.amount && this.currency.equals(other.currency)
  }
}
```

```text
class Account {
  final String accountNumber
  int balance
}
```

The key interview answer: value objects are about value; entities are about identity.

## 5. Why is it dangerous to provide a public setter for a field that must preserve invariants?

### Answer
Because a setter bypasses the validation logic. A caller can assign any value directly and violate the rules that the object is supposed to enforce.

Example:

```text
class Account {
  public int balance
}
```

Now any caller can write:

```text
account.balance = -500
```

This violates the invariant that balance must be non-negative. The object stops protecting its own state. The correct pattern is to expose methods such as `deposit` and `withdraw` that validate before changing the internal state.

## 6. What is abstraction, and how is it different from encapsulation?

### Answer
Abstraction is the idea of exposing only the essential behavior and hiding unnecessary complexity. Encapsulation is the mechanism used to enforce that boundary.

A good abstraction tells the caller what an object does and what it guarantees, without exposing how it does it.

Example:

```text
interface Notifier {
  Result send(String to, String message)
}
```

The caller does not need to know whether the notifier uses email, SMS, or a queue. That is the abstract view. Encapsulation ensures the implementation details stay hidden.

In other words:
- Encapsulation is about protecting state.
- Abstraction is about focusing on essential behavior.

## 7. What is the difference between an interface and an abstract class?

### Answer
An interface defines a contract: a capability that different implementations can satisfy. An abstract class is a partially implemented class that can share state or behavior across related subclasses.

Use an interface when:
- there is a common capability but not necessarily a true inheritance relationship
- multiple unrelated types need the same behavior
- you want flexibility and loose coupling

Use an abstract class when:
- there is a meaningful shared base with common state or logic
- subclasses are genuinely related and can inherit common implementation

Example interface:

```text
interface Shape {
  double area()
}
```

Example abstract class:

```text
abstract class Vehicle {
  private String registrationNumber

  abstract void start()
  void stop() { ... }
}
```

The interview answer should emphasize that inheritance is not always the best tool; interfaces are often the simpler and more flexible choice.

## 8. When would you choose composition instead of inheritance?

### Answer
Choose composition when the relationship is not a true subtype relationship but a “has-a” or “uses-a” relationship.

Example:

```text
class ReportService {
  private final Notifier notifier

  ReportService(Notifier notifier) {
    this.notifier = notifier
  }

  void sendReport(String user, String message) {
    notifier.send(user, message)
  }
}
```

Here `ReportService` is not a kind of `Notifier`; it uses a notifier. This is composition. It makes it easy to swap in email, SMS, or push implementations without creating many subclasses.

Inheritance is better when there is a real subtype relationship and the subclass truly behaves like the parent under the parent contract.

## 9. What is polymorphism?

### Answer
Polymorphism means the same interface or contract can be implemented in different ways, and callers can depend on the abstraction rather than the specific implementation.

```text
interface Notifier {
  Result send(String recipient, String message)
}

class EmailNotifier implements Notifier { ... }
class SmsNotifier implements Notifier { ... }

void sendWelcome(Notifier notifier, String recipient) {
  notifier.send(recipient, "Welcome!")
}
```

The method `sendWelcome` works with either implementation. The actual behavior depends on the object passed in. This is a core OOP idea because it allows flexibility and testability.

## 10. How would you model a money value object?

### Answer
A money value should be immutable and should include both amount and currency. Equality should compare both fields. It should not silently convert currencies inside `equals` or `plus` because conversion requires external policy and rate information.

```text
class Money {
  final BigDecimal amount
  final String currency

  Money(BigDecimal amount, String currency) {
    if (amount == null) throw new IllegalArgumentException()
    if (currency == null || currency.isBlank()) throw new IllegalArgumentException()
    this.amount = amount
    this.currency = currency
  }

  Money plus(Money other) {
    if (!this.currency.equals(other.currency)) {
      throw new IllegalArgumentException("Currencies must match")
    }
    return new Money(this.amount.add(other.amount), this.currency)
  }
}
```

Important design points:
- use exact decimal arithmetic rather than binary float
- define how rounding works
- reject invalid currencies early
- avoid implicit cross-currency conversion

## 11. What is a resource ownership problem in OOP?

### Answer
It is when an object acquires a resource like a file handle, socket, or database connection and must ensure it is released at the right time. If not, the system can leak resources, exhaust file descriptors, or leave the system in an inconsistent state.

Example:

```text
class ConfigReader {
  private FileHandle handle

  void read() {
    try {
      // use handle
    } finally {
      handle.close()
    }
  }
}
```

The object that opens the file is the one responsible for closing it. Garbage collection is not reliable enough for resource cleanup because it is nondeterministic, and finalizers are not a safe substitute for explicit cleanup.

## 12. Why should constructors validate input?

### Answer
Because constructors establish the valid state of the object. If an object allows invalid state at creation, every later method has to defend against it. That spreads validation throughout the code and makes the class harder to trust.

Example:

```text
class User {
  private final String email

  User(String email) {
    if (email == null || !email.contains("@")) {
      throw new IllegalArgumentException("Invalid email")
    }
    this.email = email
  }
}
```

This keeps invalid objects from existing in the first place. It is better than hoping all callers remember to check input.

## 13. What is a state machine design, and why is it useful?

### Answer
A state machine is a model where an object can only move through valid states. Each state defines which operations are allowed.

Example:

```text
enum OrderState { DRAFT, PAID, SHIPPED, CANCELLED }

class Order {
  private OrderState state = DRAFT

  void pay() {
    if (state != DRAFT) throw new IllegalStateException()
    state = PAID
  }

  void ship() {
    if (state != PAID) throw new IllegalStateException()
    state = SHIPPED
  }
}
```

This prevents illegal flows such as shipping a draft order or paying twice. State machines are especially useful when the system has business rules and different allowed transitions at different times.

## 14. What is the difference between validation at the boundary and validation in the domain model?

### Answer
Validation at the boundary is about making sure incoming data is in a valid shape and format. Domain validation ensures the object actually respects its business rules.

Example:

```text
class WithdrawRequestDto {
  String accountId
  BigDecimal amount
}
```

The boundary should validate:
- required fields are present
- amount is numeric
- amount is positive
- currency is recognized

The domain object should validate:
- the account has sufficient funds
- the withdrawal is allowed for the account
- the currency matches the account type

A DTO alone is not enough protection. A malicious or buggy caller could construct a valid-shaped request that violates real domain rules.

## 15. How do you decide whether a model should be rich or anemic?

### Answer
A rich model is best when the system has business rules, invariants, and state transitions that matter. An anemic model is fine when the data is simple and there are no meaningful rules to enforce.

Examples:
- A ticketing system with seat reservation rules should be rich.
- A CSV upload tool that just displays rows can be simple data-centric.
- A read-only report that aggregates already-validated data usually does not need a rich domain model.

Risk of choosing the wrong model:
- Too rich for simple tasks causes overengineering.
- Too anemic for rule-heavy systems allows invalid states and bypassed logic.

## 16. What is the best answer to “Why use OOP at all?”

### Answer
OOP helps organize software around concepts that own state and behavior. It makes it easier to model real-world or domain concepts, protect invariants, and create modular and testable systems. When used well, it reduces accidental invalid states and makes code easier to reason about.

The most important principle is not “make everything a class.” The important principle is: model the right boundaries, protect invariants, and expose a meaningful abstraction.

## Short interview tips

- Start by identifying the invariant.
- Explain what owns the state.
- Clarify what is the identity and what is the value.
- Mention boundary validation vs domain validation.
- Discuss why the chosen abstraction matches the problem.
- Always ask: “Can a caller create invalid state?”

If the answer is yes, the design is usually not strong enough yet.

# Technical Interview With Follow-Up Questions

This section is designed to feel more like a live interview. Each question is followed by likely follow-up prompts the interviewer may ask. Use these to practice explaining your reasoning out loud.

## 1. Design a `ShoppingCart` for an e-commerce app.

### Expected answer
A `ShoppingCart` is an entity because it has identity and state. It owns multiple items and can change over time. It should protect invariants like:
- the cart must not contain duplicate products unless the business allows quantity updates
- total price must match the sum of line items
- item quantities must be positive

```text
class ShoppingCart {
  private List<CartItem> items

  addItem(Product product, int quantity) {
    if (quantity <= 0) throw IllegalArgumentException()
    // update existing item or add new one
  }

  removeItem(Product product) { ... }

  Money total() { ... }
}
```

### Follow-up questions
- What if the same product is added multiple times?
- Should the cart store `Money` or raw numbers?
- Should the total be computed lazily or stored?
- What happens if a product is discontinued or price changes?

### Strong answer
The cart should not expose a public list of items directly. If callers can modify the list, they can violate invariants and create hidden inconsistencies. The total should either be derived from line items or recomputed intentionally, not mutated ad hoc.

## 2. Why is `balance` a bad public field in an account class?

### Expected answer
Because it allows invalid states and bypasses validation. A caller can set it to a negative number, a wrong currency, or an impossible value. A class should expose operations like `deposit`, `withdraw`, and `balance()` instead of the raw field.

### Follow-up questions
- What if the account also has a `currency` field?
- Should the balance be mutable at all?
- What if multiple threads access the account at once?

### Strong answer
The account must protect invariants. If the balance is public, callers become responsible for business rules that belong inside the domain object. For thread safety, you may also need synchronization or atomic updates, depending on the system.

## 3. What is the difference between a constructor and a factory method?

### Expected answer
A constructor creates an object using the given inputs. A factory method is a named method that encapsulates creation logic, especially when the object is built in a more complex or conditional way.

```text
class User {
  static User createGuest() {
    return new User("guest", "guest@temp.invalid")
  }
}
```

### Follow-up questions
- When would a factory be better than a constructor?
- Can a factory return a subclass or a cached instance?
- Should the object be created only when valid state is guaranteed?

### Strong answer
A constructor is usually for simple direct creation; a factory is useful when there are multiple valid creation paths, validation rules, or object reuse logic. Either way, invalid objects should not exist.

## 4. Design an `Order` object with states: DRAFT, PAID, SHIPPED, CANCELLED.

### Expected answer
The `Order` should be a stateful entity with explicit transitions. It should reject invalid transitions and enforce invariants.

```text
enum State { DRAFT, PAID, SHIPPED, CANCELLED }

class Order {
  private State state = DRAFT

  void pay() {
    if (state != DRAFT) throw IllegalStateException()
    state = PAID
  }

  void ship() {
    if (state != PAID) throw IllegalStateException()
    state = SHIPPED
  }

  void cancel() {
    if (state == SHIPPED || state == CANCELLED) throw IllegalStateException()
    state = CANCELLED
  }
}
```

### Follow-up questions
- What if `pay()` is called twice?
- What if cancellation is allowed after payment but before shipping?
- Do you store state in the database as a string, enum, or separate table?
- How do you handle concurrency?

### Strong answer
The model should reject invalid transitions and make the state machine explicit. If stored in a database, you also need optimistic locking or transaction protection to handle concurrent updates correctly.

## 5. Why is `equals()` and `hashCode()` important for value objects?

### Expected answer
Value objects are defined by their data, so equality should compare the meaningful attributes. If two value objects represent the same value, they should be equal. `hashCode()` must be consistent with `equals()` so they behave correctly in sets and maps.

```text
class Money {
  final BigDecimal amount
  final String currency

  boolean equals(Object other) {
    return other instanceof Money m
      && this.amount.equals(m.amount)
      && this.currency.equals(m.currency)
  }

  int hashCode() {
    return Objects.hash(amount, currency)
  }
}
```

### Follow-up questions
- Should `Money(10, USD)` equal `Money(10.00, USD)`?
- Should the hash include only the amount or also the currency?
- What if a value object contains a floating-point field?

### Strong answer
Equality and hashing should be based on the value semantics, not object identity. If the currency is part of identity, then it must be included in equality and hashing.

## 6. How would you model a notification system with email and SMS?

### Expected answer
Use an abstraction such as `Notifier` and inject the implementation.

```text
interface Notifier {
  Result send(String to, String message)
}

class EmailNotifier implements Notifier { ... }
class SmsNotifier implements Notifier { ... }
```

The client depends on the abstraction, not on any specific implementation.

### Follow-up questions
- What if a provider times out?
- Should the contract say “accepted for delivery” or “delivered successfully”?
- What if a notification is retried?

### Strong answer
The contract should be honest. If the service only knows it was accepted by a provider, it should report that. It should not claim successful delivery unless the provider guarantees it. Failure must be represented explicitly rather than hidden.

## 7. Why is a DTO not enough to protect domain invariants?

### Expected answer
A DTO is just transport data. It validates input shape and format, but it does not own the real business state. The domain object must enforce business rules like non-negative balance or valid state transitions.

Example:

```text
class WithdrawRequestDto {
  String accountId
  BigDecimal amount
}
```

The DTO may validate that `amount` is positive, but the `Account` object must still reject an actual withdrawal when the balance is insufficient.

### Follow-up questions
- Where should validation happen then?
- Should the domain object trust the DTO at all?
- What is the boundary between controller and domain?

### Strong answer
Validation at the boundary protects the API. Validation in the domain protects the business. A holder of the domain object should never be able to create an invalid state merely because the client passed a seemingly valid request.

## 8. How would you handle file resources in a class?

### Expected answer
The class that opens the file should also close it. Use explicit cleanup with `try/finally` or a language-specific `using` pattern. Never rely on the garbage collector as the main cleanup mechanism.

```text
class ConfigReader {
  private FileHandle handle

  String read() {
    try {
      return handle.readAll()
    } finally {
      handle.close()
    }
  }
}
```

### Follow-up questions
- What if `readAll()` throws an exception?
- Should the close be in the method that reads or in a separate lifecycle method?
- Is `finalize()` or `close()` safer?

### Strong answer
The resource must close even on error. A `finally` block guarantees cleanup in failure cases. `finalize()` is not a reliable mechanism because it is nondeterministic and too late to be the primary cleanup strategy.

## 9. When should you use an interface instead of a base class?

### Expected answer
Use an interface when you need a capability contract without a true subtype relationship. Use a base class when the types share significant implementation or state.

Example:

```text
interface Shape {
  double area()
}
```

If all shapes share state like `color` or logic like `transform()`, an abstract class may make more sense.

### Follow-up questions
- What if the objects are not logically related?
- What if they share only one method and nothing else?
- Could a helper class also implement the interface?

### Strong answer
Interfaces model capability; inheritance models relationship. If there is no natural type hierarchy, an interface is usually the cleaner design.

## 10. How do you decide between a rich domain model and a simple data model?

### Expected answer
If the system has real business rules or invariants, use a rich domain model. If it is mostly data storage and presentation, a simple data-centric model may be enough.

Examples:
- ticket sales: rich model
- CSV import viewer: simple data model
- read-only aggregates: usually data-centric

### Follow-up questions
- What if the CSV importer later gets validation rules?
- What if the report starts enforcing policy checks?
- Can a simple model evolve into a rich model later?

### Strong answer
The right design depends on whether the behavior belongs to the object. If the state must enforce rules, then behavior should live near the state; otherwise, a simple model is preferable.

## 11. Design a `Temperature` value object.

### Expected answer
A temperature is a value object with immutable fields and explicit unit handling.

```text
class Temperature {
  final BigDecimal value
  final String unit

  Temperature(BigDecimal value, String unit) {
    if (value == null) throw IllegalArgumentException()
    if (!unit in ["C", "F", "K"]) throw IllegalArgumentException()
    this.value = value
    this.unit = unit
  }

  Temperature toCelsius() { ... }
}
```

### Follow-up questions
- Should `Temperature(20C)` equal `Temperature(68F)`?
- Should equality be unit-aware or perform auto-conversion?
- What about rounding and precision?

### Strong answer
Equality should be unit-aware unless conversion is explicit. The object should not silently compare values across units because that hides meaning and creates confusion.

## 12. What is the single most important thing to check in any OOP design?

### Expected answer
Whether the design prevents invalid states. If a caller can create invalid objects or mutate them into invalid states, the design is weak.

### Follow-up questions
- What are the invariants?
- Who owns the state?
- Where is validation enforced?
- Are there any public setters or publicly mutable collections?

### Strong answer
A strong OOP design makes valid state easy to create, invalid state difficult or impossible, and all important rules explicit at the right boundary.

## Interview practice prompt

When answering these questions, say the following out loud:

1. What is the invariant?
2. What is the identity of the object?
3. What should be private versus public?
4. Where is validation enforced?
5. Is the abstraction clear and honest?
6. What happens on invalid input or invalid state?
7. Can the caller accidentally create invalid state?

If you can answer those seven questions clearly, you are usually in a strong interview position.
