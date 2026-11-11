# Structural Patterns — Mock Interview Q&A

For each answer, explain what relationship changes, what the client sees, and what new constraint the pattern introduces.

## 1. Adapter versus Decorator?

**Answer:** Adapter changes or translates an interface so a client can use an otherwise incompatible collaborator. Decorator preserves the component interface and adds behavior around an implementation. An adapter absorbs differences such as vendor-specific types and errors; a decorator can add logging, caching, or compression. A design may use both if each addresses a distinct boundary.

## 2. Adapter versus Proxy?

**Answer:** Both may delegate to another object, but Adapter's purpose is compatibility between interfaces, while Proxy's purpose is controlling access to a subject through a compatible interface. A remote proxy may preserve a subject-like contract while adding network access; an adapter may translate that remote API into the application's contract. I would name the roles by intent rather than by whether delegation happens.

## 3. When would you introduce Bridge?

**Answer:** I would use Bridge when there are two independent dimensions of variation and a subclass for every combination would grow or entangle changes. Separating a shape abstraction from a rendering backend lets both evolve independently. It adds a delegation boundary, so I would avoid it for a small fixed matrix where direct classes remain clearer.

## 4. What makes Composite useful?

**Answer:** Composite lets clients treat leaves and nested groups through a shared operation, which is useful for recursive structures such as menus or document trees. The design must define which operations are valid for leaves and groups, who owns child ordering, and whether cycles are possible. I would avoid an overly broad component interface that exposes meaningless mutation methods on leaves.

## 5. What can go wrong with Decorator composition?

**Answer:** Wrapping order can change semantics, such as whether authorization occurs before cache lookup or retry wraps metrics. Wrappers can also alter exception propagation, timing, resource ownership, or observability. I would make composition order visible, define the contract, and test combinations where order affects correctness rather than testing only each wrapper in isolation.

## 6. When does a Facade become a God object?

**Answer:** A facade becomes too broad when it accumulates unrelated business responsibilities, owns decisions that belong elsewhere, or exposes a single entry point for every subsystem operation. A good facade offers a focused subsystem or use-case boundary and delegates domain policy to its proper owner. If it keeps growing, I would split it by cohesive operation or responsibility.

## 7. Explain intrinsic and extrinsic state in Flyweight.

**Answer:** Intrinsic state is stable and shareable across logical instances; extrinsic state varies per occurrence and is supplied externally. For glyph rendering, immutable glyph shape data can be shared, while position and selection belong to each occurrence. Sharing mutable per-occurrence data causes cross-instance bugs. I would measure whether sharing is valuable and consider cache lifetime and memory retention.

## 8. What contract concerns arise with a remote Proxy?

**Answer:** A remote proxy introduces latency, timeouts, partial failure, serialization limits, and possibly retries. A local-looking method can mislead callers if those distributed failure modes are omitted from the contract. I would expose appropriate cancellation and error behavior, define retry/idempotency rules, and be explicit about resource and ownership semantics.

## 9. How do you keep authorization correct with a Proxy?

**Answer:** The authorization check must run before protected work, fail closed on indeterminate policy results, and represent denial through the contract's standard error. Any cache must be scoped by identity or checked after authorization so cached data cannot bypass access control. I would test allowed, denied, policy failure, and cache-hit paths.

## 10. How do you select among structural patterns?

**Answer:** I identify the client's problem: incompatible interface suggests Adapter; added behavior with the same contract suggests Decorator; controlled access suggests Proxy; a simplified subsystem entry point suggests Facade; independent abstraction and implementation dimensions suggest Bridge; recursive uniform composition suggests Composite; large repeated stable state suggests Flyweight. Then I compare lifecycle, mutability, and indirection costs with a direct design.
