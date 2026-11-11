# Design Principles — Lesson Notes

These principles are not commandments. They are decision tools. A good design usually comes from balancing trade-offs: fewer moving parts, less duplication, lower coupling, and enough flexibility to handle real change without building a big abstraction before the need is proven.

When we talk about design principles, the real question is not “Which slogan wins?” but “What is the likely cost of change?” If a design makes the next feature easier to add and easier to reason about, it is probably a good fit. If it adds complexity without fixing a real problem, it is not.

## 1. DRY (Don't Repeat Yourself)

DRY means there should be one authoritative representation of a fact or policy. If the same business rule appears in multiple places, then a future change will require changing several places, which creates maintenance risk.

The important idea is “knowledge,” not just repeated lines of code. Code can look similar and still represent different rules.

### What counts as duplicate knowledge?

Duplicate knowledge usually means:
- the same rule is repeated in multiple places
- a bug fix must be made in several files
- a business decision is duplicated across similar methods
- two modules must change together because they copied the same fact

### What does not count as duplicate knowledge?

- Similar-looking code paths that implement different policies
- Repeated loops or data-fetching code that happen to be shaped the same way
- A helper method created just to hide a few lines, when it makes the abstraction harder to understand

### Example: real duplication

```java
class UserService {
    boolean canAccessReport(User user) {
        return user.getRole().equals("ADMIN") || user.getRole().equals("MANAGER");
    }

    boolean canDeleteUser(User user) {
        return user.getRole().equals("ADMIN") || user.getRole().equals("MANAGER");
    }
}
```

This is duplicate business knowledge. If the rule changes later, both methods need updates.

### Example: same shape, different rule

```java
class TaxCalculator {
    BigDecimal calculateTax(BigDecimal amount, String country) {
        if (country.equals("US")) return amount.multiply(new BigDecimal("0.07"));
        if (country.equals("IN")) return amount.multiply(new BigDecimal("0.18"));
        return BigDecimal.ZERO;
    }
}
```

This may look repetitive, but the logic is different by country. That is not “same knowledge,” just similar code. Forcing all tax logic into one giant abstraction too early would hide meaningful policy distinctions.

### Better evolution path

At first, keep it simple:

```java
class TaxCalculator {
    BigDecimal calculateTax(BigDecimal amount, String country) {
        switch (country) {
            case "US": return amount.multiply(new BigDecimal("0.07"));
            case "IN": return amount.multiply(new BigDecimal("0.18"));
            default: return BigDecimal.ZERO;
        }
    }
}
```

Later, if you add many regions, exemptions, and VAT/GST rules, then a policy map or strategy object may become valuable.

### DRY rule of thumb

Extract common logic only when it represents one real rule and not just similar code. A shared abstraction should make the system easier to reason about, not harder.

### Warning sign

If the abstraction needs a large conditional tree, many special cases, or knowledge about every caller, it probably is not a good unification. The abstraction itself becomes the new complexity center.

---

## 2. KISS (Keep It Simple)

KISS means choose the design with the least conceptual machinery that still solves the problem. This is not about writing the shortest possible code. It is about avoiding unnecessary complexity in the model, workflow, and execution path.

A design is simple if a new engineer can explain it without memorizing hidden assumptions.

### Why simplicity matters

Complexity is not just the number of classes. It is the number of ideas and moving pieces a person must keep in their head to understand the system.

A system with many layers, hidden indirections, and implicit rules may look elegant to the author, but it becomes difficult and risky for everyone else.

### Example: over-engineered but not simpler

```java
interface PaymentProcessor {
    void process(Payment payment);
}

class CreditCardProcessor implements PaymentProcessor { /* ... */ }
class UpiProcessor implements PaymentProcessor { /* ... */ }
class WalletProcessor implements PaymentProcessor { /* ... */ }
class PaymentProcessorFactory { /* ... */ }
class PaymentProcessorRegistry { /* ... */ }
```

This is reasonable only if the domain truly needs multiple payment strategies and the platform has real variation. But if the app currently only supports credit-card payments, this explosion adds abstraction without value.

### Example: a simpler design that still works

```java
class CheckoutService {
    void pay(Order order) {
        PaymentGateway gateway = new StripeGateway();
        gateway.charge(order.getAmount());
    }
}
```

This is not a perfect extensible architecture, but it is honest, easy to follow, and adequate until the requirements show a clear need for multiple providers.

### KISS checklist

- Can a developer explain the flow in a few paragraphs?
- Are the important decisions visible instead of buried in indirection?
- Is there a hidden abstraction that only exists to look elegant?
- Does each class or module have one clear job?

### Practical rule

Use the simplest design that preserves the required behavior and the likely future extension points. Keep a path open for change, but do not build the entire future today.

---

## 3. YAGNI (You Ain’t Gonna Need It)

YAGNI warns against building features, abstractions, and frameworks “just in case.” If no proven requirement exists, do not add speculative interfaces, configuration layers, or plugin systems just because they seem extensible.

This does not mean “never prepare for the future.” Good systems stay adaptable. The difference is whether the extension is justified by current evidence or by speculation.

### Healthy YAGNI

- building a second implementation after a second requirement is already visible
- introducing an interface when there are clearly two current implementations
- writing code in a modular way so future edits are easy without over-architecting

### Unhealthy YAGNI

- a full plugin architecture for a one-off feature
- a complex factory/registry system for a single current provider
- generic abstractions created before the real variation is known

### Example: bad speculative design

