---
name: resiliency-change
description: >
  Resiliency / circuit-breaker change.
  Use when the user asks "retry", "circuit breaker", "resiliency", "until-successful"
---

# resiliency-change

> Paired with the knowledge extra `resiliency-and-circuit-breaker.md`. Use this prompt to change
> **retries / wire the latent circuit breaker / timeout tuning**. For generic error-to-status
> mapping use `error-handling-change.md` .

## Required inputs

| Placeholder          | How to resolve                                                          |
| -------------------- | ----------------------------------------------------------------------- |
| `{{GOAL}}`           | retry tuning / wire circuit breaker / timeout tuning / graceful degradation |
| `{{FLOW_NAME}}`      | Which flow(s) — see `curie/knowledge/architecture.md` for the relevant flows |
| `{{POLICY}}`         | Retry count/interval, breaker thresholds, fallback                      |

Ask for any missing input **one at a time** before generating code.

## Knowledge to load

The following files are located in the MuleSoft repository and should be
read when executing this skill:

- `curie/knowledge/resiliency-and-circuit-breaker.md`
- `curie/knowledge/external-systems.md`
- `curie/knowledge/error-handling.md`
- `curie/knowledge/conventions.md`

## Usage

Run this skill when the user asks any of the following:

* "retry"
* "circuit breaker"
* "resiliency"
* "until-successful"

## Steps

1. Establish current state from `curie/knowledge/resiliency-and-circuit-breaker.md` — which flows
   already have retries, and whether the circuit-breaker config is wired or latent.
2. For **retries**: adjust via the existing env properties across all env YAMLs — never hardcode
   (see `curie/knowledge/deployment.md` for the list). Check if the pull is recursive, as retries
   compound across pages.
3. For **wiring the circuit breaker**: add an `<http:request-config>` mirroring the downstream
   pattern (correlation-id, timeout from circuit-breaker property, auth headers, TLS posture) and
   call the circuit-breaker path from the property namespace. **Confirm the breaker API's contract
   with its owner first** — do not assume request/response semantics.
4. Map exhausted retries / a tripped breaker to the intended outcome. Check
   `curie/knowledge/error-handling.md` for current behavior; changing continue to propagate is a
   behavior change that must be called out.
5. Update the MUnit suite (prompt #7) so the new path is mocked and covered.

## Output

- XML for the retry change and/or the new breaker config + invocation.
- Env-YAML property additions/changes across all environments (see `curie/knowledge/deployment.md`).
- A note on the failure-to-outcome mapping and idempotency assumption.

## Success criteria

- Retries applied only to idempotent operations; all affected flows updated.
- No hardcoded retry/breaker values — all via env properties.
- Breaker contract verified with its owner before wiring.
