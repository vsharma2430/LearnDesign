# SOLID Principles — Lesson Notes

SOLID is a set of design rules that help us preserve flexibility as software changes. The principles are valuable because they reduce coupling, clarify responsibilities, and make it easier to add behavior without rewriting large parts of the system.

The best way to use SOLID is not as a slogan checklist. It is as a lens for asking whether a specific design boundary actually reduces change cost. If a principle adds complexity without reducing real maintenance risk, it is being applied too aggressively.

## 1. Single Responsibility Principle (SRP)

A class or module should have one coherent reason to change.

This is often described as “one actor” or “one responsibility,” but the important point is not literal one method per class. A class can have many methods and still be SRP-compliant if those methods support the same responsibility.

### Why it matters

When responsibilities are mixed together, a small change causes unrelated behavior to break. This creates fragile code where the system is hard to extend safely.

### Example: `ReportService`

Consider this class:

```java
class ReportService {
    void generateReport(User user) {
        List<Order> orders = orderRepository.findByUser(user);
        String html = reportFormatter.toHtml(orders);
        mailer.send(user.getEmail(), html);
    }
}
```

This class mixes:

- data access (`orderRepository`)
- formatting (`reportFormatter`)
- delivery (`mailer`)

These are three responsibilities that can change for different reasons. If the storage query changes, the formatter should not need to be touched. If we add a PDF output format, the service should not need to know about the transport layer.

### Better design

Split by responsibility:

```java
class OrderReportQueryService {
    List<Order> loadOrdersFor(User user) { ... }
}

class ReportFormatter {
    String toHtml(List<Order> orders) { ... }
}

class ReportSender {
    void send(String email, String content) { ... }
}
```

Now each module changes because of a different concern, which reduces coupling and makes tests smaller and clearer.

### What SRP is not

SRP does not mean:

- every class must have exactly one method
- every class must be tiny
- every concept must become a separate file or object

It means the behavior should be grouped around a meaningful reason to change. If a boundary does not make meaning clearer, it is probably not a useful separation.

## 2. Open/Closed Principle (OCP)

A design should be open for extension but closed for modification.

This means we should be able to add new behavior without repeatedly changing the same stable code path.

### Why it matters

The most expensive changes are often the ones where new variants require editing a central decision point. A big `switch` statement or a class with many conditional branches tends to grow over time and becomes brittle.

### Example: shipping strategy

Bad design:

```java
class ShippingCostCalculator {
    double calculate(Order order, String method) {
        switch (method) {
            case "GROUND": return 10;
            case "EXPRESS": return 25;
            case "INTERNATIONAL": return 40;
            default: throw new IllegalArgumentException();
        }
    }
}
```

This is easy to read now, but every new shipping method requires editing the same switch. The logic is central, fragile, and easy to break.

Better design:

```java
interface ShippingStrategy {
    double calculate(Order order);
}

class GroundShipping implements ShippingStrategy { ... }
class ExpressShipping implements ShippingStrategy { ... }
```

Then a factory or registry selects the strategy. The calculator remains stable while the strategy collection grows.

### When a switch is still fine

A switch can still be appropriate when:

- there are only 2–3 variants
- the rules are not expected to grow much
- the logic is simple and centralized
- introducing a strategy would add more complexity than value

The key is to choose the smallest extension point that matches the real change pressure.

## 3. Liskov Substitution Principle (LSP)

A subtype must be able to replace its base type without violating the expectations of the callers.

In other words, if a client depends on an abstraction, every implementation of that abstraction must honor the same contract.

### Why it matters

A faulty inheritance hierarchy creates silent bugs. The subtype may appear compatible, but its behavior changes in ways clients do not expect.

### Example: `ReadOnlyList`

```java
class List<T> {
    void add(T value) { ... }
}

class ReadOnlyList<T> extends List<T> {
    @Override
    void add(T value) {
        throw new UnsupportedOperationException();
    }
}
```

This violates LSP because a caller using `List` may reasonably call `add()`. A `ReadOnlyList` does not behave like a normal list in the way the base contract promises.

### What is broken?

The issue is not just “it throws.” The deeper issue is that the subtype changes the semantics of the base type. This surprises callers and breaks substitutability.

A subtype may not:

- strengthen preconditions
- weaken postconditions
- throw for operations callers expect to succeed
- change the meaning of a valid operation in a hidden way

### Good use of inheritance

Inheritance is appropriate when the subclass genuinely acts like the parent in the same important behaviors. If the base contract says “it supports mutation,” the subtype must support mutation too unless it is a completely different abstraction.

## 4. Interface Segregation Principle (ISP)

Clients should not be forced to depend on methods they do not use.

Large interfaces are a common source of accidental coupling. When one interface is reused by unrelated clients, everyone inherits behavior they do not need.

### Example: `Worker`

```java
interface Worker {
    void print(Document doc);
    void scan(Document doc);
    void fax(Document doc);
}
```

Now a `BasicPrinter` must implement `scan` and `fax`, even though it cannot do them meaningfully. This is a bad contract because it forces irrelevant responsibilities onto a class.

### Better design

```java
interface Printer {
    void print(Document doc);
}

interface Scanner {
    void scan(Document doc);
}

interface Faxer {
    void fax(Document doc);
}
```

A `BasicPrinter` implements only `Printer`. A multifunction device can implement all three. This keeps the code honest and reduces wrong assumptions in the design.

### Why ISP matters

It leads to cleaner abstractions and smaller, more accurate contracts. It also makes it easier to create mocks and test doubles because each dependency is narrow and purposeful.

## 5. Dependency Inversion Principle (DIP)

High-level policy should not depend on low-level details. Both should depend on abstractions.

This is different from “add a generic interface everywhere.” The abstraction must model a real capability, not hide complexity behind a meaningless generic contract.

### Example: business policy constructing a database client

Bad design:

```java
class PricingPolicy {
    private final DatabaseClient client = new DatabaseClient();

    double getDiscount(User user) {
        return client.query("select discount from users where id = ?", user.getId());
    }
}
```

This makes business logic tightly coupled to a concrete infrastructure choice. If the database changes, policy code changes too.

### Better design

```java
interface UserStore {
    double findDiscountFor(String userId);
}

class SqlUserStore implements UserStore { ... }

class PricingPolicy {
    private final UserStore userStore;

    PricingPolicy(UserStore userStore) {
        this.userStore = userStore;
    }
}
```

Now the policy depends on a capability (`UserStore`), not a concrete database client. This makes the logic easier to test and more stable under infrastructure changes.

### What DIP is not

DIP is not “always use an interface.” It is about dependency direction and abstraction quality.

A bad interface would be something like:

```java
interface GenericDatabase {
    void execute();
    Object query();
    void connect();
    void disconnect();
}
```

This is too broad and exposes implementation details. The abstraction should express the actual behavior needed by the policy, not every detail of the infrastructure.

## Applying the principles together

SOLID works best as a coordinated design strategy:

- SRP isolates change by responsibility
- OCP allows variation by extension instead of mutation
- LSP protects substitutability in inheritance hierarchies
- ISP keeps interfaces small and meaningful
- DIP keeps policy independent of infrastructure

A strong design does not maximize abstractions. It chooses boundaries that reduce accidental coupling and reflect real business or technical change.

### Interview mindset

When answering design questions, explain the trade-off:

- what the current problem is
- which contract or dependency is wrong
- what abstraction or boundary fixes it
- why the fix is worth it
- when the abstraction would become over-engineering

Good SOLID answers are practical, not doctrinal. Real software design is about managing cost, not memorizing rules.
