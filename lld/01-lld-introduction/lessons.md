# LLD Introduction — Lesson Notes

## What low-level design means

Low-level design (LLD) describes the internal structure of a software component: its types, responsibilities, interfaces, state, and collaborations. It is the bridge between a requirement and an implementation plan.

A good LLD is not a random list of classes. It is a design that answers questions like:

- What are the important entities in the system?
- What does each object own?
- Which behaviors live where?
- How do these pieces communicate?
- What are the invariants or rules that must always hold?
- What happens when requirements change?

The goal is to be detailed enough to guide implementation, but not so detailed that you waste time on irrelevant abstractions or implementation minutiae.

### Why LLD matters in interviews

An interviewer is often testing whether you can reason about structure, not whether you can name a design pattern. A strong LLD answer shows that you can:

- clarify ambiguity before designing,
- break a problem into the right components,
- define responsibilities clearly,
- choose a model that matches the problem size,
- explain trade-offs honestly.

If your design cannot be implemented, tested, or reasoned about, it is not a good LLD.

### Characteristics of a strong LLD

A good low-level design usually has:

- Clear entities: the key objects or data structures in the domain.
- Clear responsibilities: each unit does one job and communicates through explicit interfaces.
- Well-defined state: there is a reason for each field or property.
- Reasonable boundaries: components do not know too much about one another.
- Invariants: rules such as "a booking cannot be overbooked" or "a payment must be associated with a valid user."
- Testability: it is easy to validate behavior with examples.

### Example of a weak vs strong design

Suppose the requirement is: "Users can book movie tickets."

A weak answer might say:

- Create a `MovieBookingSystem` class.
- Add some methods.
- Store everything in one giant object.

This is vague and leaves too many unanswered questions.

A stronger answer would identify concepts such as:

- `User`
- `Movie`
- `Screen`
- `Booking`
- `Payment`
- `BookingService`

Then define responsibilities such as:

- `BookingService` validates the request
- `Payment` handles payment state
- `Booking` records seat assignment and status
- `Screen` knows which seats are available

This gives a concrete path to implementation.

## LLD, HLD, and machine coding

| Activity | Main question | Typical focus |
|---|---|---|
| High-level design (HLD) | How do major system parts work together at scale? | Services, data stores, APIs, capacity, failure boundaries |
| Low-level design (LLD) | How should a component be modeled and behave? | Classes, interfaces, state transitions, invariants |
| Machine-coding round | Can you implement a working solution under constraints? | Executable code, correctness, tests, practical trade-offs |

These are related but not identical.

### High-level design

HLD asks broad architectural questions. For example:

- How do clients talk to the backend?
- Which services exist?
- How does data flow between them?
- How is redundancy or scaling handled?

HLD is about system boundaries and flow. It usually answers the question, "What parts exist and how do they connect?"

### Low-level design

LLD goes one level deeper. Once you know that a system has a `UserService`, `OrderService`, and `InventoryService`, LLD asks:

- What methods do they expose?
- What data does each class hold?
- How do they collaborate?
- What rules must the object satisfy?

This is the design of the component itself.

### Machine coding

Machine coding is not the same as LLD, though the two overlap. In machine coding, you are expected to write actual code that runs correctly. The challenge is often about:

- implementing behavior correctly,
- handling edge cases,
- staying within time constraints,
- writing readable, testable code.

A machine-coding problem may demand pragmatic choices, but a weak design can still lead to buggy code.

### Why the boundaries matter

A common interview mistake is to confuse HLD with LLD. If the prompt asks for a component design, describing the whole distributed architecture is too broad. If the prompt asks for a system design, listing classes without considering the overall architecture is too narrow.

The correct strategy is to match the prompt's scope.

## Interview types and evaluation

An interviewer may ask for any of the following:

- a whiteboard design,
- a discussion of object relationships,
- a code-level component design,
- an iterative design with follow-up questions,
- a runnable implementation under constraints.

These are not always mutually exclusive. In many interviews, the conversation evolves as you reveal your assumptions and refine the model.

### What interviewers actually evaluate

Most LLD interviews are not graded only on "pattern correctness." They evaluate several dimensions:

1. Requirement clarification
   - Do you ask the right questions before designing?
   - Do you identify missing assumptions?

2. Decomposition
   - Can you break the problem into meaningful components?
   - Are responsibilities clear and balanced?

3. Correctness
   - Does the design actually satisfy the requirements?
   - Does it account for edge cases?

4. Extensibility
   - Is the design flexible enough for expected future changes?
   - Are you adding abstractions only when they bring value?

5. Communication
   - Can you explain your reasoning clearly?
   - Can you adapt when the interviewer presses on specific trade-offs?

6. Response to feedback
   - Are you open to refinement when new constraints appear?

### Common evaluation trap: over-engineering

A design that uses many patterns, abstract interfaces, and inheritance trees may look sophisticated, but if it is hard to explain or not clearly needed, it is a weakness.

Interviewers usually prefer a clean, direct design that solves the actual problem well over an unnecessarily complex one.

### The principle of "fit for scope"

The best design is the one that matches the scope of the problem.

- If the task is a simple utility, a pure function may be better than a class.
- If the task is a stateful domain object, classes and invariants may be more appropriate.
- If the system has multiple actors and workflows, a service-based design may be the right fit.

Good LLD is context-aware; it is not a style contest.

## Functional design and object-oriented design

Two important modeling styles are functional design and object-oriented design.

