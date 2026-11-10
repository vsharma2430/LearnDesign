# Creational Design Patterns — Lesson Notes

- **Singleton:** One shared instance with a controlled access point. It can simplify genuinely process-wide immutable services, but often hides dependencies and complicates tests. Prefer explicit dependency injection when practical.
- **Thread-safe Singleton:** Initialization must be safe under concurrent access. Use the language's well-defined lazy initialization or eager static initialization; avoid hand-rolled double-checked locking unless memory-ordering semantics are understood.
- **Factory Method:** A creator delegates selection/creation of a product to a method or subtype. Useful when subclasses or a registry vary the product; a simple constructor may be enough otherwise.
- **Abstract Factory:** Creates a compatible family of related products without exposing concrete types, such as widgets for one UI theme. It is useful when families must stay consistent.
- **Builder:** Constructs an object step by step, especially when many optional parameters or validation rules make constructors unwieldy. The finished object should still enforce its invariants.
- **Prototype:** Creates an object by copying a configured exemplar. Clarify shallow versus deep copy and ownership of nested mutable state.
- **Object Pool:** Reuses a bounded set of expensive resources. It introduces acquisition, release, exhaustion, and cleanup rules; use it only when measurement or resource constraints justify it.

Pattern names describe intent, not a requirement to add a factory class. Creation should remain discoverable, deterministic, and testable.
