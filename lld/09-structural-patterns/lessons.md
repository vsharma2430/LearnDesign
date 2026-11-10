# Structural Patterns — Lesson Notes

- **Adapter:** Converts one interface into another expected by a client. Isolate a legacy or third-party API behind the application's contract.
- **Bridge:** Separates two dimensions of variation so each can evolve independently, such as shape abstractions and rendering backends. Prefer it when subclass combinations would multiply.
- **Composite:** Gives leaf objects and groups a common tree interface. Useful for recursive structures; be careful not to expose meaningless operations on leaves.
- **Decorator:** Wraps an object to add behavior while preserving its interface. Wrappers can be composed, but order may affect behavior.
- **Facade:** Provides a focused entry point over a complex subsystem. It simplifies clients without necessarily restricting direct subsystem use.
- **Flyweight:** Shares intrinsic, stable data across many logical objects while keeping per-instance extrinsic state separate. Requires careful identity and mutability boundaries.
- **Proxy:** Stands in for another object to control access, such as lazy loading, remote access, or authorization. Keep proxy behavior consistent with the subject contract.
