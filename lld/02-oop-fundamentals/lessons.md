# OOP Fundamentals — Lesson Notes

## Types, state, and behavior

A **class** defines a type's state and operations; an **object** is a particular instance with identity and current state. **Access modifiers** (public, protected, private, or language equivalents) control which callers can rely on members. Keep representation private and expose operations that preserve valid state.

**Encapsulation** keeps data and the rules governing it together. **Abstraction** presents a useful contract while hiding irrelevant implementation. **Inheritance** specializes a base type and reuses a substitutable contract; **polymorphism** lets clients use that shared contract while implementations vary. An **interface** specifies capabilities without prescribing representation; an **abstract class** can provide shared state or partial implementation. Prefer interfaces for independent capabilities and abstract classes only when shared implementation is genuinely cohesive.

## Construction and lifecycle

**Constructors** establish valid initial state; avoid objects that are briefly invalid after construction. A **destructor** or finalizer runs during cleanup in some languages, but timing may be nondeterministic—prefer explicit resource scopes/close operations for files, locks, and sockets. **Static members** belong to a type rather than one object; global mutable static state creates hidden coupling. A **final** or sealed modifier can prevent reassignment, overriding, or subclassing, depending on language.

An object's **lifecycle** runs from creation through use to disposal. Define ownership and cleanup for resources, and do not confuse garbage collection with releasing external resources. **Enums** represent a closed set of named alternatives; give each state deliberate transition rules and account for unknown values when data crosses a versioned boundary.

## Value, identity, and data models

**Immutability** means an object's observable state cannot change after creation. It simplifies reasoning and sharing; ensure contained collections are immutable or defensively copied too.

An **entity** has identity that persists as its attributes change (for example, an account). A **value object** is defined by its values (for example, a currency amount), is commonly immutable, and can be compared structurally. A **DTO** carries data across a boundary and should not accidentally become the home for domain rules. A **domain model** expresses concepts and business invariants.

An **anemic domain model** puts most rules in external services while domain objects expose data only. This can be adequate for simple CRUD or data-centric systems. A **rich domain model** puts behavior near the state and invariants it protects; use it when the domain has meaningful rules, without turning every data structure into a class.
