# Resiliency Change — Rules

When modifying retries, circuit breakers, or timeout tuning, follow these conventions:

- **Idempotent operations only**: apply retries only to idempotent operations. Retrying non-idempotent calls can cause duplicate side-effects.
- **No hardcoded values**: all retry counts, intervals, breaker thresholds, and timeouts must be driven by env-YAML properties — never hardcode.
- **Env-YAML fan-out**: update retry/breaker/timeout properties across **all** environment YAML files.
- **Breaker contract verification**: before wiring the circuit breaker, confirm the breaker API's contract with its owner. Do not assume request/response semantics.
- **Pagination compounding**: be aware that retries compound across pages in recursive pagination flows — a retry on each page multiplies total latency.
- **Failure-to-outcome mapping**: explicitly document how exhausted retries or a tripped breaker map to the intended outcome. Check `curie/knowledge/error-handling.md` for current behavior.
- **Continue vs propagate impact**: changing `on-error-continue` to `on-error-propagate` (or vice versa) when wiring resiliency is a behavior change that must be called out.
- **MUnit coverage**: update the MUnit suite so the new retry/breaker path is mocked and covered.
