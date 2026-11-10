# Class Relationships — Lesson Notes

Relationships describe how types collaborate. Choose the relationship that matches runtime ownership and lifetime, not just how a diagram looks.

- **Association:** A lasting link between objects; either side may know about the other. Example: a doctor and a patient can exist independently.
- **Aggregation:** A whole refers to parts that can outlive or exist independently of it. Example: a team and its players, if players can transfer teams. UML shows a hollow diamond at the whole.
- **Composition:** Strong ownership: the whole controls the part's lifecycle, and the part is not independently shared in the modeled context. Example: an order owns its line items. UML shows a filled diamond at the whole.
- **Dependency:** One element temporarily uses another, often through a method argument or local variable. It is weaker than a stored association.
- **Realization / implementation:** A concrete type fulfills an interface or contract; UML uses a dashed line with a hollow triangle toward the contract.
- **Generalization / inheritance:** A specialized type is substitutable for its base type; UML uses a solid line with a hollow triangle toward the base.

The boundaries between aggregation and association can be domain-dependent. Explain lifecycle and ownership assumptions explicitly. Relationships also imply coupling: a class that stores concrete collaborators may be harder to change than one that depends on a stable abstraction.
