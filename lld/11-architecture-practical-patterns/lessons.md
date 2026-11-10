# Architectural and Practical Patterns — Lesson Notes

- **MVC:** Separates model/domain state, view presentation, and controller input coordination. Keep business policy out of presentation glue.
- **MVVM:** Uses a view model to expose presentation-ready state and actions to a view. Useful when UI state and binding logic need isolation.
- **Layered architecture:** Organizes presentation, application, domain, and infrastructure concerns. Dependencies should point toward stable policy where possible; avoid layers that only forward calls.
- **Repository:** Presents a domain-oriented collection or persistence boundary. Keep storage queries behind it without making the repository a generic dumping ground.
- **Specification:** Encapsulates a named, composable eligibility or selection rule. Avoid complex boolean frameworks when a simple predicate is clearer.
- **Middleware:** Runs reusable processing around a request pipeline, such as authentication or tracing. Define ordering, short-circuiting, and error behavior.
- **Request context and propagation:** Carries request-scoped identity, cancellation, deadlines, or trace metadata. Pass it explicitly or via a carefully scoped framework mechanism; do not turn it into an unbounded global bag.
- **Controllers, handlers, and services:** Controllers adapt transport input; handlers coordinate a use case; services host application/domain operations as appropriate. Names vary by architecture—focus on responsibility rather than labels.
- **Business Logic Layer (BLL):** Houses application rules and workflows. Keep it independent of transport details and avoid duplicating invariants already owned by domain objects.
- **Unit of Work:** Groups persistence changes into one commit boundary. Be clear about transaction scope, concurrency conflicts, and rollback behavior.
