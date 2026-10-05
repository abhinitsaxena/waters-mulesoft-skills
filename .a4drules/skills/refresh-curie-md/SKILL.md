---
name: refresh-curie-md
description: >
  Refresh CURIE.md and knowledge files.
  Use when the user asks "refresh curie", "update knowledge files", "refresh documentation", "sync knowledge"
---

# refresh-curie-md

## Required inputs

None — maintenance prompt over the whole repo.

## Knowledge to load

The following files are located in the MuleSoft repository and should be
read when executing this skill:

All files under `curie/knowledge/` (base + extras):

- `curie/knowledge/architecture.md`
- `curie/knowledge/runtime-and-build.md`
- `curie/knowledge/external-systems.md`
- `curie/knowledge/transformations.md`
- `curie/knowledge/security.md`
- `curie/knowledge/error-handling.md`
- `curie/knowledge/logging.md`
- `curie/knowledge/testing.md`
- `curie/knowledge/deployment.md`
- `curie/knowledge/conventions.md`
- `curie/knowledge/related-repos.md`
- `curie/knowledge/governance-and-links.md`
- `curie/knowledge/known-issues.md`
- `curie/knowledge/integration-test-cases.md`
- `curie/knowledge/employee-file-business-rules.md`
- `curie/knowledge/resiliency-and-circuit-breaker.md`

## Usage

Run this skill when the user asks any of the following:

* "refresh curie"
* "update knowledge files"
* "refresh documentation"
* "sync knowledge"

## Steps

1. Re-read `pom.xml`, `mule-artifact.json`, `src/main/mule/**`, `src/main/resources/env/*.yaml`,
   `src/main/resources/dwl/**`.
2. Update only the 🟡 *volatile* sections:
   - `runtime-and-build.md` — versions, dependency list
   - `external-systems.md` — property namespaces (presence, not exact host values); check whether
     `cb.*` got wired
   - `architecture.md` — flow inventory + global-config inventory
   - `transformations.md` — transform list and intent (not field mappings)
   - `employee-file-business-rules.md` — paygrade/hierarchy/mandatory-field expressions vs source
   - `resiliency-and-circuit-breaker.md` — whether the breaker is now wired / retries changed
   - `integration-test-cases.md` — keep the `TCnn` table + `curie/testData/{{env}}` refs in sync
     (runnable envs `dev`, `stage`; happy path is HTTP 201; verb-not-allowed is 405; observability
     case TC02)
   - `testing.md` — test suite/data file list
3. **Do not edit 🟢 durable sections** unless a real architectural decision changed.
4. **Update `known-issues.md`** if an issue was fixed or a new one discovered (e.g. `cb.*` wired,
   orphan `secureProperties` removed).
5. Re-render `CURIE.md` only if §2/§4/§5 need new rows.
6. New **extra** files must be tagged `(extra)` in `CURIE.md` §2/§4/§5, listed in `curie/README.md`,
   and added to this list — **without modifying the body of existing knowledge/prompt files**
   (additive index/tie-breaker edits only).

## Maintenance list — current extras (keep updated)

- `curie/knowledge/employee-file-business-rules.md` + `curie/prompts/employee-file-rule-change.md`
  (intent E1)
- `curie/knowledge/resiliency-and-circuit-breaker.md` + `curie/prompts/resiliency-change.md`
  (intent E2)

## Maintenance list — integration-test layer (keep in sync)

- `curie/knowledge/integration-test-cases.md` + `curie/prompts/integration-tests.md` (intent #15).
- `curie/testData/` — request-body template + one folder per runnable env (`dev`, `stage`), each with
  `testData.json` and `creds/creds.json`. Credentials are **plaintext** and **sensitive**; store only
  values verified from the source automation data set (never invented); use `"NA"` where a real value
  is unavailable. Never read inbound policy creds from the env YAML.

## Output

- A diff of the knowledge files touched.
- A short changelog at the end of each updated file (`Last refreshed: <date>`).

## Success criteria

- 🟢 durable sections untouched (verify via diff).
- Every 🟡 volatile claim matches the current code.
