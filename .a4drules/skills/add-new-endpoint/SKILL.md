---
name: add-new-endpoint
description: >
  Add a new endpoint.
  Use when the user asks "add a new endpoint", "new operation", "expose POST /foo", "new resource in the RAML"
---

# add new endpoint

> For the **HTTP/APIKit surface**. For a transformation-only change use
> `add-or-change-dataweave.md` ; for an employee-file rule change use the domain-rule
> extra `employee-file-rule-change.md` .

## Required inputs

| Placeholder            | How to resolve                                                  |
| ---------------------- | --------------------------------------------------------------- |
| `{{ENDPOINT}}`         | HTTP method + path (e.g., `POST /{{RESOURCE}}`)                 |
| `{{DOWNSTREAM_SYSTEM}}`| Which system(s) it calls                                        |
| `{{REQUEST_PAYLOAD}}`  | Shape/example of the incoming payload / query params            |
| `{{RESPONSE_PAYLOAD}}` | Shape/example of the outgoing payload                           |
| `{{JIRA_TICKET}}`      | Optional                                                        |

Ask for any missing input **one at a time** before generating code.

## Knowledge to load

The following files are located in the MuleSoft repository and should be
read when executing this skill:

- `curie/knowledge/security.md`
- `curie/knowledge/external-systems.md`
- `curie/knowledge/conventions.md`
- `curie/knowledge/error-handling.md`
- `curie/knowledge/logging.md`

## Usage

Run this skill when the user asks any of the following:

* "add a new endpoint"
* "new operation"
* "expose POST /foo"
* "new resource in the RAML"

## Steps

1. **Spec first** — add the operation to the RAML asset (see `curie/knowledge/architecture.md` for
   the asset name and version) and regenerate APIKit bindings. Don't hand-edit the `post:\…`/`get:\…` APIKit flows.
2. **Mirror the existing pattern** — async START + END `waters-util:logger` (`requestType`, clean
   distinct `flowName`, `responseTime="true"` on END). Note the existing trigger responds *before*
   async work — match or deliberately deviate.
3. Build downstream requests via env-YAML namespaces; do **not** hardcode hosts/paths; set
   `sendCorrelationId="ALWAYS"` on any new request config.
4. Set `vars.httpStatus` / `vars.outboundHeaders` for non-200 / custom headers, preserving the
   APIKit response contract on the main flow (see `curie/knowledge/architecture.md`).
5. If the endpoint needs retries/breaker, see `resiliency-and-circuit-breaker.md` (intent E2).

## Output

- Full flow XML block(s), not partial.
- Any new global-config request config for a new downstream.
- Env-YAML keys to add per env (cleartext shape — encryption is separate).
- Do not write MUnit here — that is prompt #7.

## Success criteria

- Endpoint compiles and is reachable via the APIKit router.
- Logging/correlation-id conventions present; trigger parity preserved.
- No property names hardcoded — all via `${…}` / `${secure::…}`.


