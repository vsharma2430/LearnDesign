# OOP Fundamentals — Lessons

Object-oriented programming (OOP) is a way to organize a program around concepts that own state and provide behavior. The aim is not to turn every noun into a class. A good model makes valid operations easy to find, prevents invalid states, and gives callers a small, dependable contract.

Examples use Java-like pseudocode. The design ideas apply in other languages, although syntax and features (such as access modifiers, destructors, and interfaces) differ.

## Learning path

By the end, you should be able to:

1. Distinguish a class, an object, its state, behavior, and identity.
2. Use encapsulation and abstraction to protect invariants and simplify callers.
3. Use interfaces and polymorphism to vary implementations; use inheritance only for a genuine substitutable relationship.
4. Construct valid objects and manage resources through their full lifecycle.
5. Choose between mutable and immutable objects, entities and value objects, and data-transfer and domain models.
6. Recognize when a rich domain model helps—and when a simple data structure is the better choice.

Suggested order: read each lesson, trace its example, then complete the matching practice exercise in [practice.md](practice.md).

## 1. Classes, objects, state, and behavior

A **class** is a definition: it describes the data and operations available for a kind of thing. An **object** is one particular instance of that definition. An object's **state** is the information it currently holds; its **behavior** is what it can do.

```java
class Lamp {
    private boolean on;

    void turnOn()  { on = true; }
    void turnOff() { on = false; }
    boolean isOn() { return on; }
}

Lamp deskLamp = new Lamp(); // one object
deskLamp.turnOn();          // behavior changes its state
```

`Lamp` is the class; `deskLamp` refers to an object. Another `Lamp` can be off at the same time. In languages that distinguish them, an object also has **identity**: two objects can contain equal data but still be different objects.

### Access and contracts

An object's **public interface** is the set of operations callers are allowed to depend on. `private` members are implementation details; other languages offer equivalents such as module visibility or properties. Restricting access is not secrecy for its own sake—it prevents callers from putting the object into a state its operations cannot handle.

```java
class Thermostat {
    private int targetCelsius;

    Thermostat(int initialTarget) {
        setTarget(initialTarget);
    }

    void setTarget(int celsius) {
        if (celsius < 5 || celsius > 35) {
            throw new IllegalArgumentException("Target must be 5–35°C");
        }
        targetCelsius = celsius;
    }

    int target() { return targetCelsius; }
}
```

If `targetCelsius` were public, any caller could set it to `500`. Keeping it private and checking in one operation protects the invariant: the target is always between 5 and 35. A constructor should use the same validation as later updates so invalid initial state is not possible either.

## 2. Encapsulation and abstraction

**Encapsulation** puts state and the rules that govern it behind a boundary. **Abstraction** is the useful, simplified view that boundary offers: callers need to know what an operation guarantees, not how it achieves the result.

Consider a bank account. A public `balance` field plus arbitrary setters exposes representation and makes the caller responsible for enforcing business rules. An operation-based API is safer:

```java
class Account {
    private final String id;
    private long balanceCents;

    Account(String id, long openingBalanceCents) {
        if (id == null || id.isBlank()) throw new IllegalArgumentException("id required");
        if (openingBalanceCents < 0) throw new IllegalArgumentException("negative opening balance");
        this.id = id;
        this.balanceCents = openingBalanceCents;
    }

    void deposit(long cents) {
        if (cents <= 0) throw new IllegalArgumentException("deposit must be positive");
        balanceCents += cents;
    }

    void withdraw(long cents) {
        if (cents <= 0 || cents > balanceCents) {
            throw new IllegalArgumentException("invalid withdrawal");
        }
        balanceCents -= cents;
    }

    long balanceCents() { return balanceCents; }
    String id() { return id; }
}
```

The public abstraction is “deposit or withdraw an amount”; callers do not directly manipulate the balance. The object protects the invariant that its balance cannot become negative. In real systems, money also needs currency, overflow handling, concurrency guarantees, and persistence; this small example is about object boundaries, not a production banking implementation.

**Rule of thumb:** expose actions and queries that express the concept. Avoid public setters by default; add a mutation only when that change is a valid domain operation.

## 3. Interfaces, inheritance, and polymorphism

An **interface** describes a capability or contract without requiring a particular representation. An **implementation** supplies the details. This lets a caller work with the contract rather than a specific class.

