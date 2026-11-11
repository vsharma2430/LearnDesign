# SOLID Principles — Real-World Mock Interview

These prompts use the kinds of change requests, code-review discussions, and follow-up questions that come up in software engineering interviews. Treat the sample answers as reasoning patterns, not scripts to memorize. In an interview, clarify assumptions, describe the smallest useful design change, and explain how you would verify it.

## 1. A checkout service owns too much. How would you refactor it?

### Interviewer

You are reviewing a `CheckoutService`. It validates a cart, calculates tax, charges a payment provider, writes an order to the database, and sends a confirmation email. The team often changes tax rules and recently added a second payment provider. What concerns do you see, and how would you approach the refactor?

### Strong answer

I would first confirm the change patterns and failure behavior rather than split the class just because it has several methods. The code has several likely axes of change: checkout policy, tax calculation, payment integration, order persistence, and notification delivery.

I would keep the checkout workflow responsible for coordinating the use case, then move out only the behavior that changes independently. For example, it could depend on a `TaxCalculator`, a `PaymentGateway`, an `OrderRepository`, and a `ConfirmationSender`. Those names describe what the workflow needs, without exposing provider or database details.

I would also check sequencing and failure semantics before moving code. For example, if charging succeeds but saving the order fails, a refactor must not make that existing risk worse. I would add or preserve tests around the workflow, move one seam at a time, and avoid introducing abstractions for behavior that has no real variation.

### Follow-up: Is this just “one class per verb”?

No. SRP is about grouping behavior around a coherent responsibility or reason to change, not minimizing class size. A small cohesive checkout workflow can have several steps. The question is whether unrelated changes repeatedly force edits to the same module.

## 2. A new payment provider means editing a growing `switch`. What would you do?

### Interviewer

The payment code has a `switch` on provider type. Every new provider adds another branch, and each provider has different request formats and error codes. How would you improve it?

### Strong answer

If providers are genuinely being added and the branches contain provider-specific behavior, I would define the capability checkout needs, such as `PaymentGateway.authorize(...)`, and implement it for each provider. A registry or dependency-injection configuration can select the implementation. Provider-specific request mapping and error translation stay inside the adapter; checkout depends on a stable result and error contract.

I would keep the selection mechanism explicit and validate its configuration at startup. I would not assume a strategy pattern removes all change: registering a provider, configuring credentials, handling new error semantics, and adding tests still require work. The benefit is containing provider-specific changes instead of spreading them through checkout.

If there are only two stable cases with trivial differences, the current `switch` may be clearer. I would use the smallest seam justified by expected change.

### Follow-up: How would you add a provider safely?

I would implement its adapter, test request mapping and error translation, verify timeout and retry behavior against the provider contract, and exercise the checkout flow with a fake gateway. I would roll it out behind configuration or a gradual enablement mechanism if the provider is being introduced to production traffic.

## 3. A “read-only” subtype throws from a mutating method. Is that an LSP problem?

### Interviewer

An API accepts a `MutableAccount` and calls `updateAddress`. A `ReadOnlyAccount` inherits from it but throws whenever `updateAddress` is called. Is that design safe?

### Strong answer

Probably not. A caller that accepts `MutableAccount` can reasonably rely on its documented update behavior. A subtype that rejects a valid operation strengthens the caller's preconditions and is not a safe substitute.

I would separate the contracts based on what clients need. For example, an `AccountView` could expose reads, and an `EditableAccount` could expose updates. The API that updates an address should request the editable capability rather than a broader base type that some implementations cannot honor.

I would also inspect less obvious contract changes: a subtype that returns stale data, silently ignores updates, or throws a new category of exception can violate substitutability even if the method signatures match.

### Follow-up: Does LSP mean subclasses can never throw?

No. They must honor the documented contract, including its failure conditions. A subtype can fail for reasons allowed by that contract; it should not surprise callers by rejecting inputs or operations the base type promises to support.

## 4. A shared interface has dozens of methods, many irrelevant to implementers. What is your response?

### Interviewer

Several implementations of `NotificationClient` have empty methods or throw `UnsupportedOperationException`. The interface includes email, SMS, push, and attachment handling. What would you change?

### Strong answer

I would look at how clients use the interface and split it into focused capabilities where those differences matter. For example, a `MessageSender` might accept a message, while attachment support could be represented separately if only some channels support it. An email-specific client could depend on an email capability rather than methods it cannot use.

I would not create one tiny interface for every method by default. Interfaces should reflect useful client needs, not just the current class layout. I would update consumers to depend on the smallest capability they actually need and keep channel-specific constraints visible, rather than pretending all channels support identical behavior.

## 5. A domain service constructs a vendor SDK directly. How would you apply DIP?

