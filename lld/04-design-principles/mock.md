# Design Principles — Mock Interview Q&A

This file is meant to sound like a practical engineering interview. The goal is not to memorize slogans but to explain trade-offs and choose the least costly design.

## 1. What is DRY, and why is it easy to misuse?

### Answer

DRY means there should be one authoritative representation of a fact or business rule. The goal is to avoid copying the same decision across many places because that creates drift and makes bug fixes inconsistent.

The common mistake is applying DRY to code that merely looks similar. If two paths share a formula but have different rounding policies, they are not the same rule. A helper that hides the difference behind conditionals creates a more complex abstraction than the original code did.

The right question is: is this the same knowledge, or just the same shape? If the same fact is repeated in multiple places, unify it. If the behavior differs by policy, keep the policy explicit.

## 2. When is KISS more important than a more “elegant” abstraction?

### Answer

KISS becomes more important when the abstraction adds conceptual overhead without removing enough duplication or risk. A design that looks elegant but requires ten new classes and a large amount of indirection can be harder to understand than the direct code.

The simplest design is not the one with the fewest lines; it is the one with the least mental overhead while still preserving correct behavior and expected change. If a team cannot explain the structure in a few paragraphs, the abstraction may be doing more harm than good.

A strong design answer is: “I prefer the smallest design that still makes the invariant and variation obvious.”

## 3. What does YAGNI mean in practice?

### Answer

YAGNI means do not build a speculative feature or a whole extensibility framework for a requirement that has not been proven. The design should still be easy to adapt, but the team should not spend time implementing a complex system that nobody has asked for.

For example, if there is only one payment provider today, it is poor design to build a plugin registry, dynamic provider lookup, and configuration layer “for future providers.” That is speculative design. If a second provider or requirement appears later, then the abstraction becomes justified.

The real principle is: build the next real need, not a hypothetical future.

## 4. When would you choose composition over inheritance?

### Answer

I choose composition when the relationship is not really a subtype relationship. Inheritance is for “is-a” semantics; composition is for “has-a” or “uses-a” semantics.

A classic example is a billing engine where pricing rules depend on region, currency, and payment method. If we create a class per combination, we get a combinatorial explosion and brittle hierarchy. Better is to compose separate policies: one for pricing, one for validation, one for region rules.

Inheritance is still appropriate when there is a genuine shared contract and the subclasses truly behave like the parent under the parent’s rules.

## 5. Explain the Law of Demeter in plain English.

### Answer

The Law of Demeter says a class should talk to its immediate collaborators rather than walk through a long chain of objects to reach data or behavior it doesn’t own.

Example:

```java
if (order.getCustomer().getAddress().getCountry().getCode().equals("US")) { ... }
```

This couples the caller to multiple internal object graphs. A better design is to ask the `Order` object a domain-level question:

```java
if (order.isDomestic()) { ... }
```

This is better because it expresses intent and reduces knowledge leakage. A wrapper method is useful only when it clarifies domain meaning; a reteaching of getters without meaning is just shallow indirection.

## 6. How do these principles conflict with each other?

### Answer

They often do, and that is expected. That is why we should think in terms of trade-offs, not strict doctrine.

For example:
- DRY may push us to extract a shared helper, but KISS may say the actual rules are different and should stay separate.
- YAGNI may discourage a plugin system, but a well-justified extension point can make future work cheaper.
- Composition can improve flexibility, but inheritance can be clearer when there is a real subtype hierarchy.

The right answer is not “always choose the principle with the most popular slogan.” It is to evaluate the maintenance cost and the actual change pressure. If a principle creates more complexity than it removes, it is not the right design.

## 7. How would you defend a design decision in an interview?

### Answer

I would explain the actual trade-off in terms of maintenance cost and domain meaning. For example:

- “I did not extract this helper because the formulas are the same only superficially; the rounding policy differs, so unifying them would hide a real business decision.”
- “I used composition here because the variation is across independent dimensions, and a subclass tree would explode combinatorially.”
- “I kept this extension point small because the requirement is real, but I did not add the full plugin framework because no second implementation exists yet.”

Strong interview answers tie the design choice to concrete costs: duplication, coupling, changing requirements, or subclass explosion.

## 8. What is a good interview summary of the design principles?

### Answer

The principles are guardrails for reducing accidental complexity, not rigid laws. DRY prevents repeated facts, KISS keeps the design understandable, YAGNI avoids speculative build-up, composition handles real variation without brittle inheritance, and Law of Demeter reduces coupling through object graph knowledge.

A good senior-engineering answer is this:

“I look for the simplest design that preserves the invariant and reduces future maintenance cost. I do not add abstractions for their own sake, and I do not avoid abstractions when they truly remove duplication or dangerous coupling.”
