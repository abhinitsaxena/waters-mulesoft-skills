---
name: error-handling-change
description: >
  Error handling change.
  Use when the user asks "error handling", "error handler", "on-error-continue", "on-error-propagate"
---

# error-handling-change

> For adding **retries / circuit-breaker** specifically, use `resiliency-change.md` .

## Required inputs

| Placeholder            | How to resolve                                                |
| ---------------------- | ------------------------------------------------------------- |
| `{{FLOW_NAME}}`        | Target flow (verify against `src/main/mule/**`)               |
| `{{ERROR_TYPE}}`       | Mule error type/condition (`HTTP:NOT_FOUND`, `HTTP:CONNECTIVITY`, `SFTP:*`, `APIKIT:BAD_REQUEST`, empty-result) |
| `{{DESIRED_BEHAVIOR}}` | Map to status / route / propagate / continue / default        |

Ask for any missing input **one at a time** before generating code.

## Knowledge to load

The following files are located in the MuleSoft repository and should be
read when executing this skill:

- `curie/knowledge/error-handling.md`
- `curie/knowledge/logging.md`
- `curie/knowledge/conventions.md`

## Usage

Run this skill when the user asks any of the following:

* "error handling"
* "error handler"
* "on-error-continue"
* "on-error-propagate"

## Steps

1. Prefer extending the shared `waters-common-error-handler` or the existing process-flow-level
   `<error-handler>` (see `curie/knowledge/architecture.md`); add a scoped handler only if
   behavior is flow-specific.
2. **Mind the async model**: be aware of the fire-and-forget design (see `curie/knowledge/architecture.md`)
   — changing the process-flow handler affects logs/downstream side-effects, not the caller's
   response. If the caller must see failures, that is a bigger design change — call it out.
3. Decide continue vs propagate deliberately — check `curie/knowledge/known-issues.md` for current
   known behavior around swallowed failures.
4. Preserve the APIKit response contract on the main flow (`vars.httpStatus` / `vars.outboundHeaders`).
5. Log the error path via `waters-util:logger` (`flowStep="ERROR"`) wrapped in `<async>`.

## Output

- XML snippet for the handler / `<choice>` / `<try>` branch.
- A note on the resulting behavior (continue vs propagate) and any status for each handled case.

## Success criteria

- No empty `<on-error-*>` blocks.
- Response contract preserved; continue/propagate choice justified.
- Error path logged via `waters-util:logger` in `<async>`.
