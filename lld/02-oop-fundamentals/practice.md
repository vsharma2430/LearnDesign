# OOP Fundamentals — Practice

1. Model `Money(amount, currency)`. Which fields are immutable? Define equality and explain why adding two amounts in different currencies should not silently work.
2. For a bank account, identify its identity, two invariants, and operations that preserve them. Explain why public setters would weaken the model.
3. Sketch an interface for a notification sender and two implementations. Name one behavior that belongs in the interface contract and one implementation detail that does not.
4. Decide whether `Shape` should be an interface, abstract class, or neither when shapes only need area calculations. State what additional requirement might justify each choice.
5. Identify cleanup behavior for a class that owns a file handle. Explain why a finalizer alone may not be sufficient.
