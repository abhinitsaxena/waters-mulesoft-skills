---
name: tech-spec-from-functional-spec
description: >
  Generate Tech Spec from Functional Spec.
  Use when the user asks "tech spec", "technical spec", "functional spec", "generate spec"
---

# tech-spec-from-functional-spec

## Required inputs

| Placeholder           | How to resolve                                                                |
| --------------------- | ----------------------------------------------------------------------------- |
| `{{FUNCTIONAL_SPEC}}` | Attached file, pasted text, or fetch from `<FUNCTIONAL_SPEC_CONFLUENCE_LINK>` |
| `{{JIRA_TICKET}}`     | Optional. From chat                                                           |
| `{{TARGET_FLOWS}}`    | Optional. Which flows/endpoints the new behavior extends or modifies          |

If `{{FUNCTIONAL_SPEC}}` cannot be resolved, **ask the user once**. Do not invent business rules.

Ask for any missing input **one at a time** before generating code.

## Knowledge to load

The following files are located in the MuleSoft repository and should be
read when executing this skill:

- `curie/knowledge/architecture.md` — trigger surface (POST/GET trigger, scheduler) and data flow
- `curie/knowledge/external-systems.md` — all downstream and upstream systems
- `curie/knowledge/employee-file-business-rules.md` (if the FS touches paygrade/hierarchy/mandatory-field logic)
- `curie/knowledge/resiliency-and-circuit-breaker.md` (if retries/breaker are touched)
- `curie/knowledge/conventions.md`
- `curie/knowledge/error-handling.md`
- `curie/knowledge/logging.md`
- `curie/knowledge/security.md`
- `curie/knowledge/transformations.md` (only if the FS touches mappings)

Do **not** load `testing.md` or anything under `src/test/**` for this prompt.

## Usage

Run this skill when the user asks any of the following:

* "tech spec"
* "technical spec"
* "functional spec"
* "generate spec"

## Steps

1. **Parse the FS** into: business goal, trigger (POST/GET/scheduler), in-scope data, downstream
   systems touched, non-functional requirements (SF page size, file format, SLA, delivery windows).
2. **Map FS items to repo concepts**: which flow/sub-flow changes, whether the RAML asset needs a
   new operation, whether an existing downstream already provides what's needed vs. a new one.
3. **Identify gaps** that block tech-spec writing (empty SF result handling, retry policy, wiring
   the circuit breaker, new mandatory fields, new file columns). List as **Open Questions**.
4. **Draft the tech spec** using the output format below. Reference conventions explicitly (logger
   in `<async>`, correlation-id, secure properties, trigger parity, `until-successful` posture).
5. **Cross-check** every runtime/dependency/path/namespace claim against the actual repo.

## Output format (markdown)

```
# Technical Design — <feature name>

## 1. Context
- Functional spec: <link or attachment ref>
- Jira: {{JIRA_TICKET}}
- Repository: {{REPO_NAME}} (branch: <ACTIVE_BRANCH>) — derive REPO_NAME from `curie/knowledge/architecture.md`

## 2. Scope (in / out)
## 3. High-level design (trigger, affected flows, reused/extended/new)
## 4. API / interface contract changes (RAML asset impact)
## 5. External system interactions (list from `curie/knowledge/external-systems.md`; auth, timeout, retry)
## 6. Data transformations (DWL intent, not field-by-field)
## 7. Business rules (paygrade/hierarchy/mandatory-field — see employee-file-business-rules.md)
## 8. Error handling (fire-and-forget response, on-error-continue, empty-result)
## 9. Logging (new eventName values, additionalParams identifiers)
## 10. Security (secure:: props, -Dkey, TLS/PGP/SFTP keys)
## 11. Test strategy (MUnit at high level — do not write tests here)
## 12. Deployment & config (new env-YAML props across all environments — see `curie/knowledge/deployment.md`)
## 13. Open questions
```

## Success criteria

- Every section present (even if "N/A").
- No invented versions, hostnames, paths, or property names.
- Open questions listed when the FS is ambiguous instead of silently resolved.
- Paste-ready for Confluence.
