# Design Principles — Real-World Mock Interview

These scenarios focus on judgment under realistic change pressure: existing behavior, deadlines, production risk, and incomplete requirements. Use the sample answers to practice explaining the decision and its trade-offs, not to memorize a single “correct” design.

## 1. Two similar calculations produce different customer totals. Would you deduplicate them?

### Interviewer

The checkout and invoice services both calculate tax. The code looks nearly identical, but checkout rounds per line item while invoicing rounds once at the invoice total. A bug fix is needed in both paths. Would you extract a shared tax helper?

### Strong answer

First I would confirm the tax and rounding rules with the domain owner and add examples for the cases where the totals diverge. The important distinction is not code shape; it is whether both paths represent the same business rule.

If the tax rate lookup or another rule is genuinely shared, I would centralize that knowledge while keeping the rounding policy explicit. I would not hide both behaviors in a helper full of flags or branching just to eliminate similar lines. That would make a future change harder to reason about and could silently change customer totals.

If the underlying rule really is identical and only duplicated by accident, I would extract it and update both paths in one tested change. DRY is about one authoritative source for the same knowledge, not one implementation for superficially similar code.

### Follow-up: The deadline allows changing only one path. What do you do?

I would make the smallest safe change to the affected path, add a regression test, and record the duplicate for follow-up. Forcing a broad refactor into a constrained fix increases risk without necessarily addressing the immediate defect.

## 2. A generic rules engine is proposed for a three-rule discount feature. Is that KISS or YAGNI?

### Interviewer

The current discount feature has three rules and one team maintaining it. A proposal adds a rule DSL, database configuration, plugin loading, and a rules dashboard because “discounts will grow.” What would you recommend?

### Strong answer

I would ask what change is actually expected: how often rules change, who needs to change them, whether non-engineers must configure them, and what auditability is required. If the rules are stable and changed through normal releases, a straightforward, tested implementation is likely easier to maintain than a configurable platform.

If the business needs frequent rule changes by authorized operators, including history, approvals, or effective dates, a rules system may be justified. That is a concrete requirement, not merely a prediction that the code might grow.

KISS is not “fewest lines,” and YAGNI is not “never prepare for change.” I would implement the current needs with clear boundaries and defer the expensive framework until its operational value is established.

### Follow-up: How do you keep the simpler design adaptable?

Keep the rules readable and tests explicit. Avoid coupling unrelated checkout behavior to their implementation. That leaves a manageable seam for a later refactor without paying today for a framework we may not need.

## 3. A class hierarchy is growing with combinations of region, currency, and payment method. What would you change?

### Interviewer

There are now subclasses such as `EuCardPayment`, `UsCardPayment`, and `EuWalletPayment`. A new currency requires more classes, and some overrides duplicate behavior. How would you approach this?

### Strong answer

I would check whether these subclasses are true behavioral subtypes or are encoding combinations of independent dimensions. Region, currency, and payment method often vary independently; representing every combination as a class leads to a cross-product of types.

If that is the case, I would compose focused policies or collaborators for the dimensions that actually vary, and let a payment workflow coordinate them. I would keep combinations explicit where rules interact rather than assume the dimensions are always independent.

I would migrate incrementally: characterize current behavior with tests, select one well-understood path, introduce composition there, and compare the resulting complexity. Composition can reduce subclass growth, but excessive tiny objects and opaque wiring can be worse than a small hierarchy.

### Follow-up: When would you keep inheritance?

When there is a stable “is-a” relationship, callers can rely on a shared contract, and subclasses honor that contract without surprise. Inheritance is not inherently wrong; using it to encode every combination of unrelated options is the problem.

## 4. A refactor replaces a long object chain with a method that just forwards getters. Did it help?

### Interviewer

A service previously read `order.getCustomer().getAddress().getCountry().getCode()`. It now calls `order.countryCode()`, which simply returns the same nested field. Is this a useful application of the Law of Demeter?

### Strong answer

Not necessarily. The new method hides the traversal at the call site, but if it only forwards data and adds no meaningful contract, it may just move the coupling. I would ask what the caller is trying to decide.

