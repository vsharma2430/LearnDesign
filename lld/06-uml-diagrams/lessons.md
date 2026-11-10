# UML Diagrams — Lesson Notes

UML is a notation family, not a design method. Choose only the view that helps answer the current question.

- **Class diagram:** Static structure—types, key attributes/operations, and relationships. Include cardinality and ownership where they matter.
- **Use case diagram:** Actors and goals they pursue with a system boundary. Useful for scope; not a substitute for detailed flows.
- **Sequence diagram:** Time-ordered messages among participants for one scenario. Show important branches or alternatives without diagramming every line.
- **Activity diagram:** Workflow, decisions, and parallel paths. Useful when process logic matters more than object structure.
- **Object diagram:** A snapshot of specific instances and links; useful for illustrating a tricky model or example state.
- **State diagram:** States and event-triggered transitions for one lifecycle. Define guards and actions when they clarify behavior.
- **Component diagram:** Larger implementation units and their provided/required interfaces.

## UML in time-bound interviews

Start with a small class diagram if responsibilities and relationships are unclear. Add a sequence or state diagram when behavior or lifecycle is ambiguous. Use readable names, consistent arrows, and only the detail needed to validate a decision. A quick verbal explanation can replace formal notation when the diagram would slow collaboration. Update diagrams as requirements change.
