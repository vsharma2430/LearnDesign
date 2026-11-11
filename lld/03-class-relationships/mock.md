# Class Relationships — Mock Interview Q&A

This file focuses on class-relationship questions that frequently appear in low-level design interviews. The goal is not to memorize labels but to explain the reasoning behind them.

## 1. What is the difference between association and composition?

### Answer

Association means there is a meaningful relationship between objects, but not necessarily ownership. Composition means the whole owns the lifecycle of the part.

Example:

```java
class Doctor {
    private Patient patient;
}

class Order {
    private List<LineItem> lineItems;
}
```

A `Doctor` and `Patient` are associated because both can exist independently. An `Order` and its `LineItem` objects are often composition because the line items are created and destroyed as part of the order.

The key interview point: association is about relationship; composition is about ownership and lifecycle.

## 2. When would you choose aggregation instead of composition?

### Answer

I would choose aggregation when the parts are conceptually contained by the whole but can also exist independently.

Example:

```java
class Team {
    private List<Player> players;
}
```

A player can join another team, switch clubs, or retain independent identity outside the team. That makes aggregation a better fit than composition.

Aggregation is a weaker ownership model. It suggests a whole/part relationship without the strict lifecycle dependency of composition.

## 3. How do you distinguish dependency from association?

### Answer

A dependency is temporary and usually method-scoped. An association is a more persistent relationship where one object holds a reference over time.

```java
class ReportGenerator {
    void generate(Clock clock, User user) {
        // clock used only in this method
    }
}
```

This is a dependency because the `Clock` is used only for a specific operation.

If the object stores `Clock` as a field and reuses it across operations, that becomes a relationship with longer-lived coupling, often modeled as an association or injected collaborator.

## 4. Why is dependency on an interface better than dependency on a concrete class?

### Answer

Because it reduces coupling and makes the code more flexible.

```java
interface Exporter {
    void export(String path);
}

class CsvExporter implements Exporter {
    public void export(String path) { }
}
```

If a service depends on `Exporter`, it can work with `CsvExporter`, `JsonExporter`, or a mock in tests without changing the service code. If it depends on a concrete class, it becomes tightly bound to one implementation and has less flexibility.

This is one reason realization and abstraction are important in design.

## 5. When is inheritance a good idea?

### Answer

Inheritance is a good idea only when the subtype is truly a subtype and can satisfy the base contract without surprises.

```java
abstract class Animal { abstract void sound(); }
class Dog extends Animal { public void sound() { System.out.println("Woof"); } }
```

This works because a `Dog` is genuinely an `Animal` and can honor the `Animal` contract.

Inheritance is not a general-purpose code reuse tool. If the relationship is more like “uses-a” or “has-a,” composition is often the right answer.

## 6. Why is `Square extends Rectangle` often a bad design?

### Answer

Because a square cannot always honor the full rectangle contract.

A rectangle allows independent width and height changes. A square requires both dimensions to remain equal. That breaks substitutability.

If the base type says one dimension can change independently, then a subclass must not impose a stronger invariant that the caller does not expect. This is a classic violation of the Liskov Substitution Principle.

The better modeling choice is usually a shared `Shape` contract with separate implementations.

## 7. What does realization mean in UML and code?

### Answer

Realization means a concrete class implements an interface or fulfills an abstract contract.

```java
interface Exporter {
    void export(String path);
}

class CsvExporter implements Exporter {
    public void export(String path) { }
}
```

In UML, the implementing class connects to the contract with a dashed line and a hollow triangle. In code, it is the `implements` relationship.

This relationship is fundamental to dependency inversion: callers depend on the abstraction, not the concrete implementation.

## 8. How would you answer a question about a `Playlist` containing tracks?

### Answer

I would first ask whether tracks are independent content objects or owned by the playlist.

If tracks are reusable songs in a library, then a playlist is likely an aggregation or association because the same song can appear in many playlists.

If the playlist owns the tracks and deleting the playlist deletes those track entries, then composition is better.

This answer shows I am reasoning about lifecycle and ownership, not just writing a generic “playlist has tracks” statement.

## 9. What is the strongest relationship and why does it matter?

### Answer

Composition is often the strongest relationship because it expresses ownership and lifecycle control.

The reason this matters is that the design tells us what can be safely deleted, what must be created together, and what cannot be reused elsewhere.

If the relationship is too strong, we risk over-modeling. If it is too weak, we lose important domain semantics. The goal is to model the ownership assumptions honestly.

## 10. How would you phrase a strong interview answer about choosing relationships?

### Answer

I would say:

> “I choose the relationship based on ownership, lifecycle, and substitutability. If the part is created and destroyed with the whole, I use composition. If the part can exist independently, I use aggregation or association. If the interaction is temporary, I use dependency. If the subclass is a real subtype under the base contract, I use inheritance; otherwise I prefer composition or an interface. The important thing is to match the design to the domain assumptions, not just to diagram conventions.”

That answer shows both conceptual clarity and practical judgment, which is exactly what interviewers want.
