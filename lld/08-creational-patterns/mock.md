# Creational Patterns — Mock Interview Q&A

Strong answers explain the pressure, the chosen creation boundary, and the trade-offs. Use these as practice prompts before reviewing the sample answers.

## 1. How do you decide whether a factory is needed?

**Answer:** I first ask whether construction or product selection is genuinely variable, complex, or owned by a different boundary. If the caller knows the concrete type and a constructor expresses the intent, I keep it direct. A factory is useful when it centralizes a decision, hides concrete products behind a contract, or enables a workflow to vary product creation. I would also consider whether the factory adds more indirection than it removes.

## 2. Factory Method versus Abstract Factory?

**Answer:** Factory Method focuses on a creation operation that varies a product choice, often as part of a creator's workflow. Abstract Factory exposes creation operations for a family of related products that should be used together. For example, selecting a parser may need one product choice; selecting a complete UI theme with compatible buttons and menus is a family choice. I would not use Abstract Factory when product types vary independently.

## 3. Why might dependency injection be preferable to Singleton?

**Answer:** Dependency injection makes ownership and dependencies visible in constructors or configuration, and allows tests to supply controlled instances. The composition root can still ensure that all consumers share one instance. A Singleton can be justified for an intentionally global identity, but global access hides dependencies and can make mutable state, concurrency, and test isolation harder.

## 4. What is the difference between Singleton thread safety and service thread safety?

**Answer:** Singleton thread safety may mean that concurrent first access creates and publishes only one valid instance. It does not mean concurrent operations on that instance are safe. If the service has mutable state, its operations need their own synchronization or concurrency design. I would test both initialization and concurrent behavior where required.

## 5. When does Builder improve a design?

**Answer:** Builder helps when there are many optional values, staged construction, or invariants that are difficult to express in a constructor call. It can make call sites readable and gather validation before producing a valid object. I would avoid it for simple objects with a few obvious values, and ensure the built object cannot be left invalid or share unintended mutable builder state.

## 6. Shallow copy versus deep copy: how do you choose?

**Answer:** I define the intended ownership of nested state. A shallow copy is appropriate when referenced objects are immutable or intentionally shared. A deep copy is needed when mutable parts must be independent, but it can be expensive and cannot safely duplicate every resource. I document which parts are copied, shared, or reset, and test those expectations.

## 7. What problems does Object Pool introduce?

**Answer:** A pool needs explicit acquisition and release semantics, capacity and exhaustion behavior, cleanup after failure, state reset, health checking, and shutdown rules. It can also add contention and resource leaks. I would only introduce one after measuring that resource creation or external capacity is a real constraint and would test the pool's lifecycle under cancellation and failure.

## 8. A registry-based factory accepts string keys. What would you guard against?

**Answer:** I would define the key type and normalization, reject duplicate registrations, make unknown keys an explicit error, and decide when registration is allowed. I would avoid silent fallback to an unrelated product because it can conceal configuration errors. If the set is closed and small, a typed conditional or enum-based mapping may be safer and simpler.

## 9. How would you make product-family mismatches impossible?

**Answer:** Select one Abstract Factory at the composition boundary and pass it to the client that needs the family. The client should request all related products through that same factory rather than choosing concrete classes independently. Tests can verify each concrete family and the factory's returned products. If compile-time typing can encode family compatibility in the language, I would consider it without overcomplicating the public API.

## 10. When is a creation pattern over-engineering?

**Answer:** It is over-engineering when the added types, indirection, configuration, or lifecycle rules do not address a current requirement. I compare the pattern with a constructor, function, or direct conditional and ask whether changes are actually localized or tests meaningfully improved. I would start simple and extract the creation boundary when evidence shows that selection or construction is becoming a source of change cost.