### Interviewer

`SubscriptionService` imports a cloud billing SDK, constructs its client, and calls the vendor's `createCharge` method. The team wants to test renewal behavior and may change providers. What would you do?

### Strong answer

I would identify the operation the subscription policy needs and define a narrow boundary around it, such as `BillingGateway.chargeRenewal(...)`. `SubscriptionService` would depend on that capability; an infrastructure adapter would translate the domain request and vendor response.

This is dependency inversion because the high-level renewal policy no longer depends directly on low-level SDK details. The interface should be shaped by the policy's needs, not copy the SDK's entire surface. A test fake can verify decisions such as whether a failed renewal schedules another attempt.

I would not add an abstraction solely to claim that the code follows DIP. If there is no meaningful policy/infrastructure boundary or likely testing/change benefit, the extra indirection may not be worthwhile.

## 6. A report change keeps breaking unrelated code. Where would you look for SRP issues?

### Interviewer

Whenever the business changes a report column, the team edits a class that also builds SQL queries and sends the report. How would you diagnose the design?

### Strong answer

I would trace which code changes together and why. Report content or formatting, data retrieval, and delivery are plausible separate responsibilities because they respond to different requests. I might separate a report query, a formatter, and a delivery mechanism, while keeping the report use case responsible for coordinating them.

I would check whether these changes actually occur independently. If the class is small, stable, and easy to test, splitting it may add navigation and coordination cost without reducing change risk. SRP is a way to reason about change boundaries, not a requirement that every technical step have its own class.

## 7. A team wants every conditional removed to satisfy OCP. Do you agree?

### Interviewer

In code review, someone says every `if` or `switch` violates OCP and proposes a plugin framework for a fixed set of three discount rules. How would you respond?

### Strong answer

I would ask what is expected to vary, how often new rules are added, and who owns them. A conditional over a small, stable set may be the simplest and most readable design. OCP is useful when recurring variation makes edits to stable, well-tested code risky; it is not a ban on modifying existing code.

If rules are frequently added or independently owned, I would consider isolating each rule behind a focused contract and composing them. I would compare that against the costs of discovery, ordering, configuration, and debugging. The design should make the likely change safer, not merely make a change possible in theory.

## 8. How would you apply SOLID to a legacy service without a rewrite?

### Interviewer

You inherit a large order service with weak test coverage. A new tax rule is due next sprint. How do you improve the design without taking on a risky rewrite?

### Strong answer

I would start with the requested tax change and map the path through the existing code. Before refactoring, I would add characterization tests for the behavior we must preserve, especially boundary cases and existing failure handling. Then I would extract or introduce the smallest tax-calculation seam needed for the new rule.

I would keep the public behavior stable, make the change in small reviewable steps, and test the order flow as well as the new tax cases. If surrounding code is hard to isolate, I would avoid broad cleanup and limit the change to the path required. SOLID can guide incremental seams; it does not require redesigning the whole service before delivering a feature.

## 9. How do you decide whether an abstraction is worth keeping?

### Interviewer

The team has introduced several interfaces with only one implementation. A reviewer argues that single-implementation interfaces are always pointless; another says they are always necessary for testing. How do you decide?

### Strong answer

The implementation count alone does not decide it. I would ask whether the abstraction captures a meaningful policy boundary, isolates volatile infrastructure, gives a consumer a useful contract, or makes an important behavior easy to test. If it only mirrors the concrete class one-to-one and has no independent value, it may be noise.

Likewise, tests do not always need a mockable interface; a real lightweight implementation or a higher-level test may be better. I would optimize for a clear seam and reliable tests, not for maximizing interfaces or mocks.

## 10. How would you explain SOLID trade-offs in a design review?

### Interviewer

You proposed splitting a service into several components. A teammate says the design is over-engineered. How do you defend or revise it?

### Strong answer

I would connect each boundary to a concrete change or risk, and be open to removing boundaries that do not pay for themselves. For example: “Provider-specific failures have already caused changes in the checkout workflow, so this adapter keeps that translation out of the business policy.” I would also explain what remains coupled and why that is acceptable.

Then I would compare the costs: extra types and indirection versus fewer unrelated edits, clearer contracts, or safer tests. If the expected variation is speculative and the simpler design is easy to change later, I would defer the abstraction. SOLID helps structure that discussion; it is not a checklist that proves a design is good.

## Interview answer checklist

- Clarify the change request, constraints, and current failure behavior.
- Identify the specific responsibility, contract, client dependency, or policy boundary at issue.
- Propose the smallest change that addresses the observed problem.
- Explain trade-offs and when the simpler design is preferable.
- Mention how you would test behavior and preserve compatibility during rollout.
