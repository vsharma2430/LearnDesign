# SOLID Principles — Practice

These are interview-style practice prompts. For each one, explain the problem, the likely design change, and the trade-off.

## 1) SRP — `ReportService`

A `ReportService` queries data, formats HTML, and emails the report. Apply SRP by naming cohesive responsibilities and a likely reason each changes.

### Sample answer

This class is violating SRP because it mixes at least three responsibilities:

- data access: the query may change when the storage schema changes
- presentation/report formatting: the format may change from HTML to PDF or CSV
- delivery: the email channel may change or be replaced by a queue / webhook

A cleaner design is to split them into smaller modules such as:

- `ReportRepository` or `ReportQueryService`
- `ReportFormatter`
- `ReportSender` or `ReportDeliveryService`

Each unit changes for a different reason, so the code is easier to test and maintain.

## 2) OCP — shipping method switch

A new shipping method requires editing a large `switch`. Sketch an OCP-friendly design and state when the switch is still simpler.

### Sample answer

Instead of one giant `switch` in a `ShippingService`, introduce a polymorphic seam such as:

```java
interface ShippingStrategy {
    ShippingCost calculate(Order order);
}
```

Then register strategies for `Express`, `Ground`, `International`, etc., in a map or factory, optionally with dependency injection.

This makes the system open for extension because new shipping rules can be added without rewriting the core logic. The `ShippingService` remains closed to modification for each new type.

A `switch` is still acceptable when:

- there are only 2–3 variants
- the rules are unlikely to change
- the behavior is trivial and centralized
- adding an abstraction would make the code harder to understand

## 3) LSP — `ReadOnlyList`

A `ReadOnlyList` subtype throws from `add`. Analyze the client expectation and explain the LSP issue.

### Sample answer

Clients expect a list to support the standard `add` contract because they are using the base type or abstraction. If a `ReadOnlyList` inherits from `List` but throws on `add`, then it violates the same contract as the parent.

This breaks substitutability: a caller using a `List` cannot safely rely on the subtype behaving like the underlying abstraction. The subtype unexpectedly strengthens preconditions or changes semantics.

The fix is either:

- make the type not inherit from `List` if it is genuinely read-only
- or use a different abstraction such as `ReadOnlyCollection` / `Collection` with no mutating methods

In other words, a subtype must not restrict behavior beyond what the base contract promises.

## 4) ISP — `Worker` interface

A `Worker` interface includes `print`, `scan`, and `fax`; a basic printer cannot implement two methods meaningfully. Apply ISP.

### Sample answer

This interface violates ISP because it forces a `BasicPrinter` to implement methods it cannot support. The contract is too broad for the actual consumers.

A better design is to split interfaces by behavior:

```java
interface Printer {
    void print(Document doc);
}

interface Scanner {
    void scan();
}

interface Faxer {
    void fax(Document doc);
}
```

Then a basic printer implements only `Printer`, while a multifunction device can implement all three. This keeps clients dependent only on behavior they actually use.

## 5) DIP — direct database construction

A business policy directly constructs a database client. Apply DIP without hiding all details behind a meaningless generic interface.

### Sample answer

The business policy is tightly coupled to a concrete infrastructure dependency. That violates DIP because high-level policy should depend on an abstraction rather than a concrete client implementation.

A good approach is to define a narrower abstraction that represents the needed behavior, such as:

```java
interface UserStore {
    User findById(String id);
    void save(User user);
}
```

Then the `PricingPolicy` or `AccountService` depends on `UserStore`, and infrastructure code provides a concrete implementation such as `SqlUserStore`.

The key is not to create a generic interface with meaningless methods like `execute()` or `doEverything()`. The abstraction should model the domain capability the policy actually needs.

## Practice checklist

When you answer these questions in an interview, make sure you cover:

- the real reason for the problem
- the specific contract or dependency that is being violated
- the design change that fixes it
- the trade-off: why this abstraction is justified, and when it would be over-engineering
