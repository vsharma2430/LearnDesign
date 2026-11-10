# Clean Code for LLD — Practice

1. Refactor a method named `process()` that validates, persists, and sends a notification. First identify its distinct responsibilities.
2. Find an example of a God object in a familiar system. Suggest one responsibility to move and the invariant that should stay protected.
3. Rewrite three vague names (`data`, `doStuff`, `Manager`) using a specific domain example.
4. A comment says “increment counter by one” above `count++`. Replace it with a comment that would explain a real design constraint—or remove it.
5. Outline unit tests for a booking operation, including one invariant failure and one dependency failure.
