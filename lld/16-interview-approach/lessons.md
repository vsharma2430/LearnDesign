# LLD Interview Approach — Lesson Notes

## A repeatable sequence

1. **Clarify requirements:** Identify actors, core use cases, constraints, and out-of-scope behavior. Ask about ambiguous rules that change the model.
2. **Identify entities and values:** Find concepts with identity, lifecycle, or invariants; distinguish them from immutable values and external systems.
3. **Define behavior and relationships:** Assign responsibilities, state transitions, ownership, and collaboration. Start with the simplest viable model.
4. **Sketch a useful view:** Use a class diagram for structure, a sequence diagram for a key flow, or a state diagram for lifecycle rules.
5. **Apply principles selectively:** Look for concrete coupling, duplicated rules, or invalid substitution. Do not force SOLID or patterns onto every class.
6. **Choose patterns only when they solve a demonstrated pressure:** Name alternatives and trade-offs.
7. **Validate with a scenario:** Walk through a normal path and an important failure or edge case. Check invariants and identify tests.
8. **Handle follow-ups incrementally:** State the changed requirement, locate its impact, and update the design rather than restarting or overgeneralizing.

## UML first or code first?

Use a small diagram first when responsibilities or relationships are unsettled. Start with interfaces or pseudocode when behavior is more concrete and the interviewer expects implementation. These approaches are complementary; keep the representation lightweight and communicate assumptions as you go.

## Communication

Explain why a decision fits the requirement and what it costs. Invite correction, make uncertainty explicit, and prioritize the core use cases over exhaustive feature lists. Reserve time for edge cases, tests, and likely extensions.