```java
interface Notifier {
    void send(String recipient, String message);
}

class EmailNotifier implements Notifier {
    public void send(String recipient, String message) {
        // deliver through an email provider
    }
}

class SmsNotifier implements Notifier {
    public void send(String recipient, String message) {
        // deliver through an SMS provider
    }
}

void sendWelcome(Notifier notifier, String address) {
    notifier.send(address, "Welcome!");
}
```

`sendWelcome` works with either implementation. This is **polymorphism**: the same contract can have different implementations, and the behavior used depends on the object supplied. The contract should specify meaningful guarantees (for example, what a successful return means and how delivery failures are reported). Provider credentials and HTTP request details are implementation details.

**Inheritance** creates a subtype relationship: a subclass inherits or specializes a base class. Use it when instances of the subtype can safely be used anywhere the base type is expected. This is the **substitution** test.

```java
abstract class Shape {
    abstract double area();
}

class Circle extends Shape {
    private final double radius;
    Circle(double radius) {
        if (radius < 0) throw new IllegalArgumentException("negative radius");
        this.radius = radius;
    }
    double area() { return Math.PI * radius * radius; }
}
```

`Circle` can stand in for `Shape` wherever only `area()` is needed. A shared abstract base class is useful if related types have cohesive shared behavior or state. If unrelated types merely need the same capability, an interface is usually more flexible. If there is no shared contract or behavior, neither inheritance nor an interface may be needed.

Inheritance can become brittle when subclasses depend on hidden details of a base class, or when a subclass cannot honor the base class's promises. Prefer **composition** when one object can use another to do a job:

```java
class ReportService {
    private final Notifier notifier;
    ReportService(Notifier notifier) { this.notifier = notifier; }
    void publish(String recipient, String report) {
        // save or generate the report
        notifier.send(recipient, "Your report is ready");
    }
}
```

`ReportService` has a notifier; it is not a kind of notifier. Composition makes the dependency replaceable without creating a subclass for every combination.

## 4. Construction, lifecycle, and static/final members

A **constructor** establishes an object's initial valid state. Require essential information up front; avoid a sequence of setters that leaves an object incomplete and usable too early.

```java
class User {
    private final String email;

    User(String email) {
        if (email == null || !email.contains("@")) {
            throw new IllegalArgumentException("valid email required");
        }
        this.email = email;
    }
}
```

An object's **lifecycle** goes from creation, through use, to disposal. Garbage collection (where available) reclaims memory, but it does not necessarily close files, release locks, or return connections to a pool. Use explicit resource scopes or `close()`/`using`/`defer` patterns supplied by the language:

```java
try (FileReader reader = new FileReader("notes.txt")) {
    // read from the file
} // close is guaranteed, including when an error occurs
```

A **destructor** in a deterministic-lifetime language may run when an object leaves scope. A **finalizer** or garbage-collector callback in a managed language may run late or not at a predictable time. Neither should be the only cleanup mechanism for important external resources.

**Static** members belong to a type rather than a particular object. A pure utility such as `Math.max` is a reasonable static operation. Mutable static state is shared globally and can make behavior order-dependent and tests interfere with each other; pass changing dependencies explicitly where practical.

**Final** has language-specific meanings. In Java, a final variable cannot be reassigned, a final method cannot be overridden, and a final class cannot be subclassed. In other languages, `final`, `sealed`, or `const` can mean something different. Prevent extension or reassignment only when it expresses a real constraint.

## 5. Enums and state transitions

An **enum** represents a closed set of named alternatives:

```java
enum OrderStatus { DRAFT, PAID, SHIPPED, CANCELLED }
```

Named alternatives are clearer and safer than unexplained strings such as `"paid"` or numeric codes such as `2`. But listing states does not define which transitions are valid. Put transition rules where they can be consistently enforced:

```java
void ship() {
    if (status != OrderStatus.PAID) {
        throw new IllegalStateException("only paid orders can ship");
    }
    status = OrderStatus.SHIPPED;
}
```

Think through the transition table before implementation: Can a draft be cancelled? Can a shipped order be cancelled? What happens on a repeated request? When enum values are stored or sent across a versioned API, tolerate or explicitly reject unknown values according to that boundary's compatibility requirements; persisted data may outlive the version of code that wrote it.

## 6. Immutability and equality

An object is **immutable** when its observable state cannot change after construction. Immutability makes sharing and reasoning easier: once validated, the object's facts stay true. `final` fields alone are not enough if they point to mutable objects that callers can change.

