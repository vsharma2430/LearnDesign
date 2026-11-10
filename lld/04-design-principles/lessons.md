# Design Principles — Lesson Notes

- **DRY (Don't Repeat Yourself):** Keep one authoritative representation of a rule or fact. Similar-looking code is not necessarily the same knowledge; premature unification can create a tangled abstraction.
- **KISS (Keep It Simple):** Prefer the design with the least conceptual machinery that meets requirements. Simplicity is about ease of understanding, not minimizing line count.
- **YAGNI (You Ain't Gonna Need It):** Do not implement speculative features. Keep a clear route to future change, but require evidence before adding flexibility.
- **Composition over inheritance:** Assemble behavior from collaborators when variation is independent or combinations may grow. Inheritance is reasonable for a true substitutable hierarchy with a stable shared contract.
- **Law of Demeter / least knowledge:** An object should talk mainly to its direct collaborators rather than navigate long object chains. This reduces knowledge of internal structure; avoid mechanically wrapping every call in a forwarding method.

These principles can conflict. For example, deduplication can undermine KISS if a shared abstraction has many conditionals. Evaluate the actual change pressure, callers, and tests, then choose the least costly design.
