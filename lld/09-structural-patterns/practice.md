# Structural Patterns — Practice Q&A

Try each prompt before reading its answer. The answers are examples; explain your assumptions when a design could vary.

## 1. Adapt a vendor payment SDK

**Question:** A vendor SDK returns a different payment API than your domain expects. Sketch an Adapter boundary.

**Answer:** Define an application-owned payment gateway contract using domain-level request and result types. Implement it with a vendor adapter that maps requests, translates vendor errors into meaningful application errors, and handles vendor-specific lifecycle details. Domain and application code depend on the gateway, not the SDK. Test mapping and failure cases without requiring the vendor implementation for every unit test.

## 2. Composite document

**Question:** A document contains paragraphs and nested sections that can all render. Which pattern fits? Where should child ordering live?

**Answer:** Composite fits because paragraphs and sections can share a renderable component interface and sections recursively contain components. Child ordering belongs to the section/document model that owns the children, not to an unrelated renderer unless ordering is specifically a rendering concern. The API should prevent paragraphs from accepting children and should define whether a section may be empty or cyclic.

## 3. Add timing and retry

**Question:** Add timing and retry behavior around a service call. Compare a Decorator with editing the service directly.

**Answer:** A decorator can wrap the service contract and add timing or retry behavior without changing the service implementation. Composition makes the behavior independently testable. Retry is not automatically safe: it needs idempotency, retryable-error classification, limits, and backoff. Directly editing the service may be simpler if the behavior is inseparable and local; decorator ordering and exception semantics should be explicit.

## 4. Flyweight media data

**Question:** A media library has many files that share large metadata but have distinct playback positions. Identify flyweight and per-object state.

**Answer:** Stable shared metadata such as codec descriptors or immutable artwork references could be intrinsic flyweight state if many files truly share it. A file's playback position is extrinsic per-use state and must remain with the playback instance, not in the shared flyweight. The design should also clarify whether filenames or other supposedly shared metadata are actually unique.

## 5. Facade for a subsystem

**Question:** A complex subsystem requires seven setup calls. Design a Facade method and explain what it should not absorb.

**Answer:** A facade could expose a use-case method such as `createReport(request)` that validates subsystem-level inputs, coordinates the seven collaborators in the correct order, and returns a focused result. It should not accumulate unrelated business policy, expose every low-level option without purpose, or own unrelated lifecycle. The facade can hide orchestration while leaving specialized subsystem APIs available where the architecture allows.

## 6. Authorization proxy

**Question:** A proxy checks authorization. How will it preserve the subject's observable contract and report denial?

**Answer:** It should implement the same contract and return the established authorization failure representation, without invoking the real subject on denial. It should fail closed if identity or policy evaluation is unavailable, unless the explicit policy says otherwise. It must ensure cache hits cannot bypass authorization and should document any relevant side effects or latency introduced by the check.

## 7. Adapter or Decorator?

**Question:** A third-party logger uses different method names and also needs timestamps added to every message. Which patterns could help?

**Answer:** An Adapter translates the third-party logger interface into the application's logging contract. A Decorator can then add timestamps while preserving that application contract. They solve different problems and may be composed, but a small implementation could combine them if separating them adds no useful test or change boundary.

## 8. Bridge or many subclasses?

**Question:** A drawing application has many shapes and rendering backends. How can you avoid a subclass for every combination?

**Answer:** Bridge separates shape abstractions from rendering implementations. Each shape holds or delegates to a renderer, so new shapes and backends can be added independently. This is worth doing if both dimensions are expected to vary. If there are only two fixed combinations, direct implementations may be simpler.
