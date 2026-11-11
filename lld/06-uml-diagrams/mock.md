# UML Diagrams — Mock Interview Q&A

This file is designed for interview-style practice. The goal is not to memorize UML syntax, but to explain which diagram is appropriate, what it shows, and what it intentionally leaves out.

## 1. What is the main purpose of UML?

### Answer

UML is a modeling language used to communicate structure, behavior, and system boundaries. It helps a team reason about design without writing code too early. The important part is that UML is not a single diagram. It is a family of views, and each view answers a different design question.

In interviews, a strong answer is: “UML helps us visualize the design at the right level of abstraction. We choose the diagram based on the question we are trying to answer.”

## 2. When would you choose a class diagram over a sequence diagram?

### Answer

I would choose a class diagram when the question is about structure, responsibilities, and relationships between entities. A sequence diagram is better when the question is about behavior over time.

For example:

- class diagram: “What are the main types and how do they relate?”
- sequence diagram: “What happens when a customer places an order?”

A class diagram is usually the starting point when the system is not yet clear. A sequence diagram is the next step when behavior is ambiguous.

## 3. What is the difference between association, aggregation, and composition?

### Answer

These are all relationship types, but they imply different ownership and lifecycle semantics.

- Association: a general relationship between two types, such as a customer and an order
- Aggregation: a whole-part relationship where the part can exist independently, like a department and an employee
- Composition: a stronger ownership relationship where the child cannot meaningfully exist without the parent, like an order and its order lines

In interviews, I would explain that composition implies stronger lifecycle coupling. If the parent is removed, the children likely disappear too.

## 4. Why is a use case diagram useful if it does not show details of implementation?

### Answer

A use case diagram is useful to clarify scope. It answers questions such as:

- who interacts with the system?
- what are the major goals of the actors?
- what capabilities need to be supported?

It is not a substitute for detailed flow modeling. It gives the “who and what,” while a sequence diagram gives the “how and in what order.”

## 5. What does a sequence diagram reveal that a class diagram does not?

### Answer

A sequence diagram shows the order of interactions over time. It exposes message passing, decision branches, and failure paths.

For example, in an order flow, a class diagram might show `Customer`, `Order`, and `Payment`, but it would not tell us whether payment is authorized before inventory reservation or after. A sequence diagram clarifies the scenario order and reveals important control flow.

## 6. When is an activity diagram better than a sequence diagram?

### Answer

An activity diagram is better when the main concern is workflow logic, decisions, and branching. It is good for approval processes, ticket workflows, and multi-step business operations.

A sequence diagram is better when the concern is object interaction and message order.

For example:

- activity diagram: “If approved by manager, go to finance; else reject”
- sequence diagram: “Employee sends request to Manager, Manager sends approval to Finance”

The right choice depends on whether the design question is about process flow or interaction choreography.

## 7. What is the purpose of an object diagram?

### Answer

An object diagram is a concrete snapshot of a system at a specific point in time. It shows actual objects and their values, which is useful when a general model is too abstract or a real example needs to be explained.

For example, instead of saying “there are many order lines,” an object diagram can show one `Order` with two specific `OrderLine` instances and their quantities. This helps make the relationship concrete.

## 8. How would you explain a state diagram in plain English?

### Answer

A state diagram describes the lifecycle of one object or entity. It shows the possible states, the events that trigger transitions, and which transitions are allowed.

A strong example is an order lifecycle: `Created -> Confirmed -> Shipped -> Delivered`. A state diagram makes invalid transitions explicit. If the object cannot move directly from `Created` to `Delivered`, the diagram reveals that the transition is not legal unless a rule or guard permits it.

## 9. What makes an invalid transition invalid?

### Answer

An invalid transition is invalid because the system rules do not allow it. There are several reasons:

- the state machine does not define that transition
- a guard blocks it
- the event does not apply in that state
- the transition would violate business constraints

For example, a payment may not move from `Pending` to `Paid` unless authorization succeeds. If payment fails, the transition is blocked by a guard. In interviews, it is important to explain that invalid transitions are not just “not drawn”; they are blocked by rules.

## 10. Why do component diagrams matter if they are not about runtime behavior?

### Answer

Component diagrams matter because they show architectural boundaries and dependency direction. They help answer questions like:

- which modules or services exist?
- what interfaces do they expose?
- what dependencies are allowed?

This is especially important in large systems where the real risk is not the method body but the coupling between modules. A component diagram can show that the order service depends on inventory and payment interfaces, without needing to show every message in detail.

## 11. How do you decide what to omit in a UML diagram?

### Answer

I omit details that do not answer the question being asked. A diagram should be as simple as possible while still proving the design point.

For example:

- in a class diagram, I do not show every helper method unless it matters
- in a sequence diagram, I do not show every internal call unless the order matters
- in a state diagram, I do not include unrelated events

The goal is clarity, not completeness. If the team needs more depth, they can drill in later.

## 12. How would you summarize UML in one sentence for an interview?

### Answer

UML is a family of diagrams used to model the system from the right perspective: structure, behavior, lifecycle, workflow, scope, or architecture.

A strong candidate answer is: “I choose the diagram that answers the decision at hand and keep it focused on the details that influence the design choice.”

## 13. If I only had one diagram to start with, which would it be?

### Answer

In most design interviews, I would start with a class diagram if responsibilities are unclear, because it shows the core entities and their relationships. If the behavior is the real uncertainty, I would start with a sequence diagram instead.

The key is not memorizing a universal rule. It is understanding the design question and choosing the diagram that gives the most useful signal with the least noise.

## 14. What is the best interview mindset for UML questions?

### Answer

The best mindset is: explain the question, choose the correct diagram, model the relevant details, and then justify what you intentionally left out.

That sounds more senior than simply drawing shapes. Senior engineers use UML to drive decisions, not to show off notation. A good answer includes trade-offs: why this view is useful, why another view would be less helpful, and what assumptions remain open.
