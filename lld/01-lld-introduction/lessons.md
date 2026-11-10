# LLD Introduction — Lesson Notes

## What low-level design means

Low-level design (LLD) describes the internal structure of a software component: its types, responsibilities, interfaces, state, and collaborations. It translates requirements into a design that can be implemented and tested. A useful LLD is detailed enough to guide code, but does not prescribe irrelevant implementation minutiae.

### LLD, HLD, and machine coding

| Activity | Main question | Typical focus |
|---|---|---|
| High-level design (HLD) | How do major system parts work together at scale? | Services, data stores, APIs, capacity, failure boundaries |
| Low-level design (LLD) | How should a component be modeled and behave? | Classes, interfaces, state transitions, invariants |
| Machine-coding round | Can you implement a working solution under constraints? | Executable code, correctness, tests, practical trade-offs |

These formats overlap: an LLD may include code, and a machine-coding task still benefits from sound design. Follow the actual interview prompt rather than assuming a fixed format.

## Interview types and evaluation

An interviewer may ask for a whiteboard design, object model, runnable implementation, or an iterative design discussion. Common evaluation dimensions are requirement clarification, decomposition, correctness, extensibility where justified, communication, and response to change. Elegant patterns do not compensate for missing core behavior.

## Functional design and object-oriented design

Functional design organizes behavior around transformations of data, ideally with explicit inputs and outputs and limited mutation. Object-oriented design groups state and behavior behind collaborating objects. They are complementary tools: a domain object can protect an invariant while pure functions calculate prices or validate a policy.

### When not to use OOP

Avoid adding classes when a small function, data record, or collection operation expresses the domain more clearly. OOP may be unnecessary for a one-off transformation, a simple script, or a pipeline of pure operations. Choose the simplest model that makes state, rules, and change easy to understand; do not force inheritance or object identity onto plain data.
