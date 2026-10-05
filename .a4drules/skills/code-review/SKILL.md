---
name: code-review
description: >
  Code review.
  Use when the user asks "code review", "review PR", "review this code", "review the diff"
---

# code-review

## Required inputs

| Placeholder       | How to resolve                                              |
| ----------------- | ----------------------------------------------------------- |
| `{{PR_NUMBER}}`   | If PR review: PR number or link                             |
| `{{FOCUS_AREA}}`  | One of: general, best-practices, security, transformations, error-handling |
| `{{SCOPE}}`       | "PR diff only" vs. "whole flow" vs. "whole repo"            |

If `{{FOCUS_AREA}}` is missing, default to **general** and say so explicitly.

Ask for any missing input **one at a time** before generating code.

## Knowledge to load

The following files are located in the MuleSoft repository and should be
read when executing this skill:

- `curie/knowledge/conventions.md` always.
- By `{{FOCUS_AREA}}`:
  - security → `curie/knowledge/security.md`, `curie/knowledge/external-systems.md`
  - transformations → `curie/knowledge/transformations.md`, `curie/knowledge/employee-file-business-rules.md`
  - error-handling → `curie/knowledge/error-handling.md`, `curie/knowledge/resiliency-and-circuit-breaker.md`, `curie/knowledge/logging.md`
  - best-practices → all durable knowledge files
- `curie/knowledge/known-issues.md` always — to avoid re-flagging existing known bugs as new findings.

## Usage

Run this skill when the user asks any of the following:

* "code review"
* "review PR"
* "review this code"
* "review the diff"

## Steps

1. Confirm `{{SCOPE}}`. PR review → diff only. Codebase review → the relevant flow(s).
2. Walk the code, flagging against `conventions.md` first, then the focus-area file. Check: logger in
   `<async>` with clean `flowName`/`flowStep`/`requestType`, `sendCorrelationId="ALWAYS"`, no
   hardcoded hosts/paths, trigger parity, `until-successful` on downstream calls, and whether an
   edge case (empty SF result, error records, exhausted retries) is handled.
3. Order findings: blocker → high → medium → nit.
4. For each: cite file/line, state the issue, link the rule (knowledge file), propose a concrete fix.

## Output

```
# Code Review — <target>
Focus: {{FOCUS_AREA}}
Scope: {{SCOPE}}

## Blockers
- [file:line] <issue> — <rule> — <fix>
## High
## Medium
## Nits
## Already-known issues touched by this PR
- (Cross-reference curie/knowledge/known-issues.md.)
```

## Success criteria

- No style-only findings; no theoretical risks without a failure path.
- Every finding traces to a knowledge-file rule or clear convention.
- Known issues aren't re-litigated unless the PR claims to fix them.
