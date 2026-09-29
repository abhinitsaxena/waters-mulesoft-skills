# Deployment — Rules

When deploying to CloudHub or advising on deployment, enforce these standards:

- **Environment validation**: confirm `{{ENVIRONMENT}}` maps to a valid env YAML under `src/main/resources/env/`.
- **VM arguments**: always include `-Dmule.env={{ENVIRONMENT}}` and `-Dkey=<AES key>`. Never log or echo the AES key.
- **App naming**: the app name must follow the naming pattern in `curie/knowledge/deployment.md` and match the batch-schedule application name property.
- **Per-environment wiring**: before any deploy, verify all downstream hosts, `api.id`, PGP keyrings, SFTP identity files, and env-specific application names.
- **Production checks**: for prod deploys, double-check API Manager autodiscovery `api.id` matches the env YAML.
- **AES key gotcha**: remind about placeholder keys → `BadPaddingException` when encrypted properties fail to decrypt.
- **Steps must be self-sufficient**: deployment guidance should work without further clarification.