### Functional design

Functional design organizes behavior around input and output. The key idea is that a function transforms data without mutating shared state in surprising ways.

A functional approach is often useful when:

- the behavior is mostly calculation,
- data is immutable or transformed in clear steps,
- you want to reduce side effects,
- you need easier reasoning and tests.

Example:

- `calculateTotal(items, discountPolicy)`
- `validateUserInput(request)`
- `formatInvoice(order)`

These functions take explicit inputs and return outputs, making them easy to test.

### Object-oriented design

Object-oriented design groups state and behavior in a single place. The object owns the data and knows how to enforce invariants.

A class is often preferable when:

- you need to represent a domain entity with lifecycle or state,
- multiple operations rely on the same internal state,
- the object must enforce rules around valid transitions,
- you want to model behavior around responsibilities rather than pure data transformation.

Example:

- `Order` tracks items, status, and total
- `Cart` adds/removes items and validates invariants
- `UserAccount` manages balance, permissions, and updates

The object is not just data; it is a cohesive unit of behavior.

### How they complement each other

Functional and object-oriented design are not enemies. They often work together.

A domain object can protect invariants while pure functions calculate derived values.

Example:

- `Order` enforces that total cannot be negative.
- `calculateTax(orderLineItems, taxRate)` is a pure function that returns a numeric result.

This separation often produces clearer code: stateful boundaries with deterministic transformation logic.

### When to prefer one over the other

Choose functional design when the behavior is mostly transformation and the state is simple.

Choose OOP when the behavior depends on long-lived state, lifecycle rules, and object-level invariants.

The best design is not determined by a personal preference. It is determined by the shape of the problem.

## When not to use OOP

Object-oriented design is powerful, but it is not always the right tool.

Avoid adding classes when a small function, plain data structure, or transformation pipeline expresses the domain more clearly. OOP is often unnecessary when:

- the logic is a one-off computation,
- there are no meaningful states or lifecycle transitions,
- the behavior can be modeled as pure data operations,
- the design would only add ceremony without clarity.

### Example: simple conversion utility

A utility that converts Celsius to Fahrenheit can be cleanly modeled as a function:

```python

def celsius_to_fahrenheit(celsius):
    return (celsius * 9 / 5) + 32
```

Creating a `TemperatureConverter` class with a single method adds abstraction without improving clarity.

### Example: when classes do help

Suppose you have a `ShoppingCart` with rules like:

- items can be added only while the cart is active,
- duplicate items may be merged,
- total price must be recalculated when items change,
- checkout transitions the cart to a completed state.

Now classes are useful because the cart has state, rules, and lifecycle transitions.

### Anti-pattern to avoid

Do not create a class just because you can. Avoid:

- inheritance for the sake of taxonomy,
- object wrappers around every function,
- deep hierarchies without real behavioral differences,
- forcing identity onto plain data.

A good design keeps objects when they help explain and enforce behavior. It does not invent complexity.

## How to think in an LLD interview

The best way to approach an LLD problem is not to start drawing a full architecture immediately. Instead, follow a structured flow.

### 1. Clarify the problem

Ask questions before finalizing the design. For example:

- What are the core entities?
- What operations matter most?
- Are there concurrency or consistency constraints?
- What is the expected scale?
- What are the important edge cases?

If you do not clarify assumptions, your design may be correct in the wrong context.

### 2. Identify the core domain objects

Look for nouns in the requirement and separate noisy details from meaningful entities.

For a parking lot problem, likely objects include:

- `Vehicle`
- `ParkingLot`
- `ParkingSpot`
- `Ticket`
- `EntryGate`
- `ExitGate`

Some nouns may just be attributes rather than actual entities.

### 3. Define responsibilities

Once you know the objects, decide what each one owns.

Examples:

- `ParkingLot` manages available spots and allocation.
- `Vehicle` has identity and type metadata.
- `Ticket` records parking session state.
- `EntryGate` handles request intake.

This is where many designs improve or fail: responsibilities must be crisp and non-overlapping.

### 4. Define interactions and rules

A good design should explain how objects communicate and what constraints exist.

Examples:

- The system should not assign a spot already occupied.
- A ticket should correspond to a single active parking session.
- Payment or validation should occur before the gate opens.

Defining these rules is essential because they become the hidden logic behind the component.

### 5. Decide if the model is simple enough

After you have the object model, ask:

- Can this be implemented directly?
- Is there unnecessary complexity?
- Can a function or data structure replace a class without harming clarity?
- Do I need inheritance or interfaces, or are they just decorative?

This self-check helps prevent over-engineering.

### 6. Explain trade-offs clearly

A strong LLD answer does not just say "This is my design." It also says:

- why this is the right abstraction,
- what alternatives were rejected,
- what trade-offs are being made,
- which constraints are expected to evolve.

This is what makes the design convincing.

## Putting it together

LLD is about thinking in terms of responsibilities, state, and interactions. The aim is not to show the most complex or fashionable design; it is to show that you can turn a vague requirement into a component that is clear, testable, and realistic.

The key lessons from this introduction are:

- LLD focuses on structure inside a component, not broad system architecture.
- Great designs are correct, clear, and scoped to the task.
- Functional and object-oriented models both have valid uses.
- OOP should be added when it improves clarity, not because it feels "more formal."
- Good communication matters as much as the design itself.

Once you internalize this, later LLD problems become easier because you are not trying to memorize patterns—you are reasoning from first principles.
