---
name: runtime-or-dependency-upgrade
description: >
  Runtime / dependency upgrade.
  Use when the user asks "upgrade", "bump version", "update dependency", "mule runtime upgrade"
---

# runtime-or-dependency-upgrade

## Required inputs

| Placeholder           | How to resolve                                                |
| --------------------- | ------------------------------------------------------------- |
| `{{TARGET_VERSION}}`  | Target Mule / Java / Maven / parent-POM / plugin / connector version |
| `{{CURRENT_VERSION}}` | Read from `pom.xml` / `mule-artifact.json`                    |

Ask for any missing input **one at a time** before generating code.

## Knowledge to load

The following files are located in the MuleSoft repository and should be
read when executing this skill:

- `curie/knowledge/runtime-and-build.md`
- `curie/knowledge/conventions.md`
- `curie/knowledge/testing.md`
- `curie/knowledge/known-issues.md`

## Usage

Run this skill when the user asks any of the following:

* "upgrade"
* "bump version"
* "update dependency"
* "mule runtime upgrade"

## Steps

1. Read `pom.xml` and `mule-artifact.json` first — these are the source of truth for current
   versions (do not rely on versions stated anywhere else). Check `mule-artifact.json` for the
   `javaSpecificationVersions` pin (see `curie/knowledge/runtime-and-build.md`).
2. Bump only the requested component; no drive-by bumps.
3. Cross-check compatibility:
   - `app.runtime` ↔ `minMuleVersion` ↔ Java line (confirm CI JDK).
   - `mule.maven.plugin.version` ↔ runtime.
   - parent POM ↔ managed connector deps (check `curie/knowledge/runtime-and-build.md` for the
     connector inventory and their managed versions).
   - RAML asset version ↔ APIKit version.
4. Keep `minMuleVersion` consistent with the new runtime line.
5. Re-run MUnit after the bump (mocks must still bind; needs real `mule.env`+`key`).

## Output

- Diff of `pom.xml` and (if changed) `mule-artifact.json`.
- A short compatibility note explaining why the chosen versions work together.

## Success criteria

- Build passes; MUnit passes (or a new failure is explained as a known migration step).
- No drive-by version bumps.
