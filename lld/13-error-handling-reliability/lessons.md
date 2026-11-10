# Error Handling and Reliability — Lesson Notes

## Errors and exceptions

Design errors around what callers can do next: correct input, retry, fall back, or report failure. **Checked exceptions** must be declared or handled in languages that support them; **unchecked exceptions** generally represent runtime failures or programming errors. Use the language and repository conventions consistently. Preserve useful context, avoid swallowing failures, and do not expose sensitive internals.

## Resilience mechanisms

- **Retries:** Retry only transient failures, with bounded attempts, backoff, and jitter. Avoid retrying non-idempotent operations blindly or multiplying retries across layers.
- **Idempotency:** Repeating a request has the same intended effect as performing it once. Use an idempotency key and durable result/claim semantics where duplicate side effects matter.
- **Timeouts:** Bound waiting at every relevant boundary and propagate deadlines. A timeout does not necessarily mean the remote operation did not complete.
- **Circuit breaker:** Temporarily stops calls to a failing dependency, then probes recovery. Define thresholds, half-open behavior, and the fallback or error clients receive.

## Observability

**Logging design** records structured, actionable context with appropriate severity while avoiding secrets and excessive noise. **Monitoring hooks** expose metrics, traces, and health signals that help detect and diagnose failures. Correlate related events with request identifiers; instrumentation should not silently alter business outcomes.
