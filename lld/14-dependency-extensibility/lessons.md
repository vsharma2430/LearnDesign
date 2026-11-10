# Dependency Management and Extensibility — Lesson Notes

**Dependency Injection (DI)** supplies a collaborator from outside the object that uses it. **Constructor injection** makes required dependencies explicit and supports valid construction; **setter injection** suits optional or reconfigurable dependencies but can leave objects incomplete. **Manual DI** wires objects directly at an application boundary; **framework DI** automates registration and lifecycle but adds framework configuration and indirection.

**Inversion of Control (IoC)** means a component delegates control—such as lifecycle or callbacks—to a framework or higher-level coordinator. DI is one form of IoC, not a synonym for every form.

Design for extensibility around known or credible change points: define a stable contract, keep policy separate from details, and test alternative implementations. **Open for extension, closed for modification** is a goal for stable policy, not a prohibition on editing code. Prefer a simple implementation until a second real variation or a test seam justifies abstraction. Avoid service locators and global registries that hide dependencies.
