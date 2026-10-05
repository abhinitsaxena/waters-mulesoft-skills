---
name: debug-issue
description: >
  Debug a failing flow / production issue (RCA).
  Use when the user asks "debug", "failing flow", "production issue", "root cause analysis"
---

# debug-issue

## Required inputs

| Placeholder           | How to resolve                                        |
| --------------------- | ----------------------------------------------------- |
| `{{ERROR_SIGNATURE}}` | Stack trace, error type, or Datadog log excerpt       |
| `{{FLOW_NAME}}`       | If known                                              |
| `{{ENVIRONMENT}}`     | Which env (must match an env YAML)                    |
| `{{RECENT_CHANGES}}`  | Recent deploys, version bumps, config changes         |

Ask for any missing input **one at a time** before generating code.

## Knowledge to load

The following files are located in the MuleSoft repository and should be
read when executing this skill:

- `curie/knowledge/error-handling.md`
- `curie/knowledge/logging.md`
- `curie/knowledge/runtime-and-build.md`
- `curie/knowledge/external-systems.md`
- `curie/knowledge/known-issues.md`
- `curie/knowledge/resiliency-and-circuit-breaker.md` (if a downstream failure is suspected)

## Usage

Run this skill when the user asks any of the following:

* "debug"
* "failing flow"
* "production issue"
* "root cause analysis"

## Steps

1. Form 2–3 hypotheses *before* reading code.
2. Match `{{ERROR_SIGNATURE}}` to known gotchas in `curie/knowledge/known-issues.md` and
   `curie/knowledge/error-handling.md`. Common categories:
   - Encryption/decryption errors → check env key configuration (`runtime-and-build.md`).
   - Job triggered but nothing delivered → fire-and-forget + swallowed downstream failure; check
     observability logs (`error-handling.md`, `known-issues.md`).
   - Paged pull hangs / partial data → recursive pagination + retry interaction
     (`resiliency-and-circuit-breaker.md`).
   - File delivery fails → per-env credential/key/host mismatch (`external-systems.md`, `deployment.md`).
   - Records unexpectedly dropped → mandatory-field or filter logic
     (`employee-file-business-rules.md`, `known-issues.md`).
   - Observability grouping odd → logger convention issues (`known-issues.md`, `logging.md`).
   - Connectivity fails only in one env → host/port differs per env YAML.
3. Read only the files the hypotheses point at. Use the correlation ID to trace.
4. If 3 fix attempts already failed, stop iterating — summarize what's tried, propose a different
   angle or a manual verification (direct SF curl, SFTP connectivity test, env-YAML host check).

## Output

- Root cause (or "best current hypothesis" if not confirmable without a deploy).
- Fix proposal with file/line references.
- Verification steps (Datadog query, direct downstream check, env-YAML check).

## Success criteria

- Fix points at a real file/line, not a guess.
- Verification steps are concrete.
