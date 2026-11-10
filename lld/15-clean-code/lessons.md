# Clean Code for LLD — Lesson Notes

- **Naming:** Name types, functions, and variables after domain meaning and observable behavior. Avoid misleading abbreviations and vague names such as `Manager` when a precise role exists.
- **Function design:** Prefer focused operations with clear inputs, outputs, and side effects. Keep branching and hidden state manageable; extract helpers when they name a meaningful concept.
- **Class size and responsibility:** Size is not a line-count rule. Split when responsibilities change for different reasons or the public contract becomes hard to understand.
- **God objects:** A class that knows or controls too much becomes a bottleneck for change and testing. Move cohesive behavior toward its domain owner or collaborator.
- **Readability:** Make the normal path obvious; use consistent structure and explicit error behavior. Avoid cleverness that saves lines at the cost of comprehension.
- **Comments:** Explain rationale, constraints, or non-obvious decisions; do not restate code. Keep comments updated when behavior changes.
- **Testing and testability:** Unit tests verify meaningful behavior and invariants. Inject external effects or use seams where isolation matters; do not distort a design solely to make trivial implementation details mockable.
