# Dependency Management and Extensibility — Practice

1. A `ReceiptService` constructs an SMTP client internally. Refactor its dependencies for tests and explain where wiring belongs.
2. Which dependencies should be constructor-injected? Identify one plausible optional setter dependency and its lifecycle.
3. Compare manual wiring and a DI framework for a small command-line program. What change in scale might alter your choice?
4. Design a payment-provider seam for one current provider and a likely second provider. Avoid abstractions unrelated to provider variation.
5. Find a hidden dependency caused by a global service locator. Show how an explicit contract improves the caller.
