---
name: security-review
description: >
  Performs security review of this MuleSoft repository.
  Use when the user asks "security review", "audit auth",
  "is this safe", "keystore/secret in repo" or asks to understand the security aspects of this repository,integrations, systems, conventions, or business rules.
---

# security-review

Performs security review of this MuleSoft repository.
The following files are located in the MuleSoft repository and should be
read when executing this skill:

- `curie/knowledge/security.md`
- `curie/knowledge/external-systems.md`
- `curie/knowledge/conventions.md`
- `curie/knowledge/known-issues.md`

## Usage

Run this skill when the user asks any of the following:

* "security review"
* "audit auth"
* "is this safe"

Also trigger when the user expresses an equivalent intent, such as "keystore/secret in repo" or asks to understand the security aspects of this repository,integrations, systems, conventions, or business rules.

## Required inputs

| Placeholder        | Required | How to resolve                                                                   |
| ------------------ | -------- | -------------------------------------------------------------------------------- |
| `{{SCOPE}}`        | **YES**  | Which flow or module to audit. Must be extracted from the user prompt or asked for. |
| `{{THREAT_MODEL}}` | No       | Specific concerns (auth, secret leakage, TLS, SFTP keys). Default: all categories. |

---

## Scope gate

**This is the first thing you MUST do before any other step.**

1. **Extract scope from the prompt.** Scan the user's message for an explicit reference to one of
   the known scopes in this repo. Read `curie/knowledge/architecture.md` for the flow inventory and
   DWL module list, then map the user's reference to a specific file.

   | Scope token (case-insensitive) | Maps to                                         |
   | ------------------------------ | ----------------------------------------------- |
   | `all` / `full` / `entire`      | Audit all flows and DWL modules                 |
   | Any flow / DWL name            | Derive path from `curie/knowledge/architecture.md` |

2. **If a scope token is found**, set `{{SCOPE}}` to the matched value and proceed to Steps.

3. **If NO scope token is found**, you MUST call `ask_followup_question` before doing anything else.
   Use this exact question and options (derive the option list from `curie/knowledge/architecture.md`):

   > **Which flow or module should I audit?**
   > Please pick one (or say "all" for a full-repo review):
   >
   > Options to offer: read flow and DWL module names from `curie/knowledge/architecture.md`
   > and present them. Always include an `all` — full-repo security review option.

4. **Do NOT proceed to Steps until `{{SCOPE}}` is set.**

---

## Steps

> **Scope: `{{SCOPE}}`** — all findings and checks below are limited to this scope unless the user
> specified "all".

1. Read the source file(s) for `{{SCOPE}}` using `read_file`. If scope is `all`, read every flow
   and DWL module listed in `curie/knowledge/architecture.md`. Enumerate inputs to the scope
   (HTTP endpoints, headers, inbound payload shapes) from `curie/knowledge/architecture.md` and
   `curie/knowledge/external-systems.md`.
2. For each, check:
   - **Input handling** — confirm the RAML constrains the trigger inputs that matter.
   - **Secrets** — all credentials, passphrases, API keys, and PGP keys must stay encrypted
     (`![...]`/`secure::`). No secret decrypted into logs or `additionalParams`.
   - **Cleartext / orphan secrets** — flag any cleartext passwords, cleartext lower-env credentials,
     and orphan `secureProperties` entries. See `curie/knowledge/known-issues.md` for known instances.
   - **TLS** — flag `insecure="true"` trust-stores. See `curie/knowledge/known-issues.md` for
     known instances.
   - **SFTP/PGP key material** — confirm no private key/keyring is committed to the repo.
   - **Auth** — check the inbound enforcement policy and `api.id` per env.
3. Confirm the two secret mechanisms (`mule-artifact.json` `secureProperties` vs `![...]` env-YAML
   encryption) aren't conflated.
4. Outbound calls: correlation-id set, no credential echoing.

## Output

- Findings list with severity + concrete fix.
- A short "did NOT find" section to make scope explicit.

## Success criteria

- Every finding has a realistic attack path.
- Fixes reference `security.md` / `conventions.md` rules.
- Pre-existing `known-issues.md` items are referenced, not re-discovered.
