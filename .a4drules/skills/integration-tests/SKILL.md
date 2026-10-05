---
name: integration-tests
description: >
  Run integration tests on the deployed app.
  Use when the user asks "run integration tests", "integration test", "smoke test", "test deployed app"
---

# integration-tests

> Integration tests are **end-to-end tests against a deployed app**, not MUnit tests. MUnit work
> lives in `munit-tests.md` . **Do not conflate the two.**
>
> Cases are catalogued in `integration-test-cases.md` (`TCnn`), with test data + credentials in this
> repo under `curie/testData/{{env}}`.
>
> **Headers**: the trigger case sends `client_id` + `client_secret` + `postmanTestFlag` +
> `Content-Type: application/json`, taken from `curie/testData/{{env}}/creds/creds.json` and
> `.../testData.json` (plaintext model — see `integration-test-cases.md` §3). The observability case
> uses the monitoring keys (`dd_api_key`/`dd_app_key`) from the same creds file.
> **Auth model**: inbound client-id enforcement policy only; **no Bearer/OAuth token and no 401
> auth-rejection case.**
>
> ⚠️ **This app is fire-and-forget**: a `201 {"message":"Job Triggered"}` confirms the trigger, not
> the SFTP file delivery. Send `postmanTestFlag: true` to end the run before the schedule-stop call.
> Confirm the actual run via the observability case (TC02) and the SFTP target.

## Default action (IMPORTANT)

When this intent matches, the goal is to **run the integration-test agent**
(`generate_integration_test`) using the `TCnn` cases + repo test data — unless the user explicitly
asked only to *view/update* cases. Do not stop at a plan/table.

**Gather every missing input from the user before invoking — one question at a time. Never guess
URLs, envs, secrets, or expected values.** All credentials come from
`curie/testData/{{env}}/creds/creds.json` only — **never** from the env YAML `http.*.clientId`.

## When to use this prompt

- "Run integration tests for the trigger in dev."
- "Run the test cases / smoke-test the deployed app."
- "Execute TC01–TC03 in stage."

## Required inputs (resolve all before invoking)

| Placeholder          | How to resolve / what to ask                                                                              |
| -------------------- | --------------------------------------------------------------------------------------------------------- |
| `{{ENVIRONMENT}}`    | Runnable env. Maps to the test-data + creds folder. See `curie/knowledge/deployment.md` for valid runnable envs vs. out-of-scope envs. **Ask if not given.** |
| `{{DEPLOYMENT_URL}}` | Public base URL. Derive from the app-name pattern in `curie/knowledge/deployment.md` or the Anypoint Exchange agent. **Ask once if it can't be derived with certainty.** |
| `{{TEST_CASES}}`     | Which `TCnn` (default: all from `integration-test-cases.md`). **Ask if the user wants a subset.**         |
| `{{FLOW_NAMES}}`     | Operations under test. Derive from the selected test cases.                                               |
| `{{SCHEDULE_KIND}}`  | Optional `ONCE`/`INTERVAL`. **Ask only if the user implied scheduling.**                                 |

## Knowledge to load

The following files are located in the MuleSoft repository and should be
read when executing this skill:

- `curie/knowledge/integration-test-cases.md` — the `TCnn` catalog (cases, tags, test-data wiring, assertions, env matrix). Source of cases.
- `curie/knowledge/architecture.md`
- `curie/knowledge/external-systems.md`
- `curie/knowledge/conventions.md`
- `curie/knowledge/deployment.md`
- `curie/knowledge/security.md`
- `curie/knowledge/employee-file-business-rules.md` (if business-rule checks ride on the cases)

Do **not** load `testing.md` or treat `src/test/munit/**` as source of truth — those are MUnit.

## Usage

Run this skill when the user asks any of the following:

* "run integration tests"
* "integration test"
* "smoke test"
* "test deployed app"

## Steps

1. **Resolve inputs.** Walk the table; ask for anything missing (one question at a time). Confirm
   `{{ENVIRONMENT}}` is runnable (`dev` or `stage`).
2. **Fetch the test data + credentials** for the chosen env — do not invent:
   - Read actual values from `curie/testData/{{env}}/testData.json` and `triggerRequestBody.json`.
   - **Credentials (env-tag driven; ALWAYS from the creds folder)**: take `client_id`/`client_secret`
     (and `dd_api_key`/`dd_app_key` for TC02) **only** from
     `curie/testData/{{env}}/creds/creds.json`. **Never read `http.*.clientId` from the env YAML**
     (those are outbound SF/batch creds). Values are plaintext — send as-is; **never log or echo
     them**.
   - If `creds.json` holds `"NA"` (e.g. stage today) or is missing for the env, **stop and ask the
     user for the real values** — do not proceed or substitute another env's creds.
3. **Select cases.** Default all `TCnn` (TC01–TC03); honor any subset. Resolve `{{FLOW_NAMES}}` from
   the selected cases.
4. **Resolve the deployment URL** for the chosen env (derive or ask).
5. **Confirm target**: print the confirmation block and wait for go-ahead (especially before any
   non-dev run).
6. **Decide schedule vs immediate.** Run now unless a schedule was implied.
7. **Invoke `generate_integration_test`** with:
   - `repo_name` = derive from `curie/knowledge/architecture.md` or ask the user
   - `flow_names` = resolved `{{FLOW_NAMES}}`
   - `deployment_url` = resolved `{{DEPLOYMENT_URL}}`
   - `additional_notes` = selected `TCnn` rows (Scenario, Expected Outcome, Status, Body/Behavior,
     Tag, **resolved test-data values from `curie/testData/{{env}}`**, Comments) + assertion patterns
     + the async/`postmanTestFlag` caveat + which headers are sent + the auth model. **Do not include
     any secret value in the notes.**
   - schedule fields only if confirmed.
8. **Report back.** Return the task id immediately; after completion summarize by case ID
   (`TC01 … pass/fail`). Cross-ref `known-issues.md` before flagging a failure as new.

## Output

- Confirmation block before invoking:
  ```
  Target:        {{REPO_NAME}}
  Environment:   {{ENVIRONMENT}}
  Base URL:      {{DEPLOYMENT_URL}}
  Cases:         {{TEST_CASES}}
  Operations:    {{FLOW_NAMES}}
  Test data:     curie/testData/{{env}}
  Creds:         curie/testData/{{env}}/creds/creds.json
  Schedule:      {{SCHEDULE_KIND}} or "Run now"
  ```
- After invocation, the integration-test agent task id.
- After completion, a results table (Sr. No. | Scenario | Expected HTTP Outcome | HTTP Status Code |
  Actual | Test Tag | Pass/Fail | Notes).

## Success criteria

- The agent was actually invoked (not just a plan), unless the user only asked to view/update cases.
- Test data + creds were **fetched from `curie/testData/{{env}}`**, not invented, and not crossed
  between envs.
- All placeholders resolved from context or **explicitly asked**.
- Cases referenced by `TCnn`, aligned with `integration-test-cases.md`.
- No credentials/secrets appear in chat or logs; out-of-scope envs not targeted.

## Common pitfalls

- Producing a table instead of a run.
- Inventing test data / guessing a missing input / running with `"NA"` creds.
- Reading credentials from the env YAML instead of the creds folder.
- Asserting file delivery from the `201` response (it's async — verify via TC02 out-of-band).
- Mixing MUnit with integration tests. Targeting `prod`/`preprod`/`-s4` envs.