```java
final class Money {
    private final long cents;
    private final String currency;

    Money(long cents, String currency) {
        if (currency == null || currency.isBlank()) throw new IllegalArgumentException("currency required");
        this.cents = cents;
        this.currency = currency;
    }

    long cents() { return cents; }
    String currency() { return currency; }

    Money plus(Money other) {
        if (!currency.equals(other.currency)) {
            throw new IllegalArgumentException("currencies must match");
        }
        return new Money(cents + other.cents, currency);
    }
}
```

`plus` returns a new value instead of changing either input. A complete implementation should define **value equality**—two `Money` objects with equal cents and currency are equal—and consider overflow and currency normalization. Never silently add different currencies: conversion requires an explicit exchange rate and policy.

If an immutable object contains a list or map, make a defensive copy on input and avoid exposing a mutable reference. Otherwise a caller can change the apparent value from the outside.

Mutable objects are not inherently wrong. They can be appropriate for state that changes over time (such as an account balance), provided mutations are controlled and invariants remain true. Immutability is a design choice, not a requirement to make every object immutable.

## 7. Entities, value objects, DTOs, and domain models

These are different roles, not competing class syntaxes:

| Kind | What defines it? | Example |
|---|---|---|
| **Entity** | Stable identity; attributes may change | A bank account with account ID `A-17` |
| **Value object** | Its values; usually immutable and compared structurally | `Money(500, "USD")` |
| **DTO** (data-transfer object) | Carrying data across a boundary | An API response containing account ID and balance |
| **Domain model** | The business concepts and rules represented in code | An account that enforces deposit/withdrawal rules |

Two accounts with the same balance are still distinct entities if their IDs differ. Two `Money(500, "USD")` values are interchangeable if their values are equal. A DTO should reflect the boundary's data shape; it need not enforce every domain rule or expose internal domain objects.

```java
// Boundary shape: data to serialize or send
record AccountResponse(String id, long balanceCents) {}

// Domain object: owns behavior and invariant
class Account {
    // identity, state, and deposit/withdraw rules
}
```

Mapping between a domain object and a DTO is often deliberate: API fields and internal business rules evolve for different reasons. Avoid making a request/response object the only place that protects a business invariant.

## 8. Anemic and rich domain models

An **anemic domain model** has objects that mostly hold data while services contain most business rules. This can be perfectly adequate for simple CRUD, reporting, or data transformation. It becomes risky when the same rules are duplicated across handlers and can be bypassed.

A **rich domain model** keeps meaningful behavior near the state and invariants it protects:

```java
// Richer model: callers request a valid operation
account.withdraw(cents);

// Anemic pattern: callers manipulate data and must remember all rules
account.setBalance(account.getBalance() - cents);
```

The richer operation gives one place to reject invalid withdrawals. Do not confuse “rich” with “many classes” or “every field has a getter and setter.” Use domain behavior where it clarifies real rules; keep straightforward data as data. The right design is the simplest one that reliably expresses the requirements.

## A repeatable design checklist

When modeling a new concept, ask:

1. What does this concept mean to the user or business?
2. What facts must always be true (invariants)?
3. Which operations can change those facts, and who should enforce the rules?
4. Does it have persistent identity, or is it defined by its values?
5. Is it data for a boundary (DTO), or part of the domain model?
6. Would callers benefit from a stable interface and multiple implementations?
7. Is inheritance truly substitutable, or would composition be clearer?
8. Who owns external resources, and how are they released?
9. Would immutability make the object easier to reason about?

## Common pitfalls

- **Public fields and setters everywhere:** any caller can bypass the object's rules.
- **Inheritance for code reuse alone:** shared lines of code do not prove an “is-a” relationship.
- **An interface for every class:** abstractions are useful when they define a boundary or allow meaningful variation, not as ceremony.
- **A class for every noun:** simple data may be better as a record, struct, or plain value.
- **Assuming garbage collection closes resources:** memory cleanup and external-resource cleanup are separate.
- **Treating a DTO as the domain model:** transport shape and business behavior often have different needs.
- **Using strings for closed states:** misspellings and undocumented transitions become easy.
- **Making mutable objects immutable by convention only:** defensive copying and no mutators are needed to prevent external changes.

## Quick self-check

Try to answer without looking back:

1. How do encapsulation and abstraction differ?
2. Why should a withdrawal be an operation rather than a public balance update?
3. When would an interface be preferable to an abstract class?
4. What makes an entity different from a value object?
5. Why does garbage collection not replace closing a file?
6. Give one case where an anemic model is reasonable and one where domain behavior should live with state.

Use the exercises in [practice.md](practice.md) to apply these ideas to complete designs.
