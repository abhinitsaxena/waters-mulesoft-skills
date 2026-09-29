---
name: munit-tests
description: >
  Write or update MUnit tests.
  Use when the user asks "munit", "write test", "unit test", "add test"
---

# munit-tests

> **This is the only prompt allowed to read `src/test/**`.** All other prompts treat the test
> directory as out-of-scope.

## Default action (IMPORTANT)

When this intent matches, the goal is to **generate or update MUnit XML test cases** — not just
describe what tests could exist. **Gather every missing input from the user before generating
code — one question at a time. Never guess which flow to test, which scenarios to cover, or which
test data to use.**

## Required inputs

| Placeholder        | Required | How to resolve                                                                                      |
| ------------------ | -------- | --------------------------------------------------------------------------------------------------- |
| `{{FLOW_NAME}}`    | **YES**  | Flow or sub-flow under test. Discover by scanning `src/main/mule/**/*.xml` for `<flow>` and `<sub-flow>` elements. **Ask if not given or ambiguous — present discovered flows as options.** |
| `{{MODE}}`         | **YES**  | `new` (write new test cases) or `update` (modify existing test). **Ask if not obvious from context.** |
| `{{SCENARIOS}}`    | **YES**  | Happy-path + at least one error/edge scenario. **Always ask if the user did not specify — never invent scenarios silently.** |
| `{{SAMPLE_DATA}}`  | No       | Input/expected JSON under `src/test/resources/`. Check what already exists. **Ask if the flow needs new test data that does not exist yet.** |
| `{{TARGET_SUITE}}` | No       | Which suite XML to place the test in. Check `src/test/munit/` for existing suites. **Ask if multiple suites exist and the right one is ambiguous.** |

## Resolution gate

**Complete this gate before doing anything else. Do NOT proceed to Steps until every required
input is resolved.**

1. **Discover flows.** Scan `src/main/mule/**/*.xml` for all `<flow>` and `<sub-flow>` elements.
   Build a list of flow names available in this repository.

2. **Resolve `{{FLOW_NAME}}`.** Scan the user's message for a flow name or keyword that matches a
   discovered flow.
   - If a match is found, set `{{FLOW_NAME}}` to the matched flow name.
   - If NO match is found, you MUST ask the user. Present the discovered flows as options:

     > **Which flow should I write tests for?**
     > Pick one (or name several):
     >
     > *(List every discovered flow with a short description derived from its name/context.)*

3. **Resolve `{{MODE}}`.** Does the user's message imply writing new tests or updating existing ones?
   - Phrases like "add test", "write test", "new test" → `new`.
   - Phrases like "update test", "fix test", "change test", "failing test" → `update`.
   - If unclear, ask: **"Should I write new test cases or update existing ones?"**

4. **Resolve `{{SCENARIOS}}`.** Check if the user specified scenarios.
   - If the user named specific scenarios (e.g., "happy path", "timeout", "empty response"),
     accept them.
   - If NOT specified, you MUST ask:

     > **Which scenarios should the tests cover?**
     > At minimum, include one happy path and one error/edge case. Examples for this flow:
     >
     > *(List 2–4 scenario suggestions specific to the resolved `{{FLOW_NAME}}`, derived from
     > reading the flow's XML — e.g., conditional branches, error handlers, choice routers.)*

5. **Resolve `{{SAMPLE_DATA}}` and `{{TARGET_SUITE}}`.**
   - List existing files under `src/test/resources/` and existing suites under `src/test/munit/`.
   - Derive `{{TARGET_SUITE}}` from the existing suite that already covers the flow, or the primary
     suite if this is a new flow. **Ask if multiple suites exist and the right one is ambiguous.**
   - If the scenarios require input data and no matching test data exists, ask:
     **"This flow has no existing test data. Should I create new test JSON, or can you point me to
     sample data?"**

6. **Print the confirmation block** (see below) and wait for the user to confirm before generating
   any code.

## Confirmation block

Before generating any code, print this summary and wait for go-ahead:

```
MUnit Test Plan
===============
Flow under test:  {{FLOW_NAME}}
Mode:             {{MODE}} (new / update)
Scenarios:        {{SCENARIOS}}
Test data:        {{SAMPLE_DATA}}
Target suite:     src/test/munit/{{TARGET_SUITE}}
```

Do not generate XML until the user confirms or adjusts.

## Knowledge to load

Read the following files **if they exist** in the repository. Skip any that are not present:

- `curie/knowledge/testing.md`
- `curie/knowledge/conventions.md`

Also read the existing test suite(s) under `src/test/munit/` to understand the current mocking
style, assertion patterns, and naming conventions before generating new tests.

## Usage

Run this skill when the user asks any of the following:

* "munit"
* "write test"
* "unit test"
* "add test"

## Steps

1. **Inputs resolved.** All placeholders were resolved in the Resolution gate. Place tests in the
   suite identified by `{{TARGET_SUITE}}`.
2. **Read the flow under test.** Open the source XML for `{{FLOW_NAME}}` and identify all external
   calls (HTTP requests, SFTP/FTP writes, flow-refs to external sub-flows, connectors) that must
   be mocked.
3. **Mock the externals** matching the existing suite's style. Use `mock-when` with
   `with-attributes` matching on `doc:id`, `doc:name`, `config-ref`, or `method` as appropriate.
   Return mock payloads via `MunitTools::getResourceAsString(...)` or simulate errors via
   `munit-tools:error`. Never hit a real external system.
4. Set payload/attributes in `munit:execution` to match the flow's expected input (POST bodies,
   query parameters, variables, etc.).
5. Cover the branches identified in `{{SCENARIOS}}` — use `munit-tools:assert-that` for value
   assertions and `munit-tools:verify-call` to confirm processors were (or were not) called.
6. Keep logger `verify-call`s aligned with the actual logger `doc:id`s in the flow XML.
7. Ensure `mule.env` and any secure-properties decryption key are set correctly for MUnit so
   encrypted properties resolve without `BadPaddingException`.

## Output

- MUnit XML test cases.
- Any new test-data JSON under `src/test/resources/` following existing naming conventions.

## Success criteria

- Happy + at least one error/edge scenario covered.
- No hits to live systems.
- Coverage stays at/above the suite's current level.
- All placeholders were resolved from user input or confirmed defaults — none silently guessed.

## Common pitfalls

- **Auto-resolving inputs without asking.** The resolution gate exists for a reason — do not skip
  the user questions by scanning the codebase and filling in defaults silently.
- **Inventing scenarios.** If the user said "write a test" without specifying scenarios, ask — do
  not fabricate a scenario list from the flow XML.
- **Putting tests in the wrong suite.** Check existing suites and their scope before adding tests.
- **Forgetting to mock externals.** Every HTTP request, SFTP/FTP write, and external connector call
  must be mocked. Never let MUnit hit a real system.
- **Stale logger doc:id in verify-calls.** If the flow's logger `doc:id` changed, the verify must
  match — always cross-check against the current flow XML.
- **Mixing MUnit with integration tests.** This skill produces MUnit XML (in-process unit tests),
  not HTTP-based integration tests against a deployed app.
