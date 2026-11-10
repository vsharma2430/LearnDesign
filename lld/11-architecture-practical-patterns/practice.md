# Architectural and Practical Patterns — Practice

1. Trace an HTTP request that creates an order through controller, handler, domain, repository, and response. What should each layer own?
2. A repository exposes `saveEverything(anything)`. Refine its contract around a specific aggregate and use case.
3. Compose specifications for an eligible discount. How do you keep rule evaluation understandable and testable?
4. Add authentication and tracing middleware. Which runs first, and how are short-circuits and exceptions represented?
5. A transaction spans two repositories. Explain where Unit of Work belongs and what consistency assumptions must be explicit.
6. Choose MVC or MVVM for a form with derived validation state. Identify the view model's responsibilities.
