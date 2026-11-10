# Design Patterns Introduction — Lesson Notes

A design pattern is a named, recurring approach to a design problem in a context. It describes roles and trade-offs, not a copy-paste implementation. Patterns help teams communicate and expose variation points.

## Why use them?

Use a pattern when it makes responsibilities, object creation, collaboration, or change easier to reason about. A name such as Strategy can succinctly describe interchangeable behavior; it does not prove that the behavior needs an abstraction.

## When not to use patterns

Avoid pattern-shaped code when the problem is simple, the variation is hypothetical, or the indirection obscures the domain. Patterns add types, wiring, and sometimes runtime complexity. Prefer a direct implementation until a real pressure calls for a seam.

## Anti-patterns and selection

An **anti-pattern** is a recurring approach that appears helpful but creates costs—examples include God objects, needless singletons, and pattern overuse. To select a pattern: identify what varies, who owns the decision, how callers should be isolated from change, and what new complexity the pattern introduces. Consider a plain function or conditional before a pattern.
