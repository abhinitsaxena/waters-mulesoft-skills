---
name: performance-analysis
description: >
  Performance analysis.
  Use when the user asks "performance", "slow", "latency", "timeout"
---

# performance-analysis

## Required inputs

| Placeholder        | How to resolve                                                |
| ------------------ | ------------------------------------------------------------- |
| `{{SYMPTOM}}`      | Slow job / high latency / timeouts / large file               |
| `{{ENVIRONMENT}}`  | Which env (must match an env YAML)                            |

Ask for any missing input **one at a time** before generating code.

## Knowledge to load

The following files are located in the MuleSoft repository and should be
read when executing this skill:

- `curie/knowledge/runtime-and-build.md`
- `curie/knowledge/external-systems.md`
- `curie/knowledge/known-issues.md`
- `curie/knowledge/resiliency-and-circuit-breaker.md`

## Usage

Run this skill when the user asks any of the following:

* "performance"
* "slow"
* "latency"
* "timeout"

## Steps

1. Anchor on the architecture: identify the dominant latency contributors (paged pull, file writes,
   encryption) from `curie/knowledge/architecture.md` and `curie/knowledge/runtime-and-build.md`.
2. Check the levers:
   - Downstream timeout properties — too low (false timeouts) or too high (slow failures)?
     See `curie/knowledge/external-systems.md` for the relevant property namespaces.
   - Page size — bigger pages = fewer round-trips but larger payloads / memory pressure.
   - `until-successful` retry properties multiply latency on a slow downstream
     (see `curie/knowledge/resiliency-and-circuit-breaker.md`).
   - Worker sizing — check in-memory aggregation approach and worker type in
     `curie/knowledge/runtime-and-build.md`; large data sets may pressure smaller workers.
3. Use the END-logger `responseTime` (Datadog) to separate app time from downstream time.
4. Recommend concrete changes (timeout/pageSize tuning, worker size) with trade-offs.

## Output

- A short latency breakdown (SF pull vs transform vs SFTP) and top 1–3 levers.
- Concrete config/code recommendations with trade-offs.

## Success criteria

- Recommendations target the actual bottleneck (usually the SF paged pull), not guesses.
- Any timeout/pageSize/retry change states its trade-off.
