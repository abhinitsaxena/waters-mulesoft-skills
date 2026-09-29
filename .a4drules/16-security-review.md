# Security Review — Rules

When performing a security review, enforce these standards:

- **Secret encryption**: all credentials, passphrases, API keys, and PGP keys must be encrypted using `![...]` notation in env-YAML files and accessed via `${secure::…}`.
- **No cleartext credentials**: no plaintext passwords or credentials in any environment file, including local/dev.
- **No committed key material**: never commit private keys, PGP keyrings, or keystore files to the repository.
- **TLS trust-stores**: flag and remediate any `insecure="true"` on trust-store configurations.
- **Secret mechanism separation**: do not conflate `mule-artifact.json` `secureProperties` with `![...]` env-YAML encryption — they serve different purposes.
- **Input handling**: confirm the RAML constrains trigger inputs that matter (headers, query params, payload shapes).
- **No secrets in logs**: verify no secret has been decrypted into logs or `additionalParams`.
- **Orphan properties**: check for orphan `secureProperties` entries that reference properties no longer in use.
- **Outbound calls**: correlation-id must be set, no credential echoing in request headers, query parameters, or log messages.
- **Inbound enforcement**: check the inbound enforcement policy and verify `api.id` is correctly set per environment.
- **Scope gate**: always resolve the audit scope (specific flow/module or full repo) before starting.
- **Known issues**: consult `curie/knowledge/known-issues.md` for pre-existing security findings — reference them, do not re-discover.
- **Realistic attack paths**: every finding must have a realistic attack path. No theoretical risks without a concrete scenario.
