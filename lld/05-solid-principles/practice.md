# SOLID Principles — Practice

1. A `ReportService` queries data, formats HTML, and emails the report. Apply SRP by naming cohesive responsibilities and a likely reason each changes.
2. A new shipping method requires editing a large `switch`. Sketch an OCP-friendly design and state when the switch is still simpler.
3. A `ReadOnlyList` subtype throws from `add`. Analyze the client expectation and explain the LSP issue.
4. A `Worker` interface includes `print`, `scan`, and `fax`; a basic printer cannot implement two methods meaningfully. Apply ISP.
5. A business policy directly constructs a database client. Apply DIP without hiding all details behind a meaningless generic interface.
