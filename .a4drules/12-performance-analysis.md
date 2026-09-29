# Performance Analysis — Rules

When analyzing performance, latency, or timeout issues, follow these conventions:

- **END-logger `responseTime`**: use the END-logger `responseTime` field (visible in Datadog) to separate app processing time from downstream call time.
- **Architecture-first**: identify the dominant latency contributors from the architecture — typically the paged pull, file writes, or encryption steps.
- **Latency levers to check**:
  - Downstream timeout properties — too low (false timeouts) or too high (slow failures)?
  - Page size — bigger pages = fewer round-trips but larger payloads and more memory pressure
  - `until-successful` retry properties — retries multiply latency on a slow downstream
  - Worker sizing — large data sets may pressure smaller workers; check in-memory aggregation approach
- **Trade-offs required**: every recommendation (timeout/pageSize/retry change, worker size) must explicitly state its trade-off.
- **Target the actual bottleneck**: recommendations must target the real bottleneck (usually the paged pull), not guesses.
