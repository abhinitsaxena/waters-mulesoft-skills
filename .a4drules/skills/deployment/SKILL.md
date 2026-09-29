---
name: deployment
description: >
  Deployment / CloudHub questions.
  Use when the user asks "deploy", "deployment", "CloudHub", "undeploy"
---

# deployment

## Required inputs

| Placeholder       | How to resolve                                            |
| ----------------- | --------------------------------------------------------- |
| `{{ENVIRONMENT}}` | Must match an env YAML under `src/main/resources/env/`     |
| `{{ACTION}}`      | deploy / undeploy / get logs / status                     |

Ask for any missing input **one at a time** before generating code.

## Knowledge to load

The following files are located in the MuleSoft repository and should be
read when executing this skill:

- `curie/knowledge/deployment.md`
- `curie/knowledge/runtime-and-build.md`

## Usage

Run this skill when the user asks any of the following:

* "deploy"
* "deployment"
* "CloudHub"
* "undeploy"

## Steps

1. Confirm `{{ENVIRONMENT}}` maps to a valid env YAML under `src/main/resources/env/`
   (see `curie/knowledge/deployment.md` for the list of valid environments).
2. Build VM args:
   ```
   -Dmule.env={{ENVIRONMENT}}
   -Dkey=<AES key for that env>
   ```
   Never log the key.
3. App name follows the naming pattern in `curie/knowledge/deployment.md`; confirm the app name
   matches the batch-schedule application name property so the stop call targets the right schedule.
4. **Before deploy, verify per-env wiring**: all downstream hosts, `api.id`, PGP keyrings,
   SFTP identity files, and env-specific names. See `curie/knowledge/deployment.md` and
   `curie/knowledge/external-systems.md` for the full checklist.
5. For prod, double-check API Manager autodiscovery `api.id` matches the env YAML.

## Output

- Concrete CloudHub 2.0 command or Anypoint UI steps for `{{ACTION}}`.
- Reminder about the AES key gotcha (placeholder keys → `BadPaddingException`).

## Success criteria

- Steps work without further clarification.
- Per-env host/api.id/keyring/applicationName verified before any deploy.
