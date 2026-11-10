# 1. LLD Introduction

**Goal:** Understand what an LLD interview asks you to design, how it differs from adjacent interview formats, and how to choose the right modeling style for the problem.

This lesson is the foundation for later LLD practice. The main idea is not memorizing class diagrams; it is learning how to turn vague requirements into a concrete, testable design.

## Topics covered

- [What low-level design means](lessons.md#what-low-level-design-means)
- [LLD vs HLD vs machine coding](lessons.md#lld-hld-and-machine-coding)
- [Interview types and evaluation](lessons.md#interview-types-and-evaluation)
- [Functional vs object-oriented design](lessons.md#functional-design-and-object-oriented-design)
- [When not to use OOP](lessons.md#when-not-to-use-oop)
- [How to think in LLD interviews](lessons.md#how-to-think-in-an-lld-interview)

**Study:** [Lesson notes](lessons.md) → [Practice](practice.md)

**Ready when:** You can clarify a problem, describe the important objects and their responsibilities, explain how they collaborate, and justify why the chosen design fits the requested scope.

## Why this matters

LLD interviews are usually not judged on whether your design is the most advanced possible. They are judged on whether you can reason clearly about behavior, boundaries, and trade-offs.

A good answer has these qualities:

- It matches the problem statement.
- It identifies the core entities and interactions.
- It respects constraints such as scale, concurrency, and complexity.
- It avoids over-engineering.
- It can be implemented without hidden assumptions.

In short, LLD is about moving from "I understand the requirement" to "I can define a working component in a way another engineer can implement and test."
