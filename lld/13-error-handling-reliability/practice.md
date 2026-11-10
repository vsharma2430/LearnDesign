# Error Handling and Reliability — Practice

1. A payment request times out after the provider may have charged the card. How do idempotency and reconciliation prevent duplicate charges?
2. Design a retry policy for a transient network failure. Which errors should not be retried, and what bounds prevent retry storms?
3. A downstream service is unhealthy. Describe circuit-breaker states and what callers observe while it is open.
4. Choose an exception or result type for invalid user input versus an unavailable database. Explain the caller's recovery path.
5. Write a structured log event for a failed order request without including sensitive payment data.
