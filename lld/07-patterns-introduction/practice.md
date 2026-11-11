# Design Patterns Introduction — Practice Q&A

Use the answers to check your reasoning, not as scripts to memorize. In each case, connect the design choice to the actual pressure and its cost.

## 1. A program has one fixed tax rule. Should it use Strategy?

### Answer

Not by default. If the rule is stable and there is no requirement to select among policies, a direct calculation is easier to follow and has fewer concepts to maintain. Strategy becomes reasonable if tax rules vary by region, customer type, or other meaningful input, especially when those policies need to be independently tested or changed.

Evidence for Strategy would include multiple real implementations, a clear owner for policy selection, and a caller that should not depend on the details of each calculation. The abstraction should correspond to a real variation point, not just anticipated future flexibility.

## 2. Describe an anti-pattern, why it is attractive, and its cost.

### Answer

A needless Singleton may seem attractive because it offers convenient global access and ensures there is only one instance. Over time, that global access hides dependencies: code can use the instance without declaring that requirement, tests can interfere through shared state, and replacing the implementation becomes harder.

If a service needs to be shared, explicit construction and dependency injection often make ownership and dependencies clearer. A Singleton is not automatically wrong, but its process-wide lifetime and global access should be intentional rather than a shortcut.

## 3. A requirement asks you to construct an object based on input, add behavior around an object, and notify many listeners. What pattern families might fit?

### Answer

- **Construct based on input:** a creation pattern such as Factory may help if selection rules are growing or callers should not know concrete product types. A straightforward constructor or switch is simpler when there are only a few stable choices.
- **Add behavior around an object:** a structural wrapper such as Decorator may fit when behavior should be composed around an existing contract. A direct helper or explicit composition may be clearer for one fixed extra step.
- **Notify many listeners:** Observer may fit when multiple subscribers need updates without the publisher depending on their concrete types. A direct call is simpler when there is just one fixed recipient.

The requirement suggests candidates, not a mandate. Check lifecycle, ordering, error handling, and subscription needs before selecting a pattern.

## 4. Explain a familiar pattern without naming its classes, then state its trade-off.

### Answer

One example is a design that lets a caller use a stable operation while substituting one of several implementations of that operation. The caller delegates the work to the selected implementation instead of choosing among algorithm details itself. This is the intent of Strategy.

The benefit is that implementations can vary independently and can be tested behind a common contract. The trade-off is extra indirection and the need to select, configure, and maintain implementations. For one fixed operation, a plain function or direct calculation is less costly.

## 5. Two code paths look similar. Should they be unified to avoid duplication?

### Answer

Not until you establish that they encode the same rule. Similar syntax may conceal meaningful differences, such as separate rounding, authorization, or error-handling policies. Combining them prematurely can create a helper full of flags and conditionals that is harder to reason about than two explicit implementations.

Ask whether the duplicated parts represent the same knowledge and whether a change should always affect both paths. If yes, a shared abstraction can prevent drift. If not, keep the differences explicit and consider sharing only the truly common part.

## 6. A team expects several future integrations and wants to build a plugin framework now. What should it consider?

### Answer

First determine whether integrations are confirmed and what varies between them. If only one integration exists and the requirements are uncertain, a full plugin framework may be speculative: it adds discovery, configuration, lifecycle, compatibility, and failure-handling rules before there is evidence those are needed.

Build the smallest current design with a clear boundary where the real integration is used. When a second integration or an operational requirement demonstrates the variation, extract the smallest useful interface or registration mechanism. Avoid both extremes: hard-coding genuine variation everywhere and designing a framework for imagined needs.

## 7. How do you decide whether a pattern paid for its complexity?

### Answer

Compare the result with the simpler alternative. Useful evidence includes changes being localized to the varying behavior, callers no longer depending on concrete implementation details, tests being easier to isolate, or a repeated decision being represented in one authoritative place.

Also account for new costs: more objects to understand, configuration or lifecycle complexity, and extra paths that can fail. A pattern is worthwhile when the maintenance and correctness benefits outweigh those costs—not merely because its name appears in the design.
