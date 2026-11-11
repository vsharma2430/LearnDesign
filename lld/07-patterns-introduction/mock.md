# Design Patterns Introduction — Mock Interview Q&A

These questions focus on explaining design decisions rather than recalling pattern definitions. Strong answers state the problem, discuss an alternative, and name the trade-off.

## 1. What is a design pattern, and what is it not?

### Answer

A design pattern is a named, recurring approach to a design problem in a particular context. It describes intent, collaborating roles, and trade-offs, so engineers can communicate a design using shared vocabulary.

It is not a library, a complete implementation, or a rule that every similar-looking problem must use the same structure. A pattern is useful only when its structure addresses a real pressure in the system. Starting from the pattern name and forcing the code to fit can add complexity without solving a problem.

## 2. How do you decide whether to introduce a pattern?

### Answer

I identify the concrete pressure first: what changes, what is coupled, or what responsibility is unclear? Then I identify what varies and who should own that decision. I compare a direct implementation with a pattern-shaped design and consider the ongoing cost of its roles, indirection, wiring, and lifecycle.

I introduce the smallest structure that makes the actual variation easier to change or test. If the behavior is fixed and local, direct code is usually better. If change pressure becomes real later, I can add an abstraction at the boundary that has emerged.

## 3. When would you use Strategy, and when would you avoid it?

### Answer

I would use Strategy when a caller must work with a real choice among interchangeable behaviors and should not be coupled to each algorithm's details. For example, multiple shipping-cost policies selected by region could implement one policy contract and be tested independently.

I would avoid it for a single stable algorithm. In that case, an interface and multiple classes create navigation and selection complexity without a meaningful variation benefit. A function, a direct calculation, or a small conditional may be clearer.

## 4. Is a conditional an inferior design to a pattern?

### Answer

No. A conditional is often the clearest expression of a small, stable set of choices. It becomes a problem when selection logic is duplicated, changes frequently, or makes one component responsible for unrelated implementations.

I would replace it when there is evidence that the variation should be isolated—not simply because a conditional exists. A pattern can make responsibilities clearer, but it can also scatter a simple decision across extra types.

## 5. What is pattern overuse? Give an example.

### Answer

Pattern overuse is adding a known structure when its complexity exceeds the problem it addresses. For example, creating an abstract factory, registry, and plugin lifecycle for one fixed object that can be created with a constructor can make creation harder to discover.

The remedy is to return to the pressure and compare against a simpler alternative. If there is no real variation, hidden coupling, or construction complexity to address, remove the extra structure. Pattern names are tools for describing useful designs, not quality scores.

## 6. What is an anti-pattern? Is every Singleton an anti-pattern?

### Answer

An anti-pattern is a recurring approach that appears to help but tends to produce greater long-term costs. A God object and a speculative framework are examples. A needless Singleton is often problematic because global access hides dependencies and shared state can make testing and replacement difficult.

Not every Singleton is automatically an anti-pattern. A genuinely process-wide resource with deliberate lifecycle and concurrency semantics may justify one. I would still ask whether explicit dependency ownership would be clearer and whether the global access creates unwanted coupling.

## 7. How can you tell whether two similar implementations should share an abstraction?

### Answer

I ask whether they represent the same knowledge and whether changes should affect them together. Similar code shape is not enough. Two calculations may differ in domain rules, rounding, authorization, or failure behavior.

If the underlying rule is the same, sharing it prevents drift. If the rules differ, forcing them through one helper may require flags and conditionals that obscure the difference. I would share only the stable common responsibility and keep distinct policies explicit.

## 8. How do patterns relate to anti-patterns?

### Answer

A pattern is a recurring solution with known benefits and costs; an anti-pattern is a recurring approach whose costs commonly outweigh its apparent benefit. The categories depend on context and outcomes. A pattern applied mechanically can become harmful, and a design without named patterns can be excellent.

I evaluate whether the design clarifies responsibility, reduces coupling, and makes expected changes safer. If it instead hides behavior, adds speculative flexibility, or increases mental overhead without a corresponding benefit, it is the wrong choice in that context.

## 9. How would you explain a design choice when an interviewer suggests a more flexible pattern?

### Answer

I would acknowledge the kind of change the pattern would support, then explain whether that change is an actual requirement. For example: “A policy interface would let us substitute tax rules. We currently have one fixed rule, so I kept the calculation direct. If regional policies become a requirement, I would introduce the seam at the tax calculation boundary and keep checkout independent of the implementations.”

This shows that I understand the pattern and its potential value while making a deliberate choice about complexity. I would also state what evidence would cause me to revisit the decision.

## 10. What makes a strong design-pattern interview answer?

### Answer

A strong answer explains:

1. The specific design pressure or change requirement.
2. The responsibility or variation point that needs a boundary.
3. Why the pattern fits better than a direct alternative.
4. The costs and risks introduced by the pattern.
5. How the design could be simplified or evolved if assumptions change.

I avoid presenting patterns as universal rules. The goal is to defend the least complex design that handles the real requirement and remains understandable to the team.
