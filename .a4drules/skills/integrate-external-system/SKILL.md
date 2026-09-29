---
name: integrate-external-system
description: >
  Integrate with a new external system.
  Use when the user asks "integrate", "new external system", "add downstream", "new connection"
---

# integrate-external-system

## Required inputs

| Placeholder             | How to resolve                                              |
| ----------------------- | ----------------------------------------------------------- |
| `{{DOWNSTREAM_SYSTEM}}` | Name + layer (SAPI / SFTP / 3rd-party)                     |
| `{{PROTOCOL}}`          | HTTPS, SFTP, etc.                                           |
| `{{AUTH_MODEL}}`        | client_id/secret headers / SSH identity+passphrase / API key |
| `{{ENDPOINTS}}`         | Paths/methods or SFTP folders this app will use             |
| `{{RETRY_POLICY}}`      | Idempotency + retry expectations                            |

Ask for any missing input **one at a time** before generating code.

## Knowledge to load

The following files are located in the MuleSoft repository and should be
read when executing this skill:

- `curie/knowledge/external-systems.md`
- `curie/knowledge/security.md`
- `curie/knowledge/error-handling.md`
- `curie/knowledge/conventions.md`
- `curie/knowledge/resiliency-and-circuit-breaker.md` (if retries / the latent breaker are involved)

## Usage

Run this skill when the user asks any of the following:

* "integrate"
* "new external system"
* "add downstream"
* "new connection"

## Steps

1. Define a property namespace following the existing pattern (e.g. `http.<system>.*` with `host`,
   `port`, `basePath`, `<verb>.path`; or `<system>.sftp.*` for SFTP).
2. Add a request/SFTP config in the global config file (see `curie/knowledge/architecture.md`), mirroring an existing one: timeout from a property,
   default auth headers or SSH identity+passphrase, `sendCorrelationId="ALWAYS"` for HTTP, and the
   repo's TLS trust-store posture (flag if strict trust is required vs. the current `insecure="true"`).
3. Decide resiliency: wrap idempotent calls in `until-successful` driven by env properties; consider
   whether the latent `cb.*` breaker should finally be wired (`resiliency-and-circuit-breaker.md`).
4. Decide error policy: how a downstream failure maps given the app's async model
   (see `curie/knowledge/architecture.md`).
5. List env-YAML additions (encrypted later via `-Dkey`) for **all environments**
   (see `curie/knowledge/deployment.md` for the list).

## Output

- Global-config block(s).
- Env-YAML property list (cleartext shape — encryption is a separate step).
- Note on auth setup and any new `secure::` properties.

## Success criteria

- No hostnames/ports/paths hardcoded.
- TLS/PGP/SFTP material referenced; correlation-id + logging conventions applied.
- Env properties added consistently across all environments (see `curie/knowledge/deployment.md`).