```java
interface PaymentProvider {
    void pay(BigDecimal amount);
}

class PaymentProviderFactory {
    // created for future multi-provider support
}

class ConfigurablePaymentProcessor {
    // built before any requirement for multiple providers
}
```

If there is only one provider and no requirement for switching providers, this is not future-proofing. It is premature abstraction.

### Better approach

Start with the actual requirement:

```java
class CardPaymentProcessor {
    void processPayment(Order order) {
        // charge the card with Stripe today
    }
}
```

Then, when a second provider or payment method becomes real, refactor into an interface and factory as needed.

### Interview-friendly way to say it

“Design for the next change you can justify, not for every hypothetical future.”

---

## 4. Composition over inheritance

Inheritance represents an “is-a” relationship. Composition represents a “has-a” or “uses-a” relationship. In many systems, composition is easier to evolve because behaviors can be mixed without building a deep inheritance tree.

Use inheritance when there is a genuine subtype relationship and the base contract is stable. But do not use inheritance just because it looks neat or because the code is easier to write at the start.

### Example: good composition

```java
interface PriceStrategy {
    BigDecimal apply(BigDecimal amount);
}

class StandardPricing implements PriceStrategy {
    public BigDecimal apply(BigDecimal amount) { return amount; }
}

class PremiumPricing implements PriceStrategy {
    public BigDecimal apply(BigDecimal amount) { return amount.multiply(new BigDecimal("1.10")); }
}

class CheckoutService {
    private final PriceStrategy priceStrategy;

    CheckoutService(PriceStrategy priceStrategy) {
        this.priceStrategy = priceStrategy;
    }

    BigDecimal total(BigDecimal subtotal) {
        return priceStrategy.apply(subtotal);
    }
}
```

This is flexible. A different pricing policy can be supplied without creating new subclasses for every combination.

### Example: inheritance explosion

```java
class RetailCustomer extends User {}
class PremiumRetailCustomer extends RetailCustomer {}
class VipPremiumRetailCustomer extends PremiumRetailCustomer {}
class InternationalVipPremiumRetailCustomer extends VipPremiumRetailCustomer {}
```

This quickly becomes unmanageable. The problem is not that inheritance is always bad; the problem is that the domain is mixing multiple independent concerns such as customer type, region, status, and discounts into an ever-growing type hierarchy.

### Composition is not automatically better

If the domain really has a valid shared base and a clear subtype hierarchy, inheritance may be the cleaner choice. For example, `Dog extends Animal` is a real subtype relationship. But in many business systems, the “shape” of variation is more like combinations of behaviors than strict inheritance.

### Decision guide

- Use inheritance for real subtype relationships with consistent meaning.
- Use composition when behaviors vary independently or may be combined in many ways.
- Avoid deep hierarchies when the combinations are growing faster than the actual domain concepts.

---

## 5. Law of Demeter / Least Knowledge

The Law of Demeter says a module should talk to its immediate collaborators, not traverse through a long chain of unrelated objects just because they happen to be connected.

The problem is that object chaining makes the caller depend on the internal structure of the system. A small change to the object graph can force many unrelated callers to change.

### Example of a problematic dependency chain

```java
order.getCustomer().getAddress().getCountry().getCode();
```

This code knows too much about how the domain is structured. It is tightly coupled to the full object graph and makes the call site fragile.

### Better design: ask for intent

```java
if (order.isInternational()) {
    // apply shipping rules
}
```

or

```java
String countryCode = order.shippingCountryCode();
```

This hides the internal structure behind a domain-level operation. The caller now asks for the meaning it cares about instead of walking the object graph.

### Example of a useful wrapper

```java
class Order {
    private Customer customer;

    String shippingCountryCode() {
        return customer.getAddress().getCountry().getCode();
    }
}
```

This is okay because the method expresses the domain concept “shipping country” and centralizes the access logic.

### Example of a useless wrapper

```java
class Order {
    Customer getCustomer() { return customer; }
}
```

This does not reduce coupling. It just exposes the dependency and keeps the chain alive. A wrapper only adds value if it represents a meaningful abstraction boundary.

### Guiding rule

- Prefer talking to the collaborator that owns the behavior.
- Avoid long chains of traversal unless that chain is part of the domain concept.
- Add a helper method only when it clarifies intent and reduces coupling.

---

## How these principles work together

These principles often pull in different directions, and that is normal.

- DRY helps reduce duplicated knowledge, but overusing it can create a complicated abstraction with lots of branching.
- KISS reduces mental overhead, but a too-simple design can ignore real variation that should be explicit.
- YAGNI prevents speculative work, but it does not mean avoiding all future-friendly design.
- Composition improves flexibility, but inheritance may still be the correct fit for genuine subtype relationships.
- Law of Demeter reduces coupling, but wrappers can become shallow pass-through methods that add no real value.

### A good mental model

Ask these questions:
- What is the actual change pressure in this system?
- What rule is being duplicated, and is it the same rule or just similar code?
- Is this abstraction solving a real problem or only looking elegant?
- Is the design understandable to a new teammate in one sitting?
- Does this reduce coupling, or is it just moving knowledge around?

A principle is useful only if it reduces confusion and maintenance cost. If it creates more hidden rules and more special cases than it removes, it is the wrong trade-off.

### One-line summary

Good software design is not about blindly following slogans. It is about choosing the simplest structure that captures real domain relationships, avoids duplicate knowledge, and keeps future change manageable without overbuilding.