If the business question is whether the order is domestic, an operation such as `order.isDomestic()` may express intent and keep the decision near the data it depends on. If several consumers need the country code as data, a well-defined value object or query boundary may be more appropriate.

The Law of Demeter is a guide to reducing unnecessary knowledge of collaborators, not a ban on navigation or a requirement to wrap every getter. A method should clarify ownership or intent, not merely conceal syntax.

## 5. A legacy service needs a production change next week. Do you refactor it first?

### Interviewer

The only code that handles refunds is a large service with little test coverage. A new partial-refund requirement is urgent. How do you balance delivery and design quality?

### Strong answer

I would first clarify the refund rules and failure cases, then trace the existing behavior and identify what must remain compatible. I would add focused characterization tests around the current refund path, including the financial boundaries and relevant failure behavior, before changing it.

I would then make the smallest refactor needed to implement the partial-refund rule safely. I would not rewrite the whole service or extract every responsibility under deadline pressure. The change should be reviewable, observable in production, and covered by tests for both the new behavior and existing full refunds.

If the tests expose a serious existing risk, I would surface it and discuss scope rather than silently expanding the change. Design principles should make delivery safer, not become a reason to block a necessary fix.

## 6. DRY and KISS point in different directions. How do you choose?

### Interviewer

Two validation flows have about 80% identical code. One serves consumer signups; the other serves business accounts. A shared helper would need several flags and callbacks. What would you do?

### Strong answer

I would identify what knowledge is shared and how independently the flows change. If the common code represents one invariant, I would extract that invariant behind a clear API and leave flow-specific policy at the call site. If the similarities are incidental and the helper needs many flags, keeping the flows separate may be simpler and safer.

I would also consider the cost of future fixes: duplicated invariant logic can drift, while a parameterized helper can obscure important differences. Tests should make each flow's behavior visible whichever design we choose.

The 80% figure alone does not determine the answer. I care more about whether the same rule has one source of truth and whether the abstraction makes the remaining variation easier to understand.

## 7. A team wants a plugin system because a second integration may arrive. What evidence would you ask for?

### Interviewer

There is one shipping provider. A proposed design adds a provider registry, dynamic loading, versioned plugin contracts, and per-tenant routing because another provider “might” be needed. How would you evaluate it?

### Strong answer

I would ask whether a second provider is committed, what differences it introduces, who configures provider selection, and whether tenants need different providers. If the requirement is only a possibility, I would avoid the dynamic plugin system. A narrow integration boundary can still keep provider details from spreading without building discovery and lifecycle machinery.

If multiple providers are a confirmed requirement, I would design the selection, configuration, failure semantics, and rollout behavior explicitly. A registry may be appropriate, but dynamic loading and per-tenant routing need separate justification.

YAGNI helps defer speculative capability; it does not mean making today's integration impossible to replace. The goal is to pay for the smallest seam that addresses real change pressure.

## 8. How do you defend a design when an interviewer challenges your abstraction?

### Interviewer

You split a service into several collaborators. The interviewer says it is over-engineered and asks why it could not stay as one class. How do you respond?

### Strong answer

I would acknowledge the cost of the extra types, then connect each boundary to a concrete need: a different change owner, an independently tested policy, volatile infrastructure, or a failure mode that must be isolated. I would explain what simpler alternative I considered and why it was less suitable for the stated constraints.

I would also be willing to revise the design. If a collaborator has one trivial implementation, no independent contract, and no meaningful testing or change benefit, I would consider folding it back in. The number of abstractions is not a measure of design quality.

A concise answer is: “I chose this boundary because it reduces a specific maintenance or operational risk. If that risk is not expected, the simpler design is preferable.”

## Interview answer checklist

- Clarify the requirement and what is already true in production.
- Separate shared business knowledge from code that only looks similar.
- Propose the smallest change that addresses an observed cost or risk.
- Explain the trade-off, including migration and failure behavior when relevant.
- State what evidence would make you choose the simpler or more extensible design.
