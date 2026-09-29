---
name: logging-change
description: >
  Logging change.
  Use when the user asks "logging", "add logger", "change logging", "waters-util:logger"
---

# logging-change

## Required inputs

| Placeholder        | How to resolve                                                          |
| ------------------ | ----------------------------------------------------------------------- |
| `{{FLOW_NAME}}`    | Flow being logged                                                       |
| `{{EVENT_NAME}}`   | Descriptive event name (see `curie/knowledge/logging.md` for naming conventions) |
| `{{BUSINESS_IDS}}` | Business identifiers to echo (see `curie/knowledge/logging.md` for relevant fields) |

Ask for any missing input **one at a time** before generating code.

## Knowledge to load

The following files are located in the MuleSoft repository and should be
read when executing this skill:

- `curie/knowledge/logging.md`
- `curie/knowledge/conventions.md`
- `curie/knowledge/known-issues.md`

## Usage

Run this skill when the user asks any of the following:

* "logging"
* "add logger"
* "change logging"
* "waters-util:logger"

## Steps

1. Use `waters-util:logger` (`config-ref="Waters_Utility_Connector_Config"`). **Never** standard
   Mule `<logger>`.
2. Wrap every logger in `<async>`.
3. Required attrs: `requestType="HTTP"` (or `POLL` for the scheduler), `flowName`, `flowStep`
   (`START|MIDDLE|END|ERROR`), `eventName`, `message`. END loggers set `responseTime="true"`.
4. Populate `additionalParams` with business identifiers. Never log secrets/decrypted `secure::`.
5. **Watch the known typos** (`known-issues.md` #4): `flowStep="STAR"`, and literal
   `requestType="POLL"`/`"vars.requestType"` where `#[vars.requestType]` was intended. Fix these in
   loggers you touch; use clean, distinct `flowName`/`flowStep` values.

## Output

- Logger XML blocks.
- A note on which business identifiers belong in `additionalParams`.

## Success criteria

- No standard `<logger>`; no logger outside `<async>`.
- `flowName`/`flowStep`/`requestType` are correct expressions/values.
- If MUnit `verify-call` references a logger `doc:id`, keep it stable or update the test.
