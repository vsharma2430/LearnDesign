# Design Principles — Practice

These questions are meant to force a trade-off, not a slogan. For each question, name the real rule, the actual change pressure, and the simplest design that preserves the needed behavior.

## 1. Should the pricing formula be extracted?

Two pricing paths share the same formula but differ in rounding rules. One path uses bankers rounding, the other always rounds half up. The formula is the same, but the rounding policy is different.

### Question

Should the formula be extracted into a shared helper or method? Explain what knowledge is actually shared and what is intentionally different.

### Answer

Only the mathematical expression is shared; the rounding policy is not. If the formula is extracted into a helper but one path still needs a different rounding rule, the shared function becomes a hidden policy machine with branchy logic.

A better design is to separate the formula from the policy:

```java
class PriceCalculator {
    private final RoundingPolicy roundingPolicy;

    PriceCalculator(RoundingPolicy roundingPolicy) {
        this.roundingPolicy = roundingPolicy;
    }

    BigDecimal calculate(BigDecimal subtotal) {
        BigDecimal raw = subtotal.multiply(new BigDecimal("1.2"));
        return roundingPolicy.apply(raw);
    }
}
```

Here the shared concept is “multiply by a pricing multiplier,” while the specific rounding behavior remains explicit in a strategy object. The abstraction is valuable because the true requirement is that pricing logic and rounding policy are separable. If you extract only the formula and keep rounding as special cases, you are likely creating DRY at the expense of clarity.

## 2. Payment method explosion: composition instead of subclass tree

A system has a `PaymentMethod` base class and subclasses for every combination of card type, currency, and region, such as `VisaUsdUs`, `VisaEurEu`, `MastercardUsdUs`, and so on.

### Question

What is the design smell here, and how would you refactor it with composition?

### Answer

The class hierarchy mixes multiple independent axes of variation: card network, currency, and region. This creates a combinatorial explosion and makes every new rule or region a new class. The hierarchy is not expressing a true subtype relationship; it is encoding a cross-product of policies.

A better design is to compose behavior from smaller collaborators:

```java
interface PaymentPolicy {
    void validate(Transaction t);
    void capture(Transaction t);
}

class CardPaymentPolicy implements PaymentPolicy { ... }
class DomesticPolicy implements PaymentPolicy { ... }
class EuroPolicy implements PaymentPolicy { ... }
```

Then a `PaymentProcessor` can receive the relevant policies as collaborators:

```java
class PaymentProcessor {
    private final PaymentPolicy strategy;
    private final CurrencyPolicy currencyPolicy;
    private final RegionPolicy regionPolicy;
}
```

This reduces subclass sprawl and makes the rules more explicit. The trade-off is that the system may become more configurable and slightly more complex in assembly. But it is easier to reason about than a brittle inheritance tree and does not force every new category to become a class.

## 3. Law of Demeter: long chain traversal

A service is doing:

```java
if (order.getCustomer().getAddress().getCountry().getCode().equals("US")) {
    ...
}
```

### Question

Apply the Law of Demeter thoughtfully. What domain operation could clarify intent, and when would a wrapper add no value?

### Answer

This violates the least-knowledge principle because the caller knows too much about the object graph. It also couples the service to the internal structure of `Order`, `Customer`, `Address`, and `Country`.

A better design is to move the question to the domain object or service that owns the business meaning:

```java
if (order.isDomestic()) {
    ...
}
```

or:

```java
String countryCode = order.shippingCountryCode();
```

This is a useful wrapper because it clarifies intent. The wrapper hides knowledge of the chain and makes the call site express a domain decision instead of a traversal exercise.

A wrapper adds no value if it simply exposes a getter with no real conceptual meaning:

```java
class Order {
    Customer getCustomer() { return customer; }
}
```

This only moves the same chain to a different class. The method should represent a meaningful domain operation, not just forward a dependency.

## 4. YAGNI and plugin hooks

A design includes plugin hooks and extension points for features not mentioned in the prompt, such as a generic event bus, custom rule registry, and dynamic loader configuration.

### Question

Use YAGNI to decide what to keep and what to drop.

### Answer

YAGNI does not say “never support extension.” It says do not build speculative extensibility without evidence. A design should remain adaptable, but not create a large framework before the need exists.

Keep the pieces that are clearly useful for future change and cheap to maintain. For example:
- a small interface for an integration with one existing provider
- a clean method boundary for the code path that may evolve
- dependency injection if there is already a need to swap implementations

Drop or postpone:
- a plugin registry with dynamic loading when there is no current plugin requirement
- abstract factory chains for a single implementation
- configuration complexity that exists only to support hypothetical future cases

The best answer is not “avoid all extensibility”; it is “add extensibility only when it lowers the cost of the actual change pressure.” If there is no evidence of multiple implementations or repeated variation, the current design should stay direct.

## 5. Reflect on a recent change

Think of a change you made recently. It may have involved a pricing rule, validation, API behavior, or a feature switch.

### Question

For that change, name the concrete complexity that each design principle would reduce.

### Answer

Here is a useful template:

- DRY reduces duplicated business knowledge and the risk of fixing the same rule in multiple places.
- KISS reduces the number of concepts a maintainer must keep in memory while reading the code.
- YAGNI reduces speculative features, those extra layers and hooks that are not yet used.
- Composition over inheritance reduces combinatorial class growth and makes independent variations easier to express.
- Law of Demeter reduces coupling to internal object structure and cuts the number of cascading changes caused by refactors.

Example:

- If a pricing bug appeared in three places, DRY would reduce the chance of conflicting fixes.
- If a service had twelve different subclasses depending on region and currency, composition would reduce complexity.
- If a wrapper object was just passing through getters, Law of Demeter would help us remove the useless indirection.
- If a plugin framework was added for a requirement that never existed, YAGNI would have prevented the wasted design cost.

The answer should always tie the principle to a real maintenance cost. A principle is valuable only if it reduces a concrete, observed cost.

## 6. One-sentence interview answer you can use

When asked to defend a design, say:

“I look for the principle that removes the actual maintenance cost without adding a more expensive abstraction. If two code paths share a rule, I unify it. If they only look similar but have different policies, I keep them separate. If the design is only adding flexibility for a hypothetical future, I postpone it unless the need is proven.”
