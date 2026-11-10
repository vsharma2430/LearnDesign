# SOLID Principles — Lesson Notes

## The five principles

1. **Single Responsibility Principle (SRP):** A module should have one coherent reason to change—often described as responsibility to one actor or stakeholder. It does not mean every class should have one method.
2. **Open/Closed Principle (OCP):** A design should accommodate likely new behavior with limited changes to stable code. Use a seam such as a strategy or registry when variation is real; do not make every class configurable preemptively.
3. **Liskov Substitution Principle (LSP):** A subtype must honor the behavioral expectations of its base contract. It should not strengthen preconditions, weaken guarantees, or surprise callers with incompatible side effects.
4. **Interface Segregation Principle (ISP):** Keep contracts focused on what clients need. A client should not depend on methods it cannot meaningfully use.
5. **Dependency Inversion Principle (DIP):** High-level policy should not be tightly bound to low-level details; both should depend on stable abstractions. This is about dependency direction, not merely adding interfaces everywhere.

## Applying them together

Start from a change or failure that is costly. SRP helps isolate change; OCP offers controlled extension; LSP protects substitutability; ISP keeps contracts usable; DIP makes policy independent of infrastructure. Excessive fragmentation can make navigation and wiring harder, so keep boundaries proportional to change and test needs.
