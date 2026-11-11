# Design Principles — Lesson Notes

These principles are decision tools, not religious rules. Good software design usually comes from managing trade-offs: fewer moving parts, less duplication, and less coupling without building a complex abstraction before the need is proven.

## 1. DRY (Don't Repeat Yourself)

DRY means there should be one authoritative representation of a fact or policy. If the code is expressing the same business rule in multiple places, that is duplication of knowledge and a maintenance risk.

The danger is applying DRY too aggressively. Code can look similar without representing the same knowledge. Two methods may both calculate a price, but one rounds to the nearest cent while the other truncates toward zero. That is not the same rule; it is a different policy. If you force them into one method with many conditionals, you create an abstraction that hides important decisions.

### What counts as duplicate knowledge?

Duplicate knowledge usually means:
- the same rule is repeated in multiple places
- a bug fix must be made in several files
- a business decision is expressed in multiple conditional branches
- two modules must change together because a shared fact was copied

### What does not count as duplicate knowledge?

- Similar-looking code paths that implement different policies
- Repeated loops that happen to be shaped the same way
- A helper method created just to avoid a few lines, when the method only hides a narrow detail and makes the abstraction less understandable

### Example

```java
class TaxCalculator {
    BigDecimal calculateTax(BigDecimal amount, String country) {
        if (country.equals("US")) return amount.multiply(new BigDecimal("0.07"));
        if (country.equals("IN")) return amount.multiply(new BigDecimal("0.18"));
        return BigDecimal.ZERO;
    }
}
```

This is not a place to over-abstract unless a tax policy framework is genuinely needed. The rules are different by country and are likely to evolve independently. If you later add VAT, GST, or region-specific exemptions, a simple policy map may help—but only after the pattern is real.

### Good DRY habits

- Extract rules only when they are truly the same rule
- Separate policy from mechanics
- Keep the abstraction narrow and easy to explain
- Prefer a shared concept with a clear boundary over a “common helper” that does everything

### Warning sign

If the shared abstraction needs a large conditional tree, special-case exceptions, or constant knowledge from every caller, it probably is not a good unification. The abstraction is becoming the new complexity anchor.

## 2. KISS (Keep It Simple)

KISS means prefer the design with the least conceptual machinery that still meets the requirement. This is not about writing the fewest lines of code; it is about keeping the mental model understandable.

A simple design is easy to explain, easy to test, and easy to change without a huge amount of hidden behavior. Simplicity is often the opposite of cleverness. A system with twenty tiny layers and a highly abstract base class may look elegant, but it adds cognitive cost.

### Why simplicity matters

Complexity is not just the number of classes. It is the number of moving parts a developer must keep in their head to understand a change.

If the system is hard to reason about, even small edits become dangerous. In interviews, a good answer is not “use the simplest code possible”; it is “use the simplest architecture that still preserves the needed invariants and extension points.”

### Example

A billing system with a `PaymentProcessor` interface and three strategies is okay if the domain really has three distinct flows. But a large `PaymentProcessor` implementation with one giant switch over payment method, region, currency, and risk level is a smell. It is not simpler because it uses interfaces; it is just distributed complexity.

### KISS checklist

- Can a new engineer explain the flow in a few paragraphs?
- Are the important decisions visible, not buried in indirection?
- Does the design minimize hidden assumptions?
- Is the class or module doing one job, not five?

### Practical rule

Choose the simplest approach that preserves clarity and support for expected change. If future changes are uncertain, keep a path open but do not add the whole framework yet.

## 3. YAGNI (You Ain't Gonna Need It)

YAGNI warns against implementing features “just in case.” If no proven requirement exists, do not add speculative interfaces, plugins, or abstractions just because they seem extensible.

This does not mean “never design for growth.” Good design keeps a future path open without building the entire future today. The difference is evidence.

### Healthy YAGNI

- being ready for a second similar implementation when the pattern is already visible
- using an interface when there are clearly two current implementations
- keeping code clean and modular enough that a later change is straightforward

### Unhealthy YAGNI

