---
name: change-impact-analysis
description: >
  Change impact analysis (blast radius).
  Use when the user asks "impact analysis", "blast radius", "what does this change affect", "what will break"
---

# change-impact-analysis

## Required inputs

| Placeholder        | How to resolve                                                |
| ------------------ | ------------------------------------------------------------- |
| `{{CHANGE}}`       | The proposed change (flow, transform, query param, property)  |
| `{{FLOW_NAME}}`    | Flow / transform being changed (verify against `src/main/mule/**`) |

Ask for any missing input **one at a time** before generating code.

## Knowledge to load

The following files are located in the MuleSoft repository and should be
read when executing this skill:

- `curie/knowledge/architecture.md`
- `curie/knowledge/external-systems.md`
- `curie/knowledge/related-repos.md`
- `curie/knowledge/employee-file-business-rules.md` (if a rule is involved)

## Usage

Run this skill when the user asks any of the following:

* "impact analysis"
* "blast radius"
* "what does this change affect"
* "what will break"

## Steps

1. Identify what consumes/produces the changed element:
   - **Trigger parity**: all trigger entry points funnel into the process flow — a change to
     process/flag handling usually touches all entries (see `curie/knowledge/architecture.md`).
   - **Transform parity**: related transform scripts must stay consistent (see
     `curie/knowledge/transformations.md` for the inventory and parity rules).
   - **Pagination**: the paged pull flow recurses on itself — page-size / skip changes affect the
     whole pull and retry behavior (see `curie/knowledge/architecture.md`).
   - **Downstream contract**: file-shape/column changes affect downstream system recipients
     (see `curie/knowledge/external-systems.md` and `curie/knowledge/related-repos.md`).
   - **Env properties**: a namespace change must be applied across **all env YAMLs**
     (see `curie/knowledge/deployment.md` for the list).
2. Trace dependencies explicitly (one of the few intents where repo-wide grep is appropriate) — grep
   the property/flow/field name.
3. List the blast radius: files, all parallel flows/entries, RAML asset, env YAMLs, MUnit suite,
   downstream coordination.

## Output

- A blast-radius list ordered by certainty (definitely → possibly affected).
- Explicit "mirror across create/filter/error scripts" and "apply to all env YAMLs" callouts.
- Any cross-repo coordination needed with downstream system owners (see `curie/knowledge/external-systems.md` and `curie/knowledge/related-repos.md`).

## Success criteria

- Trigger/transform parity and env-YAML fan-out always considered.
- No affected consumer or downstream missed.
