# Behavioral Patterns — Lesson Notes

- **Strategy:** Encapsulates interchangeable algorithms behind one contract; the caller or configuration selects one.
- **Observer:** Notifies subscribers when a subject changes. Define subscription lifetime, ordering expectations, and how listener failures are handled.
- **Command:** Represents a request as an object or value, enabling queuing, logging, or undo. Keep command state and execution semantics explicit.
- **Chain of Responsibility:** Passes a request through handlers until handled or exhausted. Define order, termination, and whether multiple handlers may act.
- **State:** Moves state-dependent behavior into state objects or an explicit state machine. Make valid transitions visible; avoid class proliferation for trivial states.
- **Template Method:** A base algorithm defines fixed steps and subclasses customize selected hooks. Ensure subclasses can honor the algorithm's contract.
- **Iterator:** Traverses a collection without exposing its representation. Define mutation behavior during iteration.
- **Mediator:** Centralizes coordination among collaborating objects to reduce direct references. Avoid creating a God mediator that owns all behavior.
- **Visitor:** Adds operations over a stable set of element types using double dispatch. New element types can make visitors costly to update.
- **Memento:** Captures state for restoration while controlling access to internals. Consider snapshot size, consistency, and versioning.