- a “plug-in architecture” for a requirement that is not yet requested
- a generic factory, strategy, decorator, and command system when only one behavior exists today
- building an extensibility layer before you know how variation will actually happen

### Example

A product has one payment provider, and the spec says nothing about multi-provider support. It would be poor design to add a provider interface, registry, factory, and config objects “for the future” if the immediate task is to process card payments. This is not future-proofing; it is overbuilding.

### A better approach

Start with the direct requirement and then refactor when you can point to a real second requirement. If a change is likely but not clearly proven, keep the code easy to adapt. That is “future-friendly,” not “speculative.”

## 4. Composition over inheritance

Inheritance models an “is-a” relationship. Composition models a “has-a” or “uses-a” relationship. In many systems, composition is more flexible because it allows independent variation without forcing a complex hierarchy.

Use inheritance when there is a genuine subtype relationship and the base contract is stable. The subclass must be able to stand in for the parent without surprising callers.

Use composition when behavior is assembled from independent parts, or when combinations may grow over time.

### Example

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

This is easier to evolve than a large inheritance tree for every pricing rule combination. A subclass explosion like `CardPricingUSVisaRetail` or `CardPricingHandlingFeeRegional` often indicates the design is mixing independent axes of variation.

### Composition is not a magic rule

Composition is not automatically better. If there is a real shared base implementation and a valid hierarchy, inheritance can be cleaner and easier to reason about. The right question is not “inheritance or composition?” It is “what relationship is real in this domain?”

### Decision guide

- Use inheritance for a genuine subtype hierarchy with consistent semantics.
- Use composition when behaviors vary independently or may combine in many ways.
- Avoid a deep hierarchy if the combinations are growing faster than the actual domain concepts.

## 5. Law of Demeter / Least Knowledge

The Law of Demeter says a module should talk to its immediate collaborators, not navigate through lots of internal objects. In other words, do not reach through a chain of objects just because they happen to be connected.

A common smell is:

```java
order.getCustomer().getAddress().getCountry().getCode();
```

This ties the caller to the internal structure of the domain. It is fragile because any small change to the object graph can force callers to change too.

### Better design

Instead of asking for a deeply nested value, ask the domain for the operation that expresses intent:

```java
if (order.isInternational()) {
    // ...
}
```

Or:

```java
String countryCode = order.shippingCountryCode();
```

The wrapper is useful if it expresses domain meaning and reduces knowledge leakage. It is not useful if it simply forwards a getter from one object to another without clarifying intent.

### Example of a useful wrapper

```java
class Order {
    private Customer customer;

    String shippingCountryCode() {
        return customer.getAddress().getCountry().getCode();
    }
}
```

Here, the method captures the meaning of shipping and hides the structure. The call site changes from object-pipeline walking to domain-level behavior.

### Example of a useless wrapper

```java
class Order {
    Customer getCustomer() { return customer; }
}
```

This does not help; it just exposes the dependency and keeps the chain alive. A forwarding method only adds value if it corresponds to an actual domain concept or a better abstraction boundary.

### Guiding rule

- Prefer direct interaction with a collaborator that owns the operation.
- Avoid long chains of traversal unless the chain is part of the domain concept.
- Add a wrapper only when it describes intent, reduces coupling, and makes the API clearer.

## How these principles work together

These principles often conflict, and that is normal.

- DRY helps avoid repeated facts, but overusing it can create an abstraction with many branches.
- KISS keeps the model understandable, but it can conflict with building explicit extension points.
- YAGNI prevents speculative work, but not every design should stay rigid to a one-off requirement.
- Composition gives flexibility, but inheritance may still be the clearest model for real subtype relationships.
- Law of Demeter reduces coupling, but a wrapper can become a shallow pass-through that adds no real value.

The real skill is not memorizing slogans. It is asking: what is the actual change pressure? What is the maintenance cost if the system must evolve? What decision do callers need to make today, and what is the least expensive way to preserve future flexibility without inventing complexity prematurely?

A good design principle should reduce the number of decisions a caller or maintainer must make later. If a principle creates more hidden rules than it removes, it is not the right trade-off.
